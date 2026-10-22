---
url: https://planetscale.com/docs/vitess/schema-changes/deploy-requests
title: "Deploy Requests"
description: ""
access_date: 2026-10-22T18:21:49.469Z
current_date: 2026-10-22T18:21:49.469Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Deploy requests

> Deploy requests are an integral part of the [PlanetScale workflow](../best-practices.md).

## Overview

Database branching, coupled with deploy requests, allows you to **deploy non-blocking schema changes to your production database with zero downtime**. You can also [undo deployments](#revert-a-schema-change) without losing any data that was written during that time.

[Deploy requests overview](https://player.vimeo.com/video/830571933?dnt=true)

## Create a deploy request

> [!NOTE]
>
> Before you can create a deploy request, the branch you are merging into must have [safe migrations](https://planetscale.com/docs/vitess/schema-changes/safe-migrations.md) enabled.

**Step 1**

Click on "**Branches**".

**Step 2**

Select the development branch you want to deploy into the base branch.

**Step 3**

This page shows you a diff of the schema against its base branch.

**Step 4**

To the right of the page, you'll see a dropdown that says "**Deploy to**". If you don't see this option, you likely don't have [safe migrations](https://planetscale.com/docs/vitess/schema-changes/safe-migrations.md) enabled on the branch you're deploying into. Go to the branch page for the branch you'd like to merge into, and enable safe migrations.

**Step 5**

Select the branch you want to deploy to. Again, if the branch doesn't show in the dropdown, you need to enable [safe migrations](https://planetscale.com/docs/vitess/schema-changes/safe-migrations.md).

**Step 6**

Optionally, add a comment about the deploy request.

**Step 7**

Click "**Create deploy request**".

![Example of deploy request on branch page](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/deploy-requests/deploy-request-page-2.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=470c972c0b7188a5b2f66ebfadbe02b0)

## Review a deploy request

Once you create a deploy request, you or your team can review it and, optionally, approve it before deploying it.

PlanetScale will check if the request is deployable. This process includes checking for issues like:

* [Incompatible unique keys](https://planetscale.com/docs/vitess/schema-changes/onlineddl-change-unique-keys.md)
* [Invalid charsets](https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors/invalid-charset.md) (PlanetScale supports `utf8`, `utf8mb4`, `utf8mb3`, `latin1`, and `ascii`)
* [Invalid foreign key constraints](https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors/foreign-keys-disallowed.md)
* [Other schema lint errors](https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors.md) that would prevent a successful schema change

We will also warn you about potential data loss or inconsistencies and check if there are any known conflicts with the production schema that could prevent a clean merge. While we attempt to find all possible conflicts, it is ultimately up to you to confirm merge details.

**Step 1**

Click the "**Deploy requests**" tab on the database dashboard page.

**Step 2**

Select the open deploy request you want to review.

**Step 3**

Under "**Summary**", you'll see if the request is deployable.

**Step 4**

To review the schema changes, click the "**Schema changes**" tab.

**Step 5**

You'll see the proposed changes here. New additions are highlighted in green, and deletions are highlighted in red.

**Step 6**

If you have required deploy requests to be approved before deployment, other users in your Organization will see the option to "**Approve changes**" or "**Leave a comment**" on the "**Schema changes**" tab.

> [!NOTE]
>
> If you are the only administrator in your Organization and you enable the "Require administrator approval for deploy requests" setting, you can self-approve your own deploy requests. If there is more than one administrator, self-approval is not allowed.

### Approvals dismissed on schema changes

To ensure only approved schema changes are applied to production, an approval is automatically dismissed if the proposed changes are updated after the deploy request is approved. When this occurs, `planetscale-bot` comments on the deploy request with the list of changes since the last approval.

If the deploy request is already in the deploy queue when the schema changes are updated, it will be removed from the queue. It must be re-approved and added to the queue again before it can deploy.

### Reviewing changes across shards

If your deploy request contains changes to a sharded keyspace, you can see the affected shards by clicking the arrow next to each changed table. This will show the SQL that will run, and in the next tab, each shard that will be affected.

![PlanetScale deploy request - changes on sharded keyspace](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/deploy-requests/sharded-deploy-request.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=a083c1db1d2cd4c5fd283eef19cb6782)

## Deploy a deploy request

By default, deploy requests on a branch run through a **serial deploy queue**: only one deploy runs at a time, and later requests wait until earlier ones finish. When a queue is already running, you can also [deploy in parallel](#parallel-deployments) alongside it.

A deploy that cannot lock the table is usually due to long-running transactions. See [Long-running transactions](https://planetscale.com/docs/vitess/schema-changes/long-running-transactions.md).

A copy can also fail because existing rows do not fit the new schema. See [Column cannot be null](https://planetscale.com/docs/vitess/schema-changes/column-cannot-be-null.md), [Column has no default value](https://planetscale.com/docs/vitess/schema-changes/column-has-no-default.md), [Duplicate entry](https://planetscale.com/docs/vitess/schema-changes/duplicate-entry.md), and [Value does not fit the new column](https://planetscale.com/docs/vitess/schema-changes/value-does-not-fit-column.md). If one migration in the deploy fails, later migrations stop until that one succeeds. See [Earlier migration failed](https://planetscale.com/docs/vitess/schema-changes/earlier-migration-failed.md).

A deploy can fail before copying starts when the schema it was computed against no longer matches production, or when MySQL rejects the new table definition. See [Constraint not found](https://planetscale.com/docs/vitess/schema-changes/constraint-not-found.md) and [Foreign key missing unique key](https://planetscale.com/docs/vitess/schema-changes/foreign-key-missing-unique-key.md).

If some tables fail while others are still copying or ready, the deploy stays in progress. See [Retry a partially failed deploy](#retry-a-partially-failed-deploy).

**Step 1**

Once the request is approved, if required, it's ready to deploy. Click on the "Summary" tab, and you'll see the option to deploy.

**Step 2**

Here you'll have the option to choose to "Deploy changes" or to "Deploy changes instantly". If [Prefer instant](#prefer-instant) is enabled and the request is instantly deployable, the button defaults to "Deploy changes instantly":

![PlanetScale deploy request - deploy options](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/deploy-requests/deploy.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=15afd661edfa9b23aa6ac7efc09fa32e)

### Deploy changes

**Step 1**

(Optional) You have the option to enable [**gated deployments**](#gated-deployments), which gives you the power to control exactly when the migration cuts over. You'll see an "**Auto-apply changes**" checkbox, which is checked by default. If you uncheck this, you will get the option to apply the changes once the schema changes are complete. If you leave it checked, it will auto-deploy as soon as it's ready.

**Step 2**

(Optional) You also have the option to run the migration using MySQL's **ALGORITHM=INSTANT**. Click the arrow next to "Deploy changes" and select "Deploy changes instantly". If [Prefer instant](#prefer-instant) is enabled, eligible deploy requests default to this option. Gated deployments are not available for this option. See the [Deploy changes instantly](#deploy-changes-instantly) section below for more information.

**Step 3**

When you're ready to deploy, click "**Deploy changes**". The deployment will begin immediately, or join the serial deploy queue if other deployments are already pending. If a queue is already running, you can instead choose [**Deploy in parallel**](#parallel-deployments) to run alongside it. You will still have a short window after deploying to enable [gated deployments](#gated-deployments) in this step.

**Step 4**

If you enabled gated deployments (step 2), you can click "**Apply changes**" to merge the deployment to production once it completes.

**Step 5**

After you deploy, you have **30 minutes to "undo"** it using our [schema revert feature](#revert-a-schema-change).

**Step 6**

If you are deploying changes to a sharded keyspace, you'll see the progress for each shard here as well.

### Deploy changes instantly

You also have the option to use MySQL's **ALGORITHM=INSTANT** to instantly deploy the schema change. Learn more in the [**Instant Deployments** section](#instant-deployments).

If [Prefer instant](#prefer-instant) is enabled, eligible deploy requests default the deploy button to this option.

1. When you're ready to deploy, click "**Deploy changes instantly**". The deployment will begin immediately, or join the serial deploy queue if other deployments are already pending.
   * If it joins the serial queue, it deploys instantly once it reaches the front of that queue.
   * You can also deploy instantly [in parallel](#parallel-deployments) alongside an existing queue.
   * Instant deployments **cannot be reverted**.

If you would like to require an administrator's approval before a request can be deployed, go to the "**Settings**" page for your database and check the "**Require administrator approval for deploy requests**" box. You must be an Organization Administrator to enable this restriction. Please note you will not be able to approve your own deploy requests.

## Retry a partially failed deploy

Online schema changes copy each affected table before cutover. If some tables fail while others are still copying or ready, the deploy stays in progress. The deploy page shows that it has partially failed and cannot continue.

You can retry the failed tables, or cancel the deploy request.

### Retry the failed tables

Retry recopies only the failed tables, in migration order. Tables that already finished stay as they are.

**Step 1**

Open the deploy request that has partially failed.

**Step 2**

Click "**Retry**".

**Step 3**

The button changes to "**Retry requested**". If a table fails again after that, "**Retry**" is available to try again.

From the [PlanetScale CLI](https://planetscale.com/docs/cli/deploy-request.md):

```bash theme={null}
pscale deploy-request retry <DATABASE_NAME> <DR_NUMBER>
```

### Cancel the deploy request

Cancel the deploy request on the deploy page. This discards every table in the deploy, including copies that already finished.

From the CLI:

```bash theme={null}
pscale deploy-request cancel <DATABASE_NAME> <DR_NUMBER>
```

## Close a deploy request

If you decide you don't want to proceed with a deploy request, you can easily close it.

**Step 1**

Click the "**Deploy requests**" tab on the database dashboard page.

**Step 2**

Select the request you want to close.

**Step 3**

Click on the "**Close deploy request**" button on the right-hand side.

## Deploy requests and foreign key constraints

In most cases, deploy requests should work as expected when your schema changes have [foreign key constraints](https://planetscale.com/docs/vitess/foreign-key-constraints.md).

> [!WARNING]
>
> A deploy request that modifies a table with many foreign key child tables may cause queries to block when the InnoDB adaptive hash index (AHI) is enabled. See [Adaptive hash index](https://planetscale.com/docs/vitess/foreign-key-constraints.md#adaptive-hash-index).

There are some cases where a deploy request will not be deployable.
This includes cases where there is a mismatched column type or when a foreign key constraint references a deleted column.

For example, if we open a deploy request to add a foreign key constraint `t1_id` with type `BIGINT` on a table `t2` that references a column `id` on table `t1`, where `t1.id`'s type is `BIGINT`, the following cases would produce a linting error in the deploy request because it is not deployable:

* if, while the previously mentioned deploy request is open, someone else updates `t1.id` to a different column type, i.e., `int`.
* if, while the previously mentioned deploy request is open, someone else deletes `t1.id`.
* if, while the previously mentioned deploy request is open, someone else deletes all indexes that cover `t1.id` as their prefix. (Because in a foreign key relationship, the referenced columns on the parent table must be indexed, usually by a dedicated index, but they can be the first columns in an otherwise wider index.)

These are all cases where another user changes schema, causing the initial user's definition to be invalid MySQL.

There are also two cases where a revert would cause orphaned rows that you can read about in this document's [revert section](#when-a-revert-can-result-in-orphaned-rows).

### Validating referential integrity of existing columns

Deploy requests do not validate the referential integrity of *existing* columns. `ALTER TABLE… ADD FOREIGN KEY…` does not validate existing row relations within the context of a deploy request. Unlike standard MySQL, it is possible to add the foreign key constraint to a table with orphaned rows, and they will remain orphaned. In standard MySQL, adding a foreign key is a blocking operation, and it fails if any orphaned rows are found.

## Instant deployments

Instant deployments give you the option to run schema changes using MySQL's **ALGORITHM=INSTANT**. This is different than how our [**online schema migrations**](https://planetscale.com/docs/vitess/schema-changes/how-online-schema-change-tools-work.md) work.

Instant deployments will apply schema changes faster, however, these schema changes must be **auto-applied** and **cannot be reverted**.

### Who should use instant deployments?

Instant deployments are well-suited to experienced users who want their schema change to take effect immediately rather than going through the online schema migration process.

> [!WARNING]
>
> Although instant deployments complete near-instantaneously in most cases, they require briefly acquiring an exclusive metadata lock on the affected tables. To prevent the deployment from being blocked, instant deployments will immediately terminate any queries and transactions holding locks on the target table when they run.
>
> On a busy table this can mean a burst of failed application queries at the moment of deployment. Ensure your application has retry logic for terminated queries.
>
> In rare cases, if something prevents the instant deployment from acquiring or releasing the metadata lock, application writes to the table will be blocked until the lock is released.
>
> For details on which operations support `ALGORITHM=INSTANT` and their limitations, see the [MySQL InnoDB online DDL operations documentation](https://dev.mysql.com/doc/refman/en/innodb-online-ddl-operations.html).

### Supported operations

In order for a deploy request to be instantly deployed, *all* schema changes in the deploy request must be instantly deployable. Some of those changes include:

* Adding or dropping a column (with some exceptions)
* Changing or dropping a column's default value
* Changing an `ENUM` or `SET` definition

The following changes are examples of changes that are **not** instantly deployable:

* Changing a column's data type
* Adding a column with a non-literal default value
* Adding or dropping an index
* Altering the visibility of an index
* Adding or dropping a foreign key constraint
* Extending a `VARCHAR` column size
* Updating a column to `NULL` or `NOT NULL`

To know whether or not a deploy request is instantly deployable, look for the "Instantly deployable" badge on your deploy request. This badge will only be visible on deploy requests that can be deployed instantly.

![PlanetScale deploy request - deploy instantly badge](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/assets/docs/concepts/deploy-requests/deploy-instantly.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=6c60531060cf8770ddcee94ebfb7a422)

We recommend reading [MySQL's Online DDL documentation](https://dev.mysql.com/doc/refman/en/innodb-online-ddl-operations.html) for the full list of operations that can be deployed instantly.

### Prefer instant

Admins can enable **Prefer instant** on the database **Settings** page under **Advanced settings**. When enabled, deploy requests that are instantly deployable default the deploy button to **Deploy changes instantly**. You can still choose **Deploy changes** from the dropdown.

The setting applies to all future deploy requests that have not started deploying. It does not change in-progress regular deploys.

CLI and API deploys are unchanged. Pass [`--instant`](../../cli/deploy-request.md) with `pscale deploy-request deploy`, or set `instant_ddl` when [queueing a deploy request](https://planetscale.com/docs/api/reference/queue_deploy_request.md).

Before enabling this as a default, review [supported operations](#supported-operations) and [who should use instant deployments](#who-should-use-instant-deployments). Instant deployments cannot be reverted and can terminate queries holding locks on the table.

## Gated deployments

Gated deployments give you more control over when a migration goes live after the deployment process completes.

As part of our non-blocking schema change process, instead of directly modifying table(s) when you deploy a deploy request, we make a copy of the affected table(s) and apply changes to the copy. We get the data from the original table and the copy table in sync, and once complete, initiate a quick cutover where we swap the tables.

> [!NOTE]
>
> If a deploy request includes changes to multiple tables, all tables cut over at the same time — unless there is a sequential dependency.

With gated deployments, you can initiate the deployment, but once the table syncing is complete, we'll hold off on the cutover and let you click a button to swap the tables and complete the deployment. Gated deployments can be enabled on each deploy request by unchecking the "Auto-apply changes" box before you deploy.

This feature is helpful if you have long-running migrations. For very large or complex databases, deploying a schema change can take several hours to complete. In those scenarios, you don't want the cutover to happen while you're offline. With gated deployments, you can start the deployment process by adding your deploy request to the queue, and once it's done, you'll be able to click a button to merge it in and complete the deployment while you're there to monitor it.

### Enable gated deployments in the dashboard

**Step 1**

When you open a deploy request, uncheck the "**Auto-apply changes**" box.

**Step 2**

![PlanetScale deploy request - Auto-apply changes checkbox unchecked](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/deploy-requests/gated-deployments-2.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=934d81816eec779827ee80ffebc44491)

Once your deploy requests begins running, you'll also have the option to uncheck the box here.

**Step 3**

When your deploy request has completed and is ready for cutover, the "**Apply changes**" button will appear. You can now complete the deployment at any time by clicking this button.

### Enable gated deployments via CLI

You can also manage auto-apply settings using the [PlanetScale CLI](https://planetscale.com/docs/cli/deploy-request.md).

To create a deploy request with auto-apply disabled:

```bash theme={null}
pscale deploy-request create <DATABASE_NAME> <BRANCH_NAME> --disable-auto-apply
```

To disable auto-apply on an existing deploy request:

```bash theme={null}
pscale deploy-request edit <DATABASE_NAME> <DR_NUMBER> --disable-auto-apply
```

Once your deploy request has completed and is ready for cutover, trigger the swap:

```bash theme={null}
pscale deploy-request apply <DATABASE_NAME> <DR_NUMBER>
```

> [!NOTE]
>
> If neither `--enable-auto-apply` nor `--disable-auto-apply` is provided when creating a deploy request, the setting is inherited from the previous deploy request.

### Limitations

* An open gated deployment holds the **serial** deploy queue until you apply (or cancel) it. Later serial deploy requests wait behind it. You can still [deploy in parallel](#parallel-deployments) alongside a gated deployment if the changes touch different tables.
* Deploy requests that are [instantly deployed](#instant-deployments) cannot be gated.

For more information about this process and why we built it, check out the [Gated Deployments: Addressing the complexity of schema deployments at scale](https://planetscale.com/blog/gated-deployments-addressing-the-complexity-of-schema-deployments-at-scale) blog post.

## Parallel deployments

By default, deploy requests on a branch run one at a time through the serial deploy queue. Each deploy waits for the one ahead of it to finish before it starts.

**Parallel deployments** let you run two deploy requests at once: one in the serial queue, and one alongside it in a parallel lane. Put a slow change in the parallel lane and keep shipping faster ones through the queue, or leave slow work in the queue and run a quick change in parallel. Either way works, as long as the changes touch different tables.

### Deploy in parallel

When a deploy queue is already running on the branch, the deploy request Summary tab shows a deployment strategy choice:

* **Add to deploy queue**: joins the serial queue and waits its turn (default)
* **Deploy in parallel**: starts now, alongside the queue

Select **Deploy in parallel**, then confirm. The deploy request page and the database deploy queue view show parallel deploys in a separate lane from the serial queue.

You can also deploy in parallel from the [PlanetScale CLI](https://planetscale.com/docs/cli/deploy-request.md):

```bash theme={null}
pscale deploy-request deploy <DATABASE_NAME> <DR_NUMBER> --strategy parallel
```

Use `--strategy serial` (or omit `--strategy`) to join the serial queue.

If a deploy request is already waiting in the serial queue, remove it from the queue and deploy it again with the parallel strategy.

Everything else about the deploy (review, schema diff, gated cutover, and revert) works the same way as a serial deploy. Reverting a parallel deploy only undoes that deploy's changes. It does not rewind work the serial queue already completed, and it does not pause the serial queue.

### Limits

* **One parallel deploy at a time per branch.** If a parallel deploy is already running, you must wait for it to finish before starting another.
* **No overlapping table changes across lanes.** A parallel deploy and any deploy in the serial queue cannot modify the same table at the same time. The deploy is rejected if there is overlap.

### Performance considerations

> [!WARNING]
>
> Two deploys at once can **increase database load**. Impact depends on the database, available resources, and workload. Avoid running two resource-intensive deploy requests at the same time. For example, two large but quiet tables may be fine together, while busy tables or deploy requests that each change many tables can put more pressure on the database.

You can independently adjust the [throttler settings](https://planetscale.com/docs/vitess/schema-changes/throttling-deploy-requests.md) for each deploy request to control the impact on the database.

## Artifact tables

During schema changes, Vitess creates artifact tables to facilitate non-blocking migrations. For detailed information about artifact table behavior, storage implications, and cleanup processes, see the [Artifact tables in schema changes](https://planetscale.com/docs/vitess/schema-changes/artifact-tables) documentation.

## Revert a schema change

If you ever merge a deploy request, only to realize you need to undo it, PlanetScale can handle that! You have the option to revert a recently deployed schema change while maintaining data that was written to the original schema during that time.

> [!NOTE]
>
> Deploy requests that are instantly deployed *cannot* be reverted.

### How to revert a schema change

You can revert a deployment for **up to 30 minutes** after the deploying. After the 30 minute period is up, the deployment becomes permanent, and you will no longer have the option to revert.

[Revert a schema change](https://player.vimeo.com/video/830571822?dnt=true)

**Step 1**

Select the deploy request you want to revert.

**Step 2**

To revert the schema changes made with the deploy request, click "**Revert changes**" and confirm.

**Step 3**

We will immediately revert the base branch back to its previous schema.

**Step 4**

Any data that was written to the original schema in the time between deploying and reverting will remain in your database after the revert.

**Step 5**

The deploy request will be closed, but the branch will remain for you to continue development on if you choose.

### When is data not retained

There are some scenarios where some data is not retained when you revert your changes.

1. You add a table or column to your schema and then revert it. If any data was written to those newly introduced fields between deployment and reverting, that data will not be retained upon revert, as the fields will no longer exist.

### When a revert can result in orphaned rows

In some cases, when you are using foreign key constraints, a revert of a deploy request can result in orphaned rows. These can happen when your schema change is:

* Dropping a foreign key constraint: Once a foreign key constraint is dropped, new data written to the table is less constrained. Reverting this change may result in data that is inconsistent with the dropped foreign key constraint.
* Dropping a table with foreign key constraints: When a table with foreign key constraints is dropped, the parent table(s) will continue to be written to. If this change is reverted, data in the table that was dropped may no longer be consistent with its foreign key constraints.

> [!NOTE]
>
> You must enable [foreign key constraint](https://planetscale.com/docs/vitess/foreign-key-constraints.md) support in the database settings page before using them.

### When are you unable to revert a schema change

There are also some edge cases where reverting a schema change is not possible. We will always attempt to revert, but if there are scenarios where your data integrity is at risk, we will not proceed with the revert. The following are some cases where a revert will fail:

1. If you deploy a schema change that expands the length of some column, such as changing from `VARCHAR(10)` to `VARCHAR(50)`, and add new data larger than 10 characters to it, a revert attempt may fail. This is to protect your data. You may have written data to the `VARCHAR(50)` field in that time that will not fit in the smaller 10 character space. If no data is added between deployment and revert, the revert process can proceed.
2. Some examples of other similar scenarios where revert won't be possible (again, only if larger sized data is added between deployment and revert) are:
   * `INT` to `BIGINT`
   * `NOT NULL` to `NULL`
   * `TIMESTAMP` to `TIMESTAMP(6)`
   * `utf8` to `utf8mb4`
   * Any other operation that expands the size of a field
3. If you deploy a schema change that removes a unique key or relaxes a unique constraint, and in the time between deployment and attempting to revert, you insert rows that would otherwise conflict with that constraint, the revert may fail.
4. Another uncommon but possible scenario: you deploy a schema change that has a `NOT NULL` column without a `DEFAULT` value, combined with an `ALTER TABLE DROP COLUMN` statement for that column. If you insert some rows between the deployment and the revert attempt, the revert will fail. We will not be able to re-add that column for the newly inserted rows and will not know how to populate it.

For an in-depth look at how this process works, check out our [Behind the scenes: how schema reverts work](https://planetscale.com/blog/behind-the-scenes-how-schema-reverts-work) blog post.

### Schema revert and migration data

If you've selected a migration framework or specified a table with migration data in the settings tab of your database, the data within the table that tracks migrations will be moved to the production branch only after the revert window has been closed. This is to ensure that if the deploy request is reverted, the production branch has the correct log of applied migrations.

### Billing considerations

You may see some temporary `_vt` tables in your database. These are called [artifact tables](https://planetscale.com/docs/vitess/schema-changes/artifact-tables) and are used to facilitate the deployment and revert process. They do not count toward your storage costs if you're using [network-attached storage](https://planetscale.com/docs/plans/planetscale-skus.md#network-attached-storage).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
