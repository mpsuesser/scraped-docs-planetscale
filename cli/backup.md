---
url: https://planetscale.com/docs/cli/backup
title: "Backup"
description: ""
access_date: 2026-08-14T00:39:58.404Z
current_date: 2026-08-14T00:39:58.404Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The backup command

This command allows you to create, list, show, and delete [branch backups](../vitess/backups.md), and manage scheduled backup policies.

**Usage:**

```shellscript
pscale backup <SUB-COMMAND> <FLAG>
```

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Description** | **Product** |
| --- | --- | --- | --- |
| `create <DATABASE_NAME> <BRANCH_NAME>` |  | Backup a branch’s data and schema | Postgres, Vitess |
| `delete <DATABASE_NAME> <BRANCH_NAME> <BACKUP_ID>` |  | Delete a branch backup | Postgres, Vitess |
| `list <DATABASE_NAME> <BRANCH_NAME>` |  | List all backups of a branch | Postgres, Vitess |
| `policy list <DATABASE_NAME>` |  | List backup policies for a database | Postgres, Vitess |
| `policy show <DATABASE_NAME> <POLICY_ID>` |  | Show a backup policy | Postgres, Vitess |
| `policy create <DATABASE_NAME>` | `--target` \*, `--retention-value` \*, `--retention-unit` \*, `--frequency-value` \*, `--frequency-unit` \*, `--schedule-time` \*, `--name`, `--schedule-day`, `--schedule-week` | Create a backup policy | Postgres, Vitess |
| `policy update <DATABASE_NAME> <POLICY_ID>` | `--name`, `--target`, `--retention-value`, `--retention-unit`, `--frequency-value`, `--frequency-unit`, `--schedule-time`, `--schedule-day`, `--schedule-week` | Update a backup policy | Postgres, Vitess |
| `policy delete <DATABASE_NAME> <POLICY_ID>` | `--force` | Delete a backup policy | Postgres, Vitess |
| `restore <DATABASE_NAME> <BRANCH_NAME> <BACKUP_ID>` |  | Restore a backup to a new branch | Postgres, Vitess |
| `show <DATABASE_NAME> <BRANCH_NAME> <BACKUP_ID>` |  | Show a specific backup of a branch | Postgres, Vitess |

> \* *Flag is required*

#### Sub-command flag descriptions

| **Sub-command flag** | **Description** | **Applicable sub-commands** |
| --- | --- | --- |
| `--name` | Optional name for the backup policy. | `policy create`, `policy update` |
| `--target` | Branch target: `production` or `development`. | `policy create`, `policy update` |
| `--retention-value` | Retention period value. | `policy create`, `policy update` |
| `--retention-unit` | Retention unit: `hour`, `day`, `week`, `month`, or `year`. | `policy create`, `policy update` |
| `--frequency-value` | Frequency value. | `policy create`, `policy update` |
| `--frequency-unit` | Frequency unit: `hour`, `day`, `week`, or `month`. | `policy create`, `policy update` |
| `--schedule-time` | Schedule time of day in `HH:MM` format. | `policy create`, `policy update` |
| `--schedule-day` | Day of week (`0` =Sunday … `6` =Saturday); used for weekly/monthly schedules. | `policy create`, `policy update` |
| `--schedule-week` | Week of month (`0` =first … `3` =fourth); used for monthly schedules. | `policy create`, `policy update` |
| `--force` | Delete a backup policy without confirmation. | `policy delete` |

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for `backup` command |
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
pscale backup list <DATABASE_NAME> <BRANCH_NAME> --org <ORGANIZATION_NAME>
```

**Output:**

```shellscript
ID             NAME                  STATE     SIZE    CREATED AT    UPDATED AT    STARTED AT    EXPIRES AT          COMPLETED AT
-------------- --------------------- --------- ------- ------------- ------------- ------------- ------------------- --------------
xxxxxxxx   2022.02.11 16:01:03   success   24.1M   3 hours ago   3 hours ago   3 hours ago   1 day from now      3 hours ago
xxxxxxxx   2022.02.10 16:01:03   success   23.2M   1 day ago     1 day ago     1 day ago     20 hours from now   1 day ago
```

### The show sub-command

**Command:**

```shellscript
pscale backup list <DATABASE_NAME> <BRANCH_NAME> <BACKUP_ID>
```

You can find the `<BACKUP_ID>` by running the `pscale backup list <DATABASE_NAME> <BRANCH_NAME>` command.

**Output:**

```shellscript
ID             NAME                  STATE     SIZE    CREATED AT    UPDATED AT    STARTED AT    EXPIRES AT          COMPLETED AT
-------------- --------------------- --------- ------- ------------- ------------- ------------- ------------------- --------------
xxxxxxxx   2022.02.11 16:01:03   success   24.1M   3 hours ago   3 hours ago   3 hours ago   1 day from now      3 hours ago
```

### Manage backup policies

Backup policies define automatic backup frequency, schedule, and retention for production or development branches. This is separate from one-off backups created with `pscale backup create`.

```shellscript
pscale backup policy list <DATABASE_NAME>
pscale backup policy create <DATABASE_NAME> \
  --target production \
  --retention-value 7 \
  --retention-unit day \
  --frequency-value 1 \
  --frequency-unit day \
  --schedule-time 02:00
pscale backup policy update <DATABASE_NAME> <POLICY_ID> --retention-value 14
pscale backup policy delete <DATABASE_NAME> <POLICY_ID>
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
