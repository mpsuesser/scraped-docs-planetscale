---
url: https://planetscale.com/docs/neki/web-console
title: "Web Console"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

## Get started

Run Postgres queries and DDL from the **Console** page. The console is available on any database branch. It is [disabled for production branches](#enable-for-production-branches) by default to protect production data.

Once connected, run queries or apply DDL against the branch. Use `EXPLAIN` or `EXPLAIN ANALYZE` in front of `SELECT`, `INSERT`, `UPDATE`, and `DELETE` statements to see how the database executes a query.

## Inspect database objects

The console supports a subset of `psql` meta commands. Type `\?` or `help;` to show the complete list in the console.

Use `\d` without a name to list tables, partitioned tables, views, materialized views, sequences, and foreign tables. Add a relation name to inspect its columns, data types, nullability, and defaults:

```text
\d public.orders
```

Use the more specific commands when you only want one type of object. For example, `\dt` lists tables and `\di` lists indexes and their definitions.

Commands that accept a pattern support `*` for any number of characters and `?` for one character. You can also include a schema name:

```text
\dt public.*
\di *created_at*
```

`\d` and `\dt` also show tables in Neki’s internal `__neki` schema. Filter with a pattern such as `public.*` to show only objects in your application schema. Do not use the `__neki` schema for application objects.

The console accepts `\d+`, but it returns the same information as `\d`. Other `psql` meta commands that are not listed below are not supported.

## Logical databases

After connecting to a branch, use the **Database** dropdown in the console header to choose which logical database to connect to. The default database name is `postgres`.

You can also switch databases from the console prompt with `\connect database_name` or `\c database_name`, just like in `psql`. Run `\l` to list the available databases on the branch.

Switching databases reconnects the console and clears the current output.

## Primary and replica connections

After connecting to a branch, use the **Server** dropdown in the console header to choose which instance to connect to:

- **Primary** — Connect to the primary Postgres instance for read and write queries.
- **Replica** — Connect to a replica for read-only queries.

If a branch has no replicas, the console connects to the primary and the **Replica** option is unavailable.

Switching between primary and replica reconnects the console and clears the current output.

### Connection roles

The console creates a short-lived Postgres role for each connection. The inherited permissions depend on the selected server and your organization or database role.

Tables created during a web console session are reassigned to the `postgres` role shortly after the session ends. Until reassignment completes, newly created tables may temporarily appear owned by the console’s short-lived role.

#### Development branches

All organization roles (member, analyst, administrator) have full read and write access to development branch consoles on the primary. On a replica, the console always uses a read-only role.

#### Production branches

Production branch console access also requires the [web console setting](#enable-for-production-branches) to be enabled. Once enabled, access depends on your role:

| Role | Console access | Primary permissions |
| --- | --- | --- |
| Organization or database **Administrator** | Read and write | `pg_read_all_data`, `pg_write_all_data`, `postgres` |
| Organization or database **Analyst** | Read-only | `pg_read_all_data` only |
| **Member** | No access | — |

On a **replica**, the console always uses a read-only role with `pg_read_all_data`, regardless of your primary permissions.

## Supported console commands

| Command | Description |
| --- | --- |
| ?, \\? | Synonym for `help` |
| clear, reset | Clear the current input statement |
| help, \\h | Display list of commands |
| ; | Send SQL statement to server |
| \\connect \[database\], \\c \[database\] | Connect to a different logical database |
| \\d \[name\] | List relations, or show a relation’s columns, types, nullability, and defaults |
| \\dt \[pattern\] | List tables and partitioned tables |
| \\dv \[pattern\] | List views and materialized views |
| \\di \[pattern\] | List indexes and their definitions |
| \\ds \[pattern\] | List sequences |
| \\da \[pattern\] | List aggregate functions |
| \\db \[pattern\] | List tablespaces |
| \\dc \[pattern\] | List casts |
| \\dconfig \[pattern\] | List configuration parameters with non-default values |
| \\dD \[pattern\] | List domains |
| \\det \[pattern\] | List foreign tables |
| \\dn \[pattern\] | List schemas |
| \\do \[pattern\] | List operators |
| \\dO \[pattern\] | List collations |
| \\dp, \\z \[pattern\] | List access privileges |
| \\dT \[pattern\] | List data types |
| \\du \[pattern\] | List roles |
| \\l \[pattern\] | List databases |
| \\df \[pattern\] | List functions |
| \\dx \[pattern\] | List extensions |

Commands with uppercase letters, including `\dD`, `\dO`, and `\dT`, are case sensitive.

## Keyboard shortcuts

| Shortcut | Action |
| --- | --- |
| Up and Down | Move through query history when the cursor is on the first or last line of the current input |
| Command + K on macOS or Ctrl + K elsewhere | Clear console output |
| Command + Up and Command + Down on macOS, or Ctrl with the arrow keys elsewhere | Move between previous result blocks |
| Up and Down while autocomplete is open | Select a suggestion |
| Enter or Tab while autocomplete is open | Apply the selected suggestion |
| Escape | Close the current autocomplete suggestions |

## Enable for production branches

By default, the web console is disabled for production branches to prevent accidental data loss.

You can enable the web console for production branches on the **Settings** page for the given database, `app.planetscale.com/<org>/<database>/settings`.

Select the checkbox for **Allow web console access to production branches**, then scroll down and select **Save database settings** to save your changes.

This enables the web console to run queries against production branches for the given database.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
