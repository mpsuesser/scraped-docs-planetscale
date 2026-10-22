---
url: https://planetscale.com/docs/vitess/tutorials/deploy-to-vercel
title: "Deploy To Vercel"
description: ""
access_date: 2026-10-22T18:21:49.469Z
current_date: 2026-10-22T18:21:49.469Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Deploy to Vercel

> This guide will walk you through setting up and deploying your PlanetScale database on Vercel.

To use a PlanetScale database with Vercel, there are a few prerequisites:

* A PlanetScale database — If you haven't created a database, refer to our [PlanetScale quickstart guide](https://planetscale.com/docs/vitess/tutorials/planetscale-quick-start-guide.md) to get started
* A [Vercel account](https://vercel.com/)
* A project deployed to Vercel — If you're just poking around and don't already have an application to deploy, you can use our [Next.js + PlanetScale sample](https://planetscale.com/docs/vitess/tutorials/connect-nextjs-app.md)

## Get your connection string from PlanetScale

**Step 1**

In your [PlanetScale dashboard](https://app.planetscale.com), click on the database you want to connect to.

**Step 2**

Click "**Connect**".

**Step 3**

Create a new password. Make sure to copy the password, as you'll only be shown it once.

**Step 4**

Select the framework you're using from the "**Select your language or framework**" section. This will give you the exact environment variable names you need for your selected framework. If your framework is not listed, choose "Other".

![PlanetScale dashboard connect modal \{priority}](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/deploy-to-vercel/prisma.png?fit=max\&auto=format\&n=AJPY38bILe2zenXX\&q=85\&s=a0e74c1e2d55b2b86a1eb506ec0a91d4)

**Step 5**

Keep this page open, as you'll need to copy these to Vercel momentarily.

**Step 6**

If you navigate away from the page, and can no longer access the password, create a new password by repeating steps 1-5.

## Copy environment variables to Vercel

**Step 1**

Go to your Vercel dashboard.

**Step 2**

Click on your Vercel project.

**Step 3**

Click "**Settings**".

**Step 4**

Click "**Environment variables**".

**Step 5**

Copy each value from your PlanetScale dashboard into a new environment variable in Vercel. Once you're done with one, click "**Add**" and continue to the next, if applicable.

For example, if you're using Prisma, your connection string will look similar to this:

```bash theme={null}
DATABASE_URL='mysql://xxxxxxxxx:************@xxxxxxxxxx.us-east-3.psdb.cloud/my_database?sslaccept=strict'
```

In Vercel, you'll set it as follows:

* **NAME** = `DATABASE_URL`
* **VALUE** = `mysql://xxxxxxxxx:************@xxxxxxxxxx.us-east-3.psdb.cloud/my_database?sslaccept=strict`

> [!NOTE]
>
> The credentials are blurred for the example, but when you paste them in, use the actual values.

![Vercel dashboard - Environment variables](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/deploy-to-vercel/environment-variables.png?fit=max\&auto=format\&n=AJPY38bILe2zenXX\&q=85\&s=e66d4f8a5e6ab9c21f7be071a3086a1c)

## What's next?

Learn more about how PlanetScale allows you to make [non-blocking schema changes](https://planetscale.com/docs/vitess/schema-changes.md) to your database tables without locking or causing downtime for production databases.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
