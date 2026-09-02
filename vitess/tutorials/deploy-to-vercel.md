---
url: https://planetscale.com/docs/vitess/tutorials/deploy-to-vercel
title: "Deploy To Vercel"
description: ""
access_date: 2026-09-02T21:57:53.710Z
current_date: 2026-09-02T21:57:53.710Z
---

To use a PlanetScale database with Vercel, there are a few prerequisites:

- A PlanetScale database — If you haven’t created a database, refer to our [PlanetScale quickstart guide](planetscale-quick-start-guide.md) to get started
- A [Vercel account](https://vercel.com/)
- A project deployed to Vercel — If you’re just poking around and don’t already have an application to deploy, you can use our [Next.js + PlanetScale sample](connect-nextjs-app.md)

## Get your connection string from PlanetScale

## Copy environment variables to Vercel

For example, if you’re using Prisma, your connection string will look similar to this:

```shellscript
DATABASE_URL='mysql://xxxxxxxxx:************@xxxxxxxxxx.us-east-3.psdb.cloud/my_database?sslaccept=strict'
```

In Vercel, you’ll set it as follows:

- **NAME** = `DATABASE_URL`
- **VALUE** = `mysql://xxxxxxxxx:************@xxxxxxxxxx.us-east-3.psdb.cloud/my_database?sslaccept=strict`

The credentials are blurred for the example, but when you paste them in, use the actual values.

## What’s next?

Learn more about how PlanetScale allows you to make [non-blocking schema changes](../schema-changes.md) to your database tables without locking or causing downtime for production databases.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
