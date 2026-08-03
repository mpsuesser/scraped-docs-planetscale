---
url: https://planetscale.com/docs/vitess/delete-a-database
title: "Delete A Database"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

To access the settings for deleting a database:

## Delete Database

The settings page provides the option to permanently delete your database. This action is irreversible and will:

- Delete the entire database and all of its branches
- Remove all data and backups permanently
- Disconnect any applications currently connected to the database
- Include usage charges in your invoice through the deletion date

Database deletion cannot be undone. Make sure you have proper backups and that no critical applications depend on this database before proceeding.

To delete a database:

Only organization administrators and database administrators have permission to delete databases. See the [Access Control documentation](../security/access-control.md) for more information about user permissions.

## IP Restrictions

If you would like to ‘soft delete’ your database first, you can instead enable IP restrictions on the passwords for that database.

To enable IP restrictions:

This will restrict the IP range to what is specified and can be used to test if connection are going to the database via the password in question. This is a useful test for determining if the password in question is still in use, before deleting either the database or the password. You should also confirm in [Insights](monitoring/query-insights.md) that no queries are executing on the database.

This helps avoid remaking passwords that are still in use or deleting a database that is still in use.

Once you’ve confirmed that no traffic is using any of your database passwords, you can follow the steps above to delete the database if desired and safe.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
