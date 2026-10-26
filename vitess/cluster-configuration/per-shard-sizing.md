---
url: https://planetscale.com/docs/vitess/cluster-configuration/per-shard-sizing
title: "Per Shard Sizing"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Per-shard sizing

> Adjust VTTablet sizes for individual shards in a Vitess keyspace

This page covers Vitess.

Neki version: [Neki](https://planetscale.com/docs/neki/cluster-configuration/cluster-sizing.md#size-profiles-for-their-assigned-shards)

Per-shard sizing lets you adjust the VTTablet size of individual shards in a sharded Vitess keyspace instead of resizing the whole keyspace. A shard that takes more traffic can run more CPU and memory than the rest of the keyspace, and on [Metal](https://planetscale.com/docs/plans/planetscale-skus.md#metal) it can run more local storage.

Enable per-shard sizing from the keyspace Shards tab, then set a size on any shard. Shards you don't set keep the keyspace default.

## Eligibility

Per-shard sizing is available on a Vitess keyspace that:

* Has two or more shards, all covering equal [key ranges](https://planetscale.com/docs/vitess/sharding/vindexes.md). A four-shard keyspace named `-40`, `40-80`, `80-c0`, `c0-` is eligible. One named `-80`, `80-c0`, `c0-`, where the first shard covers half the keyspace and the others cover a quarter each, is not.
* Is not imported or external

If **Enable per-shard sizing** is not shown on the Shards tab, the keyspace is not eligible.

You cannot enable per-shard sizing while a [keyspace resize](https://planetscale.com/docs/vitess/cluster-configuration.md#adjust-your-cluster-size) or [deploy request](https://planetscale.com/docs/vitess/schema-changes/deploy-requests.md) is in progress.

## Enable per-shard sizing

Enabling per-shard sizing is metadata only. Existing key ranges, [VSchema](https://planetscale.com/docs/vitess/sharding/vschema.md), routing, and the keyspace default cluster size stay the same until you resize a shard.

**Step 1**

In the [PlanetScale dashboard](https://app.planetscale.com), open the Vitess database and select the branch.

**Step 2**

Open the keyspace and go to the **Shards** tab.

**Step 3**

Click **Enable per-shard sizing**. The dialog confirms the shard count and default size, for example: "This keyspace has N shards with a default size of PS-X. Once enabled you will be able to adjust the sizes of individual shards."

**Step 4**

Confirm. You can then set sizes on individual shards, or leave shards on the keyspace default.

## Resize a shard

After per-shard sizing is enabled, resize a shard the same way you [resize a keyspace](https://planetscale.com/docs/vitess/cluster-configuration.md#adjust-your-cluster-size): pick a cluster size and save. This changes the CPU, memory, and, on Metal, the local storage for that shard's primary and replicas.

Clearing a shard's size returns it to the keyspace default.

All shards in a keyspace must stay on the same storage class: all [Metal](https://planetscale.com/docs/plans/planetscale-skus.md#metal) or all [network-attached storage](https://planetscale.com/docs/plans/planetscale-skus.md#network-attached-storage). The size picker only lists sizes that match the keyspace storage class.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
