---
url: https://planetscale.com/docs/cli/backup
title: "Backup"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The backup command

This command allows you to create, list, show, update, restore, and delete branch backups for Vitess, Neki, and Postgres databases, and manage scheduled backup policies.

**Usage:**

```shellscript
pscale backup <SUB-COMMAND> <FLAG>
```

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Description** | **Product** |
| --- | --- | --- | --- |
| `create <DATABASE_NAME> <BRANCH_NAME>` | `--name` | Backup a branch’s data and schema | Postgres, Vitess, Neki |
| `delete <DATABASE_NAME> <BRANCH_NAME> <BACKUP_ID>` | `--force` | Delete a branch backup | Postgres, Vitess, Neki |
| `list <DATABASE_NAME> <BRANCH_NAME>` |  | List all backups of a branch | Postgres, Vitess, Neki |
| `policy list <DATABASE_NAME>` |  | List backup policies for a database | Postgres, Vitess, Neki |
| `policy show <DATABASE_NAME> <POLICY_ID>` |  | Show a backup policy | Postgres, Vitess, Neki |
| `policy create <DATABASE_NAME>` | `--target` \*, `--retention-value` \*, `--retention-unit` \*, `--frequency-value` \*, `--frequency-unit` \*, `--schedule-time` \*, `--name`, `--schedule-day`, `--schedule-week` | Create a backup policy | Postgres, Vitess, Neki |
| `policy update <DATABASE_NAME> <POLICY_ID>` | `--name`, `--target`, `--retention-value`, `--retention-unit`, `--frequency-value`, `--frequency-unit`, `--schedule-time`, `--schedule-day`, `--schedule-week` | Update a backup policy | Postgres, Vitess, Neki |
| `policy delete <DATABASE_NAME> <POLICY_ID>` | `--force` | Delete a backup policy | Postgres, Vitess, Neki |
| `restore <DATABASE_NAME> <NEW_BRANCH_NAME> <BACKUP_ID>` | `--cluster-size`, `--replicas`, `--config-profile`, `--router` | Restore a backup to a new branch | Postgres, Vitess, Neki |
| `restore show <DATABASE_NAME> <SOURCE_BRANCH_NAME> <BACKUP_ID>` |  | Preview the Neki configuration-profile and router sizes a restore will use if you do not override them | Neki |
| `show <DATABASE_NAME> <BRANCH_NAME> <BACKUP_ID>` |  | Show a specific backup of a branch | Postgres, Vitess, Neki |
| `update <DATABASE_NAME> <BRANCH_NAME> <BACKUP_ID>` | `--protected` \* | Update a backup’s protected status | Postgres, Vitess, Neki |

> \* *Flag is required*

#### Sub-command flag descriptions

| **Sub-command flag** | **Description** | **Applicable sub-commands** |
| --- | --- | --- |
| `--name` | Optional name for an on-demand backup or backup policy. | `create`, `policy create`, `policy update` |
| `--cluster-size` | Cluster size for the restored branch. Optional for Neki; omit it to keep the source default configuration-profile size. | `restore` |
| `--replicas` | Number of additional replicas for a Postgres or Neki restore. `0` creates a single-node Postgres branch; Neki requires a highly available configuration. Omit to use the target cluster size default. Not used for Vitess. | `restore` |
| `--config-profile` | Neki restore override: `name=<profile>[,cluster-size=<size>][,replicas=<n>]`. Repeatable. Omitted profiles inherit the source. | `restore` |
| `--router` | Neki restore override: `name=<router>[,size=<sku>][,replicas-per-cell=<n>]`. Repeatable. Omitted routers inherit the source. | `restore` |
| `--target` | Branch target: `production` or `development`. | `policy create`, `policy update` |
| `--retention-value` | Retention period value. | `policy create`, `policy update` |
| `--retention-unit` | Retention unit: `hour`, `day`, `week`, `month`, or `year`. | `policy create`, `policy update` |
| `--frequency-value` | Frequency value. | `policy create`, `policy update` |
| `--frequency-unit` | Frequency unit: `hour`, `day`, `week`, or `month`. | `policy create`, `policy update` |
| `--schedule-time` | Schedule time of day in `HH:MM` format. | `policy create`, `policy update` |
| `--schedule-day` | Day of week (`0` =Sunday … `6` =Saturday); used for weekly/monthly schedules. | `policy create`, `policy update` |
| `--schedule-week` | Week of month (`0` =first … `3` =fourth); used for monthly schedules. | `policy create`, `policy update` |
| `--force` | Delete a backup or backup policy without confirmation. | `delete`, `policy delete` |
| `--protected` | Protect the backup from deletion (`--protected=false` to disable). Required on `update`. | `update` |

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
pscale backup show <DATABASE_NAME> <BRANCH_NAME> <BACKUP_ID>
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

### Restore a backup to a new branch

`<NEW_BRANCH_NAME>` is the branch to create. It must not already exist. Identify the backup by ID; the source branch is not a restore argument.

```shellscript
pscale backup restore <DATABASE_NAME> <NEW_BRANCH_NAME> <BACKUP_ID>
```

For Neki, omitted configuration-profile and router sizes inherit the live source branch. Preview those defaults first:

```shellscript
pscale backup restore show <DATABASE_NAME> <SOURCE_BRANCH_NAME> <BACKUP_ID>
```

Override individual Neki configuration profiles or routers. `name` is required; size and replica count are optional per entry.

```shellscript
pscale backup restore <DATABASE_NAME> <NEW_BRANCH_NAME> <BACKUP_ID> \
  --config-profile name=default,cluster-size=PS_40,replicas=2 \
  --config-profile name=analytics,replicas=2 \
  --router name=default,size=NKR-20,replicas-per-cell=1
```

`--config-profile` and `--router` are Neki-only. Sidecar, admin, and parameter settings are not restored.

You can also restore with [`pscale branch create --restore`](branch.md).

### Protect a backup from deletion

`--protected` is required. Use `--protected=false` to turn protection off.

```shellscript
pscale backup update <DATABASE_NAME> <BRANCH_NAME> <BACKUP_ID> --protected
pscale backup update <DATABASE_NAME> <BRANCH_NAME> <BACKUP_ID> --protected=false
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
