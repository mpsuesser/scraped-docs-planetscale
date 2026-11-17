---
url: https://planetscale.com/docs/cli/insights
title: "Insights"
description: ""
access_date: 2026-11-17T22:48:04.450Z
current_date: 2026-11-17T22:48:04.450Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The insights command

Surface PlanetScale’s server-side analysis of a database: aggregated query statistics, failing query patterns, resource anomalies, and schema recommendations, all computed from production traffic.

For live, connection-level diagnostics (table sizes, locks, running queries), use [`pscale inspect`](inspect.md) instead.

Query insights require Query Insights to be enabled for the database. See [Postgres](../postgres/monitoring/query-insights.md), [Vitess](../vitess/monitoring/query-insights.md), or [Neki](../neki/monitoring/query-insights.md).

**Usage:**

```shellscript
pscale insights <sub-command> <database> [<branch>] --org <org> <FLAG>
```

Place **positional arguments first**, then flags. **`--org` is required.**

### Available sub-commands

| **Sub-command** | **Product** | **Description** |
| --- | --- | --- |
| `queries` | Postgres, Vitess, Neki | List top queries ranked by a performance metric |
| `queries samples` | Postgres, Vitess, Neki | List recent executions for a query fingerprint |
| `queries show` | Postgres, Vitess, Neki | Show one query execution by sample ID |
| `queries summary` | Postgres, Vitess, Neki | Show aggregate statistics for a query fingerprint |
| `queries traffic-budgets` | Postgres, Vitess | List traffic budgets that affect a query fingerprint |
| `errors` | Postgres, Vitess, Neki | List queries that are failing with errors |
| `errors show` | Postgres, Vitess, Neki | List the individual query executions that failed with an error fingerprint |
| `anomalies` | Postgres, Vitess, Neki | List detected resource anomalies (CPU, memory, IOPS, rows read/written) |
| `anomalies show` | Postgres, Vitess, Neki | Show one anomaly and the queries correlated with it |
| `tags` | Postgres, Vitess, Neki | List query tag keys (sqlcommenter / system) |
| `tags show` | Postgres, Vitess, Neki | Show a query tag key and its values |
| `tags summaries` | Postgres, Vitess, Neki | List query statistics grouped by tag keys |
| `recommendations` | Postgres, Vitess, Neki | List schema recommendations with ready-to-apply DDL |
| `recommendations show` | Postgres, Vitess, Neki | Show one schema recommendation and its full DDL |
| `recommendations dismiss` | Postgres, Vitess, Neki | Dismiss a schema recommendation |
| `agent runs` | Postgres, Vitess, Neki | List Insights Agent runs on a branch, newest first |
| `agent runs show` | Postgres, Vitess, Neki | Show an Insights Agent run, its stages, and the pull request it opened |
| `agent runs create` | Postgres, Vitess, Neki | Start an Insights Agent run that reviews slow queries or implements schema recommendations |
| `agent runs cancel` | Postgres, Vitess, Neki | Cancel a pending or running Insights Agent run |
| `agent schedules` | Postgres, Vitess, Neki | List the Insights Agent schedules on a branch |
| `agent schedules update` | Postgres, Vitess, Neki | Turn on, turn off, or change the schedule for a run type |

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

`queries show` takes an individual execution/sample `id` from `queries samples`. `queries samples` and `queries summary` take a query `fingerprint`. These identifiers are not interchangeable.

### The queries show sub-command

Show one query execution using an ID returned by `pscale insights queries samples`.

```shellscript
pscale insights queries show <database> <branch> <query-id> --org <org>
```

### The queries summary sub-command

Show aggregate statistics for a fingerprint from `pscale insights queries`. `--keyspace` is required (use the keyspace column from the queries list).

**Available flags:**

| **Flag** | **Description** |
| --- | --- |
| `--keyspace <name>` | Keyspace for the fingerprint (required; from `insights queries`) |
| `--period <duration>` | Named time period to summarize (for example `1h`, `12h`, or `1d`) |
| `--from <timestamp>` | Start of a custom time range as an ISO 8601 timestamp (use with `--to`) |
| `--to <timestamp>` | End of a custom time range as an ISO 8601 timestamp (use with `--from`) |

`--period` cannot be combined with `--from` and `--to`. Both range flags must be set together.

```shellscript
pscale insights queries summary <database> <branch> <fingerprint> --org <org> --keyspace <keyspace>
pscale insights queries summary <database> <branch> <fingerprint> --org <org> --keyspace <keyspace> --period 1h
```

### The queries traffic-budgets sub-command

List traffic budgets that affect a query fingerprint. Pass `--keyspace` from the queries list. The command is paginated; pass `--page` for the next page instead of walking every page automatically.

**Available flags:**

| **Flag** | **Description** |
| --- | --- |
| `--keyspace <name>` | Keyspace for the fingerprint (from `insights queries`) |
| `--page <n>` | Page of results to fetch. |
| `--per-page <n>` | Number of results per page. Default: `25`. |

```shellscript
pscale insights queries traffic-budgets <database> <branch> <fingerprint> --org <org> --keyspace <keyspace>
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

#### errors show

List the individual executions that failed with an error fingerprint, including the users, keyspaces, and statements involved. Use the fingerprint column from `insights errors` (`error_fingerprint` in JSON), not the truncated id.

**Available flags:**

| **Flag** | **Description** |
| --- | --- |
| `--limit <n>` | Number of queries to return. Default: `25`. |
| `--period <duration>` | Time period to look back (for example `1h`, `1d`). |

```shellscript
pscale insights errors show <database> <branch> <fingerprint> --org <org>
pscale insights errors show <database> <branch> <fingerprint> --org <org> --period 1h --limit 50
```

### The anomalies sub-command

List detected resource anomalies for a branch.

**Usage:**

```shellscript
pscale insights anomalies <database> <branch> --org <org> <FLAG>
```

#### anomalies show

Show one anomaly from `insights anomalies`, along with the queries whose activity correlates with it.

```shellscript
pscale insights anomalies show <database> <branch> <anomaly-id> --org <org>
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

#### recommendations show

Show a single schema recommendation, including the full ready-to-apply DDL. `<number>` is the recommendation sequence number from `pscale insights recommendations`, the same value used by `recommendations dismiss`.

```shellscript
pscale insights recommendations show <database> <number> --org <org>
```

#### recommendations dismiss

Dismiss a schema recommendation. Use the recommendation number from `pscale insights recommendations`. Interactive confirmation is required unless `--force` is passed.

```shellscript
pscale insights recommendations dismiss <database> <number> --org <org> --force --reason "not applicable"
```

If the database or branch is not found, or Query Insights is not enabled, the command returns an error explaining both possible causes.

### The agent sub-command

Start and review [Insights Agent](../insights-agent.md) runs and schedules. The agent reviews slow queries or implements schema recommendations against the repository connected to a database, and opens a pull request with the changes it makes.

Runs belong to a branch. Each run reviews up to five slow query patterns or implements up to five schema recommendations from that branch. The database needs a connected repository.

If the branch is not found, or the Insights Agent is not enabled for the organization, the command returns an error explaining both possible causes.

**Usage:**

```shellscript
pscale insights agent <command> <database> <branch> --org <org> <FLAG>
```

#### agent runs

List Insights Agent runs on a branch, newest first.

**Available flags:**

| **Flag** | **Description** |
| --- | --- |
| `--fingerprint <fp>` | Only list runs that reviewed the slow query with this fingerprint |
| `--keyspace <name>` | Keyspace of the `--fingerprint` query |
| `--schema-recommendation <number>` | Only list runs that implemented the schema recommendation with this number |
| `--page <n>` | Page number to fetch |
| `--per-page <n>` | Number of results per page. Default: `25`. |

`--keyspace` requires `--fingerprint`.

```shellscript
pscale insights agent runs <database> <branch> --org <org>
pscale insights agent runs <database> <branch> --org <org> --fingerprint <fingerprint> --keyspace <keyspace>
pscale insights agent runs <database> <branch> --org <org> --schema-recommendation <number>
```

#### agent runs show

Show an Insights Agent run, its stages, and the pull request it opened. `<number>` is the run number from `pscale insights agent runs`.

```shellscript
pscale insights agent runs show <database> <branch> <number> --org <org>
pscale insights agent runs show <database> <branch> <number> --org <org> --format json
```

#### agent runs create

Start an Insights Agent run on a branch. The database needs a connected repository, and the agent opens a pull request against it with the changes it makes.

Pass up to five slow query fingerprints with `--fingerprint` and their `--keyspace` to review slow queries, or up to five schema recommendation numbers with `--schema-recommendation` to implement them. Find fingerprints with `pscale insights queries` and recommendation numbers with `pscale insights recommendations`. `--fingerprint` and `--schema-recommendation` cannot be combined. Pass one of the two, not both.

**Available flags:**

| **Flag** | **Description** |
| --- | --- |
| `--fingerprint <fp>` | Fingerprint of a slow query to review (repeat or comma-separate) |
| `--keyspace <name>` | Keyspace of the `--fingerprint` queries |
| `--schema-recommendation <number>` | Number of a schema recommendation to implement (repeat or comma-separate) |

`--fingerprint` and `--keyspace` must be passed together.

```shellscript
pscale insights agent runs create <database> <branch> --org <org> --fingerprint <fingerprint> --keyspace <keyspace>
pscale insights agent runs create <database> <branch> --org <org> --schema-recommendation 3,4
```

#### agent runs cancel

Cancel a pending or running Insights Agent run. Interactive confirmation is required unless `--force` is passed.

```shellscript
pscale insights agent runs cancel <database> <branch> <number> --org <org> --force
```

#### agent schedules

List the Insights Agent schedules on a branch.

```shellscript
pscale insights agent schedules <database> <branch> --org <org>
```

#### agent schedules update

Turn on, turn off, or change when the Insights Agent runs on its own for one run type on a branch. `<run-type>` is `slow_query` or `schema_recommendation`.

Scheduled runs choose their own targets: slow query runs look at the queries Insights recorded in the last 24 hours, and schema recommendation runs work on the branch’s open recommendations. A run with nothing to work on is skipped.

Flags that are not passed keep their current value. At least one of the flags below is required.

**Available flags:**

| **Flag** | **Description** |
| --- | --- |
| `--enabled` | Whether the schedule runs |
| `--frequency <unit>` | How often the schedule runs: `day`, `week`, or `month` |
| `--time <HH:MM>` | The local time of day to run, in HH:MM format |
| `--time-zone <zone>` | The IANA time zone for `--time`, such as `America/New_York` |
| `--day-of-week <0-6>` | The day of the week for weekly schedules, from 0 (Sunday) to 6 (Saturday) |
| `--day-of-month <1-31>` | The day of the month for monthly schedules, from 1 to 31. Shorter months run on their last day. |

```shellscript
pscale insights agent schedules update <database> <branch> slow_query --org <org> --enabled --frequency week --day-of-week 1 --time 03:00 --time-zone America/New_York
pscale insights agent schedules update <database> <branch> schema_recommendation --org <org> --enabled=false
```

## Related documentation

## pscale inspect

## Insights Agent

## Query Insights (Postgres)

## Query Insights (Vitess)

## Query Insights (Neki)

## Anomalies (Neki)

## Schema recommendations (Postgres)

## Schema recommendations (Neki)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
