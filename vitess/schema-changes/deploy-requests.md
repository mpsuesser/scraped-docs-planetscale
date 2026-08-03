---
url: https://planetscale.com/docs/vitess/schema-changes/deploy-requests
title: "Deploy Requests"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

## Overview

Database branching, coupled with deploy requests, allows you to **deploy non-blocking schema changes to your production database with zero downtime**. You can also [undo deployments](#revert-a-schema-change) without losing any data that was written during that time.

## Create a deploy request

Before you can create a deploy request, the branch you are merging into must have [safe migrations](safe-migrations.md) enabled.

![Example of deploy request on branch page](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/deploy-requests/deploy-request-page-2.png?w=2500&fit=max&auto=format&n=GA0k5H-MolPvBjDk&q=85&s=ef2ac069a782ed847ee67de18da4ed25)

Example of deploy request on branch page

## Review a deploy request

Once you create a deploy request, you or your team can review it and, optionally, approve it before deploying it.

PlanetScale will check if the request is deployable. This process includes checking for issues like:

- [Incompatible unique keys](onlineddl-change-unique-keys.md)
- [Invalid charsets](schema-lint-errors/invalid-charset.md) (PlanetScale supports `utf8`, `utf8mb4`, `utf8mb3`, `latin1`, and `ascii`)
- [Invalid foreign key constraints](schema-lint-errors/foreign-keys-disallowed.md)
- [Other schema lint errors](schema-lint-errors.md) that would prevent a successful schema change

We will also warn you about potential data loss or inconsistencies and check if there are any known conflicts with the production schema that could prevent a clean merge. While we attempt to find all possible conflicts, it is ultimately up to you to confirm merge details.

If you are the only administrator in your Organization and you enable the “Require administrator approval for deploy requests” setting, you can self-approve your own deploy requests. If there is more than one administrator, self-approval is not allowed.

### Approvals dismissed on schema changes

To ensure only approved schema changes are applied to production, an approval is automatically dismissed if the proposed changes are updated after the deploy request is approved. When this occurs, `planetscale-bot` comments on the deploy request with the list of changes since the last approval.

If the deploy request is already in the deploy queue when it’s schema changes, it will be removed from the queue. It must be re-approved and added to the queue again before it can deploy.

### Reviewing changes across shards

If your deploy request contains changes to a sharded keyspace, you can see the affected shards by clicking the arrow next to each changed table. This will show the SQL that will run, and in the next tab, each shard that will be affected.

![PlanetScale deploy request - changes on sharded keyspace](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/deploy-requests/sharded-deploy-request.png?w=2500&fit=max&auto=format&n=GA0k5H-MolPvBjDk&q=85&s=d7808adb5c714da06cf9bc7d0738989d)

PlanetScale deploy request - changes on sharded keyspace

## Deploy a deploy request

### Deploy changes

### Deploy changes instantly

You also have the option to use MySQL’s **ALGORITHM=INSTANT** to instantly deploy the schema change. Learn more in the [**Instant Deployments** section](#instant-deployments).

1. When you’re ready to deploy, click “ **Deploy changes instantly** ”. The deployment will begin or be queued if there are other pending deployments.
	- Though the deployment many be queued, once it’s at the front of the queue, it will be deployed instantly.
		- Instant deployments **cannot be reverted**.

If you would like to require an administrator’s approval before a request can be deployed, go to the “ **Settings** ” page for your database and check the “ **Require administrator approval for deploy requests** ” box. You must be an Organization Administrator to enable this restriction. Please note you will not be able to approve your own deploy requests.

## Close a deploy request

If you decide you don’t want to proceed with a deploy request, you can easily close it.

## Deploy requests and foreign key constraints

In most cases, deploy requests should work as expected when your schema changes have [foreign key constraints](../foreign-key-constraints.md).

A deploy request that modifies a table with many foreign key child tables may cause queries to block when the InnoDB adaptive hash index (AHI) is enabled. See [Adaptive hash index](../foreign-key-constraints.md#adaptive-hash-index).

There are some cases where a deploy request will not be deployable. This includes cases where there is a mismatched column type or when a foreign key constraint references a deleted column.

For example, if we open a deploy request to add a foreign key constraint `t1_id` with type `BIGINT` on a table `t2` that references a column `id` on table `t1`, where `t1.id` ’s type is `BIGINT`, the following cases would produce a linting error in the deploy request because it is not deployable:

- if, while the previously mentioned deploy request is open, someone else updates `t1.id` to a different column type, i.e., `int`.
- if, while the previously mentioned deploy request is open, someone else deletes `t1.id`.
- if, while the previously mentioned deploy request is open, someone else deletes all indexes that cover `t1.id` as their prefix. (Because in a foreign key relationship, the referenced columns on the parent table must be indexed, usually by a dedicated index, but they can be the first columns in an otherwise wider index.)

These are all cases where another user changes schema, causing the initial user’s definition to be invalid MySQL.

There are also two cases where a revert would cause orphaned rows that you can read about in this document’s [revert section](#when-a-revert-can-result-in-orphaned-rows).

### Validating referential integrity of existing columns

Deploy requests do not validate the referential integrity of *existing* columns. `ALTER TABLE… ADD FOREIGN KEY…` does not validate existing row relations within the context of a deploy request. Unlike standard MySQL, it is possible to add the foreign key constraint to a table with orphaned rows, and they will remain orphaned. In standard MySQL, adding a foreign key is a blocking operation, and it fails if any orphaned rows are found.

## Instant deployments

Instant deployments give you the option to run schema changes using MySQL’s **ALGORITHM=INSTANT**. This is different than how our [**online schema migrations**](how-online-schema-change-tools-work.md) work.

Instant deployments will apply schema changes faster, however, these schema changes must be **auto-applied** and **cannot be reverted**.

### Who should use instant deployments?

Instant deployments are well-suited to experienced users who want their schema change to take effect immediately rather than going through the online schema migration process.

Although instant deployments complete near-instantaneously in most cases, they require briefly acquiring an exclusive metadata lock on the affected tables. To prevent the deployment from being blocked, instant deployments will immediately terminate any queries and transactions holding locks on the target table when they run.

On a busy table this can mean a burst of failed application queries at the moment of deployment. Ensure your application has retry logic for terminated queries.

In rare cases, if something prevents the instant deployment from acquiring or releasing the metadata lock, application writes to the table will be blocked until the lock is released.

For details on which operations support `ALGORITHM=INSTANT` and their limitations, see the [MySQL InnoDB online DDL operations documentation](https://dev.mysql.com/doc/refman/en/innodb-online-ddl-operations.html).

### Supported operations

In order for a deploy request to be instantly deployed, *all* schema changes in the deploy request must be instantly deployable. Some of those changes include:

- Adding or dropping a column (with some exceptions)
- Changing or dropping a column’s default value
- Changing an `ENUM` or `SET` definition

The following changes are examples of changes that are **not** instantly deployable:

- Changing a column’s data type
- Adding a column with a non-literal default value
- Adding or dropping an index
- Altering the visibility of an index
- Adding or dropping a foreign key constraint
- Extending a `VARCHAR` column size
- Updating a column to `NULL` or `NOT NULL`

To know whether or not a deploy request is instantly deployable, look for the “Instantly deployable” badge on your deploy request. This badge will only be visible on deploy requests that can be deployed instantly.

![PlanetScale deploy request - deploy instantly badge](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/assets/docs/concepts/deploy-requests/deploy-instantly.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=783d1d4de4360405677e8f69f1d739d6)

PlanetScale deploy request - deploy instantly badge

We recommend reading [MySQL’s Online DDL documentation](https://dev.mysql.com/doc/refman/en/innodb-online-ddl-operations.html) for the full list of operations that can be deployed instantly.

## Gated deployments

Gated deployments give you more control over when a migration goes live after the deployment process completes.

As part of our non-blocking schema change process, instead of directly modifying table(s) when you deploy a deploy request, we make a copy of the affected table(s) and apply changes to the copy. We get the data from the original table and the copy table in sync, and once complete, initiate a quick cutover where we swap the tables.

If a deploy request includes changes to multiple tables, all tables cut over at the same time — unless there is a sequential dependency.

With gated deployments, you can initiate the deployment, but once the table syncing is complete, we’ll hold off on the cutover and let you click a button to swap the tables and complete the deployment. Gated deployments can be enabled on each deploy request by unchecking the “Auto-apply changes” box before you deploy.

This feature is helpful if you have long-running migrations. For very large or complex databases, deploying a schema change can take several hours to complete. In those scenarios, you don’t want the cutover to happen while you’re offline. With gated deployments, you can start the deployment process by adding your deploy request to the queue, and once it’s done, you’ll be able to click a button to merge it in and complete the deployment while you’re there to monitor it.

### Enable gated deployments in the dashboard

### Enable gated deployments via CLI

You can also manage auto-apply settings using the [PlanetScale CLI](../../cli/deploy-request.md).

To create a deploy request with auto-apply disabled:

```shellscript
pscale deploy-request create <DATABASE_NAME> <BRANCH_NAME> --disable-auto-apply
```

To disable auto-apply on an existing deploy request:

```shellscript
pscale deploy-request edit <DATABASE_NAME> <DR_NUMBER> --disable-auto-apply
```

Once your deploy request has completed and is ready for cutover, trigger the swap:

```shellscript
pscale deploy-request apply <DATABASE_NAME> <DR_NUMBER>
```

If neither `--enable-auto-apply` nor `--disable-auto-apply` is provided when creating a deploy request, the setting is inherited from the previous deploy request.

### Limitations

- If you have an open gated deployment, you cannot deploy another deploy request until the current one has been merged in.
- Deploy requests that are [instantly deployed](#instant-deployments) cannot be gated.

For more information about this process and why we built it, check out the [Gated Deployments: Addressing the complexity of schema deployments at scale](https://planetscale.com/blog/gated-deployments-addressing-the-complexity-of-schema-deployments-at-scale) blog post.

## Artifact tables

During schema changes, Vitess creates artifact tables to facilitate non-blocking migrations. For detailed information about artifact table behavior, storage implications, and cleanup processes, see the [Artifact tables in schema changes](https://planetscale.com/docs/vitess/schema-changes/artifact-tables) documentation.

## Revert a schema change

If you ever merge a deploy request, only to realize you need to undo it, PlanetScale can handle that! You have the option to revert a recently deployed schema change while maintaining data that was written to the original schema during that time.

Deploy requests that are instantly deployed *cannot* be reverted.

### How to revert a schema change

You can revert a deployment for **up to 30 minutes** after the deploying. After the 30 minute period is up, the deployment becomes permanent, and you will no longer have the option to revert.

### When is data not retained

There are some scenarios where some data is not retained when you revert your changes.

1. You add a table or column to your schema and then revert it. If any data was written to those newly introduced fields between deployment and reverting, that data will not be retained upon revert, as the fields will no longer exist.

### When a revert can result in orphaned rows

In some cases, when you are using foreign key constraints, a revert of a deploy request can result in orphaned rows. These can happen when your schema change is:

- Dropping a foreign key constraint: Once a foreign key constraint is dropped, new data written to the table is less constrained. Reverting this change may result in data that is inconsistent with the dropped foreign key constraint.
- Dropping a table with foreign key constraints: When a table with foreign key constraints is dropped, the parent table(s) will continue to be written to. If this change is reverted, data in the table that was dropped may no longer be consistent with its foreign key constraints.

You must enable [foreign key constraint](../foreign-key-constraints.md) support in the database settings page before using them.

### When are you unable to revert a schema change

There are also some edge cases where reverting a schema change is not possible. We will always attempt to revert, but if there are scenarios where your data integrity is at risk, we will not proceed with the revert. The following are some cases where a revert will fail:

1. If you deploy a schema change that expands the length of some column, such as changing from `VARCHAR(10)` to `VARCHAR(50)`, and add new data larger than 10 characters to it, a revert attempt may fail. This is to protect your data. You may have written data to the `VARCHAR(50)` field in that time that will not fit in the smaller 10 character space. If no data is added between deployment and revert, the revert process can proceed.
2. Some examples of other similar scenarios where revert won’t be possible (again, only if larger sized data is added between deployment and revert) are:
	- `INT` to `BIGINT`
		- `NOT NULL` to `NULL`
		- `TIMESTAMP` to `TIMESTAMP(6)`
		- `utf8` to `utf8mb4`
		- Any other operation that expands the size of a field
3. If you deploy a schema change that removes a unique key or relaxes a unique constraint, and in the time between deployment and attempting to revert, you insert rows that would otherwise conflict with that constraint, the revert may fail.
4. Another uncommon but possible scenario: you deploy a schema change that has a `NOT NULL` column without a `DEFAULT` value, combined with an `ALTER TABLE DROP COLUMN` statement for that column. If you insert some rows between the deployment and the revert attempt, the revert will fail. We will not be able to re-add that column for the newly inserted rows and will not know how to populate it.

For an in-depth look at how this process works, check out our [Behind the scenes: how schema reverts work](https://planetscale.com/blog/behind-the-scenes-how-schema-reverts-work) blog post.

### Schema revert and migration data

If you’ve selected a migration framework or specified a table with migration data in the settings tab of your database, the data within the table that tracks migrations will be moved to the production branch only after the revert window has been closed. This is to ensure that if the deploy request is reverted, the production branch has the correct log of applied migrations.

### Billing considerations

You may see some temporary `_vt` tables in your database. These are called [artifact tables](https://planetscale.com/docs/vitess/schema-changes/artifact-tables) and are used to facilitate the deployment and revert process. They do not count toward your storage costs if you’re using [network-attached storage](../../plans/planetscale-skus.md#network-attached-storage).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
