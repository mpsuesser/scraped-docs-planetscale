---
url: https://planetscale.com/docs/vitess/delete-a-database
title: "Delete A Database"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Deleting a database

> How to remove a database from PlanetScale.

This page covers Vitess.

To access the settings for deleting a database:

**Step 1**

From the PlanetScale organization dashboard, select your database

**Step 2**

Navigate to **Settings** on the left sidebar menu

## Delete Database

The settings page provides the option to permanently delete your database. This action is irreversible and will:

* Delete the entire database and all of its branches
* Remove all data and backups permanently
* Disconnect any applications currently connected to the database
* Include usage charges in your invoice through the deletion date

> [!WARNING]
>
> Database deletion cannot be undone. Make sure you have proper backups and that no critical applications depend on this database before proceeding.

To delete a database:

**Step 1**

Scroll to the bottom of the Settings page

**Step 2**

Click the red **Delete database** button

**Step 3**

Enter the database name

**Step 4**

Click **Delete database** to confirm

Only organization administrators and database administrators have permission to delete databases. See the [Access Control documentation](https://planetscale.com/docs/security/access-control.md) for more information about user permissions.

## IP Restrictions

If you would like to 'soft delete' your database first, you can instead enable IP restrictions on the passwords for that database.

To enable IP restrictions:

**Step 1**

From the **Settings** page, select **Passwords** from the list

**Step 2**

Click the three dots button next to the password you wish to restrict

**Step 3**

Click the **Manage IP restrictions** button

**Step 4**

Add in an IP range that is NOT used by any clients and click the **Add** button

This will restrict the IP range to what is specified and can be used to test if connection are going to the database via the password in question.
This is a useful test for determining if the password in question is still in use, before deleting either the database or the password.
You should also confirm in [Insights](https://planetscale.com/docs/vitess/monitoring/query-insights.md) that no queries are executing on the database.

This helps avoid remaking passwords that are still in use or deleting a database that is still in use.

Once you've confirmed that no traffic is using any of your database passwords, you can follow the steps above to delete the database if desired and safe.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
