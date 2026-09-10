---
url: https://planetscale.com/docs/neki/monitoring/metrics
title: "Metrics"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Metrics

> Monitor Neki router traffic, Postgres resource utilization, storage, WAL activity, replication lag, and instance health.

The **Metrics** page shows live and historical performance data for a Neki
database branch. Its three tabs separate metrics for **Shards**, **Storage**,
and **Routers**.

To open the page, select a database and branch in the PlanetScale dashboard,
then select **Metrics**.

## Filter metrics

All three tabs provide a branch selector, time range, **Live** toggle,
**Refresh** button, and controls to expand or collapse every graph. Live mode
is enabled by default and refreshes the graphs approximately every 30 seconds.
The default time range is the previous 12 hours. The custom date picker is
limited to the previous seven days.

The remaining filters depend on the selected tab:

| Tab         | Filters                                                                |
| :---------- | :--------------------------------------------------------------------- |
| **Shards**  | **Shard configuration profile** (`All profiles` or one named profile). |
| **Storage** | **Shard configuration profile** (`All profiles` or one named profile). |
| **Routers** | **Router** (`All routers` or one named router group).                  |

Opening **Metrics** from a shard or Postgres instance on the database overview
highlights that shard or server in the graphs. The Metrics page does not have
separate shard or server filter menus.

For ranges longer than 15 minutes, click and drag across a graph to narrow the
selected time range. Moving the pointer across one graph highlights the same
timestamp on the other graphs. The menu on an individual graph can save that
graph as an image.

## Shard metrics

The **Shards** tab covers Postgres resource utilization, database activity,
replication lag, and instance health. CPU, memory, IOPS, connections, locks, and
pod status each have separate primary and replica graphs so you can compare their
behavior.

| Graph                                | What it shows                                                   |
| :----------------------------------- | :-------------------------------------------------------------- |
| **Primary CPU utilization**          | CPU utilization for shard primaries.                            |
| **Replica CPU utilization**          | CPU utilization for shard replicas.                             |
| **Replication lag**                  | The time by which each replica trails its shard primary.        |
| **Primary memory utilization**       | Percentage of available memory in use by primaries.             |
| **Replica memory utilization**       | Percentage of available memory in use by replicas.              |
| **Primary IOPS**                     | Storage input/output operations per second for primaries.       |
| **Replica IOPS**                     | Storage input/output operations per second for replicas.        |
| **Primary connections**              | Primary connections grouped by Postgres connection state.       |
| **Replica connections**              | Replica connections grouped by Postgres connection state.       |
| **Transaction rate**                 | Committed transactions per second, grouped by shard.            |
| **Transaction rate by database**     | Committed transactions per second, grouped by logical database. |
| **Primary locks**                    | Locks on primaries grouped by lock mode.                        |
| **Replica locks**                    | Locks on replicas grouped by lock mode.                         |
| **Container out-of-memory restarts** | Containers restarted after exhausting available memory.         |
| **Container restarts**               | Container restarts grouped by reason.                           |
| **Primary pod status**               | The lifecycle state reported by each selected primary.          |
| **Replica pod status**               | The lifecycle state reported by each selected replica.          |
| **Container waiting reasons**        | Reasons that a container is waiting to start or resume.         |

The main graphs summarize the selected instances. Expanding a graph shows its
larger chart and, where available, its per-shard or per-instance breakdown. The
expanded CPU graphs also show memory utilization for the same instance, and the
expanded memory graphs break memory down into mapped, resident, active cache, and
inactive cache bytes.

Increasing replication lag means replica reads may return older data. If no
replica satisfies the router's configured health and lag requirements, a query
targeted to replicas fails instead of running on a primary. See [Choosing where
reads run](../query-planning.md#choosing-where-reads-run).

When an out-of-memory event occurs in the selected period, the page also shows
a warning banner. Correlate the event with memory, connections, and query
activity before changing the configuration-profile size.

## Storage metrics

The **Storage** tab separates primary and replica disk use and reports
write-ahead log (WAL) archiving health.

| Graph                        | What it shows                                  |
| :--------------------------- | :--------------------------------------------- |
| **Primary disk usage**       | Percentage of the primary volume in use.       |
| **Replica disk usage**       | Percentage of replica volumes in use.          |
| **Primary storage usage**    | Storage consumed by primaries in bytes.        |
| **Replica storage usage**    | Storage consumed by replicas in bytes.         |
| **WAL storage**              | Storage currently occupied by WAL.             |
| **WAL archive success rate** | Successful WAL archive operations per second.  |
| **WAL archive failure rate** | Failed WAL archive operations per second.      |
| **WAL archive age**          | Time since the last successful WAL archive.    |
| **Unarchived WAL**           | WAL waiting to be archived, measured in bytes. |

Expanding either archive-rate graph shows a per-shard breakdown of successful
and failed operations together.

Investigate a rising archive age, failed archive operations, and unarchived WAL
together. Compare them with disk usage and IOPS from the same period.

## Router metrics

Routers plan incoming statements, determine which shards need to participate,
and combine distributed results. The **Routers** tab shows the following
graphs for the selected branch or router group:

| Graph                                | What it shows                                                                                      |
| :----------------------------------- | :------------------------------------------------------------------------------------------------- |
| **Queries per second**               | Query rate grouped by logical database.                                                            |
| **Query latency**                    | Branch-wide p50 and p95 latency, with average, p50, p95, and p99 detail for each logical database. |
| **Query errors per second**          | Error rate grouped by logical database.                                                            |
| **Router utilization**               | CPU and memory utilization, with detail for individual router instances.                           |
| **Container out-of-memory restarts** | Router containers restarted after exhausting available memory.                                     |
| **Container restarts**               | Router container restarts grouped by reason.                                                       |
| **Pod status**                       | The lifecycle state reported by each router instance.                                              |
| **Container waiting reasons**        | Reasons that a router container is waiting to start or resume.                                     |

A query latency increase without a corresponding increase in Postgres resource
utilization can indicate that you should inspect routing behavior, query
fanout, or the work required to combine results.

## Metrics elsewhere in the dashboard

The database overview and cluster-configuration pages use recent metrics to
summarize router memory, primary CPU and memory, replica lag, and branch or
per-shard storage. These summaries provide current infrastructure context. Use
the **Metrics** tabs for historical time-series graphs.

## Interpret metrics in context

There is no single healthy value for every Neki workload. Establish a baseline
for each branch and investigate changes from its normal behavior.

* If one shard has higher CPU, IOPS, or storage use than the others, review its
  routed workload and [data topology](../data-topology.md).
* If router latency rises while one shard is saturated, use [Query
  Insights](query-insights.md) to find queries that reach that
  shard or make many shard calls.
* If replica lag increases, compare replica CPU, IOPS, and connection activity
  on **Shards** with WAL activity on **Storage**.
* If multiple instances approach their resource limits, review [cluster
  sizing](../cluster-configuration/cluster-sizing.md).
* If a graph changes suddenly, use its time range when searching [Logs](logs.md)
  and Query Insights.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
