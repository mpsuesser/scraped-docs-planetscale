---
url: https://planetscale.com/docs/vitess/tutorials/using-planetscale-with-prisma
title: "Using Planetscale With Prisma"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

Prisma and PlanetScale together offer a powerful workflow. Prisma handles the object-relational mapping with full type safety and intuitive data modeling, while PlanetScale provides horizontal scaling, branching workflows, and zero-downtime schema changes, making it an ideal stack for building modern, scalable applications.

This guide covers how to use Prisma and PlanetScale together, including how to:

- Create a database with PlanetScale
- Integrate it with Prisma
- Use additional features like sharding and the serverless driver

If you don’t already have an application set up with Prisma, but want to test how it works with PlanetScale, we recommend grabbing a sample application from [Prisma’s examples repository](https://github.com/prisma/prisma-examples/tree/latest/orm).

## Set up your PlanetScale database

First, set up your PlanetScale database.

Your database will deploy with an initial production branch, `main`.

### Create a password

After the database is created, you’ll be prompted to generate credentials for it. You can come back to this later if needed.

## Connect to PlanetScale in your application

Next, add your database credentials to your Prisma application. If you are migrating an existing database to PlanetScale, you can test the PlanetScale/Prisma integration locally or in staging first. Once you’re ready to migrate the database from your existing provider, refer to our no downtime [migration guides](../imports/database-imports.md).

To connect PlanetScale to your Prisma application:

## Foreign key constraints

PlanetScale does not enable foreign key constraints by default. If you are not using foreign key constraints to enforce referential integrity at the database level, integrating with Prisma will still work, but it requires a couple additional steps (detailed below).

If you do plan to use foreign key constraints, enable them on your PlanetScale database settings page.

### Using Prisma without foreign key constraints

If you are not using foreign key constraints, you can use Prisma’s [`relationMode`](https://www.prisma.io/docs/orm/prisma-schema/data-model/relations/relation-mode) to emulate relations.

You need to update `datasource db` in your `schema.prisma` file to include `relationMode = "prisma"`:

```text
datasource db {
  provider     = "mysql"
  url          = env("DATABASE_URL")
  relationMode = "prisma"
}
```

When emulating relations, you also need to manually create dedicated indexes on foreign keys to avoid performance issues. For example, if you have `Post` and `Comment` tables, where the `Comment` table references a `post`, you need to add an index to your `Post` model:

```javascript
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

```shellscript
npx prisma db push
```

Then generate the Prisma Client:

```shellscript
npx prisma generate
```

The recommended workflow with using Prisma alongside PlanetScale is to use `prisma db push` instead of `prisma migrate`. You can read more about [`prisma db push` here](https://www.prisma.io/docs/orm/reference/prisma-cli-reference#db-push).

Your PlanetScale database schema now matches the Prisma schema you configured in `prisma/schema.prisma`. To confirm this, go to your PlanetScale dashboard, click “Branches”, and select the branch you generated credentials for. You should see your schema.

## Using the PlanetScale serverless driver with Prisma

The [PlanetScale serverless driver](planetscale-serverless-driver.md) allows you to execute queries over HTTP. You can use it with Prisma ORM via the `@prisma/adapter-planetscale` driver adapter.

For more information, see the [Prisma documentation](https://www.prisma.io/docs/getting-started/prisma-orm/quickstart/planetscale).

## Using the MariaDB adapter with Prisma (TCP)

If you are running in a traditional server environment (Express, Fastify, etc.) where TCP connections are available, you can use `@prisma/adapter-mariadb` instead of the PlanetScale serverless driver.

## Sharding with PlanetScale and Prisma

If you are using a [sharded database](../sharding.md) with PlanetScale, Prisma supports defining shard keys in your Prisma schema. This is currently available as a [Preview](https://www.prisma.io/docs/orm/more/releases#preview) feature in Prisma as of [v6.10.0](https://github.com/prisma/prisma/releases/tag/6.10.0).

To use shard key attributes, specify the `shardKeys` Preview feature on your Prisma generator in `schema.prisma`:

```prisma
generator client {
  provider        = "prisma-client"
  output          = "../generated/prisma"
  previewFeatures = ["shardKeys"]
}
```

This allows you to use `@shardKey` and `@@shardKey` attributes.

- `@shardKey` — Used to define a single-column shard key
- `@@shardKey` — Used to define a multi-column shard key

For example, if you have a shard key on your `region` column in your `User` database, you can define that in your Prisma model with:

```javascript
model User {
  id     String @default(uuid())
  region String @shardKey
}
```

## Next steps

- Make safe schema changes with [branching and deploy requests](../schema-changes/branching.md)
- Explore [PlanetScale Insights](../monitoring/query-insights.md) for performance monitoring
- Learn how to [target your replicas](../scaling/replicas.md)
- Explore our [migration guides](../imports/database-imports.md)
- Enable [PlanetScale vectors](../vectors.md)
- Implement [read replicas](../scaling/replicas.md) for scale
- Join the [PlanetScale Discord](https://pscale.link/community) community

## Additional resources

## Prisma Documentation

## Prisma + PlanetScale Best Practices

## PlanetScale Support

## Foreign key constraints support

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
