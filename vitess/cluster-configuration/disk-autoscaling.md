---
url: https://planetscale.com/docs/vitess/cluster-configuration/disk-autoscaling
title: "Disk Autoscaling"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Disk autoscaling

> Disk autoscaling grows the disks in a Vitess keyspace as they fill up, up to a storage limit. You can also turn growth off, or shrink disks you no longer need.

This page covers Vitess.

Postgres version: [Postgres](https://planetscale.com/docs/postgres/cluster-configuration/disk-autoscaling.md)

> [!NOTE]
>
> Disk autoscaling is only for keyspaces on network-attached storage. On [PlanetScale Metal](https://planetscale.com/docs/metal.md), storage is tied to
> the cluster size, so you [resize the keyspace](https://planetscale.com/docs/vitess/cluster-configuration.md) to get more storage.

Each keyspace has a disk scaling setting that controls how the disks of its tablets are sized:

* **Grow automatically**: disks grow as they fill up, until they reach the storage limit. This is the default for new keyspaces.
* **Shrink**: disks are recreated at the size you choose, and automatic growth is then turned off.
* **Off**: disks stay at their current size and will not grow as they fill up.

> [!NOTE]
>
> You're billed for the disk that is allocated, not the storage limit.

## Grow automatically

When a disk fills up, PlanetScale grows it in place, without replacing the tablet or dropping connections. A disk grows when its usage reaches:

* **70%** for disks smaller than 4 TB
* **90%** for disks of 4 TB and larger

Smaller disks grow by a larger percentage than larger disks. A disk can grow at most once an hour, and cloud providers can limit how often a disk can be resized. If you expect data to grow quickly, for example during an import, raise the storage limit ahead of time.

### Storage limit

The storage limit is the maximum size a disk can grow to. Once a disk reaches the limit, it stops growing.

The limit must be at least the size of the largest disk in the keyspace, and no more than your organization's storage limit (4 TB by default). To raise the limit above that, [contact support](https://planetscale.com/contact?initial=support). After support raises the limit, it can only be changed by support.

## Shrink

Disks grow but don't shrink on their own. After your data gets smaller, for example after you drop a large table, use **Shrink** to stop paying for the disk you no longer need.

Cloud providers can't shrink a disk in place, so PlanetScale recreates the disks at the size you choose. Tablets are replaced one at a time, replicas first and the primary last. Each new tablet restores from the latest backup and catches up through replication before the next one is replaced. Before the primary's disk is replaced, the primary role moves to another tablet, which closes existing connections to the primary. Make sure your application can reconnect.

The size you choose:

* Must be a whole number of GB, and no more than your organization's storage limit
* Must be large enough for your data. A shrink is rejected if any disk in the keyspace, including in [read-only regions](https://planetscale.com/docs/vitess/scaling/read-only-regions.md), is using more than 80% of the requested size.

After the shrink, automatic growth stays off. To let disks grow again, switch back to **Grow automatically**.

## Off

With disk scaling off, disks keep their current size. They don't grow, even when they are full. If a disk fills up, MySQL can no longer write to it, so keep an eye on disk usage, or switch back to **Grow automatically**.

## Change the disk scaling setting

**Dashboard**

**Step 1**

Select your database, and click [**Clusters**](https://planetscale.com/docs/vitess/cluster-configuration.md) in the left nav

**Step 2**

Select the keyspace, and open the **Storage** tab

**Step 3**

Choose **Grow automatically**, **Shrink**, or **Off**, and set the storage limit or the size to shrink to

**Step 4**

Click **Save changes**

**CLI**

Use `pscale keyspace update-settings` with `--disk-scaling-strategy`, `--max-storage`, and `--storage`. For example:

```bash theme={null}
pscale keyspace update-settings <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME> \
  --disk-scaling-strategy grow \
  --max-storage 1TiB
```

See [Configure disk scaling](https://planetscale.com/docs/cli/keyspace.md#configure-disk-scaling) in the CLI reference for more examples.

**API**

Use the `storage` object on the [update keyspace](https://planetscale.com/docs/api/reference/update_keyspace.md) endpoint, with `disk_scaling_strategy` (`grow`, `disable`, or `shrink`), `max_storage_bytes`, and `storage_bytes`.

> [!NOTE]
>
> [Read-only regions](https://planetscale.com/docs/vitess/scaling/read-only-regions.md) with network-attached storage have their own disk scaling
> setting, which is **Grow automatically** by default. Changing the keyspace's setting doesn't change theirs. Like Metal
> keyspaces, read-only regions on Metal don't have disk scaling settings. Shards with a custom size from [per-shard
> sizing](https://planetscale.com/docs/vitess/cluster-configuration/per-shard-sizing.md) follow the keyspace's setting.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
