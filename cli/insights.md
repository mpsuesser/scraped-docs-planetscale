---
url: https://planetscale.com/docs/cli/insights
title: "Insights"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale CLI commands: insights

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you've installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The `insights` command

Surface PlanetScale's server-side analysis of a database: aggregated query statistics, failing query patterns, resource anomalies, and schema recommendations, all computed from production traffic.

For live, connection-level diagnostics (table sizes, locks, running queries), use [`pscale inspect`](inspect.md) instead.

Query insights require Query Insights to be enabled for the database. See [Postgres](../postgres/monitoring/query-insights.md) or [Vitess](../vitess/monitoring/query-insights.md). Postgres and Vitess branches are supported.

**Usage:**

```bash theme={null}
pscale insights <sub-command> <database> [<branch>] --org <org> <FLAG>
```

Place **positional arguments first**, then flags. **`--org` is required.**

### Available sub-commands

| **Sub-command**   | **Product**      | **Description**                                                         |
| :---------------- | :--------------- | :---------------------------------------------------------------------- |
| `queries`         | Postgres, Vitess | List top queries ranked by a performance metric                         |
| `errors`          | Postgres, Vitess | List queries that are failing with errors                               |
| `anomalies`       | Postgres, Vitess | List detected resource anomalies (CPU, memory, IOPS, rows read/written) |
| `recommendations` | Postgres, Vitess | List schema recommendations with ready-to-apply DDL                     |

### Available flags

| **Flag**                  | **Description**                                                                      |
| ------------------------- | ------------------------------------------------------------------------------------ |
| `--org <org>`             | Organization name **(required)**                                                     |
| `-f`, `--format <FORMAT>` | Show output in a specific format. Possible values: `human` (default), `json`, `csv`. |
| `-h`, `--help`            | Help for `insights`                                                                  |

## Examples

### The `queries` sub-command

List the top queries for a branch, ranked by cumulative execution time (default).

**Usage:**

```bash theme={null}
pscale insights queries <database> <branch> --org <org> <FLAG>
```

**Available flags:**

| **Flag**              | **Description**                                                                                                                                                                                                                   |
| --------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--sort <metric>`     | Metric to rank by. One of: `totalTime`, `count`, `errorCount`, `rowsRead`, `rowsReturned`, `rowsAffected`, `rowsReadPerReturned`, `p50Latency`, `p99Latency`, `maxLatency`, `cpuTime`, `ioTime`, `lastRun`. Default: `totalTime`. |
| `--dir <direction>`   | Sort direction: `asc` or `desc`. Default: `desc`.                                                                                                                                                                                 |
| `--limit <n>`         | Number of queries to return. Default: `15`.                                                                                                                                                                                       |
| `--period <duration>` | Time period to aggregate over (for example `1h`, `24h`).                                                                                                                                                                          |

**Examples:**

```bash theme={null}
pscale insights queries <database> <branch> --org <org>
pscale insights queries <database> <branch> --org <org> --sort rowsReadPerReturned
pscale insights queries <database> <branch> --org <org> --sort p99Latency --period 1h --format json
```

### The `errors` sub-command

List failing query patterns with error messages.

**Usage:**

```bash theme={null}
pscale insights errors <database> <branch> --org <org> <FLAG>
```

**Available flags:**

| **Flag**              | **Description**                                          |
| --------------------- | -------------------------------------------------------- |
| `--limit <n>`         | Number of errors to return. Default: `15`.               |
| `--period <duration>` | Time period to aggregate over (for example `1h`, `24h`). |

**Example:**

```bash theme={null}
pscale insights errors <database> <branch> --org <org> --format json
```

### The `anomalies` sub-command

List detected resource anomalies for a branch.

**Usage:**

```bash theme={null}
pscale insights anomalies <database> <branch> --org <org> <FLAG>
```

### The `recommendations` sub-command

List schema recommendations for a database: unused tables and indexes, duplicate indexes, bloated tables and indexes, missing indexes derived from production query patterns, and sequence overflow risks. Each recommendation includes ready-to-apply DDL in JSON output.

This sub-command takes a database name only (not a branch).

**Usage:**

```bash theme={null}
pscale insights recommendations <database> --org <org> <FLAG>
```

**Example:**

```bash theme={null}
pscale insights recommendations <database> --org <org> --format json
```

If the database or branch is not found, or Query Insights is not enabled, the command returns an error explaining both possible causes.

## Related documentation

<CardGroup>
  <Card title="pscale inspect" href="/docs/cli/inspect" icon="angles-right" horizontal />

  <Card title="Query Insights (Postgres)" href="/docs/postgres/monitoring/query-insights" icon="angles-right" horizontal />

  <Card title="Query Insights (Vitess)" href="/docs/vitess/monitoring/query-insights" icon="angles-right" horizontal />

  <Card title="Schema recommendations (Postgres)" href="/docs/postgres/monitoring/schema-recommendations" icon="angles-right" horizontal />
</CardGroup>

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
