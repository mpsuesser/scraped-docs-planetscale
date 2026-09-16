---
url: https://planetscale.com/docs/postgres/search/reference/limitations
title: "Limitations"
description: ""
access_date: 2026-09-16T16:23:24.602Z
current_date: 2026-09-16T16:23:24.602Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Limitations

> Constraints on TIN indexes.

**Platform availability:** Postgres only

* **One column per index** — Create a separate TIN index for each searchable column (or expression). Combine columns in a query with multiple `==>` predicates. `tin.score(ctid)` combines scores across those fields.
* **Partitioned tables** — TIN indexes on partitioned tables are supported, and querying the parent works when every partition has a TIN index. BM25 scores are computed **per partition** (each partition has its own corpus statistics), so ordering by score across partitions mixes scores from different subsets. Some query shapes that pass `tin.score` through aggregates or window functions against the parent are refused with an error that names the workaround: compute the score in a `MATERIALIZED` CTE (or query a single partition) and consume it above.
* **Index relation size** — The index relation grows to a high-water mark and does not shrink on its own. Space freed by deleted and updated rows is reused internally, and `REINDEX` is the only way to shrink it. Measure it with `pg_relation_size('<index name>')`. `CREATE INDEX CONCURRENTLY`, `REINDEX`, and `REINDEX CONCURRENTLY` are supported.
* **Tokenizer** — Tokenization is configured per index via `WITH (…)` (see [Index options](indexes.md#index-options-with)). After changing analysis options on a populated index, `REINDEX` so existing rows are re-tokenized. `tin.tokenize` accepts the same arguments.
* **Read replicas** — On physical standbys, set `hot_standby_feedback = on`. Standby results are exact. Under heavy replay a query can be cancelled with SQLSTATE `40001` (serialization failure) and should be retried.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
