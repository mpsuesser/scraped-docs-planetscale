---
url: https://planetscale.com/docs/cli/maintenance
title: "Maintenance"
description: ""
access_date: 2026-08-17T22:26:09.446Z
current_date: 2026-08-17T22:26:09.446Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The maintenance command

List and show [maintenance schedules](../plans/managed/maintenance-schedules.md) for a Vitess database on an Enterprise plan.

Maintenance schedules define when PlanetScale can perform planned maintenance (for example Vitess or MySQL version updates). This command is read-only. **`--org` is required.**

**Usage:**

```shellscript
pscale maintenance <SUB-COMMAND> <FLAG>
```

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Description** | **Product** |
| --- | --- | --- | --- |
| `list <DATABASE_NAME>` |  | List maintenance schedules for a database | Vitess |
| `show <DATABASE_NAME> <SCHEDULE_ID>` |  | Show a maintenance schedule | Vitess |
| `windows <DATABASE_NAME> <SCHEDULE_ID>` |  | List maintenance windows for a schedule | Vitess |

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for the `maintenance` command |
| `--org <ORGANIZATION_NAME>` | The organization for the current user (required) |

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

### List schedules

```shellscript
pscale maintenance list <DATABASE_NAME> --org <ORGANIZATION_NAME>
```

### Show a schedule

```shellscript
pscale maintenance show <DATABASE_NAME> <SCHEDULE_ID> --org <ORGANIZATION_NAME>
```

### List windows for a schedule

```shellscript
pscale maintenance windows <DATABASE_NAME> <SCHEDULE_ID> --org <ORGANIZATION_NAME>
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
