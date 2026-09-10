---
url: https://planetscale.com/docs/neki/guides/kysely
title: "Kysely"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

Kysely connects to Neki through its Postgres dialect and a `pg` connection pool.

Before you start, you need:

- A Neki database with a ready branch.
- An application [role](../connecting/roles.md) for that branch. Use `pg_read_all_data` for read traffic and add `pg_write_all_data` when the application writes rows. These inherited data roles do not grant `CREATE` or `ALTER`, so application credentials cannot change the schema.
- A separate migration role, if you run schema changes against the branch. DDL requires the `postgres` inherited role. Neki restricts execution of the cross-router DDL barrier function `__neki.wait_for_ddl` to its `neki_viewer` role, so add `neki_viewer`, which requires `pg_read_all_data`, to the same role. Keep the migration credentials out of the application’s runtime configuration.
- The **Primary** connection details from the database’s **Connect** page.

Use the complete generated username. It contains the branch routing information required by Neki.

## Install the dependencies

Install Kysely, `pg`, and `dotenv`. This example uses `tsx` to run TypeScript:

```shellscript
npm install kysely pg dotenv
npm install --save-dev typescript tsx @types/node @types/pg
```

## Add the connection string

Copy the connection URI from the **Connect** page into `.env`:

.env

```shellscript
DATABASE_URL='postgresql://<USERNAME>:<PASSWORD>@<HOST>:5432/postgres?sslmode=verify-full'
```

Use the generated URI so reserved characters in the credentials remain URL encoded. The `node-postgres` driver uses Node.js TLS certificate and hostname verification for `sslmode=verify-full`. Do not add `sslrootcert=system`; `node-postgres` treats its value as a certificate filename. Do not commit the `.env` file.

## Connect and run a query

Create a Kysely instance with the Postgres dialect:

app.ts

```typescript
import 'dotenv/config'

import { Kysely, PostgresDialect, sql } from 'kysely'
import pg from 'pg'

interface Database {}

const db = new Kysely<Database>({
  dialect: new PostgresDialect({
    pool: new pg.Pool({
      connectionString: process.env.DATABASE_URL,
    }),
  }),
})

const result = await sql<{ database: string; role: string }>\`
  SELECT current_database() AS database, current_user AS role
\`.execute(db)

console.log(result.rows[0])
await db.destroy()
```

Run the application:

```shellscript
npx tsx app.ts
```

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

For read-only traffic, add the Neki replica startup option described in [Primary and replica routing](../connecting.md#primary-and-replica-routing) to the connection URI.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
