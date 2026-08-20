---
url: https://planetscale.com/docs/cli/deploy-request
title: "Deploy Request"
description: ""
access_date: 2026-08-20T18:00:05.296Z
current_date: 2026-08-20T18:00:05.296Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The deploy-request command

This command allows you to create, review, diff, and manage deploy requests for your Vitess clusters. This command is not currently available for Postgres database clusters.

**Usage:**

```shellscript
pscale deploy-request <SUB-COMMAND> <FLAG>
```

Your database must have a production branch with [safe migrations](../vitess/schema-changes/safe-migrations.md) enabled before you can create a deploy request.

### Available sub-commands

| Sub-command | Sub-command flags | Description | **Product** |
| --- | --- | --- | --- |
| `apply <DATABASE_NAME> <DR_NUMBER>` |  | Trigger a deploy request to swap over to the new schema. | Vitess |
| `cancel <DATABASE_NAME> <DR_NUMBER>` |  | Cancel a deploy request. | Vitess |
| `close <DATABASE_NAME> <DR_NUMBER>` |  | Close the specified deploy request. | Vitess |
| `create <DATABASE_NAME> <BRANCH_NAME>` | `--into <BRANCH_NAME>`, `--notes <NOTE>`, `--enable-auto-apply`, `--disable-auto-apply` | Create a new deploy request. | Vitess |
| `deploy <DATABASE_NAME> <DR_NUMBER\|BRANCH_NAME>` | `--instant`, `--strategy <serial\|parallel>` | Deploy the specified deploy request. | Vitess |
| `deployment <DATABASE_NAME> <DR_NUMBER>` |  | Show the deployment for a deploy request. | Vitess |
| `diff <DATABASE_NAME> <DR_NUMBER>` | `--web` | Show the diff of the specified deploy request. | Vitess |
| `edit <DATABASE_NAME> <DR_NUMBER>` | `--enable-auto-apply`, `--disable-auto-apply`, `--auto-delete-branch` | Alias for `update`. | Vitess |
| `force-cutover <DATABASE_NAME> <DR_NUMBER>` | `--force` | Force cutover when a migration is delayed by a table lock. | Vitess |
| `list <DATABASE_NAME>` | `--web` | List all deploy requests for a database. | Vitess |
| `operations <DATABASE_NAME> <DR_NUMBER>` |  | List deploy operations for a deploy request. | Vitess |
| `queue <DATABASE_NAME>` |  | Show the deploy queue for a database. | Vitess |
| `revert <DATABASE_NAME> <DR_NUMBER>` |  | Revert a deployed deploy request. | Vitess |
| `review <DATABASE_NAME> <DR_NUMBER>` | `--web`, `--approve`, `--comment <COMMENT>` | Approve or comment on a deploy request. | Vitess |
| `reviews <DATABASE_NAME> <DR_NUMBER>` |  | List reviews for a deploy request. | Vitess |
| `show <DATABASE_NAME> <DR_NUMBER\|BRANCH_NAME>` | `--web` | Show the specified deploy request. | Vitess |
| `skip-revert <DATABASE_NAME> <DR_NUMBER>` |  | Skip and close a pending deploy request revert. | Vitess |
| `storage-check <DATABASE_NAME> <DR_NUMBER>` |  | Check storage readiness for a deploy request. | Vitess |
| `throttler show <DATABASE_NAME> <DR_NUMBER>` |  | Show throttler configuration for a deploy request. | Vitess |
| `throttler update <DATABASE_NAME> <DR_NUMBER>` | `--ratio <RATIO>`, `--configuration <KEYSPACE=RATIO>` | Update throttler configuration for a deploy request. | Vitess |
| `unblock <DATABASE_NAME> <DR_NUMBER>` |  | Unblock the deploy queue after a failed deploy or revert. | Vitess |
| `update <DATABASE_NAME> <DR_NUMBER>` | `--enable-auto-apply`, `--disable-auto-apply`, `--auto-delete-branch` | Update settings on a deploy request. | Vitess |

> \* *Flag is required*

The value `<DR_NUMBER>` represents the deploy request number (not to be confused with `id`). To see a deploy request number, run `pscale deploy-request list <DATABASE_NAME>`.

You can also find the number in the PlanetScale dashboard in the URL of the specified deploy request: `https://app.planetscale.com/<ORGANIZATION>/<DATABASE>/deploy-requests/<DR_NUMBER>`.

#### Sub-command flag descriptions

Some of the sub-commands have additional flags unique to the sub-command. This section covers what each of those does. See the above table for which context.

| Sub-command flag | Description | Applicable sub-commands |
| --- | --- | --- |
| `--into <BRANCH_NAME>` | Specify that the new deploy request deploy to a specified branch. Default is `main`. | `create` |
| `--notes <NOTE>` | A note describing the deploy request. Acts as the first comment. | `create` |
| `--enable-auto-apply` | Enable auto-apply for this deploy request. When enabled, the deploy request will swap over to the new schema once ready. | `create`, `update` |
| `--disable-auto-apply` | Disable auto-apply for this deploy request. If neither flag is provided, the setting is inherited from the previous deploy request. | `create`, `update` |
| `--auto-delete-branch` | Delete the source branch after the deploy request completes (`--auto-delete-branch=false` to keep it). | `create`, `update` |
| `--web` | Perform the action in your web browser | `diff`, `list`, `show` |
| `--approve` | Approve a deploy request | `review` |
| `--comment <COMMENT>` | Leave a comment on a deploy request | `review` |
| `--instant` | Deploy a deploy request using MySQL’s built-in ALGORITHM=INSTANT option. Deployment will be faster, but cannot be reverted. | `deploy` |
| `--strategy <serial\|parallel>` | Deployment strategy. `serial` (default) joins the deploy queue. `parallel` runs alongside the queue. See [Parallel deployments](../vitess/schema-changes/deploy-requests.md#parallel-deployments). | `deploy` |
| `--force` | Skip the confirmation prompt and force cutover now. | `force-cutover` |
| `--ratio <RATIO>` | Throttler ratio between 0 and 95 applied to all eligible keyspaces. 0 disables throttling; 95 slows migrations the most. | `throttler update` |
| `--configuration <KEYSPACE=RATIO>` | Per-keyspace throttler ratio as `keyspace=ratio` (repeatable). Use instead of `--ratio`, not together. | `throttler update` |

### Available flags

| Flag | Description |
| --- | --- |
| `-h`, `--help` | Get help with the `deploy-request` command |
| `--org <ORGANIZATION_NAME>` | Specify the organization for the deploy request you’re acting upon |

### Global flags

| Command | Description |
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

### The deploy-request command with review subcommand and --comment flag

**Command:**

```shellscript
pscale deploy-request review <DATABASE_NAME> 1 --comment 'Lets wait on this.'
```

**Output:**

A comment is added to the deploy request `<DATABASE_NAME>` /1.

### The deploy-request command with force-cutover subcommand

The final step of a migration requires a brief table lock. Long-running transactions can block that lock and delay completion. PlanetScale retries for up to 1 hour, then forces cutover automatically. Use this command to skip the wait. It kills long-running transactions that are blocking the table lock so the migration can finish.

Only allowed when the deployment state is `in_progress_cutover`. See [Aggressive cutover](../vitess/schema-changes/aggressive-cutover.md).

**Command:**

```shellscript
pscale deploy-request force-cutover <DATABASE_NAME> <DR_NUMBER>
```

**Output:**

Successfully requested force cutover for deploy request `<DATABASE_NAME>` / `<DR_NUMBER>`. Vitess will attempt again momentarily.

### The deploy-request command with throttler update subcommand

Adjust the migration throttler for a single deploy request. This is per deploy request, not the [database-level throttler](database.md#database-level-vitess-migration-throttler). Use `--ratio` for one ratio across all eligible keyspaces, or `--configuration` for per-keyspace ratios.

**Command:**

```shellscript
pscale deploy-request throttler update <DATABASE_NAME> <DR_NUMBER> --ratio 25
```

**Output:**

The throttler configuration for deploy request `<DATABASE_NAME>` / `<DR_NUMBER>` is updated.

### The deploy-request command with unblock subcommand

When a deployment or revert errors, PlanetScale blocks the deploy queue as a precaution. This is the same action as **Unblock deploy queue** in the dashboard. It does not apply a gated deploy, which is [`deploy-request apply`](deploy-request.md), and it does not fix a schema that failed deploy checks.

**Command:**

```shellscript
pscale deploy-request unblock <DATABASE_NAME> <DR_NUMBER>
```

### The deploy-request command with update subcommand

Changes settings on an existing deploy request. At least one flag is required, and flags you leave off are not sent. `edit` is an alias for this command.

**Command:**

```shellscript
pscale deploy-request update <DATABASE_NAME> <DR_NUMBER> --enable-auto-apply
pscale deploy-request update <DATABASE_NAME> <DR_NUMBER> --auto-delete-branch
pscale deploy-request update <DATABASE_NAME> <DR_NUMBER> --auto-delete-branch=false
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
