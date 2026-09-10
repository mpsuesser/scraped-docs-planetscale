---
url: https://planetscale.com/docs/neki/monitoring/anomalies
title: "Anomalies"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Anomalies

> Periods when a high share of queries run slower than the established baseline.

**Platform availability:** [Vitess](../../vitess/monitoring/anomalies.md) and [Postgres](../../postgres/monitoring/anomalies.md)

PlanetScale Insights continuously analyzes query performance to establish a
baseline. When a high enough percentage of queries run more slowly than that
baseline, Insights records an anomaly.

Anomalies are available on every Neki cluster.

## Open Anomalies

From the [PlanetScale dashboard](https://app.planetscale.com), select a Neki
database and branch, then select **Insights** and **Anomalies**.

## Read the Anomalies graph

The graph shows the percentage of queries executing slower than the 97.7th
(2-sigma) percentile baseline on the y-axis and time on the x-axis. The
**expected** line is the share of slow queries that is statistically normal
for a database with uniform performance over time. Small deviations are
normal. Only substantial, sustained deviations are treated as an anomaly.

Periods of unhealthy performance are highlighted. Select an anomaly to open its
detail view, which shows when the period started and ended, how long it lasted,
and the slow-query percentage before, during, and after it.

The detail view then lists signals from the same period:

* **Correlated queries**, with the execution rate of each one.
* Queries per second, rows written per second, rows read per second, and errors
  per second for the branch.
* Shard-primary CPU utilization, memory utilization, and IOPS.
* Backups that were running during the period.

These signals are measured over the anomaly's window; none of them is
identified as the cause. A correlated query ran while the anomaly was open,
which makes it worth investigating, but it can equally be a victim of the
slowdown or unrelated to it.

Compare the same period on [Metrics](metrics.md) before deciding
what to change. A shard-primary CPU or IOPS spike, rising replica lag, or a
router latency increase without matching Postgres load each point at a different
explanation.

## Anomalies vs query latency

A query-latency spike does not always indicate an anomaly, because Insights
compares each query against its own baseline rather than against an absolute
threshold.

For example, a weekly report that always runs a few slow queries raises branch
query latency at the same time each week. Those queries are running at their
expected latency, so Insights does not record an anomaly.

## Investigate an anomaly

Use the anomaly details to decide whether you need to act.

A common case is a new query that runs often, reads many rows, and consumes
enough resources to slow other traffic. Open each correlated query in
[Query Insights](query-insights.md) and check its execution count,
rows read, and shard calls over the anomaly window to establish whether it
is a likely contributor, a victim, or unrelated. If it contributed, consider
adding an index, reducing the rows read, or changing the routing predicate.

After you change a query or schema, return to Anomalies and Query Insights to
confirm the pattern returned to its baseline.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
