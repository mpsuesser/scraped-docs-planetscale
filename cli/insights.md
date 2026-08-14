---
url: https://planetscale.com/docs/cli/insights
title: "Insights"
description: ""
access_date: 2026-08-14T00:39:58.404Z
current_date: 2026-08-14T00:39:58.404Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The insights command

Surface PlanetScale’s server-side analysis of a database: aggregated query statistics, failing query patterns, resource anomalies, and schema recommendations, all computed from production traffic.

For live, connection-level diagnostics (table sizes, locks, running queries), use [`pscale inspect`](inspect.md) instead.

Query insights require Query Insights to be enabled for the database. See [Postgres](../postgres/monitoring/query-insights.md) or [Vitess](../vitess/monitoring/query-insights.md). Postgres and Vitess branches are supported.

**Usage:**

```shellscript
pscale insights <sub-command> <database> [<branch>] --org <org> <FLAG>
```

Place **positional arguments first**, then flags. **`--org` is required.**

### Available sub-commands

| **Sub-command** | **Product** | **Description** |
| --- | --- | --- |
| `queries` | Postgres, Vitess | List top queries ranked by a performance metric |
| `queries samples` | Postgres, Vitess | List recent executions for a query fingerprint |
| `errors` | Postgres, Vitess | List queries that are failing with errors |
| `anomalies` | Postgres, Vitess | List detected resource anomalies (CPU, memory, IOPS, rows read/written) |
| `tags` | Postgres, Vitess | List query tag keys (sqlcommenter / system) |
| `tags show` | Postgres, Vitess | Show a query tag key and its values |
| `tags summaries` | Postgres, Vitess | List query statistics grouped by tag keys |
| `recommendations` | Postgres, Vitess | List schema recommendations with ready-to-apply DDL |
| `recommendations dismiss` | Postgres, Vitess | Dismiss a schema recommendation |

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `--org <org>` | Organization name **(required)** |
| `-f`, `--format <FORMAT>` | Show output in a specific format. Possible values: `human` (default), `json`, `csv`. |
| `-h`, `--help` | Help for `insights` |

## Examples

### The queries sub-command

List the top queries for a branch, ranked by cumulative execution time (default).

**Usage:**

```shellscript
pscale insights queries <database> <branch> --org <org> <FLAG>
```

**Available flags:**

| **Flag** | **Description** |
| --- | --- |
| `--sort <metric>` | Metric to rank by. One of: `totalTime`, `count`, `errorCount`, `rowsRead`, `rowsReturned`, `rowsAffected`, `rowsReadPerReturned`, `p50Latency`, `p99Latency`, `maxLatency`, `cpuTime`, `ioTime`, `lastRun`. Default: `totalTime`. |
| `--dir <direction>` | Sort direction: `asc` or `desc`. Default: `desc`. |
| `--limit <n>` | Number of queries to return. Default: `15`. |
| `--period <duration>` | Time period to aggregate over (for example `1h`, `24h`). |

**Examples:**

```shellscript
pscale insights queries <database> <branch> --org <org>
pscale insights queries <database> <branch> --org <org> --sort rowsReadPerReturned
pscale insights queries <database> <branch> --org <org> --sort p99Latency --period 1h --format json
```

### The queries samples sub-command

List recent executions for a specific query fingerprint. Use the fingerprint from `pscale insights queries`. `--keyspace` is required.

**Usage:**

```shellscript
pscale insights queries samples <database> <branch> <fingerprint> --org <org> --keyspace <keyspace> <FLAG>
```

**Available flags:**

| **Flag** | **Description** |
| --- | --- |
| `--keyspace <name>` | Keyspace for the fingerprint (required; from `insights queries`) |
| `--limit <n>` | Number of samples to return. Default: `25`. |
| `--period <duration>` | Time period to look back (for example `1h`, `1d`). |

**Example:**

```shellscript
pscale insights queries samples <database> <branch> <fingerprint> --org <org> --keyspace <keyspace> --format json
```

### The errors sub-command

List failing query patterns with error messages.

**Usage:**

```shellscript
pscale insights errors <database> <branch> --org <org> <FLAG>
```

**Available flags:**

| **Flag** | **Description** |
| --- | --- |
| `--limit <n>` | Number of errors to return. Default: `15`. |
| `--period <duration>` | Time period to aggregate over (for example `1h`, `24h`). |

**Example:**

```shellscript
pscale insights errors <database> <branch> --org <org> --format json
```

### The anomalies sub-command

List detected resource anomalies for a branch.

**Usage:**

```shellscript
pscale insights anomalies <database> <branch> --org <org> <FLAG>
```

### The tags sub-command

List query tag keys from sqlcommenter / system tags on a branch.

**Usage:**

```shellscript
pscale insights tags <database> <branch> --org <org> <FLAG>
```

**Available flags:**

| **Flag** | **Description** |
| --- | --- |
| `--period <duration>` | Time period to look back (for example `1h`, `1d`). |
| `--fingerprint <fp>` | Only tags seen on this query fingerprint. |
| `--keyspace <name>` | Filter tags to a keyspace. |
| `--limit <n>` | Number of top values to show in human output. Default: `3`. |

**Example:**

```shellscript
pscale insights tags <database> <branch> --org <org> --format json
```

#### tags show

Show a query tag key and its values.

```shellscript
pscale insights tags show <database> <branch> <tag> --org <org>
```

#### tags summaries

List query statistics grouped by one or more tag keys. `--tags` is required and repeatable.

```shellscript
pscale insights tags summaries <database> <branch> --org <org> --tags username --tags app --format json
```

### The recommendations sub-command

List schema recommendations for a database: unused tables and indexes, duplicate indexes, bloated tables and indexes, missing indexes derived from production query patterns, and sequence overflow risks. Each recommendation includes ready-to-apply DDL in JSON output.

This sub-command takes a database name only (not a branch).

**Usage:**

```shellscript
pscale insights recommendations <database> --org <org> <FLAG>
```

**Example:**

```shellscript
pscale insights recommendations <database> --org <org> --format json
```

#### recommendations dismiss

Dismiss a schema recommendation. Use the recommendation number from `pscale insights recommendations`. Interactive confirmation is required unless `--force` is passed.

```shellscript
pscale insights recommendations dismiss <database> <number> --org <org> --force --reason "not applicable"
```

If the database or branch is not found, or Query Insights is not enabled, the command returns an error explaining both possible causes.

## Related documentation

## pscale inspect

## Query Insights (Postgres)

## Query Insights (Vitess)

## Schema recommendations (Postgres)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
