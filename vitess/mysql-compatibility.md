---
url: https://planetscale.com/docs/vitess/mysql-compatibility
title: "Mysql Compatibility"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Overview

PlanetScale databases run on MySQL 8. If you’re [importing an existing database](imports/database-imports.md), PlanetScale supports MySQL database versions `5.7` through `8.0`.

New PlanetScale databases are created on MySQL 8 with character set `utf8mb4_0900_ai_ci`. PlanetScale supports `utf8`, `utf8mb4`, and `utf8mb3`, character sets. We also support `latin1` and `ascii` character sets, but do not recommend them.

## MySQL compatibility limitations

The following reference guide will cover some MySQL syntax, features, and more that PlanetScale either does not support or has limitations around. We are actively working on driving up compatibility, but it’s an ongoing effort and will take some time to complete. See this [project board on GitHub](https://github.com/vitessio/vitess/projects/4) to learn what the Vitess team is currently focusing on.

If you’re attempting to import a database using our Import tool, there are some additional requirements that you can find in our [Database imports documentation](imports/database-imports.md).

### Queries, functions, syntax, data types, and SQL modes

\= *Limitations in support*

\= *Not supported*

| Statement | Support | Description |
| --- | --- | --- |
| `ALTER TABLE...RENAME COLUMN` |  | Renaming columns and tables may be destructive. See our [guide for column rename recommendations](schema-changes/handling-table-and-column-renames.md). |
| `CREATE DATABASE` |  | You cannot `CREATE` a logical database within a PlanetScale Vitess database. |
| `DROP DATABASE` |  | You cannot `DROP` a logical database within a PlanetScale Vitess database. |
| `JSON_TABLE` |  | The [`JSON_TABLE` function](https://dev.mysql.com/doc/refman/8.0/en/json-table-functions.html#function_json-table) is not yet supported. All other [JSON SQL functions](https://dev.mysql.com/doc/refman/8.0/en/json-function-reference.html) should work. |
| `PROCEDURE` |  | We do not support any form of [stored routines](https://dev.mysql.com/doc/refman/8.0/en/stored-routines.html). |
| `FUNCTION` |  | We do not support any form of [stored routines](https://dev.mysql.com/doc/refman/8.0/en/stored-routines.html). |
| `TRIGGER` |  | We do not support any form of [stored routines](https://dev.mysql.com/doc/refman/8.0/en/stored-routines.html). |
| `EVENT` |  | We do not support any form of [stored routines](https://dev.mysql.com/doc/refman/8.0/en/stored-routines.html). |
| `LOAD DATA INFILE` |  | Loading data via [`LOAD DATA INFILE` is not supported](https://github.com/vitessio/vitess/issues/2976). |
| `KILL` |  | We do not support killing queries or shards from the command line. |
| `:=` |  | The `:=` assignment operator is not yet supported. |
| `SET GLOBAL time_zone` |  | The global time zone is set to UTC and can not be modified. |
| `SET GLOBAL sql_mode` |  | The global SQL mode can not be changed permanently. Set each new session’s mode instead with `SET sql_mode`. |
| `PIPES_AS_CONCAT` |  | Enabling this SQL mode can interfere with Vitess’ evalengine parsing the SQL queries so enabling it may result in incorrect or unexpected results. Please use MySQL’s standard dialect instead, e.g. `CONCAT()`. |
| `ANSI_QUOTES` |  | Enabling this SQL mode can interfere with Vitess’ evalengine parsing the SQL queries so enabling it may result in incorrect or unexpected results. Please use MySQL’s standard quotation instead. |
| `WITH RECURSIVE` |  | Experimental support for recursive common table expressions (CTEs) was introduced in Vitess 21 for `SELECT` queries. |

## Miscellaneous

| Action | Support | Description |
| --- | --- | --- |
| **Empty schemas** |  | Databases with empty schemas are invalid. You cannot deploy a schema change to production if no tables exist. |
| **Non-InnoDB Storage engine** |  | We only support [InnoDB](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html) storage engine. |
| **No applicable unique key** |  | We require all tables have a [unique, non-null key](schema-changes/onlineddl-change-unique-keys.md) and that respective covered columns are shared between old and new schema. |
| **Direct DDL** |  | We do [not allow Direct DDL](schema-changes/how-online-schema-change-tools-work.md) on production branches when [safe migrations](schema-changes/safe-migrations.md) is enabled. This includes `TRUNCATE` statements. |
| **Binary log access** |  | PlanetScale does not currently support binlog replication to external databases. |
| **Large JSON documents** |  | MySQL supports JSON documents up to 1 GB in size. However, we do not recommend to store more than a few MB in a JSON document for performance reasons. |

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
