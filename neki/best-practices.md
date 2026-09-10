---
url: https://planetscale.com/docs/neki/best-practices
title: "Best Practices"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Neki best practices

> Data placement, transaction scope, and workload isolation

## Design data placement before adding shards

For sharded data, choose shard keys from queries and transactions your application relies on. Prefer keys that:

* Distribute data and writes evenly.
* Appear in the predicates of latency-sensitive queries.
* Keep rows that are joined or updated together in the same shard group.
* Remain stable for the lifetime of a row.

Use [reference tables or global secondary indexes (GSIs)](reference-tables-and-gsis.md)
when queries need data that their shard-key route does not provide:

* Reference tables duplicate a small shared dataset across shards so joins to
  sharded data remain local.
* GSIs map another lookup key to the owner row's shard key.

Topology declarations do not bootstrap historical data. Populate and verify
every reference-table copy before binding it. Create a GSI disabled, backfill
and verify its lookup table while following owner changes, and enable it only
at cutover. An enabled but incomplete GSI can return incomplete results without
an error.

`COPY FROM` does not maintain GSI lookup rows for a sharded owner table. Keep
the GSI disabled while copying, then backfill, verify, and enable it through the
same activation process.

Once application data is placed across shards, leave the [authoritative shard group](data-topology.md#authoritative-shard-group) as a separate shard.
Size it for metadata, catalog work, sequence reservations, and remaining unsharded tables.

## Keep transactions on one shard when possible

Neki can route a transaction to more than one shard, but cross-shard transactions do not provide atomic commit across all shards.
Include the shard key in transactional queries and verify the query plan before depending on multi-statement behavior.
See [Transactions across shards](query-planning.md#transactions-across-shards).

## Watch for scatter queries

A query without enough routing information can run on every shard.
Use `EXPLAIN` and [Query Insights](monitoring/query-insights.md) to look for an unexpected number of shard calls, then add a routing predicate or revisit the topology.

## Separate workload classes

Shards do not need to be evenly sized or identically configured.
Assign resources, replicas, parameters, extensions, and more according to the requirements of the data within the shard.

Use additional [router groups](cluster-configuration.md#configure-router-groups) when a workload needs independent router sizing or autoscaling.
Choose the group on the **Connect** page.

Profile, router, and Admin changes are asynchronous. Follow the object's
**Changes** tab and wait for it to finish before submitting a dependent change.
PlanetScale's [orchestration layer](terminology.md#orchestration-layer)
rolls the requested configuration through the affected resources.

## Monitor capacity and recovery

Review [Metrics](monitoring/metrics.md) for CPU, memory, connections, storage, router errors, and replication lag.
On the **Storage** tab, also watch WAL growth and archive failures.
[Anomalies](monitoring/anomalies.md) flags queries that run slower than their baseline.

Schedule an [MCP](../mcp-server.md) agent to review [Query Insights](monitoring/query-insights.md), query errors, and [schema recommendations](monitoring/schema-recommendations.md).
[Self-improving database](../self-improving-database.md) has prompts you can run with Cursor Automations.

Required [backup](backups.md) schedules run automatically.
Choose retention for manual backups, and periodically restore into a disposable branch.
A successful backup verifies that data was captured; a restore test verifies the recovery workflow your application depends on.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
