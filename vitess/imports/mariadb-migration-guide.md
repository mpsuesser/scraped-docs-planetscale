---
url: https://planetscale.com/docs/vitess/imports/mariadb-migration-guide
title: "Mariadb Migration Guide"
description: ""
access_date: 2026-10-02T19:13:10.531Z
current_date: 2026-10-02T19:13:10.531Z
---

## Overview

In this article, you’ll learn how to migrate a database from MariaDB, a fork of MySQL, into PlanetScale.

The steps outlined in this guide used MariaDB version 10.6.12 on an Ubuntu host. Depending on the version of MariaDB you are using, your results may vary. Don’t hesitate to [reach out to us](https://planetscale.com/contact) for further assistance.

We recommend reading through the [Database import documentation](database-imports.md) to learn how imports work before proceeding.

### Prerequisites

- A PlanetScale account
- A MariaDB server with traffic permitted from our [import IP addresses](import-tool-migration-addresses.md)

## Configure MariaDB

Before you can start migrating data, there are a number of configuration options that need to be in place for the import to work properly:

- `binlog_format`
- `log_bin`
- `sql_mode`

The steps below edit the configuration file on a self-managed server. If your MariaDB database is hosted on Amazon RDS, you cannot edit these values directly. Instead, set them through a DB parameter group, enable binary logging by turning on automated backups, and set your binlog retention period, as described in the [AWS RDS migration guide](aws-rds-migration-guide.md). Skip the GTID parameters in that guide, since MariaDB uses its own GTID implementation and does not have a `gtid_mode` setting. Then return here to configure the migration account.

You may run the following query to check these values:

```sql
SHOW variables WHERE Variable_name IN ('binlog_format','log_bin','sql_mode');

+---------------+-------------------------------------------------------------------------------------------+
| Variable_name | Value                                                                                     |
+---------------+-------------------------------------------------------------------------------------------+
| binlog_format | MIXED                                                                                     |
| log_bin       | OFF                                                                                       |
| sql_mode      | STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION |
+---------------+-------------------------------------------------------------------------------------------+
```

In the results listed above, none of the options are configured properly, so let’s set them up now. The exact path to your configuration file varies by operating system. This demo uses Ubuntu, so the MariaDB configuration file is located at `/etc/mysql/mariadb.conf.d/50-server.cnf`. Edit the configuration file and add the following values at the end of the file:

```text
binlog_format = ROW
log_bin = /var/log/mysql/mysql-bin.log
sql_mode = 'NO_ZERO_IN_DATE,NO_ZERO_DATE,ONLY_FULL_GROUP_BY'
```

With the configuration updated, restart the MariaDB service. The exact command varies by the service manager you are using on your host, with this demo using `systemctl`:

```shellscript
sudo systemctl restart mariadb
```

## Configure a migration account

PlanetScale requires a user account with a specific set of permissions on the database you wish to migrate, as well as the server itself to set up the databases that track replication changes.

Connect to your MariaDB server with an account that has admin privileges, then run the following script, replacing the placeholders:

- `<SUPER_STRONG_PASSWORD>` - The password for the `migration_user` account
- `<DATABASE_NAME>` - The name of the database you will import into PlanetScale

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
GRANT SELECT ON performance_schema.* TO 'migration_user'@'%';
FLUSH PRIVILEGES;
```

PlanetScale creates the `ps_import_<id>` database on your server to track replication between MariaDB and PlanetScale. The last portion of the `ps_import_<id>` name varies per external keyspace, which is why the grant uses a wildcard. Your server must not already contain a `_vt` database.

If your MariaDB database is hosted on Amazon RDS, also grant access to the RDS configuration procedure so PlanetScale can read your binlog retention setting:

```sql
GRANT EXECUTE ON PROCEDURE mysql.rds_show_configuration TO 'migration_user'@'%';
```

This procedure only exists on Amazon RDS. Running the grant against a self-managed MariaDB server fails with `ERROR 1305: PROCEDURE mysql.rds_show_configuration does not exist`.

Verify the grants were applied correctly:

```sql
SHOW GRANTS FOR 'migration_user'@'%';
```

You should see all of the `GRANT` statements listed. If you only see one or two lines, the grants didn’t apply correctly.

For a full explanation on what each of these grants do, [our article on configuring a migration account for MySQL databases](import-tool-user-requirements.md) details each requirement.

## Importing your database

Now that your MariaDB database is configured and ready, follow the [Database imports guide](database-imports.md) to complete your import.

When you [create the external keyspace](database-imports.md#step-3-create-an-external-keyspace), use the following connection settings:

- **Host name** (`--host`) - Your MariaDB server hostname or IP address
- **Port** (`--port`) - 3306 (default for MariaDB)
- **Database name** (`--source-database`) - The exact database name to import
- **Username** (`--username`) - `migration_user` (created in previous section)
- **Password** (`--password`) - The password you set for the migration user
- **SSL verification mode** (`--ssl-mode`) - Select based on your MariaDB SSL configuration

The Database imports guide will walk you through:

- Creating your PlanetScale database
- Connecting to your MariaDB database with an external keyspace
- Checking your server settings, user grants, and schema
- Starting the import with MoveTables
- Monitoring the import progress
- Switching traffic and completing the import

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
