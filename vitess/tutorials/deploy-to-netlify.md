---
url: https://planetscale.com/docs/vitess/tutorials/deploy-to-netlify
title: "Deploy To Netlify"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Deploy to Netlify

> This guide will walk you through setting up and deploying your PlanetScale database on Netlify.

This page covers Vitess.

> [!NOTE]
>
> This doc is intended for users that are manually storing a connection string in an environment variable in Netlify. If you want to use the Netlify integration, which handles this for you, see the [PlanetScale integration in the Netlify docs](https://docs.netlify.com/extend/install-and-use/setup-guides/planetscale-integration/).

## Prerequisites

* A PlanetScale database — If you haven't created a database, refer to our [PlanetScale quickstart guide](https://planetscale.com/docs/vitess/tutorials/planetscale-quick-start-guide.md) to get started
* A [Netlify account](https://netlify.com/)
* A project deployed to Netlify — If you're just poking around and don't already have an application to deploy, you can use our [Next.js + PlanetScale sample](https://planetscale.com/docs/vitess/tutorials/connect-nextjs-app.md)

## Connecting your PlanetScale database to your Netlify application

### Get your connection string from PlanetScale

**Step 1**

In your [PlanetScale dashboard](https://app.planetscale.com), click on the database you want to connect to.

**Step 2**

Click "**Connect**".

**Step 3**

Create a new password. Make sure to copy the password, as you'll only be shown it once.

**Step 4**

Select the framework you're using from the "**Select your language or framework**" section. This will give you the exact environment variable names you need for your selected framework. If your framework is not listed, choose "Other".

![PlanetScale dashboard connect modal priority](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/deploy-to-netlify/prisma.png?fit=max\&auto=format\&n=AJPY38bILe2zenXX\&q=85\&s=835e84a4d3e55f73b4989fba9a697a93)

**Step 5**

Keep this page open, as you'll need to copy these to Netlify momentarily.

**Step 6**

If you navigate away from the page, and can no longer access the password, create a new password by repeating steps 1-5.

### Copy environment variables to Netlify

**Step 1**

Go to your Netlify dashboard.

**Step 2**

Click on your Netlify project.

**Step 3**

Click "**Site settings**".

**Step 4**

Click "**Build & deploy**," then "**Environment**".

**Step 5**

Click "**Edit variable**".

**Step 6**

Click "**New variable**" and copy each value from your PlanetScale dashboard into a new environment variable in Netlify. Once you're done with one, click "**Add**" and continue to the next, if applicable.

For example, if you're using Prisma, your connection string will look similar to this:

```bash theme={null}
DATABASE_URL='mysql://xxxxxxxxx:************@xxxxxxxxxx.us-east-3.psdb.cloud/my-database?sslaccept=strict'
```

> [!NOTE]
>
> Your environment variable name will be the same in your application's code. We used `DATABASE_URL` as an example, but this can be given a different name.

In Netlify, you'll set it as follows:

* **Key** = `DATABASE_URL`
* **Value** = `mysql://xxxxxxxxx:************@xxxxxxxxxx.us-east-3.psdb.cloud/my-database?sslaccept=strict`

> [!NOTE]
>
> The credentials are blurred for the example, but when you paste them in, use the actual values.

![Netlify dashboard - Environment variables](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/deploy-to-netlify/environment-variables.png?fit=max\&auto=format\&n=AJPY38bILe2zenXX\&q=85\&s=0c932e42819d03a2c1f098333b41c42a)

After you have saved, you will need to rebuild the site with the new environment variable.

## What's next?

Learn more about how PlanetScale allows you to make [non-blocking schema changes](https://planetscale.com/docs/vitess/schema-changes.md) to your database tables without locking or causing downtime for production databases.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
