---
url: https://planetscale.com/docs/neki/monitoring
title: "Monitoring"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Monitor a Neki database

> Use metrics, logs, and Query Insights to monitor Neki routers, shards, Postgres instances, and queries.

Monitor Neki routers, the Postgres instances within each shard, and query
patterns across the database.

PlanetScale provides these complementary monitoring tools:

| Tool                                                              | Use it to                                                                                                                     |
| :---------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------- |
| [Metrics](monitoring/metrics.md)                               | Track router traffic and latency, Postgres resource utilization, storage, WAL activity, replication lag, and instance health. |
| [Logs](monitoring/logs.md)                                     | Search individual log events and filter them by shard, server, severity, and time.                                            |
| [Query Insights](monitoring/query-insights.md)                 | Find expensive or frequently executed query patterns and inspect their shard-level cost.                                      |
| [Anomalies](monitoring/anomalies.md)                           | Investigate periods when queries run slower than their established baseline.                                                  |
| [Schema recommendations](monitoring/schema-recommendations.md) | Review automatic DDL suggestions from production query telemetry and schema.                                                  |

## Router monitoring

Applications connect to Neki routers rather than directly to individual shards.
Routers plan each statement, determine which shards need to participate, and
combine results when a query runs on more than one shard.

Router metrics include:

* Queries per second.
* Query latency, including per-database detail.
* Query errors per second.
* Router CPU and memory utilization.
* Container restarts, out-of-memory restarts, and pod status.

Use the **Routers** tab to view all router groups or select one named group.

## Shard and Postgres monitoring

Each Neki shard contains a primary Postgres instance and can contain one or
more replicas. The **Shards** and **Storage** metrics tabs can limit graphs to
one [configuration profile](cluster-configuration.md#configuration-profiles).
The Logs page lets you select shards and then narrow the results to servers
within those shards.

Postgres monitoring includes:

* CPU, memory, IOPS, and storage utilization.
* Connections, transaction rate, and locks.
* WAL storage and archival activity.
* Replication lag between primaries and replicas.
* Out-of-memory events, container restarts, and instance health.

Use shard and server filters together when investigating whether a problem is
isolated to one shard, affects a configuration profile, or appears throughout
the database.

## Query monitoring

Query Insights groups executions into query patterns and shows their behavior
over time. Use it to investigate:

* Query latency and execution count.
* Rows read and rows written.
* Errors and [anomalous performance](monitoring/anomalies.md).
* Query tags.
* Shard calls made by each query.
* Parallel worker activity.
* [Schema recommendations](monitoring/schema-recommendations.md).

For Neki queries, **Qualified table** shows the complete resolved table name,
including its database and schema. For example, if a query connected to
`appdb` refers to `orders` and Postgres resolves it from the `sales` schema,
the qualified table is `appdb.sales.orders`. A query can reference more than
one qualified table. The **Schema** column is a connection grouping rather than
a PostgreSQL schema name; see
[Table and schema names in Neki](monitoring/query-insights.md#table-and-schema-names-in-neki).

A high shard-call count can come from a query that fans out across shards or
from repeated dispatches to the same shard. Review the query plan and the
database's [data topology](data-topology.md) when investigating an
unexpected value.

## Investigate a performance problem

A typical investigation moves between Query Insights, Metrics, and Logs:

1. Start with Query Insights to identify the affected query pattern and time
   range.
2. Check Metrics for changes in router latency, shard resource utilization,
   storage activity, or replication lag during the same period.
3. Search Logs for errors or server events from the affected shards and
   Postgres instances.
4. Compare the results across shards to determine whether the issue is isolated
   or branch-wide.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
