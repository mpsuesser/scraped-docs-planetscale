---
url: https://planetscale.com/docs/vitess/tutorials/nextjs-deploy-to-vercel
title: "Nextjs Deploy To Vercel"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Deploy to Vercel

> This guide will walk you through setting up and deploying your PlanetScale database on Vercel.

This page covers Vitess.

It will cover two options:

> **[Manually adding PlanetScale environment variables to your Vercel project](#manually-connect-to-vercel)**

> **[Connecting your PlanetScale database to your application using the Vercel integration](#deploy-with-the-planetscale-vercel-integration)**

## Prerequisites

* A PlanetScale database — If you haven't created a database, refer to our [PlanetScale quickstart guide](https://planetscale.com/docs/vitess/tutorials/planetscale-quick-start-guide.md) to get started
* A [Vercel account](https://vercel.com/)
* A project deployed to Vercel — If you're just poking around and don't already have an application to deploy, you can use our [Next.js + PlanetScale sample](https://planetscale.com/docs/vitess/tutorials/connect-nextjs-app.md)

## Manually connect to Vercel

### Get your connection string from PlanetScale

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

### Copy environment variables to Vercel

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

## Deploy with the PlanetScale Vercel integration

If you don't want to copy and paste the environment variables over to Vercel, you can use the [PlanetScale integration from the Vercel marketplace](https://vercel.com/integrations/planetscale). You can choose which database you want to connect to, and we'll automatically pull the necessary environment variables into your Vercel project.

**Step 1**

You must have an existing PlanetScale database to use the integration. You can create a database in the [PlanetScale dashboard](https://app.planetscale.com).

**Step 2**

Click "**Add integration**" on the [Vercel integrations page](https://vercel.com/integrations/planetscale).

**Step 3**

Select the Vercel account you want to connect with.

**Step 4**

On the left, you'll see the Vercel options, and on the right, the PlanetScale options.

**Step 5**

Select the Vercel project you want to connect to, and beneath that, select the framework you're using. If the framework isn't listed, select "**General**". This selection is what determines the names of the environment variables.

**Step 6**

On the right side, choose the [PlanetScale Organization](https://planetscale.com/docs/security/access-control.md) that the database is in. The integration will remain tied to this Organization and cannot be changed.

**Step 7**

Beneath that, select the database you want to connect to.

**Step 8**

Click "**Connect database**".

**Step 9**

Back in your Vercel dashboard, confirm the environment variables were added by going to your Vercel project > "**Settings**" > "**Environment variables**"

### Configure your connection

After you set up the initial connection, you also have the option to configure the PlanetScale connection, add more databases to the project, or remove databases from the project.

To access the configuration page:

**Step 1**

In your Vercel project dashboard, click "**Integrations**".

**Step 2**

Click the "**PlanetScale**" integration.

**Step 3**

Click the "**Configure**" button.

> [!NOTE]
>
> If you're modifying an existing connection on a Vercel project, these environment variable values will be regenerated and overwritten.

> [!NOTE]
>
> * Environment variables are removed from all projects tied to the integration.
> * Your application will no longer be able to connect to your PlanetScale database.

## What's next?

Learn more about how PlanetScale allows you to make [non-blocking schema changes](https://planetscale.com/docs/vitess/schema-changes.md) to your database tables without locking or causing downtime for production databases.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
