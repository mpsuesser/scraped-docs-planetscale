---
url: https://planetscale.com/docs/neki/guides/node
title: "Node"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

Neki uses the Postgres wire protocol, so a Node.js application can connect with [`pg`](https://node-postgres.com/).

Before you start, you need:

- A Neki database with a ready branch.
- An application [role](../connecting/roles.md) for that branch. Use `pg_read_all_data` for read traffic and add `pg_write_all_data` when the application writes rows. These inherited data roles do not grant `CREATE` or `ALTER`, so application credentials cannot change the schema.
- A separate migration role, if you run schema changes against the branch. DDL requires the `postgres` inherited role. Neki restricts execution of the cross-router DDL barrier function `__neki.wait_for_ddl` to its `neki_viewer` role, so add `neki_viewer`, which requires `pg_read_all_data`, to the same role. Keep the migration credentials out of the application’s runtime configuration.
- The **Primary** connection details from the database’s **Connect** page.

Use the complete generated username. It contains the branch routing information required by Neki.

## Install the Postgres client

Install `pg`. This example also uses `dotenv` to load local environment variables:

```shellscript
npm install pg dotenv
```

## Add the connection string

Copy the **Primary** connection URI from the **Connect** page into `.env`:

.env

```shellscript
DATABASE_URL='postgresql://<USERNAME>:<PASSWORD>@<HOST>:5432/postgres?sslmode=verify-full'
```

Use the generated URI so reserved characters in the credentials remain URL encoded. Do not commit the `.env` file. Set the same value through your deployment platform’s secret manager in production.

## Connect and run a query

Create a client from `DATABASE_URL`. The `node-postgres` connection-string parser maps `sslmode=verify-full` to Node.js TLS certificate and hostname verification. Do not add `sslrootcert=system`; `node-postgres` treats its value as a certificate filename.

index.js

```javascript
require('dotenv').config()

const { Client } = require('pg')

const client = new Client({
  connectionString: process.env.DATABASE_URL,
})

async function main() {
  await client.connect()

  const result = await client.query(
    'SELECT current_database() AS database, current_user AS role, NOW() AS time'
  )

  console.log(result.rows[0])
  await client.end()
}

main().catch((error) => {
  console.error(error)
  process.exit(1)
})
```

Run the application:

```shellscript
node index.js
```

The query travels through a Neki router, which plans it and sends it to the appropriate shard.

## Apply schema changes

Framework migration commands send DDL through a normal Neki connection. The router fans that DDL out to the managed shards, but it does not create a managed schema-change workflow. Use a [schema-change workflow](../schema-changes.md) when you want Neki to prepare and coordinate an Online DDL change.

Run migrations with the migration role described in the prerequisites. An application role that inherits only `pg_read_all_data` and `pg_write_all_data` cannot run `CREATE` or `ALTER`, so a migration that uses application credentials fails on its first DDL statement.

Apply migration DDL with `psql` and the migration role so the client prints PostgreSQL notices:

```shellscript
psql "$MIGRATION_DATABASE_URL" -f <MIGRATION_FILE>
```

The router that commits the DDL emits a `NOTICE` containing the barrier call for that transaction:

```text
NOTICE: DDL is committed and the change is visible on this router; other routers may not see it yet. To wait until it is visible on every router, use __neki.wait_for_ddl(42, 7)
```

The schema and cluster versions belong to that transaction, and the notice is the only place Neki reports them. Before you deploy application code that depends on the new schema, run the emitted call verbatim on a primary connection that uses the migration role:

```sql
SELECT __neki.wait_for_ddl(42, 7);
```

The call returns after every router has applied both versions. A successful migration only confirms that the DDL committed and is visible through the router that ran it; it does not replace this cross-router barrier.

Some framework migration commands discard notices. Generate migration SQL with the framework, but apply it with `psql` when the framework cannot preserve the notice. If the notice is lost, its version pair cannot be reconstructed.

For read-only traffic, see [Primary and replica routing](../connecting.md#primary-and-replica-routing).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
