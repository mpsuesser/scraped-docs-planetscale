---
url: https://planetscale.com/docs/vitess/imports/amazon-aurora-migration-guide
title: "Amazon Aurora Migration Guide"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Amazon Aurora migration guide

This page covers Vitess.

Postgres version: [Postgres](https://planetscale.com/docs/postgres/imports/aurora.md)

## Overview

This document will demonstrate how to migrate a database from Amazon Aurora (MySQL compatible) to PlanetScale.

> [!NOTE]
>
> This guide assumes you are using Amazon Aurora (MySQL compatible) on RDS. If you are using MySQL on Amazon RDS, follow the [Amazon RDS for MySQL migration guide](https://planetscale.com/docs/vitess/imports/aws-rds-migration-guide.md). Other database systems (non-MySQL or MariaDB databases) available through RDS will not work with this guide.

We recommend reading through the [Database import documentation](https://planetscale.com/docs/vitess/imports/database-imports.md) to learn how imports work before proceeding.

## Prerequisites

Gather the following information from the AWS Console:

* **Database cluster endpoint address** - Located in "**Connectivity & security**" tab (use the regional cluster endpoint, not reader or writer instances)
* **Port number** - Typically 3306
* **Master username and password** - Your Aurora root credentials

![The Connectivity & security tab of the database in RDS.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/amazon-aurora-migration-guide/the-connectivity-and-security-tab-of-the-database-in-aurora.jpg?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=7643e830fa16b79b18be6f2b327a3094)

## Step 1: Configure server settings

Your Aurora database needs specific server settings configured before you can import. Follow these steps to configure GTID mode, binlog format, and sql\_mode.

### Check your current parameter group

Your Amazon Aurora database is either using the default DB cluster parameter group (e.g., default.aurora-mysql8.0) or a custom one. You can view it in the "**Configuration**" tab of your regional database cluster (not reader or writer instances).

![The Configuration tab of the database view in RDS.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/amazon-aurora-migration-guide/the-configuration-tab-of-the-database-view-in-aurora.jpg?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=54d99c8cca6901211645b5b12fb52a42)

### Configure the parameter group

**Step 1**

If you are using the default DB cluster parameter group, you'll need to create a new parameter group to reconfigure settings.

To create a parameter group, select "**Parameter groups**" from the left nav and then "**Create parameter group**".

![The Parameter groups view in RDS.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-parameter-groups-view-in-rds.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=b5e55af884e382a3fbc39bb24f8f0168)

Specify the **Parameter group family**, **Type**, **Group name**, and **Description**. All fields are required.

* Parameter group family: aurora-mysql8.0
* Type: DB Cluster Parameter Group (Note: Not "DB Parameter Group" type)
* Group name: psmigrationgroup (or your choice)
* Description: Parameter group for PlanetScale migration

You'll be brought back to the list of available parameter groups when you save.

**Step 2**

Edit the settings in your custom DB cluster parameter group. Select your parameter group from the list.

Click "**Edit parameters**" to unlock editing.

![The header of the view when editing a parameter group.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-header-of-the-view-when-editing-a-parameter-group.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=350c20efabe62fc1371216cb61c2d420)

Search for "**gtid**" and update:

* gtid-mode: ON
* enforce\_gtid\_consistency: ON

Search for "**sql\_mode**" and update:

* sql\_mode: NO\_ZERO\_IN\_DATE,NO\_ZERO\_DATE,ONLY\_FULL\_GROUP\_BY

Search for "**binlog\_format**" and update:

* binlog\_format: ROW

Click "**Save changes**".

**Step 3**

Associate the DB cluster parameter group to your database. Select "**Databases**" from the left nav, select your regional cluster (not writer or reader instance), and click "**Modify**".

Scroll to **Additional configuration** section. Update the **DB cluster parameter group** to your new parameter group. Click "**Continue**".

![The Additional configuration section of the database configuration view.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/amazon-aurora-migration-guide/the-additional-configuration-section-of-the-database-configuration-view.jpg?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=3303c0580b4e6c42a3b0e5629dab3a81)

Choose when to apply:

* **Apply during the next scheduled maintenance window** - Applied during maintenance window
* **Apply immediately** - Applied now, but requires manual reboot

Click "**Modify DB instance**".

**Step 4**

Reboot your database's writer instance to apply the settings. Click "**Actions**" > "**Reboot**". (Make sure you're selecting the writer instance, not the regional cluster.)

> [!WARNING]
>
> This will briefly disconnect active users! The parameter group changes won't take effect without a reboot.

Confirm the reboot. You can check the status in the databases list (click refresh to update).

## Step 2: Enable binary logging

Binary logging must be enabled for the import to work. On Aurora/RDS, binary logging is tied to automated backups.

To enable binary logging, [enable automated backups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html#USER_WorkingWithAutomatedBackups.Enabling) by setting the backup retention period to any value greater than zero days.

Verify binary logging is enabled:

```sql theme={null}
mysql> show variables like 'log_bin';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| log_bin       | ON    |
+---------------+-------+
```

## Step 3: Configure binlog retention

Set the binary log retention period to ensure logs aren't purged during the import. For most cases, 48 hours is sufficient, but larger imports may need more time.

> [!WARNING]
>
> Longer retention periods use more disk space. Evaluate your binlog size to avoid running out of disk space. Contact [PlanetScale Support](https://planetscale.com/contact?initial=support) if you need assistance.

Set the retention period using the `mysql.rds_set_configuration()` procedure:

```sql theme={null}
CALL mysql.rds_set_configuration('binlog retention hours', 48);
```

Verify the setting:

```sql theme={null}
CALL mysql.rds_show_configuration;
```

Expected output:

```
+------------------------+-------+-----------------------------------------------------------------------------------------------------------+
| name                   | value | description                                                                                               |
+------------------------+-------+-----------------------------------------------------------------------------------------------------------+
| binlog retention hours | 48    | binlog retention hours specifies the duration in hours before binary logs are automatically deleted.      |
+------------------------+-------+-----------------------------------------------------------------------------------------------------------+
```

## Step 4: Ensure database is publicly accessible

PlanetScale needs to connect to your Aurora database over the internet. Check that your database is publicly accessible.

In the writer instance, go to "**Connectivity & security**" tab. Under "**Security**", check if **Publicly accessible** is set to "Yes". If it says "No", you'll need to modify the database settings to enable public access.

If you cannot make the database publicly accessible, [contact us](https://planetscale.com/contact) to discuss alternative import options.

## Step 5: Create a migration user

Create a dedicated user with limited privileges for the import process.

Connect to your Aurora database using the MySQL command line with your master credentials:

```bash theme={null}
mysql -u admin -p -h [your-aurora-endpoint]
```

Run the following script, replacing the placeholders:

* `<SUPER_STRONG_PASSWORD>` - Password for the migration\_user account
* `<DATABASE_NAME>` - Name of the database you're importing

```sql theme={null}
CREATE USER 'migration_user'@'%' IDENTIFIED BY '<SUPER_STRONG_PASSWORD>';
GRANT PROCESS, REPLICATION SLAVE, REPLICATION CLIENT, RELOAD ON *.* TO 'migration_user'@'%';
GRANT SELECT, INSERT, UPDATE, DELETE, SHOW VIEW, LOCK TABLES ON `<DATABASE_NAME>`.* TO 'migration_user'@'%';
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, ALTER ON `ps\_import\_%`.* TO 'migration_user'@'%';
GRANT EXECUTE ON PROCEDURE mysql.rds_show_configuration TO 'migration_user'@'%';
GRANT SELECT ON mysql.db TO 'migration_user'@'%';
GRANT SELECT ON mysql.func TO 'migration_user'@'%';
GRANT SELECT ON mysql.innodb_table_stats TO 'migration_user'@'%';
GRANT SELECT ON mysql.tables_priv TO 'migration_user'@'%';
GRANT SELECT ON mysql.user TO 'migration_user'@'%';
GRANT SELECT ON performance_schema.* TO 'migration_user'@'%';
FLUSH PRIVILEGES;
```

Save the username and password securely - you'll need them for the import.

## Step 6: Configure RDS security group

Allow PlanetScale to connect by adding PlanetScale's IP addresses to your security group.

The specific IP addresses depend on your PlanetScale database region. See [Import public IP addresses](https://planetscale.com/docs/vitess/imports/import-tool-migration-addresses.md) for how to find them.

### Add IP addresses to security group

1. Navigate to "**Connectivity & security**" tab of your writer instance
2. Click the VPC security group link

![The Connectivity & security tab of the database view in RDS.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/amazon-aurora-migration-guide/the-connectivity-and-security-tab-of-the-database-view-in-aurora.jpg?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=54cc849d916f8168e22379b70aa00d88)

3. Select "**Inbound rules**" tab, then "**Edit inbound rules**"

![The view of security groups associated with the RDS instance.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-view-of-security-groups-associated-with-the-rds-instance.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=a00d564d0c17b2e5c4e1f2c44301927b)

4. Click "**Add rule**"
5. **Type**: Select `MYSQL/Aurora`
6. **Source**: Enter the first PlanetScale IP address (AWS will format it as `x.x.x.x/32`)
7. Repeat for each IP address in your region
8. Click "**Save rules**"

![The Edit inbound rules view where source traffic can be allowed.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-edit-inbound-rules-view-where-source-traffic-can-be-allowed.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=ff9336f2b92a2bb5d54f149f87ae6ff3)

## Importing your database

Now that your Aurora database is configured, follow the [Database imports guide](https://planetscale.com/docs/vitess/imports/database-imports.md) to complete your import.

When you [create the external keyspace](https://planetscale.com/docs/vitess/imports/database-imports.md#step-3-create-an-external-keyspace), use the following connection settings:

* **Host name** (`--host`) - Your Aurora cluster endpoint address (from Prerequisites)
* **Port** (`--port`) - 3306 (or your custom port)
* **Database name** (`--source-database`) - The exact database name to import
* **Username** (`--username`) - `migration_user`
* **Password** (`--password`) - The password you set in Step 5
* **SSL verification mode** (`--ssl-mode`) - Select based on your Aurora SSL configuration

The Database imports guide will walk you through:

* Creating your PlanetScale database
* Connecting to your Aurora database with an external keyspace
* Checking your server settings, user grants, and schema
* Starting the import with MoveTables
* Monitoring the import progress
* Switching traffic and completing the import

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
