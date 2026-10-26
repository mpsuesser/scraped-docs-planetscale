---
url: https://planetscale.com/docs/vitess/security/password-roles
title: "Password Roles"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Password roles

> PlanetScale allows you to [create and manage passwords](../connecting/connection-strings.md) for each branch of your database.

This page covers Vitess.

Postgres version: [Postgres](https://planetscale.com/docs/postgres/connecting/roles.md)

## Overview

> [!NOTE]
>
> The "roles" on this page are the four **access levels of a Vitess/MySQL branch
> password** (Read-only, Write-only, Read/Write, Admin). They are **not** the same
> as Postgres **roles**, which are the credential system for PlanetScale Postgres
> databases. If your database is Postgres, see [Managing
> roles](https://planetscale.com/docs/postgres/connecting/roles.md) instead.

PlanetScale passwords can be created with one of four roles:

* **Read-only** — Can query rows
* **Write-only** — Can modify rows
* **Read/Write** — Can query and modify rows
* **Admin** — All read/write permissions and can modify schema\*

\* *This does not apply to production branches with [safe migrations](https://planetscale.com/docs/vitess/schema-changes/safe-migrations.md) enabled, as we [do not allow direct DDL](https://planetscale.com/docs/vitess/schema-changes/how-online-schema-change-tools-work.md) on those branches, even if your password has the `Admin` role.*

## Create a password with custom role

**Step 1**

Go to your database settings page.

**Step 2**

Click "**Passwords**" > "**New password**".

**Step 3**

Give it a name, select the role from the dropdown, select the branch, and click "**Generate password**".

![PlanetScale password roles priority](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/password-roles/roles.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=10bccb8f7e7678d0ff93f9779dff17d8)

Once a password is created, **its role cannot be changed**.

The access level available to these roles is shown in the table below.

| Role name | Can create/edit schema | Can insert/update/delete rows | Can query rows |
| :- | :- | :- | :- |
| Read-only | | | |
| Write-only | | | |
| Read/write | | | |
| Admin | | | |

> [!NOTE]
>
> The default role for all passwords created by the **Connect** button is `Administrator`. Passwords with custom roles
> must be created from your database settings page.

## Troubleshooting

The following errors indicate that you do not have the permissions needed to perform an action. You must create a new password with a more privileged role to proceed.

**SELECT DENIED**

`Select command denied to user ‘planetscale-writer-only for table ‘customers’ (ACL check error) (CallerID: planetscale-writer-only)`

**INSERT DENIED**

`Insert command denied to user ‘planetscale-reader’ for table ‘customers’ (ACL check error) (CallerID: planetscale-reader)`

**DELETE DENIED**

`Delete command denied to user ‘planetscale-reader’ for table ‘customers’ (ACL check error) (CallerID: planetscale-reader)`

**DDL DENIED**

`DDL command denied to user ‘planetscale-writer' for table my-new-table’ (ACL check error) (CallerID: planetscale-writer)`

> [!NOTE]
>
> If your pscale CLI version is less than 0.94.0, please upgrade your installation by following [this
> document](https://planetscale.com/docs/cli/planetscale-environment-setup.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
