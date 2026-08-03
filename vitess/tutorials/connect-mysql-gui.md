---
url: https://planetscale.com/docs/vitess/tutorials/connect-mysql-gui
title: "Connect Mysql Gui"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

## Introduction

In this tutorial, you’ll learn how to connect to a PlanetScale database using a MySQL GUI. While this tutorial uses Sequel Ace as a demonstration, many applications that connect to MySQL databases will support connecting to and querying a PlanetScale database as long as the applicaton supports connecting over SSL.

## Gather the credentials

To connect to a PlanetScale database, you’ll need four pieces of information:

- The database name
- Host name
- Username
- Password

The easiest way to gather this information is by selecting the **Connect** button from the **Dashboard** tab. Then, on the **Connect** page, select the branch that you wish to connect to and click the **Create password** button. Within the **Select a language or framework** section, select “Other” to display the connection details as a list instead of a language or framework-specific connection string.

As a security best practice, passwords are only displayed when they are created.

## Connect to the database

In the application you are using, enter the access information you gathered in the previous step into the appropriate fields. Make sure to check **“Require SSL”** as SSL is required to connect to a PlanetScale database. Click **“Connect”** once you are finished.

![The new connection window in Sequel Ace.](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/connect-mysql-gui/ace-connect.png?w=2500&fit=max&auto=format&n=AJPY38bILe2zenXX&q=85&s=d02db10a7fcba42895e6322ceda5275d)

The new connection window in Sequel Ace.

If the connection is successful, you should be able to query your database and perform other [supported operations](../troubleshooting/mysql-compatibility.md).

![A sample query in Sequel Ace.](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/connect-mysql-gui/ace-query.png?w=2500&fit=max&auto=format&n=AJPY38bILe2zenXX&q=85&s=0b569521c9eff7a53e581542fd50e41d)

A sample query in Sequel Ace.

## Caveats

While many standard MySQL statements are supported, there are a few caveats worth calling out:

## Tested GUIs

The following MySQL GUI applications have been tested and confirmed to work with PlanetScale databases:

## Sequel Ace

## TablePlus

## MySQL Workbench

## JetBrains DataGrip

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
