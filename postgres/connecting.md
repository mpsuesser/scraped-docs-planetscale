---
url: https://planetscale.com/docs/postgres/connecting
title: "Connecting"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Connecting to your PlanetScale Postgres database

Connecting to your PlanetScale Postgres database involves understanding several key components. This page provides an overview of connection options — for detailed instructions, see the linked documentation below.

## Postgres

Create **role credentials** with [`pscale role`](../cli/role.md), then connect using a [connection string](connecting/quickstart.md).

`pscale connect` is not supported.

## Vitess / MySQL

Create a **branch password** with [`pscale password`](../cli/password.md), then connect with a [connection string](../vitess/connecting/connection-strings.md) or [`pscale connect`](../cli/connect.md).

### AI assistant integrations

You can expose read-only schema and query capabilities to tools like Claude, Cursor, or Zed through the shared [Model Context Protocol (MCP)](../connect/mcp.md) guide. The CLI configures each tool for you via `pscale mcp install --target <claude|cursor|zed>`, and the PlanetScale MCP server streams data over a secure, read-only connection.

### Roles and credentials

PlanetScale provides two types of roles for database access:

- **Default postgres role** — A near-superuser role with extensive permissions, ideal for administrative tasks and initial database setup. This role should not be used for application connections.
- **User-defined roles** — Custom roles with specific permission sets that follow the principle of least privilege. These are recommended for all application connections and allow credential rotation without downtime.

Connection credentials include a hostname, username (formatted as `{role}.{branch_id}`), password (prefixed with `pscale_pw_`), and database name. Learn more about [managing roles and creating credentials](connecting/roles.md).

### Connection strings

PlanetScale databases require SSL/TLS encryption for all connections. Connection strings include parameters for the host, port, username, password, database name, and SSL configuration. The port determines the connection method:

- **Port 5432** — Direct connections to Postgres, bypassing PgBouncer
- **Port 6432** — Connections through PgBouncer for connection pooling

The [connections quickstart](connecting/quickstart.md) provides detailed connection string examples and explains when to use each connection method.

### Private connectivity

For enhanced security and reduced latency, PlanetScale supports private connectivity that keeps traffic within cloud provider networks:

- **AWS PrivateLink** — Establishes private connections from your AWS VPC to PlanetScale databases without exposing traffic to the public internet. See the [AWS PrivateLink documentation](connecting/private-connections/aws-privatelink.md).
- **GCP Private Service Connect** — Provides private connectivity from your Google Cloud VPC to PlanetScale databases. See the [GCP Private Service Connect documentation](connecting/private-connections/gcp-private-service-connect.md).

### Choosing a connection method for your platform

Most JavaScript and TypeScript applications should connect with a standard TCP driver such as [`pg`](tutorials/planetscale-postgres-node.md) through [PgBouncer](connecting/pgbouncer.md) on port **6432**. This applies to Vercel (with [`@vercel/functions`](https://vercel.com/guides/connection-pooling-with-functions) connection pooling), AWS Lambda, Railway, Render, and long-running servers.

For platforms without reliable TCP pooling, PlanetScale supports the [Neon serverless driver](connecting/neon-serverless-driver.md) over HTTP or WebSockets. Use it for [Netlify Functions](https://docs.netlify.com/functions/overview/), [Deno Deploy](https://deno.com/deploy/docs), and edge runtimes where TCP is not available. For [Cloudflare Workers](https://developers.cloudflare.com/workers/), prefer [Hyperdrive with `pg`](tutorials/planetscale-postgres-cloudflare-workers.md) over the serverless driver when possible.

## Understanding Postgres connections

Postgres uses a connection-per-process architecture. Each connection made to a Postgres server [spawns a new process](https://planetscale.com/blog/processes-and-threads), which consumes system resources including memory and CPU. For this reason, it’s important to manage the number of direct connections to keep the system performant.

Connection pooling is the primary solution to this challenge. In the Postgres ecosystem, [PgBouncer](https://www.pgbouncer.org/) is the most widely-used connection pooler. PgBouncer instances sit between clients and the Postgres server, maintaining a small pool of connections to Postgres while accepting a much larger number of client connections. PgBouncer routes client requests through these pooled connections efficiently.

## Connection options

PlanetScale provides several ways to connect to your Postgres database:

1. **Direct primary connections** - Connect directly to your Postgres primary server on port `5432`. This provides the lowest latency and full Postgres session capabilities. Use this for administrative tasks, long-running operations, and data imports.
2. **Direct replica connections** - Connect directly to read-only replicas on port `5432` by appending `|replica` to your username. Use this for read-only queries that can tolerate replication lag.
3. **Local PgBouncer (primary only)** - All Postgres databases include a local PgBouncer running on the same host as the primary. Connect via port `6432`. This is recommended for all application connections to the primary.
4. **Dedicated replica PgBouncer** - Create dedicated PgBouncer instances that pool connections to your replicas. These run on separate nodes and are useful for read-heavy workloads. Connect via port `6432` with the PgBouncer name appended to your username.
5. **Dedicated primary PgBouncer** - Create dedicated PgBouncer instances that pool connections to your primary database. These run on separate nodes and provide improved high availability, with connections persisting through cluster resizes, upgrades, and most failover scenarios. Connect via port `6432` with the PgBouncer name appended to your username.

The following sections describe each option in detail to help you choose the right connection method for your use case.

## Direct primary connections

Direct connections provide the lowest-latency access to your Postgres primary instance.

![Direct connections](https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/postgres/connecting/diagram-direct-connect.png?w=2500&fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=74bd197e2f263822552d2c3845ef9e5b)

Direct connections

However, these connections are considered *heavy-weight* since each one consumes significant resources. Direct connections are recommended only for specific scenarios:

1. Administrative tasks, like creating new databases/schemas, manual DDL commands, and installing extensions.
2. Long-running operations like `VACUUM` s and large analytical queries that are executed infrequently.
3. Importing data during a migration or other bulk-loading operations.
4. When you need features like `SET`, pub/sub, and other features not provided by PgBouncer pooled connections.

Because having too many direct connections degrades performance, PlanetScale sets `max_connections` to a conservative default value that varies depending on cluster size. To find this value, navigate to the “Clusters” page and select the “Parameters” tab.

![Navigate to the Cluster Parameters page](https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/postgres/connecting/cluster-configuration-parameters-darkmode.png?w=2500&fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=941ad0599c231328f8b91f545d3818af)

Navigate to the Cluster Parameters page

Search for `max_connections` to view the current configured value. This can be increased if necessary, though doing so requires careful consideration as increasing direct connections can negatively impact performance.

When the `max_connections` limit is reached, error messages like the following will appear:

```text
FATAL: sorry, too many clients already
```

Or variations such as:

```text
FATAL: remaining connection slots are reserved for non-replication superuser connections
```

For application connections outside of the specific use cases listed above, PgBouncer should be used instead.

## Direct replica connections

The main purpose for the default [Replicas](scaling/replicas.md) in a cluster is to maintain [high-availability](operations-philosophy.md), but they can also be used to handle read traffic. Since replicas are read-only, they are only capable of serving `SELECT` queries. All write traffic (`INSERT`, `UPDATE`, etc) must be sent to the primary.

![Direct replica connections](https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/postgres/connecting/diagram-replica-direct-connect.png?w=2500&fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=31d4d9690e765bb47f548266d148be80)

Direct replica connections

Replicas always experience some level of replication lag — the delay between data arriving at the primary and being replicated to a replica. Frequently, replication lag is measured in milliseconds, but it can grow to multiple seconds, especially when the server is experiencing high write traffic or network issues.

Because of these factors, queries should only be sent to replicas if they meet the following criteria: (A) they are read-only and (B) they can tolerate being slightly out-of-sync with the data on the primary. For reads that cannot tolerate this lag, send them to the primary.

To connect to a replica, append `|replica` to your credential username and use port `5432`. For example:

```shellscript
psql 'host=xxxxxxxxxx-useast1-1.horizon.psdb.cloud \
      port=5432 \
      user=postgres.xxxxxxxxxx|replica \
      password=pscale_pw_xxxxxxxxxxxxxxxxxx \
      dbname=my_database \
      sslnegotiation=direct \
      sslmode=verify-full \
      sslrootcert=system'
```

Learn more about replicas and when to use them in the [database replicas documentation](scaling/replicas.md).

## PgBouncer connections

PgBouncer provides connection pooling for your Postgres database, allowing applications to scale beyond the constraints of direct connections. Connections from application servers should be made via PgBouncer whenever possible. PlanetScale provides three types of PgBouncer instances:

All managed PgBouncers run in transaction pooling mode. Session pooling is not offered because it creates a 1:1 mapping between clients and Postgres connections, so it hits the same connection limits as direct connections while introducing additional latency through another proxy layer. If you require session-specific behavior, use a direct connection on port `5432` instead of requesting session pooling.

### Local PgBouncer

![Local PgBouncer connections](https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/postgres/connecting/diagram-local-pgbouncer.png?w=2500&fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=c54abe2369f2d869954ec6fe5e9ce60f)

Local PgBouncer connections

All PlanetScale Postgres databases include a local PgBouncer instance running on the same host node as the Postgres primary. This is recommended for all application connections to the primary. To connect via the local PgBouncer, use the same credentials as a direct connection but change the port from `5432` to `6432`.

The local PgBouncer only routes connections to the primary. To pool connections to replicas, use a [dedicated replica PgBouncer](#dedicated-replica-pgbouncer).

### Dedicated replica PgBouncer

![Dedicated replica PgBouncer connections](https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/postgres/connecting/diagram-dedicated-replica-pgbouncer.png?w=2500&fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=52d7c26310e78922f9074ac6a503522b)

Dedicated replica PgBouncer connections

[Dedicated replica PgBouncers](connecting/pgbouncer.md#dedicated-replica-pgbouncers) run on nodes separate from the Postgres instances and pool connections to your replicas. These are useful for read-heavy workloads that send significant read traffic to replicas.

### Dedicated primary PgBouncers

![Dedicated primary PgBouncer connections](https://mintcdn.com/planetscale-2/kiyN17feiApl1in5/images/assets/docs/postgres/connecting/diagram-dedicated-primary-pgbouncer.png?w=2500&fit=max&auto=format&n=kiyN17feiApl1in5&q=85&s=2b2747293c8455088d4ffe2f0b4a4488)

Dedicated primary PgBouncer connections

[Dedicated primary PgBouncers](connecting/pgbouncer.md#dedicated-primary-pgbouncers) provide connection pooling for your primary database on nodes separate from the Postgres servers. Traffic routes from the dedicated primary PgBouncer to the local PgBouncer on the primary node, and then to Postgres. This lets client connections persist through cluster resizes, upgrades, and most failover scenarios, providing improved high availability.

## Connecting to dedicated PgBouncers

Connect to replica or primary PgBouncers via port `6432` and append the name of the PgBouncer to your username. For example, if your PgBouncer is named `read-bouncer`, the connection username should be `postgres.xxxxxxxxxx|read-bouncer`.

```shellscript
psql 'host=xxxxxxxxxx-useast1-1.horizon.psdb.cloud \
      port=6432 \
      user=postgres.xxxxxxxxxx|read-bouncer \
      password=pscale_pw_xxxxxxxxxxxxxxxxxx \
      dbname=my_database \
      sslnegotiation=direct \
      sslmode=verify-full \
      sslrootcert=system'
```

Learn more about [creating, configuring, and connecting to PgBouncers](connecting/pgbouncer.md).
