---
url: https://planetscale.com/docs/vitess/imports/azure-database-for-mysql-migration-guide
title: "Azure Database For Mysql Migration Guide"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Overview

This document will demonstrate how to migrate a database from Azure Database for MySQL to PlanetScale. We recommend reading through the [Database import documentation](database-imports.md) to learn how our import tool works before proceeding.

## Prerequisites

Before you can perform the migration, you’ll need to gather the following information from you MySQL instance in Azure:

- Server name
- Server admin login name
- Server admin password
- Database name

The server name and admin login name can be located on the **Overview** tab of the MySQL instance in Azure.

![The server name and server admin login name located in the Azure dashboard.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/azure-database-for-mysql-migration-guide/the-server-name-and-server-admin-login-name-located-in-the-azure-dashboard.png?w=2500&fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=e5d068e2ad53f78c28ec943fbe7c444d)

The server name and server admin login name located in the Azure dashboard.

The server admin password is the same password you set when initially creating the database instance.

To view your available databases, select the **Databases** tab from the sidebar.

![The databases tab of the Azure dashboard.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/azure-database-for-mysql-migration-guide/the-databases-tab-of-the-azure-dashboard.png?w=2500&fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=124174c4d488201b575daaa2bbe70eca)

The databases tab of the Azure dashboard.

## Configure firewall rules

In order for PlanetScale to connect to your Azure database, you must allow traffic into the database through the associated security group. The specific IP addresses you will need to allow depend on the region you plan to host your PlanetScale database. Check the [Import tool public IP addresses page](import-tool-migration-addresses.md) to determine the IP addresses to allow before continuing. This guide will use the **AWS us-east-1 (North Virginia)** region so we’ll allow the following addresses:

```text
3.209.149.66
3.215.97.46
34.193.111.15
23.23.187.137
52.6.141.108
52.70.2.89
50.17.188.76
52.2.251.189
52.72.234.74
35.174.68.24
52.5.253.172
54.156.81.4
34.200.24.255
35.174.79.154
44.199.177.24
```

To allow traffic into your Azure database, navigate to the “ **Networking** ” section from the sidebar and locate the **Firewall rules** section. There are already a series of inputs allowing you to add entries into the Firewall rules, each of which will permit network traffic from that IP address. Add a new entry for each address required, then click “Save” from the toolbar.

![The networking tab of the Azure dashboard.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/azure-database-for-mysql-migration-guide/the-networking-tab-of-the-azure-dashboard.png?w=2500&fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=fb1a356a226fb174d0347c27d885cd8c)

The networking tab of the Azure dashboard.

## Configure MySQL server settings

There are three settings that need to be configured before you can import your database:

- gtid\_mode
- enforce\_gtid\_consistency
- binlog\_row\_image

To access these settings in Azure, select “ **Server parameters** ” from the sidebar and enter “ **gtid** ” in the search bar. Set both “ **enforce\_gtid\_consistency** ” and “ **gtid\_mode** ” to “ **ON** ”. Next, search for “ **binlog\_row\_image** ” and set to “ **full** ”. Click “ **Save** ”.

For “ **gtid\_mode** ”, you’ll need to update the value in sequence displayed in the dropdown until it is set to “ **ON** ”. For example, if the current setting is “ **OFF\_PERMISSIVE** ”, you’ll need to first change it to “ **ON\_PERMISSIVE** ”, save the changes, then set it to “ **ON** ” in that order.

![How to access gtid settings in the Azure dashboard.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/azure-database-for-mysql-migration-guide/how-to-access-gtid-settings-in-the-azure-dashboard.png?w=2500&fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=c1a71397fade758d9ffe501913adc1db)

How to access gtid settings in the Azure dashboard.

## Import your database

Now that your Azure Database for MySQL is configured and ready, follow the [Database Imports guide](database-imports.md) to complete your import.

When filling out the connection form in the import workflow, use the following information:

- **Host name** - Your Azure server name (from Prerequisites)
- **Port** - 3306 (default for Azure MySQL)
- **Database name** - The exact database name to import
- **Username** - Your server admin login name
- **Password** - Your server admin password
- **SSL verification mode** - Select “ **Verify Identity** ” (Verify certificate and hostname)

The Database Imports guide will walk you through:

- Creating your PlanetScale database
- Connecting to your Azure MySQL database
- Validating your configuration
- Selecting tables to import
- Monitoring the import progress
- Switching traffic and completing the import

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
