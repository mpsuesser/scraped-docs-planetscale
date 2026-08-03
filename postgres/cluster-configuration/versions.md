---
url: https://planetscale.com/docs/postgres/cluster-configuration/versions
title: "Versions"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

PlanetScale Postgres supports versions 17 and 18 of Postgres.

Specifically, the following versions are currently supported:

- 17.9
- 18.3

New databases will be created using the latest version by default and we recommend sticking to that default, but you can choose an older version if you need to.

## Major version upgrades

PlanetScale doesn’t currently offer in-place upgrades between major versions of Postgres. To upgrade from Postgres 17 to 18, create a new Postgres 18 database and perform an online migration from your existing PlanetScale Postgres 17 database using our [import guides](../imports/postgres-imports.md).

The same import process also applies if you’re migrating from another provider such as RDS, Aurora, or a self-hosted Postgres instance. You can import into any supported major version regardless of where the source database is hosted.

## Benchmarking Postgres 17 vs 18

Our [Benchmarking Postgres 17 vs 18](https://planetscale.com/blog/benchmarking-postgres-17-vs-18) blog post covers sysbench `oltp_read_only` results across multiple EC2 instance types and I/O configurations.

- **Postgres 18’s new `io_method=worker` default delivers the best overall read performance**, outperforming both `io_uring` and the legacy synchronous I/O in most scenarios.
- **Local NVMe disks dramatically outperform network-attached storage** (gp3, io2) regardless of Postgres version or I/O setting.

## What’s new in Postgres 18

Postgres 18 builds on Postgres 17 with new features, changed defaults, and some deprecations. For full details, see the official [Postgres 18 release notes](https://www.postgresql.org/docs/18/release-18.html).

| Area | What Postgres 18 adds |
| --- | --- |
| **I/O architecture** | New asynchronous I/O (AIO) subsystem with `io_method` setting (`worker`, `io_uring`, `sync`); up to 3× faster reads in benchmarks |
| **Vacuum** | Proactive page-freezing during regular vacuums, reducing the need for aggressive vacuum passes |
| **WAL / write throughput** | Per-connection WAL and I/O statistics for finer-grained monitoring |
| **Indexes** | Skip-scan lookups on multicolumn B-tree indexes; parallel GIN index builds; broader `OR` -clause index usage |
| **`pg_upgrade`** | Retains optimizer statistics through upgrades, reducing post-upgrade performance dips; parallel checks via `--jobs`; `--swap` flag |
| **SQL / developer features** | Virtual generated columns (default); `uuidv7()` / `uuidv4()` functions; `OLD` / `NEW` in `RETURNING` for DML statements; temporal `PRIMARY KEY` / `UNIQUE` / `FOREIGN KEY` constraints (`WITHOUT OVERLAPS`, `PERIOD`) |
| **Authentication** | OAuth 2.0 authentication; FIPS mode validation; TLS v1.3 cipher suite allowlisting; SCRAM passthrough for `postgres_fdw` / `dblink`; `md5` auth deprecated |
| **Logical replication** | Write-conflict logging; parallel streaming for subscriptions by default; `pg_createsubscriber --all` for all databases; auto-invalidation of idle replication slots |
| **Observability** | `EXPLAIN ANALYZE` auto-shows buffer access and index-lookup counts; `ANALYZE (VERBOSE)` now reports WAL, CPU, and average read stats |
| **Text processing** | `PG_UNICODE_FAST` collation; `casefold()` function; `LIKE` over nondeterministic collations; full-text search uses cluster default collation provider |
| **Wire protocol** | Protocol version 3.2 (first update since 2003); `libpq` still defaults to 3.0 for backward compatibility |
| **Data checksums** | On by default at `initdb` (off by default in 17) |
