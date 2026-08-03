---
url: https://planetscale.com/docs/vitess/imports/import-tool-user-requirements
title: "Import Tool User Requirements"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

Below is the minimum set of permissions needed and what each allows the user to do:

| Scope | Databases | Grant | Description |
| --- | --- | --- | --- |
| Global | n/a | `PROCESS` | Enable the user to see all processes with SHOW PROCESSLIST. |
| Global | n/a | `REPLICATION SLAVE` | Enable replicas to read binary log events from the source. |
| Global | n/a | `REPLICATION CLIENT` | Enable the user to ask where source or replica servers are. |
| Global | n/a | `RELOAD` | Enable use of FLUSH operations. |
| Table | `<DATABASE_NAME>`, `ps_import_*`, `_vt`, `mysql`.`func`, `mysql`.`innodb_table_stats` | `SELECT` | Enable use of SELECT. |
| Table | `<DATABASE_NAME>`, `ps_import_*`, `_vt` | `INSERT` | Enable use of INSERT. |
| Table | `<DATABASE_NAME>` | `LOCK TABLES` | Enable use of LOCK TABLES on tables for which you have the SELECT privilege. |
| Table | `<DATABASE_NAME>` | `SHOW VIEW` | Enable use of SHOW VIEW. |
| Table | `<DATABASE_NAME>`, `ps_import_*`, `_vt` | `UPDATE` | Enable use of UPDATE. |
| Table | `<DATABASE_NAME>`, `ps_import_*`, `_vt` | `DELETE` | Enable use of DELETE. |
| Table | `ps_import_*`, `_vt` | `CREATE` | Enable database and table creation. |
| Table | `ps_import_*`, `_vt` | `DROP` | Enable databases, tables, and views to be dropped. |
| Table | `ps_import_*`, `_vt` | `ALTER` | Enable use of ALTER TABLE. |

The descriptions in the table above were taken from the MySQL docs. For a full list of all possible grants and their impact, please refer to the [GRANT Statement page](https://dev.mysql.com/doc/refman/8.0/en/grant.html) of the MySQL docs, and locate the section titled **Privileges Supported by MySQL**.

## Script to create user

This MySQL script can be used to create a user with the necessary permissions inside of your database. You will need appropriate database permissions to run the script. The username will be `migration_user`. Make sure to update the following variables:

- `<SUPER_STRONG_PASSWORD>` — The password for the `migration_user` account.
- `<DATABASE_NAME>` — The name of the database you will import into PlanetScale.

```sql
CREATE USER 'migration_user'@'%' IDENTIFIED BY '<SUPER_STRONG_PASSWORD>';
GRANT PROCESS, REPLICATION SLAVE, REPLICATION CLIENT, RELOAD ON *.* TO 'migration_user'@'%';
GRANT SELECT, INSERT, UPDATE, DELETE, SHOW VIEW, LOCK TABLES ON \`<DATABASE_NAME>\`.* TO 'migration_user'@'%';
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, ALTER ON \`ps\_import\_%\`.* TO 'migration_user'@'%';
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, ALTER ON \`_vt\`.* TO 'migration_user'@'%';
GRANT EXECUTE ON PROCEDURE mysql.rds_show_configuration TO 'migration_user'@'%';
GRANT SELECT ON mysql.db TO 'migration_user'@'%';
GRANT SELECT ON mysql.func TO 'migration_user'@'%';
GRANT SELECT ON mysql.innodb_table_stats TO 'migration_user'@'%';
GRANT SELECT ON mysql.tables_priv TO 'migration_user'@'%';
GRANT SELECT ON mysql.user TO 'migration_user'@'%';
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
