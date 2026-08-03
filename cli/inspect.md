---
url: https://planetscale.com/docs/cli/inspect
title: "Inspect"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale CLI commands: inspect

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you've installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The `inspect` command

Run read-only diagnostic checks against a database branch. Each check is a single bounded query against the engine's statistics tables. Nothing is written to the database.

`inspect` uses the same ephemeral credential model as [`pscale sql`](sql.md): always read-only, always bounded. For server-side, traffic-aware analysis (slow queries, schema recommendations, anomalies), use [`pscale insights`](insights.md) instead.

The two surfaces cross-reference each other in human output and JSON `next_steps`, so you can move from live state to traffic-aware history (or the reverse) without leaving the CLI.

**Usage:**

```bash theme={null}
pscale inspect <check> <database> <branch> --org <org> <FLAG>
```

Place **positional arguments first**, then flags. **`--org` is required.**

### Database engine support

Checks adapt to the database engine. MySQL (Vitess) checks read `information_schema`, `mysql`, and `sys`. PostgreSQL checks read `pg_catalog` and `pg_stat` views. Checks that do not apply to an engine explain what to use instead (often a matching `pscale insights` command).

|                 | **Postgres**                    | **Vitess**                                 |
| :-------------- | :------------------------------ | :----------------------------------------- |
| Targeting flags | `--dbname`, `--role`            | `--keyspace`                               |
| Scope           | One PostgreSQL database per run | One shard's MySQL instance per run         |
| Replica checks  | `--replica`                     | `--replica` (via `--keyspace` tablet type) |

On sharded Vitess databases, statistics reflect one shard's MySQL instance per run. Pass `--keyspace` to pick the keyspace, or target an exact shard with `--keyspace 'mykeyspace/-80'` (enumerate shards with `SHOW VITESS_SHARDS` via `pscale sql`). Databases can have hundreds of shards, so no check fans out across shards automatically.

On PostgreSQL, statistics are scoped to one database. Pass `--dbname` to target the database your application uses (defaults to `postgres`). The reader role may lack `CONNECT` on non-default databases; use `--role admin` if connecting with `--dbname` fails.

### Available checks

| **Check**              | **Postgres**                   | **Vitess**             | **Description**                                                     |
| :--------------------- | :----------------------------- | :--------------------- | :------------------------------------------------------------------ |
| `table-sizes`          | Yes                            | Yes                    | Tables by total size, largest first                                 |
| `index-sizes`          | Yes                            | Yes                    | Indexes by size, largest first                                      |
| `unused-indexes`       | Yes                            | Yes                    | Indexes with little or no use — removal candidates                  |
| `redundant-indexes`    | Via `insights recommendations` | Yes                    | Indexes made redundant by another index                             |
| `invalid-indexes`      | Yes                            | No                     | Invalid indexes left over from failed concurrent index builds       |
| `seq-scans`            | Yes                            | Yes                    | Tables receiving full-table scans                                   |
| `long-running-queries` | Yes                            | Yes                    | Queries running longer than 5 minutes                               |
| `locks`                | Yes                            | Yes                    | Blocking locks and the sessions stuck behind them                   |
| `outliers`             | Yes\*                          | Via `insights queries` | Queries by cumulative execution time                                |
| `calls`                | Yes\*                          | Via `insights queries` | Most frequently called queries                                      |
| `bloat`                | Yes                            | Yes                    | Wasted space: estimated bloat (PostgreSQL) or fragmentation (MySQL) |
| `vacuum-stats`         | Yes                            | Via `inspect bloat`    | Autovacuum and autoanalyze health                                   |
| `replication-slots`    | Yes                            | Via `workflow list`    | Replication slots: status, WAL retention, and lag                   |
| `subscriptions`        | Yes                            | Via `data-imports get` | Per-table logical replication progress on this subscriber           |
| `all`                  | Yes                            | Yes                    | Run every applicable check and print a combined report              |

\* `outliers` and `calls` require the `pg_stat_statements` extension on PostgreSQL. If it is not installed, the check is skipped and points you at the matching `pscale insights` command.

### Available flags

| **Flag**                  | **Description**                                                                                                                                                     |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--org <org>`             | Organization name **(required)**                                                                                                                                    |
| `--keyspace <target>`     | Vitess keyspace to inspect, optionally with a shard and tablet type (for example `mykeyspace`, `mykeyspace/-80`, `mykeyspace/-80@replica`). Defaults to `@primary`. |
| `--dbname <name>`         | PostgreSQL database name to inspect. Default: `postgres`.                                                                                                           |
| `--role <role>`           | Access role for ephemeral credentials: `reader`, `writer`, `readwriter`, or `admin`. Default: `reader`.                                                             |
| `--replica`               | Run checks against a replica instead of the primary.                                                                                                                |
| `-f`, `--format <FORMAT>` | Show output in a specific format. Possible values: `human` (default), `json`, `csv`. `csv` is supported for individual checks only, not `inspect all`.              |
| `-h`, `--help`            | Help for `inspect`                                                                                                                                                  |

## Examples

Run a single check:

```bash theme={null}
pscale inspect table-sizes <database> <branch> --org <org>
pscale inspect locks <database> <branch> --org <org> --format json
pscale inspect unused-indexes <database> <branch> --org <org> --format csv
```

Run every applicable check:

```bash theme={null}
pscale inspect all <database> <branch> --org <org>
pscale inspect all <database> <branch> --org <org> --format json
```

Target a specific Vitess shard:

```bash theme={null}
pscale inspect table-sizes <database> <branch> --org <org> --keyspace commerce/-80
```

Inspect a non-default PostgreSQL database:

```bash theme={null}
pscale inspect table-sizes <database> <branch> --org <org> --dbname myapp --role admin
```

### JSON output

Individual checks return a `CheckResult` object with `check`, `database`, `branch`, `columns`, `rows`, `row_count`, and optional `skipped` and `next_steps` fields.

`inspect all` returns a `Report` with a `results` array (one entry per check) and top-level `next_steps` pointing at the complementary `pscale insights` commands.

## Related documentation

<CardGroup>
  <Card title="pscale insights" href="/docs/cli/insights" icon="angles-right" horizontal />

  <Card title="pscale sql" href="/docs/cli/sql" icon="angles-right" horizontal />

  <Card title="PlanetScale CLI environment setup" href="/docs/cli/planetscale-environment-setup" icon="angles-right" horizontal />
</CardGroup>

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
