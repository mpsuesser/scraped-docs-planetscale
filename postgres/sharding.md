---
url: https://planetscale.com/docs/postgres/sharding
title: "Sharding"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

Neki is currently in Platform Preview. Platform Preview features are “Beta Features” under the PlanetScale Terms of Service or your applicable agreement with PlanetScale. Accordingly, Neki is subject to the limitations and disclaimers applicable to Beta Features and is not covered by any service level agreement.

[Neki](../neki.md) scales past a single primary while keeping one connection string. A router sits in front of real Postgres so applications keep talking to one database while data lives on one cluster or many.

PlanetScale Postgres already helps with schema recommendations, Query Insights, and faster instances on [Metal](../metal.md). Neki is the upgrade when you need more than one writer, schema changes that do not stall a large table, and backups that run per cluster instead of against one heap.

You do not have to shard on day one. A new Neki database starts as one unsharded cluster and still runs the router, admin service, and sidecar: one connection string, failover, and pooling without a client-facing PgBouncer. [When to shard](../neki/when-to-shard.md) depends on your traffic, access patterns, and data.

## Neki documentation

Architecture, data topology, query planning, and cluster configuration.

## Quickstart

Create a Neki database, connect, and insert data.

## When to shard

Whether a single primary is still the cheaper answer.

## Neki vs PlanetScale Postgres

The upgrade from a single primary, and the habits that change.

## Why sharding matters

**Horizontal sharding** splits a logical table’s rows across multiple database instances, each with its own primary. A router sends each query to the shard or shards that hold the rows. That differs from **vertical scaling**, where you add CPU, memory, or faster disks to one server.

A larger instance eventually hits a ceiling on write throughput and disk, and one failure takes the whole database with it. If read capacity is your bottleneck, replicas help, but you cannot add more write capacity to a single primary.

Sharding is the next step when you need:

- **More than one writer.** Writes spread across shard primaries instead of one WAL.
- **More storage than one instance can hold.** Each shard has its own disk.
- **A smaller blast radius.** A lagged replica, a vacuum, or a restore is a shard problem, not a whole-database problem.
- **Tenant isolation.** A noisy customer can live on its own shard instead of starving the rest of the cluster.

### When horizontal sharding makes sense

**Multi-tenant SaaS** often fits when a few tenants dominate storage or query volume. A tenant key can keep each tenant’s rows together and keep heavy tenants from starving others.

**Very large datasets or I/O-heavy workloads** can exhaust one primary even after you tune queries and storage. Spreading rows across shards adds capacity that a larger instance does not.

**Write-heavy applications** can scale reads with replicas, but every write still hits one primary. Sharding splits those writes.

See [When to shard](../neki/when-to-shard.md) for the full decision, including what partitioning does not solve.

## Tune queries and Metal first

Neki is still in Platform Preview, and PlanetScale Postgres is still the best single-primary Postgres. Use [Query Insights](monitoring/query-insights.md) and [schema recommendations](monitoring/schema-recommendations.md) before you split the database. Many limits are expensive queries or missing indexes, not a need for more primaries.

[Metal](../metal.md) uses locally attached NVMe. Workloads that look like they need sharding are sometimes hitting I/O limits that Metal removes. Tune the workload and the storage class, then move to Neki when that one primary is still the limit.

Neki is the next upgrade. You can import into an unsharded Neki database then add shards and split data later. See [Neki vs PlanetScale Postgres](../neki/coming-from-postgres.md) and [Migrate PostgreSQL to Neki](../neki/imports/postgres.md).

## Frequently asked questions

What is horizontal sharding?

Rows are split across multiple database servers, and a router sends each query using a shard key. See [Why sharding matters](#why-sharding-matters) for how that differs from vertical scaling.

When should I shard my Postgres database?

After query tuning and [Metal](../metal.md) still leave one primary as the limit. See [Tune queries and Metal first](#tune-queries-and-metal-first) and [When to shard](../neki/when-to-shard.md).

Is Neki a fork of Vitess?

No. It is built from scratch for Postgres. See [Neki vs Vitess](../neki/vitess-and-postgres.md).

How does Neki relate to PlanetScale Postgres?

Neki is the upgrade from a single Postgres primary. Each shard is real Postgres. [Neki vs PlanetScale Postgres](../neki/coming-from-postgres.md) covers what you gain and the habits that change.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
