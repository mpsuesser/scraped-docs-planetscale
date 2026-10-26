---
url: https://planetscale.com/docs/vitess/schema-changes/safe-migrations
title: "Safe Migrations"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Safe migrations

> Safe migrations is an optional but highly recommended feature for branches in PlanetScale. With safe migrations enabled on a branch, you’ll gain zero-downtime schema migrations, schema reverts, and protection against accidental schema changes. The safe migrations setting is recommended for all production database branches to prevent downtime and unintentional data loss during schema migrations.

This page covers Vitess.

## Zero-downtime schema migrations

Safe migrations enable the [PlanetScale workflow](https://planetscale.com/docs/vitess/best-practices.md) on a given branch and allow your team to create deploy requests to merge schema changes into that branch. When changes are merged using deploy requests, a ghost table will be created with the desired schema changes. Your data will be continuously synchronized with that table until you decide to apply the changes.

## Schema revert

Safe migrations and deploy requests provide the option to quickly [revert schema changes](https://planetscale.com/docs/vitess/schema-changes/deploy-requests.md#revert-a-schema-change) if you discover that they are not compatible with your application. With schema revert enabled for a database, the old table is retained. You’re provided a 30-minute window where data will still be synchronized as writes occur on the new table. If you decide to revert your changes, the status of the two tables is flipped, bringing the former table back.

## Protection against accidental schema changes

To prevent accidental changes to the database schema, which may cause downtime, safe migrations enforce the use of [branching](https://planetscale.com/docs/vitess/schema-changes/branching.md) and [deploy requests](https://planetscale.com/docs/vitess/schema-changes/deploy-requests.md). This requires that changes be made safely and allows all team members to check and comment on schema changes before they are applied.

With safe migrations enabled, Data Definition Language (DDL) statements issued to branches with safe migrations enabled will automatically be rejected by PlanetScale. Any `CREATE`, `ALTER`, or `DELETE` commands, whether sent using the PlanetScale built-in console, terminal, or MySQL GUI, will fail when we receive them.

## Staging branches

You can use a development branch with safe migrations enabled to set up a workflow with a “staging” branch. First, make sure you have safe migration enabled for your main production branch. Then, create a “staging” branch with your main production branch as the base and turn on safe migrations. All new branches created for development can use this “staging” branch as the base branch.

You can then open a deploy request against either the main production or “staging” branch. Once it is deployed to “staging,” you can open a deploy request against the main production branch. The main production branch must be set as the default branch (found in your database's “Settings” page) to open a deploy request against it.

> [!NOTE]
>
> In this setup, the “staging” branch is still a development branch. Compared to your production branch, it will have reduced resources, similar to other development branches.

![View of the Branches tab with main \<- staging \<- dev branches](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/safe-migrations/branches-with-staging-branch.jpg?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=f7ddf5ad9f99a86f2121909ef57e6783)

## How to enable safe migrations

Safe migrations can be enabled using the PlanetScale dashboard or the pscale CLI.

### Using the PlanetScale dashboard

To enable safe migrations on a branch, select the branch you want to modify from the branch dropdown and click the **”cog”** in the upper right of the infrastructure card on the ”**Dashboard**” tab of the database.

![The production branch UI card.](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/safe-migrations/production-branch-card-with-sm-disabled-2.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=e69aef44ce888729b22a81aa59cca2d2)

In the modal, toggle the option labeled **”Enable safe migrations”**, then click the **”Enable safe migrations”** button to save and close the modal.

The UI card will reflect the status of the safe migrations for that branch.

![Branch UI card with safe migrations enabled.](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/safe-migrations/production-branch-card-with-sm-enabled-2.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=7cbcf6b5ea23eeceacc43d1443bf9553)

You can also access the same settings from the **”cog”** on a branch overview page (from the **”Branches”** tab, then select the branch you want to view or modify).

### Using the pscale CLI

To enable safe migrations on a branch using the pscale CLI, use the following command in your terminal:

```
pscale branch safe-migrations enable <DATABASE\_NAME> <BRANCH\_NAME>
```

## How to disable safe migrations

There are two ways to disable safe migrations: the PlanetScale dashboard and the CLI.

### Using the PlanetScale dashboard

To disable safe migrations, click the **”cog”** in the upper right of the infrastructure card on the ”**Dashboard**” tab of the database.

![Branch UI with enabled with cog highlighted.](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/safe-migrations/prod-card-cog-2.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=da093e115a9159f7d76cad61ca1ecc11)

In the modal, toggle the option labeled **”Enable safe migrations,”** then click the **”Disable safe migrations”** button to save and close the modal.

You can also access the same settings from the **”cog”** on a branch overview page (from the **”Branches”** tab, then select the branch you want to view or modify).

### Using the pscale CLI

To disable safe migrations on a branch using the pscale CLI, use the following command in your terminal:

```
pscale branch safe-migrations disable <DATABASE\_NAME> <BRANCH\_NAME>
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
