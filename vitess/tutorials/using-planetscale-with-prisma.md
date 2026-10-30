---
url: https://planetscale.com/docs/vitess/tutorials/using-planetscale-with-prisma
title: "Using Planetscale With Prisma"
description: ""
access_date: 2026-10-30T18:26:51.105Z
current_date: 2026-10-30T18:26:51.105Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Using PlanetScale with Prisma

> [Prisma](https://www.prisma.io/) ORM provides type-safe database access through an intuitive API, eliminating the need to write SQL queries manually.

This page covers Vitess.

Postgres version: [Postgres](https://planetscale.com/docs/postgres/tutorials/planetscale-postgres-prisma.md)

Prisma and PlanetScale together offer a powerful workflow. Prisma handles the object-relational mapping with full type safety and intuitive data modeling, while PlanetScale provides horizontal scaling, branching workflows, and zero-downtime schema changes, making it an ideal stack for building modern, scalable applications.

This guide covers how to use Prisma and PlanetScale together, including how to:

* Create a database with PlanetScale
* Integrate it with Prisma
* Use additional features like sharding and the serverless driver

If you don't already have an application set up with Prisma, but want to test how it works with PlanetScale, we recommend grabbing a sample application from [Prisma's examples repository](https://github.com/prisma/prisma-examples/tree/latest/orm).

## Set up your PlanetScale database

First, set up your PlanetScale database.

**Step 1**

Sign in to your PlanetScale account

**Step 2**

Click "Create a database"

**Step 3**

Give your database a name

**Step 4**

Select your preferred region

**Step 5**

Choose cluster and storage size

**Step 6**

Click "Create database"

Your database will deploy with an initial production branch, `main`.

### Create a password

After the database is created, you'll be prompted to generate credentials for it. You can come back to this later if needed.

**Step 1**

Give your password a name or leave the default

**Step 2**

Select a [password role](https://planetscale.com/docs/vitess/security/password-roles.md). We recommend `Admin` for your default password.

**Step 3**

Click "Create password"

**Step 4**

Select "Prisma" for "Select your language or framework"

**Step 5**

Do not navigate away from this page. The next section covers where to paste these credentials in your Prisma app. If you close without copying, you can regenerate new credentials.

## Connect to PlanetScale in your application

Next, add your database credentials to your Prisma application. If you are migrating an existing database to PlanetScale, you can test the PlanetScale/Prisma integration locally or in staging first. Once you're ready to migrate the database from your existing provider, refer to our no downtime [migration guides](https://planetscale.com/docs/vitess/imports/database-imports.md).

To connect PlanetScale to your Prisma application:

**Step 1**

Follow the steps in the above section if you navigated away from the page previously

**Step 2**

Update your `prisma/schema.prisma` file with the following:

```js theme={null}
generator client {
  provider = "prisma-client"
  output   = "../generated/prisma"
}

datasource db {
  provider     = "mysql"
  url          = env("DATABASE_URL")
  relationMode = "prisma"
}
```

**Step 3**

Create a `prisma.config.ts` file in the root of your project:

```js theme={null}
import "dotenv/config";
import { defineConfig, env } from "prisma/config";

export default defineConfig({
  schema: "prisma/schema.prisma",
  datasource: {
    url: env("DATABASE_URL"),
  },
});
```

**Step 4**

Update the `DATABASE_URL` value in your `.env` file with the value provided to you when you created a new password in the PlanetScale dashboard. It should look like this:

```
DATABASE_URL='mysql://<USERNAME>:<PASSWORD>@aws.connect.psdb.cloud/<DATABASE>?sslaccept=strict'
```

## Foreign key constraints

PlanetScale does not enable foreign key constraints by default. If you are not using foreign key constraints to enforce referential integrity at the database level, integrating with Prisma will still work, but it requires a couple additional steps (detailed below).

If you do plan to use foreign key constraints, enable them on your PlanetScale database settings page.

**Step 1**

In your database dashboard, go to "Settings"

**Step 2**

Enable "Foreign key constraints"

**Step 3**

Save your changes

### Using Prisma without foreign key constraints

If you are not using foreign key constraints, you can use Prisma's [`relationMode`](https://www.prisma.io/docs/orm/prisma-schema/data-model/relations/relation-mode) to emulate relations.

You need to update `datasource db` in your `schema.prisma` file to include `relationMode = "prisma"`:

```
datasource db {
  provider     = "mysql"
  url          = env("DATABASE_URL")
  relationMode = "prisma"
}
```

When emulating relations, you also need to manually create dedicated indexes on foreign keys to avoid performance issues. For example, if you have `Post` and `Comment` tables, where the `Comment` table references a `post`, you need to add an index to your `Post` model:

```js theme={null}
model Post {
  id       Int       @id @default(autoincrement())
  title    String
  content  String
  likes    Int       @default(0)
  comments Comment[]
}

model Comment {
  id      Int    @id @default(autoincrement())
  comment String
  postId  Int
  post    Post   @relation(fields: [postId], references: [id], onDelete: Cascade)

  @@index([postId]) // manually created index
}
```

You can learn more about how to do this in Prisma in their [Emulating relations documentation](https://www.prisma.io/docs/orm/overview/databases/planetscale#option-1-emulate-relations-in-prisma-client).

## Push your Prisma schema to PlanetScale

Push your schema to your PlanetScale branch with:

```bash theme={null}
npx prisma db push
```

Then generate the Prisma Client:

```bash theme={null}
npx prisma generate
```

The recommended workflow with using Prisma alongside PlanetScale is to use `prisma db push` instead of `prisma migrate`. You can read more about [`prisma db push` here](https://www.prisma.io/docs/orm/reference/prisma-cli-reference#db-push).

Your PlanetScale database schema now matches the Prisma schema you configured in `prisma/schema.prisma`. To confirm this, go to your PlanetScale dashboard, click "Branches", and select the branch you generated credentials for. You should see your schema.

## Using the PlanetScale serverless driver with Prisma

The [PlanetScale serverless driver](https://planetscale.com/docs/vitess/tutorials/planetscale-serverless-driver.md) allows you to execute queries over HTTP. You can use it with Prisma ORM via the `@prisma/adapter-planetscale` driver adapter.

**Step 1**

Install the Prisma `adapter-planetscale` and PlanetScale serverless driver packages:

```bash theme={null}
npm install @prisma/adapter-planetscale @planetscale/database
```

**Step 2**

Generate Prisma Client:

```bash theme={null}
npx prisma generate
```

**Step 3**

If you're not already using a direct connection string, update your host to `aws.connect.psdb.cloud` or `gcp.connect.psdb.cloud`, depending on your chosen region.

**Step 4**

Configure your Prisma Client with the adapter:

```js theme={null}
import "dotenv/config"
import { PrismaPlanetScale } from '@prisma/adapter-planetscale'
import { PrismaClient } from './generated/prisma/client.js'

const adapter = new PrismaPlanetScale({ url: process.env.DATABASE_URL })
const prisma = new PrismaClient({ adapter })
```

For more information, see the [Prisma documentation](https://www.prisma.io/docs/getting-started/prisma-orm/quickstart/planetscale).

## Using the MariaDB adapter with Prisma (TCP)

If you are running in a traditional server environment (Express, Fastify, etc.) where TCP connections are available, you can use `@prisma/adapter-mariadb` instead of the PlanetScale serverless driver.

**Step 1**

Install the MariaDB adapter:

```bash theme={null}
npm install @prisma/adapter-mariadb
```

**Step 2**

Configure your Prisma Client with the adapter:

```js theme={null}
import "dotenv/config"
import { PrismaMariaDb } from '@prisma/adapter-mariadb'
import { PrismaClient } from './generated/prisma/client.js'

const adapter = new PrismaMariaDb({
  host: 'aws.connect.psdb.cloud',
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,
  port: 3306,
  ssl: true,
})
const prisma = new PrismaClient({ adapter })
```

> [!NOTE]
>
> The MariaDB adapter requires explicit SSL configuration when connecting to PlanetScale. Set `ssl: true` in the adapter options. Passing a connection string URL directly to `PrismaMariaDb` does not automatically enable SSL.

## Sharding with PlanetScale and Prisma

If you are using a [sharded database](https://planetscale.com/docs/vitess/sharding.md) with PlanetScale, Prisma supports defining shard keys in your Prisma schema. This is currently available as a [Preview](https://www.prisma.io/docs/orm/more/releases#preview) feature in Prisma as of [v6.10.0](https://github.com/prisma/prisma/releases/tag/6.10.0).

To use shard key attributes, specify the `shardKeys` Preview feature on your Prisma generator in `schema.prisma`:

```prisma theme={null}
generator client {
  provider        = "prisma-client"
  output          = "../generated/prisma"
  previewFeatures = ["shardKeys"]
}
```

This allows you to use `@shardKey` and `@@shardKey` attributes.

* `@shardKey` — Used to define a single-column shard key
* `@@shardKey` — Used to define a multi-column shard key

For example, if you have a shard key on your `region` column in your `User` database, you can define that in your Prisma model with:

```js theme={null}
model User {
  id     String @default(uuid())
  region String @shardKey
}
```

## Next steps

* Make safe schema changes with [branching and deploy requests](https://planetscale.com/docs/vitess/schema-changes/branching.md)
* Explore [PlanetScale Insights](https://planetscale.com/docs/vitess/monitoring/query-insights.md) for performance monitoring
* Learn how to [target your replicas](https://planetscale.com/docs/vitess/scaling/replicas.md)
* Explore our [migration guides](https://planetscale.com/docs/vitess/imports/database-imports.md)
* Enable [PlanetScale vectors](https://planetscale.com/docs/vitess/vectors.md)
* Implement [read replicas](https://planetscale.com/docs/vitess/scaling/replicas.md) for scale
* Join the [PlanetScale Discord](https://pscale.link/community) community

## Additional resources

> [card] [Prisma Documentation](https://www.prisma.io/docs)

> [card] [Prisma + PlanetScale Best Practices](https://www.prisma.io/docs/orm/overview/databases/planetscale)

> [card] [PlanetScale Support](https://planetscale.com/contact?initial=support)

> [card] [Foreign key constraints support](https://planetscale.com/docs/vitess/foreign-key-constraints.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
