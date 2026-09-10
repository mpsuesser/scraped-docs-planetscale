---
url: https://planetscale.com/docs/cli/branch
title: "Branch"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The branch command

This command creates and manages Vitess, Postgres, and [Neki branches](../neki/branching.md). It also contains the Neki configuration, shard, router, and data-topology commands.

**Usage:**

```shellscript
pscale branch <SUB-COMMAND> <FLAG>
```

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Description** | **Product** |
| --- | --- | --- | --- |
| `admin <COMMAND>` | `show`, `update`, `parameters`, `sizes`, `changes` (`list`, `show`, `cancel`) | Manage the Admin configuration for a Neki branch | Neki |
| `connections <COMMAND>` | `show`, `top`, `kill`, `kill-transaction` | Inspect and act on live branch connections. See the [`connections` reference](connections.md). | Postgres, Vitess |
| `config-profile <COMMAND>` | `create`, `default`, `delete`, `list`, `set-default`, `show`, `update`, `parameters`, `extensions` (`enable`, `disable`), `maintenance`, `changes` (`list`, `show`, `cancel`) | Manage configuration profiles for a Neki branch | Neki |
| `create <DATABASE_NAME> <BRANCH_NAME>` | `--from <SOURCE_BRANCH>`, `--region <BRANCH_REGION>`, `--restore <BACKUP_ID>`, `--restore-point <TIMESTAMP>`, `--seed-data`, `--cluster-size <CLUSTER_SIZE>`, `--replicas <COUNT>`, `--major-version <MAJOR_VERSION>`, `--min-storage <BYTES>`, `--max-storage <BYTES>`, `--config-profile`, `--router`, `--wait` | Create a new branch on the specified database | Postgres, Vitess, Neki |
| `data-topology <COMMAND>` | `get`, `ls` (`--shards`), `update` | View or update a Neki branch’s data topology | Neki |
| `delete <DATABASE_NAME> <BRANCH_NAME>` | `--force`, `--delete-descendants` | Delete the specified branch from a database | Postgres, Vitess, Neki |
| `demote <DATABASE_NAME> <BRANCH_NAME>` |  | Demote a production branch to development | Vitess |
| `diff <DATABASE_NAME> <BRANCH_NAME>` | `--web` | Show the diff of the specified branch against the parent branch. | Vitess |
| `extensions list <DATABASE_NAME> <BRANCH_NAME>` |  | List the extensions available on a Postgres branch’s cluster image | Postgres |
| `lint <DATABASE_NAME> <BRANCH_NAME>` |  | Lint the schema of a branch | Vitess |
| `list <DATABASE_NAME>` | `--web` | List all branches of a database | Postgres, Vitess, Neki |
| `maintenance run <DATABASE_NAME> <BRANCH_NAME>` | `--update-postgres-minor-version` (Postgres only) | Run maintenance for a Postgres or Neki branch | Postgres, Neki |
| `parameters list <DATABASE_NAME> <BRANCH_NAME>` | `--namespace <NAMESPACE>`, `--extension`, `--internal` | List the configuration parameters of a Postgres branch | Postgres |
| `promote <DATABASE_NAME> <BRANCH_NAME>` |  | Promote a database branch to production | Vitess |
| `query-patterns list <DATABASE_NAME> <BRANCH_NAME>` | `--limit <NUMBER>`, `--starting-after <REPORT_ID>` | List query pattern reports for a branch. See the [`query-patterns` reference](query-patterns.md). | Postgres, Vitess |
| `query-patterns show <DATABASE_NAME> <BRANCH_NAME> <REPORT_ID>` |  | Show a query pattern report. See the [`query-patterns` reference](query-patterns.md). | Postgres, Vitess |
| `query-patterns delete <DATABASE_NAME> <BRANCH_NAME> <REPORT_ID>` | `--force` | Delete a query pattern report. See the [`query-patterns` reference](query-patterns.md). | Postgres, Vitess |
| `query-patterns download <DATABASE_NAME> <BRANCH_NAME>` | `--output <PATH>` | Generate and download a CSV report of branch query patterns. See the [`query-patterns` reference](query-patterns.md). | Postgres, Vitess |
| `refresh-schema <DATABASE_NAME> <BRANCH_NAME>` |  | Refresh the schema for a database branch | Vitess |
| `resize <DATABASE_NAME> <BRANCH_NAME>` | `--cluster-size <SKU>`, `--replicas <COUNT>`, `--parameters <NAMESPACE.NAME=VALUE>`, `--wait`, `--wait-timeout <DURATION>` | Change a Postgres branch’s cluster size, replica count, or configuration parameters | Postgres |
| `resize status <DATABASE_NAME> <BRANCH_NAME>` |  | Show the latest change request for a Postgres branch | Postgres |
| `resize cancel <DATABASE_NAME> <BRANCH_NAME>` |  | Cancel the queued change request for a Postgres branch | Postgres |
| `routing-rules get <DATABASE_NAME> <BRANCH_NAME>` |  | Show the keyspace routing rules of a branch | Vitess |
| `routing-rules update <DATABASE_NAME> <BRANCH_NAME>` | `--routing-rules <FILE>` \* | Replace the keyspace routing rules of a branch | Vitess |
| `safe-migrations enable <DATABASE_NAME> <BRANCH_NAME>` |  | Enables safe migrations for a database branch | Vitess |
| `safe-migrations disable <DATABASE_NAME> <BRANCH_NAME>` |  | Disables safe migrations for a database branch | Vitess |
| `router <COMMAND>` | `create`, `delete`, `list`, `show`, `update`, `sizes`, `changes` (`list`, `show`, `cancel`) | Manage router groups for a Neki branch | Neki |
| `schema <DATABASE_NAME> <BRANCH_NAME>` | `--web`, `--keyspace`, `--namespace` | Show the schema of a branch | Postgres, Vitess, Neki |
| `shard <COMMAND>` | `assign`, `create`, `delete`, `list`, `show`, `update` | Manage shards and their configuration-profile assignments | Neki |
| `show <DATABASE_NAME> <BRANCH_NAME>` | `--web` | Show a specific branch | Postgres, Vitess, Neki |
| `sidecar <COMMAND>` | `list`, `show`, `update`, `parameters`, `changes` (`list`, `show`, `cancel`) | Manage connection-pool sidecars for a Neki branch | Neki |
| `switch <BRANCH_NAME>` | `--database <DATABASE_NAME>` \*, `--create`, `--parent-branch <BRANCH_NAME>`, `--wait` | Switch to the specified branch | Postgres, Vitess, Neki |
| `switchover <DATABASE_NAME> <BRANCH_NAME>` | `--candidate <REPLICA_NAME>` | Move the primary of a Postgres branch to a replica. See [Switchovers](../postgres/troubleshooting/switchovers.md). | Postgres |
| `switchover list <DATABASE_NAME> <BRANCH_NAME>` | `--page <NUMBER>`, `--per-page <NUMBER>` | List switchovers for a Postgres branch | Postgres |
| `switchover show <DATABASE_NAME> <BRANCH_NAME> <ID>` |  | Show a Postgres switchover | Postgres |
| `update <DATABASE_NAME> <BRANCH_NAME>` | `--new-name <BRANCH_NAME>`, `--deletion-protected` | Rename a branch or change its deletion protection | Postgres, Vitess, Neki |
| `vtgate show <DATABASE_NAME> <BRANCH_NAME>` |  | Show the current VTGate configuration for a Vitess branch | Vitess |
| `vtgate resize <DATABASE_NAME> <BRANCH_NAME>` | `--vtgate-size <SKU>`, `--vtgate-count <COUNT>`, `--vtgate-max-count <COUNT>`, `--vtgate-autoscaling`, `--vtgate-target-cpu-utilization <PERCENT>` | Resize VTGates for a Vitess production branch | Vitess |
| `vtgate resize status <DATABASE_NAME> <BRANCH_NAME>` |  | Show the latest VTGate resize request for a Vitess branch | Vitess |
| `vtgate resize cancel <DATABASE_NAME> <BRANCH_NAME>` |  | Cancel a queued VTGate resize for a Vitess branch | Vitess |

### Service token automation: branch

Legend: ✅ supported · 🚫 unavailable · 👤 interactive login only. All sub-commands require `--org` or `PLANETSCALE_ORG`.

| Sub-command | Env-var auth | `--service-token` flag | Requires `--org` | Postgres / Vitess | `--format json` | API equivalent |
| --- | --- | --- | --- | --- | --- | --- |
| `list <database>` | ✅ | ✅ | ✅ | Both | ✅ | `pscale api organizations/<org>/databases/<database>/branches --format json` |
| `show <database> <branch>` | ✅ | ✅ | ✅ | Both | ✅ | `pscale api organizations/<org>/databases/<database>/branches/<branch> --format json` |
| `create` / `delete` | ✅ | ✅ | ✅ | Both | ✅ | `POST pscale api organizations/<org>/databases/<database>/branches --format json` · `DELETE pscale api organizations/<org>/databases/<database>/branches/<branch> --format json` |
| `schema <database> <branch>` | ✅ | ✅ | ✅ | Both | ✅ | `pscale api organizations/<org>/databases/<database>/branches/<branch>/schema --format json` |
| `diff` / `promote` | ✅ | ✅ | ✅ | Vitess | ✅ | `pscale api organizations/<org>/databases/<database>/branches/<branch>/schema/lint --format json` · `POST pscale api organizations/<org>/databases/<database>/branches/<branch>/promote --format json` |
| `connections` | ✅ | ✅ | ✅ | Both | ✅ | `pscale branch connections top <database> <branch> --org <org> --format json` |

```shellscript
export PLANETSCALE_ORG="<org>"
pscale branch list <database> --format json
pscale branch show <database> <branch> --format json
```

> \* *Flag is required*

#### Sub-command flag descriptions

Some of the sub-commands have additional flags unique to the sub-command. This section covers what each of those does. See the above table for which context.

| **Sub-command flag** | **Description** | **Applicable sub-commands** |
| --- | --- | --- |
| `--from <SOURCE_BRANCH>` | Parent branch that you want to create a new branch off of | `create` |
| `--region <BRANCH_REGION>` | Region where database should be created | `create` |
| `--restore <BACKUP_ID>` | Create a new branch from a backup ID. For Neki restore sizing, see `--config-profile` and `--router`. | `create` |
| `--restore-point <TIMESTAMP>` | For Postgres or Neki, restore to a point-in-time recovery timestamp (for example `2023-01-01T00:00:00Z`). Requires `--restore` or `--from`. Cannot be used with `--seed-data`. | `create` |
| `--seed-data` | Create a new branch and seed data using the [Data Branching® feature](../vitess/schema-changes/data-branching.md) | `create` |
| `--web` | Perform the action in your web browser | `diff`, `list`, `schema`, `show` |
| `--wait` | Wait until the branch is ready (`create`) or the change request completes (`resize`) | `create`, `resize` |
| `--major-version` | PostgreSQL major version for a Postgres or Neki branch. Defaults to the parent branch or the database default. Ignored when restoring from a backup. | `create` |
| `--min-storage <BYTES>` | Minimum storage size in bytes for a Postgres or Neki branch, or for shards in a Neki configuration profile. Not available for Vitess. | `create`, `config-profile create`, `config-profile update` |
| `--max-storage <BYTES>` | Maximum storage size in bytes for storage autoscaling on a Postgres or Neki branch, or for shards in a Neki configuration profile. Not available for Vitess. | `create`, `config-profile create`, `config-profile update` |
| `--storage-autoscaling` | Enable storage autoscaling for shards in a Neki configuration profile (`--storage-autoscaling=false` to disable). | `config-profile create`, `config-profile update` |
| `--storage-iops <NUMBER>` | Storage IOPS for shards in a Neki configuration profile | `config-profile create`, `config-profile update` |
| `--storage-throughput <NUMBER>` | Storage throughput in MiB/s for shards in a Neki configuration profile | `config-profile create`, `config-profile update` |
| `--update-postgres-minor-version` | Also upgrade a Postgres branch to the latest PostgreSQL minor version during the maintenance run. | `maintenance run` |
| `--database <DATABASE_NAME>` | Specify the database name | `switch` |
| `--create` | Create a new branch if it does not exist | `switch` |
| `--parent-branch <BRANCH_NAME>` | If a new branch is being created, use this to specify a parent branch. Default is `main`. | `switch` |
| `--delete-descendants` | Recursively delete all descendant branches when deleting a branch | `delete` |
| `--limit <NUMBER>` | Number of query pattern reports to return, up to 100. | `query-patterns list` |
| `--starting-after <REPORT_ID>` | Fetch the next page of query pattern reports after a report ID. | `query-patterns list` |
| `--output <PATH>` | Write the query patterns CSV report to a specific file path. | `query-patterns download` |
| `--candidate <REPLICA_NAME>` | The replica to promote during a switchover, as returned by `pscale branch infra`. Omit to select automatically. | `switchover` |
| `--page <NUMBER>` | Page of results to fetch. | `switchover list` |
| `--per-page <NUMBER>` | Number of results per page. Default is `100`. | `switchover list` |
| `--new-name <BRANCH_NAME>` | New name for the branch | `update` |
| `--deletion-protected` | Protect the branch from deletion (`--deletion-protected=false` to disable) | `update` |
| `--routing-rules <FILE>` | JSON file with the routing rules to set on the branch | `routing-rules update` |
| `--cluster-size <SKU>` | Cluster size for a new branch, a Postgres branch resize, or shards in a Neki configuration profile. For Neki `create`, omit this flag to use the database default. Use `pscale size cluster list` (`--engine neki` for Neki) or the [cluster sizing](../neki/cluster-configuration/cluster-sizing.md) menu. | `create`, `resize`, `config-profile create`, `config-profile update` |
| `--replicas <COUNT>` | Additional replica count for a Postgres branch resize, a Postgres or Neki backup restore, or shards in a Neki configuration profile. | `create`, `resize`, `config-profile create`, `config-profile update` |
| `--parameters <NAMESPACE.NAME=VALUE>` | Set a configuration parameter (e.g. `pgconf.max_connections=200`). Repeat the flag to set multiple parameters. Use `pscale branch parameters list` for Postgres, or `config-profile parameters`, `admin parameters`, or `sidecar parameters` for Neki. | `resize`, `config-profile update`, `admin update`, `router update`, `sidecar update` |
| `--wait-timeout <DURATION>` | Maximum time to wait for the change request to complete with `--wait`. Default is `10m`. | `resize` |
| `--namespace <NAMESPACE>` | Only show parameters in this namespace (e.g. `pgconf`, `pgbouncer`, `patroni`) | `parameters list` |
| `--extension` | Only show parameters that configure an extension (`--extension=false` hides them) | `parameters list` |
| `--internal` | Only show internal (immutable) parameters (`--internal=false` hides them) | `parameters list` |
| `--vtgate-size <SKU>` | VTGate size SKU (e.g. `VTG_320`, `VTG_1280`). Hyphens are accepted and converted to underscores. | `vtgate resize` |
| `--vtgate-count <COUNT>` | Number of VTGates per availability zone (minimum when autoscaling is enabled) | `vtgate resize` |
| `--vtgate-max-count <COUNT>` | Maximum VTGates per availability zone when autoscaling is enabled | `vtgate resize` |
| `--vtgate-autoscaling` | Enable or disable VTGate autoscaling (`--vtgate-autoscaling=false` to disable) | `vtgate resize` |
| `--vtgate-target-cpu-utilization <PERCENT>` | Target CPU utilization percent when autoscaling is enabled | `vtgate resize` |
| `--config-profile <PROFILE>` | Configuration profile that hosts new shards, or the profile to assign or filter by. On `create --restore`, repeat `name=<profile>[,cluster-size=<size>][,replicas=<n>]` to override Neki restore sizes. | `create`, `shard create`, `shard assign`, `shard list` |
| `--exclude-config-profile <PROFILE>` | Exclude shards assigned to this configuration profile | `shard list` |
| `--count <NUMBER>` | Number of shards to create. Defaults to `1`. | `shard create` |
| `--display-name <NAME>` | Shard display name. Pass an empty string to clear it. | `shard update` |
| `--query <QUERY>` | Search shards by name, display name, or configuration profile | `shard list` |
| `--size <SKU>` | Router size SKU (for example `NKR-5`) or Admin size (for example `NKA-0`). Hyphens are accepted and converted to underscores. Use `router sizes` or `admin sizes` to list valid SKUs. | `router create`, `router update`, `admin update` |
| `--replicas-per-cell <COUNT>` | Number of router replicas in each cell | `router create`, `router update` |
| `--autoscaling` | Enable horizontal router scaling within each cell | `router update` |
| `--max-replicas-per-cell <COUNT>` | Maximum router replicas in each cell when autoscaling | `router update` |
| `--target-cpu-utilization <PERCENT>` | Target average CPU utilization when router autoscaling is enabled | `router update` |
| `--name <NAME>` | New name for a configuration profile | `config-profile update` |
| `--postgres-major-version` | PostgreSQL major version for a configuration profile | `config-profile create`, `config-profile update` |
| `--postgres-minor-version` | PostgreSQL minor version. Requires `--postgres-major-version`. | `config-profile create`, `config-profile update` |
| `--router` | On `create --restore`, repeat `name=<router>[,size=<sku>][,replicas-per-cell=<n>]` to override Neki restore router sizes. | `create` |
| `--shards` | Group data-topology output by the physical shards that host the data | `data-topology ls` |
| `--period <PERIOD>` | Only show changes from this period | `config-profile changes list`, `admin changes list`, `router changes list`, `sidecar changes list` |
| `--completed-at <TIMESTAMP>` | Only show changes completed at this time | `config-profile changes list`, `admin changes list`, `router changes list`, `sidecar changes list` |

You can use `--region` with `--restore` to restore a backup into another region. `--restore-point` is available for Postgres and Neki. When you pass `--restore-point` without `--restore`, `--from` is required so the CLI can look up a backup that covers that timestamp.

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for branch command |
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

### The connections sub-command

**Command:**

```shellscript
pscale branch connections top <database> <branch>
```

Opens a live view of the branch’s connection activity. It works for Postgres and Vitess branches; for Vitess, pass `--keyspace` and `--shard` to target a tablet, or run interactively to select them. See the [`connections` reference](connections.md), [Inspect live Postgres connections](../postgres/monitoring/connections.md), and [Inspect live Vitess connections](../vitess/monitoring/connections.md) for the full workflow.

### Neki configuration commands

Neki infrastructure commands are nested under `pscale branch`. See [Neki cluster configuration](../neki/cluster-configuration.md) and [Neki data topology](../neki/data-topology.md) for the workflows behind these commands.

#### Configuration profiles

```shellscript
pscale branch config-profile list <DATABASE_NAME> <BRANCH_NAME>
pscale branch config-profile default <DATABASE_NAME> <BRANCH_NAME>
pscale branch config-profile create <DATABASE_NAME> <BRANCH_NAME> <PROFILE> \
  --cluster-size <SKU> --replicas 2 --postgres-major-version 18
pscale branch config-profile update <DATABASE_NAME> <BRANCH_NAME> <PROFILE> \
  --cluster-size <SKU> --parameters pgconf.max_connections=200
pscale branch config-profile set-default <DATABASE_NAME> <BRANCH_NAME> <PROFILE>
pscale branch config-profile parameters <DATABASE_NAME> <BRANCH_NAME> <PROFILE> --namespace pgconf
pscale branch config-profile extensions <DATABASE_NAME> <BRANCH_NAME> <PROFILE>
pscale branch config-profile extensions enable <DATABASE_NAME> <BRANCH_NAME> <PROFILE> <EXTENSION>
pscale branch config-profile extensions disable <DATABASE_NAME> <BRANCH_NAME> <PROFILE> <EXTENSION>
pscale branch config-profile maintenance <DATABASE_NAME> <BRANCH_NAME> <PROFILE>
pscale branch config-profile changes list <DATABASE_NAME> <BRANCH_NAME> <PROFILE>
pscale branch config-profile changes show <DATABASE_NAME> <BRANCH_NAME> <PROFILE> <CHANGE_ID>
pscale branch config-profile changes cancel <DATABASE_NAME> <BRANCH_NAME> <PROFILE> <CHANGE_ID>
pscale branch config-profile delete <DATABASE_NAME> <BRANCH_NAME> <PROFILE>
```

`create` and `update` accept `--cluster-size`, `--replicas`, `--postgres-major-version`, `--postgres-minor-version`, `--min-storage`, `--max-storage`, `--storage-autoscaling`, `--storage-iops`, and `--storage-throughput`. `--postgres-major-version` is required when you pass `--postgres-minor-version`. `update` also accepts `--name` and repeatable `--parameters namespace.name=value`. `parameters` accepts `--namespace`, `--extension`, and `--internal`. `extensions enable` and `extensions disable` toggle an extension that the catalog marks as enablable. `maintenance` upgrades one or more profiles to the latest image and returns immediately; use `pscale branch maintenance run` to maintain every profile on the branch. Change lists accept `--period`, `--completed-at`, `--page`, and `--per-page`.

#### Shards

```shellscript
pscale branch shard create <DATABASE_NAME> <BRANCH_NAME> --config-profile <PROFILE> --count 2
pscale branch shard list <DATABASE_NAME> <BRANCH_NAME> --config-profile <PROFILE>
pscale branch shard show <DATABASE_NAME> <BRANCH_NAME> <SHARD_ID>
pscale branch shard assign <DATABASE_NAME> <BRANCH_NAME> <SHARD_ID> --config-profile <PROFILE>
pscale branch shard update <DATABASE_NAME> <BRANCH_NAME> <SHARD_ID> --display-name "tenant-a"
pscale branch shard delete <DATABASE_NAME> <BRANCH_NAME> <SHARD_ID>
```

`--config-profile` is required on `create` and `assign`. `list` and `show` include whether the shard is the authoritative copy from the branch data topology. `list` accepts `--config-profile`, `--exclude-config-profile`, `--query`, `--page`, and `--per-page`. `update --display-name ""` clears a display name. `delete` accepts `--force`.

#### Routers

```shellscript
pscale branch router create <DATABASE_NAME> <BRANCH_NAME> <ROUTER> --size NKR-5 --replicas-per-cell 2
pscale branch router list <DATABASE_NAME> <BRANCH_NAME>
pscale branch router show <DATABASE_NAME> <BRANCH_NAME> <ROUTER>
pscale branch router sizes <DATABASE_NAME> <BRANCH_NAME>
pscale branch router update <DATABASE_NAME> <BRANCH_NAME> <ROUTER> \
  --autoscaling --max-replicas-per-cell 8 --target-cpu-utilization 50
pscale branch router changes list <DATABASE_NAME> <BRANCH_NAME> <ROUTER>
pscale branch router changes show <DATABASE_NAME> <BRANCH_NAME> <ROUTER> <CHANGE_ID>
pscale branch router changes cancel <DATABASE_NAME> <BRANCH_NAME> <ROUTER> <CHANGE_ID>
pscale branch router delete <DATABASE_NAME> <BRANCH_NAME> <ROUTER>
```

`create` accepts `--size` and `--replicas-per-cell`. `update` also accepts `--autoscaling`, `--max-replicas-per-cell`, `--target-cpu-utilization`, and repeatable `--parameters`. `sizes` lists valid router SKUs. Change lists accept `--period`, `--completed-at`, `--page`, and `--per-page`. `delete` accepts `--force`. Connect through a named router with [`pscale shell --router`](shell.md).

#### Admin and sidecars

Each Neki branch has one Admin. Each configuration profile has one sidecar. Both are created and deleted with the cluster or profile.

```shellscript
pscale branch admin show <DATABASE_NAME> <BRANCH_NAME>
pscale branch admin sizes <DATABASE_NAME> <BRANCH_NAME>
pscale branch admin parameters <DATABASE_NAME> <BRANCH_NAME>
pscale branch admin update <DATABASE_NAME> <BRANCH_NAME> --size NKA-0
pscale branch admin changes list <DATABASE_NAME> <BRANCH_NAME>
pscale branch sidecar list <DATABASE_NAME> <BRANCH_NAME>
pscale branch sidecar show <DATABASE_NAME> <BRANCH_NAME> <SIDECAR>
pscale branch sidecar update <DATABASE_NAME> <BRANCH_NAME> <SIDECAR> \
  --parameters pgbouncer.default_pool_size=50
pscale branch sidecar changes list <DATABASE_NAME> <BRANCH_NAME> <SIDECAR>
```

`<SIDECAR>` is the sidecar ID from `sidecar list`, or the configuration profile name. Admin `update` accepts `--size` and `--parameters`. `admin sizes` lists valid Admin SKUs. Sidecar `update` accepts `--parameters`. Change lists include a **Changes** column with the previous and requested size or parameter values, and accept `--period`, `--completed-at`, `--page`, and `--per-page`.

#### Data topology and maintenance

```shellscript
pscale branch data-topology get <DATABASE_NAME> <BRANCH_NAME>
pscale branch data-topology ls <DATABASE_NAME> <BRANCH_NAME>
pscale branch data-topology ls <DATABASE_NAME> <BRANCH_NAME> --shards --format json
pscale branch data-topology update <DATABASE_NAME> <BRANCH_NAME> < data-topology.json
pscale branch maintenance run <DATABASE_NAME> <BRANCH_NAME>
```

`data-topology update` reads a JSON object from standard input. `ls --shards` groups the topology by the physical shards that host the data. `maintenance run` updates every profile on the branch to the latest available image.

#### Restore a Neki backup

`pscale branch create --restore` creates a new branch from a backup, the same as [`pscale backup restore`](backup.md). For Neki, omit `--cluster-size` to keep the source default profile size. Override individual profiles and routers with repeatable `--config-profile` and `--router` flags.

```shellscript
pscale branch create <DATABASE_NAME> <NEW_BRANCH_NAME> --restore <BACKUP_ID>
pscale branch create <DATABASE_NAME> <NEW_BRANCH_NAME> --restore <BACKUP_ID> \
  --config-profile name=default,cluster-size=PS_40,replicas=2 \
  --router name=default,size=NKR-20,replicas-per-cell=1
```

Preview the live source sizes first with `pscale backup restore show <DATABASE_NAME> <SOURCE_BRANCH_NAME> <BACKUP_ID>`.

### The query-patterns sub-command

**Command:**

```shellscript
pscale branch query-patterns list <DATABASE_NAME> <BRANCH_NAME> --org <ORGANIZATION_NAME>
pscale branch query-patterns show <DATABASE_NAME> <BRANCH_NAME> <REPORT_ID> --org <ORGANIZATION_NAME>
pscale branch query-patterns delete <DATABASE_NAME> <BRANCH_NAME> <REPORT_ID> --org <ORGANIZATION_NAME>
pscale branch query-patterns download <DATABASE_NAME> <BRANCH_NAME> --org <ORGANIZATION_NAME>
```

List, inspect, and delete existing Query Insights reports for a branch. The `download` command creates a new report, waits for it to finish generating, and downloads the CSV file. See the [`query-patterns` reference](query-patterns.md) for the full workflow.

### The update sub-command

**Command:**

```shellscript
pscale branch update <DATABASE_NAME> <BRANCH_NAME> --new-name <NEW_BRANCH_NAME>
pscale branch update <DATABASE_NAME> <BRANCH_NAME> --deletion-protected
pscale branch update <DATABASE_NAME> <BRANCH_NAME> --deletion-protected=false
```

Renames a branch or changes its deletion protection. Only the flags you pass are sent, and at least one is required.

### The extensions sub-command

**Command:**

```shellscript
pscale branch extensions list <DATABASE_NAME> <BRANCH_NAME>
```

Lists the extensions available on a Postgres branch’s cluster image. This is the catalog the image can load, not the result of `CREATE EXTENSION`. Preload libraries are configured with `pscale branch resize --parameters`. See [Postgres extensions](../postgres/extensions.md).

### The switchover sub-command

**Command:**

```shellscript
pscale branch switchover <DATABASE_NAME> <BRANCH_NAME> --candidate <REPLICA_NAME>
```

Moves the primary of a Postgres branch to a replica. With `--candidate`, the named replica is promoted; without it, an eligible replica is selected automatically. On a branch without replicas, the single instance is restarted in place instead. See [Switchovers](../postgres/troubleshooting/switchovers.md) for the full workflow.

List past switchovers or show one by id. `switchover list` supports `--page` and `--per-page`.

```shellscript
pscale branch switchover list <DATABASE_NAME> <BRANCH_NAME>
pscale branch switchover show <DATABASE_NAME> <BRANCH_NAME> <ID>
```

### The list sub-command with --web flag

**Command:**

```shellscript
pscale branch list <DATABASE_NAME> --web
```

**Output:**

Opens the Branches page, `<https://app.planetscale.com/org/database/branches>`, in browser.

### The parameters sub-command

**Command:**

```shellscript
pscale branch parameters list <DATABASE_NAME> <BRANCH_NAME> --namespace pgconf
```

Lists the configuration parameters of a Postgres branch, including their current values, defaults, allowed ranges, and whether changing them requires a restart. Use `--namespace` to limit the output to a single namespace such as `pgconf`, `pgbouncer`, or `patroni`.

### The resize sub-command

**Command:**

```shellscript
pscale branch resize <DATABASE_NAME> <BRANCH_NAME> --parameters pgconf.max_connections=200 --parameters pgconf.work_mem=8MB --wait
```

Creates a change request for a Postgres branch. A single change request can update the cluster size (`--cluster-size`), the number of replicas (`--replicas`), and configuration parameters (`--parameters`, repeatable) together. With `--wait`, the command polls until the change request completes; without it, the command returns immediately and you can follow up with `resize status`.

Some parameters require a database restart to take effect; the command tells you which ones before the change is applied.

**Command:**

```shellscript
pscale branch resize status <DATABASE_NAME> <BRANCH_NAME>
```

Shows the latest change request for the branch, including its state (`queued`, `pending`, `resizing`, `completed`, or `canceled`) and what it changes.

**Command:**

```shellscript
pscale branch resize cancel <DATABASE_NAME> <BRANCH_NAME>
```

Cancels the queued change request for the branch. Only change requests that have not started being applied can be canceled.

### The vtgate sub-command

**Command:**

```shellscript
pscale branch vtgate show <DATABASE_NAME> <BRANCH_NAME>
```

Shows the current VTGate size, count, and autoscaling settings for a Vitess branch.

**Command:**

```shellscript
pscale branch vtgate resize <DATABASE_NAME> <BRANCH_NAME> \
  --vtgate-size VTG_320 \
  --vtgate-autoscaling \
  --vtgate-count 2 \
  --vtgate-max-count 8 \
  --vtgate-target-cpu-utilization 50
```

Queues a VTGate resize for a Vitess production branch. Pass at least one of `--vtgate-size`, `--vtgate-count`, `--vtgate-max-count`, `--vtgate-autoscaling`, or `--vtgate-target-cpu-utilization`. Development branches cannot be resized. See the [VTGate documentation](../vitess/scaling/vtgates.md) for size defaults, autoscaling limits, and pricing.

**Command:**

```shellscript
pscale branch vtgate resize status <DATABASE_NAME> <BRANCH_NAME>
```

Shows the latest VTGate resize request for the branch.

**Command:**

```shellscript
pscale branch vtgate resize cancel <DATABASE_NAME> <BRANCH_NAME>
```

Cancels a queued VTGate resize. Only resize requests that have not started being applied can be canceled.

### The diff sub-command

**Command:**

```shellscript
pscale branch diff <DATABASE_NAME> <BRANCH_NAME>
```

**Output:**

```sql
-- users --
+CREATE TABLE \`users\` (
+  \`id\` bigint unsigned NOT NULL AUTO_INCREMENT,
+  \`name\` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
+  PRIMARY KEY (\`id\`)
+) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

This will return the diff against the parent branch.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
