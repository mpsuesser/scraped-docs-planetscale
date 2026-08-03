---
url: https://planetscale.com/docs/vitess/tutorials/automatic-prisma-migrations
title: "Automatic Prisma Migrations"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

This document has been updated to include the recommended Prisma and PlanetScale workflow, specifically the recommendation to use `prisma db push` instead of `prisma migrate dev` with shadow branches. Also, you previously needed to turn on the ability to automatically copy the Prisma migration metadata. You no longer need to do this. Read more below.

## Introduction

In this tutorial, we’re going to learn how to do Prisma migrations in PlanetScale as part of your deployment process using `prisma db push`.

### Quick introduction to Prisma’s db push

From a high level, [Prisma’s `db push`](https://www.prisma.io/docs/orm/reference/prisma-cli-reference#db-push) introspects your PlanetScale database to infer and execute the changes required to make your database schema reflect the state of your Prisma schema. When `prisma db push` is run, it will ensure the schema in the PlanetScale branch you are currently connected to matches your current Prisma schema.

We recommend `prisma db push` over `prisma migrate dev` for the following reasons:

PlanetScale provides [Online Schema Changes](../schema-changes/how-online-schema-change-tools-work.md) that are deployed automatically when you merge a deploy request and prevents [blocking schema changes](../schema-changes.md) that can lead to downtime. This is different from the typical Prisma workflow which uses `prisma migrate` in order to generate SQL migrations for you based on changes in your Prisma schema. When using PlanetScale with Prisma, the responsibility of applying the changes is on the PlanetScale side. Therefore, there is little value to using `prisma migrate` with PlanetScale.

Also, the migrations table created when `prisma migrate` runs can also be misleading since PlanetScale does the actual migration when the deploy request is merged, not when `prisma migrate` is run which only updates the schema in the development database branch. You can still see the history of your schema changes in PlanetScale.

## Prerequisites

- Add Prisma to your project using `npm install prisma --save-dev` or `yarn add prisma --dev` (depending on what package manager you prefer).
- Run `npx prisma init` inside of your project to create the initial files needed for Prisma.
- Install the [PlanetScale CLI](https://github.com/planetscale/cli).
- Authenticate the CLI with the following command:

```shellscript
pscale auth login
```

## Execute your first Prisma db push

Prisma migrations follow the PlanetScale [non-blocking schema change](../schema-changes.md) workflow. First, the schema is applied to a *development* branch and then the development branch is merged into the `main` production database.

Let’s begin with an example flow for running Prisma migrations in PlanetScale:

## Execute succeeding Prisma migrations in PlanetScale

Our first example migration flow went well, but what happens when you need to run further changes to your schema?

Let’s take a look:

## What’s next?

Now that you’ve successfully conducted your first automatic Prisma migration in PlanetScale and know how to handle future migrations, it’s time to deploy your application with a PlanetScale database! Let’s learn how to [deploy an application with a PlanetScale database to Vercel](deploy-to-vercel.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
