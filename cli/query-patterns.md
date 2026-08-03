---
url: https://planetscale.com/docs/cli/query-patterns
title: "Query Patterns"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The query-patterns command

Download a CSV report of the query patterns for a database branch. The command creates a Query Insights report, waits for the report to finish generating, and writes the CSV file locally.

Query pattern reports require Query Insights to be enabled for the database.

**Usage:**

```shellscript
pscale branch query-patterns download <database> <branch> --org <org> <FLAG>
```

### Available sub-commands

| **Sub-command** | **Product** | **Description** |
| --- | --- | --- |
| `download` | Postgres, Vitess | Generate and download a CSV report of branch query patterns |

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `--output <path>` | Output file for the CSV report. Defaults to `query-patterns-<organization>-<database>-<branch>-<timestamp>.csv` in the current directory. |
| `--org <org>` | Organization name |
| `-f`, `--format <FORMAT>` | Show the download result in a specific format. Possible values: `human` (default), `json`, `csv`. |
| `-h`, `--help` | Help for `query-patterns` |

## Examples

Download a report with the default file name:

```shellscript
pscale branch query-patterns download <database> <branch> --org <org>
```

Download a report to a specific path:

```shellscript
pscale branch query-patterns download <database> <branch> --org <org> --output ./query-patterns.csv
```

Use JSON output for automation:

```shellscript
pscale branch query-patterns download <database> <branch> --org <org> --format json
```

The JSON response includes the report ID, final state, and local file path:

```json
{
  "id": "report1",
  "state": "completed",
  "file": "./query-patterns.csv"
}
```

If the branch is not found, or Query Insights is not enabled for the database, the command returns an error explaining both possible causes.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
