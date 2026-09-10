---
url: https://planetscale.com/docs/neki/connecting/roles
title: "Roles"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

A role in Neki has scoped access across all shards within a single branch.

The **Roles** dashboard page tracks roles created through the dashboard, API, or PlanetScale CLI. Roles created directly with Postgres `CREATE ROLE` are not managed on that page. You are responsible for their passwords, permissions, and lifecycle.

## Default and user-defined roles

The default `postgres` role is best reserved for administrative work. All application traffic should connect via user-defined roles with the least permissions that the connection needs.

Resetting the default role’s credentials replaces the password, so every application using that role must be updated together.

User-defined roles can be rotated independently. The dashboard selects `pg_read_all_data` and `pg_write_all_data` by default, which fits a typical read-write application. Remove either permission when the application does not need it.

Common role configurations are:

| Use case | Inherited roles |
| --- | --- |
| Read-only application or reporting tool | `pg_read_all_data` |
| Read-write application | `pg_read_all_data`, `pg_write_all_data` |
| Monitoring tool | `pg_monitor` |
| Read Neki workflow status and use DDL barriers | `pg_read_all_data`, `neki_viewer` |
| Run Neki workflow mutations | `postgres`, `neki_operator` |
| Administrative work | `postgres` |

## Create a role

#### Dashboard

You can also create a role while connecting. Open the database, click **Connect**, select the branch, and click **Create new role**.

#### CLI

Install and authenticate the [PlanetScale CLI](../../cli/planetscale-environment-setup.md) first.

Add `--ttl 24h` to create temporary credentials. The CLI accepts a duration or a number of seconds. A role created without a TTL does not expire automatically.

```shellscript
pscale role create <DATABASE_NAME> <BRANCH> app \
  --inherited-roles pg_read_all_data,pg_write_all_data \
  --ttl 24h
```

Add `--with-replication` only for a client that needs to start logical replication. It requires `--inherited-roles` to include `postgres`.

```shellscript
pscale role create <DATABASE_NAME> <BRANCH> replicator \
  --inherited-roles postgres \
  --with-replication
```

## Available inherited roles

| Role | Grants |
| --- | --- |
| `pg_read_all_data` | Read all tables, views, and sequences |
| `pg_write_all_data` | Write all tables, views, and sequences |
| `pg_read_all_settings` | Read all configuration variables |
| `pg_read_all_stats` | Read all `pg_stat_*` views |
| `pg_stat_scan_tables` | Run monitoring functions that may take `ACCESS SHARE` locks |
| `pg_monitor` | Use Postgres monitoring views and functions |
| `pg_signal_backend` | Cancel a query or terminate another backend session |
| `pg_checkpoint` | Run `CHECKPOINT` |
| `pg_maintain` | Run maintenance commands such as `VACUUM`, `ANALYZE`, and `REINDEX` |
| `pg_use_reserved_connections` | Use reserved connection slots |
| `pg_create_subscription` | Create logical replication subscriptions when the role also has the required database permission |
| `neki_viewer` | Read Neki workflow status and run functions such as the DDL propagation barrier; requires `pg_read_all_data` |
| `neki_operator` | Run Neki workflow mutation functions; requires `postgres` |
| `postgres` | Perform broad administrative work |

`pg_read_all_data` and `pg_write_all_data` grant data access. They do not grant permission to create or alter tables. Add the `postgres` inherited role only when the connection needs administrative access.

Neki enforces inherited-role dependencies. Selecting `neki_viewer` also requires `pg_read_all_data`; selecting `neki_operator` also requires `postgres`. Use separate least-privilege application and migration roles when an application does not need DDL or workflow access.

The **WITH REPLICATION** attribute is available only when the role inherits `postgres`. Enable it only for a client that needs to start logical replication.

## Connection credentials

Each managed role has:

- A display name used in the dashboard.
- A generated Postgres login name.
- A password shown only after the role is created or reset.
- A branch assignment.

The complete connection username includes a branch suffix. Use the username from the **Connect** page rather than constructing it yourself. Renaming a role changes its display name, not its generated login name.

See [Connect to Neki](../connecting.md) for the complete connection string and replica-routing options.

## Manage a role

Resetting credentials creates a new password. Update every client that opens new connections with that role.

Before deleting a user-defined role that owns tables, schemas, or other objects, reassign those objects to another role. If the role is still referenced by another object, Neki leaves it in place and reports that it cannot be deleted.

#### Dashboard

From **Settings** > **Roles**, open a role to:

- View its connection strings.
- Rename its display name.
- Reset its credentials.
- Reassign objects it owns.
- Delete it.

Use **Reassign objects**, or select **Reassign owned objects** while deleting the role. Both options transfer ownership to `postgres`.

#### CLI

List roles on a branch, then copy the role ID for later commands:

```shellscript
pscale role list <DATABASE_NAME> <BRANCH>
```

Get a role’s details:

```shellscript
pscale role get <DATABASE_NAME> <BRANCH> <ROLE_ID>
```

Rename a role’s display name:

```shellscript
pscale role update <DATABASE_NAME> <BRANCH> <ROLE_ID> \
  --name <NEW_NAME>
```

Reset a user-defined role’s password. The command prints the new password once:

```shellscript
pscale role reset <DATABASE_NAME> <BRANCH> <ROLE_ID>
```

Reset the default `postgres` role:

```shellscript
pscale role reset-default <DATABASE_NAME> <BRANCH>
```

Reassign objects owned by a role. The CLI can transfer ownership to `postgres` or another role:

```shellscript
pscale role reassign <DATABASE_NAME> <BRANCH> <ROLE_ID> \
  --successor postgres
```

Delete a role. Pass `--successor` when the role owns objects:

```shellscript
pscale role delete <DATABASE_NAME> <BRANCH> <ROLE_ID> \
  --successor postgres
```

Extend a temporary role before it expires:

```shellscript
pscale role renew <DATABASE_NAME> <BRANCH> <ROLE_ID>
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
