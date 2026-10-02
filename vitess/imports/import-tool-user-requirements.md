---
url: https://planetscale.com/docs/vitess/imports/import-tool-user-requirements
title: "Import Tool User Requirements"
description: ""
access_date: 2026-10-02T19:13:10.531Z
current_date: 2026-10-02T19:13:10.531Z
---

PlanetScale uses one user for everything it does on your source database: reading your schema, copying rows, replicating changes, and, after you switch primary traffic, replicating writes back to your source database. When you [create the external keyspace](database-imports.md#step-3-create-an-external-keyspace), PlanetScale checks that this user has the grants below and won’t create the keyspace if any are missing.

Below is the minimum set of permissions needed and what each allows the user to do:

| Scope | Databases | Grant | Description |
| --- | --- | --- | --- |
| Global | n/a | `PROCESS` | Enable the user to see all processes with SHOW PROCESSLIST. |
| Global | n/a | `REPLICATION SLAVE` | Enable replicas to read binary log events from the source. |
| Global | n/a | `REPLICATION CLIENT` | Enable the user to ask where source or replica servers are. |
| Global | n/a | `RELOAD` | Enable use of FLUSH operations. On MySQL 8.0, `FLUSH_TABLES` can be used instead. |
| Database | `<DATABASE_NAME>` | `SELECT` | Enable use of SELECT. |
| Database | `<DATABASE_NAME>` | `INSERT` | Enable use of INSERT. |
| Database | `<DATABASE_NAME>` | `UPDATE` | Enable use of UPDATE. |
| Database | `<DATABASE_NAME>` | `DELETE` | Enable use of DELETE. |
| Database | `<DATABASE_NAME>` | `LOCK TABLES` | Enable use of LOCK TABLES on tables for which you have the SELECT privilege. |
| Database | `<DATABASE_NAME>` | `SHOW VIEW` | Enable use of SHOW VIEW. Needed if your database has views. |
| Database | `ps_import_*` | `SELECT`, `INSERT`, `UPDATE`, `DELETE` | Read and write the replication state PlanetScale keeps on your source database. |
| Database | `ps_import_*` | `CREATE`, `DROP`, `ALTER` | Create and maintain the `ps_import_*` database and its tables. |

PlanetScale creates a database named `ps_import_<ID>` on your source database to track replication. The last portion of the name varies per external keyspace, which is why the grants use a wildcard.

- Create the user with the host `'%'`. Grants for other hosts are not recognized.
- Grant the privileges in the table above directly to the user, at the global or database level. The check does not recognize table-level grants or grants through a role.
- `ALL PRIVILEGES` on a scope covers every grant for that scope.

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
GRANT SELECT ON mysql.db TO 'migration_user'@'%';
GRANT SELECT ON mysql.func TO 'migration_user'@'%';
GRANT SELECT ON mysql.innodb_table_stats TO 'migration_user'@'%';
GRANT SELECT ON mysql.tables_priv TO 'migration_user'@'%';
GRANT SELECT ON mysql.user TO 'migration_user'@'%';
```

If your database is on Amazon RDS or Aurora, also let the user read your binlog retention setting:

```sql
GRANT EXECUTE ON PROCEDURE mysql.rds_show_configuration TO 'migration_user'@'%';
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
