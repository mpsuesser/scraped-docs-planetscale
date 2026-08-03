---
url: https://planetscale.com/docs/vitess/imports/gcp-cloudsql-migration-guide
title: "Gcp Cloudsql Migration Guide"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Overview

This document will demonstrate how to migrate a database from Google Cloud Platform (GCP) Cloud SQL MySQL Cluster to PlanetScale using our [Import tool](database-imports.md).

This guide assumes you are using MySQL on GCP. Other database systems available through GCP will not work with the PlanetScale import tool.

We recommend reading through the [Database import documentation](database-imports.md) to learn how our import tool works before proceeding.

## Prerequisites

Before you can perform a migration, gather the following information from the GCP Console:

- **Public IP address** - Found in the **Overview** tab of your Cloud SQL cluster under the **Connect to this instance** section
- **Database name** - The name of the database you want to import
- **Root username and password** - You’ll need these to create the migration user

![The GCP Cloud SQL console with the IP address highlighted.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/gcp-cloudsql-migration-guide/cloudsql-ip-address.png?w=2500&fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=99e852a2d99299c14cdf1350bd3729ab)

The GCP Cloud SQL console with the IP address highlighted.

A list of your databases can be found in the **Databases** tab. In this guide, we’ll be using the `prod` database.

![The Databases list in the GCP console.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/gcp-cloudsql-migration-guide/cloudsql-databases.png?w=2500&fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=9095e921daf061354c09798e02c2f6bb)

The Databases list in the GCP console.

## Create a migration user

Create a migration user account with limited privileges for the import process. **You must run this as the root user or another user with admin privileges.** Connect to your Cloud SQL instance as root using the MySQL command line:

```shellscript
mysql -u root -p -h [your-cloud-sql-ip]
```

Then run the following script, making sure to update the placeholders:

- `<SUPER_STRONG_PASSWORD>` - The password for the `migration_user` account
- `<DATABASE_NAME>` - The name of the database you will import into PlanetScale

```sql
CREATE USER 'migration_user'@'%' IDENTIFIED BY '<SUPER_STRONG_PASSWORD>';
GRANT PROCESS, REPLICATION SLAVE, REPLICATION CLIENT, RELOAD ON *.* TO 'migration_user'@'%';
GRANT SELECT, INSERT, UPDATE, DELETE, SHOW VIEW, LOCK TABLES ON \`<DATABASE_NAME>\`.* TO 'migration_user'@'%';
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, ALTER ON *.* TO 'migration_user'@'%';
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, ALTER ON \`_vt\`.* TO 'migration_user'@'%';
GRANT SELECT ON mysql.db TO 'migration_user'@'%';
GRANT SELECT ON mysql.func TO 'migration_user'@'%';
GRANT SELECT ON mysql.innodb_table_stats TO 'migration_user'@'%';
GRANT SELECT ON mysql.tables_priv TO 'migration_user'@'%';
GRANT SELECT ON mysql.user TO 'migration_user'@'%';
GRANT SELECT ON performance_schema.* TO 'migration_user'@'%';
FLUSH PRIVILEGES;
```

Verify the grants were applied correctly:

```sql
SHOW GRANTS FOR 'migration_user'@'%';
```

You should see all the GRANT statements listed. If you only see one or two lines, the grants didn’t apply correctly.

**Important**

You must create the migration user on the MySQL command line and not in the GCP console. Creating users through the GCP console automatically grants the `cloudsqlsuperuser` role, which will cause the import to fail.

## Allow PlanetScale to connect to your Cloud SQL instance

For PlanetScale to connect to your database, you’ll need to update the Authorized networks for your cluster. The specific IP addresses to permit are shown during the import workflow on the **Connect to external database** step. The list includes IP addresses specific to your PlanetScale database region.

See the [Import public IP addresses](import-tool-migration-addresses.md) page for more details on where to find these IP addresses in the workflow. To permit traffic from these IP addresses to your database in GCP, select **Connections** from the navigation on the left. Under **Authorized networks**, click “ **Add network** ”. This will display an inline form for you to add a network. The name of the field is arbitrary, but the **Network** field should contain the IP address that needs access to your database. Click “ **Done** ” to add the new entry. Perform this step for each IP address for the selected region, then click “ **Save** ” to apply the settings.

![The form to add a new authorized network in the GCP console.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/gcp-cloudsql-migration-guide/cloudsql-networking.png?w=2500&fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=8698060bf4f771dda3af9b20c7ba01f4)

The form to add a new authorized network in the GCP console.

## Configure MySQL server settings

Certain MySQL server settings may need to be changed before you can begin the import. The initial connection test will fail if these settings are not configured correctly.

- binlog\_expire\_logs\_seconds

To set a flag in your GCP console, go to your database’s “ **Overview** ” page, select the “ **Edit** ” button, and then scroll down to the “ **Flags** ” section.

You want to select the “ **binlog\_expire\_logs\_seconds** ” flag and set it to `172800` seconds.

Make sure to select the “ **Done** ” button.

![The form to set MySQL flags.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/gcp-cloudsql-migration-guide/cloudsql-set-flags.png?w=2500&fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=ac3fc3a5f07de3f8cda478b6152a6149)

The form to set MySQL flags.

- log\_bin

If `log_bin` is set to OFF you may need to [enable Point in Time Recovery (PITR)](https://cloud.google.com/sql/docs/mysql/backup-recovery/pitr#enablingpitr) from the GCP console to start binary logging.

![The form to set enable point in time recovery.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/gcp-cloudsql-migration-guide/cloudsql-enable-pitr.png?w=2500&fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=7f94caa0babcb84f344723e6ab6791e7)

The form to set enable point in time recovery.

## Importing your database

Now that your GCP Cloud SQL database is configured and ready, follow the [Database Imports guide](database-imports.md) to complete your import.

When filling out the connection form in the import workflow, use the following information:

- **Host name** - Your GCP Cloud SQL public IP address (from Prerequisites)
- **Port** - 3306 (default for Cloud SQL)
- **Database name** - The exact database name to import
- **Username** - `migration_user`
- **Password** - The password you set for the migration user
- **SSL verification mode** - Select based on your Cloud SQL SSL configuration

The Database Imports guide will walk you through:

- Creating your PlanetScale database
- Connecting to your Cloud SQL database
- Validating your configuration
- Selecting tables to import
- Monitoring the import progress
- Switching traffic and completing the import

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
