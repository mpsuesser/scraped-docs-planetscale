---
url: https://planetscale.com/docs/postgres/integrations/workos-sync-engine
title: "Workos Sync Engine"
description: ""
access_date: 2026-11-19T17:53:01.748Z
current_date: 2026-11-19T17:53:01.748Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# WorkOS Sync Engine integration

> Mirror WorkOS data into PlanetScale Postgres with the @workos/sync-postgres package and query it with SQL.

This page covers Postgres.

Vitess version: [Vitess](https://planetscale.com/docs/vitess/integrations/workos-sync-engine.md)

[WorkOS Sync Engine](https://workos.com/docs/events/data-syncing/database) keeps a copy of your WorkOS data, such as users and organizations, in your own database.
It connects to PlanetScale Postgres over a standard Postgres connection with the `@workos/sync-postgres` package.
For the full command and configuration reference, see the [`@workos/sync-postgres` README](https://github.com/workos/sync-engine/tree/main/packages/sync-postgres).

## Set up Sync Engine

### Requirements

* A PlanetScale Postgres database
* A WorkOS account and API key
* Node.js 22 or later, to run the `workos-sync-postgres` command

#### Network access

Sync Engine runs on your own infrastructure and connects to PlanetScale from there.
If your database uses [IP restrictions](https://planetscale.com/docs/postgres/connecting/ip-restrictions.md), allow the IP addresses of the host that runs the sync.

### Create a role

Sync Engine's `init` command creates a `workos` schema, so the [role](https://planetscale.com/docs/postgres/connecting/roles.md) it uses needs `CREATE` on the database, which the `postgres` permission grants.
Without it, `init` fails with `permission denied for database postgres`.

1. In PlanetScale, navigate to your database and branch, and click the "**Connect**" button.
2. Click "**Create new role**" and keep "**User-defined role**" selected.
3. Select the `postgres` permission.
4. Click "**Create role**" and copy the connection URI. PlanetScale only shows the password once.

### Set the connection string

If the connection URI includes `sslrootcert=system`, remove it and keep `sslmode=verify-full`.
The `pg` driver that Sync Engine uses reads `sslrootcert` as a file name, so every command otherwise fails with `ENOENT: no such file or directory, open 'system'`.
Without it, `sslmode=verify-full` still verifies the server's certificate and host name.

### Run the sync

1. Install the package:

   ```bash theme={null}
   npm install @workos/sync-postgres
   ```

2. Set your environment variables:

   ```bash theme={null}
   export DATABASE_URL='postgresql://<username>:<password>@<host>:5432/postgres?sslmode=verify-full'
   export WORKOS_API_KEY='sk_...'
   export WORKOS_ENVIRONMENT_NAME='<environment-name>'
   ```

3. Run `init`, `backfill`, and `listen`:

   ```bash theme={null}
   npx workos-sync-postgres init
   npx workos-sync-postgres backfill
   npx workos-sync-postgres listen
   ```

> [!WARNING]
>
> Set `WORKOS_ENVIRONMENT_NAME` to the name of the WorkOS environment your API key belongs to, such as `staging`, before you run any command.
> Use the same value for every command.
> Sync Engine labels every row with `_env = 'production'` by default, even when you use a test API key.
> Changing the label later doesn't relabel existing rows.
> Sync Engine writes a second copy of each row, and the `_live` views return both.

## Sync overview

* `init` creates the `workos` schema with its tables and views.
* `backfill` loads the data that already exists in WorkOS.
* `listen` applies new WorkOS events as they happen, and keeps running until you stop it.

## Query the mirror

Each mirror table in the `workos` schema except `events` and `auth_attempts` has a `_live` view that leaves out deleted rows.
Query the views:

```sql theme={null}
SELECT id, email FROM workos.users_live;
```

## Branches

Each [branch](https://planetscale.com/docs/postgres/branching.md) needs its own credentials.
A branch created from the **Branches** page has no schema or data, so create a role on it and run `init` and `backfill`.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
