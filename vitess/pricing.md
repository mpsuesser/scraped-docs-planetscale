---
url: https://planetscale.com/docs/vitess/pricing
title: "Pricing"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

Vitess clusters are charged based on the following:

- Cluster size and [region](#region-pricing)
- Storage size (for network-attached storage clusters)
- [Branch](schema-changes/branching.md) hours beyond the included 1440 hours
- Number of [replicas](scaling/replicas.md)
- Number and size of [VTGates](scaling/vtgates.md)
- [Read-only regions](scaling/read-only-regions.md)

## Branches

Each database includes one production branch that provides a primary instance and two replica instances spread across availability zones. The primary serves all queries by default, but the replicas can be used to serve read traffic, and are also used to maintain high-availability of your cluster.

In addition to the three instances used by your production branch, ~1440 hours of [development branch](../planetscale-plans.md#development-branches) time per month is included. The development branch time works out to two “always on” database instances, though we do recommend spinning them up and down and as needed during your development cycle.

These development branches can replace your existing development or staging environment on other providers. Instead of purchasing 2-3 databases on PlanetScale to recreate your environment, you can just purchase one database cluster and use [branching](schema-changes/branching.md) for development.

You can upsize and downsize your cluster at any time. Pricing is prorated to the millisecond, so if you temporarily upsize, you will only be charged for the larger cluster size for the time that it was running. Billing for a new cluster size begins as soon as you begin the resize. You can also spin up additional production branches at any timing for additional cost. The pricing for these is also prorated.

## Read-only regions

[Read-only regions](scaling/read-only-regions.md) are an additional cost. Each read-only region is configured per keyspace with its own cluster size and replica count: the read-only region rate for the selected cluster size covers a single replica, and each additional replica is billed at the standard additional replica rate. Storage for read-only regions is billed as standalone storage and does not count toward your plan’s included storage.

See the [read-only regions pricing documentation](scaling/read-only-regions.md#availability-and-pricing) for per-region rates and storage costs.

Cluster size options are capped at `PS-160` until you have a successfully paid an invoice of at least $100. If you need larger sizes immediately, please [contact us](https://planetscale.com/contact) to unlock all sizes. You can find the full list of cluster sizes in our [Plans documentation](../planetscale-plans.md#base-plan).

## Region pricing

### Amazon Web Services

## ap-northeast-1 (Tokyo)

## ap-south-1 (Mumbai)

## ap-southeast-1 (Singapore)

## ap-southeast-2 (Sydney)

## eu-central-1 (Frankfurt)

## ca-central-1 (Montreal)

## eu-west-1 (Dublin)

## eu-west-2 (London)

## sa-east-1 (Sao Paulo)

## us-east-1 (N. Virginia)

## us-east-2 (Ohio)

## us-west-2 (Oregon)

### Google Cloud

## asia-northeast3 (Seoul, South Korea)

## europe-west4 (Eemshaven, Netherlands)

## northamerica-northeast1 (Montréal, Québec)

## us-central1 (Council Bluffs, Iowa)

## us-east1 (Moncks Corner, South Carolina)

## us-east4 (Ashburn, Virginia)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
