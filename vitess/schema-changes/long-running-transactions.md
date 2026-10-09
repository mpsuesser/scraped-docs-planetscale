---
url: https://planetscale.com/docs/vitess/schema-changes/long-running-transactions
title: "Long Running Transactions"
description: ""
access_date: 2026-10-09T23:32:13.485Z
current_date: 2026-10-09T23:32:13.485Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Long-running transactions

**Platform availability:** Vitess only

Long-running transactions on your database can cause deploy requests to fail.

When you deploy a schema change, PlanetScale copies the table in the background so the original stays available. That copy needs a brief lock on the table: once at the start, and again as copying continues. The last step, [cutover](aggressive-cutover.md), needs a lock too.

A deploy that cannot get the lock fails with a lock wait timeout. The statement after `during query` shows which step was waiting:

```text theme={null}
Lock wait timeout exceeded; try restarting transaction (errno 1205) (sqlstate HY000) during query: lock tables orders read
```

* `lock tables ... read` is the lock at the start of the copy.
* `RENAME TABLE ...` is the cutover.
* `CREATE TABLE ...` or `ALTER TABLE ...` on a `_vt_vrp_...` table is PlanetScale building the copy. These wait on a lock when the table has [foreign key constraints](../foreign-key-constraints.md) and a transaction is open on the parent or child table.

## How to fix long-running transactions

If Vitess is unable to get a lock on the table, it will fail the deploy. This is most commonly caused by a long-running transaction that is still open on the table. For example, if your app opens a transaction, updates the table, and then doesn't commit or rollback, Vitess will be unable to get a lock on the table.

Having this query pattern in low volume is generally not a problem. But if you have a high volume of these queries, it can cause Vitess to not be able to get a lock on the table.

To fix this, you need to: **Commit or rollback the transaction as soon as possible**

### External service calls

Another common anti-pattern is to open a transaction, update the table, and then wait on additional API calls (such as calls to an external service). This can cause the transaction to be held open for a long time, and can prevent the deploy request from completing.

In these cases, we recommend moving the external API calls to outside of the transaction.

## Additional tips

* The [aggressive cutover setting](aggressive-cutover.md) only helps after the copy phase has finished. If your deploy request is stuck in the copy phase, this setting will not help.
* If the change is [instantly deployable](deploy-requests.md#instant-deployments), Instant deploy skips the copy, so it doesn't need this lock.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
