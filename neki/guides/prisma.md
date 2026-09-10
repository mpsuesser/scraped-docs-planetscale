---
url: https://planetscale.com/docs/neki/guides/prisma
title: "Prisma"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

Prisma ORM 7 connects to Neki through the `@prisma/adapter-pg` driver adapter.

Before you start, you need:

- A Neki database with a ready branch.
- An application [role](../connecting/roles.md) for that branch. Use `pg_read_all_data` for read traffic and add `pg_write_all_data` when the application writes rows. These inherited data roles do not grant `CREATE` or `ALTER`, so application credentials cannot change the schema.
- A separate migration role, if you run schema changes against the branch. DDL requires the `postgres` inherited role. Neki restricts execution of the cross-router DDL barrier function `__neki.wait_for_ddl` to its `neki_viewer` role, so add `neki_viewer`, which requires `pg_read_all_data`, to the same role. Keep the migration credentials out of the application’s runtime configuration.
- The **Primary** connection details from the database’s **Connect** page.

Use the complete generated username. It contains the branch routing information required by Neki.

## Install and initialize Prisma

Install Prisma, the Postgres adapter, and `pg`, then initialize Prisma:

```shellscript
npm install prisma @prisma/client @prisma/adapter-pg pg dotenv
npx prisma init
```

## Add the connection strings

Set `DATABASE_URL` to the application role and `MIGRATION_DATABASE_URL` to the migration role. Copy both URIs from the **Connect** page:

.env

```shellscript
DATABASE_URL='postgresql://<APPLICATION_USERNAME>:<PASSWORD>@<HOST>:5432/postgres?sslmode=verify-full'
MIGRATION_DATABASE_URL='postgresql://<MIGRATION_USERNAME>:<PASSWORD>@<HOST>:5432/postgres?sslmode=verify-full'
```

If the username or password contains reserved URL characters, use the encoded value from the generated URI instead of inserting the raw value.

## Configure Prisma

Use the Postgres provider in `prisma/schema.prisma`:

prisma/schema.prisma

```prisma
generator client {
  provider = "prisma-client"
  output   = "../generated/prisma"
}

datasource db {
  provider = "postgresql"
}
```

Configure the Prisma CLI to use the migration credentials:

prisma.config.ts

```typescript
import 'dotenv/config'
import { defineConfig, env } from 'prisma/config'

export default defineConfig({
  schema: 'prisma/schema.prisma',
  migrations: {
    path: 'prisma/migrations',
  },
  datasource: {
    url: env('MIGRATION_DATABASE_URL'),
  },
})
```

## Create the client

Prisma ORM 7 requires a driver adapter at runtime:

src/db.ts

```typescript
import 'dotenv/config'

import { PrismaPg } from '@prisma/adapter-pg'
import { PrismaClient } from '../generated/prisma/client'

const adapter = new PrismaPg({
  connectionString: process.env.DATABASE_URL,
})

export const prisma = new PrismaClient({ adapter })
```

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

Prisma Migrate does not expose the PostgreSQL notice that contains Neki’s DDL barrier. Create migration files against a local Postgres database, not against the Neki branch:

```shellscript
MIGRATION_DATABASE_URL="$LOCAL_DATABASE_URL" npx prisma migrate dev --create-only
```

Apply the generated SQL with `psql`, run the barrier call printed in the notice, then record the migration in Prisma’s history:

```shellscript
psql "$MIGRATION_DATABASE_URL" -f prisma/migrations/<MIGRATION>/migration.sql
npx prisma migrate resolve --applied <MIGRATION>
```

Do not run `prisma migrate dev` or `prisma migrate deploy` against Neki because those commands do not preserve the DDL barrier notice.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
