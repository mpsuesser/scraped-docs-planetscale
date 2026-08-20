---
url: https://planetscale.com/docs/cli/branch
title: "Branch"
description: ""
access_date: 2026-08-20T18:00:05.296Z
current_date: 2026-08-20T18:00:05.296Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The branch command

This command allows you to create, delete, diff, and manage [branches](../vitess/schema-changes/branching.md).

**Usage:**

```shellscript
pscale branch <SUB-COMMAND> <FLAG>
```

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Description** | **Product** |
| --- | --- | --- | --- |
| `connections <COMMAND>` | `show`, `top`, `kill`, `kill-transaction` | Inspect and act on live branch connections. See the [`connections` reference](connections.md). | Postgres, Vitess |
| `create <DATABASE_NAME> <BRANCH_NAME>` | `--from <SOURCE_BRANCH>`, `--region <BRANCH_REGION>`, `--restore <BACKUP_NAME>`, `--seed-data`, `--wait` | Create a new branch on the specified database | Postgres, Vitess |
| `delete <DATABASE_NAME> <BRANCH_NAME>` | `--force`, `--delete-descendants` | Delete the specified branch from a database | Postgres, Vitess |
| `demote <DATABASE_NAME> <BRANCH_NAME>` |  | Demote a production branch to development | Vitess |
| `diff <DATABASE_NAME> <BRANCH_NAME>` | `--web` | Show the diff of the specified branch against the parent branch. | Vitess |
| `extensions list <DATABASE_NAME> <BRANCH_NAME>` |  | List the extensions available on a Postgres branch’s cluster image | Postgres |
| `lint <DATABASE_NAME> <BRANCH_NAME>` |  | Lint the schema of a branch | Vitess |
| `list <DATABASE_NAME>` | `--web` | List all branches of a database | Postgres, Vitess |
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
| `schema <DATABASE_NAME> <BRANCH_NAME>` | `--web` | Show the schema of a branch | Vitess |
| `show <DATABASE_NAME> <BRANCH_NAME>` | `--web` | Show a specific backup of a branch | Postgres, Vitess |
| `switch <BRANCH_NAME> --database <DATABASE_NAME>` | `--database <DATABASE_NAME>` \*, `--create`, `parent-branch <BRANCH_NAME>` | Switch to the specified branch | Postgres, Vitess |
| `switchover <DATABASE_NAME> <BRANCH_NAME>` | `--candidate <REPLICA_NAME>` | Move the primary of a Postgres branch to a replica. See [Switchovers](../postgres/troubleshooting/switchovers.md). | Postgres |
| `update <DATABASE_NAME> <BRANCH_NAME>` | `--new-name <BRANCH_NAME>`, `--deletion-protected` | Rename a branch or change its deletion protection | Postgres, Vitess |
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
| `--restore <BACKUP_NAME>` | Create a new branch from a specified backup | `create` |
| `--seed-data` | Create a new branch and seed data using the [Data Branching® feature](../vitess/schema-changes/data-branching.md) | `create` |
| `--web` | Perform the action in your web browser | `create`, `diff`, `list`, `schema`, `show` |
| `--wait` | Wait until the branch is ready (`create`) or the change request completes (`resize`) | `create`, `resize` |
| `--major-version` | The major version of the branch (Postgres only). Currently supports `17` or `18`. | `create` |
| `--database <DATABASE_NAME>` | Specify the database name | `switch` |
| `--create` | Create a new branch if it does not exist | `switch` |
| `--parent-branch <BRANCH_NAME>` | If a new branch is being created, use this to specify a parent branch. Default is `main`. | `switch` |
| `--delete-descendants` | Recursively delete all descendant branches when deleting a branch | `delete` |
| `--limit <NUMBER>` | Number of query pattern reports to return, up to 100. | `query-patterns list` |
| `--starting-after <REPORT_ID>` | Fetch the next page of query pattern reports after a report ID. | `query-patterns list` |
| `--output <PATH>` | Write the query patterns CSV report to a specific file path. | `query-patterns download` |
| `--candidate <REPLICA_NAME>` | The replica to promote during a switchover, as returned by `pscale branch infra`. Omit to select automatically. | `switchover` |
| `--new-name <BRANCH_NAME>` | New name for the branch | `update` |
| `--deletion-protected` | Protect the branch from deletion (`--deletion-protected=false` to disable) | `update` |
| `--routing-rules <FILE>` | JSON file with the routing rules to set on the branch | `routing-rules update` |
| `--cluster-size <SKU>` | New cluster size for the branch, as a fully-qualified SKU name (e.g. `PS_10_GCP_X86`). Use `pscale size cluster list --engine postgresql` to see the valid sizes. | `resize` |
| `--replicas <COUNT>` | Desired number of replicas for the branch | `resize` |
| `--parameters <NAMESPACE.NAME=VALUE>` | Set a configuration parameter (e.g. `pgconf.max_connections=200`). Repeat the flag to set multiple parameters. Use `pscale branch parameters list` to see available parameters. | `resize` |
| `--wait-timeout <DURATION>` | Maximum time to wait for the change request to complete with `--wait`. Default is `10m`. | `resize` |
| `--namespace <NAMESPACE>` | Only show parameters in this namespace (e.g. `pgconf`, `pgbouncer`, `patroni`) | `parameters list` |
| `--extension` | Only show parameters that configure an extension (`--extension=false` hides them) | `parameters list` |
| `--internal` | Only show internal (immutable) parameters (`--internal=false` hides them) | `parameters list` |
| `--vtgate-size <SKU>` | VTGate size SKU (e.g. `VTG_320`, `VTG_1280`). Hyphens are accepted and converted to underscores. | `vtgate resize` |
| `--vtgate-count <COUNT>` | Number of VTGates per availability zone (minimum when autoscaling is enabled) | `vtgate resize` |
| `--vtgate-max-count <COUNT>` | Maximum VTGates per availability zone when autoscaling is enabled | `vtgate resize` |
| `--vtgate-autoscaling` | Enable or disable VTGate autoscaling (`--vtgate-autoscaling=false` to disable) | `vtgate resize` |
| `--vtgate-target-cpu-utilization <PERCENT>` | Target CPU utilization percent when autoscaling is enabled | `vtgate resize` |

The `--region` flag can not be used with `--restore` when creating a branch. Branch backups will be restored to their original region.

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for auth command |
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
