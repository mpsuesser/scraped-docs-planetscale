---
url: https://planetscale.com/docs/vitess/scaling/read-only-regions
title: "Read Only Regions"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

## Overview

Replicate your production database across the globe by creating read-only regions in any available [PlanetScale region](https://planetscale.com/docs/vitess/regions).

This feature supports globally distributed applications by enabling your database to perform low latency reads in the regions closest to your applications and users.

Read-only regions are configured per [keyspace](../sharding/keyspaces.md). Each read-only region runs a single replica by default, and you can choose the cluster size and number of replicas for each region independently of your primary region.

## How to create a read-only region

To add more than one region, click “ **Add another region** ” and configure each region before saving.

## How to change a read-only region’s cluster size or replica count

Each read-only region has its own cluster size and replica count. For example, a region that serves less read traffic than your primary region can run a smaller cluster size.

If a read-only region runs a single replica, that replica is replaced during a cluster size change, and reads routed to the region will fail until the replacement completes. With two or more replicas, the region continues to serve reads while its replicas are replaced. We recommend running at least two replicas in any read-only region where uninterrupted reads are important.

## How to remove a read-only region

Once you remove a region, you will no longer be charged for the storage or row reads associated with that region. If you were using global replica credentials, you do not need to take any additional action. Read queries will still be sent to the closest replica for any queries that are using global replica credentials.

## How to query a read-only region

Connecting to a read-only region requires using a [replica credential](replicas.md). You can create a global replica credential by following these steps:

Alternatively, you can create a connection string by going to your database settings page > “ **Passwords** ” > “ **New password** ”.

All queries made using this password will be routed to your branch’s replicas or the nearest read-only region. If you want to route queries to a specific read-only region, you can go to the “ **Passwords** ” page within your database’s settings page and select the created password. Under “ **Database endpoint** ”, you can then select “ **Direct** ” and choose your desired host from the “ **Host** ” dropdown.

## Concepts

### Read-only regions and keyspaces

Read-only regions are configured independently for each keyspace on your production branch. If your branch has multiple keyspaces, each keyspace has its own set of read-only regions, with its own cluster size and replica count in each region. One keyspace can replicate to a region that another keyspace does not.

For sharded keyspaces, the configuration applies to every shard in the keyspace. For example, a read-only region with 2 replicas in a 4-shard keyspace runs 2 replicas of each shard in that region, and the cost shown when configuring the region is per shard.

### Replication across regions

PlanetScale replicates your data across regions with an asynchronous strategy, first storing your changes in the primary region and then forwarding them to your read-only region(s). The time that it takes those changes to propagate to your read-only region can be defined as “replication lag” and be measured by issuing the following statement to your read-only regions:

```sql
SELECT max_repl_lag();
```

The `max_repl_lag()` function will return an instantaneous measurement of the maximum amount of seconds it has been since your read-only region has stored changes made to your primary region.

### Read-only connections

Connecting to a read-only region will allow you to query your data, but will not allow you to insert, update, or delete it.

## Availability and pricing

Read-only regions are available on the [Base and Enterprise plans](../../planetscale-plans.md). Read-only regions are priced differently depending on the selected region.

### Replica costs

The prices in the tables below cover a read-only region running a single replica at the selected cluster size. Each replica beyond the first is billed at the standard additional replica rate for that cluster size. This is the same rate as additional [replicas](replicas.md) in your primary region. The total monthly cost for a region is shown in the app as you configure it.

For sharded keyspaces, read-only region charges apply to each shard in the keyspace. As with all cluster charges, read-only region charges are prorated, so changing a region’s cluster size or replica count mid-month only bills the new configuration for the time it was running.

### Storage costs

Your storage costs will increase linearly with the amount of read-only regions added. Adding new read-only regions will always be billed as standalone storage and will not count toward your included storage.

As an example, let’s say you’re on our Base plan with 10 GB of included storage and your primary contains 7 GB of data. If you have two read-only regions, each one will be charged at our additional storage rate, for a total of 14 GB. The read-only region storage rate is $0.75 per GB, and in this case would lead to an additional storage charge of $10.50.

For more information on storage billing costs, see our [Billing documentation](../../planetscale-plans.md).

## Pricing

Below we include the costs for read-only regions in each available region up to `400` -level instances. Larger sizes are available in the app.

### AWS ap-northeast-1 (Tokyo)

| Read-only region | Price |
| --- | --- |
| PS-10RR | $19 |
| PS-20RR | $28 |
| PS-40RR | $48 |
| PS-80RR | $86 |
| PS-160RR | $168 |
| PS-320RR | $336 |
| PS-400RR | $480 |

| Metal read-only region | Storage | Price |
| --- | --- | --- |
| M-160RR | 110 GB | $296 |
| M-160RR | 460 GB | $324 |
| M-320RR | 229 GB | $588 |
| M-320RR | 929 GB | $640 |

### AWS ap-south-1 (Mumbai)

| Read-only region | Price |
| --- | --- |
| PS-10RR | $10 |
| PS-20RR | $15 |
| PS-40RR | $26 |
| PS-80RR | $46 |
| PS-160RR | $91 |
| PS-320RR | $182 |
| PS-400RR | $260 |

| Metal read-only region | Storage | Price |
| --- | --- | --- |
| M-160RR | 110 GB | $256 |
| M-160RR | 460 GB | $312 |
| M-320RR | 229 GB | $508 |
| M-320RR | 929 GB | $620 |

### AWS ap-southeast-1 (Singapore)

| Read-only region | Price |
| --- | --- |
| PS-10RR | $19 |
| PS-20RR | $28 |
| PS-40RR | $48 |
| PS-80RR | $86 |
| PS-160RR | $168 |
| PS-320RR | $336 |
| PS-400RR | $480 |

| Metal read-only region | Storage | Price |
| --- | --- | --- |
| M-160RR | 110 GB | $296 |
| M-160RR | 460 GB | $332 |
| M-320RR | 229 GB | $588 |
| M-320RR | 929 GB | $656 |

### AWS ap-southeast-2 (Sydney)

| Read-only region | Price |
| --- | --- |
| PS-10RR | $19 |
| PS-20RR | $28 |
| PS-40RR | $48 |
| PS-80RR | $86 |
| PS-160RR | $168 |
| PS-320RR | $336 |
| PS-400RR | $480 |

| Metal read-only region | Storage | Price |
| --- | --- | --- |
| M-160RR | 110 GB | $280 |
| M-160RR | 460 GB | $332 |
| M-320RR | 229 GB | $560 |
| M-320RR | 929 GB | $656 |

### AWS eu-central-1 (Frankfurt)

| Read-only region | Price |
| --- | --- |
| PS-10RR | $19 |
| PS-20RR | $28 |
| PS-40RR | $48 |
| PS-80RR | $86 |
| PS-160RR | $168 |
| PS-320RR | $336 |
| PS-400RR | $480 |

| Metal read-only region | Storage | Price |
| --- | --- | --- |
| M-160RR | 110 GB | $292 |
| M-160RR | 460 GB | $328 |
| M-320RR | 229 GB | $584 |
| M-320RR | 929 GB | $652 |

### AWS eu-west-1 (Dublin)

| Read-only region | Price |
| --- | --- |
| PS-10RR | $18 |
| PS-20RR | $26 |
| PS-40RR | $44 |
| PS-80RR | $80 |
| PS-160RR | $156 |
| PS-320RR | $313 |
| PS-400RR | $448 |

| Metal read-only region | Storage | Price |
| --- | --- | --- |
| M-160RR | 110 GB | $272 |
| M-160RR | 460 GB | $304 |
| M-320RR | 229 GB | $540 |
| M-320RR | 929 GB | $604 |

### AWS eu-west-2 (London)

| Read-only region | Price |
| --- | --- |
| PS-10RR | $18 |
| PS-20RR | $28 |
| PS-40RR | $46 |
| PS-80RR | $84 |
| PS-160RR | $163 |
| PS-320RR | $327 |
| PS-400RR | $468 |

| Metal read-only region | Storage | Price |
| --- | --- | --- |
| M-160RR | 110 GB | $288 |
| M-160RR | 460 GB | $320 |
| M-320RR | 229 GB | $568 |
| M-320RR | 929 GB | $632 |

### AWS sa-east-1 (Sao Paulo)

| Read-only region | Price |
| --- | --- |
| PS-10RR | $25 |
| PS-20RR | $38 |
| PS-40RR | $63 |
| PS-80RR | $114 |
| PS-160RR | $223 |
| PS-320RR | $447 |
| PS-400RR | $639 |

### AWS us-east-1 (N. Virginia)

| Read-only region | Price |
| --- | --- |
| PS-10RR | $16 |
| PS-20RR | $24 |
| PS-40RR | $40 |
| PS-80RR | $72 |
| PS-160RR | $140 |
| PS-320RR | $280 |
| PS-400RR | $400 |

| Metal read-only region | Storage | Price |
| --- | --- | --- |
| M-160RR | 110 GB | $244 |
| M-160RR | 460 GB | $276 |
| M-160RR | 1,241 GB | $404 |
| M-320RR | 229 GB | $484 |
| M-320RR | 929 GB | $584 |
| M-320RR | 2,490 GB | $804 |

### AWS us-east-2 (Ohio)

| Read-only region | Price |
| --- | --- |
| PS-10RR | $16 |
| PS-20RR | $24 |
| PS-40RR | $40 |
| PS-80RR | $72 |
| PS-160RR | $140 |
| PS-320RR | $280 |
| PS-400RR | $400 |

| Metal read-only region | Storage | Price |
| --- | --- | --- |
| M-160RR | 110 GB | $244 |
| M-160RR | 460 GB | $276 |
| M-160RR | 1,241 GB | $404 |
| M-320RR | 229 GB | $484 |
| M-320RR | 929 GB | $584 |
| M-320RR | 2,490 GB | $804 |

### AWS us-west-2 (Oregon)

| Read-only region | Price |
| --- | --- |
| PS-10RR | $16 |
| PS-20RR | $24 |
| PS-40RR | $40 |
| PS-80RR | $72 |
| PS-160RR | $140 |
| PS-320RR | $280 |
| PS-400RR | $400 |

| Metal read-only region | Storage | Price |
| --- | --- | --- |
| M-160RR | 110 GB | $244 |
| M-160RR | 460 GB | $276 |
| M-160RR | 1,241 GB | $404 |
| M-320RR | 229 GB | $484 |
| M-320RR | 929 GB | $584 |
| M-320RR | 2,490 GB | $804 |

### GCP asia-northeast3 (Seoul, South Korea)

| Read-only region | Price |
| --- | --- |
| PS-10RR | $19 |
| PS-20RR | $28 |
| PS-40RR | $48 |
| PS-80RR | $86 |
| PS-160RR | $168 |
| PS-320RR | $336 |
| PS-400RR | $480 |

### GCP northamerica-northeast1 (Montréal, Québec)

| Read-only region | Price |
| --- | --- |
| PS-10RR | $16 |
| PS-20RR | $24 |
| PS-40RR | $40 |
| PS-80RR | $72 |
| PS-160RR | $140 |
| PS-320RR | $280 |
| PS-400RR | $400 |

### GCP us-central1 (Council Bluffs, Iowa)

| Read-only region | Price |
| --- | --- |
| PS-10RR | $16 |
| PS-20RR | $24 |
| PS-40RR | $40 |
| PS-80RR | $72 |
| PS-160RR | $140 |
| PS-320RR | $280 |
| PS-400RR | $400 |

### GCP us-east4 (Ashburn, Virginia)

| Read-only region | Price |
| --- | --- |
| PS-10RR | $16 |
| PS-20RR | $24 |
| PS-40RR | $40 |
| PS-80RR | $72 |
| PS-160RR | $140 |
| PS-320RR | $280 |
| PS-400RR | $400 |

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
