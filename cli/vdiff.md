---
url: https://planetscale.com/docs/cli/vdiff
title: "Vdiff"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The vdiff command

Run [Vitess VDiff](https://vitess.io/docs/reference/vreplication/vdiff/) against a VReplication workflow on a Vitess database branch. Use it to verify that source and target tables match before you switch traffic on a [MoveTables](move-tables.md) workflow.

JSON output for `create` and `show` includes a `next_steps` field with a suggested next command.

**Usage:**

```shellscript
pscale branch vtctl vdiff <SUB-COMMAND> <DATABASE_NAME> <BRANCH_NAME>
```

`pscale branch vtctld vdiff` also works.

You must be an [Organization Administrator](../security/access-control.md#organization-administrator) or a [Database Administrator](../security/access-control.md#database-administrator) of the database to create and change VDiffs. [Service tokens](../api/service-tokens.md) need `read_workflow` to list and show VDiffs, `write_workflow` to create, stop, or resume them, and `delete_workflow` to delete them.

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Description** |
| --- | --- | --- |
| `list <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \* | List VDiffs for a workflow |
| `create <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \*, `--auto-retry`, `--auto-start`, `--debug-query`, `--only-pks`, `--update-table-stats`, `--verbose`, `--tables <TABLES>`, `--tablet-types <TYPES>`, `--tablet-selection-preference <PREF>`, `--filtered-replication-wait-time <SECONDS>`, `--max-report-sample-rows <N>`, `--max-extra-rows-to-compare <N>`, `--row-diff-column-truncate-at <N>`, `--limit <N>` | Create a VDiff |
| `show <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--uuid <UUID>` \*, `--target-keyspace <KEYSPACE_NAME>` \* | Show a VDiff |
| `stop <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--uuid <UUID>` \*, `--target-keyspace <KEYSPACE_NAME>` \*, `--target-shards <SHARDS>` | Stop a running VDiff |
| `resume <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--uuid <UUID>` \*, `--target-keyspace <KEYSPACE_NAME>` \*, `--target-shards <SHARDS>` | Resume a stopped VDiff |
| `delete <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--uuid <UUID>` \*, `--target-keyspace <KEYSPACE_NAME>` \* | Delete a VDiff |

> \* *Flag is required*

#### Sub-command flag descriptions

| **Sub-command flag** | **Description** | **Applicable sub-commands** |
| --- | --- | --- |
| `--workflow <NAME>` | VReplication workflow name | all |
| `--target-keyspace <KEYSPACE_NAME>` | Target keyspace for the workflow | all |
| `--uuid <UUID>` | VDiff UUID from `create` or `list` | `show`, `stop`, `resume`, `delete` |
| `--auto-retry` | Automatically retry on error (default `true`) | `create` |
| `--auto-start` | Automatically start the VDiff (default `true`) | `create` |
| `--debug-query` | Log the queries used for the VDiff | `create` |
| `--only-pks` | Only compare primary keys | `create` |
| `--update-table-stats` | Update table statistics before the VDiff | `create` |
| `--verbose` | Verbose output | `create` |
| `--tables <TABLES>` | Tables to compare (comma-separated) | `create` |
| `--tablet-types <TYPES>` | Tablet types to use (comma-separated) | `create` |
| `--tablet-selection-preference <PREF>` | Tablet selection preference | `create` |
| `--filtered-replication-wait-time <SECONDS>` | Filtered replication wait time in seconds | `create` |
| `--max-report-sample-rows <N>` | Maximum sample rows in the report | `create` |
| `--max-extra-rows-to-compare <N>` | Maximum extra rows to compare | `create` |
| `--row-diff-column-truncate-at <N>` | Truncate column values at this length in the report | `create` |
| `--limit <N>` | Maximum number of rows to compare | `create` |
| `--target-shards <SHARDS>` | Target shards to stop or resume (comma-separated) | `stop`, `resume` |

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for `vdiff` |
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

1. Create a [MoveTables](move-tables.md) workflow and wait until streams are `Running`.
2. **Create** a VDiff and note the `uuid`.
3. **Show** until the VDiff completes with no mismatches.
4. Switch replica traffic, then primary traffic.
5. Optionally **stop**, **resume**, or **delete** a VDiff.

The **Workflows** page shows the latest VDiff for a MoveTables workflow. Creating a VDiff stays in the CLI or API.

## Examples

```shellscript
pscale branch vtctl vdiff create <DATABASE_NAME> <BRANCH_NAME> \
  --org <ORG> \
  --workflow <WORKFLOW_NAME> \
  --target-keyspace <TARGET_KEYSPACE> \
  --format json

pscale branch vtctl vdiff show <DATABASE_NAME> <BRANCH_NAME> \
  --org <ORG> \
  --workflow <WORKFLOW_NAME> \
  --target-keyspace <TARGET_KEYSPACE> \
  --uuid <VDIFF_UUID> \
  --format json

pscale branch vtctl vdiff list <DATABASE_NAME> <BRANCH_NAME> \
  --org <ORG> \
  --workflow <WORKFLOW_NAME> \
  --target-keyspace <TARGET_KEYSPACE> \
  --format json
```

## API

Matching API paths under `/v1/organizations/:org/databases/:db/branches/:branch/vdiff/workflows/:workflow/vdiffs`.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
