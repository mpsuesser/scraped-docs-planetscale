---
url: https://planetscale.com/docs/postgres/tutorials/planetscale-postgres-quickstart
title: "Planetscale Postgres Quickstart"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

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

### Connection strings

PlanetScale provides connection strings in various formats for different frameworks and languages. Here are some common examples:

General PostgreSQL URL

```shellscript
postgresql://postgres.{branch-id}:{password}@{host}.horizon.psdb.cloud:5432/postgres?sslmode=verify-full
```

psql command line

terminal

```shellscript
psql 'host={host} port=5432 user={user} password={password} dbname=postgres sslnegotiation=direct sslmode=verify-full sslrootcert=system'
```

Node.js (node-postgres)

db.ts

```typescript
const { Client } = require('pg');
const client = new Client({
  host: '{host}',
  port: 5432,
  user: '{user}',
  password: '{your-password}',
  database: 'postgres',
  ssl: { rejectUnauthorized: true }
});
```

Rails

```ruby
planetscale:
  username: {user}
  host: {host}
  port: 5432
  database: postgres
  password: {your-password}
```

## Default databases

When your database branch is first created, there are a number of default databases that are created at the same time.

```sql
postgres=> \l
                    List of databases
      Name       |      Owner       | Removed columns...
------------------+------------------+-------------------
 postgres         | postgres         | ...
 pscale_admin     | pscale_admin     | ...
 pscale_exporter  | pscale_admin     | ...
 pscale_pgbouncer | pscale_admin     | ...
 template0        | pscale_admin     | ...
 template1        | pscale_superuser | ...
(6 rows)
```

These databases include those that are used by various PlanetScale platform features such as metrics and logs collection, backups, Insights, or are used by PGBouncer (as examples). They cannot be removed.

| Database | Purpose |
| --- | --- |
| postgres | Default user database |
| pscale\_admin | PlanetScale platform |
| pscale\_exporter | PlanetScale platform |
| pscale\_pgbouncer | PlanetScale platform |
| `template0` and `template1` | [Postgres database defaults](https://www.postgresql.org/docs/current/manage-ag-templatedbs.html) |

## Security requirements

All connections to Postgres databases require:

- **SSL/TLS encryption** - Use `sslmode=verify-full` (recommended, with `sslrootcert=system`); `sslmode=require` is the weaker minimum
- **Certificate verification** - Connections verify PlanetScale’s SSL certificates
- **Secure passwords** - Generated passwords use cryptographically secure random generation

### Password management

- **Password reset**: You can [reset your default user password](../connecting/roles.md) anytime from the dashboard
- **No password rotation required**: Passwords don’t expire unless you set them to
- **Single credential per branch**: Each branch has one default user credential

## Next steps

Your database is ready. See [Next steps](../day-one-with-planetscale-postgres.md) for the top platform features to enable before going to production.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
