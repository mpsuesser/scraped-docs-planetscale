---
url: https://planetscale.com/docs/vitess/tutorials/automatic-prisma-migrations
title: "Automatic Prisma Migrations"
description: ""
access_date: 2026-10-20T18:01:41.586Z
current_date: 2026-10-20T18:01:41.586Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Automatic Prisma migrations

> [!NOTE]
>
> This document has been updated to include the recommended Prisma and PlanetScale workflow, specifically the
> recommendation to use `prisma db push` instead of `prisma migrate dev` with shadow branches. Also, you previously
> needed to turn on the ability to automatically copy the Prisma migration metadata. You no longer need to do this. Read
> more below.

## Introduction

In this tutorial, we're going to learn how to do Prisma migrations in PlanetScale as part of your deployment process using `prisma db push`.

### Quick introduction to Prisma's db push

From a high level, [Prisma's `db push`](https://www.prisma.io/docs/orm/reference/prisma-cli-reference#db-push) introspects your PlanetScale database to infer and execute the changes required to make your database schema reflect the state of your Prisma schema. When `prisma db push` is run, it will ensure the schema in the PlanetScale branch you are currently connected to matches your current Prisma schema.

We recommend `prisma db push` over `prisma migrate dev` for the following reasons:

PlanetScale provides [Online Schema Changes](../schema-changes/how-online-schema-change-tools-work.md) that are deployed automatically when you merge a deploy request and prevents [blocking schema changes](../schema-changes.md) that can lead to downtime. This is different from the typical Prisma workflow which uses `prisma migrate` in order to generate SQL migrations for you based on changes in your Prisma schema. When using PlanetScale with Prisma, the responsibility of applying the changes is on the PlanetScale side. Therefore, there is little value to using `prisma migrate` with PlanetScale.

Also, the migrations table created when `prisma migrate` runs can also be misleading since PlanetScale does the actual migration when the deploy request is merged, not when `prisma migrate` is run which only updates the schema in the development database branch. You can still see the history of your schema changes in PlanetScale.

## Prerequisites

* Add Prisma to your project using `npm install prisma --save-dev` or `yarn add prisma --dev` (depending on what package manager you prefer).
* Run `npx prisma init` inside of your project to create the initial files needed for Prisma.
* Install the [PlanetScale CLI](https://github.com/planetscale/cli).
* Authenticate the CLI with the following command:

```bash theme={null}
pscale auth login
```

## Execute your first Prisma db push

Prisma migrations follow the PlanetScale [non-blocking schema change](../schema-changes.md) workflow. First, the schema is applied to a *development* branch and then the development branch is merged into the `main` production database.

Let's begin with an example flow for running Prisma migrations in PlanetScale:

**Step 1**

Create a new *prisma-playground* database:

```bash theme={null}
pscale db create prisma-playground
```

**Step 2**

Connect to the database branch:

```bash theme={null}
pscale connect prisma-playground main --port 3309
```

> [!NOTE]
>
> This step assumes you created a new PlanetScale database and have not yet enabled [Safe Migrations](../schema-changes/safe-migrations.md) on the `main` branch. You will need to create a new development branch otherwise.

**Step 3**

Update your `prisma/schema.prisma` file with the following schema:

> [!NOTE]
>
> In Prisma `4.5.0`, `referentialIntegrity` changed to `relationMode` and became generally available in `4.7.0`. The following schema reflects this change.
>
> You can learn more about Prisma's Relation mode in the
> [Prisma docs](https://www.prisma.io/docs/orm/prisma-schema/data-model/relations/relation-mode).

```js expandable theme={null}
datasource db {
  provider = "mysql"
  url      = env("DATABASE_URL")
  relationMode = "prisma"
}

generator client {
  provider = "prisma-client-js"
}

model Post {
  id        Int      @default(autoincrement()) @id
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  title     String   @db.VarChar(255)
  content   String?
  published Boolean  @default(false)
  author    User     @relation(fields: [authorId], references: [id])
  authorId  Int
}

model Profile {
  id     Int     @default(autoincrement()) @id
  bio    String?
  user   User    @relation(fields: [userId], references: [id])
  userId Int     @unique
}

model User {
  id      Int      @default(autoincrement()) @id
  email   String   @unique
  name    String?
  posts   Post[]
  profile Profile?
}
```

**Step 4**

Update your `.env` file:

```shell theme={null}
DATABASE_URL="mysql://root@127.0.0.1:3309/prisma-playground"
```

**Step 5**

In another terminal, use the `db push` command to push the schema defined in `prisma/schema.prisma`:

```bash theme={null}
npx prisma db push
```

Unlike the `prisma migrate dev` command, it will not create a migrations folder containing a SQL file with the SQL used to update the schema in your PlanetScale database. PlanetScale will be tracking your migrations in this workflow.

> [!TIP]
>
> You can learn more about the `prisma db push` command in the
> [Prisma docs](https://www.prisma.io/docs/orm/reference/prisma-cli-reference#db-push).

After `db push` is successful, you can see the table created in your terminal. For example, to see the `Post` table:

```bash theme={null}
pscale shell prisma-playground main
```

```sql theme={null}
describe Post;
```

> [!TIP]
>
> Use the `exit` command to exit the MySQL shell.

Or you can see it in the PlanetScale UI under the Schema tab in your `main` branch.

**Step 6**

Finally, turn on safe migrations on the `main` branch to enable non-blocking schema changes:

```bash theme={null}
pscale branch safe-migrations enable prisma-playground main
```

## Execute succeeding Prisma migrations in PlanetScale

Our first example migration flow went well, but what happens when you need to run further changes to your schema?

Let's take a look:

**Step 1**

Create a new *development* branch from `main` called `add-subtitle-to-posts`:

```bash theme={null}
pscale branch create prisma-playground add-subtitle-to-posts
```

**Step 2**

Close the proxy connection to your `main` branch (if still open) and connect to the new `add-subtitle-to-posts` development branch:

```bash theme={null}
pscale connect prisma-playground add-subtitle-to-posts --port 3309
```

**Step 3**

In the `prisma/schema.prisma` file, update the `Post` model:

Add a new `subtitle` field to `Post`:

```
subtitle  String   @db.VarChar(255)
```

**Step 4**

Run `db push` again to update the schema in PlanetScale:

```bash theme={null}
npx prisma db push
```

**Step 5**

Open a deploy request for your `add-subtitle-to-posts` branch, so that you can deploy these changes to `main`.

You can complete the deploy request either in the web app or with the `pscale deploy-request` command.

```bash theme={null}
pscale deploy-request create prisma-playground add-subtitle-to-posts
```

```bash theme={null}
pscale deploy-request deploy prisma-playground 1
```

**Step 6**

Once the deploy request is merged, you can see the results in your main branch's `Post` table:

```bash theme={null}
pscale shell prisma-playground main
```

```sql theme={null}
describe Post;
```

## What's next?

Now that you've successfully conducted your first automatic Prisma migration in PlanetScale and know how to handle future migrations, it's time to deploy your application with a PlanetScale database! Let's learn how to [deploy an application with a PlanetScale database to Vercel](deploy-to-vercel.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
