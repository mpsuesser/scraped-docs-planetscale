---
url: https://planetscale.com/docs/postgres-vs-vitess
title: "Postgres Vs Vitess"
description: ""
access_date: 2026-10-22T18:21:49.469Z
current_date: 2026-10-22T18:21:49.469Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Vitess, Neki, and Postgres

> Compare PlanetScale's Vitess, Neki, and Postgres products

PlanetScale offers [**Vitess**](https://planetscale.com/docs/vitess.md) (our MySQL-compatible engine), [**Neki**](https://planetscale.com/docs/neki.md) (sharded Postgres), and [**Postgres**](https://planetscale.com/docs/postgres.md). All three provide PlanetScale's signature branching workflow and enterprise-grade scaling capabilities.

## Feature comparison

| Feature | Vitess | Neki | Postgres |
| - | - | - | - |
| **Branching** | ✅ Schema and data branches | ✅ Isolated branches | ✅ Schema and data branches |
| **Deploy requests** | ✅ Online schema changes | ❌ Not available | ❌ Not available |
| **Horizontal sharding** | ✅ | ✅ | ❌ |
| **Read replicas** | ✅ | ✅ | ✅ |
| **Read-only regions** | ✅ | ❌ | ❌ |
| **Serverless driver** | ✅ | ✅ | ✅ |
| **Connection pooling** | ✅ Built-in | ✅ Built-in | ✅ Built-in (PgBouncer) |
| **Query Insights** | ✅ | ✅ | ✅ |
| **Automatic and custom backups** | ✅ | ✅ | ✅ |
| **PITR** | ❌ | ✅ | ✅ |
| **Multi-region** | ✅ | ✅ | ✅ |
| **SQL compatibility** | Some [MySQL compatibility](https://planetscale.com/docs/vitess/mysql-compatibility.md) limitations | PostgreSQL compatible; some [platform preview limitations](https://planetscale.com/docs/neki/platform-preview-limitations.md) | Fully PostgreSQL compatible |
| **Multiple logical databases per cluster** | ❌ (use [keyspaces](https://planetscale.com/docs/vitess/terminology.md#keyspace) instead) | ✅ Logical databases | ✅ `CREATE DATABASE` |
| **Max cluster size** | Unlimited shards | Unlimited shards | Single cluster |

## Which product should you choose?

For most teams, **choose based on your existing database experience**:

* **Choose Vitess** if you're currently using MySQL or have a large-scale cluster that requires [horizontal sharding](https://planetscale.com/docs/vitess/sharding.md)
* **Choose Neki** when your Postgres workload could benefit from [online schema changes](https://planetscale.com/docs/neki/schema-changes.md) or [horizontal sharding](https://planetscale.com/docs/neki.md)
* **Choose Postgres** if you're currently using PostgreSQL or prefer its feature set

## Scale considerations

[Metal](https://planetscale.com/metal) clusters with over 100 TB of storage handle throughput and latency much better than traditional EBS-backed instances. Check out our [benchmarks](https://planetscale.com/blog/benchmarking-postgres) to see how Metal stacks up against other providers.

When a single cluster is no longer enough, [Vitess](https://planetscale.com/docs/vitess/sharding.md) provides explicit sharding for MySQL-compatible workloads, and [Neki](https://planetscale.com/docs/neki.md) adds horizontal sharding for Postgres. See [Neki vs Vitess](https://planetscale.com/docs/neki/vitess-and-postgres.md) to compare their differences.

If your workload can run on a single shard, [Postgres](https://planetscale.com/docs/postgres.md) is a strong choice. There are also benefits to using [Neki](https://planetscale.com/docs/neki.md) on a single shard, which puts you in a better position if you later need to shard. See [Neki vs PlanetScale Postgres](https://planetscale.com/docs/neki/coming-from-postgres.md).


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
