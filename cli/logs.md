---
url: https://planetscale.com/docs/cli/logs
title: "Logs"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The logs command

Query recent logs for a Postgres or Neki branch. By default, the command returns up to 100 entries from the primary server over the last hour, newest first.

**Usage:**

```shellscript
pscale logs <DATABASE_NAME> <BRANCH_NAME> --org <ORGANIZATION_NAME> <FLAG>
```

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `--org <ORGANIZATION_NAME>` | Organization name **(required)** |
| `--query <QUERY>` | Text or LogsQL expression to search for |
| `--period <PERIOD>` | Time period to query: `15m`, `1h`, `3h`, `6h`, `12h`, `1d`, `7d`, or `8d`. Defaults to `1h`. |
| `--from <TIMESTAMP>` | Start of a custom ISO 8601 time range. Requires `--to`. |
| `--to <TIMESTAMP>` | End of a custom ISO 8601 time range. Requires `--from`. |
| `--level <LEVEL>` | Include `DEBUG`, `INFO`, `WARNING`, or `ERROR` entries. Repeat the flag or pass a comma-separated list. |
| `--server <SERVER>` | Include the `primary` role or a named server. Repeat the flag or pass a comma-separated list. Defaults to `primary`. |
| `--shard <SHARD>` | Include a Neki shard. Repeat the flag or pass a comma-separated list. |
| `--limit <COUNT>` | Maximum number of entries to return. Defaults to `100`. |
| `--page <NUMBER>` | Page to fetch. Defaults to `1`. |
| `-h`, `--help` | Help for `logs` |

`--period` cannot be combined with `--from` and `--to`. When you use a custom range, provide both timestamps.

### Global flags

| **Flag** | **Description** |
| --- | --- |
| `-f`, `--format <FORMAT>` | Output format: `human` (default), `json`, or `csv` |
| `--api-token`, `--service-token-id`, `--service-token` | Authentication |
| `--api-url <URL>` | API base URL |
| `--config <FILE>` | Config file |
| `--debug` | Enable debug mode |
| `--no-color` | Disable color output |

## Examples

Show recent primary logs:

```shellscript
pscale logs mydatabase main --org myorganization
```

Show errors from the last six hours as JSON:

```shellscript
pscale logs mydatabase main --org myorganization \
  --period 6h --level ERROR --format json
```

Filter a Neki branch to selected shards:

```shellscript
pscale logs mydatabase main --org myorganization \
  --shard shard-a --shard shard-b
```

Query a custom time range:

```shellscript
pscale logs mydatabase main --org myorganization \
  --from 2026-08-20T16:00:00Z \
  --to 2026-08-20T18:00:00Z
```

Log output can contain query text and application data. Review it before sharing or attaching it to a support request.

## Related documentation

## Neki logs

## Postgres logs

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
