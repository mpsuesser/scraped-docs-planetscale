---
url: https://planetscale.com/docs/vitess/cluster-configuration
title: "Cluster Configuration"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

From here, you can:

- Adjust the instance sizes for [keyspaces](sharding/keyspaces.md)
- Create sharded or unsharded keyspaces
- Switch between [Metal](../plans/planetscale-skus.md#metal) and [network-attached storage](../plans/planetscale-skus.md#network-attached-storage)
- Adjust the number of replicas for each keyspace
- Add and configure [read-only regions](scaling/read-only-regions.md) for each keyspace
- Adjust the VSchema
- Adjust the number and size of [VTGates](scaling/vtgates.md)
- Turn on [VTGate autoscaling](scaling/vtgates.md#autoscaling-vtgates)
- View the CPU and memory utilization of your VTGates
- View any changes to your VTGates
- Adjust the [memory allocated to vector indexes](vectors.md#resource-requirements) and the InnoDB buffer pool, for any branch with [vectors](vectors.md) enabled
- Configure [VReplication settings](#vreplication-settings)
- Configure [replication durability constraints](#replication-durability-constraints)
- Tune [VTGate, VTTablet, and MySQL parameters](cluster-configuration/parameters.md)

This documentation will cover how to use everything in this Clusters page. For a full walkthrough with an example of setting up a sharded keyspace, refer to the [Sharding quickstart](sharding/sharding-quickstart.md).

If you would like additional support configuring your sharded keyspaces, our [Enterprise plan](../planetscale-plans.md#enterprise-plan) may be a good fit. [Get in touch](https://planetscale.com/contact) for a quick assessment.

## Adjust your cluster size

To adjust your cluster size, first select the [keyspace](sharding/keyspaces.md) you’d like to adjust. If you only have one keyspace, it will be selected by default.

Click on the “Cluster size” dropdown. Select the new cluster size, ([Metal](../metal.md) or network-attached storage). If you select Metal, make sure you select the correct storage size. Finally. click “Save”.

A network-attached storage cluster (Amazon Elastic Block Storage or Google Persistent Disk) may take a few minutes to change sizes. And a Metal cluster can take hours, depending on the size.

You can check the status of the resize from the [database homepage](architecture.md#resizing) in the dashboard, [CLI](../cli/keyspace.md), or [API](../api/reference/get_keyspace_rollout_status.md). There will be no downtime or locking during this process.

For more information about selecting a cluster size, see the [Cluster sizing documentation](../plans/cluster-sizing.md).

## Configure read-only regions

Each keyspace has a “Read-only regions” section where you can add [read-only regions](scaling/read-only-regions.md) and choose the cluster size and number of replicas for each region. The monthly cost of each region is shown as you configure it. See the [Read-only regions documentation](scaling/read-only-regions.md) for more information.

## Create a keyspace

Misconfiguration to keyspaces can cause availability issues. We recommend thoroughly reading through the documentation in the [Sharding section](sharding.md) of the docs prior to adding additional keyspaces. If you have any questions, please [reach out to our support team](https://planetscale.com/contact?initial=support).

If you are using [Vitess global routing](https://vitess.io/docs/reference/features/global-routing/), you must take extra care when adding a sharded keyspace to your database (for example, if you are using `@primary`). Before creating one, you must ensure that all tables from your first *unsharded keyspace* are added to the `VSchema` of that *unsharded keyspace*. Eg:

```text
{
  "tables": {
    "users": { }
    ...
  }
}
```

Otherwise, queries will fail. [Learn more about VSchema.](sharding/vschema.md)

Sharded keyspaces are not currently supported on databases that have foreign key constraints enabled.

To create a new [keyspace](sharding/keyspaces.md):

The cost of adding this additional keyspace largely depends on the number of shards you choose, the cluster size, and if you’d like to add additional replicas.

## Modify the VSchema of a keyspace via the Clusters page

You can modify the VSchema on your development branch either in the Clusters page, using the [`ALTER VSCHEMA` command](sharding/vschema.md#modifying-vschema), or with the pscale CLI using [`pscale keyspace vschema update`](../cli/keyspace.md).

Once you have created your keyspace, you will see a new tab: **VSchema**. The VSchema contains information about how the keyspace is sharded, sequence tables, and other Vitess schema information. The VSchema tab allows you to configure the Vschema for your new keyspace or modify it for existing keyspaces.

We do not recommend modifying the VSchema directly on your production branch. In fact, it is not possible to do if you have [safe migrations](schema-changes/safe-migrations.md) enabled (as recommended). Instead, to modify the VSchema, you should first [create a new development branch](schema-changes/branching.md). Once you have your branch ready, follow these steps:

Once your change is deployed to production, you can come back to the Clusters page, switch to your production branch, and view the updates to your VSchema. You can also click the “Changes” tab to see information, such as the resize event, status, and start/end time for any previous changes to the VSchema.

## Modify routing rules

This configuration setting is currently only available for some Enterprise customers. To modify your routing rules, click “Manage routing rules” on the bottom left of the keyspace configuration panel.

Again, you will need to create a new branch to modify routing rules, as described in the “Modify the VSchema of a keyspace” section above.

## Modify keyspace settings

There are a number of keyspace-specific settings you can use to customize keyspace behavior.

### Replication durability constraints

By default, replication to replica and read-only VTTablets is configured to maximize safety and data integrity. This behavior can be relaxed for performance improvements and reduced replication lag.

- **Maximum** — Default setting; use maximum durability constraints.
- **Dynamic** — Use maximum durability constraints when replication lag is under 5s, and automatically relax durability constraints when replication lag exceeds 5s. Durability constraints are automatically set back to maximum when replication lag reduces to under 5s.
- **Minimum** — Reduce durability constraints. Optimizes for replica and read-only performance, but has highest risk of data loss on crashed instances.

### VReplication settings

These settings improve performance during [VReplication](https://vitess.io/docs/reference/vreplication/vreplication/) processes like deploy requests and workflows.

- **Optimize inserts** — Enabled by default. When enabled, during binlog replication catch-up, skip sending insert events for rows that have yet to be copied. For more technical details, see the corresponding [Vitess implementation](https://github.com/vitessio/vitess/pull/7708).
- **Allow NOBLOB binlog row image** — Enabled by default. When enabled, then we support enabling MySQL’s NOBLOB binlog mode, to omit unchanged BLOB and TEXT columns from replication events, reducing binlog size. For more technical details, see the corresponding [Vitess Implementation](https://github.com/vitessio/vitess/pull/14502).
- **Batch binlog statements** — Disabled by default. If enabled, batches binlog statements and transactions to limit the number of round-trips to MySQL. For more technical details, see the corresponding [Vitess implementation](https://github.com/vitessio/vitess/pull/14502).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
