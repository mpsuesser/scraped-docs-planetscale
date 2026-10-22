---
url: https://planetscale.com/docs/vitess/architecture
title: "Architecture"
description: ""
access_date: 2026-10-22T18:21:49.469Z
current_date: 2026-10-22T18:21:49.469Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale Vitess architecture

> PlanetScale's Vitess product is designed for reliability, scalability, and developer productivity.

## Overview

We achieve these goals through a combination of [MySQL](https://planetscale.com/docs/vitess/terminology.md#mysql), [Vitess](https://planetscale.com/docs/vitess/terminology.md#vitess), and our own application and ecosystem we have built atop these open-source technologies.
There is a great deal of infrastructure that enables our databases to be highly-available, secure, and resilient.
In this article, you'll learn about what powers PlanetScale databases and how you can view your database's configuration on our app.

## The infrastructure diagram

After creating a PlanetScale account and joining at least one organization, you can create a database.
Each new database has a single default [keyspace](https://planetscale.com/docs/vitess/terminology.md#keyspace) — a logical database — with the same name as the database.
On the dashboard of every PlanetScale database is a diagram outlining the infrastructure that powers the database.

![Architecture diagram for a PlanetScale database](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=9ec6943a8d9e7fa4d258fdc00fc5f8e1)

By default, the architecture diagram will show the architecture for the keyspace corresponding to your default branch.
Here's how you can tell what keyspace and branch you are viewing the diagram of:

![Architecture diagram for a PlanetScale database](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-labeled.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=f8dfbba16aeda9c7e58a199f7649e9ae)

### Production branches

Production branches are designed for production workloads, and as such are given enough resources to ensure high availability.
By default, every production branch has a single primary MySQL instance and two replicas.
Each primary also comes with 3 [VTGates](https://planetscale.com/docs/vitess/terminology.md#vtgate) across 3 availability zones, which act as proxies for your MySQL instances.
These are all pictured in the diagram for a production branch:

![Production branch architecture](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-production-branch.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=d1688f64961aa7260622d92bfc9f0122)

Generally, the application connecting to this database need not be aware of these various components.
One exception to this is if you are specifically trying to [send queries to a replica](https://planetscale.com/docs/vitess/scaling/replicas.md#how-to-query-replicas).

### Development branches

Development branches are specced to enable the development and testing of new features and are not designed for production workloads.
When a new development branch is created, a single MySQL node is created along with a VTGate that handles connections to that node.
This is reflected in the diagram of a development branch.

![Development branch architecture](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-dev-branch.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=eb7a0c3d1251f39dc99676a50c42e61b)

When you promote a development branch to production status, PlanetScale automatically adds additional replicas and VTGates deployed across multiple availability zones in a given region.

### Read-only regions

The primary of your database is the only node that can accept writes, and it resides in a single region.
You can add [read-only regions](https://planetscale.com/docs/vitess/scaling/read-only-regions.md) to each keyspace on a production branch, which adds replicas in another region that can be used to serve read traffic.
This can help reduce read latency for application servers that are distributed around the world.
Each read-only region's cluster size and number of replicas are configured per keyspace on the [Clusters page](https://planetscale.com/docs/vitess/cluster-configuration.md).

Below, you can see our database has the primary and two replicas in `us-east-2` with read-only replicas added in both `us-west-2` and `eu-central-1`.

![Production branch with read-only regions architecture](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/read-only-regions.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=e04cbb60812049bae0cd8e02d6ecfea0)

The read-only replicas can be identified by the blue globe icon.

## Infrastructure metrics

Each element within the infrastructure diagram for PlanetScale database branches can be selected to display additional metrics related to that element.
These metrics are displayed in expandable cards that present themselves when an element is selected.
By default, the cards display metrics from the last 6 hours but can be adjusted if additional data is needed.

### VTGates

The VTGate node displays the total number of VTGates that exist for a given branch, as well as the number of availability zones in which they live.
Selecting the VTGates node will show the following metrics:

* Number of connections.
* Latency.
* Queries received.
* CPU.
* Memory consumption.

![VTGate metrics](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/vtgate-metrics.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=7f63563d72d7aaed530b17d5146045a4)

### MySQL nodes

Each MySQL node in the diagram will display whether it is the primary node or a replica, along with the region where that node is deployed to.
Clicking any of the MySQL nodes will display the following metrics:

* Database reads and writes for that node.
* Queries served.
* IOPS.
* CPU and Memory utilization.
* Storage utilization over the past week.

![Primary MySQL node metrics](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/vttablet-metrics.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=67b38884dca40c084cd994284c7e2ae3)

Selecting a replica will display the replication lag in addition to the other metrics.

![Replication lag diagram](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/replication-lag-diagram.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=1336d1234b183ef4dfbcf226c291eb3b)

### Replication lag at a glance

Within the infrastructure diagram, you'll also notice that there is a number near the connection points for each replica.
These numbers are a way to read the replication lag between the Primary node and that given node at a glance.

![Replication lag](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/replication-lag.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=1470e0d88205aaaca54acd2d59f2b5ce)

### Database shards

If your database is [sharded](https://planetscale.com/docs/vitess/sharding.md), the infrastructure diagram will represent that as a green stack of shards.

![Stacked shards](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/shard-stack.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=18c7dfaa2566c3329b47bdb6dca445aa)

Selecting the stack from the diagram will open a card displaying all of the shards belonging to that keyspace.

![Shard list](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/shard-list.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=9b6ce295f63cf8e865594329aa6a33fe)

After selecting a shard, you'll be able to choose to look at metrics for either that shard's primary or one of its replicas.

![Shard list](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/shard-primary-replicas-list.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=1f7ba70b238190e895f0cb0411e6327c)

Selecting one will show you the metrics for that specific node in your database architecture.

![Shard](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/shard-primary-metrics.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=95d49bd3c5929730dfaebb759604f1dc)

### Resizing

You can use the [Clusters page](https://planetscale.com/docs/vitess/cluster-configuration.md) menu to resize your keyspaces. On a sharded keyspace, you can also enable [per-shard sizing](https://planetscale.com/docs/vitess/cluster-configuration/per-shard-sizing.md) and resize individual shards.
When a resize is in progress, this will be indicated at the top of the diagram.

![Architecture diagram with resize indicator](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-resize.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=6ec8d02f12cd08efd6d29668a491c57d)

Click on "**View**" to see the status for each shard being resized:

![Per-shard resize status](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/resize-progress.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=06fdecd2db2bf1fdace4f830b3d41f10)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
