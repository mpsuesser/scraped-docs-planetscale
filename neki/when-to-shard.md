---
url: https://planetscale.com/docs/neki/when-to-shard
title: "When To Shard"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# When to shard

> Whether a Postgres workload needs horizontal sharding

Horizontal sharding distributes a logical table's rows across Postgres shards, each with its own primary and replicas.
Sharding is recommended when one primary remains a bottleneck, even after you tune Postgres configuration, query performance, indexes, and storage.
Sharding allows you to spread data across multiple primary Postgres servers, alleviating both storage pressure and read and write throughput limitations of single-primary Postgres setups.

## What sharding changes

|                  | Scale up                            | Shard horizontally                                                              |
| ---------------- | ----------------------------------- | ------------------------------------------------------------------------------- |
| Unit of capacity | One primary                         | Many distinct primaries working together                                        |
| Write path       | Every write hits a single primary   | Writes are spread across shard primaries                                        |
| Row placement    | One primary stores every row        | The data topology assigns each row to a shard                                   |
| Query path       | Postgres executes the query locally | Clients send queries to routers, which then route them to the appropriate shard |

Each shard can be configured to have a different number of replicas.
Replicas can provide failover capacity in a multi-node profile and can offload read traffic from the primary. A shard configured with no replicas is not highly available.

## Partitioning vs sharding

[Postgres declarative partitioning](https://www.postgresql.org/docs/current/ddl-partitioning.html) splits a logical table into physical tables.
In a typical Postgres deployment, it can improve performance and make data pruning cheaper.
However, it does not remove a single-primary write bottleneck or let data exceed the capacity of one Postgres instance.

Neki distributes a logical table across independent Postgres shards, each with its own primary, storage, and replication state.

## When sharding is the correct choice

Sharding becomes a good next step for scaling when one or more of these are true:

* The primary is persistently limited by write throughput or disk IOPS.
* The working set of your relational data no longer fits within the RAM available on a single instance.
* Independent shards would reduce the amount of data affected by one primary failure.
* The workload has a stable routing key that keeps related data and common queries on one shard.

A tenant key (such as a `customer_id` or `company_id`) often keeps each tenant's rows together in the query workload.

## Evaluate single-shard changes first

Before sharding, evaluate whether configuration tuning, schema changes, query optimization, or a larger cluster size can remove the bottleneck.

[Query Insights](monitoring/query-insights.md) identifies expensive and frequently run queries.
It can also surface [schema recommendations](monitoring/schema-recommendations.md).
These tools can help identify improvements for an existing Postgres database.

In addition, [Metal](../metal.md) uses locally attached NVMe and can provide higher I/O throughput and lower latency than network-attached storage.

## Design considerations

Taking a database from unsharded to sharded requires an up-front decision about how to distribute the rows of logical tables.
For predictable performance, design the topology so most queries reach one shard instead of requiring several shards to fulfill each query.

The [data topology](data-topology.md) controls which tables are distributed across which shards and how their rows are divided.
Before moving data, validate the topology against the schema and the application's common access patterns.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
