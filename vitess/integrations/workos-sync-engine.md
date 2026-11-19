---
url: https://planetscale.com/docs/vitess/integrations/workos-sync-engine
title: "Workos Sync Engine"
description: ""
access_date: 2026-11-19T17:53:01.748Z
current_date: 2026-11-19T17:53:01.748Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# WorkOS Sync Engine integration

> Mirror WorkOS data into PlanetScale Vitess with the @workos/sync-mysql package and query it with SQL.

This page covers Vitess.

Postgres version: [Postgres](https://planetscale.com/docs/postgres/integrations/workos-sync-engine.md)

[WorkOS Sync Engine](https://workos.com/docs/events/data-syncing/database) keeps a copy of your WorkOS data, such as users and organizations, in your own database.
It runs on your own infrastructure and connects to PlanetScale Vitess over the MySQL protocol with the `@workos/sync-mysql` package.
For the full command and configuration reference, see the [`@workos/sync-mysql` README](https://github.com/workos/sync-engine/tree/main/packages/sync-mysql).

## Set up Sync Engine

### Requirements

* A PlanetScale Vitess database with an unsharded keyspace for the mirror
* A WorkOS account and API key
* Node.js 22 or later, to run the `workos-sync-mysql` command

### Create a password

Sync Engine creates tables, views, and temporary tables, so it needs a password with the **Admin** [role](https://planetscale.com/docs/vitess/security/password-roles.md).

1. In PlanetScale, navigate to your database and click the "**Connect**" button.
2. Select the branch, choose the **Admin** role, and click "**Create password**".
3. Copy the username, password, and host. PlanetScale only shows the password once.

### Set the connection string

Use the host PlanetScale shows with your password, and end the connection string with `?ssl={"rejectUnauthorized":true,"verifyIdentity":true}`.
This turns on TLS and verifies the server's certificate and host name:

```text theme={null}
mysql://<username>:<password>@<host>/<database>?ssl={"rejectUnauthorized":true,"verifyIdentity":true}
```

Without the parameter, every command fails with `server does not allow insecure connections, client must use SSL/TLS`.

Set `WORKOS_SYNC_SCHEMA` to your database name.
Without it, or with a name that doesn't match your database, every command fails with `VT12001: unsupported: create database by failDBDDL`.
If your database has more than one keyspace, set `WORKOS_SYNC_SCHEMA` to an unsharded [keyspace](https://planetscale.com/docs/vitess/sharding/keyspaces.md).

### Check table names

Sync Engine creates its tables in your database, next to your application's tables.
With the default streams, these are:

`users`, `organizations`, `organization_memberships`, `organization_domains`, `organization_roles`, `invitations`, `sessions`, `directories`, `directory_users`, `directory_groups`, `directory_group_memberships`, `connections`, `groups`, `group_memberships`, `roles`, `permissions`, `feature_flags`, `events`, `auth_attempts`

It also creates five bookkeeping tables: `_sync_state`, `_migrations`, `_sync_lease`, `_rejected`, and `_columns`.
`--streams` limits the sync to the streams you list, so list the default streams along with an added one such as `webhook_endpoints`.
Run `npx workos-sync-mysql streams` to list them.
The added stream gets a table with its name.

> [!WARNING]
>
> If your database already has a table with one of these names, such as `users`, `init` treats it as its own, tries to alter it, and fails.
> To keep the mirror apart, set `WORKOS_SYNC_TABLE_PREFIX` to a prefix such as `workos_` before the first `init`, and use the same value for every command.
> Every table and view the mirror creates then starts with the prefix, for example `workos_users` and `workos_users_live`.

### Run the sync

If your production branch has [safe migrations](https://planetscale.com/docs/vitess/schema-changes/safe-migrations.md) on, follow [Safe migrations](#safe-migrations) instead of running `init` there.

1. Install the package:

   ```bash theme={null}
   npm install @workos/sync-mysql
   ```

2. Set your environment variables:

   ```bash theme={null}
   export DATABASE_URL='mysql://<username>:<password>@<host>/<database>?ssl={"rejectUnauthorized":true,"verifyIdentity":true}'
   export WORKOS_SYNC_SCHEMA='<database>'
   export WORKOS_API_KEY='sk_...'
   export WORKOS_ENVIRONMENT_NAME='<environment-name>'
   ```

3. Run `init`, `backfill`, and `listen`:

   ```bash theme={null}
   npx workos-sync-mysql init
   npx workos-sync-mysql backfill
   npx workos-sync-mysql listen
   ```

> [!WARNING]
>
> Set `WORKOS_ENVIRONMENT_NAME` to the name of the WorkOS environment your API key belongs to, such as `staging`, before you run any command.
> Use the same value for every command.
> Sync Engine labels every row with `_env = 'production'` by default, even when you use a test API key.
> Changing the label later doesn't relabel existing rows.
> Sync Engine writes a second copy of each row, and the `_live` views return both.

### Safe migrations

With [safe migrations](https://planetscale.com/docs/vitess/schema-changes/safe-migrations.md) on, PlanetScale refuses direct DDL on the branch, so every command except `status` fails until the mirror is deployed.
Deploy the schema with two [deploy requests](https://planetscale.com/docs/vitess/schema-changes/deploy-requests.md): one for the tables, then one for the views.
One deploy request with both fails lint.

1. Create a [development branch](https://planetscale.com/docs/vitess/schema-changes/branching.md#create-a-development-branch) from your production branch, and an Admin password for it.
2. On the development branch, run `init`.
3. On the development branch, drop the 17 `_live` views:

   ```sql theme={null}
   DROP VIEW IF EXISTS users_live, organizations_live, organization_memberships_live, organization_domains_live,
     organization_roles_live, invitations_live, sessions_live, directories_live, directory_users_live,
     directory_groups_live, directory_group_memberships_live, connections_live, groups_live,
     group_memberships_live, roles_live, permissions_live, feature_flags_live;
   ```

   If you added streams with `--streams`, add `<stream>_live` for each one.
   If you set a table prefix, put it before each view name.
   Open a deploy request, deploy it, and skip the [revert period](https://planetscale.com/docs/vitess/schema-changes/deploy-requests.md#revert-a-schema-change), so you can open the second deploy request right away.
4. On the development branch, run `init --repair` to create the views. Open a second deploy request, deploy it, and skip the revert period.
5. On the production branch, with an Admin password, run `backfill`, then `listen`.

Adding a stream with `--streams` changes the schema, so deploy it the same way: create a new development branch from the production branch, run `init` there with the new stream list, and deploy the change with a deploy request.
Until it's deployed, every command with the new stream list fails on the production branch.

## Sync overview

* `init` creates the mirror tables and views.
* `backfill` loads the data that already exists in WorkOS.
* `listen` applies new WorkOS events as they happen, and keeps running until you stop it.

## Query the mirror

The mirror's tables and views are in your database, so query them without the `workos.` prefix.
Each mirror table except `events` and `auth_attempts` has a `_live` view that leaves out deleted rows.

```sql theme={null}
SELECT id, email FROM users_live;
```

## Manage the sync

### Reconcile

`reconcile` compares the mirror with WorkOS to find missed creates and deletes.

`reconcile` exits `1` when a stream can't be compared, and marks it `"failed": true` in its output.

### Branches

Each [branch](https://planetscale.com/docs/vitess/schema-changes/branching.md) needs its own credentials.
A new branch has the mirror's tables and views, but no rows.
Run `init` and `backfill` on it.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
