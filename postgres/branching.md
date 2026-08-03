---
url: https://planetscale.com/docs/postgres/branching
title: "Branching"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

When your PostgreSQL database is first initialized, a single production branch is created called `main` which acts as the default branch. You can then create development branches that you can use for development before shipping schema changes to production.

Branches are completely isolated databases. Changes made in one branch, whether to the schema or the data, do not affect any other branches for a given database. There is no data replication between branches, so writing data into one will not replicate to another.

New branches incur additional charges. The cost depends on the selected resources for the branch. The monthly price for the new branch will be shown during branch creation. See [pricing](pricing.md) for more information.

**A branch’s region is set at creation.** To serve traffic from a different region, you can migrate to a new branch. See [Changing regions](#changing-regions) for the steps.

## Development and production branches

PlanetScale Postgres provides two types of database branches:

- **Development branches** — Development branches run on `PS-DEV` instances that have limited performance capabilities, different egress rates, and no replicas. This is great for experimentation, testing new schemas, and limited exploration of your data.
- **Production branches** — Production branches are intended for production traffic and include optional replicas for high availability. Production branches can be created on non-HA ([single node](cluster-configuration/single-node.md)) or HA (primary + 2 replicas) nodes.

New branches created from the **Branches** page always start as development branches on `PS-DEV` instances. To convert a branch into a production branch, [upsize the cluster](cluster-configuration.md#adjusting-cluster-size) and [add replicas](cluster-configuration.md#managing-replicas) from the **Clusters** page. Only production branches can be set as the [default branch](#set-as-default-branch).

A database can have a maximum of `100` development branches at the same time. To create additional branches, delete existing development branches that are no longer needed. If you need a higher limit, [contact support](https://planetscale.com/contact).

## Create a branch

We are still in the process of building out our full branching functionality Postgres. You can currently create a new empty branch with no schema and no data or create a branch from a backup, which includes schema and data.

There are two ways to create a new Postgres branch: from the Branches page (no schema or data included) or by restoring from a backup (schema and data included).

Each branch is its own isolated database and uses its own storage separate from production. You will be charged for the storage consumed by all production and development branches.

We do not recommend using production data for development environments.

### From the Branches page

This method does not include schema or data.

Branches created via this method will always initialize as a single node `PS-DEV` database, which does not have any replicas and begins at $5/month, depending on the region. After initialization, you have the option to upsize the branch or add replicas from the “Clusters” page in the dashboard.

If [Restrict branch regions](settings.md#restrict-branch-regions) is enabled in database settings, new branches can only be created in the same region as the default branch.

### From a backup

This method includes both the schema and the data for the selected backup.

The cluster size, and therefore cost, is inherited from your main parent branch.

A new branch will be created based on this backup and will become visible under the **Branches** page.

## Changing regions

A branch stays in the region you select at creation, so switching regions means migrating to a new branch. The process is straightforward — create a branch in the target region, bring your schema and data over, and promote it when ready.

**Keep in mind:**

- There is no data replication between branches, so schema and data need to be copied manually as described above.
- Branches created [from a backup](#from-a-backup) inherit the region of the source branch, so backups alone won’t get you into a new region — use the migration steps instead.
- To run production in a new region, you’ll need to promote the new branch to default. See [Set as default branch](#set-as-default-branch).

## View all branches

To view all branches for your PostgreSQL database:

Similarly you can navigate directly to the Branches page to see all branches:

## Connect to a branch

Each branch has its own connection details. To connect to a specific branch:

For detailed connection instructions, see the [PostgreSQL connection documentation](connecting.md).

## Rename a branch

To rename a branch:

Renaming a branch does not affect that branch’s credentials. You do not need to regenerate credentials if you rename a branch.

## Set as default branch

The default branch serves as the source branch when creating new development branches.

Only production branches can be the default branch. New branches created from the **Branches** page start as development branches, so before a branch can be promoted to the default, you must first [upsize the cluster](cluster-configuration.md#adjusting-cluster-size) and [add replicas](cluster-configuration.md#managing-replicas) from the **Clusters** page to turn it into a production branch.

To change the default branch:

### From the Branches page

### From the Settings page

If you change the default branch and intend for it to power your production application, you may need to update your application credentials to reference the new default branch.

## Delete a branch

You can delete a branch that you no longer need:

**Important notes:**

- You cannot delete the default branch. You must first set another branch as the default branch.
- You cannot set development branches as the default branch.
- Only [Organization Administrators and Database Administrators](../security/access-control.md) have permission to delete production branches. Database Members can delete non-production branches.
- Development branches are billed only for the time they are used, so it’s beneficial to delete them when no longer needed.

## Schema changes

Since PlanetScale Postgres branches don’t use [deploy requests like in Vitess](../vitess/schema-changes/deploy-requests.md), you make schema changes directly on each branch:

There’s currently no automated way to merge schema changes between PlanetScale Postgres branches. You must manually copy your changes from development to production branches.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
