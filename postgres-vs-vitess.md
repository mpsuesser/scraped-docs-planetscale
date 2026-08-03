---
url: https://planetscale.com/docs/postgres-vs-vitess
title: "Postgres Vs Vitess"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Postgres vs Vitess

> Compare PlanetScale's Postgres and Vitess products to choose the best fit for your application

PlanetScale offers two powerful database products: **Postgres** (our PostgreSQL engine) and **Vitess** (our MySQL-compatible engine). Both provide PlanetScale's signature branching workflow and enterprise-grade scaling capabilities.

## Feature comparison

| Feature                                    | Postgres                                    | Vitess                                                              |
| ------------------------------------------ | ------------------------------------------- | ------------------------------------------------------------------- |
| **Branching**                              | ✅ Schema and data branches                  | ✅ Schema and data branches                                          |
| **Deploy requests**                        | ❌ Not available                             | ✅ Online schema changes                                             |
| **Horizontal sharding**                    | 🔜 ([Neki](postgres/sharding.md) coming soon) | ✅ Explicit sharding                                                 |
| **Read replicas**                          | ✅                                           | ✅                                                                   |
| **Read-only regions**                      | ❌                                           | ✅                                                                   |
| **Serverless driver**                      | ✅                                           | ✅                                                                   |
| **Connection pooling**                     | ✅ Built-in (PgBouncer)                      | ✅ Built-in                                                          |
| **Query Insights**                         | ✅                                           | ✅                                                                   |
| **Automatic and custom backups**           | ✅                                           | ✅                                                                   |
| **PITR**                                   | ✅                                           | ❌                                                                   |
| **Multi-region**                           | ✅                                           | ✅                                                                   |
| **SQL compatibility**                      | Fully PostgreSQL compatible                 | Some [MySQL compatibility](vitess/mysql-compatibility.md) limitations |
| **Multiple logical databases per cluster** | ✅ `CREATE DATABASE`                         | ❌ (use [keyspaces](vitess/terminology.md#keyspace) instead)           |
| **Max cluster size**                       | Single cluster                              | Unlimited shards                                                    |

## Which product should you choose?

For most teams, **choose based on your existing database experience**:

* **Choose Postgres** if you're currently using PostgreSQL or prefer its feature set
* **Choose Vitess** if you're currently using MySQL or have a large-scale cluster that requires [near-term horizontal sharding](https://planetscale.com/blog/how-to-scale-your-database-and-when-to-shard-mysql)

## Scale considerations

If you're operating at **massive scale** (multi-TB datasets, tens to hundreds of thousands of QPS), Vitess is currently the best option. We've successfully migrated several large PostgreSQL workloads to Vitess for customers who needed unlimited horizontal scaling.

However, if you can manage on a single PostgreSQL cluster for the foreseeable future, **staying on Postgres** might be the right choice. We're developing [Neki](https://planetscale.com/blog/announcing-neki), our sharded PostgreSQL solution, which will bring Vitess-style horizontal sharding to PostgreSQL.

We offer [Metal](https://planetscale.com/metal) clusters with over 100 TB of storage that handle throughput and latency much better than traditional EBS-backed instances. Check out our [benchmarks](https://planetscale.com/blog/benchmarking-postgres) to see how Metal stacks up against other providers.
