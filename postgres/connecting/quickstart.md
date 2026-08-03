---
url: https://planetscale.com/docs/postgres/connecting/quickstart
title: "Quickstart"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

We recommend reading the before going through this quickstart. It contains important information such as when and how to use PgBouncer over direct connections.

## Postgres

Create **role credentials** with [`pscale role`](../../cli/role.md), then connect using a [connection string](quickstart.md).

`pscale connect` is not supported.

## Vitess / MySQL

Create a **branch password** with [`pscale password`](../../cli/password.md), then connect with a [connection string](../../vitess/connecting/connection-strings.md) or [`pscale connect`](../../cli/connect.md).

## Creating a role

After creating a Postgres database, you will need to create a new role to connect.

Make sure you copy the credentials, as we will not display them once you leave the page. If you need to access your default credentials after leaving the page, you have to reset the default password.

You’ll be provided with the following:

```shellscript
DATABASE_HOST=**********-<REGION>.horizon.psdb.cloud
DATABASE_NAME=<DATABASE_NAME>
DATABASE_USERNAME=postgres.<BRANCH_ID>
DATABASE_PASSWORD=pscale_pw_**************************
```

Here is an example of the connection string that connects directly (without [PgBouncer](pgbouncer.md)) you can use to connect to the Postgres CLI:

```shellscript
psql 'host=xxxxxxxxxx-useast1-1.horizon.psdb.cloud \
      port=5432 \
      user=postgres.xxxxxxxxxx \
      password=pscale_pw_xxxxxxxxxxxxxxxxxx \
      dbname=my_database \
      sslnegotiation=direct \
      sslmode=verify-full \
      sslrootcert=system'
```

### Connection parameters

| Parameter | Description |
| --- | --- |
| **host** | Your database hostname in the format `{id+region}.horizon.psdb.cloud` |
| **port** | Connection port (`5432` for direct, `6432` for PgBouncer) |
| **user** | Your role username in the format `{role}.{branch_id}` |
| **password** | Your role password (begins with `pscale_pw_`) |
| **dbname** | Your database name |
| **sslmode** | Set to `verify-full` for secure connections (required) |
| **sslrootcert** | Set to `system`. See the [Secure connections documentation](quickstart.md#secure-connections) if that produces an error. |
| **sslnegotiation** | Set to `direct` for improved performance (optional) |

## Secure connections

All PlanetScale Postgres connections require SSL/TLS encryption. The following parameters are used to enforce this:

| Parameter | Required | Description |
| --- | --- | --- |
| `sslmode=verify-full` | Required | Verifies both encryption and server identity |
| `sslrootcert=system` | Required | Uses [system certificate](https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLROOTCERT) store by default. If this method doesn’t work, you can enter the path to your root certificate instead. The exact [locations of root certificates](../connecting.md) differ by SSL implementation and platform. |
| `sslnegotiation=direct` | Optional | Enables direct SSL negotiation for better performance. |

## Additional roles

You should not connect to your database from application servers with the default role, as you will need to take some downtime if you ever have to rotate your password.

Instead, you can [create additional roles](roles.md) with fine-grained permission settings for this purpose. Check out our [roles](roles.md) documentation for more information.

## Resetting the default role credentials

If you need to reset the password for the default `postgres` role:

Resetting the default password will disconnect any existing connections using the previous credentials.

## Default postgres role

The default `postgres` role is similar to the Postgres `superuser`, but with fewer permissions. It is defined by the following statement:

```sql
CREATE ROLE $POSTGRES_USERNAME
  NOSUPERUSER CREATEDB CREATEROLE INHERIT LOGIN REPLICATION BYPASSRLS PASSWORD '$PASSWORD';
```

It also inherits the following permissions:

```sql
GRANT pg_read_all_data,
  pg_write_all_data,
  pg_read_all_settings,
  pg_read_all_stats,
  pg_stat_scan_tables,
  pg_monitor,
  pg_signal_backend,
  pg_checkpoint,
  pg_maintain,
  pg_use_reserved_connections,
  pg_create_subscription
TO $ALMOST_SUPERUSER_ROLENAME WITH ADMIN OPTION;
```

You should not connect to your database from your applications using the default role, as you will need to take some downtime if you ever have to rotate your password. Instead, you should [create additional roles](roles.md) as needed.

## Connection types: Direct vs PgBouncer

PlanetScale offers two connection methods for PostgreSQL databases: direct (port `5432`) and via PgBouncer (port `6432`).

**PlanetScale’s use of PgBouncer (port `6432`) does not support replica routing.** All connections through PgBouncer are automatically routed to the primary database, regardless of the username specification. Use direct connections (port `5432`) for replica access.

### Direct connection (Port 5432)

Using port 5432 bypasses PgBouncer and connects directly to Postgres. This is the recommended way to connect for any operations that require long-running queries or persistent connections.

PgBouncer operates in transaction pooling mode, where connections are returned to the pool after each transaction completes. This means that session-level features and long-running operations are interrupted between transactions.

Direct connections are recommended for:

- Schema changes and DDL
- OLAP, analytics, reporting, or batch processing
- Session-specific features: Custom session variables, temporary tables
- ETL processes and data streaming
- Long-running transactions or queries that span multiple transactions

Additionally, if you’re connecting to a replica, you must connect directly. PgBouncer is not supported for replica connections.

### PgBouncer connection (Port 6432)

[PgBouncer](pgbouncer.md) enables high availability for a Postgres database by efficiently pooling connections and buffering queries during failovers. PgBouncer is generally recommended for OLTP workloads. For example, we’d recommend routing your application connections through PgBouncer. You can connect through PgBouncer by updating your connection string to use port `6432`.

PgBouncer connections operate in transaction mode, which means each connection is only held for the duration of a single transaction. This provides excellent performance for OLTP workloads but limits certain PostgreSQL features that require persistent connections. For use cases that require long-running operations, we recommend a direct connection on port `5432`.

## Routing to replicas

PlanetScale Postgres supports routing connections to replicas for improved read performance and load distribution. To connect to a replica, append `|replica` to your credential username. For example:

```shellscript
# Connect to replica
user=postgres.xxxxxxxxx|replica # where postgres.xxxxxxxxx is your username
```

You can append `|replica` to any role you create on your Postgres database.

**PlanetScale’s use of PgBouncer (port `6432`) does not support replica routing.** All connections through PgBouncer are automatically routed to the primary database, regardless of the username specification. Use direct connections (port `5432`) for replica access.

## Authentication

PlanetScale Postgres uses SCRAM-SHA-256 authentication, which provides enhanced security over traditional password authentication methods.

### Username format

All usernames follow the format `{role}.{branch_id}`:

| Component | Description | Examples |
| --- | --- | --- |
| **role** | The role name | `postgres`, `app_user` |
| **branch\_id** | The unique identifier for your database branch | `cnlmx96ec5kw` |
| **Full username** | Complete format: `{role}.{branch_id}` | `postgres.cnlmx96ec5kw`, `app_user.cnlmx96ec5kw` |

The branch ID in the username tells PlanetScale’s routing layer (Exosphere) which specific database branch to connect to.

### Password format

All PlanetScale Postgres passwords begin with `pscale_pw_` followed by a unique string:

```shellscript
pscale_pw_XXXXXXXXXXXXXXXXXXXX
```

## Strong security model

PlanetScale roles are created for use with a single database branch. This strong security model allows you to generate roles that are tied to a branch, and cannot access data/schema from another branch.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
