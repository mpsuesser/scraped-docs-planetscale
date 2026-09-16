---
url: https://planetscale.com/docs/postgres/search/reference/settings
title: "Settings"
description: ""
access_date: 2026-09-16T16:23:24.602Z
current_date: 2026-09-16T16:23:24.602Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Settings

> Postgres settings that control TIN.

**Platform availability:** Postgres only

These are ordinary Postgres GUCs (`SET`, `ALTER DATABASE … SET`, or `postgresql.conf`). Defaults are tuned for good performance, and you usually do not need to change them.

| Setting                       | Default             | Notes                                                                                                                                                                                                                                                                    |
| ----------------------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `tin.enable_custom_scan`      | `on`                | When `off`, falls back to generic index scanning (troubleshooting / comparison).                                                                                                                                                                                         |
| `tin.index_maintenance_mode`  | `background`        | How index maintenance runs: `background`, `foreground` (in the writing session), or `manual` (no automatic maintenance).                                                                                                                                                 |
| `tin.build_io_concurrency`    | `0` (auto)          | Caps concurrent disk I/O during `CREATE INDEX` / `REINDEX`. `1` serializes build I/O.                                                                                                                                                                                    |
| `tin.rss_baseline`            | server-derived (MB) | Per-worker RSS estimate used when planning build parallelism.                                                                                                                                                                                                            |
| `tin.maintenance_jobs_per_db` | `0`                 | How many of its own database's maintenance jobs a cluster-wide maintenance worker finishes before yielding to another database with queued work. `0` drains the current database first. Set it in `postgresql.conf` or with `ALTER SYSTEM`. A session `SET` is rejected. |
| `tin.track_page_reuse_stats`  | `off`               | When `on`, `EXPLAIN ANALYZE` reports distinct vs repeated index page reads.                                                                                                                                                                                              |

## Debug settings

The `tin.debug_*` settings are for debugging and testing TIN, not for production use. Each one forces a planning choice the cost model would otherwise make, so that an alternative plan can be measured. They never change query results, and a query shape that would be incorrect is still refused. All of them are session-level (`SET`) and default to following the cost model.

| Setting                             | Values                                      | Default | Notes                                                                                                                                       |
| ----------------------------------- | ------------------------------------------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `tin.debug_force_boolean_family`    | `auto`, `fused`, `factored`                 | `auto`  | For a query over several TIN indexes, force one combined scan (`fused`) or separate scans joined with boolean operators (`factored`).       |
| `tin.debug_force_topk`              | `auto`, `topk`, `exhaustive`                | `auto`  | For `ORDER BY tin.score(ctid) DESC LIMIT k`, force the bounded top-k scan or an exhaustive scored scan.                                     |
| `tin.debug_force_multi_index_drive` | `auto`, `sparse`, `stripe`                  | `auto`  | For a query over several TIN indexes, drive from the rarest required term (`sparse`) or evaluate by block range (`stripe`).                 |
| `tin.debug_force_conjunction_mode`  | `auto`, `generic`, `pushdown`, `noadaptive` | `auto`  | Override how an `AND` between a text predicate and another indexed predicate is executed. `noadaptive` only disables the adaptive verifier. |
| `tin.debug_force_visibility`        | `auto`, `streaming`, `sorted`               | `auto`  | Where a count-only `AND` can check row visibility in streaming or sorted order, force one.                                                  |
| `tin.debug_disable_count_pushdown`  | `on`, `off`                                 | `off`   | Disable the index-answered `count(*)` path so a plain aggregate over a scan runs instead.                                                   |
| `tin.debug_disable_index_probe`     | `on`, `off`                                 | `off`   | Disable probing a btree index alongside the text search; mixed predicates fall back to a TIN scan with Postgres filters.                    |
| `tin.debug_force_parallel`          | `on`, `off`                                 | `off`   | Drop serial plans that have a parallel sibling so the parallel plan is chosen, including on tables too small to qualify otherwise.          |

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
