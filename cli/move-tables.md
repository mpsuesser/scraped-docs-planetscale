---
url: https://planetscale.com/docs/cli/move-tables
title: "Move Tables"
description: ""
access_date: 2026-09-16T19:45:27.654Z
current_date: 2026-09-16T19:45:27.654Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The move-tables command

Run [Vitess MoveTables](https://vitess.io/docs/user-guides/migration/move-tables/) workflows on a Vitess database branch: copy tables from one keyspace to another, check copy and traffic state, switch reads then writes, and complete or cancel the workflow.

JSON output includes a `next_steps` field with a suggested next command. Use it to see what to run after `list` or `status`.

**Usage:**

```shellscript
pscale branch vtctld move-tables <SUB-COMMAND> <DATABASE_NAME> <BRANCH_NAME>
```

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Description** |
| --- | --- | --- |
| `list <DATABASE_NAME> <BRANCH_NAME>` | `--target-keyspace <KEYSPACE_NAME>` | List MoveTables workflows on the branch |
| `create <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--source-keyspace <KEYSPACE_NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \*, `--tables <TABLE_NAMES>`, `--all-tables`, `--exclude-tables <TABLE_NAMES>`, `--auto-start`, `--stop-after-copy`, `--defer-secondary-keys`, `--on-ddl <ACTION>`, `--sharded-auto-increment-handling <ACTION>`, `--global-keyspace <KEYSPACE_NAME>`, `--source-time-zone <TIME_ZONE>`, `--tenant-id <ID>`, `--cells <CELLS>`, `--tablet-types <TYPES>`, `--atomic-copy` | Create a MoveTables workflow |
| `show <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \* | Show a workflow |
| `status <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \* | Show copy progress and traffic state |
| `switch-traffic <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \*, `--tablet-types <TYPES>` \*, `--dry-run`, `--initialize-target-sequences`, `--max-replication-lag-allowed <SECONDS>` | Switch traffic to the target keyspace |
| `reverse-traffic <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \*, `--tablet-types <TYPES>`, `--dry-run`, `--max-replication-lag-allowed <SECONDS>` | Switch traffic back to the source keyspace |
| `complete <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \*, `--keep-data` \*, `--keep-routing-rules` \*, `--rename-tables`, `--dry-run` | Finish the workflow and clean up |
| `cancel <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \*, `--keep-data` \*, `--keep-routing-rules` \* | Cancel a workflow that is still in progress |

> \* *Flag is required*

`list` also accepts the alias `ls`. Omit `--target-keyspace` on `list` to use the branch’s default keyspace.

#### Sub-command flag descriptions

| **Sub-command flag** | **Description** | **Applicable sub-commands** |
| --- | --- | --- |
| `--workflow <NAME>` | Workflow name | `create`, `show`, `status`, `switch-traffic`, `reverse-traffic`, `complete`, `cancel` |
| `--source-keyspace <KEYSPACE_NAME>` | Keyspace to copy tables from | `create` |
| `--target-keyspace <KEYSPACE_NAME>` | Keyspace to copy tables to. On `list`, defaults to the branch’s default keyspace | `list`, `create`, `show`, `status`, `switch-traffic`, `reverse-traffic`, `complete`, `cancel` |
| `--tables <TABLE_NAMES>` | Tables to move (comma-separated). Mutually exclusive with `--all-tables` and `--exclude-tables` | `create` |
| `--all-tables` | Move all tables from the source keyspace | `create` |
| `--exclude-tables <TABLE_NAMES>` | Tables to skip when moving (comma-separated) | `create` |
| `--auto-start` | Start the workflow after creation (default `true`) | `create` |
| `--stop-after-copy` | Stop the workflow after the copy phase | `create` |
| `--defer-secondary-keys` | Defer secondary indexes until copy finishes (default `true`) | `create` |
| `--on-ddl <ACTION>` | DDL handling: `IGNORE`, `STOP`, `EXEC`, `EXEC_IGNORE` | `create` |
| `--sharded-auto-increment-handling <ACTION>` | `AUTO_INCREMENT` handling for sharded targets: `LEAVE`, `REMOVE`, `REPLACE` | `create` |
| `--global-keyspace <KEYSPACE_NAME>` | Unsharded keyspace for sequence tables when `--sharded-auto-increment-handling` is `REPLACE` | `create` |
| `--source-time-zone <TIME_ZONE>` | Convert `DATETIME` values from this timezone to UTC | `create` |
| `--tenant-id <ID>` | Tenant ID for multi-tenant MoveTables | `create` |
| `--cells <CELLS>` | Cells to restrict the workflow to (comma-separated) | `create` |
| `--tablet-types <TYPES>` | Tablet types for the workflow or traffic switch (comma-separated). Use `REPLICA,RDONLY` for reads and `PRIMARY` for writes | `create`, `switch-traffic`, `reverse-traffic` |
| `--atomic-copy` | Use atomic copy | `create` |
| `--dry-run` | Show what would happen without applying it | `switch-traffic`, `reverse-traffic`, `complete` |
| `--initialize-target-sequences` | Initialize target sequences when switching traffic | `switch-traffic` |
| `--max-replication-lag-allowed <SECONDS>` | Maximum replication lag allowed before switching | `switch-traffic`, `reverse-traffic` |
| `--keep-data` | Keep copied data in the target (`true`) or remove it (`false`) | `complete`, `cancel` |
| `--keep-routing-rules` | Keep routing rules (`true`) or remove them (`false`) | `complete`, `cancel` |
| `--rename-tables` | Rename source tables instead of dropping them | `complete` |

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
2. **Verify** with `pscale branch vtctld vdiff create`, then `vdiff show` until it completes with no mismatches. You can skip VDiff and switch replica traffic directly.
3. **Switch replica traffic** with `--tablet-types REPLICA,RDONLY`.
4. **Switch primary traffic** with `--tablet-types PRIMARY`.
5. **Preview cleanup** with `complete --dry-run`, then complete after you review the result.

Use `status` between steps. `traffic_state` values are:

- `Reads Not Switched. Writes Not Switched`
- `All Reads Switched. Writes Not Switched`
- `Reads Not Switched. Writes Switched`
- `All Reads Switched. Writes Switched`

## Examples

List MoveTables workflows and get the next command to run:

```shellscript
pscale branch vtctld move-tables list mydb main --org acme --format json
```

Create a workflow:

```shellscript
pscale branch vtctld move-tables create mydb main \
  --org acme \
  --workflow commerce2customer \
  --source-keyspace commerce \
  --target-keyspace customer \
  --tables customers,orders \
  --format json
```

Check copy and traffic state:

```shellscript
pscale branch vtctld move-tables status mydb main \
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
pscale branch vtctld move-tables switch-traffic mydb main \
  --org acme \
  --workflow commerce2customer \
  --target-keyspace customer \
  --tablet-types REPLICA,RDONLY \
  --format json

pscale branch vtctld move-tables switch-traffic mydb main \
  --org acme \
  --workflow commerce2customer \
  --target-keyspace customer \
  --tablet-types PRIMARY \
  --format json
```

Verify with VDiff before switching traffic:

```shellscript
pscale branch vtctld vdiff create mydb main \
  --org acme \
  --workflow commerce2customer \
  --target-keyspace customer \
  --format json

pscale branch vtctld vdiff show mydb main \
  --org acme \
  --workflow commerce2customer \
  --target-keyspace customer \
  --uuid <VDIFF_UUID> \
  --format json
```

Preview cleanup, then complete:

```shellscript
pscale branch vtctld move-tables complete mydb main \
  --org acme \
  --workflow commerce2customer \
  --target-keyspace customer \
  --keep-data=false \
  --keep-routing-rules=false \
  --dry-run \
  --format json
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
