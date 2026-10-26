---
url: https://planetscale.com/docs/vitess/tutorials/connect-any-application
title: "Connect Any Application"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Connect any application to PlanetScale

This page covers Vitess.

## Introduction

In this tutorial, you'll learn how to connect any application to your PlanetScale database.

If you're just getting started and still need to set up a database, we recommend starting with the [PlanetScale quick start guide](https://planetscale.com/docs/vitess/tutorials/planetscale-quick-start-guide.md) first. We also have language/framework-specific guides under "**Integration guides**" if you prefer a more detailed walk-through.

PlanetScale uses [database branches](https://planetscale.com/docs/vitess/schema-changes/branching.md) to create a development-friendly workflow. Your database is initially created with a default branch, `main`, which is meant to serve as a production database branch. Production branches are highly available databases intended for production traffic. They are automatically provided with an additional replica to resist outages, enabling zero-downtime failovers.

You can connect to a *production* or *development* database branch. We recommend creating and connecting to a *development* branch while in *development*, as it allows you to make schema changes without affecting production. Your production application, however, should be connected to your production database branch. Check out our [Branching guide](https://planetscale.com/docs/vitess/schema-changes/branching.md) for more information about the branching workflow.

> [!NOTE]
>
> By default, production branches have safe migrations turned off. This means that any schema changes you make will be applied immediately. Once you are ready to go to production, we recommend turning on safe migrations if you want to make non-blocking schema changes. Check out our [Safe migrations documentation](https://planetscale.com/docs/vitess/schema-changes/safe-migrations.md) for more information.

There are two ways to connect your app to PlanetScale. Both are covered below.

## Option 1: Connect with username and password (Recommended)

This section will show you how to create a username and password for your branch and use those credentials to connect to your database. This is the recommended way to connect.

There are two ways to generate a new username and password for your branch:

* In the PlanetScale dashboard
* With the PlanetScale CLI

### Generate credentials in the PlanetScale dashboard

**Step 1**

On the database dashboard page, click the **Connect** button.

**Step 2**

Select the database branch that you want to create a password for.

**Step 3**

Click the **Create password** button.

**Step 4**

Select the applicable language or framework for your application or click "Other".

**Step 5**

Copy the credentials.

**Step 6**

Paste them in your application's MySQL configuration file (often just a `.env` file). The layout and name of this file will vary depending on the application language, but it may look something like this:

```bash theme={null}
DATABASE=<DATABASE_NAME>
USERNAME=<USERNAME>
HOST=<HOST_NAME>
PASSWORD=<PASSWORD>
SSL= // more information about this in next step
```

Check out our language integration guides in the side navigation for more explicit instructions.

**Step 7**

To ensure a secure connection, you must validate the server-side certificate from PlanetScale. This configuration depends on your application, but often it just means adding a line to your `.env` file similar to this:

```bash theme={null}
MYSQL_ATTR_SSL_CA=/etc/ssl/cert.pem
```

The path to the certificate depends on your system. The above example shows the path for macOS, but you can find others in our [Secure connections documentation](https://planetscale.com/docs/vitess/connecting/secure-connections.md#ca-root-configuration).

Again, the variable name here, `MYSQL_ATTR_SSL_CA`, is just an example. The actual name and location for it will depend on the application.

If you're **unsure what to put here**, we recommend selecting your application's language or framework from the 2nd section of the Connect page (see step 4 above) and copy the credentials from there. This includes the necessary SSL configuration variables and shows what files they belong in. Additionally, we show you the correct certificate path by default based on your system.

### Generate credentials in the PlanetScale CLI

If you prefer working from the CLI, you can quickly spin up new credentials there. Make sure you have the [CLI set up](https://planetscale.com/docs/cli/planetscale-environment-setup.md) first.

**Step 1**

Run the following command in the CLI to create a new username and password for your branch.

```bash theme={null}
pscale password create <DATABASE_NAME> <BRANCH_NAME> <PASSWORD_NAME>
```

> [!NOTE]
>
> The `PASSWORD_NAME` value represents the name of the username and password being generated. You can have multiple credentials for a branch, so this gives you a way to categorize them. To manage your passwords in the dashboard, go to your database dashboard page, click "Settings", and then click "Passwords".

**Step 2**

Take note of the values returned. You won't be able to see this password again.

```
Password production-password was successfully created.
Please save the values below, as they will not be shown again.

  NAME                  USERNAME       ACCESS HOST URL                     ROLE               PASSWORD
 --------------------- ------------- --------------------------------- ------------------ --------------------------------
  production-password   xxxxxxxxxx   xxxxxxxxxx.us-east-2.psdb.cloud   Can Read & Write   pscale_pw_<PLANETSCALE_DATABASE_PASSWORD>
```

**Step 3**

Paste the values from the console output into your application's MySQL configuration file. The layout and name of this file will vary depending on the application language, but it may look something like this:

```bash theme={null}
DATABASE=<DATABASE_NAME>
USERNAME=<USERNAME>
HOST=<ACCESS HOST URL>
PASSWORD=<PASSWORD>
SSL= // This is covered in the next step
```

**Step 4**

To ensure a secure connection, you must validate the server-side certificate from PlanetScale. This configuration depends on your application, but often it just means adding a line to your `.env` file similar to this:

```bash theme={null}
MYSQL_ATTR_SSL_CA=/etc/ssl/cert.pem
```

The path to the certificate depends on your system. The above example shows the path for macOS, but you can find others in our [Secure connections documentation](https://planetscale.com/docs/vitess/connecting/secure-connections.md#ca-root-configuration).

Again, the variable name here, `MYSQL_ATTR_SSL_CA`, is just an example. The actual name and location for it will depend on the application.

If you're **unsure what to put here**, we recommend selecting your application's language from the dropdown in the PlanetScale dashboard (see step 3 from the previous section) and copying the credentials from there. This includes the necessary SSL configuration variables and shows what files they belong in. Additionally, we show you the correct certificate path by default based on your system.

## Option 2: Connect using the PlanetScale proxy

Another way to connect your application to your PlanetScale database *during development* is using the PlanetScale proxy. You won't have to fiddle with configuring any credential details, as that's handled by PlanetScale. It's as simple as a single CLI command.

You'll use the CLI to establish a secure connection to PlanetScale. It will listen on a local port that your application can connect to. The main benefit of this method is you won't have to generate and remember multiple passwords every time you're creating or switching to a new branch.

**Step 1**

Make sure you have [the CLI set up](https://planetscale.com/docs/cli/planetscale-environment-setup.md), and then run the following command:

```bash theme={null}
pscale connect <DATABASE_NAME> <BRANCH_NAME>
```

This establishes a secure connection and opens a port on your local machine that you can use to connect to any MySQL client.

**Step 2**

Take note of the address it returns to you. By default, it is `127.0.0.1:3306`. The CLI will use a different port if `3306` is unavailable.

**Step 3**

In your application's MySQL configuration file, use the following to connect:

```bash theme={null}
DATABASE=<DATABASE_NAME>
HOST=127.0.0.1
PORT=3306 // use the value that was returned in the console
```

Your application should now be connected to the specified PlanetScale database branch!

## What's next?

Once your application is connected to a development database branch, you can make schema changes in an isolated development environment without worrying about affecting production. Additionally, we recommend that [safe migrations](https://planetscale.com/docs/vitess/schema-changes/safe-migrations.md) be enabled on your production database branch, which allows you to make [non-blocking schema changes](https://planetscale.com/docs/vitess/schema-changes.md) without locking or causing downtime.

### PlanetScale workflow

Here's the general workflow that you'll go through to get schema changes from development to production:

**Step 1**

Follow this guide to connect to a development branch.

**Step 2**

Modify your schema as needed.

**Step 3**

Test them locally or in your staging environment.

**Step 4**

Once satisfied and ready to deploy your changes to production, [create a deploy request](https://planetscale.com/docs/vitess/schema-changes/deploy-requests.md).

**Step 5**

You or your team can review and approve the schema changes.

**Step 6**

Deploy your deploy request to production.

**Step 7**

Bonus: If you realize you made a mistake, you can click "Revert changes" to [undo a schema change](https://planetscale.com/docs/vitess/schema-changes/deploy-requests.md#revert-a-schema-change).

> [!NOTE]
>
> Note: you must already have [a production branch](https://planetscale.com/docs/vitess/schema-changes/branching.md#promote-a-branch-to-production) in place to create a deploy request.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
