---
url: https://planetscale.com/docs/vitess/web-console
title: "Web Console"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Web console

> The PlanetScale web console is an interactive interface for running MySQL queries and DDL (Create, Alter, and Delete) against your PlanetScale database branches.

**Platform availability:** Vitess and [Postgres](../postgres/web-console.md)

## Get started

The PlanetScale web console can be used to query to any database branch; however, it is [disabled for production branches](web-console.md#enable-for-production-branches) by default to protect production data.

To access the web console, navigate to a database, and click on the "Console" tab in the page navigation. From here, you can select which branch you'd like to connect to by selecting it in the dropdown and clicking "Connect".

Once you have accessed the web console, you can run queries against your database branch, or apply DDL to branches without [safe migrations](schema-changes/safe-migrations.md) enabled.

The following are examples of MySQL statements you may find useful within the web console:

Use `SHOW TABLES;` to see a list of the tables in your database branch.

Use `DESCRIBE table_name;` to obtain information about a given table's structure.

Use `EXPLAIN` in front of `SELECT`, `DELETE`, `INSERT`, `REPLACE` and `UPDATES` statements to learn how the database is executing a query. This can be useful for optimizing slow queries.

## Primary and replica connections

After connecting to a branch, use the **Server** dropdown in the console header to choose which node to connect to:

* **Primary** — Connect to the primary node. Use this for read and write queries.
* **Replica** — Connect to a read replica. The console uses read-only credentials on replica connections.

If a branch has no replicas, the console connects to the primary and the **Replica** option is unavailable.

Switching between primary and replica reconnects the console and clears the current output.

## Supported console commands

| Command   | Description                       |
| :-------- | :-------------------------------- |
| ?, \\?    | Synonym for `help`                |
| clear, \c | Clear the current input statement |
| help, \h  | Display list of commands          |
| ego, \G   | Send command to server            |
| go, \g    | Send command to server            |

## Enable for production branches

By default, the web console is disabled for production branches to prevent accidental data loss.

You can enable the web console for production branches on the "Settings" page for the given database,
`app.planetscale.com/<org>/<database>/settings`.

Select the checkbox for "Allow web console access to production branches", then scroll down and click the "Save database settings" button to save your changes.

This will enable the ability to use the web console to run queries against production branches for the given database.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
