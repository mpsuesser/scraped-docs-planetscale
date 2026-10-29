---
url: https://planetscale.com/docs/security/deletion-protection
title: "Deletion Protection"
description: ""
access_date: 2026-10-29T14:57:53.846Z
current_date: 2026-10-29T14:57:53.846Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Deletion protection

> Block database and branch deletion until protection is turned off

Available for Vitess, Neki, and Postgres.

Deletion protection blocks anyone from deleting a database or branch until an administrator turns protection off. Use it to add an explicit step before a destructive delete.

For an additional safeguard that requires two administrators to approve a delete, see [Require multiple administrators for deletion](https://planetscale.com/docs/security/multiple-admins-required-for-deletion.md).

## Protect a database

Organization Administrators and Database Administrators can enable protection from the database **Settings** page.

**Step 1**

From the PlanetScale organization dashboard, select the database

**Step 2**

Open **Settings**

**Step 3**

Turn on **Prevent database deletion**

While protection is on, delete attempts are blocked until you disable protection. Turning protection off does not delete the database.

You can also set database deletion protection with the [Update database settings](https://planetscale.com/docs/api/reference/update_database_settings.md) API (`deletion_protected`).

## Protect a branch

Individual branches can be protected separately from the database. Enable **Prevent branch deletion** when you create a branch, or later from the branch page.

From the [PlanetScale CLI](https://planetscale.com/docs/cli/branch.md):

```bash theme={null}
pscale branch update <DATABASE_NAME> <BRANCH_NAME> --deletion-protected
pscale branch update <DATABASE_NAME> <BRANCH_NAME> --deletion-protected=false
```

You can also set branch deletion protection with the [Update a branch](https://planetscale.com/docs/api/reference/update_branch.md) API (`deletion_protected`).

Terraform can enforce the same safeguard with `deletion_protected` on Neki branches, or with `lifecycle { prevent_destroy = true }` on other branch resources. See [Deletion protection](https://planetscale.com/docs/terraform.md#deletion-protection) in the Terraform documentation.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
