---
url: https://planetscale.com/docs/security/multiple-admins-required-for-deletion
title: "Multiple Admins Required For Deletion"
description: ""
access_date: 2026-10-29T14:57:53.846Z
current_date: 2026-10-29T14:57:53.846Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Require multiple administrators for deletion

> Require two administrators to approve database deletion

Available for Vitess, Neki, and Postgres.

When `multiple_admins_required_for_deletion` is enabled, two different [Organization Administrators](https://planetscale.com/docs/security/access-control.md#organization-administrator) or [Database Administrators](https://planetscale.com/docs/security/access-control.md#database-administrator) must request deletion before the database can be deleted. The first request records intent. The second request, from a different administrator, completes the delete.

To enable this setting, [contact PlanetScale Support](https://planetscale.com/contact?initial=support) and include the branch you want it enabled for.

Your organization must have at least two administrators so a second administrator can approve the delete.

## What this setting covers

With the setting on, two administrators must approve:

* Database deletion, including all branches, data, and backups
* [Keyspace](https://planetscale.com/docs/vitess/sharding.md) deletion on Vitess databases

If a database also has [deletion protection](https://planetscale.com/docs/security/deletion-protection.md) enabled, disable that protection before administrators can request deletion.

## Request deletion

**Step 1**

From the PlanetScale organization dashboard, select the database

**Step 2**

Open **Settings**

**Step 3**

Click **Delete database** and confirm the database name

The dashboard shows **Deleting this database requires two administrators** and lists outstanding requests. After you submit a request, you can cancel your own request from that same settings section. A second administrator must then request deletion for the delete to proceed.

The same two-administrator flow appears when deleting a Vitess keyspace.

## Check whether it is enabled

The [Get a database](https://planetscale.com/docs/api/reference/get_database.md) API response includes `multiple_admins_required_for_deletion`.

Terraform database data sources expose the same attribute.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
