---
url: https://planetscale.com/docs/vitess/imports/aws-rds-migration-guide
title: "Aws Rds Migration Guide"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# AWS RDS migration guide

This page covers Vitess.

## Overview

This document will demonstrate how to migrate a database from AWS Relational Database Services (RDS) to PlanetScale.

> [!NOTE]
>
> This guide assumes you are using MySQL on Amazon RDS. If you are using Amazon Aurora (MySQL compatible) on RDS, follow the [Amazon Aurora migration guide](https://planetscale.com/docs/vitess/imports/amazon-aurora-migration-guide.md). Other database systems (non-MySQL or MariaDB databases) available through RDS will not work with this guide.

We recommend reading through the [Database import documentation](https://planetscale.com/docs/vitess/imports/database-imports.md) to learn how imports work before proceeding.

## Prerequisites

Gather the following information from the AWS Console:

* **Database endpoint address** - Located in "**Connectivity & security**" tab of your database instance
* **Port number** - Typically 3306
* **Master username and password** - Your RDS root credentials

![The Connectivity & security tab of the database in RDS.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-connectivity-and-security-tab-of-the-database-in-rds.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=20474788ee59661d90250f15bb6f0be3)

## Step 1: Configure server settings

Your RDS database needs specific server settings configured before you can import. Follow these steps to configure GTID mode, binlog format, and sql\_mode.

### Check your current parameter group

Your Amazon RDS database is either using the default DB parameter group (e.g., default.mysql8.0) or a custom one. You can view it in the "**Configuration**" tab of your database instance.

![The Configuration tab of the database view in RDS.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-configuration-tab-of-the-database-view-in-rds.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=a30ebea8e2762b69a3b7e33e1fba3b8f)

### Configure the parameter group

**Step 1**

If you are using the default DB parameter group, you'll need to create a new parameter group to reconfigure settings.

To create a parameter group, select "**Parameter groups**" from the left nav and then "**Create parameter group**".

![The Parameter groups view in RDS.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-parameter-groups-view-in-rds.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=b5e55af884e382a3fbc39bb24f8f0168)

Specify the **Parameter group family**, **Type**, **Group name**, and **Description**. All fields are required.

* Parameter group family: mysql8.0 (or your MySQL version)
* Type: DB Parameter Group (Note: Not "DB Cluster Parameter Group" type)
* Group name: psmigrationgroup (or your choice)
* Description: Parameter group for PlanetScale migration

![The form used to create a parameter group.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-form-used-to-create-a-parameter-group.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=3476446fe5a00f8ef0bfd38e9525f57c)

You'll be brought back to the list of available parameter groups when you save.

**Step 2**

Edit the settings in your custom DB parameter group. Select your parameter group from the list.

![The list of parameter groups in RDS.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-list-of-parameter-groups-in-rds.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=15cfa3fa50d42549371c6d1d341a8e03)

Click "**Edit parameters**" to unlock editing.

![The header of the view when editing a parameter group.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-header-of-the-view-when-editing-a-parameter-group.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=350c20efabe62fc1371216cb61c2d420)

Search for "**binlog\_format**" and update:

* binlog\_format: ROW

![The binlog\_format configuration required.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-binlog_format-configuration-required.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=d9508a7c11a68b30486c33fb1d1c57dc)

Search for "**gtid**" and update:

* gtid-mode: ON
* enforce\_gtid\_consistency: ON

Search for "**sql\_mode**" and update:

* sql\_mode: NO\_ZERO\_IN\_DATE,NO\_ZERO\_DATE,ONLY\_FULL\_GROUP\_BY

![The GTID configurations that are required.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-gtid-configurations-that-are-required.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=12cbd3d9ddd38a10b5fbdf4e43b341c8)

Click "**Save changes**".

**Step 3**

Associate the parameter group to your database. Select "**Databases**" from the left nav, check the **select box** next to your database instance, and click "**Modify**".

![The list of databases in RDS.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-list-of-databases-in-rds.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=6dfc1a2b2b77a6babd2621aec8647c10)

Scroll to **Additional configuration** section. Update the **DB parameter group** to your new parameter group. Click "**Continue**".

![The Additional configuration section of the database configuration view.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-additional-configuration-section-of-the-database-configuration-view.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=ed9606c46e59229a04fc7e5d2ec5b455)

Choose when to apply:

* **Apply during the next scheduled maintenance window** - Applied during maintenance window
* **Apply immediately** - Applied now, but requires manual reboot

![The confirmation view that is displayed when modifying RDS database settings.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-confirmation-view-that-is-displayed-when-modifying-rds-database-settings.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=80c6516675d0491dc1469a18ff8434a2)

Click "**Modify DB instance**".

**Step 4**

Reboot your database instance to apply the settings. Click "**Actions**" > "**Reboot**".

> [!WARNING]
>
> This will briefly disconnect active users! The parameter group changes won't take effect without a reboot.

You'll be presented with a page to confirm the database you want to reboot. Click "**Confirm**" and the database will begin rebooting.

If you opted to apply changes immediately, monitor the status on the "**Configuration**" tab. The page does not automatically update, so refresh to check status.

![The configuration tab with the new parameter group applying.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-configuration-tab-with-the-new-parameter-group-applying.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=eb449d643c5de82d594b27ab26c0f64a)

## Step 2: Enable binary logging

Binary logging must be enabled for the import to work. On RDS, binary logging is tied to automated backups.

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

PlanetScale needs to connect to your RDS database over the internet. Check that your database is publicly accessible.

In your database instance, go to "**Connectivity & security**" tab. Under "**Security**", check if **Publicly accessible** is set to "Yes". If it says "No", you'll need to modify the database settings to enable public access.

If you cannot make the database publicly accessible, [contact us](https://planetscale.com/contact) to discuss alternative import options.

## Step 5: Create a migration user

Create a dedicated user with limited privileges for the import process.

Connect to your RDS database using the MySQL command line with your master credentials:

```bash theme={null}
mysql -u admin -p -h [your-rds-endpoint]
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

Navigate to "**Connectivity & security**" tab of your database instance and click the VPC security group link.

![The Connectivity & security tab of the database view in RDS.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-connectivity-and-security-tab-of-the-database-view-in-rds.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=68e1c754e4f5d7f5632443582b845362)

Select "**Inbound rules**" tab, then "**Edit inbound rules**".

![The view of security groups associated with the RDS instance.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-view-of-security-groups-associated-with-the-rds-instance.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=a00d564d0c17b2e5c4e1f2c44301927b)

Click "**Add rule**", then:

* **Type**: Select `MYSQL/Aurora`
* **Source**: Enter the first PlanetScale IP address (AWS will format it as `x.x.x.x/32`)

Repeat for each IP address in your region, then click "**Save rules**".

![The Edit inbound rules view where source traffic can be allowed.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/aws-rds-migration-guide/the-edit-inbound-rules-view-where-source-traffic-can-be-allowed.png?fit=max\&auto=format\&n=TneybaJ6MA8SGyM3\&q=85\&s=ff9336f2b92a2bb5d54f149f87ae6ff3)

## Importing your database

Now that your RDS database is configured, follow the [Database imports guide](https://planetscale.com/docs/vitess/imports/database-imports.md) to complete your import.

When you [create the external keyspace](https://planetscale.com/docs/vitess/imports/database-imports.md#step-3-create-an-external-keyspace), use the following connection settings:

* **Host name** (`--host`) - Your RDS endpoint address (from Prerequisites)
* **Port** (`--port`) - 3306 (or your custom port)
* **Database name** (`--source-database`) - The exact database name to import
* **Username** (`--username`) - `migration_user`
* **Password** (`--password`) - The password you set in Step 5
* **SSL verification mode** (`--ssl-mode`) - Select based on your RDS SSL configuration

The Database imports guide will walk you through:

* Creating your PlanetScale database
* Connecting to your RDS database with an external keyspace
* Checking your server settings, user grants, and schema
* Starting the import with MoveTables
* Monitoring the import progress
* Switching traffic and completing the import

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
