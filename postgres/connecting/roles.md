---
url: https://planetscale.com/docs/postgres/connecting/roles
title: "Roles"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

You should not connect to the database from your application servers using the default role. If you ever need to rotate your default role credentials and you use the default role to connect to your application, you will have to take some downtime while rotating the credentials.

Instead, we recommend creating [user-defined roles](#user-defined-roles) for this purpose. We’ll first cover the default role below, and then explain how to generate user-defined roles.

## Default role

The default `postgres` role is similar to the Postgres `superuser`, but with fewer permissions. It is defined by the following statement:

```sql
CREATE ROLE $POSTGRES_USERNAME
  NOSUPERUSER CREATEDB CREATEROLE INHERIT LOGIN REPLICATION BYPASSRLS PASSWORD '$PASSWORD';
```

It also inherits the following permissions:

```sql
GRANT pg_read_all_data,
  pg_write_all_data,
  pg_read_all_settings,
  pg_read_all_stats,
  pg_stat_scan_tables,
  pg_monitor,
  pg_signal_backend,
  pg_checkpoint,
  pg_maintain,
  pg_use_reserved_connections,
  pg_create_subscription
TO pscale_superuser WITH ADMIN OPTION;
```

## User-defined roles

When creating custom roles for your application, you can select from a variety of permissions to grant specific capabilities. User-defined roles allow you to implement the principle of least privilege by granting only the permissions necessary for each use case.

For examples of common user-defined roles, see [User-defined role examples and use cases](#user-defined-role-examples-and-use-cases).

### Available permissions

Below is a list of available permissions you can set on user-defined roles.

**Data access permissions**

- **pg\_read\_all\_data** — Read data from all tables, views, and sequences. This permission allows `SELECT` queries across all database objects.
- **pg\_write\_all\_data** — Write data to all tables, views, and sequences. This permission allows `INSERT`, `UPDATE`, `DELETE`, and `TRUNCATE` operations. Note that write operations typically require `pg_read_all_data` as well to read the data being modified.

**Configuration and monitoring permissions**

- **pg\_read\_all\_settings** — Read all configuration variables. This allows viewing database configuration parameters.
- **pg\_read\_all\_stats** — Read all `pg_stat_*` views. This provides access to database statistics and performance metrics.
- **pg\_stat\_scan\_tables** — Execute monitoring functions that may take `ACCESS SHARE` locks on tables. This is useful for running database monitoring and analysis operations.
- **pg\_monitor** — Read and execute monitoring views and functions. This is a convenience role that combines several monitoring-related permissions.

**Administrative permissions**

- **pg\_signal\_backend** — Signal another backend to cancel a query or terminate its session. This is useful for managing long-running queries and terminating problematic connections.
- **pg\_checkpoint** — Execute the `CHECKPOINT` command. Checkpoints ensure that all data is written to disk and are important for database recovery.
- **pg\_maintain** — Execute maintenance operations including `VACUUM`, `ANALYZE`, `CLUSTER`, `REFRESH MATERIALIZED VIEW`, `REINDEX`, and `LOCK TABLE`. These operations are essential for database performance and maintenance.
- **pg\_use\_reserved\_connections** — Use connection slots reserved via `reserved_connections`. This allows connecting to the database even when all regular connection slots are in use.
- **pg\_create\_subscription** — Allow users with `CREATE` permission on the database to issue `CREATE SUBSCRIPTION`. This is used for logical replication scenarios.

**Superuser-equivalent permission**

- **postgres** — The default near-superuser role with extensive permissions. This role can create, modify, and drop databases, users, roles, tables, schemas, and all other objects. Use this permission carefully, as it grants broad administrative capabilities.

## Creating new user-defined roles

There are several ways to create a new role:

- Using the “Connect” button in your dashboard
- Using “Roles” section in your database settings
- Using the [`CREATE ROLE`](https://www.postgresql.org/docs/current/sql-createrole.html) command as the default role (which has elevated privileges).
- Using the Postgres [Roles API](../../api/reference/list_roles.md)
- Using the PlanetScale CLI [pscale role commands](../../cli/role.md)

### Creating roles in the dashboard

To create a new role in the dashboard, you can either click the “Connect” button on the database overview page, or navigate to “Settings” > “Roles” and click “New role”.

![Configure the new role](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/roles/image3.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=021c2cce085fbd2a2eec855d4494563e)

Configure the new role

### Creating roles via the CLI

You can also manage roles directly from the command line using the PlanetScale `pscale role` CLI. This provides a convenient way to create, list, and manage roles as part of your development workflow or automation scripts.

Make sure you have the [PlanetScale CLI](../../cli.md) installed

#### Available commands

**Create a new role:**

```text
pscale role create <database> <branch> <name> [flags]
```

Example:

```text
pscale role create my-database main api-user --inherited-roles pg_read_all_data --ttl 24h
```

**List all roles for a branch:**

```text
pscale role list <database> <branch>
```

**Get details for a specific role:**

```text
pscale role get <database> <branch> <role-id>
```

**Delete a role:**

```text
pscale role delete <database> <branch> <role-id> [--successor <other-role>]
```

**Update a role’s name:**

```text
pscale role update <database> <branch> <role-id> --name <new-name>
```

**Renew a role’s expiration:**

```text
pscale role renew <database> <branch> <role-id>
```

**Reset the default postgres role credentials:**

```text
pscale role reset-default <database> <branch>
```

**Reset a role’s password:**

```text
pscale role reset --org <org> <database> <branch> <role-id>
```

This command resets the password for a role after prompting for confirmation. It returns the role object with the new password.

**Reassign objects owned by a role:**

```text
pscale role reassign --org <org> <database> <branch> <donor> --successor <recipient>
```

This command assigns all objects owned by the donor role to the recipient role.

Roles created via the CLI will appear in your database settings and can be managed through the dashboard as well.

### Creating roles via CREATE ROLE

Use this path when you need fine-grained permissions that the managed role builder doesn’t expose (for example, table- or column-level `GRANT` s). For most use cases, prefer the dashboard, [`pscale role`](../../cli/role.md), or the [Roles API](../../api/reference/create_role.md), which produce managed roles that appear in the dashboard and handle credential and lifecycle management for you.

When you create a role via the Postgres [`CREATE ROLE`](https://www.postgresql.org/docs/current/sql-createrole.html) command, these will not display on your database settings. It is up to you to manage these via the `psql` CLI.

PlanetScale’s routing layer uses the `user` to identify which database or branch we are sending queries to. For example, the user `matt.nk35mx55qq` routes to the PlanetScale database with branch id `nk35mx55qq`. When you create a new role, you do not need to specify the branch id on the user. You can simply set the user to `matt`.

However, when you connect, you must append the branch id to the user so we know which branch to route to.

## Viewing, deleting, and renaming roles

On the roles page, you will see all roles created via the dashboard, API, and the `pscale role` CLI. However, we will not display roles created manually via `CREATE ROLE` commands.

You can rename a role by clicking the ”…” button for the role on the Roles page at “Settings” > “Roles”.

### Deleting a role

To delete a role, click the ”…” for the role on the database Roles page at “Settings” > “Roles”.

![Rename or delete a role](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/roles/image5.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=a3e81f507a14f3e10e7b41cad6eea686)

Rename or delete a role

Deleting a role requires an extra step if the role has created any objects like tables or schemas, if the role has been granted any additional permissions, or if the role has created any other roles. If you try to delete a role that is still referenced, you may see this error: `Role is still referenced and cannot be dropped.`.

Such roles must designate a successor role, to which allowed objects are reassigned. Additional granted permissions are dropped as part of the transfer process. The usual successor role is `postgres`, which you can indicate in the “Delete role” modal.

You can reassign owned objects when deleting a role directly in the dashboard. When you click “Delete role”, check the “ **Reassign owned objects** ” box on the modal.

![Specify a successor for a role](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/roles/image6.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=27242b10c8c15d4baadc87b350549b14)

Specify a successor for a role

You can choose successors other than `postgres`, but only by using the API or the `pscale role` CLI. Deleting a role that owns objects, has additional permissions, or has created other roles will fail if no successor is specified.

You can [delete a role using the `pscale role` CLI](../../cli/role.md#the-delete-sub-command) with:

```shellscript
pscale role delete --org <org> <database> main <role-id> --successor postgres
```

### Reassigning objects owned by a role

If you need to transfer ownership of database objects from one role to another without deleting the role, you can reassign the objects from the dashboard or the CLI.

**In the dashboard**, click the ”…” button for the role on the Roles page at “Settings” > “Roles”, then select “ **Reassign objects** ”. This will transfer all objects owned by the role to the `postgres` role. Reassigning objects to a recipient other than `postgres` is only possible through the API and the CLI.

**Using the CLI**, you can reassign objects with:

```shellscript
pscale role reassign --org <org> <database> <branch> <donor> --successor <recipient>
```

This command will prompt for confirmation before transferring ownership of all objects from the donor role to the recipient role.

### Resetting role credentials

If you need to reset a role’s password, you can do so from the dashboard or the CLI.

**In the dashboard**, click the ”…” button for the role on the Roles page at “Settings” > “Roles”, then select “ **Reset credentials** ”. This will generate a new password for the role.

**Using the CLI**, you can reset a role’s password with:

```shellscript
pscale role reset --org <org> <database> <branch> <role-id>
```

This command will prompt for confirmation before resetting the password and return the role object with the new password.

## User-defined role examples and use cases

Understanding when to use user-defined roles versus the default role is essential for maintaining secure and maintainable database access patterns. This section provides practical examples of role configurations for common scenarios.

### When to use user-defined roles vs. the default role

**Use user-defined roles when:**

- **Connecting from application servers**: Application connections should never use the default role. This allows you to rotate the default role credentials without application downtime.
- **Principle of least privilege**: Different parts of your application or different services may need different levels of access. Create specific roles for each use case.
- **Managing team access**: Different team members may need different permissions (e.g., developers vs. data analysts vs. DBAs).
- **Integrating third-party tools**: External tools and services should have their own roles with limited permissions appropriate to their function.

**Use the default role when:**

- **Performing administrative tasks**: Creating schemas, managing database structure, or performing major database migrations.
- **Initial database setup**: Setting up the initial database structure and creating the first set of user-defined roles.

### Example role configurations

The following are some example permission configurations that you may use for user-defined roles. Your use cases may vary, but these are generic examples.

**Application read-write role**

For a typical web application that needs to read and write data, you may consider these permissions:

- `pg_read_all_data`
- `pg_write_all_data`

`pg_read_all_data` and `pg_write_all_data` grant **data** access only (`SELECT`, `INSERT`, `UPDATE`, `DELETE`). They do **not** grant the ability to run DDL such as `CREATE TABLE`, `CREATE INDEX`, or `CREATE SCHEMA`. Creating an object in a schema additionally requires the `CREATE` privilege **on that schema**.

A role with only these two permissions that tries to create a table in the `public` schema will fail with:

```text
ERROR: permission denied for schema public
```

To let a role create objects in `public`, grant it explicitly (run this as the default `postgres` role, which has the necessary privileges):

```sql
GRANT CREATE ON SCHEMA public TO "<role_name>";
```

Alternatively, run schema migrations and other DDL as the default `postgres` role and reserve the least-privilege application role for reads and writes.

Enable [pg\_strict](../extensions/pg_strict.md) for production roles to block UPDATE and DELETE statements without WHERE clauses.

**Read-only analytics role**

For analytics tools or reporting dashboards that only need to query data:

- `pg_read_all_data`
- `pg_read_all_settings`
- `pg_read_all_stats`

**Monitoring and observability role**

For monitoring tools like Datadog, New Relic, or custom monitoring solutions:

- `pg_monitor`
- `pg_read_all_stats`
- `pg_read_all_settings`
- `pg_stat_scan_tables`

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
