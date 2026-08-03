---
url: https://planetscale.com/docs/vitess/imports/digitalocean-database-migration-guide
title: "Digitalocean Database Migration Guide"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Introduction

In this article, we’ll walk through migrating a MySQL database from DigitalOcean to PlanetScale using the [Import tool](database-imports.md).

This guide assumes you are using MySQL on DigitalOcean. Other database systems available through DigitalOcean will not work with the PlanetScale import tool.

We recommend reading through the [Database import documentation](database-imports.md) to learn how our import tool works before proceeding.

## Prerequisites

Before you can start migrating your database, you’ll also need to collect the following information from your DigitalOcean cluster:

- The admin username and password
- The database host
- The port
- Database name

Most of this information is located on the landing page of your cluster.

![The Overview of the database cluster.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/digitalocean-database-migration-guide/the-overview-of-the-database-cluster.png?w=2500&fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=eb908479c13c97fd13292488104f9b03)

The Overview of the database cluster.

You can view the list of databases in the “ **Users & Databases** ” tab. This article will use the default database created when the cluster was initialized, named `defaultdb`.

![The Users & Databases view of the cluster with the Databases section highlighted.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/digitalocean-database-migration-guide/the-users-and-databases-view-of-the-cluster-with-the-databases-section-highlighted.png?w=2500&fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=026b545ca5224e3284618a7cd198b16d)

The Users & Databases view of the cluster with the Databases section highlighted.

If you don’t know the admin password, you can create a new set of credentials using the information on the [Import tool user permissions page](import-tool-user-requirements.md) to create an account that can be used to import your database.

## Update trusted sources

In order for PlanetScale to connect to your DigitalOcean database, you must allow network traffic into the database by adding the necessary IP addresses to the trusted sources list in DigitalOcean. The specific IP addresses you will need to allow depend on the region you plan to host your PlanetScale database. Check the [Import tool public IP addresses page](import-tool-migration-addresses.md) to determine the IP addresses to allow before continuing. This guide will use the **AWS us-east-1 (North Virginia)** region so we’ll allow the following addresses:

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

In the DigitalOcean dashboard, navigate to the “ **Settings”** tab of your database and locate **Trusted sources** in the list of configuration items. Click “ **Edit”** and the row should change to allow edits to the setting.

![The Settings tab of the database cluster in Digital Ocean.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/digitalocean-database-migration-guide/the-settings-tab-of-the-database-cluster-in-digital-ocean.png?w=2500&fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=599f875b4c69576eb1cb647b2be4698c)

The Settings tab of the database cluster in Digital Ocean.

When you enter an IP address from the list, a message will appear below the input box asking if you want to add that IP as an address. Click that message to add it to the list. Repeat this step for each IP address that needs to be added, then click “ **Save** ” once you are done.

![The Trusted sources section of the Settings tab.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/digitalocean-database-migration-guide/the-trusted-sources-section-of-the-settings-tab.png?w=2500&fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=b8d77470a59cba94d01f79ed89318f89)

The Trusted sources section of the Settings tab.

### Disable ANSI\_QUOTES setting

The default settings for MySQL databases on DigitalOcean is to have `ANSI_QUOTES` enabled in the global MySQL settings, which is not supported by PlanetScale. To remove this setting, navigate to the “ **Settings** ” tab of your cluster and locate the section titled **Global SQL mode**. Click “ **Edit** ” in that section to change the configuration settings.

![The Settings tab of the database cluster in DigitalOcean with the Global SQL mode section highlighted.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/digitalocean-database-migration-guide/the-settings-tab-of-the-database-cluster-in-digitalocean-with-the-global-sql-mode-section-highlighted.png?w=2500&fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=57ce01510a1a8a2d0dc477fae71743a2)

The Settings tab of the database cluster in DigitalOcean with the Global SQL mode section highlighted.

To remove the `ANSI_QUOTES` setting, click the “ **x** ” next to the tag and click “ **Save**.” The change should apply immediately.

![An example of removing the ANSI_QUOTES setting from the Global SQL mode settings.](https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/digitalocean-database-migration-guide/an-example-of-removing-the-ansi_quotes-setting-from-the-global-sql-mode-settings.png?w=2500&fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=46871147fcd04490d6d22aa6e62fe523)

An example of removing the ANSI\_QUOTES setting from the Global SQL mode settings.

### Set the Binlog Retention Period

The binary log related [MySQL server variables](database-imports.md#server-configuration-check) required for PlanetScale’s importer are already set to acceptable values by default on Digital Ocean managed MySQL servers but there’s one more variable to check on the “ **Settings** ” tab of your existing cluster.

Scroll down to “ **Advanced configurations** ” and ensure the “ **Binlog Retention Period** ” is set to the maximum value of `86400` seconds.

![Setting the binlog retention period under Advanced Configurations](https://mintcdn.com/planetscale-2/xsX1e-5IXCYXbX59/vitess/imports/digitalocean-binlog-retention.png?w=2500&fit=max&auto=format&n=xsX1e-5IXCYXbX59&q=85&s=ff28a4434c6bec127a5dfa1402a742ca)

Setting the binlog retention period under Advanced Configurations

## Importing your database

Now that your DigitalOcean database is configured and ready, follow the [Database Imports guide](database-imports.md) to complete your import.

When filling out the connection form in the import workflow, use the following information:

- **Host name** - Your DigitalOcean database host (from Prerequisites)
- **Port** - Your port (typically 25060 for DigitalOcean)
- **Database name** - The exact database name to import (e.g., `defaultdb`)
- **Username** - Your admin username
- **Password** - Your admin password
- **SSL verification mode** - Select based on your DigitalOcean SSL configuration

The Database Imports guide will walk you through:

- Creating your PlanetScale database
- Connecting to your DigitalOcean database
- Validating your configuration
- Selecting tables to import
- Monitoring the import progress
- Switching traffic and completing the import

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
