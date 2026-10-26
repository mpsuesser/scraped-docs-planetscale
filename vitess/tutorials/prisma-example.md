---
url: https://planetscale.com/docs/vitess/tutorials/prisma-example
title: "Prisma Example"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Using the PlanetScale serverless driver with Prisma

> This document outlines how you can use the [PlanetScale serverless driver](planetscale-serverless-driver.md) along with Prisma in your application.

This page covers Vitess.

Postgres version: [Postgres](https://planetscale.com/docs/postgres/tutorials/planetscale-postgres-prisma.md)

## Set up

To get started:

**Step 1**

Install the Prisma driver adapter for PlanetScale (`@prisma/adapter-planetscale`) and PlanetScale serverless driver (`@planetscale/database`) packages:

```shell theme={null}
npm install @prisma/adapter-planetscale @planetscale/database
```

**Step 2**

Update your `prisma/schema.prisma` file:

```javascript theme={null}
// schema.prisma
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

> [!NOTE]
>
> Ensure you update the host value in your connection string to `aws.connect.psdb.cloud`. You can learn more about this [here](https://planetscale.com/docs/vitess/tutorials/planetscale-serverless-driver.md#add-and-use-the-planetscale-serverless-driver-for-javascript-to-your-project).

**Step 3**

Generate Prisma Client:

```shell theme={null}
npx prisma generate
```

**Step 4**

Update your Prisma Client instance to use the PlanetScale serverless driver:

```javascript expandable theme={null}
import "dotenv/config"
import { PrismaPlanetScale } from '@prisma/adapter-planetscale'
import { PrismaClient } from './generated/prisma/client.js'

const adapter = new PrismaPlanetScale({ url: process.env.DATABASE_URL })
const prisma = new PrismaClient({ adapter })

async function main() {
  const posts = await prisma.post.findMany()
  console.log(posts)
}
```

You can then use Prisma Client as you usually would with auto-completion and full type-safety.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
