---
url: https://planetscale.com/docs/vitess/scaling/workflows
title: "Workflows"
description: ""
access_date: 2026-10-02T19:13:10.531Z
current_date: 2026-10-02T19:13:10.531Z
---

Workflows are [Vitess MoveTables](https://vitess.io/docs/user-guides/migration/move-tables/) workflows built on [Vitess VReplication](https://vitess.io/docs/reference/vreplication/vreplication/). A workflow copies tables from a source keyspace to a target keyspace, keeps them in sync with real-time replication, and lets you verify the data and switch traffic when you’re ready.

You create and drive workflows with [`pscale branch vtctl move-tables`](../../cli/move-tables.md) or the MoveTables API. The dashboard shows the live state of each workflow, read directly from Vitess.

There are two ways to use workflows:

## Move tables between keyspaces

Move tables between keyspaces within your PlanetScale database. Used for sharding, resharding, and reorganizing data.

## Import a database

Import data from an external MySQL database into PlanetScale with no downtime.

## Move tables between keyspaces

Move tables from one [keyspace](../sharding/keyspaces.md) to another within your PlanetScale database:

- **Unsharded to sharded**: Move tables from an unsharded keyspace to a new sharded keyspace. This is the primary way to shard existing tables. See the [Sharding quickstart](../sharding/sharding-quickstart.md).
- **Sharded to sharded**: Move tables between sharded keyspaces to change the number of shards. See [Modifying the number of shards](../sharding/sharding-a-sharded-keyspace.md).
- **Unsharded to unsharded**: Move tables from one unsharded keyspace to another unsharded keyspace.

## Import a database

To import an **external internet-accessible MySQL database**, you first connect it to your production branch as an [external keyspace](../cluster-configuration.md#create-an-external-keyspace). Then you run a workflow with the external keyspace as the source and your PlanetScale keyspace as the target. The import copies your data, replicates new changes in real time, and switches traffic to PlanetScale with no downtime.

For a full walkthrough, see the [Database imports documentation](../imports/database-imports.md).

## Workflow lifecycle

Every workflow follows the same lifecycle:

1. **Create**: `move-tables create` copies the table schemas to the target keyspace and starts copying rows.
2. **Copy**: Vitess copies the existing rows from the source to the target. Streams are `Copying`.
3. **Replicate**: Once the copy finishes, Vitess keeps the target up to date with every new write on the source. Streams are `Running`.
4. **Verify**: Optionally compare the source and target with a [VDiff](https://vitess.io/docs/reference/vreplication/vdiff/).
5. **Switch traffic**: `move-tables switch-traffic` moves replica traffic, then primary traffic, to the target keyspace. `move-tables reverse-traffic` moves it back.
6. **Complete**: `move-tables complete` stops replication. Its required flags decide whether the moved tables are dropped from the source keyspace and whether the routing rules are removed. You can also `cancel` a workflow at any point before it is complete.

Use `move-tables status` between steps. It shows each table’s copy progress, each stream’s state, and the traffic state. JSON output includes a `next_steps` field with the command to run next. See the [`move-tables` reference](../../cli/move-tables.md) for every command and flag.

## View workflows in the dashboard

Select your database and click **Workflows** in the left nav. Pick a branch to see the MoveTables workflows on it, across all keyspaces or filtered to one keyspace. Open a workflow to see:

- The source and target keyspaces, replication lag, and when the workflow last updated
- Which keyspace is serving reads and writes
- Each stream’s state, rows copied, and any error message
- Each table’s copy progress
- The results of the latest VDiff

The Workflows page is read-only. To create, switch, complete, or cancel a workflow, use [`pscale branch vtctl move-tables`](../../cli/move-tables.md).

## Earlier workflows

Workflows created with `pscale workflow create`, the earlier dashboard workflow pages, or the `/workflows` API endpoints are managed separately from MoveTables workflows. [`pscale workflow`](../../cli/workflow.md) and the `/workflows` API endpoints are deprecated. Use `pscale branch vtctl move-tables` and the MoveTables API for new work.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
