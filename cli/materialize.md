---
url: https://planetscale.com/docs/cli/materialize
title: "Materialize"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The materialize command

Run [Vitess Materialize](https://vitess.io/docs/reference/vreplication/materialize/) workflows on a Vitess database branch. Materialize keeps target tables in sync in near-realtime. Targets can be copies, aggregations, or views of source tables.

Prefer [`move-tables`](move-tables.md) when you only need to copy tables between keyspaces. Prefer Materialize when the target needs a different schema or a `SELECT` that filters or aggregates rows.

**Usage:**

```shellscript
pscale branch vtctl materialize <SUB-COMMAND> <DATABASE_NAME> <BRANCH_NAME>
```

`pscale branch vtctld materialize` also works.

You must be an [Organization Administrator](../security/access-control.md#organization-administrator) or a [Database Administrator](../security/access-control.md#database-administrator) of the database to create and change Materialize workflows. [Service tokens](../api/service-tokens.md) need `read_workflow` to show a workflow, `write_workflow` to create, start, or stop one, and `delete_workflow` to cancel one.

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Description** |
| --- | --- | --- |
| `create <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--source-keyspace <KEYSPACE_NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \*, `--table-settings <JSON>` \*, `--cells <CELLS>`, `--reference-tables <TABLES>`, `--tablet-types <TYPES>`, `--stop-after-copy`, `--tablet-types-in-preference-order`, `--defer-secondary-keys`, `--atomic-copy`, `--on-ddl <ACTION>`, `--source-time-zone <TIME_ZONE>` | Create a Materialize workflow |
| `show <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \*, `--include-logs` | Show a workflow |
| `start <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \* | Start or resume a stopped workflow |
| `stop <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \* | Stop a running workflow |
| `cancel <DATABASE_NAME> <BRANCH_NAME>` | `--workflow <NAME>` \*, `--target-keyspace <KEYSPACE_NAME>` \*, `--keep-data`, `--keep-routing-rules` | Cancel a workflow |

> \* *Flag is required*

#### Sub-command flag descriptions

| **Sub-command flag** | **Description** | **Applicable sub-commands** |
| --- | --- | --- |
| `--workflow <NAME>` | Workflow name | all |
| `--source-keyspace <KEYSPACE_NAME>` | Keyspace the materialization queries read from | `create` |
| `--target-keyspace <KEYSPACE_NAME>` | Keyspace that holds the materialization targets | all |
| `--table-settings <JSON>` | JSON array of materialization definitions (see below) | `create` |
| `--cells <CELLS>` | Cells to source from (comma-separated) | `create` |
| `--reference-tables <TABLES>` | Reference tables to copy to each shard (comma-separated) | `create` |
| `--tablet-types <TYPES>` | Tablet types to source from (comma-separated) | `create` |
| `--stop-after-copy` | Stop the workflow after the copy phase | `create` |
| `--tablet-types-in-preference-order` | Preserve tablet preference order | `create` |
| `--defer-secondary-keys` | Defer secondary index creation during copy | `create` |
| `--atomic-copy` | Use atomic copy when the source has foreign key constraints | `create` |
| `--on-ddl <ACTION>` | DDL handling: `IGNORE`, `STOP`, `EXEC`, `EXEC_IGNORE` | `create` |
| `--source-time-zone <TIME_ZONE>` | Convert `DATETIME` values from this timezone to UTC | `create` |
| `--include-logs` | Include workflow logs (default `true`) | `show` |
| `--keep-data` | Keep data copied into the target after cancel | `cancel` |
| `--keep-routing-rules` | Keep routing rules after cancel | `cancel` |

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for `materialize` |
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

## \--table-settings

`--table-settings` is a JSON array. Each object needs:

- `target_table` — table name in the target keyspace
- `source_expression` — a `SELECT` against a single source table

Optional `create_ddl`:

- `"copy"` — copy the source table schema as-is
- a `CREATE TABLE` statement — create a custom target schema

Vitess limits which `SELECT` s Materialize accepts (no `JOIN` s, no `OR` / `LIKE`, expressions need aliases, and more). See the [Vitess Materialize docs](https://vitess.io/docs/reference/vreplication/materialize/).

Example:

```json
[
  {
    "target_table": "sales_by_sku",
    "source_expression": "select sku, count(*) as orders, sum(price) as revenue from corder group by sku",
    "create_ddl": "create table sales_by_sku (sku varchar(128) not null primary key, orders bigint, revenue bigint)"
  },
  {
    "target_table": "states",
    "source_expression": "select * from states",
    "create_ddl": "copy"
  }
]
```

## Lifecycle

1. **Create** the workflow with `--table-settings`.
2. **Show** the workflow until streams are replicating.
3. Read from the target tables once they are in sync.
4. **Stop** / **start** to pause and resume, or **cancel** to tear the workflow down.

## Examples

```shellscript
pscale branch vtctl materialize create <DATABASE_NAME> <BRANCH_NAME> \
  --org <ORG> \
  --workflow <WORKFLOW_NAME> \
  --source-keyspace <SOURCE_KEYSPACE> \
  --target-keyspace <TARGET_KEYSPACE> \
  --table-settings '[{"target_table":"sales_by_sku","create_ddl":"create table sales_by_sku (sku varchar(128) not null primary key, orders bigint, revenue bigint)","source_expression":"select sku, count(*) as orders, sum(price) as revenue from corder group by sku"}]' \
  --format json

pscale branch vtctl materialize show <DATABASE_NAME> <BRANCH_NAME> \
  --org <ORG> \
  --workflow <WORKFLOW_NAME> \
  --target-keyspace <TARGET_KEYSPACE> \
  --format json
```

## Dashboard and API

The **Workflows** page lists Materialize workflows next to MoveTables workflows and shows stream state, lag, and each target table’s query. It is read-only — create, start, stop, and cancel stay in the CLI or API. See [Workflows](../vitess/scaling/workflows.md).

Matching API paths under `/v1/organizations/:org/databases/:db/branches/:branch/materialize/workflows`.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
