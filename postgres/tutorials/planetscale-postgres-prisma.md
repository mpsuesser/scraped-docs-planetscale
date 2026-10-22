---
url: https://planetscale.com/docs/postgres/tutorials/planetscale-postgres-prisma
title: "Planetscale Postgres Prisma"
description: ""
access_date: 2026-10-22T18:21:49.469Z
current_date: 2026-10-22T18:21:49.469Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale Postgres with Prisma

> Create a new Postgres database and integrate it with Prisma.

Prisma ORM allows you to write type-safe SQL queries and automatically generate migrations.

Already created a PlanetScale Postgres database? [Jump straight to integration instructions](#integrate-with-prisma).

We'll cover:

* Creating a new Postgres database
* Cluster configuration options
* Connecting to your database

## Prerequisites

Before you begin, make sure you have a [PlanetScale account](https://auth.planetscale.com/sign-up). After you create an account, you'll be prompted to create a new organization, which is essentially a container for your databases, settings, and members.

After creating your organization, it's important to understand the relationship between databases, branches, and clusters.

* **Database**: Your overall project (e.g., "my-ecommerce-app")
* **Branch**: Isolated database deployments that provide you with separate environments for development and testing, as well as restoring from backups - [learn more about branching](https://planetscale.com/docs/postgres/branching.md)
* **Cluster**: The underlying compute and storage infrastructure that powers each branch

PlanetScale Postgres clusters use real Postgres in a [high-availability architecture with one primary and two replicas](https://planetscale.com/docs/postgres/postgres-architecture.md#cluster-design).

## Create a new database

**Dashboard**

[Create a database on PlanetScale](https://www.youtube-nocookie.com/embed/6BBrgJcpTBY?rel=0)

### Step 1: Navigate to database creation

**Step 1**

Log in to your [PlanetScale dashboard](https://app.planetscale.com)

**Step 2**

Select your organization from the dropdown

**Step 3**

Click **"New database"** button or navigate to `/new`

### Step 2: Choose database engine

**Step 1**

On the database creation form, you'll see two engine options:

* **Vitess** (MySQL-compatible)
* **Postgres** (PostgreSQL-compatible)

**Step 2**

Select **Postgres** to create a PostgreSQL database

### Step 3: Configure your database cluster

**Step 1**

**Database name**: Enter a unique name for your database

> [!NOTE]
>
> This "name" is referenced in the PlanetScale Dashboard and APIs and not created as a logical database inside of Postgres.

**Step 2**

**Region**: Choose the primary region where your database will be hosted. For the lowest latency, select a region near you or your application's hosting location.

**Step 3**

**Cluster configuration**: Select your preferred cluster size and [CPU architecture](https://planetscale.com/docs/postgres/cluster-configuration/cpu-architectures.md)

### Step 4: Create the database cluster

**Step 1**

Review your configuration settings

**Step 2**

Click **"Create database"** to provision your Postgres database

**Step 3**

Your database will be created with a `main` branch by default

**CLI**

If you are creating an automation, or are an LLM, you may prefer to create new databases using the PlanetScale CLI.

### Step 1: Install the CLI

**Step 1**

Check to see if the PlanetScale CLI is installed already by running:

```bash theme={null}
pscale --version
```

**Step 2**

Alternatively, follow the instructions in the [PlanetScale CLI GitHub repository](https://github.com/planetscale/cli#installation)

### Step 2: Log in or sign up

**Step 1**

If you do not already have a PlanetScale account, [sign up for one](https://auth.planetscale.com/sign-up) by running:

```bash theme={null}
pscale signup
```

**Step 2**

Log in to the PlanetScale CLI by running:

```bash theme={null}
pscale auth login
```

You’ll be taken to a screen in the browser where you’ll be asked to confirm the code displayed in your terminal. If the confirmation codes match, click the **"Confirm code"** button in your browser.

You should receive the message "Successfully logged in" in your terminal. You can now close the confirmation page in the browser and proceed in the terminal.

### Step 3: Create a database

**Step 1**

Configure the CLI to use the **organization** in which you want to create the database if you have more than one. List organizations by running:

```bash theme={null}
pscale org list
```

Switch organizations by running:

```bash theme={null}
pscale org switch <ORGANIZATION_NAME>
```

**Step 2**

Find the **region** closest to your application's hosting.

List available regions by running:

```bash theme={null}
pscale region list
```

> [!NOTE]
>
> If you do not specify a **region**, your database will automatically be deployed to `us-east` (US East — Northern Virginia).

**Step 3**

Create a new Postgres database by running:

```bash theme={null}
pscale database create <DATABASE_NAME>  --region <REGION_SLUG> --engine postgres
```

> [!NOTE]
>
> Your **database name** can contain lowercase, alphanumeric characters, or underscores. We allow dashes, but don't recommend them, as they may need to be escaped in some instances.

## What happens during creation

When you create a Postgres database cluster, PlanetScale automatically:

* Provisions a PostgreSQL cluster in your selected region
* Creates the initial `main` branch
* Prepopulates Postgres with required default databases
* Sets up monitoring and metrics collection
* Configures backup and high availability settings

## Create credentials and connect

> **Postgres**
>
> Create **role credentials** with [`pscale role`](../../cli/role.md), then connect using a [connection string](https://planetscale.com/docs/postgres/connecting/quickstart.md).
>
> `pscale connect` is not supported.

> **Vitess / MySQL**
>
> Create a **branch password** with [`pscale password`](../../cli/password.md), then connect with a [connection string](https://planetscale.com/docs/vitess/connecting/connection-strings.md) or [`pscale connect`](../../cli/connect.md).

In this section you'll create the "Default role" in your PlanetScale dashboard to create connection credentials for your database branch.

> [!NOTE]
>
> The "Default role" is meant purely for administrative purposes. You can only create one and it has significant privileges for your database cluster and you should treat these credentials carefully. After completing this quickstart, it is *strongly recommended* that you [create another role](https://planetscale.com/docs/postgres/connecting/roles.md) for your application use-cases.

**Dashboard**

![Database dashboard](https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/tutorials/new-database.png?fit=max\&auto=format\&n=NAfHErQ6-kE8SaMw\&q=85\&s=0dc877c9af2d9be252c165c0c16274b8)

**Step 1**

Navigate to your database in the [PlanetScale dashboard](https://app.planetscale.com)

**Step 2**

Click on the **"Connect"** button in the top right

**Step 3**

Select **"Default role"**

**Step 4**

Click **"Create default role"**. A new default role is created for your database branch.

**Step 5**

Record the "Host", "Username", and "Password" for the "Default role" someplace secure.

![Create a new role](https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/tutorials/create-role.png?fit=max\&auto=format\&n=NAfHErQ6-kE8SaMw\&q=85\&s=dd760b8bac6d56d7ea96ef3bb981c666)

**Step 6**

You can generate connection strings under **"How are you connecting?"** for major languages, frameworks, and tools.

![Connection strings](https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/tutorials/langs-and-frames.png?fit=max\&auto=format\&n=NAfHErQ6-kE8SaMw\&q=85\&s=709ecbea3be475aeadf8c19fae75dc87)

Your connection details will include:

* **Host**: the DNS name of your database endpoint
* **Username**: automatically formatted for routing to the correct `branch`
* **Password**: A securely generated password
* **Database**: `postgres` (default database)
* **Port**: `5432` (standard PostgreSQL port) or `6432` (for using [PgBouncer](https://planetscale.com/docs/postgres/connecting/pgbouncer.md))

**CLI**

Create a new "Default role" in your PlanetScale CLI to create connection credentials for your database branch.

**Step 1**

Run the following command to create the "Default role" for your database branch.

```bash theme={null}
pscale role reset-default <DATABASE_NAME> <BRANCH_NAME>
```

**Step 2**

Record the "Host", "Username", and "Password" for the "Default role" somewhere secure.

> [!NOTE]
>
> Passwords are shown only once. If you lose your record of the password, you must [reset the password](https://planetscale.com/docs/postgres/connecting/roles.md).

## Integrate with Prisma

Installation instructions vary depending on your choice of JavaScript runtime or framework. The following are general instructions for installing Prisma and connecting to your database.

### Step 1: Install packages and initialize

Run the following to install `prisma`:

```bash Terminal theme={null}
npm install prisma
```

Next, set up Prisma in your application with the following command:

```bash Terminal theme={null}
npx prisma init
```

### Step 2: Add credentials to .env

For local development, you can place your credentials in a `.env` file. For production, we recommend setting your credentials as environment variables wherever your application is deployed.

Replace the placeholders below with the role credentials created in the previous section.

```bash .env theme={null}
DATABASE_URL='postgresql://{username}:{password}@{host}:{port}/{database}?sslmode=verify-full'
```

Choose the appropriate **port** for your use case. Learn more about [Direct vs PgBouncer connections](https://planetscale.com/docs/postgres/connecting/quickstart.md#connection-types%3A-direct-vs-pgbouncer).

> **PgBouncer**
>
> Port `6432` enables a lightweight connection pooler for PostgreSQL. This facilitates better performance when there are many simultaneous connections.

> **Direct**
>
> Port `5432` connects directly to PostgreSQL. Total connections are limited by your cluster's `max_connections` setting.

> [!NOTE]
>
> Both connection types will disconnect when your database restarts or handles a failover scenario.

### Step 3: Create a database connection

By default, Prisma should create a `prisma/schema.prisma` file with the `postgresql` provider.

```prisma prisma/schema.prisma theme={null}
generator client {
  provider = "prisma-client"
  output   = "../generated/prisma"
}

datasource db {
  provider = "postgresql"
}
```

As well as a `prisma.config.ts` file which reads `DATABASE_URL` from your `.env` file.

```ts prisma.config.ts theme={null}
import 'dotenv/config'
import { defineConfig, env } from 'prisma/config'

export default defineConfig({
  schema: 'prisma/schema.prisma',
  migrations: {
    path: 'prisma/migrations',
  },
  datasource: {
    url: env('DATABASE_URL'),
  },
})
```

[Learn how to query your replicas directly](https://planetscale.com/docs/postgres/scaling/replicas.md)

For more information on using Prisma including creating schemas and performing queries, [refer to their documentation](https://www.prisma.io/orm).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
