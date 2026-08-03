---
url: https://planetscale.com/docs/postgres/imports/postgres-imports
title: "Postgres Imports"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

Use this guide if you are importing from platforms like Aurora Postgres, RDS Postgres, Neon, Supabase, and other Postgres instances.

Before beginning your migration, we recommend running our [migration assessment tool](https://planetscale.com/liftoff) for instant feedback on migration complexity, potential blockers, and the recommended migration path.

If you have IP restrictions in place on your source database and need to grant a set of IP addresses access, see our [Import public IP addresses documentation](import-ips.md).

## Migration Options Overview

PlanetScale Postgres provides three primary migration approaches to suit different business requirements, database sizes, and downtime tolerances:

## Migrate using pg\_dump & pg\_restore

## Migrate using WAL streaming

## Migrate using pgcopydb

You can also utilize our [migration scripts](https://github.com/planetscale/migration-scripts/tree/main/postgres-direct) directly if you prefer. These scripts can be used to migrate straight from any Postgres source that supports logical replication into PlanetScale Postgres.

### 1\. pg\_dump & pg\_restore

The [pg\_dump](https://www.postgresql.org/docs/current/app-pgdump.html) method is the simplest approach for migrating smaller PostgreSQL databases. This method involves creating a full backup of your source database using PostgreSQL’s built-in `pg_dump` utility and then restoring it to your PlanetScale Postgres database.

**How it works:**

- Export your entire database schema and data using [pg\_dump](https://www.postgresql.org/docs/current/app-pgdump.html)
- Transfer the dump file to PlanetScale Postgres
- Restore the database using [pg\_restore](https://www.postgresql.org/docs/current/app-pgrestore.html) or [psql](https://www.postgresql.org/docs/current/app-psql.html)

This approach is straightforward and doesn’t require additional infrastructure, making it ideal for databases that can tolerate some downtime during the migration process.

### 2\. WAL Log Replication

[Write-Ahead Logging (WAL)](https://www.postgresql.org/docs/current/wal-intro.html) replication provides a near-zero downtime migration by continuously streaming transaction logs from your source PostgreSQL database to PlanetScale Postgres.

**How it works:**

- Set up [logical replication](https://www.postgresql.org/docs/current/logical-replication.html) between your source database and PlanetScale Postgres
- Stream [WAL logs](https://www.postgresql.org/docs/current/wal-intro.html) in real-time to keep the target database synchronized
- Perform a quick cutover when ready to switch to the new database

This method is ideal for production databases that require minimal downtime and need to maintain data consistency during the migration process.

### 3\. pgcopydb

[pgcopydb](postgres-migrate-pgcopydb.md) automates the `pg_dump | pg_restore` pipeline by copying table data in parallel without intermediate files and building indexes concurrently. PlanetScale maintains a fork with PostgreSQL 17 and 18 support, improved filtering, resilient retry, and improved CDC support. PlanetScale also provides [helper scripts](https://github.com/planetscale/migration-scripts/tree/main/pgcopydb-helpers) for setup, monitoring, cutover, and cleanup.

**How it works:**

- Copy schema from source using `pg_dump`
- Copy table data in parallel using `COPY` with configurable concurrency
- Build indexes and constraints concurrently on the target
- Optionally stream changes via [logical decoding](https://www.postgresql.org/docs/current/logicaldecoding.html) for near-zero downtime CDC
- Perform cutover when replication is caught up

pgcopydb is the recommended approach for large databases requiring high-throughput parallel copying, near-zero downtime, and fine-grained control over the migration process. PlanetScale has used pgcopydb and these helper scripts to migrate multi-terabyte databases from live customer environments, with speeds as fast as 2 TB per hour using [Metal](../../metal.md).

## Migration Method Comparison

| Feature | pg\_dump & pg\_restore | WAL Log Replication | pgcopydb |
| --- | --- | --- | --- |
| **Best For** | Small databases | Production databases requiring minimal downtime | Large databases requiring fast parallel migration |
| **Data Size** | <20GB | <150GB | \>=150GB |
| **Downtime** | High (up to an hour) | Minimal (minutes) | Minimal (minutes) |
| **Setup Complexity** | Low | Medium | Medium |
| **Infrastructure Requirements** | None (built-in tools) | Source DB configuration changes | Migration instance with network access |
| **Data Consistency** | [Point-in-time snapshot](https://www.postgresql.org/docs/current/backup-dump.html) | Real-time sync | Real-time sync via CDC |
| **Cost** | Free (built-in tools) | Low (minimal resources) | Low (migration instance only) |
| **Schema Changes During Migration** | Not supported | Limited support | Not supported |
| **Data Transformation** | None | Limited | None |
| **Error Handling** | Manual intervention required | Basic retry mechanisms | Resumable with retry and exponential backoff |
| **Rollback Options** | Manual restore from backup | Stop replication, switch back | Stop CDC, switch back |

## Migration Considerations

Before migrating your PostgreSQL database to PlanetScale Postgres, there are several important factors to consider to ensure a smooth migration process.

### PostgreSQL Version Compatibility

PlanetScale Postgres supports [PostgreSQL 18](https://www.postgresql.org/docs/18/index.html) and [PostgreSQL 17](https://www.postgresql.org/docs/17/index.html). If your source database is running an older version of PostgreSQL, you should verify compatibility and consider upgrading your source database before migration, or plan for potential compatibility issues during the migration process.

For major version upgrades, use [pgcopydb](postgres-migrate-pgcopydb.md). Only pgcopydb supports migrations across PostgreSQL major versions; pg\_dump & pg\_restore and WAL log replication are intended for same-major-version imports. PlanetScale has successfully migrated companies from PostgreSQL 13 and newer to PostgreSQL 18 with pgcopydb.

**Version considerations:**

- **PostgreSQL 18**: Fully supported
- **PostgreSQL 17**: Fully supported
- **Earlier versions**: May require additional testing and validation
- **Version-specific features**: Newer features may not be available in older versions

For detailed information about PostgreSQL version differences, refer to the [PostgreSQL 18 release notes](https://www.postgresql.org/docs/18/release.html) and [PostgreSQL 17 release notes](https://www.postgresql.org/docs/17/release.html).

### Upgrading from PostgreSQL 17 to 18 on PlanetScale

We don’t currently offer an automated in-place major version upgrade from PostgreSQL 17 to 18.

You can perform an online upgrade by migrating from your existing PlanetScale Postgres 17 database to a new PostgreSQL 18 database using pgcopydb:

- For major version upgrades: follow the [pgcopydb guide](postgres-migrate-pgcopydb.md)

At a high level, the process is:

1. Create a new PostgreSQL 18 database (same region and similar configuration).
2. Use pgcopydb to sync data from your PostgreSQL 17 database.
3. Validate data and application behavior, then update your application connection string to the new database.
4. Decommission the old PostgreSQL 17 database when you’re ready.

### Extension Support

Many PostgreSQL databases rely on extensions to provide additional functionality, but not all extensions are supported by PlanetScale Postgres. See the list of [supported extensions](../extensions.md).

**Important notes about extensions:**

- Review your current database’s installed extensions using `\dx` in psql or by querying `pg_extension`
- Identify which extensions are critical to your application’s functionality
- Plan for alternative approaches if critical extensions are not supported
- Test your application thoroughly in a staging environment before migrating production data

Before migrating, compare your source extension versions with [PlanetScale’s supported versions](../extensions.md) and review the extension’s changelog for any breaking changes.

Common extensions to verify:

- **[PostGIS](https://postgis.net/)** — Geospatial functions and data types may change between versions
- **[Full-text search extensions](https://www.postgresql.org/docs/current/textsearch.html)** (`pg_trgm`, `unaccent`, `dict_int`) — Dictionary and tokenization behavior can vary between versions
- **[UUID extensions](https://www.postgresql.org/docs/current/uuid-ossp.html)** — During replication, UUID generation can cause value misalignment if source and target use different generation methods
- **[pg\_stat\_statements](https://www.postgresql.org/docs/current/pgstatstatements.html)**

### Third-Party Enhancements and Tools

PlanetScale Postgres does **not support third-party enhancements** to PostgreSQL’s core capabilities. This includes:

**Currently unsupported:**

- Custom background workers
- Third-party connection poolers (like [PgBouncer](https://www.pgbouncer.org/))
- External procedural languages beyond the standard ones
- Third-party monitoring tools that require database-level access
- Custom shared libraries or plugins

PlanetScale Postgres includes connection pooling by default.

**Alternatives to consider:**

- Migrate custom functions to standard PostgreSQL syntax where possible.
- Utilize [Metrics](../monitoring/metrics.md), [Insights](../monitoring/query-insights.md), and 3rd party integrations for monitoring.

### Pre-Migration Checklist

Before starting your migration:

For the most up-to-date information on supported features and extensions, refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/current/) and PlanetScale Postgres release notes.

## Get Started

Follow the migration guide that’s right for you:

## Migrate using pg\_dump & pg\_restore

## Migrate using WAL streaming

## Migrate using pgcopydb

## Migrate from Heroku

Heroku does not support logical replication, so the WAL streaming approach above won’t work for Heroku Postgres. Use the dedicated [Heroku migration guide](heroku.md) instead, which provides a migration tool with a web dashboard.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
