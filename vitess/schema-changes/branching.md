---
url: https://planetscale.com/docs/vitess/schema-changes/branching
title: "Branching"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Branching

> PlanetScale allows you to branch database schemas the same way you branch your code.

This page covers Vitess.

Postgres version: [Postgres](https://planetscale.com/docs/postgres/branching.md)

## What are branches on PlanetScale

Database branches on PlanetScale are isolated database instances that give you flexibility when developing your application. When your database is first initialized, a single production branch is created called `main` and acts as the default branch. When you create additional branches, the schema of the source branch is copied to the new branch, giving you an isolated MySQL instance to develop with. Changes made in one branch, whether to the schema or the data, do not affect any other branches for a given database.

[Data drop: Branching and deploy requests](https://www.youtube-nocookie.com/embed/EbG6-GUUIfc?rel=0)

## Development and production branches

PlanetScale provides two types of database branches:

* **Development branches** — Development branches provide isolated copies of your production database schema where you can make changes, experiment, or run CI. Please note that only the schema is copied. A new development branch will not have any data stored in it unless you [restore from a backup](https://planetscale.com/docs/vitess/backups.md#restore-from-a-backup). To automatically create a development branch with data from another branch, see the [Data Branching® feature](https://planetscale.com/docs/vitess/schema-changes/data-branching.md).
* **Production branches** — Production branches are highly available databases intended for production traffic. They are automatically provided with an additional [replica](https://planetscale.com/docs/vitess/scaling/replicas.md) to resist outages, enabling zero-downtime failovers. The Base plan includes two additional replicas, and Enterprise plans are customizable.

Both types of branches also offer [safe migrations](https://planetscale.com/docs/vitess/schema-changes/safe-migrations.md) as an optional feature, which helps protect the branch from accidental schema changes and enables non-blocking schema migrations. We highly recommend that all production database branches have the safe migrations setting turned on.

If you want to set up a workflow with a ”staging” branch, see more the [staging branch documentation](https://planetscale.com/docs/vitess/schema-changes/safe-migrations.md#staging-branches) below.

## Promote a branch to production

PlanetScale provides the ability to **promote any development branch with a valid schema to production**. Promoting a branch to production will automatically set up the database replica(s) to make your database highly available. Note: Only one production branch is included in the Base plan, but you can add more production branches for an additional fee.

Only administrators can promote database branches.

### Promote a development branch to production

Every new PlanetScale database is created with a production branch named `main`. This branch is intended as the starting point for building your database on PlanetScale and has increased performance and resilience by default when compared to development branches.

That said, you don't have to use the default `main` branch as your production branch. **Any development branch can be promoted to production**.

Once you are satisfied with the changes you've made to a given development branch, you can promote it to production. Going forward, you can continue to make new development branches off of this production branch to experiment with changes as needed.

A branch can be promoted from the branch overview page in the PlanetScale app or by using the [PlanetScale CLI](https://planetscale.com/docs/cli/branch.md), as shown below:

```
pscale branch promote <DATABASE_NAME> <BRANCH_NAME>
```

It's possible to run multiple production branches simultaneously, within [your plan's limits](https://planetscale.com/docs/planetscale-plans.md). Keep in mind, PlanetScale does not provide data syncing between production branches.

### Demote a production branch to development

Sometimes, you might need to demote a production branch to a development branch. This can be done from the production branch's overview page in the PlanetScale app (located in the right column of the page).

Be aware when demoting a production branch to a development branch:

* Databases must have at least one production branch at all times
* Development branches are not meant to be used with production workloads
* The branch will no longer have high-availability features
* Existing scheduled production branch backup policies will no longer run
* Any read-only regions will need to be removed

If you are on an Enterprise plan, only an administrator for your organization can request to demote a branch, and the demotion request will need to be approved by another administrator. Once the first administrator requests to demote a production branch, the second administrator can approve the demotion on the production branch's overview page. You will see the request from the first administrator and a **Demote to development branch** button to complete the demotion.

## Create a development branch

If you need to experiment with schema changes, you can create a new development branch. This will copy the schema from the base branch into a new branch where you can create and test your changes.

Development branches **will not** copy over data, just the schema. To create a development branch with data from another branch, see the [Data Branching® feature](https://planetscale.com/docs/vitess/schema-changes/data-branching.md) section.

**How to create a development branch**:

**Step 1**

On the database dashboard page, click the "**New branch**" button.

**Step 2**

Give your development branch a name and select the region closest to your or your application.

**Step 3**

Select the base branch you want to branch off of.

**Step 4**

Click "**Create branch**".

**Step 5**

(*Optional*) You can also create a new branch from the [PlanetScale CLI](https://planetscale.com/docs/cli/branch.md) with:

```
pscale branch create <DATABASE_NAME> <BRANCH_NAME>
```

## Safe migrations

[Safe migrations](https://planetscale.com/docs/vitess/schema-changes/safe-migrations.md) is an optional, but recommended, feature that can be enabled on branches. Branches with safe migrations enabled are restricted from accepting DDL directly. This prevents accidental changes to the database schema, and also enables non-blocking schema migrations. To make changes to branches with safe migrations enabled, you must create a new branch, then merge changes using a [deploy request](https://planetscale.com/docs/vitess/schema-changes/deploy-requests.md). Using this method, you get to see a schema diff before merging changes, have the option to have your team review changes, and receive [additional checks and warnings](https://planetscale.com/blog/deploy-requests-now-alert-on-potential-unwanted-changes) prior to making a production schema change.

To enable safe migrations on a branch, select the branch you want to modify from the branch dropdown and click the **”cog”** in the upper right of the infrastructure card on the ”**Dashboard**” tab of the database. In the modal, toggle the option labeled **”Enable safe migrations”**, then click the **”Enable safe migrations”** button to save and close the modal.

### Staging branches

You can use a development branch with safe migrations enabled to set up a workflow with a “staging” branch. First, make sure you have safe migration enabled for your main production branch. Then, create a “staging” branch with your main production branch as the base and turn on safe migrations. All new branches created for development can use this “staging” branch as the base branch.

You can then open a deploy request against either the main production or “staging” branch. Once it is deployed to “staging,” you can open a deploy request against the main production branch. The main production branch must be set as the [default branch](https://planetscale.com/docs/vitess/schema-changes/branching.md#default-branches) to open a deploy request against it.

> [!NOTE]
>
> In this setup, the “staging” branch is still a development branch. Compared to your main production branch (additional production branches are an additional cost), it will have reduced resources, similar to other development branches.

![View of the Branches tab with main \< staging \< dev branches](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/assets/docs/concepts/branching/branches-with-staging-branch.jpg?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=7f26070434f6e5fe07dd777c206a52c0)

## How to make schema changes on a branch with safe migrations enabled

Since DDL is restricted on branches with safe migrations enabled to prevent accidental changes and enable zero-downtime migrations, you'll need to perform the following steps in order to make changes to a safe migrations enabled branch:

![PlanetScale Branching Flow Diagram](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/assets/docs/concepts/branching/diagram.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=70edd8ce6763ac4a1a0c70528a5aae19)

> [!TIP]
>
> You'll see a `ERROR 1105 (HY000): direct DDL is disabled` message if you attempt to make schema changes in a branch with safe migrations enabled. Instead, create a development branch, and make your changes there.

### 1. Create a development branch

The first step is to create a new development branch off of the branch you want to make changes to. This will make a copy of the source branches schema into the newly created development branch where you can perform the necessary changes to the schema.

### 2. Create a deploy request

If you are working in a team, the [deploy request](https://planetscale.com/docs/vitess/schema-changes/deploy-requests.md) creates an opportunity for your teammates to review the changes you have made before they are deployed to production.

**Step 1**

To create the deploy request, go to the branch overview page for the branch you want to deploy.

**Step 2**

Select the base branch you want to deploy to from the "**Deploy to**" dropdown.

**Step 3**

(*Optional*) Add a comment describing your deploy request. . Click "**Create deploy request**".

![PlanetScale deploy request example](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/assets/docs/concepts/branching/deploy-request-page.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=abd052cb235c898d9ffb1f0690283bee)

Alternatively, you can create a deploy request from the [PlanetScale CLI](https://planetscale.com/docs/cli/deploy-request.md) with the following command:

```
pscale deploy-request create <DATABASE_NAME> <BRANCH_NAME>
```

### 3. Review the deploy request

The deploy request includes a schema diff so that you can review the schema changes introduced by the deploy request against the base branch.

**Step 1**

On the deploy request page, click the "**Schema changes**" tab.

**Step 2**

Schema additions are highlighted in green, and deletions are highlighted in red.

Alternatively, you can run the command below to see the [schema diff in the PlanetScale CLI](https://planetscale.com/docs/cli/deploy-request.md):

```
pscale deploy-request diff <DATABASE> <DEPLOY_REQUEST_NUMBER>
```

Another benefit of the deploy request review process is that **PlanetScale will determine if certain requests aren't deployable**. For example, if you try to deploy a branch with no unique key, PlanetScale will block the deployment, as the unique key is required.

### 4. Add changes to the deploy queue

Once a deploy request has been created and, optionally, approved, you need to add the changes to the deploy queue.

Schema changes are deployed to a database in the order in which they are received. PlanetScale analyzes the schema changes in a deploy request when they are added to the deploy queue to ensure that the changes do not conflict with any of the queued schema changes.

PlanetScale also provides insight on the deploy queue, listing all of the schema changes in the queue with their completion status.

**Step 1**

Organizations have the option to require approval before a deploy request can be added to the queue. If this is enabled, first make sure the request is approved. You can control this option in the database's "**Settings**" tab.

**Step 2**

To add a deploy request to the deploy queue, click “**Add changes to the deploy queue**” on the deploy request page.

**Step 3**

If successful, you'll get the message "These changes have been deployed".

**Step 4**

Your deploy request has now been merged into production. You can click on the "Deploy requests" tab of the database to see the list of previous deploys.

Alternatively, you can run the following command with the [PlanetScale CLI](https://planetscale.com/docs/cli/deploy-request.md):

```
pscale deploy-request deploy <DATABASE_NAME> <DEPLOY_REQUEST_NUMBER>
```

## Default branches

The `main` branch is automatically set as the default branch when the database is initialized. However, you can change the default branch if needed.

**How to change the default branch**:

There are two ways to change the default branch.

**Step 1**

Go to the "Branches" page in your dashboard.

**Step 2**

Click on the three dots "..." next to the branch whose name you'd like to change.

**Step 3**

Click "Set as default branch".

If the current default branch has child branches, you also have the option to move those under the new default branch from here by checking the "Replace the current default branch" checkbox.

Alternatively, you can do this from your Settings page.

**Step 1**

Go to your database dashboard page and click the "**Settings**" tab.

**Step 2**

Under "**General**" in the sidebar, you'll find the "**Default branch**" dropdown.

**Step 3**

Select the branch you want to be the default branch. It does not have to be a production branch.

**Step 4**

Scroll down and click "**Save database settings**".

> [!NOTE]
>
> If you change the default branch, you will also need to update your credentials in your application, or wherever else you use credentials, to reference the new default branch (if desired).

## Renaming a branch

You can change the name of a branch from the Branches tab in your PlanetScale dashboard. Click on the three dots ("...") next to the branch name that you would like to change, type in the new name, and click "Save changes".

Renaming a branch does not affect that branch's credentials. You do not need to regenerate credentials if you rename a branch.

## Delete a branch

You can delete a branch from the Branches overview page or by running the following command in the [PlanetScale CLI](https://planetscale.com/docs/cli/branch.md):

```
pscale branch delete <DATABASE_NAME> <BRANCH_NAME>
```

We recommend deleting branches after a deploy request is complete or if you are no longer using the branch for testing. Base plan development branches are billed only for the time that they are used, prorated to the millisecond, so it is beneficial to delete them when they are no longer in use. See the [billing documentation](https://planetscale.com/docs/planetscale-plans.md#development-branches) for more info.

> [!NOTE]
>
> Only [Organization Administrators](https://planetscale.com/docs/security/access-control.md#organization-administrator) have permission to delete production branches.

You cannot delete a branch that's [set as default](#default-branches). To delete, it set another branch as the default first.

## Resolve a schema conflict

Schema conflicts occur when your branch has conflicting changes with the base branch.

To resolve a schema conflict, create a new branch from the base branch, which will have the most up-to-date schema, and apply the necessary schema changes to the new branch before repeating the deploy process.

## Automatically copy migration data

Many frameworks and migration tools keep track of data schema changes in a migration table. When turned on, PlanetScale will automatically copy migration table metadata from your development branches to the production branch as part of our deployment process.

**Turn on automatic copying of migration data**:

**Step 1**

On your database dashboard page, click the "**Settings**" tab.

**Step 2**

Check the "**Automatically copy migration data**" box.

**Step 3**

Select one of the listed frameworks: Rails, Phoenix, Laravel, Django, .NET, Sequelize, or Other, which allows you to specify a custom table name.

You can see how this works in this [Rails migration tutorial](https://planetscale.com/docs/vitess/tutorials/automatic-rails-migrations.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
