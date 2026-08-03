---
url: https://planetscale.com/docs/postgres/postgres-compatibility
title: "Postgres Compatibility"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Overview

PlanetScale Postgres runs on PostgreSQL versions 17 and 18. See our [supported versions](cluster-configuration/versions.md) documentation for the specific versions available.

PlanetScale Postgres is designed to be fully compatible with standard PostgreSQL. Unlike PlanetScale Vitess, which has [certain MySQL compatibility limitations](../vitess/mysql-compatibility.md), PlanetScale Postgres supports the full PostgreSQL feature set, including stored procedures, triggers, and foreign keys.

## Full PostgreSQL feature support

PlanetScale Postgres supports all core PostgreSQL features:

| Feature | Support | Notes |
| --- | --- | --- |
| Stored procedures and functions | ✅ | Full support for `CREATE FUNCTION` and `CREATE PROCEDURE` |
| Foreign keys and constraints | ✅ | All constraint types supported |
| Triggers | ✅ | All trigger types supported |
| Materialized views | ✅ | Full support including `REFRESH MATERIALIZED VIEW` |
| Transactions and ACID | ✅ | Full transactional support |
| JSON/JSONB | ✅ | All JSON functions and operators |
| Full-text search | ✅ | Including `tsvector`, `tsquery`, and related functions |
| Table partitioning | ✅ | Range, list, and hash partitioning |
| Logical replication | ✅ | As source or target — see [logical replication documentation](integrations/logical-cdc.md) |
| Window functions | ✅ | Full support |
| Common Table Expressions (CTEs) | ✅ | Including recursive CTEs |
| `LISTEN` / `NOTIFY` | ✅ | Full support for pub/sub messaging |
| `CREATE DATABASE` / multiple logical databases | ✅ | Create multiple PostgreSQL databases within a single cluster |

## Tool compatibility

PlanetScale Postgres works with the PostgreSQL ecosystem:

- **Drivers**: Standard PostgreSQL drivers and connection strings (libpq, psycopg, pg, node-postgres, etc.)
- **ORMs**: Prisma, ActiveRecord, Sequelize, Drizzle, Django ORM, SQLAlchemy, and others
- **Monitoring**: pganalyze, Datadog DBM — see [monitoring documentation](monitoring/pganalyze.md)
- **Migration tools**: pgcopydb, AWS DMS — see [import documentation](imports/postgres-imports.md)
- **CDC tools**: Debezium, Fivetran, Airbyte, ClickPipes — see [CDC documentation](integrations/logical-cdc.md)

## PlanetScale-specific behaviors

While PlanetScale Postgres is fully PostgreSQL-compatible, there are some platform-specific behaviors to be aware of.

### Roles and permissions

The following reference describes PlanetScale-specific permission behaviors.

| Behavior | Description |
| --- | --- |
| **No SUPERUSER access** | PlanetScale does not grant `SUPERUSER` privileges as part of our security model. The default role has near-superuser capabilities. See [role management](connecting/roles.md) for details. |
| **Default role permissions** | The default `postgres` role has `NOSUPERUSER CREATEDB CREATEROLE INHERIT LOGIN REPLICATION BYPASSRLS` and inherits permissions including `pg_read_all_data`, `pg_write_all_data`, `pg_monitor`, `pg_read_all_settings`, `pg_read_all_stats`, `pg_stat_scan_tables`, `pg_signal_backend`, `pg_checkpoint`, `pg_maintain`, `pg_use_reserved_connections`, and `pg_create_subscription`. See [role management](connecting/roles.md) for the complete list. |
| **User-defined roles** | Create application-specific roles with minimal required permissions. Roles created via the dashboard, CLI, or API are managed by PlanetScale. |

Most operations that would require `SUPERUSER` in vanilla PostgreSQL work with PlanetScale’s default role. If you encounter a specific use case requiring elevated permissions, [contact support](https://planetscale.com/contact?initial=support).

### Extensions

PlanetScale supports a curated list of vetted PostgreSQL extensions. See the [extensions documentation](extensions.md) for the complete list.

| Behavior | Description |
| --- | --- |
| **Curated extension list** | Extensions must be on our supported list. You can [request new extensions](https://ps-extensions.io/) or contact support. |
| **Version pinning** | Extension versions are pinned to each PostgreSQL major version and do not auto-upgrade. |
| **Restart requirements** | Some extensions require a cluster restart to enable. These are marked in the [extensions documentation](extensions.md#supported-extensions). |
| **Dashboard configuration** | Extensions requiring shared memory or background workers must be enabled through the PlanetScale dashboard. |

When migrating to PlanetScale Postgres, verify that your required extensions are supported and check version compatibility. Extensions like PostGIS may have version differences that affect functionality.

### Branch behavior

PlanetScale Postgres uses [branches](branching.md) for isolated database environments.

| Behavior | Description |
| --- | --- |
| **Configuration not inherited** | New branches start with default configuration. Extensions and custom parameters must be re-enabled on each branch. |
| **Isolated environments** | Branches are completely isolated. Schema and data changes in one branch do not affect others. |
| **No automatic schema sync** | Unlike PlanetScale Vitess deploy requests, schema changes must be manually applied to each branch. |

### Configuration and logging

| Behavior | Description |
| --- | --- |
| **Log line prefix** | The `log_line_prefix` does not include database (`%d`) and user (`%u`) identifiers by default, but can be [customized](cluster-configuration/parameters.md) to include them. |
| **Default search path** | Default `search_path` is `"$user", public`. Custom search paths from source databases need manual configuration via `ALTER ROLE ... SET search_path = 'your_schema'`. |
| **Parameter changes** | Some parameters require a cluster restart. See [parameters documentation](cluster-configuration/parameters.md) for details. |

### Operational characteristics

| Behavior | Description |
| --- | --- |
| **Failover behavior** | Production clusters have 1 primary and 2 replicas. Failovers typically complete in seconds. See [operations philosophy](operations-philosophy.md). |
| **Disk autoscaling** | Storage autoscaling is enabled by default for network-attached storage clusters, automatically growing disk when utilization thresholds are reached. See [storage configuration](cluster-configuration/cluster-storage.md). |
| **No CPU/RAM autoscaling** | Cluster sizing is manual. Use the Clusters page to monitor utilization and resize as needed. |

## Migrating to PlanetScale Postgres

For detailed migration guidance, including step-by-step instructions for importing your existing PostgreSQL database, see our [import documentation](imports/postgres-imports.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
