---
url: https://planetscale.com/docs/vitess/schema-changes/branching
title: "Branching"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## What are branches on PlanetScale

Database branches on PlanetScale are isolated database instances that give you flexibility when developing your application. When your database is first initialized, a single production branch is created called `main` and acts as the default branch. When you create additional branches, the schema of the source branch is copied to the new branch, giving you an isolated MySQL instance to develop with. Changes made in one branch, whether to the schema or the data, do not affect any other branches for a given database.

## Development and production branches

PlanetScale provides two types of database branches:

- **Development branches** — Development branches provide isolated copies of your production database schema where you can make changes, experiment, or run CI. Please note that only the schema is copied. A new development branch will not have any data stored in it unless you [restore from a backup](../backups.md#restore-from-a-backup). To automatically create a development branch with data from another branch, see the [Data Branching® feature](data-branching.md).
- **Production branches** — Production branches are highly available databases intended for production traffic. They are automatically provided with an additional [replica](../scaling/replicas.md) to resist outages, enabling zero-downtime failovers. The Base plan includes two additional replicas, and Enterprise plans are customizable.

Both types of branches also offer [safe migrations](safe-migrations.md) as an optional feature, which helps protect the branch from accidental schema changes and enables non-blocking schema migrations. We highly recommend that all production database branches have the safe migrations setting turned on.

If you want to set up a workflow with a ”staging” branch, see more the [staging branch documentation](safe-migrations.md#staging-branches) below.

## Promote a branch to production

PlanetScale provides the ability to **promote any development branch with a valid schema to production**. Promoting a branch to production will automatically set up the database replica(s) to make your database highly available. Note: Only one production branch is included in the Base plan, but you can add more production branches for an additional fee.

Only administrators can promote database branches.

### Promote a development branch to production

Every new PlanetScale database is created with a production branch named `main`. This branch is intended as the starting point for building your database on PlanetScale and has increased performance and resilience by default when compared to development branches.

That said, you don’t have to use the default `main` branch as your production branch. **Any development branch can be promoted to production**.

Once you are satisfied with the changes you’ve made to a given development branch, you can promote it to production. Going forward, you can continue to make new development branches off of this production branch to experiment with changes as needed.

A branch can be promoted from the branch overview page in the PlanetScale app or by using the [PlanetScale CLI](../../cli/branch.md), as shown below:

```text
pscale branch promote <DATABASE_NAME> <BRANCH_NAME>
```

It’s possible to run multiple production branches simultaneously, within [your plan’s limits](../../planetscale-plans.md). Keep in mind, PlanetScale does not provide data syncing between production branches.

### Demote a production branch to development

Sometimes, you might need to demote a production branch to a development branch. This can be done from the production branch’s overview page in the PlanetScale app (located in the right column of the page).

Be aware when demoting a production branch to a development branch:

- Databases must have at least one production branch at all times
- Development branches are not meant to be used with production workloads
- The branch will no longer have high-availability features
- Existing scheduled production branch backup policies will no longer run
- Any read-only regions will need to be removed

If you are on an Enterprise plan, only an administrator for your organization can request to demote a branch, and the demotion request will need to be approved by another administrator. Once the first administrator requests to demote a production branch, the second administrator can approve the demotion on the production branch’s overview page. You will see the request from the first administrator and a **Demote to development branch** button to complete the demotion.

## Create a development branch

If you need to experiment with schema changes, you can create a new development branch. This will copy the schema from the base branch into a new branch where you can create and test your changes.

Development branches **will not** copy over data, just the schema. To create a development branch with data from another branch, see the [Data Branching® feature](data-branching.md) section.

**How to create a development branch**:

## Safe migrations

[Safe migrations](safe-migrations.md) is an optional, but recommended, feature that can be enabled on branches. Branches with safe migrations enabled are restricted from accepting DDL directly. This prevents accidental changes to the database schema, and also enables non-blocking schema migrations. To make changes to branches with safe migrations enabled, you must create a new branch, then merge changes using a [deploy request](deploy-requests.md). Using this method, you get to see a schema diff before merging changes, have the option to have your team review changes, and receive [additional checks and warnings](https://planetscale.com/blog/deploy-requests-now-alert-on-potential-unwanted-changes) prior to making a production schema change.

To enable safe migrations on a branch, select the branch you want to modify from the branch dropdown and click the **”cog”** in the upper right of the infrastructure card on the ” **Dashboard** ” tab of the database. In the modal, toggle the option labeled **”Enable safe migrations”**, then click the **”Enable safe migrations”** button to save and close the modal.

### Staging branches

You can use a development branch with safe migrations enabled to set up a workflow with a “staging” branch. First, make sure you have safe migration enabled for your main production branch. Then, create a “staging” branch with your main production branch as the base and turn on safe migrations. All new branches created for development can use this “staging” branch as the base branch.

You can then open a deploy request against either the main production or “staging” branch. Once it is deployed to “staging,” you can open a deploy request against the main production branch. The main production branch must be set as the [default branch](branching.md#default-branches) to open a deploy request against it.

In this setup, the “staging” branch is still a development branch. Compared to your main production branch (additional production branches are an additional cost), it will have reduced resources, similar to other development branches.

![View of the Branches tab with main < staging < dev branches](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/assets/docs/concepts/branching/branches-with-staging-branch.jpg?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=d172be7481aaa84442b1c99bc0f1ad41)

View of the Branches tab with main < staging < dev branches

## How to make schema changes on a branch with safe migrations enabled

Since DDL is restricted on branches with safe migrations enabled to prevent accidental changes and enable zero-downtime migrations, you’ll need to perform the following steps in order to make changes to a safe migrations enabled branch:

![PlanetScale Branching Flow Diagram](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/assets/docs/concepts/branching/diagram.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=8ed3037acbc0ce1cdedfb94c525107c2)

PlanetScale Branching Flow Diagram

You’ll see a `ERROR 1105 (HY000): direct DDL is disabled` message if you attempt to make schema changes in a branch with safe migrations enabled. Instead, create a development branch, and make your changes there.

### 1\. Create a development branch

The first step is to create a new development branch off of the branch you want to make changes to. This will make a copy of the source branches schema into the newly created development branch where you can perform the necessary changes to the schema.

### 2\. Create a deploy request

If you are working in a team, the [deploy request](deploy-requests.md) creates an opportunity for your teammates to review the changes you have made before they are deployed to production.

Alternatively, you can create a deploy request from the [PlanetScale CLI](../../cli/deploy-request.md) with the following command:

```text
pscale deploy-request create <DATABASE_NAME> <BRANCH_NAME>
```

### 3\. Review the deploy request

The deploy request includes a schema diff so that you can review the schema changes introduced by the deploy request against the base branch.

Alternatively, you can run the command below to see the [schema diff in the PlanetScale CLI](../../cli/deploy-request.md):

```text
pscale deploy-request diff <DATABASE> <DEPLOY_REQUEST_NUMBER>
```

Another benefit of the deploy request review process is that **PlanetScale will determine if certain requests aren’t deployable**. For example, if you try to deploy a branch with no unique key, PlanetScale will block the deployment, as the unique key is required.

### 4\. Add changes to the deploy queue

Once a deploy request has been created and, optionally, approved, you need to add the changes to the deploy queue.

Schema changes are deployed to a database in the order in which they are received. PlanetScale analyzes the schema changes in a deploy request when they are added to the deploy queue to ensure that the changes do not conflict with any of the queued schema changes.

PlanetScale also provides insight on the deploy queue, listing all of the schema changes in the queue with their completion status.

Alternatively, you can run the following command with the [PlanetScale CLI](../../cli/deploy-request.md):

```text
pscale deploy-request deploy <DATABASE_NAME> <DEPLOY_REQUEST_NUMBER>
```

## Default branches

The `main` branch is automatically set as the default branch when the database is initialized. However, you can change the default branch if needed.

**How to change the default branch**:

There are two ways to change the default branch.

If the current default branch has child branches, you also have the option to move those under the new default branch from here by checking the “Replace the current default branch” checkbox.

Alternatively, you can do this from your Settings page.

If you change the default branch, you will also need to update your credentials in your application, or wherever else you use credentials, to reference the new default branch (if desired).

## Renaming a branch

You can change the name of a branch from the Branches tab in your PlanetScale dashboard. Click on the three dots (”…”) next to the branch name that you would like to change, type in the new name, and click “Save changes”.

Renaming a branch does not affect that branch’s credentials. You do not need to regenerate credentials if you rename a branch.

## Delete a branch

You can delete a branch from the Branches overview page or by running the following command in the [PlanetScale CLI](../../cli/branch.md):

```text
pscale branch delete <DATABASE_NAME> <BRANCH_NAME>
```

We recommend deleting branches after a deploy request is complete or if you are no longer using the branch for testing. Base plan development branches are billed only for the time that they are used, prorated to the millisecond, so it is beneficial to delete them when they are no longer in use. See the [billing documentation](../../planetscale-plans.md#development-branches) for more info.

Only [Organization Administrators](../../security/access-control.md#organization-administrator) have permission to delete production branches.

You cannot delete a branch that’s [set as default](#default-branches). To delete, it set another branch as the default first.

## Resolve a schema conflict

Schema conflicts occur when your branch has conflicting changes with the base branch.

To resolve a schema conflict, create a new branch from the base branch, which will have the most up-to-date schema, and apply the necessary schema changes to the new branch before repeating the deploy process.

## Automatically copy migration data

Many frameworks and migration tools keep track of data schema changes in a migration table. When turned on, PlanetScale will automatically copy migration table metadata from your development branches to the production branch as part of our deployment process.

**Turn on automatic copying of migration data**:

You can see how this works in this [Rails migration tutorial](../tutorials/automatic-rails-migrations.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
