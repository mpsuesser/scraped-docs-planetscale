---
url: https://planetscale.com/docs/vitess/monitoring/datadog
title: "Datadog"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

This Datadog integration is no longer receiving updates or new metric additions. You should instead use our [Datadog Agent Integration](../tutorials/prometheus-metrics-datadog.md), which provides more metrics than this native integration.

If you have any questions about migrating to the Datadog Agent Integration, [reach out to support](https://planetscale.com/contact?initial=support).

## Prerequisites

- A [Datadog](https://www.datadoghq.com/) account

## Configuring the Datadog integration

Once complete, a “PlanetScale” dashboard will be available with incoming metrics from PlanetScale.

![PlanetScale Default Dashboard in Datadog](https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/datadog/dashboard.png?w=2500&fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=fda5e21f5e73e8bd3bb8bc30a80caff1)

PlanetScale Default Dashboard in Datadog

## Metrics We Collect

Once configured, PlanetScale collects the following metrics from every branch in your organization.

| **Metric name** | **Metric type** | **Description** |
| --- | --- | --- |
| planetscale.connections | gauge | Number of active connections to a database branch. *Shown as connection.* |
| planetscale.primary.cpu\_usage | gauge | Percentage of CPU utilized on a database branch’s primary. *Shown as percent.* |
| planetscale.primary.memory\_usage | gauge | Percentage of memory utilized on a database branch’s primary. *Shown as percent.* |
| planetscale.queries.latency | gauge | Query times in the p50, p95, p99 and p999 percentiles. *Shown as millisecond.* |
| planetscale.replication\_lag | gauge | Replication lag in seconds between a database branch’s primary and each replica. *Shown as second.* |
| planetscale.rows\_read | count | Number of rows read from a database branch. *Shown as row.* |
| planetscale.rows\_written | count | Number of rows written to a database branch. *Shown as row.* |
| planetscale.tables.cumulative\_query\_time | count | Cumulative active query time in a database branch, by table and statement. *Shown as nanosecond.* |
| planetscale.tables.queries | count | Number of queries issued to a database branch, by table and statement. *Shown as query.* |
| planetscale.tables.rows\_deleted | count | Number of rows deleted from a database branch, by table. *Shown as row.* |
| planetscale.tables.rows\_inserted | count | Number of rows inserted into a database branch, by table. *Shown as row.* |
| planetscale.tables.rows\_selected | count | Number of rows selected in a database branch, by table. *Shown as row.* |
| planetscale.tables.rows\_updated | count | Number of rows updated in a database branch, by table. *Shown as row.* |
| planetscale.tables.storage | gauge | Total bytes stored in a database branch, by table. *Shown as byte.* |
| planetscale.vtgate.errors | count | Number of errors encountered by a database branch’s vtgate. *Shown as error.* |
| planetscale.vttablet.mem\_util.max | gauge | Maximum memory utilization of a database branch’s vttablet. *Shown as percent.* |
| planetscale.vttablet.mem\_util.avg | gauge | Average memory utilization of a database branch’s vttablet. *Shown as percent.* |
| planetscale.vttablet.iops | gauge | Number of IOPS performed by a database branch’s vttablet. *Shown as operation.* |

## Billing

The Datadog integration is available on all of our [paid plans](https://planetscale.com/pricing).

## Frequently asked questions

### How do I track replication lag in Datadog?

You can use the following formula to set alerts for replication lag:

```shellscript
(max:planetscale.replication_lag{ps_database:<DATABASE_NAME> ps_tablet_type:replica, ps_branch:<MAIN>})
```

Make sure you replace `<DATABASE_NAME>` with your PlanetScale database name and `<MAIN>` with the name of the branch for which you’d like to track replication lag.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
