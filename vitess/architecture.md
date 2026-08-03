---
url: https://planetscale.com/docs/vitess/architecture
title: "Architecture"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Overview

We achieve these goals through a combination of [MySQL](terminology.md#mysql), [Vitess](terminology.md#vitess), and our own application and ecosystem we have built atop these open-source technologies. There is a great deal of infrastructure that enables our databases to be highly-available, secure, and resilient. In this article, you’ll learn about what powers PlanetScale databases and how you can view your database’s configuration on our app.

## The infrastructure diagram

After creating a PlanetScale account and joining at least one organization, you can create a database. Each new database has a single default [keyspace](terminology.md#keyspace) — a logical database — with the same name as the database. On the dashboard of every PlanetScale database is a diagram outlining the infrastructure that powers the database.

![Architecture diagram for a PlanetScale database](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-darkmode.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=eb2a2dd0c3b4ce0c5f02c93b9f67fd2b)

Architecture diagram for a PlanetScale database

![Architecture diagram for a PlanetScale database](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=3efd0a7f025f66ba88dc7363a6d759b5)

Architecture diagram for a PlanetScale database

By default, the architecture diagram will show the architecture for the keyspace corresponding to your default branch. Here’s how you can tell what keyspace and branch you are viewing the diagram of:

![Architecture diagram for a PlanetScale database](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-labeled-darkmode.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=194b9dcacfd62cdefa6ab5944cdeb981)

Architecture diagram for a PlanetScale database

![Architecture diagram for a PlanetScale database](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-labeled.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=8662abe9f8b1f8898456f776bbaa63fb)

Architecture diagram for a PlanetScale database

### Production branches

Production branches are designed for production workloads, and as such are given enough resources to ensure high availability. By default, every production branch has a single primary MySQL instance and two replicas. Each primary also comes with 3 [VTGates](terminology.md#vtgate) across 3 availability zones, which act as proxies for your MySQL instances. These are all pictured in the diagram for a production branch:

![Production branch architecture](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-production-branch-darkmode.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=42aa5525aefda5db6cf8a7cf5bd1e477)

Production branch architecture

![Production branch architecture](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-production-branch.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=e509aa72486309af8fa8a27ac0fa5b81)

Production branch architecture

Generally, the application connecting to this database need not be aware of these various components. One exception to this is if you are specifically trying to [send queries to a replica](scaling/replicas.md#how-to-query-replicas).

### Development branches

Development branches are specced to enable the development and testing of new features and are not designed for production workloads. When a new development branch is created, a single MySQL node is created along with a VTGate that handles connections to that node. This is reflected in the diagram of a development branch.

![Development branch architecture](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-dev-branch-darkmode.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=829c3fcd2bb8262d00d9ab25118a59f0)

Development branch architecture

![Development branch architecture](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-dev-branch.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=3bab99a2ff2ad7339b468a0f1a4946b9)

Development branch architecture

When you promote a development branch to production status, PlanetScale automatically adds additional replicas and VTGates deployed across multiple availability zones in a given region.

### Read-only regions

The primary of your database is the only node that can accept writes, and it resides in a single region. You can add [read-only regions](scaling/read-only-regions.md) to each keyspace on a production branch, which adds replicas in another region that can be used to serve read traffic. This can help reduce read latency for application servers that are distributed around the world. Each read-only region’s cluster size and number of replicas are configured per keyspace on the [Clusters page](cluster-configuration.md).

Below, you can see our database has the primary and two replicas in `us-east-2` with read-only replicas added in both `us-west-2` and `eu-central-1`.

![Production branch with read-only regions architecture](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/read-only-regions-darkmode.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=2b3eb19fb140a7650b0c41aeb9f5b7bf)

Production branch with read-only regions architecture

![Production branch with read-only regions architecture](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/read-only-regions.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=0d3c3da8878dd732f80659e94d16cfa2)

Production branch with read-only regions architecture

The read-only replicas can be identified by the blue globe icon.

## Infrastructure metrics

Each element within the infrastructure diagram for PlanetScale database branches can be selected to display additional metrics related to that element. These metrics are displayed in expandable cards that present themselves when an element is selected. By default, the cards display metrics from the last 6 hours but can be adjusted if additional data is needed.

### VTGates

The VTGate node displays the total number of VTGates that exist for a given branch, as well as the number of availability zones in which they live. Selecting the VTGates node will show the following metrics:

- Number of connections.
- Latency.
- Queries received.
- CPU.
- Memory consumption.

![VTGate metrics](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/vtgate-metrics-darkmode.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=7bb7ce6b5fbf7191a967bf57a3d73bed)

VTGate metrics

![VTGate metrics](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/vtgate-metrics.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=46e4b1efa1c0bc6c3e3fab96b73723c5)

VTGate metrics

### MySQL nodes

Each MySQL node in the diagram will display whether it is the primary node or a replica, along with the region where that node is deployed to. Clicking any of the MySQL nodes will display the following metrics:

- Database reads and writes for that node.
- Queries served.
- IOPS.
- CPU and Memory utilization.
- Storage utilization over the past week.

![Primary MySQL node metrics](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/vttablet-metrics-darkmode.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=abbbbb62735f26f7ab26eeff6cfeb2ed)

Primary MySQL node metrics

![Primary MySQL node metrics](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/vttablet-metrics.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=2716b18e40ced5f90715791225aaa967)

Primary MySQL node metrics

Selecting a replica will display the replication lag in addition to the other metrics.

![Replication lag diagram](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/replication-lag-diagram-darkmode.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=29aac8e716e32395d06147129e65c55c)

Replication lag diagram

![Replication lag diagram](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/replication-lag-diagram.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=027ca93a9c049a42f57bde25581fb7d1)

Replication lag diagram

### Replication lag at a glance

Within the infrastructure diagram, you’ll also notice that there is a number near the connection points for each replica. These numbers are a way to read the replication lag between the Primary node and that given node at a glance.

![Replication lag](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/replication-lag-darkmode.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=13676aab50e28a858898bbf0e496a389)

Replication lag

![Replication lag](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/replication-lag.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=cd8702cc1fdfd4908e3cc89f28e9a358)

Replication lag

### Database shards

If your database is [sharded](sharding.md), the infrastructure diagram will represent that as a green stack of shards.

![Stacked shards](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/shard-stack-darkmode.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=e74f9dcd2ce3f0f9d51f42d368e71553)

Stacked shards

![Stacked shards](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/shard-stack.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=9f0962f796bec2786eb049172b949e88)

Stacked shards

Selecting the stack from the diagram will open a card displaying all of the shards belonging to that keyspace.

![Shard list](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/shard-list-darkmode.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=bbc4baf3bc7c33fa822500ca4b29e3ec)

Shard list

![Shard list](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/shard-list.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=1e5c0d05fb61d6e2a8f35f72c1ac5f95)

Shard list

After selecting a shard, you’ll be able to choose to look at metrics for either that shard’s primary or one of its replicas.

![Shard list](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/shard-primary-replicas-list-darkmode.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=03506ee109c02bacef7f6c61d35a785d)

Shard list

![Shard list](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/shard-primary-replicas-list.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=0b867333638f9cdcc79c3a8bb49f82d7)

Shard list

Selecting one will show you the metrics for that specific node in your database architecture.

![Shard](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/shard-primary-metrics-darkmode.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=96fb149e8943297693b641d5541c2255)

### Resizing

You can use the [Clusters page](cluster-configuration.md) menu to resize your keyspaces. When a resize is in progress, this will be indicated at the top of the diagram.

![Architecture diagram with resize indicator](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-resize-darkmode.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=fb5674261efa1740fea11deb41de8987)

Architecture diagram with resize indicator

![Architecture diagram with resize indicator](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-resize.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=428765881308f6db991ece2f3ceb8245)

Architecture diagram with resize indicator

Click on “ **View** ” to see the status for each shard being resized:

![Per-shard resize status](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/resize-progress-darkmode.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=59825ddf26f57208010d2382d2cf9cd0)

Per-shard resize status

![Per-shard resize status](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/resize-progress.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=277e6b578e55789f7f59b54ab954e9d0)

Per-shard resize status

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
