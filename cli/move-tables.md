---
url: https://planetscale.com/docs/cli/move-tables
title: "Move Tables"
description: ""
access_date: 2026-10-02T19:13:10.531Z
current_date: 2026-10-02T19:13:10.531Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The move-tables command

Run [Vitess MoveTables](https://vitess.io/docs/user-guides/migration/move-tables/) workflows on a Vitess database branch: copy tables from one keyspace to another, check copy and traffic state, switch reads then writes, and complete or cancel the workflow. Use it to [shard tables](../vitess/sharding/sharding-quickstart.md), [change the number of shards](../vitess/sharding/sharding-a-sharded-keyspace.md), and [import an external MySQL database](../vitess/imports/database-imports.md) from an external keyspace.

`move-tables` prints JSON. The output includes a `next_steps` field with a suggested next command. Use it to see what to run after `list` or `status`. Pass `--format json` when you script `move-tables`, so progress messages don’t mix with the JSON.

**Usage:**

```shellscript
pscale branch vtctl move-tables <SUB-COMMAND> <DATABASE_NAME> <BRANCH_NAME>
```

`pscale branch vtctld move-tables` also works.

You must be an [Organization Administrator](../security/access-control.md#organization-administrator) or a [Database Administrator](../security/access-control.md#database-administrator) of the database to create and change workflows. [Service tokens](../api/service-tokens.md) need `read_workflow` to list and view workflows, `write_workflow` to create, start, stop, switch traffic, and complete them, and `delete_workflow` to cancel them.

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Description** |
| --- | --- | --- |
| `list <DATABASE_NAME> <BRANCH_NAME>` | `--target-keyspace <KEYSPACE_NAME>` | List MoveTables workflows on the branch |
| `create <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--source-keyspace <KEYSPACE_NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \*, `--tables <TABLE_NAMES>` †, `--all-tables` †, `--exclude-tables <TABLE_NAMES>`, `--auto-start`, `--stop-after-copy`, `--defer-secondary-keys`, `--on-ddl <ACTION>`, `--sharded-auto-increment-handling <ACTION>`, `--global-keyspace <KEYSPACE_NAME>`, `--source-time-zone <TIME_ZONE>`, `--tenant-id <ID>`, `--cells <CELLS>`, `--tablet-types <TYPES>`, `--atomic-copy` | Create a MoveTables workflow |
| `show <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \* | Show a workflow |
| `status <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \* | Show copy progress and traffic state |
| `start <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \* | Start or resume a stopped workflow |
| `stop <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \* | Stop a running workflow |
| `switch-traffic <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \*, `--tablet-types <TYPES>` \*, `--dry-run`, `--initialize-target-sequences`, `--max-replication-lag-allowed <SECONDS>` | Switch traffic to the target keyspace |
| `reverse-traffic <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \*, `--tablet-types <TYPES>`, `--dry-run`, `--max-replication-lag-allowed <SECONDS>` | Switch traffic back to the source keyspace |
| `complete <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \*, `--keep-data` \*, `--keep-routing-rules` \*, `--rename-tables`, `--dry-run` | Finish the workflow and clean up |
| `cancel <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \*, `--keep-data` \*, `--keep-routing-rules` \* | Cancel a workflow that is still in progress |

> \* *Flag is required*
> 
> † *Pass either `--tables` or `--all-tables`*

`list` also accepts the alias `ls`.

#### Sub-command flag descriptions

| **Sub-command flag** | **Description** | **Applicable sub-commands** |
| --- | --- | --- |
| `--workflow <NAME>` | Workflow name. Letters, numbers, `_`, and `-` only | `create`, `show`, `status`, `start`, `stop`, `switch-traffic`, `reverse-traffic`, `complete`, `cancel` |
| `--source-keyspace <KEYSPACE_NAME>` | Keyspace to copy tables from. For an import, this is the [external keyspace](keyspace.md#create-an-external-keyspace) | `create` |
| `--target-keyspace <KEYSPACE_NAME>` | Keyspace to copy tables to. On `list`, filters to one keyspace. `list` shows workflows from every keyspace when you omit it | `list`, `create`, `show`, `status`, `start`, `stop`, `switch-traffic`, `reverse-traffic`, `complete`, `cancel` |
| `--tables <TABLE_NAMES>` | Tables to move (comma-separated). Cannot be combined with `--all-tables` or `--exclude-tables` | `create` |
| `--all-tables` | Move all tables from the source keyspace | `create` |
| `--exclude-tables <TABLE_NAMES>` | Tables to skip when moving all tables (comma-separated) | `create` |
| `--auto-start` | Start the workflow after creation (default `true`). Pass `--auto-start=false` and run `start` later to start it yourself | `create` |
| `--stop-after-copy` | Stop the workflow after the copy phase | `create` |
| `--defer-secondary-keys` | Create secondary indexes after the copy finishes instead of during it. This makes large copies much faster. Only applied when you pass the flag. Don’t use it with foreign key constraints | `create` |
| `--on-ddl <ACTION>` | What to do when a schema change runs on the source during the workflow: `STOP` (default), `IGNORE`, `EXEC`, `EXEC_IGNORE` | `create` |
| `--sharded-auto-increment-handling <ACTION>` | `AUTO_INCREMENT` handling for sharded targets: `REMOVE` (default) removes it, `REPLACE` replaces it with Vitess sequences, `LEAVE` keeps it | `create` |
| `--global-keyspace <KEYSPACE_NAME>` | Unsharded keyspace in which to create sequence tables when `--sharded-auto-increment-handling` is `REPLACE` | `create` |
| `--source-time-zone <TIME_ZONE>` | Convert `DATETIME` values from this timezone to UTC | `create` |
| `--tenant-id <ID>` | Tenant ID for multi-tenant MoveTables | `create` |
| `--cells <CELLS>` | Cells to restrict the workflow to (comma-separated) | `create` |
| `--tablet-types <TYPES>` | Tablet types for the workflow or traffic switch (comma-separated). Use `REPLICA,RDONLY` for reads and `PRIMARY` for writes | `create`, `switch-traffic`, `reverse-traffic` |
| `--atomic-copy` | Copy all tables in a single consistent copy phase. Use it when the source has foreign key constraints | `create` |
| `--dry-run` | Show what would happen without applying it | `switch-traffic`, `reverse-traffic`, `complete` |
| `--initialize-target-sequences` | Initialize target sequence tables when switching primary traffic, so new IDs start above the existing ones | `switch-traffic` |
| `--max-replication-lag-allowed <SECONDS>` | Maximum replication lag allowed before switching | `switch-traffic`, `reverse-traffic` |
| `--keep-data` | On `complete`: keep the source tables (`true`) or drop them (`false`). Must be `true` when the source is an external keyspace. On `cancel`: keep the data copied into the target keyspace (`true`) or delete it (`false`) | `complete`, `cancel` |
| `--keep-routing-rules` | Keep routing rules (`true`) or remove them (`false`) | `complete`, `cancel` |
| `--rename-tables` | Rename source tables instead of dropping them. Not supported when the source is an external keyspace | `complete` |

Write boolean flags with an `=`, such as `--keep-data=false`. A space-separated value such as `--keep-data false` is read as `--keep-data=true`.

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for `move-tables` |
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

## Lifecycle

A typical MoveTables run looks like this:

1. **Create** the workflow and wait until copy finishes and streams are `Running`.
2. **Verify** with `pscale branch vtctl vdiff create`, then `vdiff show` until it completes with no mismatches. You can skip VDiff and switch replica traffic directly.
3. **Switch replica traffic** with `--tablet-types REPLICA,RDONLY`.
4. **Switch primary traffic** with `--tablet-types PRIMARY`.
5. **Preview cleanup** with `complete --dry-run`, then complete after you review the result.

Use `status` between steps. Each stream’s state is `Copying`, `Running`, `Lagging`, `Stopped`, or `Error`. `traffic_state` values are:

- `Reads Not Switched. Writes Not Switched`
- `All Reads Switched. Writes Not Switched`
- `Reads Not Switched. Writes Switched`
- `All Reads Switched. Writes Switched`

Use `stop` to pause a workflow and `start` to resume it, for example after it stops on a schema change. Until you complete a workflow, you can switch traffic back with `reverse-traffic` or `cancel` it.

You can also follow each workflow on the **Workflows** page in the dashboard. The page is read-only.

## Examples

List MoveTables workflows and get the next command to run:

```shellscript
pscale branch vtctl move-tables list mydb main --org acme --format json
```

Create a workflow:

```shellscript
pscale branch vtctl move-tables create mydb main \
  --org acme \
  --workflow commerce2customer \
  --source-keyspace commerce \
  --target-keyspace customer \
  --tables customers,orders \
  --format json
```

Check copy and traffic state:

```shellscript
pscale branch vtctl move-tables status mydb main \
  --org acme \
  --workflow commerce2customer \
  --target-keyspace customer \
  --format json
```

Example `status` JSON after replica traffic has switched:

```json
{
  "traffic_state": "All Reads Switched. Writes Not Switched",
  "next_steps": [
    {
      "command": "pscale branch vtctld move-tables switch-traffic mydb main --org acme --workflow commerce2customer --target-keyspace customer --tablet-types PRIMARY --format json",
      "reason": "Switch primary traffic after validating replica traffic"
    }
  ]
}
```

Switch replica traffic, then primary traffic:

```shellscript
pscale branch vtctl move-tables switch-traffic mydb main \
  --org acme \
  --workflow commerce2customer \
  --target-keyspace customer \
  --tablet-types REPLICA,RDONLY \
  --format json

pscale branch vtctl move-tables switch-traffic mydb main \
  --org acme \
  --workflow commerce2customer \
  --target-keyspace customer \
  --tablet-types PRIMARY \
  --format json
```

Verify with VDiff before switching traffic:

```shellscript
pscale branch vtctl vdiff create mydb main \
  --org acme \
  --workflow commerce2customer \
  --target-keyspace customer \
  --format json

pscale branch vtctl vdiff show mydb main \
  --org acme \
  --workflow commerce2customer \
  --target-keyspace customer \
  --uuid <VDIFF_UUID> \
  --format json
```

Preview cleanup, then complete:

```shellscript
pscale branch vtctl move-tables complete mydb main \
  --org acme \
  --workflow commerce2customer \
  --target-keyspace customer \
  --keep-data=false \
  --keep-routing-rules=false \
  --dry-run \
  --format json
```

### Import from an external keyspace

After you [create an external keyspace](keyspace.md#create-an-external-keyspace), move its tables into your PlanetScale keyspace:

```shellscript
pscale branch vtctl move-tables create mydb main \
  --org acme \
  --workflow import_commerce \
  --source-keyspace commerce_source \
  --target-keyspace mydb \
  --all-tables \
  --defer-secondary-keys \
  --format json
```

When you complete an import, `--keep-data` must be `true`, so the tables in your source database are never dropped:

```shellscript
pscale branch vtctl move-tables complete mydb main \
  --org acme \
  --workflow import_commerce \
  --target-keyspace mydb \
  --keep-data=true \
  --keep-routing-rules=false \
  --format json
```

See [Database imports](../vitess/imports/database-imports.md) for the full import walkthrough.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
