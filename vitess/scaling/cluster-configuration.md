---
url: https://planetscale.com/docs/vitess/scaling/cluster-configuration
title: "Cluster Configuration"
description: ""
access_date: 2026-10-20T18:01:41.586Z
current_date: 2026-10-20T18:01:41.586Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Cluster configuration

> The Clusters page in your PlanetScale dashboard allows you to configure your PlanetScale cluster.

From here, you can:

* Adjust the instance sizes for [keyspaces](../sharding/keyspaces.md)
* Enable [per-shard sizing](../cluster-configuration/per-shard-sizing.md) on sharded keyspaces
* Create sharded or unsharded keyspaces
* Create [external keyspaces](../cluster-configuration.md#create-an-external-keyspace) that connect a production branch to an existing MySQL database
* Switch between [Metal](../../plans/planetscale-skus.md#metal) and [network-attached storage](../../plans/planetscale-skus.md#network-attached-storage)
* Adjust the number of replicas for each keyspace
* Add and configure [read-only regions](read-only-regions.md) for each keyspace
* Adjust the VSchema
* Adjust the number and size of [VTGates](vtgates.md)
* Turn on [VTGate autoscaling](vtgates.md#autoscaling-vtgates)
* View the CPU and memory utilization of your VTGates
* View any changes to your VTGates
* Adjust the [memory allocated to vector indexes](../vectors.md#resource-requirements) and the InnoDB buffer pool, for any branch with [vectors](../vectors.md) enabled
* Configure [VReplication settings](#vreplication-settings)
* Configure [replication durability constraints](#replication-durability-constraints)
* Enable or disable the [tablet throttler](#throttler) and set its replication lag threshold
* Tune [VTGate, VTTablet, and MySQL parameters](../cluster-configuration/parameters.md)

This documentation will cover how to use everything on this Clusters page. For a full walkthrough with an example of setting up a sharded keyspace, refer to the [Sharding quickstart](../sharding/sharding-quickstart.md).

If you would like additional support configuring your sharded keyspaces, our [Enterprise plan](../../planetscale-plans.md#enterprise-plan) may be a good fit. [Get in touch](https://planetscale.com/contact) for a quick assessment.

## Adjust your cluster size

To adjust your cluster size, first select the [keyspace](../sharding/keyspaces.md) you'd like to adjust. If you only have one keyspace, it will be selected by default.

Click on the "Cluster size" dropdown. Select the new cluster size, ([Metal](../../metal.md) or network-attached storage). If you select Metal, make sure you select the correct storage size. Finally. click "Save".

A network-attached storage cluster (Amazon Elastic Block Storage or Google Persistent Disk) may take a few minutes to change sizes. And a Metal cluster can take hours, depending on the size.

You can check the status of the resize from the [database homepage](../architecture.md#resizing) in the dashboard, [CLI](../../cli/keyspace.md), or [API](../../api/reference/get_keyspace_rollout_status.md). There will be no downtime or locking during this process.

For more information about selecting a cluster size, see the [Cluster sizing documentation](../../plans/cluster-sizing.md).

## Per-shard sizing

On a sharded keyspace, enable [per-shard sizing](../cluster-configuration/per-shard-sizing.md) from the Shards tab, then set a cluster size on individual shards. Shards you don't set keep the keyspace default.

## Configure read-only regions

Each keyspace has a "Read-only regions" section where you can add [read-only regions](read-only-regions.md) and choose the cluster size and number of replicas for each region. The monthly cost of each region is shown as you configure it. See the [Read-only regions documentation](read-only-regions.md) for more information.

## Create a keyspace

> [!WARNING]
>
> Misconfiguration to keyspaces can cause availability issues. We recommend thoroughly reading through the documentation
> in the [Sharding section](../sharding.md) of the docs prior to adding additional keyspaces. If you have any
> questions, please [reach out to our support team](https://planetscale.com/contact?initial=support).

> [!WARNING]
>
> If you are using [Vitess global routing](https://vitess.io/docs/reference/features/global-routing/), you must take extra care when adding a sharded keyspace to your database (for example, if you are using `@primary`).
> Before creating one, you must ensure that all tables from your first *unsharded keyspace* are added to the `VSchema` of that *unsharded keyspace*. Eg:
>
> ```
> {
>   "tables": {
>     "users": { }
>     ...
>   }
> }
> ```
>
> Otherwise, queries will fail. [Learn more about VSchema.](../sharding/vschema.md)

> [!NOTE]
>
> Sharded keyspaces are not currently supported on databases that have foreign key constraints enabled.

To create a new [keyspace](../sharding/keyspaces.md):

**Step 1**

Select the database you want to configure.

**Step 2**

Click "Clusters" in the left nav.

**Step 3**

You should see the existing unsharded keyspace for your database here.

**Step 4**

Click "New keyspace".

**Step 5**

Enter the keyspace name. For example, if your existing unsharded keyspace is named `metal`, you may create a sharded
keyspace named `metal-sharded`.

**Step 6**

Select whether you want to keep it unsharded, or, if not, select the number of shards you want to exist in this
keyspace. In most cases, you will be adding a new sharded keyspace. Adding a new unsharded keyspace is not a common
use case, but is an option if you're looking to do [vertical
sharding](https://planetscale.com/learn/courses/database-scaling/sharding/vertical-sharding).

> [!NOTE]
>
> The cost of adding this additional keyspace largely depends on the number of shards you choose, the cluster size, and if you'd like to add additional replicas.

**Step 1**

Choose the cluster sizes you would like to use for this keyspace. Keep in mind, if you are creating a sharded
keyspace, this will spin up multiple clusters of the selected size. For example, if you are creating 4 shards and
choose the `PS-80` cluster size, we will create 4 `PS-80`s, each with 1 primary and 2 replicas.

**Step 2**

Select the number of *additional* replicas, if any, that you'd like to add to each cluster. Each cluster comes with
2 replicas by default, so any number you choose will be in addition to those 2.

**Step 3**

Review the new monthly cost for this keyspace below. This is in addition to your existing unsharded keyspace, as
well as any other keyspaces you add.

**Step 4**

Once satisfied, click "Create keyspace".

To attach an existing MySQL database as a keyspace instead of creating an internal one, see [Create an external keyspace](../cluster-configuration.md#create-an-external-keyspace).

## Modify the VSchema of a keyspace via Clusters page

> [!NOTE]
>
> You can modify the VSchema on your development branch either on the Clusters page , using the [`ALTER VSCHEMA` command](../sharding/vschema.md#modifying-vschema), or with the pscale CLI using [`pscale keyspace vschema update`](../../cli/keyspace.md).

Once you have created your keyspace, you will see a new tab: **VSchema**. The VSchema contains information about how the keyspace is sharded, sequence tables, and other Vitess schema information. The VSchema tab allows you to configure the Vschema for your new keyspace or modify it for existing keyspaces.

We do not recommend modifying the VSchema directly on your production branch. In fact, it is not possible to do if you have [safe migrations](../schema-changes/safe-migrations.md) enabled (as recommended). Instead, to modify the VSchema, you should first [create a new development branch](../schema-changes/branching.md). Once you have your branch ready, follow these steps:

**Step 1**

To update the VSchema on the Clusters page, select your new development branch from the dropdown at the top, and
then select the keyspace below that has the VSchema you'd like to modify.

**Step 2**

Next, click the tab labeled "VSchema".

**Step 3**

Modify the VSchema configuration JSON file as needed. Refer to the [VSchema
documentation](../sharding/vschema.md) for more information about the available options.

**Step 4**

When finished, click "Save changes". We will validate your VSchema, and if it is valid, the changes will be saved.
If there are errors, we will warn you here to change them before saving.

**Step 5**

Go back to your "Branches" tab and click on the development branch that you modified. You should see a note on the
right that says "Updated VSchema configuration" which lets you know the VSchema(s) for this branch has been
modified.

**Step 6**

From here, go through the normal [deploy request process](../schema-changes/deploy-requests.md) to deploy
this change to production.

Once your change is deployed to production, you can come back to the Clusters page, switch to your production branch, and view the updates to your VSchema. You can also click the "Changes" tab to see information, such as the resize event, status, and start/end time for any previous changes to the VSchema.

## Modify routing rules

This configuration setting is currently only available for some Enterprise customers. To modify your routing rules, click "Manage routing rules" on the bottom left of the keyspace configuration panel.

Again, you will need to create a new branch to modify routing rules, as described in the "Modify the VSchema of a keyspace" section above.

## Modify keyspace settings

There are a number of keyspace-specific settings you can use to customize keyspace behavior.

### Replication durability constraints

By default, replication to replica and read-only VTTablets is configured to maximize safety and data integrity. This behavior can be relaxed for performance improvements and reduced replication lag.

* **Maximum** — Default setting; use maximum durability constraints.
* **Dynamic** — Use maximum durability constraints when replication lag is under 5s, and automatically relax durability constraints when replication lag exceeds 5s. Durability constraints are automatically set back to maximum when replication lag reduces to under 5s.
* **Minimum** — Reduce durability constraints. Optimizes for replica and read-only performance, but has highest risk of data loss on crashed instances.

### VReplication settings

These settings improve performance during [VReplication](https://vitess.io/docs/reference/vreplication/vreplication/) processes like deploy requests and workflows.

* **Optimize inserts** — Enabled by default. When enabled, during binlog replication catch-up, skip sending insert events for rows that have yet to be copied. For more technical details, see the corresponding [Vitess implementation](https://github.com/vitessio/vitess/pull/7708).
* **Allow NOBLOB binlog row image** — Enabled by default. When enabled, then we support enabling MySQL’s NOBLOB binlog mode, to omit unchanged BLOB and TEXT columns from replication events, reducing binlog size. For more technical details, see the corresponding [Vitess Implementation](https://github.com/vitessio/vitess/pull/14502).
* **Batch binlog statements** — Disabled by default. If enabled, batches binlog statements and transactions to limit the number of round-trips to MySQL. For more technical details, see the corresponding [Vitess implementation](https://github.com/vitessio/vitess/pull/14502).

### Throttler

The [Vitess tablet throttler](https://vitess.io/docs/reference/features/tablet-throttler/) pauses schema migrations and VReplication workflows when replication lag rises above a threshold. You can enable or disable it per keyspace, and set the lag threshold, from the same keyspace settings form.

* **Enable** — On by default. When enabled, migrations and workflows pause while replication lag is above the threshold. This setting applies across all shards in the keyspace.
* **Replication lag threshold** — Seconds of lag that trip the throttler. The default is 5 seconds.

You can also change these settings from the CLI with `pscale keyspace update-settings`. See the [keyspace CLI docs](../../cli/keyspace.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
