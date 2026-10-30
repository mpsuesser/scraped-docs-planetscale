---
url: https://planetscale.com/docs/vitess/scaling/workflows
title: "Workflows"
description: ""
access_date: 2026-10-30T18:26:51.105Z
current_date: 2026-10-30T18:26:51.105Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# What are workflows?

> Vitess MoveTables and Materialize workflows for sharding tables, reorganizing keyspaces, importing external MySQL databases, and near-realtime materializations.

This page covers Vitess.

Workflows are [Vitess VReplication](https://vitess.io/docs/reference/vreplication/vreplication/) workflows. PlanetScale supports [MoveTables](https://vitess.io/docs/user-guides/migration/move-tables/) and [Materialize](https://vitess.io/docs/reference/vreplication/materialize/) on Vitess branches.

You create and drive workflows with [`pscale branch vtctl`](../../cli/vtctl.md) (or the matching APIs). The dashboard shows the live state of each workflow, read directly from Vitess.

> [card] [Move tables between keyspaces](#move-tables-between-keyspaces)
>
> Move tables between keyspaces within your PlanetScale database. Used for sharding, resharding, and reorganizing data.

> [card] [Import a database](#import-a-database)
>
> Import data from an external MySQL database into PlanetScale with no downtime.

> [card] [Materialize tables and views](#materialize)
>
> Keep copies, aggregations, or views in sync in near-realtime.

## Move tables between keyspaces

Move tables from one [keyspace](https://planetscale.com/docs/vitess/sharding/keyspaces.md) to another within your PlanetScale database:

* **Unsharded to sharded**: Move tables from an unsharded keyspace to a new sharded keyspace. This is the primary way to shard existing tables. See the [Sharding quickstart](https://planetscale.com/docs/vitess/sharding/sharding-quickstart.md).
* **Sharded to sharded**: Move tables between sharded keyspaces to change the number of shards. See [Modifying the number of shards](https://planetscale.com/docs/vitess/sharding/sharding-a-sharded-keyspace.md).
* **Unsharded to unsharded**: Move tables from one unsharded keyspace to another unsharded keyspace.

## Import a database

To import an **external internet-accessible MySQL database**, you first connect it to your production branch as an [external keyspace](https://planetscale.com/docs/vitess/cluster-configuration.md#create-an-external-keyspace). Then you run a workflow with the external keyspace as the source and your PlanetScale keyspace as the target. The import copies your data, replicates new changes in real time, and switches traffic to PlanetScale with no downtime.

For a full walkthrough, see the [Database imports documentation](https://planetscale.com/docs/vitess/imports/database-imports.md).

## MoveTables lifecycle

Every MoveTables workflow follows the same lifecycle:

1. **Create**: `move-tables create` copies the table schemas to the target keyspace and starts copying rows.
2. **Copy**: Vitess copies the existing rows from the source to the target. Streams are `Copying`.
3. **Replicate**: Once the copy finishes, Vitess keeps the target up to date with every new write on the source. Streams are `Running`.
4. **Verify**: Optionally compare the source and target with [`vdiff`](../../cli/vdiff.md).
5. **Switch traffic**: `move-tables switch-traffic` moves replica traffic, then primary traffic, to the target keyspace. `move-tables reverse-traffic` moves it back.
6. **Complete**: `move-tables complete` stops replication. Its required flags decide whether the moved tables are dropped from the source keyspace and whether the routing rules are removed. You can also `cancel` a workflow at any point before it is complete.

Use `move-tables status` between steps. It shows each table's copy progress, each stream's state, and the traffic state. JSON output includes a `next_steps` field with the command to run next. See the [`move-tables` reference](../../cli/move-tables.md) for every command and flag.

## Materialize

[Materialize](https://planetscale.com/docs/cli/materialize.md) keeps target tables in sync in near-realtime. Targets can be copies, aggregations, or views defined by a `SELECT` against a single source table.

Prefer MoveTables when you only need to move whole tables between keyspaces. Prefer Materialize when the target needs a different schema or a query that filters or aggregates rows.

1. **Create** with `--table-settings` JSON that names each target table and its `source_expression`.
2. **Show** until streams are replicating.
3. Read from the target tables once they are in sync.
4. **Stop** / **start** to pause and resume, or **cancel** to tear the workflow down.

See the [`materialize` reference](../../cli/materialize.md) for flags and examples, and the [Vitess Materialize docs](https://vitess.io/docs/reference/vreplication/materialize/) for `SELECT` limitations.

## View workflows in the dashboard

Select your database and click **Workflows** in the left nav. Pick a branch to see MoveTables and Materialize workflows on it, across all keyspaces or filtered to one keyspace. The list shows each workflow's type.

Open a **MoveTables** workflow to see:

* The source and target keyspaces, replication lag, and when the workflow last updated
* Which keyspace is serving reads and writes
* Each stream's state, rows copied, and any error message
* Each table's copy progress
* The results of the latest VDiff

Open a **Materialize** workflow to see stream states, replication lag, and each target table with the query that feeds it.

The Workflows page is read-only. To create, switch, complete, or cancel a workflow, use [`pscale branch vtctl move-tables`](../../cli/move-tables.md) or [`pscale branch vtctl materialize`](../../cli/materialize.md).

## Earlier workflows

Workflows created with `pscale workflow create`, the earlier dashboard workflow pages, or the `/workflows` API endpoints are managed separately from MoveTables and Materialize workflows. [`pscale workflow`](../../cli/workflow.md) and the `/workflows` API endpoints are deprecated. Use `pscale branch vtctl move-tables`, `pscale branch vtctl materialize`, and the matching APIs for new work.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
