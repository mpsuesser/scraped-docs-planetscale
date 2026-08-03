---
url: https://planetscale.com/docs/postgres/tutorials/planetscale-postgres-bun
title: "Planetscale Postgres Bun"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

Bun provides native support for Postgres databases.

Already created a PlanetScale Postgres database? [Jump straight to integration instructions](#integrate-with-bun).

We’ll cover:

- Creating a new Postgres database
- Cluster configuration options
- Connecting to your database

## Prerequisites

Before you begin, make sure you have a [PlanetScale account](https://auth.planetscale.com/sign-up). After you create an account, you’ll be prompted to create a new organization, which is essentially a container for your databases, settings, and members.

After creating your organization, it’s important to understand the relationship between databases, branches, and clusters.

- **Database**: Your overall project (e.g., “my-ecommerce-app”)
- **Branch**: Isolated database deployments that provide you with separate environments for development and testing, as well as restoring from backups - [learn more about branching](../branching.md)
- **Cluster**: The underlying compute and storage infrastructure that powers each branch

PlanetScale Postgres clusters use real Postgres in a [high-availability architecture with one primary and two replicas](../postgres-architecture.md#cluster-design).

## Create a new database

#### Dashboard

### Step 1: Navigate to database creation

### Step 2: Choose database engine

### Step 3: Configure your database cluster

### Step 4: Create the database cluster

#### CLI

If you are creating an automation, or are an LLM, you may prefer to create new databases using the PlanetScale CLI.

### Step 1: Install the CLI

### Step 2: Log in or sign up

### Step 3: Create a database

## What happens during creation

When you create a Postgres database cluster, PlanetScale automatically:

- Provisions a PostgreSQL cluster in your selected region
- Creates the initial `main` branch
- Prepopulates Postgres with required default databases
- Sets up monitoring and metrics collection
- Configures backup and high availability settings

## Create credentials and connect

## Postgres

Create **role credentials** with [`pscale role`](../../cli/role.md), then connect using a [connection string](../connecting/quickstart.md).

`pscale connect` is not supported.

## Vitess / MySQL

Create a **branch password** with [`pscale password`](../../cli/password.md), then connect with a [connection string](../../vitess/connecting/connection-strings.md) or [`pscale connect`](../../cli/connect.md).

In this section you’ll create the “Default role” in your PlanetScale dashboard to create connection credentials for your database branch.

The “Default role” is meant purely for administrative purposes. You can only create one and it has significant privileges for your database cluster and you should treat these credentials carefully. After completing this quickstart, it is *strongly recommended* that you [create another role](../connecting/roles.md) for your application use-cases.

#### Dashboard

![Database dashboard](https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/tutorials/new-database.png?w=2500&fit=max&auto=format&n=NAfHErQ6-kE8SaMw&q=85&s=5f0bb654347b6ee3e49b4d6ac19cc962)

Database dashboard

#### CLI

Create a new “Default role” in your PlanetScale CLI to create connection credentials for your database branch.

Passwords are shown only once. If you lose your record of the password, you must [reset the password](../connecting/roles.md).

## Integrate with Bun

### Step 1: Initialization

You will need to have Bun installed. See the Bun docs for [installation instructions](https://bun.sh/docs/installation).

If you do not already have a Bun project, initialize one with the following command:

Terminal

```shellscript
bun init
```

### Step 2: Add credentials to.env

For local development, you can place your credentials in a `.env` file. For production, we recommend setting your credentials as environment variables wherever your application is deployed.

Bun will automatically load your credentials from the `.env` file.

Replace the placeholders below with the role credentials created in the previous section.

.env

```shellscript
DATABASE_URL='postgresql://{username}:{password}@{host}:{port}/{database}?sslmode=verify-full'
```

Choose the appropriate **port** for your use case. Learn more about [Direct vs PgBouncer connections](../connecting/quickstart.md#connection-types%3A-direct-vs-pgbouncer).

## PgBouncer

Port `6432` enables a lightweight connection pooler for PostgreSQL. This facilitates better performance when there are many simultaneous connections.

## Direct

Port `5432` connects directly to PostgreSQL. Total connections are limited by your cluster’s `max_connections` setting.

Both connection types will disconnect when your database restarts or handles a failover scenario.

### Step 3: Create a database connection

To run a query, you can use the `sql` template literal. As Postgres is the default choice in Bun, using it requires no additional configuration.

index.ts

```typescript
import { sql } from 'bun'

const now = await sql\`
  SELECT NOW()
\`

console.log(now)
```

From the command line, run the following command to execute the script:

Terminal

```shellscript
bun index.ts
```

See the [Bun SQL documentation](https://bun.sh/docs/runtime/sql#postgresql) for more information about queries and mutations.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
