---
url: https://planetscale.com/docs/vitess/tutorials/prisma-best-practices
title: "Prisma Best Practices"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Referential actions and integrity with Prisma and PlanetScale

When using Prisma with PlanetScale, you need to make sure to set `relationMode` to `prisma` in your Prisma schema:

```javascript
datasource db {
  provider     = "mysql"
  url          = env("DATABASE_URL")
  relationMode = "prisma"
}
```

In Prisma `4.5.0`, `referentialIntegrity` changed to `relationMode` and generally became available in `4.7.0`.

The `prisma` relation mode emulates some foreign key constraints and referential actions for each Prisma Client query to maintain referential integrity, using some additional database queries and logic.

Read more about [Relation mode in Prisma’s documentation](https://www.prisma.io/docs/orm/prisma-schema/data-model/relations/relation-mode).

### Creating an index for relation scalar fields

When a Prisma client uses the `foreignKeys` relation mode, which does not work with PlanetScale, the database implicitly creates an index for the foreign key columns. Therefore, it is recommended that you create an index for your relation scalar fields with the `@@index` attribute (or the `@unique`, `@@unique` or `@@id` attributes, if applicable) when using `prisma` relation mode with PlanetScale.

```javascript
model Post {
  id     Int  @id
  userId Int
  user   User @relation(fields: [userId], references: [id])

  @@index([userId])
}
```

If you do not add the index, you might notice that some of your queries are slow or are performing full table scans and reading a lot of data. When you run `prisma format` or `prisma validate`, it will warn you about a missing index where you are using foreign key constraints.

If you want to learn more about MySQL indexes, check out the [MySQL for Developers course section on indexes](https://planetscale.com/learn/courses/mysql-for-developers/indexes/introduction-to-indexes).

## Migration workflows using prisma db push

With Prisma, there are two ways to apply schema changes to your database: `prisma migrate` and `prisma db push`. We recommend `prisma db push` over `prisma migrate dev` for the following reasons:

PlanetScale automatically provides built-in [Online Schema Change tools](../schema-changes/how-online-schema-change-tools-work.md) when you merge a deploy request and prevents [blocking schema changes](../schema-changes.md) that can lead to downtime. This differs from the typical Prisma workflow, which uses `prisma migrate` to generate SQL migrations for you based on changes in your Prisma schema. When using PlanetScale with Prisma, the responsibility of applying the changes is on the PlanetScale side. Therefore, there is little value to using `prisma migrate` with PlanetScale.

Also, the migrations table created when `prisma migrate` runs can be misleading since PlanetScale does the actual migration when the deploy request is merged, not when `prisma migrate` is run, which only updates the schema in the development database branch. You can still see the history of your schema changes in PlanetScale.

If you want to read more about `prisma db push`, see the [Prisma documentation on making schema changes with `prisma db push`](https://www.prisma.io/docs/orm/overview/databases/planetscale).

## Connection management

In Prisma v7, connection pooling is managed by the driver adapter (e.g. `@prisma/adapter-mariadb` or `@prisma/adapter-planetscale`) rather than through URL parameters like `connection_limit` and `pool_timeout`. If you are using Prisma v7, configure pool settings directly in your adapter options. See the [Prisma connection pool guide](https://www.prisma.io/docs/orm/prisma-client/setup-and-configuration/databases-connections/connection-pool) for details.

When using Prisma with PlanetScale, you might encounter some specific error messages. It might seem like your PlanetScale database is down, but there are other reasons why it might appear this way.

For both errors, if your serverless function or application servers and database are not in the same region, this can contribute to latency problems. If possible, try to move them closer together to decrease physical latency.

Here are some of the common error messages with their possible causes and solutions:

### Prisma error P1001

```shellscript
"Can't reach database server at {database_host}:{database_port} Please make sure your database server is running at {database_host}:{database_port}."
```

**Possible cause:**

- The Prisma Client did not establish the connection within the `connect_timeout`. There are many possible reasons for this to occur, two of the common examples we see are DNS resolution issues and network latency.

**Possible solutions:**

- Increase the `connect_timeout` in your `DATABASE_URL`.

Example `DATABASE_URL`: `mysql://USER:PASSWORD@HOST:PORT/DATABASE?connect_timeout=30`

The default is `5`. The `connect_timeout` is the maximum number of seconds to wait for a new connection to be opened, `0` means no timeout. We suggest trying a higher number around `30` seconds if you have this issue. See the [Prisma connection URL argument documentation](https://www.prisma.io/docs/orm/reference/connection-urls) for more information.

### Prisma error P2024

```text
"Timed out fetching a new connection from the connection pool. (More info: http://pris.ly/d/connection-pool (Current connection pool timeout: {timeout}, connection limit: {connection_limit})"
```

**Possible cause:**

- The connection is established, but the Prisma query engine is not able to process a query in the queue before the time limit.

**Possible solutions:**

- Increase the pool size by increasing the `connection_limit`.

Example `DATABASE_URL`: `mysql://USER:PASSWORD@HOST:PORT/DATABASE?connection_limit=10`

The default is `num_cpus * 2 + 1`. The `connection_limit` is the maximum size of the connection pool for each instance of the Prisma Client. `connection_limit` is not the total for all of your application servers or serverless functions. While PlanetScale can handle hundreds of thousands of connections at a time, we recommend incrementally increasing this number to tune your Prisma Client. See the [Prisma documentation on optimizing the connection pool size](https://www.prisma.io/docs/orm/prisma-client/setup-and-configuration/databases-connections#optimizing-the-connection-pool) for more info.

- Increase the `pool_timeout`.

Example `DATABASE_URL`: `mysql://USER:PASSWORD@HOST:PORT/DATABASE?pool_timeout=30`

The default is `10`. The `pool_timeout` is the maximum number of seconds to wait for a new connection from the pool, `0` means no timeout. We recommend increasing this only after you’ve tuned the `connection_limit`. See the [Prisma documentation on optimizing the connection pool timeout](https://www.prisma.io/docs/orm/prisma-client/setup-and-configuration/databases-connections#optimizing-the-connection-pool) for more info.

- Decrease query duration, which might include asking for less data in the query or improving performance with an index or other methods. Start by looking at your [Insights](../monitoring/query-insights.md) page within your PlanetScale database to see which queries might be performing poorly.

Lastly, this [discussion topic response on Prisma connection pooling](https://github.com/planetscale/discussion/discussions/188#discussioncomment-3808093) can be helpful if you are experiencing these errors.

## Other resources:

## Using PlanetScale with Prisma

## Prisma quickstart for adding Prisma to an existing project

## Prisma document on connection management

## Prisma document on how the connection pool works in Prisma

## Video: Prisma & PlanetScale Best Practices

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
