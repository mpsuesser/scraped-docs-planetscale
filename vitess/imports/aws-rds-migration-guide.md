---
url: https://planetscale.com/docs/vitess/imports/aws-rds-migration-guide
title: "Aws Rds Migration Guide"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

## Overview

This document will demonstrate how to migrate a database from AWS Relational Database Services (RDS) to PlanetScale.

This guide assumes you are using MySQL on Amazon RDS. If you are using Amazon Aurora (MySQL compatible) on RDS, follow the [Amazon Aurora migration guide](amazon-aurora-migration-guide.md). Other database systems (non-MySQL or MariaDB databases) available through RDS will not work with the PlanetScale import tool.

We recommend reading through the [Database import documentation](database-imports.md) to learn how our import tool works before proceeding.

## Prerequisites

Gather the following information from the AWS Console:

- **Database endpoint address** - Located in “ **Connectivity & security** ” tab of your database instance
- **Port number** - Typically 3306
- **Master username and password** - Your RDS root credentials

![The Connectivity & security tab of the database in RDS.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-connectivity-and-security-tab-of-the-database-in-rds.png?w=2500&fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=2f725f3c23d8d61b7ffb67cc97409ddb)

The Connectivity & security tab of the database in RDS.

## Step 1: Configure server settings

Your RDS database needs specific server settings configured before you can import. Follow these steps to configure GTID mode, binlog format, and sql\_mode.

### Check your current parameter group

Your Amazon RDS database is either using the default DB parameter group (e.g., default.mysql8.0) or a custom one. You can view it in the “ **Configuration** ” tab of your database instance.

![The Configuration tab of the database view in RDS.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-configuration-tab-of-the-database-view-in-rds.png?w=2500&fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=116576fb4241e74f5cb291ec62fe547c)

The Configuration tab of the database view in RDS.

### Configure the parameter group

## Step 2: Enable binary logging

Binary logging must be enabled for the import to work. On RDS, binary logging is tied to automated backups.

To enable binary logging, [enable automated backups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html#USER_WorkingWithAutomatedBackups.Enabling) by setting the backup retention period to any value greater than zero days.

Verify binary logging is enabled:

```sql
mysql> show variables like 'log_bin';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| log_bin       | ON    |
+---------------+-------+
```

## Step 3: Configure binlog retention

Set the binary log retention period to ensure logs aren’t purged during the import. For most cases, 48 hours is sufficient, but larger imports may need more time.

Longer retention periods use more disk space. Evaluate your binlog size to avoid running out of disk space. Contact [PlanetScale Support](https://planetscale.com/contact?initial=support) if you need assistance.

Set the retention period using the `mysql.rds_set_configuration()` procedure:

```sql
CALL mysql.rds_set_configuration('binlog retention hours', 48);
```

Verify the setting:

```sql
CALL mysql.rds_show_configuration;
```

Expected output:

```text
+------------------------+-------+-----------------------------------------------------------------------------------------------------------+
| name                   | value | description                                                                                               |
+------------------------+-------+-----------------------------------------------------------------------------------------------------------+
| binlog retention hours | 48    | binlog retention hours specifies the duration in hours before binary logs are automatically deleted.      |
+------------------------+-------+-----------------------------------------------------------------------------------------------------------+
```

## Step 4: Ensure database is publicly accessible

PlanetScale needs to connect to your RDS database over the internet. Check that your database is publicly accessible.

In your database instance, go to “ **Connectivity & security** ” tab. Under “ **Security** ”, check if **Publicly accessible** is set to “Yes”. If it says “No”, you’ll need to modify the database settings to enable public access.

If you cannot make the database publicly accessible, [contact us](https://planetscale.com/contact) to discuss alternative import options.

## Step 5: Create a migration user

Create a dedicated user with limited privileges for the import process.

Connect to your RDS database using the MySQL command line with your master credentials:

```shellscript
mysql -u admin -p -h [your-rds-endpoint]
```

Run the following script, replacing the placeholders:

- `<SUPER_STRONG_PASSWORD>` - Password for the migration\_user account
- `<DATABASE_NAME>` - Name of the database you’re importing

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
GRANT SELECT ON performance_schema.* TO 'migration_user'@'%';
FLUSH PRIVILEGES;
```

Save the username and password securely - you’ll need them for the import.

## Step 6: Configure RDS security group

Allow PlanetScale to connect by adding PlanetScale’s IP addresses to your security group.

The specific IP addresses depend on your PlanetScale database region. These will be shown during the import workflow on the **Connect to external database** step. See the [Import public IP addresses](import-tool-migration-addresses.md) page for more details.

### Add IP addresses to security group

Navigate to “ **Connectivity & security** ” tab of your database instance and click the VPC security group link.

![The Connectivity & security tab of the database view in RDS.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-connectivity-and-security-tab-of-the-database-view-in-rds.png?w=2500&fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=9bba7897fcba4c73a1be2c73eaed4f39)

The Connectivity & security tab of the database view in RDS.

Select “ **Inbound rules** ” tab, then “ **Edit inbound rules** ”.

![The view of security groups associated with the RDS instance.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-view-of-security-groups-associated-with-the-rds-instance.png?w=2500&fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=be39a270bec73e73e5a8809e0def99d3)

The view of security groups associated with the RDS instance.

Click “ **Add rule** ”, then:

- **Type**: Select `MYSQL/Aurora`
- **Source**: Enter the first PlanetScale IP address (AWS will format it as `x.x.x.x/32`)

Repeat for each IP address in your region, then click “ **Save rules** ”.

![The Edit inbound rules view where source traffic can be allowed.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-edit-inbound-rules-view-where-source-traffic-can-be-allowed.png?w=2500&fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=c47d06daa3ad6267b1a88261d4954989)

The Edit inbound rules view where source traffic can be allowed.

## Importing your database

Now that your RDS database is configured, follow the [Database Imports guide](database-imports.md) to complete your import.

When filling out the connection form in the import workflow, use:

- **Host name** - Your RDS endpoint address (from Prerequisites)
- **Port** - 3306 (or your custom port)
- **Database name** - The exact database name to import
- **Username** - `migration_user`
- **Password** - The password you set in Step 5
- **SSL verification mode** - Select based on your RDS SSL configuration

The Database Imports guide will walk you through:

- Creating your PlanetScale database
- Connecting to your RDS database
- Validating your configuration
- Selecting tables to import
- Monitoring the import progress
- Switching traffic and completing the import

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
