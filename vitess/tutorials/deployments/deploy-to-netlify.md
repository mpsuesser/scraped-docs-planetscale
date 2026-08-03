---
url: https://planetscale.com/docs/vitess/tutorials/deployments/deploy-to-netlify
title: "Deploy To Netlify"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

This doc is intended for users that are manually storing a connection string in an environment variable in Netlify. If you want to use the Netlify integration, which handles this for you, see the [PlanetScale integration in the Netlify docs](https://docs.netlify.com/integrations/planetscale-integration).

## Prerequisites

- A PlanetScale database — If you haven’t created a database, refer to our [PlanetScale quickstart guide](../planetscale-quick-start-guide.md) to get started
- A [Netlify account](https://netlify.com/)
- A project deployed to Netlify — If you’re just poking around and don’t already have an application to deploy, you can use our [Next.js + PlanetScale sample](../connect-nextjs-app.md)

## Connecting your PlanetScale database to your Netlify application

### Get your connection string from PlanetScale

### Copy environment variables to Netlify

For example, if you’re using Prisma, your connection string will look similar to this:

```shellscript
DATABASE_URL='mysql://xxxxxxxxx:************@xxxxxxxxxx.us-east-3.psdb.cloud/my-database?sslaccept=strict'
```

Your environment variable name will be the same in your application’s code. We used `DATABASE_URL` as an example, but this can be given a different name.

In Netlify, you’ll set it as follows:

- **Key** = `DATABASE_URL`
- **Value** = `mysql://xxxxxxxxx:************@xxxxxxxxxx.us-east-3.psdb.cloud/my-database?sslaccept=strict`

The credentials are blurred for the example, but when you paste them in, use the actual values.

![Netlify dashboard - Environment variables](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/deploy-to-netlify/environment-variables.png?w=2500&fit=max&auto=format&n=AJPY38bILe2zenXX&q=85&s=d0cfec940f13e3797e3cf3d35ba21ec2)

Netlify dashboard - Environment variables

After you have saved, you will need to rebuild the site with the new environment variable.

## What’s next?

Learn more about how PlanetScale allows you to make [non-blocking schema changes](../../schema-changes.md) to your database tables without locking or causing downtime for production databases.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
