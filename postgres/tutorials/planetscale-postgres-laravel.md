---
url: https://planetscale.com/docs/postgres/tutorials/planetscale-postgres-laravel
title: "Planetscale Postgres Laravel"
description: ""
access_date: 2026-08-14T20:39:03.125Z
current_date: 2026-08-14T20:39:03.125Z
---

Laravel is a popular, fully-featured PHP framework for building web applications.

Already created a PlanetScale Postgres database? [Jump straight to integration instructions](#integrate-with-laravel).

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

![Database dashboard](https://mintlify.s3.us-west-1.amazonaws.com/planetscale-2/postgres/tutorials/new-database.png)

Database dashboard

#### CLI

Create a new “Default role” in your PlanetScale CLI to create connection credentials for your database branch.

Passwords are shown only once. If you lose your record of the password, you must [reset the password](../connecting/roles.md).

## Integrate with Laravel

Laravel contains a built-in ORM, [Eloquent](https://laravel.com/docs/master/eloquent), that allows you to interact with your database using PHP objects.

### Step 1: Add credentials to.env

For local development, you can place your credentials in a `.env` file. For production, we recommend setting your credentials as environment variables wherever your application is deployed.

Replace the placeholders below with the role credentials created in the previous section.

.env

```shellscript
DB_CONNECTION=pgsql
DB_HOST=<host>
DB_PORT=<port>
DB_DATABASE=<database>
DB_USERNAME=<username>
DB_PASSWORD=<password>
```

Choose the appropriate **port** for your use case. Learn more about [Direct vs PgBouncer connections](../connecting/quickstart.md#connection-types%3A-direct-vs-pgbouncer).

## PgBouncer

Port `6432` enables a lightweight connection pooler for PostgreSQL. This facilitates better performance when there are many simultaneous connections.

## Direct

Port `5432` connects directly to PostgreSQL. Total connections are limited by your cluster’s `max_connections` setting.

Both connection types will disconnect when your database restarts or handles a failover scenario.

When using [PgBouncer](../connecting/pgbouncer.md) (port `6432`), set `DB_POOLED=true` and ensure your `pgsql` connection in `config/database.php` includes `'pooled' => env('DB_POOLED', false)`. This option is available in Laravel 13.17.0 and later. See [Laravel’s pooled PostgreSQL connections](https://laravel.com/docs/13.x/database#pooled-postgresql-connections).

### Step 2: Create a database connection

The following example loads in your credentials from your `.env` file, establishes a connection, and, if successful, prints what database you are connected to. Add the following code to your `routes/web.php` file:

routes/web.php

```php
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Route;

Route::get('/test-database', function () {
    try {
        DB::connection()->getPdo();
        print_r("Connected successfully to: " . DB::connection()->getDatabaseName());
    } catch (\Exception $e) {
        die("Could not connect to the database. Please check your configuration. Error: " . $e);
    }
});
```

Run the following to start the Laravel server, then navigate to `http://localhost:8000/test-database` to see the results.

Terminal

```shellscript
php artisan serve
```

See the [Laravel documentation on databases](https://laravel.com/docs/master/database) for more information.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
