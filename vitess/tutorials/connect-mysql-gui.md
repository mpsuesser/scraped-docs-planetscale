---
url: https://planetscale.com/docs/vitess/tutorials/connect-mysql-gui
title: "Connect Mysql Gui"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Connect a MySQL GUI to PlanetScale

This page covers Vitess.

## Introduction

In this tutorial, you'll learn how to connect to a PlanetScale database using a MySQL GUI. While this tutorial uses Sequel Ace as a demonstration, many applications that connect to MySQL databases will support connecting to and querying a PlanetScale database as long as the applicaton supports connecting over SSL.

## Gather the credentials

To connect to a PlanetScale database, you'll need four pieces of information:

* The database name
* Host name
* Username
* Password

The easiest way to gather this information is by selecting the **Connect** button from the **Dashboard** tab. Then, on the **Connect** page, select the branch that you wish to connect to and click the **Create password** button. Within the **Select a language or framework** section, select "Other" to display the connection details as a list instead of a language or framework-specific connection string.

> [!NOTE]
>
> As a security best practice, passwords are only displayed when they are created.

## Connect to the database

In the application you are using, enter the access information you gathered in the previous step into the appropriate fields. Make sure to check **"Require SSL"** as SSL is required to connect to a PlanetScale database. Click **"Connect"** once you are finished.

![The new connection window in Sequel Ace.](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/connect-mysql-gui/ace-connect.png?fit=max\&auto=format\&n=AJPY38bILe2zenXX\&q=85\&s=917a6f3faaaa65eca280742f4db0e73e)

If the connection is successful, you should be able to query your database and perform other [supported operations](https://planetscale.com/docs/vitess/troubleshooting/mysql-compatibility.md).

![A sample query in Sequel Ace.](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/connect-mysql-gui/ace-query.png?fit=max\&auto=format\&n=AJPY38bILe2zenXX\&q=85\&s=e7b77e5ea7ca4d1b2f825004497b3910)

## Caveats

While many standard MySQL statements are supported, there are a few caveats worth calling out:

**Step 1**

Each branch of a PlanetScale database is considered an isolated MySQL database. You'll need separate connection details per branch.

**Step 2**

Production branches with [safe migrations](https://planetscale.com/docs/vitess/schema-changes/safe-migrations.md) enforce the use of [branching](https://planetscale.com/docs/vitess/schema-changes/branching.md) and [deploy requests](https://planetscale.com/docs/vitess/schema-changes/deploy-requests.md) to safely make schema changes and do not support direct DDL as a result. However, DDL is supported on development branches and production branches without safe migrations enabled (not recommended).

**Step 3**

Creating new databases is not supported using any GUI tool.

## Tested GUIs

The following MySQL GUI applications have been tested and confirmed to work with PlanetScale databases:

> **[Sequel Ace](https://sequel-ace.com/)**

> **[TablePlus](https://tableplus.com/)**

> **[MySQL Workbench](https://www.mysql.com/products/workbench/)**

> **[JetBrains DataGrip](https://www.jetbrains.com/datagrip/)**

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
