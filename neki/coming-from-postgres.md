---
url: https://planetscale.com/docs/neki/coming-from-postgres
title: "Coming From Postgres"
description: ""
access_date: 2026-09-10T17:00:58.777Z
current_date: 2026-09-10T17:00:58.777Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Neki vs PlanetScale Postgres

> The upgrade path from regular Postgres to Neki

PlanetScale lets you create Neki and regular Postgres databases.
Both products lead to great performance, and have a full slew of common features like Insights, metrics, automated backups, a hosted MCP server, and much more.

Since both are ways of getting a great Postgres database in the cloud, how do you know which to choose when?
This article provides guidance.

## What stays the same

Both products include:

* Insights, metrics, anomalies, and schema recommendations
* Automated backups, backup schedules, and point-in-time restore
* CLI, API, and MCP support
* Standard Postgres clients, drivers, and ORMs
* Roles and credentials
* Production branches with high availability

## Connections

Applications connect in the same way for Postgres and Neki.
Ultimately, both are powered by real Postgres nodes.

In PlanetScale Postgres, connections from clients either are made as [direct connections](../postgres/connecting.md) or [through PgBouncer](../postgres/connecting/pgbouncer.md).
Either way, these are normal postgres connections, and work with all postgres clients, drivers, and ORMs.

All client connections to a Neki database are made to [Neki routers](overview.md#routers).
These are also just regular connections, with the same authentication and communication protocol as Postgres.
Neki routers are not just a pooling proxy like PgBouncer.
Rather, each router contains a full Postgres query parser, planner, buffering capabilities, and is tied in with Neki's health-monitoring system.
These then route query requests along to the Postgres node(s) that are needed to respond to the query.

In most cases, connections are more scalable in Neki compared to regular Postgres.

Making direct connections to Postgres is notoriously difficult to scale, due to Postgres creating a new full unix process per connection.
Using PgBouncer helps this significantly, but there can still be some performance and latency issues using PgBouncer.

Neki routers allow for much safer connection scalability.
Not only do they use more sophisticated connection pooling than PgBouncer, but they also have the advantage of connecting to [sidecars](overview.md#what-runs-alongside-postgres) next to each Postgres instance.
Neki also makes it easy to scale the number, size, and grouping of these routers, giving you more precise control over how to handle incoming traffic.

## Scale

Whereas Neki has full capabilities for massive data sharding, PlanetScale Postgres does not.

On PlanetScale Postgres, each database cluster has a single primary.
These primaries can get very large, up to 96 vCPUs and over 700 gigabytes of RAM.
However, for large databases this can still become a bottleneck.
PlanetScale Postgres also allows you to create many read replicas, both in the same region as your primary and in other regions.
However, this only allows for read scalability, and does not help scale data size, only query processing capabilities.

On the other hand, Neki allows for near-unlimited scalability for reads, writes, and data.
With [sharding](when-to-shard.md), Neki is capable of spreading out the data from a single logical database onto many distinct shards, each with their own primary and replicas.
For example, we may have a large, 100 terabyte database, which we spread out across 64 shards, each storing approximately 1.5 terabytes.
This allows us to scale data, but also distribute queries across many more nodes.
Neki has been proven to scale to 100 million queries per second and over a petabyte of data.

Regular Postgres can scale to tens of terabytes and hundreds of thousands of queries per second.
However, the best scalability is on Neki.

It's also good to note that Neki is also a great option for smaller, unsharded databases.
You get all the other features like online DDL, zero-downtime operations, connection pooling, and version upgrades, even at smaller scales.

## Availability

We maintain [high standards for operational excellence](../postgres/operations-philosophy.md) for our Postgres databases.
Due to this, we have industry-leading uptime and availability for Postgres.

However, there are some fundamental limitations of Postgres and PgBouncer that lead to availability imperfections.

When a switchover occurs, whether due to a node failure or an upgrade cycle, direct connections to PlanetScale Postgres databases must be severed.
PgBouncer improves the situation, however its ability to buffer queries and maintain HA during failovers is limited.
The nature of running raw Postgres means we must take small windows of downtime during version upgrades, primary server failure, and other events.

Neki provides much better guarantees.
Because the query path is client → Router → Sidecar → Postgres, we have greater control over keeping the system fully available during switchovers and failovers.
Router failure is also easily handled, since we can deploy any number of them we need to have reserve capacity to unexpected node failures.

When running Neki in sharded mode, there is the additional benefit of the shared-nothing architecture.
If we have 64 shards, a failure of a database primary only means 1/64th of our data becomes impacted during the switchover to replace this node.

Neki allows us to operate Postgres with true high-availability, and most maintenance operations complete with zero downtime.

## Backups

We have [written extensively](https://planetscale.com/blog/massively-parallel-postgres-backups) about the benefits sharding provides for backups.
When your data is sharded, Neki allows for extremely fast backups, since each shard can perform backup work in parallel.

PlanetScale Postgres also has automated backups, custom backup schedules, and point-in-time recovery.
However, large databases can lead to long backups, since all data is managed by the primary.

If backup time is a pain in your current OLTP database, Neki and sharding is a great solution to improve performance.

## Summary

### What features stay familiar

* Applications connect with standard Postgres clients and TLS.
* Insights, Anomalies, Schema Recommendations
* Full CLI, API, and MCP support
* Automated backups, backup schedules, point-in-time restore
* Choose between network-backed and local NVMe storage
* Roles grant permissions.
* Production branches are highly available.

### What is different

Familiar conventions from PlanetScale Postgres take a different path in Neki.

| PlanetScale Postgres                                                                    | In Neki                                                                                                                                |
| --------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| Connect to Postgres, or to PgBouncer on port `6432`                                     | Connect to a [router](connecting.md) on port `5432`                                                                                 |
| Specify connection type by appending a replica name or PgBouncer suffix to the username | Set [`__neki.target`](connecting.md#primary-and-replica-routing) or target a connection at a router group                           |
| One primary owns every row                                                              | The [data topology](data-topology.md) assigns each table to a shard group                                                           |
| Adding replicas or vertical size adds capacity for the whole database                   | Adding a shard adds capacity only after topology and a [data-migration workflow](data-migration.md) place data there                |
| A statement runs on one Postgres server                                                 | A statement can reach one shard, several shards, or every shard in a table's group                                                     |
| Transactions execute on a single node                                                   | [Cross-shard work](query-planning.md#transactions-across-shards) does not share a snapshot or atomic commit; single-shard work does |
| `ALTER TABLE` runs on the one database                                                  | DDL through a router [fans out](schema-changes.md) to every managed shard                                                           |
| Size one Postgres cluster (and optional PgBouncers)                                     | Size [shards, routers, and Admin](cluster-configuration.md) separately                                                              |

### Send replica reads with a Neki target

PlanetScale Postgres routes replica traffic by appending `|replica` to the username, or by using a dedicated replica PgBouncer.

Neki does not use that username form. Select **Route queries to a replica** on the **Connect** page, or set the startup option `-c __neki.target=REPLICA`. You can also run `SET __neki.target = 'replica'` before a transaction.

Replica connections are read-only. Writes fail instead of going to a primary. If no eligible replica is available for a destination shard, the query fails instead of falling back to that shard's primary.

See [Primary and replica routing](connecting.md#primary-and-replica-routing) and [Replicas](replicas.md).

### Placement is in the topology

On PlanetScale Postgres, connecting to the database is enough. Every table's rows live on that one primary.

On Neki, once you begin to shard data, the [data topology](data-topology.md) decides where rows go.

A table belongs to one shard group, either by an explicit binding or by inheriting a schema, database, or topology default. An unsharded group routes every row of those tables to one physical shard. A sharded group uses a shard key to split rows across shards.

Creating a shard provisions Postgres capacity. It does not move tables or rows onto that shard. [Reference tables](reference-tables-and-gsis.md) are the exception that stores the same rows on every shard in the bound groups.

### Query planning and routing

On PlanetScale Postgres, every query runs on the one cluster.

On Neki, a shard key determines how rows of sharded tables are assigned to specific shards using the [data topology](data-topology.md). For example, tables that share a `tenant_id` key can keep that tenant's rows on the same shard.

That key determines how a Router routes a query. If the query is looking up rows by a single `tenant_id`, the router can send the statement to one shard. Without it, the router sends the same statement to multiple or all shards and combines the response.

Use `EXPLAIN` and [Query Insights](monitoring/query-insights.md) to see how many shards a statement reaches. See [Query planning](query-planning.md) and [When to shard](when-to-shard.md).

### Transactions and snapshots

On PlanetScale Postgres, a transaction is one Postgres transaction: one snapshot, one commit.

On Neki that is still true for work that stays on a single shard. Once a transaction spans more than one shard, those shards do not share a snapshot or an atomic commit. See [Transactions across shards](query-planning.md#transactions-across-shards) for routing modes and how to keep a transaction on one shard.

### Schema changes

Native DDL issued through a router runs on every managed shard immediately.
After it commits, wait with `__neki.wait_for_ddl` before sending dependent SQL through other router instances.

Managed [schema-change workflows](schema-changes.md) add readiness checks and an explicit completion step.
Online DDL builds a shadow table and uses the same copy-and-stream process as other Replicator workflows.

### Branches and clusters

A Neki branch is still an isolated environment: its own routers, Postgres instances, data, configuration, backups, and billing.

An ordinary development branch starts empty.
It does not copy the parent branch's schema or data.
Just like on PlanetScale Postgres, restore a [backup](backups.md) to create a new branch with existing data.

On PlanetScale Postgres, configuration such as parameters affects the single cluster.
On Neki, shards, routers and the admin service can all be individually resourced and configured.

* A [configuration profile](cluster-configuration.md#configuration-profiles) sets Postgres size, replica count, parameters, extensions, and sidecar pools for the shards assigned to it.
* [Router groups](cluster-configuration.md) accept application connections and have their own size.
* The [admin service](cluster-configuration.md#configure-the-admin-service) health-checks sidecars and coordinates failover. It is not a Postgres role.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
