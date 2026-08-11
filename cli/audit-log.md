---
url: https://planetscale.com/docs/cli/audit-log
title: "Audit Log"
description: ""
access_date: 2026-08-11T16:46:50.137Z
current_date: 2026-08-11T16:46:50.137Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The audit log command

Lists all [audit logs](../security/audit-log.md) in an organization. The user running the command must have [Organization-level permissions](../security/access-control.md), specifically `list_organization_audit_logs`.

**Usage:**

```shellscript
pscale audit-log <SUB-COMMAND> <FLAG>
```

### Available sub-commands

| **Sub-command** | **Description** | **Product** |
| --- | --- | --- |
| `list` | List all audit logs in an organization | Postgres, Vitess |
| `auth-attempts download` | Download a report of database authentication attempts | Postgres |

### The list sub-command flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for `audit-log` command |
| `--action` | Filter based on action type |
| `--limit` int | The number of events to return. Min: 1, Max: 100 |
| `--starting-after` string | The ID of the audit log to start after (for pagination) |
| `--org <ORGANIZATION_NAME>` | The organization for the current user |

## The auth-attempts download sub-command

Generates a report of authentication attempts against your Postgres databases and downloads it as a ZIP archive. Each authentication attempt includes information such as the source IP address, credential, targeted database and branch, outcome, and failure details.

Available to [Organization Administrators](../security/access-control.md#organization-administrator) only. Service tokens cannot generate these reports.

**Usage:**

```shellscript
pscale audit-log auth-attempts download [flags]
```

The command generates the report, waits for it to be ready, and writes the archive to disk. The archive contains two files: the data (`auth-attempts.csv`, `.jsonl`, or `.parquet`) and a `manifest.json` recording the exact window and filters used.

### Time window flags

Specify either `--since` or `--start-at`. Named values and timestamps without a zone use your local timezone; requests are sent in UTC.

| **Flag** | **Description** |
| --- | --- |
| `--since` | Window ending now, as a positive duration: `24h`, `7d`, `2w` |
| `--start-at` | Inclusive start: `today`, `yesterday`, `YYYY-MM-DD`, local ISO, or RFC3339 |
| `--end-at` | Exclusive end: `now`, or any format `--start-at` accepts. Defaults to now |

The window is inclusive of `start-at` and exclusive of `end-at`.

### Filter flags

Filters combine with AND. Repeat a flag to match any of several values.

| **Flag** | **Description** |
| --- | --- |
| `--source-ip` | Source IPv4 address or CIDR range (repeat or comma-separate) |
| `--branch` | Branch public ID or `database/branch` name (repeat or comma-separate) |
| `--username` | Authentication username (repeatable; commas are part of the name, not separators) |
| `--startup-database` | Startup database name (repeatable) |
| `--outcome` | `allow` or `deny` |
| `--failure-reason` | `bad_password`, `unknown_user`, `authorization_failed`, `ip_not_allowed`, or `other` |
| `--backend-route` | `postgres`, `pgbouncer`, or `unknown` |

### Output flags

| **Flag** | **Description** |
| --- | --- |
| `--export-format` | `csv`, `jsonl`, or `parquet`. Defaults from `--format`: `human` and `csv` produce CSV, `json` produces JSONL. `parquet` must be requested explicitly |
| `--output` | Output file name, or `-` to write raw ZIP bytes to stdout |
| `--org` | The organization for the current user |

### Global flags

| **Command** | **Description** |
| --- | --- |
| `--api-token <TOKEN>` | The API token to use for authenticating against the PlanetScale API. |
| `--api-url <URL>` | The base URL for the PlanetScale API. Default is `https://api.planetscale.com/`. |
| `--config <CONFIG_FILE>` | Config file. Default is `$HOME/.config/planetscale/pscale.yml`. |
| `--debug` | Enable debug mode. |
| `-f`, `--format <FORMAT>` | Show output in a specific format. Possible values: `human` (default), `json`, `csv`. |
| `--no-color` | Disable color output. |
| `--service-token <TOKEN>` | The service token for authenticating. |
| `--service-token-id <TOKEN_ID>` | The service token ID for authenticating. |

## Examples

### The list sub-command with --org flag

**Command:**

```shellscript
pscale audit-log list --org <ORGANIZATION_NAME>
```

**Output:**

```shellscript
ID (25)      ACTOR (25)  ACTION                   EVENT                     REMOTE IP      LOCATION         CREATED AT
------------- ----------- ------------------------ ------------------------ --------------- ---------------- ------------
xxxxxxxxxx  Name        Open_web_console main    branch.open_web_console  xxx.xxx.xxx.x   Los Angeles, CA  1 day ago
```

### Pagination

Use the ID from the last result and pass it as the `--starting-after` to retrieve the next page of results.

```shellscript
pscale audit-log list --limit 5 --starting-after <ID>
```

### Download authentication attempts

Export the last 24 hours:

```shellscript
pscale audit-log auth-attempts download --since 24h --output auth-attempts.zip
```

**Output:**

```shellscript
Generating authentication-attempt export for <ORGANIZATION_NAME>...
Successfully downloaded auth attempts (csv) to auth-attempts.zip
```

Filter by a source IP address or range:

```shellscript
pscale audit-log auth-attempts download --since 7d \
  --source-ip 203.0.113.10 --outcome deny --output suspect-ip.zip
```

Filter by authentication username:

```shellscript
pscale audit-log auth-attempts download --since 7d \
  --username app_readonly --outcome deny --output credential.zip
```

Export a fixed window as Parquet:

```shellscript
pscale audit-log auth-attempts download \
  --start-at 2026-07-29T00:00:00Z --end-at 2026-07-30T00:00:00Z \
  --export-format parquet --output window.zip
```

Write the archive to stdout instead of directly to a file:

```shellscript
pscale audit-log auth-attempts download --since 24h --output - > report.zip
```

See [Authentication attempts](../postgres/monitoring/authentication-attempts.md) for what the report contains and how to work with it.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
