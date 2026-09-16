---
url: https://planetscale.com/docs/postgres/search/operations
title: "Operations"
description: ""
access_date: 2026-09-16T16:23:24.602Z
current_date: 2026-09-16T16:23:24.602Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Operational guidance

> Best practices running TIN in production.

**Platform availability:** Postgres only

## Memory for index builds

A TIN index build runs in parallel, and each worker prefers about 1 GB of memory. The budget comes from `maintenance_work_mem`, split evenly across the workers. TIN starts as many workers as `max_parallel_maintenance_workers` allows and the budget can support, so a small `maintenance_work_mem` means fewer workers and a slower build. A worker can run with less than 1 GB, but the index it writes is more fragmented, and that fragmentation slows every query on the index until you `REINDEX` it with more memory.

Before building an index on a large table, raise the budget for that session:

```sql theme={null}
SET maintenance_work_mem = '8GB';
SET max_parallel_maintenance_workers = 8;
CREATE INDEX posts_body_tin ON posts USING tin (body);
```

Keep the total inside the memory of your cluster size. As a rule of thumb, plan on 1 GB for each core you want the build to use, on top of `shared_buffers` and the memory your normal traffic needs. On [network-attached storage clusters](../../plans/planetscale-skus.md#network-attached-storage), a PS-160 has 16 GB of RAM and a PS-1280 has 128 GB, so an 8 GB build budget fits comfortably on the larger size and not at all on the smaller one. Both settings are also available under [Parameters](../cluster-configuration/parameters.md) when you want a cluster-wide default rather than a session setting.

## Storage

PlanetScale clusters run on network-attached storage (AWS gp3, Amazon Elastic Block Store) or on [PlanetScale Metal](../../metal.md), which uses locally attached NVMe drives.

Network-attached storage supports [storage autoscaling](../cluster-configuration/cluster-storage.md) and comes with an IOPS and throughput allowance that you can raise. Every read that misses the cache pays network latency, and a search over a cold index is many such reads. TIN reads index pages as sequentially as it can and returns matching rows in heap order within each segment, which keeps its I/O pattern friendly to network-attached storage and to Postgres' caching. Metal has no IOPS limit and much lower latency per read, and its drive size is fixed when the cluster is created. For a search index that does not fit in memory, Metal is the faster choice, and it is the one to prefer when search latency matters.

`effective_io_concurrency` matters on network-attached storage. When it is positive, TIN issues readahead hints for some index reads. Keep it positive when the index is read from disk, and set it to `0` when the index fits in `shared_buffers`, where the hints are pure overhead.

## Parallel query

TIN splits an index into segments at build time, one per core by default, and a query can use one parallel worker per segment. Postgres caps the workers with `max_parallel_workers_per_gather` and `max_parallel_workers`, both available under [Parameters](../cluster-configuration/parameters.md). As an index grows, the work per query grows with it, and TIN spreads that work across the cores it is given, so a multi-terabyte index performs best on a cluster size with more vCPUs. If you build on one cluster size and serve on another, set `initial_segment_count` for the serving size, because the segment count is fixed at build. See [Index options](reference/indexes.md#index-options-with).

## Read replicas

Set `hot_standby_feedback = on` on every replica that serves TIN queries. TIN's background maintenance frees index pages once no transaction on the primary needs them, and the feedback is what tells the primary that a query on a replica still does. Without it, replica queries that overlap with maintenance are cancelled with SQLSTATE `40001` more often. Results on a replica are always exact. Under heavy replication traffic a query can still be cancelled with `40001`, and the application should retry it. `hot_standby_feedback` is available under [Parameters](../cluster-configuration/parameters.md). See also [Replicas](../scaling/replicas.md).

## Vacuum

When a row is updated or deleted, Postgres keeps the old version in the table until `VACUUM` removes it, and the row's entry stays in the TIN index. TIN learns that the entry is dead only when `VACUUM` runs on the table. Until then, a query that reaches the entry has to check the row in the heap to find out whether it is still visible.

`count(*)` is affected most. A count over a TIN predicate is normally answered from counts stored in the index. TIN trusts those stored counts only where it knows of no dead entries and Postgres's visibility map marks every row visible. Rows deleted or updated since the last `VACUUM` break both conditions, so the count falls back to checking rows in the heap. On a table with steady update or delete traffic, count performance degrades between vacuums and recovers after each one.

`VACUUM` also drives background maintenance. It marks deleted entries dead in the index, and once a segment's dead fraction crosses `dead_percent_threshold` (default `0.5`), background maintenance rewrites the segment without them. BM25 statistics keep counting deleted rows until that rewrite. See [Visibility](scoring.md#visibility).

A long-running transaction delays `VACUUM`, so dead heap rows and dead TIN index entries remain until that transaction ends.

### Autovacuum settings

By default Postgres does not vacuum a table until 20% of its rows have changed (`autovacuum_vacuum_scale_factor = 0.2`, plus `autovacuum_vacuum_threshold = 50` rows). On a large table that is millions of dead rows, and a TIN index over that table carries them the whole time. For tables you search with TIN, and especially when `count(*)` is part of the workload, lower the trigger for that table:

```sql theme={null}
ALTER TABLE posts SET (
  autovacuum_vacuum_scale_factor = 0.01,
  autovacuum_vacuum_threshold = 1000
);
```

With these values autovacuum runs after about 1% of the table plus 1,000 rows have changed. Set them per table so that tables you do not search keep the defaults. The cluster-wide `autovacuum_vacuum_scale_factor` can also be changed under [Parameters](../cluster-configuration/parameters.md).

## Postgres settings TIN uses

TIN has few settings of its own. Most of its behavior follows ordinary Postgres settings, which you can change under [Parameters](../cluster-configuration/parameters.md) or, for the session-level ones, with `SET`.

| Setting                                                                                                                                          | How TIN uses it                                                                                                                                                                                                                                                                                                                                                                        |
| ------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `maintenance_work_mem`                                                                                                                           | The memory budget for an index build, split evenly across the build workers, and the budget for folding new writes into the index. Together with `tin.rss_baseline` it decides how many build workers can run.                                                                                                                                                                         |
| `max_parallel_maintenance_workers`                                                                                                               | The upper bound on build workers.                                                                                                                                                                                                                                                                                                                                                      |
| `max_worker_processes`                                                                                                                           | The pool that background maintenance, parallel builds, and the end-of-build WAL pass draw from. When the pool is exhausted, maintenance runs inline in the writing session and builds proceed with fewer workers.                                                                                                                                                                      |
| `max_parallel_workers_per_gather`, `max_parallel_workers`                                                                                        | The cap on parallel workers for a query. TIN uses up to one worker per segment within that cap.                                                                                                                                                                                                                                                                                        |
| `work_mem`                                                                                                                                       | Memory for deduplicating and sorting row identifiers when a query combines several predicates, for example an `OR` between a text predicate and a btree lookup. Larger sets spill to disk.                                                                                                                                                                                             |
| `shared_buffers`                                                                                                                                 | Where index pages are cached. Size it to hold the part of the index your queries touch.                                                                                                                                                                                                                                                                                                |
| `effective_io_concurrency`                                                                                                                       | When positive, TIN issues readahead hints for some index reads. Keep it positive when the index is read from disk and `0` when the index fits in `shared_buffers`.                                                                                                                                                                                                                     |
| `seq_page_cost`, `random_page_cost`, `cpu_tuple_cost`, `cpu_operator_cost`, `parallel_setup_cost`, `parallel_tuple_cost`, `effective_cache_size` | TIN's cost model measures the work of each candidate plan in physical units and prices them with these settings, so they decide between serial and parallel plans and between index paths. The defaults describe spinning disk. On a cluster whose index fits in memory, `seq_page_cost = 0.1`, `random_page_cost = 0.1`, and an accurate `effective_cache_size` produce better plans. |
| `wal_level`                                                                                                                                      | Changes to a TIN index are written to WAL under the same rule as built-in indexes. Only `minimal`, with an index created in the current transaction, skips logging.                                                                                                                                                                                                                    |
| `wal_compression`                                                                                                                                | The last phase of an index build writes the whole index to WAL. `lz4` or `zstd` reduces that WAL volume at the cost of CPU that is usually idle by then.                                                                                                                                                                                                                               |
| `hot_standby_feedback`                                                                                                                           | Required on replicas that serve TIN queries. See [Read replicas](#read-replicas).                                                                                                                                                                                                                                                                                                      |
| `autovacuum_vacuum_scale_factor`, `autovacuum_vacuum_threshold`                                                                                  | Decide how soon dead rows are reported to TIN. See [Vacuum](#vacuum).                                                                                                                                                                                                                                                                                                                  |

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
