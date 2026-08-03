---
url: https://planetscale.com/docs/cli/workflow
title: "Workflow"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The workflow command

This command allows you to create and manage [workflows](../vitess/scaling/workflows.md) for Vitess databases, including creating workflows, listing them, and performing actions like traffic switching and cutover. This command is not supported for Postgres databases.

The `create` sub-command is for [table movement workflows](../vitess/scaling/workflows.md#table-movement-workflows) only (moving tables between keyspaces). [Database import workflows](../vitess/imports/database-imports.md) must be created through the PlanetScale dashboard. Once created, all other sub-commands (`list`, `show`, `switch-traffic`, `verify-data`, `complete`, `cancel`, etc.) work with both workflow types.

**Usage:**

```shellscript
pscale workflow <SUB-COMMAND> <FLAG>
```

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Product** | **Description** |
| --- | --- | --- | --- |
| `cancel <DATABASE_NAME> <WORKFLOW_NUMBER>` | `--force` | Vitess | Cancel a workflow that is in progress |
| `complete <DATABASE_NAME> <WORKFLOW_NUMBER>` |  | Vitess | Mark a workflow as complete |
| `create <DATABASE_NAME> <BRANCH_NAME>` | `--defer-secondary-keys`, `--global-keyspace <KEYSPACE_NAME>`, `-i`, `--interactive`, `--name <WORKFLOW_NAME>`, `--on-ddl <ACTION>`, `--source-keyspace <KEYSPACE_NAME>`, `--tables <TABLE_NAMES>`, `--target-keyspace <KEYSPACE_NAME>` | Vitess | Create a new workflow within a branch |
| `cutover <DATABASE_NAME> <WORKFLOW_NUMBER>` | `--force` | Vitess | Completes the workflow, cutting over all traffic to the target keyspace |
| `list <DATABASE_NAME>` |  | Vitess | List all of the workflows for a PlanetScale database |
| `retry <DATABASE_NAME> <WORKFLOW_NUMBER>` |  | Vitess | Retry a workflow that has been stopped or failed |
| `reverse-cutover <DATABASE_NAME> <WORKFLOW_NUMBER>` |  | Vitess | Reverse the cutover of a workflow back to the source keyspace |
| `reverse-traffic <DATABASE_NAME> <WORKFLOW_NUMBER>` |  | Vitess | Route queries back to the source keyspace for a specific workflow |
| `show <DATABASE_NAME> <WORKFLOW_NUMBER>` |  | Vitess | Show a specific workflow for a PlanetScale database |
| `switch-traffic <DATABASE_NAME> <WORKFLOW_NUMBER>` | `--force`, `--replicas-only` | Vitess | Route queries to the target keyspace for a specific workflow |
| `verify-data <DATABASE_NAME> <WORKFLOW_NUMBER>` |  | Vitess | Verify data consistency for a specific workflow |

#### Sub-command flag descriptions

Some of the sub-commands have additional flags unique to the sub-command. This section covers what each of those does. See the above table for which context.

| **Sub-command flag** | **Type** | **Description** | **Applicable sub-commands** |
| --- | --- | --- | --- |
| `--defer-secondary-keys` | boolean | Don’t create secondary indexes for tables until they’ve been copied (default true) | `create` |
| `--force` | boolean | Force the operation without prompting for confirmation | `cancel`, `cutover`, `switch-traffic` |
| `--global-keyspace <KEYSPACE_NAME>` | string | Choose an unsharded keyspace where sequence tables will be created for any workflow table that contains AUTO\_INCREMENT | `create` |
| `-i, --interactive` | boolean | Create the workflow in interactive mode | `create` |
| `--name <WORKFLOW_NAME>` | string | Name of the workflow | `create` |
| `--on-ddl <ACTION>` | string | Action to take when a DDL statement is encountered during a running workflow. Options: EXEC, EXEC\_IGNORE, STOP, IGNORE (default “STOP”) | `create` |
| `--replicas-only` | boolean | Route read queries from the replica and read-only tablets to the target keyspace | `switch-traffic` |
| `--source-keyspace <KEYSPACE_NAME>` | string | Keyspace where the tables will be copied from | `create` |
| `--tables <TABLE_NAMES>` | strings | Tables to migrate to the target keyspace | `create` |
| `--target-keyspace <KEYSPACE_NAME>` | string | Keyspace where the tables will be copied to | `create` |

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for workflow command |
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

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
