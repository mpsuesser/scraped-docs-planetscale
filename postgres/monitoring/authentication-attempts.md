---
url: https://planetscale.com/docs/postgres/monitoring/authentication-attempts
title: "Authentication Attempts"
description: ""
access_date: 2026-08-11T16:46:50.137Z
current_date: 2026-08-11T16:46:50.137Z
---

Authentication attempt reports provide a record of attempts to authenticate against your Postgres databases. You can use them to review connection activity, troubleshoot authentication failures, investigate unexpected access, or export authentication data for further analysis.

Each record includes information such as the client IP address, credential used, targeted database and branch, and whether the attempt was allowed or denied.

The `source_ip` in the report is the address of the client that connected to PlanetScale. This is especially useful for connections routed through a pooler, where Postgres server logs may show a PlanetScale infrastructure address instead of the original client address.

Generating a report is limited to [Organization Administrators](../../security/access-control.md#organization-administrator). Service tokens cannot generate reports.

## Generate a report

Use the [`pscale` CLI](../../cli/audit-log.md) to generate and download a report. Specify a time window and, optionally, filters:

```shellscript
pscale audit-log auth-attempts download --since 24h --output auth-attempts.zip
```

```shellscript
Generating authentication-attempt export for acme...
Successfully downloaded auth attempts (csv) to auth-attempts.zip
```

The download link expires 24 hours after the report is generated. If you need to download the report again after that, generate a new report.

## What’s in the archive

Every archive contains exactly two files:

| File | Contents |
| --- | --- |
| `auth-attempts.csv` (or `.jsonl`, `.parquet`) | One row per authentication attempt |
| `manifest.json` | The time window, filters, format, and other metadata for the report |

### The data

| Column | Description |
| --- | --- |
| `occurred_at` | When the attempt happened, UTC, to the millisecond |
| `source_ip` | The IP address the client connected from |
| `source_port` | The client’s source port |
| `organization_id`, `organization_name` | The organization |
| `database_id`, `database_name` | The database the attempt targeted |
| `branch_id`, `branch_name` | The branch the attempt targeted |
| `username` | The credential used |
| `startup_database` | The database named in the connection’s startup message |
| `outcome` | `allow` or `deny` |
| `failure_reason` | Why it was denied. Empty when the attempt succeeded |
| `sqlstate` | The Postgres SQLSTATE returned on failure |
| `backend_route` | `postgres` or `pgbouncer`, depending on the connection path |
| `auth_protocol` | The wire protocol used |

Denials carry one of these reasons:

| `failure_reason` | Meaning |
| --- | --- |
| `bad_password` | The credential exists but the password did not match |
| `unknown_user` | No such credential |
| `authorization_failed` | Authenticated, but not permitted to proceed |
| `ip_not_allowed` | Blocked by an IP allow list |
| `other` | Anything else |

### The manifest

`manifest.json` records how the report was generated:

```json
{
  "schema_version": 1,
  "format": "csv",
  "generated_at": "2026-08-10T14:46:20Z",
  "organization": { "id": "...", "name": "acme" },
  "window": {
    "start_at": "2026-08-09T00:00:00Z",
    "end_at": "2026-08-10T00:00:00Z",
    "semantics": "[start_at,end_at)"
  },
  "filters": { "source_ips": ["203.0.113.10/32"] },
  "resolved_branch_public_ids": ["..."]
}
```

Keep the manifest with the exported data when storing or sharing a report. It records the time window and filters used to produce the export.

The report window uses `[start_at,end_at)` semantics: `start_at` is inclusive and `end_at` is exclusive.

## Example queries

### Filter by source IP

Pass a single address or a whole CIDR range:

```shellscript
pscale audit-log auth-attempts download --since 7d \
  --source-ip 203.0.113.10 --output suspect-ip.zip
```

```shellscript
pscale audit-log auth-attempts download --since 7d \
  --source-ip 203.0.113.0/24 --source-ip 198.51.100.0/24 --output suspect-range.zip
```

### Filter by credential

Use `--username` to find attempts made with a particular credential. For example, to return only denied attempts:

```shellscript
pscale audit-log auth-attempts download --since 7d \
  --username app_readonly --outcome deny --output credential.zip
```

### Export a time window for analysis

Omit filters to export all authentication attempts in a time window.

For larger datasets, Parquet can be useful for analysis with tools such as DuckDB:

```shellscript
pscale audit-log auth-attempts download \
  --start-at 2026-07-29T00:00:00Z --end-at 2026-07-30T00:00:00Z \
  --export-format parquet --output window.zip

unzip -q window.zip -d window
duckdb -c "
  SELECT username, source_ip, count(*) AS attempts
  FROM 'window/auth-attempts.parquet'
  WHERE outcome = 'deny'
  GROUP BY username, source_ip
  ORDER BY attempts DESC
  LIMIT 20;"
```

Because the archive includes its manifest, you can also share or retain the complete archive without separately recording the parameters used to generate it.

## Combining filters

Filters combine with AND across different flags, and OR within a repeated flag. This request means “denied attempts, from either of these two ranges, using either of these two credentials”:

```shellscript
pscale audit-log auth-attempts download --since 24h \
  --outcome deny \
  --source-ip 203.0.113.0/24 --source-ip 198.51.100.0/24 \
  --username app_readonly --username reporting
```

## Choosing a format

| Format | Good for |
| --- | --- |
| `csv` | Spreadsheets, quick inspection, most SIEM ingest paths |
| `jsonl` | Log pipelines and tools that process one object per line |
| `parquet` | Analysis over larger datasets and querying with tools such as DuckDB or a data warehouse |

CSV is the default. JSONL is selected by `--format json`, and Parquet must be requested with `--export-format parquet`.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
