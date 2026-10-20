---
url: https://planetscale.com/docs/postgres/branching
title: "Branching"
description: ""
access_date: 2026-10-20T18:01:41.586Z
current_date: 2026-10-20T18:01:41.586Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Branching

> Branches on PlanetScale Postgres are isolated database deployments that provide you with separate environments for development and testing, as well as restoring from backups.

When your PostgreSQL database is first initialized, a single production branch is created called `main` which acts as the default branch. You can then create development branches that you can use for development before shipping schema changes to production.

Branches are completely isolated databases. Changes made in one branch, whether to the schema or the data, do not affect any other branches for a given database. There is no data replication between branches, so writing data into one will not replicate to another.

> [!NOTE]
>
> New branches incur additional charges. The cost depends on the selected resources for the branch. The monthly price for the new branch will be shown during branch creation. See [pricing](pricing.md) for more information.

> [!WARNING]
>
> **A branch's region is set at creation.** To serve traffic from a different region, you can migrate to a new branch. See [Changing regions](#changing-regions) for the steps.

## Development and production branches

PlanetScale Postgres provides two types of database branches:

* **Development branches** — Development branches run on `PS-DEV` instances that have limited performance capabilities, different egress rates, and no replicas. This is great for experimentation, testing new schemas, and limited exploration of your data.

* **Production branches** — Production branches are intended for production traffic and include optional replicas for high availability. Production branches can be created on non-HA ([single node](cluster-configuration/single-node.md)) or HA (primary + 2 replicas) nodes.

New branches created from the **Branches** page always start as development branches on `PS-DEV` instances. To convert a branch into a production branch, [upsize the cluster](cluster-configuration.md#adjusting-cluster-size) and [add replicas](cluster-configuration.md#managing-replicas) from the **Clusters** page. Only production branches can be set as the [default branch](#set-as-default-branch).

Each database allows `100` development branches by default. Change the limit on the database [Settings](settings.md#development-branch-limit) page. The limit can be any number from 1 to 5,000. When a database is at its limit, delete development branches you no longer need, or raise the limit, before creating another one. For a limit above 5,000, [contact support](https://planetscale.com/contact).

## Create a branch

There are two ways to create a new Postgres branch: from the Branches page (no schema or data included) or by restoring from a backup (schema and data included).

Each branch is its own isolated database and uses its own storage separate from production. You will be charged for the storage consumed by all production and development branches.

We do not recommend using production data for development environments.

### From the Branches page

This method does not include schema or data.

Branches created via this method will always initialize as a single node `PS-DEV` database, which does not have any replicas and begins at \$5/month, depending on the region. After initialization, you have the option to upsize the branch or add replicas from the "Clusters" page in the dashboard.

**Step 1**

Click **Branches**

**Step 2**

Click **New branch**

**Step 3**

Give the branch a name

**Step 4**

Choose the base branch. This currently does not copy the schema.

**Step 5**

Select a region. See [Changing regions](#changing-regions) to migrate to a different region later.

**Step 6**

Click **Create branch**

> [!NOTE]
>
> If [Restrict branch regions](settings.md#restrict-branch-regions) is enabled in database settings, new branches can only be created in the same region as the default branch.

### From a backup

This method includes both the schema and the data for the selected backup.

The cluster size, and therefore cost, is inherited from your main parent branch.

**Step 1**

From the PlanetScale organization dashboard, select the desired database

**Step 2**

Navigate to the **Backups** page from the menu on the left

**Step 3**

Select the backup you wish to restore from

**Step 4**

Click **Restore to new branch**

**Step 5**

Configure the restored branch:

* **Branch name**: Name for the new branch
* **Cluster size**: Choose the desired size of the new cluster

**Step 6**

Click **Restore backup**

A new branch will be created based on this backup and will become visible under the **Branches** page.

## Changing regions

A branch stays in the region you select at creation, so switching regions means migrating to a new branch. The process is straightforward — create a branch in the target region, bring your schema and data over, and promote it when ready.

**Step 1**

Create a new empty branch from the **Branches** page and select the desired region.

**Step 2**

Apply your schema to the new branch using standard PostgreSQL DDL.

**Step 3**

Copy data from the existing branch using a tool such as `pg_dump`/`pg_restore` or [pgcopydb](imports/postgres-migrate-pgcopydb.md).

**Step 4**

[Upsize the cluster](cluster-configuration.md#adjusting-cluster-size) and [add replicas](cluster-configuration.md#managing-replicas) to make it a production branch.

**Step 5**

[Set it as the default branch](#set-as-default-branch) if needed, and update your application connection strings to point at the new branch.

**Keep in mind:**

* There is no data replication between branches, so schema and data need to be copied manually as described above.
* Branches created [from a backup](#from-a-backup) inherit the region of the source branch, so backups alone won't get you into a new region — use the migration steps instead.
* To run production in a new region, you'll need to promote the new branch to default. See [Set as default branch](#set-as-default-branch).

## View all branches

To view all branches for your PostgreSQL database:

**Step 1**

From the PlanetScale organization dashboard, select the desired database

**Step 2**

Click on the **Branches** drop down

**Step 3**

You'll see a list of all branches with their type (development/production)

**Step 4**

You can select a branch by its name to see its **Dashboard**

Similarly you can navigate directly to the Branches page to see all branches:

**Step 1**

From the PlanetScale organization dashboard, select the desired database

**Step 2**

Navigate to the **Branches** page from the menu on the left

## Connect to a branch

Each branch has its own connection details. To connect to a specific branch:

**Step 1**

Navigate to the desired branch following either of the paths shown above

**Step 2**

Click the **Connect** button

**Step 3**

If you haven't already, follow the instructions to **Create the default role**

**Step 4**

Choose your preferred connection method ([direct or PgBouncer](connecting.md))

**Step 5**

Copy the connection string or credentials

**Step 6**

You should create new roles for specific use-cases and tailor their permissions appropriately for them

**Step 7**

In your application, use the specific role(s) and the preferred connection method.

For detailed connection instructions, see the [PostgreSQL connection documentation](connecting.md).

## Rename a branch

To rename a branch:

**Step 1**

From the PlanetScale organization dashboard, select the desired database

**Step 2**

Navigate to the **Branches** page from the menu on the left

**Step 3**

Click on the three dots ("...") next to the branch you want to rename

**Step 4**

Select **Rename branch**

**Step 5**

Enter the new branch name

**Step 6**

Click **Save changes**

Renaming a branch does not affect that branch's credentials. You do not need to regenerate credentials if you rename a branch.

## Set as default branch

The default branch serves as the source branch when creating new development branches.

Only production branches can be the default branch. New branches created from the **Branches** page start as development branches, so before a branch can be promoted to the default, you must first [upsize the cluster](cluster-configuration.md#adjusting-cluster-size) and [add replicas](cluster-configuration.md#managing-replicas) from the **Clusters** page to turn it into a production branch.

To change the default branch:

### From the Branches page

**Step 1**

From the PlanetScale organization dashboard, select the desired database

**Step 2**

Go to the **Branches** tab in your database dashboard

**Step 3**

Click on the three dots ("...") next to the branch you want to set as default

**Step 4**

Click **Set as default branch**

### From the Settings page

**Step 1**

From the PlanetScale organization dashboard, select the desired database

**Step 2**

Navigate to the **Settings** page from the menu on the left

**Step 3**

Select the branch from the **Default branch** dropdown you want to be the default branch

**Step 4**

Scroll down and click **Save database settings**

If you change the default branch and intend for it to power your production application, you may need to update your application credentials to reference the new default branch.

## Delete a branch

You can delete a branch that you no longer need:

**Step 1**

From the PlanetScale organization dashboard, select the desired database

**Step 2**

Go to the **Branches** tab in your database dashboard

**Step 3**

Click on the three dots ("...") next to the branch you want to delete

**Step 4**

Select **Delete**

**Step 5**

Confirm the deletion

**Important notes:**

* You cannot delete the default branch. You must first set another branch as the default branch.
* You cannot set development branches as the default branch.
* Only [Organization Administrators and Database Administrators](../security/access-control.md) have permission to delete production branches. Database Members can delete non-production branches.
* Development branches are billed only for the time they are used, so it's beneficial to delete them when no longer needed.

## Schema changes

Since PlanetScale Postgres branches don't use [deploy requests like in Vitess](../vitess/schema-changes/deploy-requests.md), you make schema changes directly on each branch:

**Step 1**

Connect to your development branch

**Step 2**

Make your schema changes using standard PostgreSQL DDL commands

**Step 3**

Test your changes in the development environment

**Step 4**

When ready, manually apply the same changes to your production branch

There's currently no automated way to merge schema changes between PlanetScale Postgres branches. You must manually copy your changes from development to production branches.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
