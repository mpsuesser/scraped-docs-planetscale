---
url: https://planetscale.com/docs/neki/guides/laravel
title: "Laravel"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

Laravel connects to Neki through its Postgres database driver. Eloquent and the query builder work through the same connection.

Before you start, you need:

- A Neki database with a ready branch.
- An application [role](../connecting/roles.md) for that branch. Use `pg_read_all_data` for read traffic and add `pg_write_all_data` when the application writes rows. These inherited data roles do not grant `CREATE` or `ALTER`, so application credentials cannot change the schema.
- A separate migration role, if you run schema changes against the branch. DDL requires the `postgres` inherited role. Neki restricts execution of the cross-router DDL barrier function `__neki.wait_for_ddl` to its `neki_viewer` role, so add `neki_viewer`, which requires `pg_read_all_data`, to the same role. Keep the migration credentials out of the application’s runtime configuration.
- The **Primary** connection details from the database’s **Connect** page.

Use the complete generated username. It contains the branch routing information required by Neki.

## Enable the Postgres PDO driver

Laravel’s Postgres connector requires PHP’s `pdo_pgsql` extension. Install or enable the extension for every environment that runs the application, then confirm that PHP loaded it:

```shellscript
php --ri pdo_pgsql
```

## Add the connection details

Add the values from the **Connect** page to `.env`. Set `DB_SSLROOTCERT` to the path of the system CA bundle:

.env

```shellscript
DB_CONNECTION=pgsql
DB_HOST=<HOST>
DB_PORT=5432
DB_DATABASE=postgres
DB_USERNAME=<USERNAME>
DB_PASSWORD=<PASSWORD>
DB_SSLMODE=verify-full
DB_SSLROOTCERT=<SYSTEM_CA_PATH>
```

Do not commit this file. Set the same values through your deployment platform’s secret manager in production.

## Configure the Postgres connection

Make sure the `pgsql` entry in `config/database.php` reads the TLS settings:

config/database.php

```php
'pgsql' => [
    'driver' => 'pgsql',
    'host' => env('DB_HOST'),
    'port' => env('DB_PORT', '5432'),
    'database' => env('DB_DATABASE', 'postgres'),
    'username' => env('DB_USERNAME'),
    'password' => env('DB_PASSWORD'),
    'charset' => 'utf8',
    'prefix' => '',
    'prefix_indexes' => true,
    'search_path' => 'public',
    'sslmode' => env('DB_SSLMODE', 'verify-full'),
    'sslrootcert' => env('DB_SSLROOTCERT'),
],
```

Confirm the connection with Tinker:

```shellscript
php artisan tinker
```

```php
DB::select('SELECT current_database() AS database');
```

Framework migration commands send DDL through a normal Neki connection. The router fans that DDL out to the managed shards, but it does not create a managed schema-change workflow. Use a [schema-change workflow](../schema-changes.md) when you want Neki to prepare and coordinate an Online DDL change.

Run migrations with the migration role described in the prerequisites. An application role that inherits only `pg_read_all_data` and `pg_write_all_data` cannot run `CREATE` or `ALTER`, so a migration that uses application credentials fails on its first DDL statement.

Apply migration DDL with `psql` and the migration role so the client prints PostgreSQL notices:

```shellscript
psql "$MIGRATION_DATABASE_URL" -f <MIGRATION_FILE>
```

The router that commits the DDL emits a `NOTICE` containing the barrier call for that transaction:

```text
NOTICE: DDL is committed and the change is visible on this router; other routers may not see it yet. To wait until it is visible on every router, use __neki.wait_for_ddl(42, 7)
```

The schema and cluster versions belong to that transaction, and the notice is the only place Neki reports them. Before you deploy application code that depends on the new schema, run the emitted call verbatim on a primary connection that uses the migration role:

```sql
SELECT __neki.wait_for_ddl(42, 7);
```

The call returns after every router has applied both versions. A successful migration only confirms that the DDL committed and is visible through the router that ran it; it does not replace this cross-router barrier.

Some framework migration commands discard notices. Generate migration SQL with the framework, but apply it with `psql` when the framework cannot preserve the notice. If the notice is lost, its version pair cannot be reconstructed.

## Route a Laravel process to replicas

Laravel 12’s `PostgresConnector` does not add arbitrary libpq startup parameters from `config/database.php` to its PDO DSN. Set `PGOPTIONS` in the process environment before PHP starts so `pdo_pgsql` sends the Neki target when it opens each connection:

```shellscript
PGOPTIONS='-c __neki.target=REPLICA' php artisan tinker
```

Confirm the target in Tinker:

```php
DB::scalar('SHOW __neki.target');
// "REPLICA"
```

In production, set the same `PGOPTIONS` value on a dedicated read-only worker or service. The setting applies to every `pdo_pgsql` connection created by that PHP process, so do not set it on a process that also handles writes. Replica-routed connections reject inserts, updates, and deletes, and reads can return stale data. See [Primary and replica routing](../connecting.md#primary-and-replica-routing).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
