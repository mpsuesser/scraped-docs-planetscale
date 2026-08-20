---
url: https://planetscale.com/docs/cli/query-patterns
title: "Query Patterns"
description: ""
access_date: 2026-08-20T18:00:05.296Z
current_date: 2026-08-20T18:00:05.296Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The query-patterns command

List, inspect, delete, and download query pattern reports for a database branch. The `download` command creates a Query Insights report, waits for it to finish generating, and writes the CSV file locally.

Query pattern reports require Query Insights to be enabled for the database.

**Usage:**

```shellscript
pscale branch query-patterns <command> <database> <branch> --org <org>
```

### Available sub-commands

| **Sub-command** | **Product** | **Description** |
| --- | --- | --- |
| `list` | Postgres, Vitess | List query pattern reports for a branch |
| `show` | Postgres, Vitess | Show the status and details of a query pattern report |
| `delete` | Postgres, Vitess | Delete a query pattern report |
| `download` | Postgres, Vitess | Generate and download a CSV report of branch query patterns |

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `--limit <number>` | Number of reports to return, up to 100. Defaults to 25. Available for `list`. |
| `--starting-after <report-id>` | Fetch the next page after a report ID. Available for `list`. |
| `--force` | Delete a report without confirmation. Available for `delete`. |
| `--output <path>` | Output file for the CSV report. Defaults to `query-patterns-<organization>-<database>-<branch>-<timestamp>.csv` in the current directory. Available for `download`. |
| `--org <org>` | Organization name |
| `-f`, `--format <FORMAT>` | Show output in a specific format. Possible values: `human` (default), `json`, `csv`. |
| `-h`, `--help` | Help for `query-patterns` |

## Examples

List reports for a branch:

```shellscript
pscale branch query-patterns list <database> <branch> --org <org>
```

Show a report:

```shellscript
pscale branch query-patterns show <database> <branch> <report-id> --org <org>
```

Delete a report:

```shellscript
pscale branch query-patterns delete <database> <branch> <report-id> --org <org>
```

Generate and download a report with the default file name:

```shellscript
pscale branch query-patterns download <database> <branch> --org <org>
```

Generate and download a report to a specific path:

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
