---
url: https://planetscale.com/docs/vitess/sharding/vschema
title: "Vschema"
description: ""
access_date: 2026-09-02T21:57:53.710Z
current_date: 2026-09-02T21:57:53.710Z
---

## VSchema overview

Each Vitess cluster can have one or more [keyspaces](https://vitess.io/docs/concepts/keyspace/). For unsharded databases, there is a 1:1 relationship between a keyspace and a database within MySQL. For sharded databases, a single keyspace can map to multiple MySQL databases under the hood.

Each keyspace in your PlanetScale database has an associated [VSchema](https://vitess.io/docs/reference/features/vschema/). The VSchema contains information about how the keyspace is sharded, sequence tables, and other Vitess schema information.

## Viewing VSchema

In order to view your VSchema, first go to the “Branches” tab in the PlanetScale app.

Click on the branch you would like to view the VSchema for. Then, select the keyspace and expand out the “Configuration Files” drop-down.

From here, you can inspect your VSchema configuration JSON file.

## Modifying VSchema

You must have a sharded keyspace in order to make VSchema changes.

If you have a database with at least one sharded keyspace, you can modify its VSchema either in the [Clusters](../cluster-configuration.md) tab in the dashboard, from the [pscale CLI](../../cli/keyspace.md), or using `ALTER VSCHEMA ...` commands.

### Using the Clusters page

We do not recommend modifying the VSchema directly on your production branch. In fact, it is not possible to do if you have [safe migrations](../schema-changes/safe-migrations.md) enabled (as recommended). Instead, to modify the VSchema, you should first [create a new development branch](../schema-changes/branching.md). Once you have your branch ready, follow these steps:

Once your change is deployed to production, you can come back to the Clusters page, switch to your production branch, and view the updates to your VSchema. You can also click the “Changes” tab to see information, such as the resize event, status, and start/end time for any previous changes to the VSchema.

### Using ALTER VSchema

You can modify the VSchema with `ALTER VSCHEMA ...` statements over a regular MySQL connection. For a complete list of the supported statements, see the [`ALTER VSCHEMA` syntax reference](alter-vschema.md).

PlanetScale recommends making all such modifications in a development branch. When ready, you can make a deploy request to get the changes into production. Consider the following database with two keyspaces.

`sharded` is a sharded keyspace with two shards and `tweeter` is unsharded. Also note that safe migrations are enabled. In order to make a VSchema change for the production branch in this configuration, we first must create a new branch. We’ll call it `add-tweets`

On this branch you can make your VSchema and schema changes. In this case, we’ll create a new table called `tweets` in the sharded keyspace and also update the VSchema.

We will also create a sequence table in the unsharded keyspace, and update the VSchema accordingly:

We have now made updates both to our Vitess VSchema and MySQL schema. To get these changes into production, navigate to the “Branches” page and select the `add-tweets` branch. Here, you will be presented with a diff of both the VSchema and schema changes:

Click “Create deploy request.” The deploy request should indicate that it is going to apply both VSchema and Schema changes:

Click “Deploy changes.” Once complete, the Vschema and schema changes will be applied to your production branch.

`ALTER VSCHEMA ...` commands also work on unsharded keyspaces, but they are rarely useful there. An unsharded keyspace has only one shard, so Vitess can route every query to it without any VSchema metadata. Most of what `ALTER VSCHEMA` manages — Vindexes, auto-increment columns, and sequence tables — only comes into play when a keyspace is sharded, or is about to be.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
