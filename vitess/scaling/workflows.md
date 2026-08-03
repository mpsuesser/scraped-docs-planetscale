---
url: https://planetscale.com/docs/vitess/scaling/workflows
title: "Workflows"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

PlanetScale workflows are built on top of [Vitess VReplication](https://vitess.io/docs/reference/vreplication/vreplication/) and provide a managed way to move data between database sources with real-time progress tracking, data verification, and controlled traffic switching.

There are two types of workflows available:

## Table movement

Move tables between keyspaces within your PlanetScale database. Used for sharding, resharding, and reorganizing data.

## Database import

Import data from an external MySQL or MariaDB database into PlanetScale with no downtime.

## Table movement workflows

Table movement workflows let you move tables from one [keyspace](../sharding/keyspaces.md) to another within your PlanetScale database. If you are familiar with [Vitess](https://vitess.io/), these are analogous to the [Vitess `MoveTables` workflow](https://vitess.io/docs/user-guides/migration/move-tables/).

Available table movement workflows:

- **Unsharded to sharded** — Move tables from an unsharded keyspace to a new sharded keyspace. This is the primary way to shard existing tables. See the [Sharding quickstart](../sharding/sharding-quickstart.md).
- **Sharded to sharded** — Move tables between sharded keyspaces to change the number of shards. See [Modifying the number of shards](../sharding/sharding-a-sharded-keyspace.md).
- **Unsharded to unsharded** — Move tables from one unsharded keyspace to another unsharded keyspace.

Table movement workflows can be created from the dashboard, the [CLI](../../cli/workflow.md), or the [API](../../api/reference/create_workflow.md).

## Database import workflows

Database import workflows let you migrate data from an **external internet-accessible MySQL or MariaDB database** into PlanetScale with no downtime. The import workflow handles connection setup, schema validation, data copying, real-time replication, and controlled traffic switching.

Database import workflows are created through the PlanetScale dashboard. To get started, go to **New database** > **Import database**.

For a full walkthrough, see the [Database imports documentation](../imports/database-imports.md).

## Shared workflow lifecycle

Both workflow types follow a similar lifecycle:

1. **Copying** — Initial data copy from source to target
2. **Running** — Real-time replication keeps source and target in sync
3. **Switching traffic** — Controlled cutover of replica and primary traffic to the target
4. **Complete** — Workflow finalized and source connection closed

For the full list of states specific to table movement workflows, see the [Workflow state reference](../sharding/sharding-workflow-state-reference.md). For import workflow states, see [Workflow phases](../imports/database-imports.md#workflow-phases).

## Create a workflow

To create a new workflow, select your database and click **Workflows** in the left nav, then click **New workflow**. You’ll be prompted to choose between moving tables between keyspaces or importing from an external database.

You can also create table movement workflows using the [`pscale workflow create`](../../cli/workflow.md) CLI command or the [Create workflow API endpoint](../../api/reference/create_workflow.md).

Database import workflows can only be created through the PlanetScale dashboard. The CLI and API `create` commands are for table movement workflows only. Once created, all other workflow lifecycle commands (`switch-traffic`, `verify-data`, `complete`, `cancel`, etc.) work with both workflow types.

## View workflow history

To view the history of all completed or pending workflows, click on **Workflows** in the left nav. From here, you can see all previous workflows along with information such as status, duration, and the time it took to complete.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
