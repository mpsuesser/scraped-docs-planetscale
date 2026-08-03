---
url: https://planetscale.com/docs/cli/audit-log
title: "Audit Log"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
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

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for `audit-log` command |
| `--action` | Filter based on action type |
| `--limit` int | The number of events to return. Min: 1, Max: 100 |
| `--starting-after` string | The ID of the audit log to start after (for pagination) |
| `--org <ORGANIZATION_NAME>` | The organization for the current user |

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

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
