---
url: https://planetscale.com/docs/vitess/schema-changes/long-running-transactions
title: "Long Running Transactions"
description: ""
access_date: 2026-08-31T07:29:59.083Z
current_date: 2026-08-31T07:29:59.083Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Long-running transactions

**Platform availability:** Vitess only

When you deploy a schema change, PlanetScale copies the table in the background so the original stays available. That copy needs a brief lock on the table: once at the start, and again as copying continues. The last step, [cutover](aggressive-cutover.md), needs a lock too.

## Why the deploy request failed

If Vitess is unable to get a lock on the table, it will fail the deploy. This is most commonly caused by a long-running transaction that is still open on the table. For example, if your app opens a transaction, updates the table, and then doesn't commit or rollback, Vitess will be unable to get a lock on the table.

[Force cutover](aggressive-cutover.md) only helps after copy has finished. If copy never finished, it won't do anything.

If the change is [instantly deployable](deploy-requests.md#instant-deployments), Instant deploy skips the copy, so it doesn't need this lock.

If copy already finished and the deploy says "Attempting to lock the table", see [Aggressive cutover](aggressive-cutover.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
