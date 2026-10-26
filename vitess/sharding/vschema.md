---
url: https://planetscale.com/docs/vitess/sharding/vschema
title: "Vschema"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# VSchema

> PlanetScale databases are powered by Vitess.

This page covers Vitess.

## VSchema overview

Each Vitess cluster can have one or more [keyspaces](https://vitess.io/docs/concepts/keyspace/).
For unsharded databases, there is a 1:1 relationship between a keyspace and a database within MySQL.
For sharded databases, a single keyspace can map to multiple MySQL databases under the hood.

Each keyspace in your PlanetScale database has an associated [VSchema](https://vitess.io/docs/reference/features/vschema/).
The VSchema contains information about how the keyspace is sharded, sequence tables, and other Vitess schema information.

## Viewing VSchema

In order to view your VSchema, first go to the "Branches" tab in the PlanetScale app.

![PlanetScale app tab bar](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/enterprise/tabs.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=999eb75e681ce17244962c3cc222a095)

Click on the branch you would like to view the VSchema for.
Then, select the keyspace and expand out the "Configuration Files" drop-down.

![PlanetScale keyspace selection and configuration files drop down](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/enterprise/keyspace.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=a2884cf910a9a84f11ac23beac862596)

From here, you can inspect your VSchema configuration JSON file.

![VSchema JSON view](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/enterprise/vschema.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=98286e12ad8804178e1204c3b4a757b4)

## Modifying VSchema

You must have a sharded keyspace in order to make VSchema changes.

If you have a database with at least one sharded keyspace, you can modify its VSchema either in the [Clusters](https://planetscale.com/docs/vitess/cluster-configuration.md) tab in the dashboard, from the [pscale CLI](https://planetscale.com/docs/cli/keyspace.md), or using `ALTER VSCHEMA ...` commands.

### Using the Clusters page

We do not recommend modifying the VSchema directly on your production branch. In fact, it is not possible to do if you have [safe migrations](https://planetscale.com/docs/vitess/schema-changes/safe-migrations.md) enabled (as recommended). Instead, to modify the VSchema, you should first [create a new development branch](https://planetscale.com/docs/vitess/schema-changes/branching.md). Once you have your branch ready, follow these steps:

**Step 1**

To update on the Clusters page, select your new development branch from the dropdown at the top, and then select the
keyspace below that has the VSchema you'd like to modify.

**Step 2**

Next, click the tab labeled "VSchema".

**Step 3**

Modify the VSchema configuration JSON file as needed. Refer to the [VSchema
documentation](https://planetscale.com/docs/vitess/sharding/vschema.md) for more information about the available options.

**Step 4**

When finished, click "Save changes". We will validate your VSchema, and if it is valid, the changes will be saved.
If there are errors, we will warn you here to change them before saving.

**Step 5**

Go back to your "Branches" tab and click on the development branch that you modified. You should see a note on the
right that says "Updated VSchema configuration" which lets you know the VSchema(s) for this branch has been
modified.

**Step 6**

From here, go through the normal [deploy request process](https://planetscale.com/docs/vitess/schema-changes/deploy-requests.md) to deploy
this change to production.

Once your change is deployed to production, you can come back to the Clusters page, switch to your production branch, and view the updates to your VSchema. You can also click the "Changes" tab to see information, such as the resize event, status, and start/end time for any previous changes to the VSchema.

### Using `ALTER VSchema`

You can modify the VSchema with `ALTER VSCHEMA ...` statements over a regular MySQL connection.
For a complete list of the supported statements, see the [`ALTER VSCHEMA` syntax reference](alter-vschema.md).

PlanetScale recommends making all such modifications in a development branch.
When ready, you can make a deploy request to get the changes into production.
Consider the following database with two keyspaces.

![Sharded keyspace](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/enterprise/sharded-keyspace.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=37565511e5d1e6b0c9c7f7ea89d0697e)

`sharded` is a sharded keyspace with two shards and `tweeter` is unsharded.
Also note that safe migrations are enabled.
In order to make a VSchema change for the production branch in this configuration, we first must create a new branch.
We'll call it `add-tweets`

![New branch](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/enterprise/new-branch.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=37d7be81e67eb81ea6a34a3ae16a253e)

On this branch you can make your VSchema and schema changes.
In this case, we'll create a new table called `tweets` in the sharded keyspace and also update the VSchema.

![Create the tweets table](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/enterprise/tweets-table.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=4b576310285299ee264c929474cf01bf)

We will also create a sequence table in the unsharded keyspace, and update the VSchema accordingly:

![Create the sequence table](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/enterprise/sequence-table.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=004856cf8a48f41477528c2ac2d712e7)

We have now made updates both to our Vitess VSchema and MySQL schema.
To get these changes into production, navigate to the "Branches" page and select the `add-tweets` branch.
Here, you will be presented with a diff of both the VSchema and schema changes:

![Schema diff](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/enterprise/schema-diff.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=655ba0a94ea0631f4849990e37e4aca9)

Click "Create deploy request."
The deploy request should indicate that it is going to apply both VSchema and Schema changes:

![Deploy request](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/enterprise/deploy-request.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=78abdfd7f4fb6ebd51c48708eff38d5c)

Click "Deploy changes."
Once complete, the Vschema and schema changes will be applied to your production branch.

> [!NOTE]
>
> `ALTER VSCHEMA ...` commands also work on unsharded keyspaces, but they are rarely useful there.
> An unsharded keyspace has only one shard, so Vitess can route every query to it without any VSchema metadata.
> Most of what `ALTER VSCHEMA` manages — Vindexes, auto-increment columns, and sequence tables — only comes into play when a keyspace is sharded, or is about to be.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
