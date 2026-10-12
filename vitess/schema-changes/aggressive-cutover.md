---
url: https://planetscale.com/docs/vitess/schema-changes/aggressive-cutover
title: "Aggressive Cutover"
description: ""
access_date: 2026-10-12T23:42:51.316Z
current_date: 2026-10-12T23:42:51.316Z
---

## Overview

Cutover is the final step in an online schema migration where Vitess atomically swaps the original table with a newly created shadow table containing the updated schema. Completing the swap requires a brief metadata lock (MDL) on the table.

Long-running queries or transactions holding locks on the table can prevent Vitess from acquiring the MDL, delaying cutover.

Force cutover only applies after copy has finished. If the deploy failed while copying, see [Long-running transactions](long-running-transactions.md).

## Force cutover

When cutover is blocked, Vitess retries for up to **1 hour**, then automatically runs force cutover and kills the blocking queries and transactions.

To complete the cutover sooner, use the **Force cutover now** button on the deploy request page, or run:

```shellscript
pscale deploy-request force-cutover <DATABASE_NAME> <DR_NUMBER>
```

This triggers force cutover immediately without waiting for the 1 hour timeout. Pass `--force` to skip the confirmation prompt.

Force cutover terminates active queries and transactions holding locks on the table. Understand which queries will be affected before using this option.

Having retry logic or a strategy to handle re-running killed queries is advised.

## Aggressive cutover for a single deploy request

If you expect one deploy request to be blocked, opt it into aggressive cutover when you create it or any time before you deploy it. Vitess kills blocking queries and transactions on the first cutover attempt for that deploy request only. The database setting and other deploy requests are unchanged.

```shellscript
pscale deploy-request create <DATABASE_NAME> <BRANCH_NAME> --aggressive-cutover
pscale deploy-request update <DATABASE_NAME> <DR_NUMBER> --aggressive-cutover
pscale deploy-request update <DATABASE_NAME> <DR_NUMBER> --aggressive-cutover=false
```

Through the API, pass `aggressive_cutover: true` when creating a deploy request, or `PUT /organizations/{organization}/databases/{database}/deploy-requests/{number}/aggressive-cutover` with `{ "enable": true }`. The deployment object reports the opt-in as `aggressive_cutover`.

## Aggressive cutover setting

For databases that regularly hit cutover delays, admins can enable **aggressive cutover** on the database **Settings** page under **Advanced settings** or with [`pscale database aggressive-cutover enable`](../../cli/database.md#database-level-aggressive-cutover). When enabled, Vitess kills blocking queries and transactions on the first cutover attempt instead of retrying for up to an hour. The setting applies to all future deploy requests.

### When to enable aggressive cutover

1. **Migration delayed notice**: Deploy requests showing “Migration delayed due to long-running transactions”
2. **Slow or long-running workloads**: Applications that consistently block cutover with slow queries or open transactions
3. **Frequent manual force cutover**: Regular use of **Force cutover now** on deploy requests

### Normal vs aggressive cutover behavior

**Normal cutover behavior:**

- Vitess attempts to acquire a metadata lock (MDL) on the table
- If blocked, it waits and retries for up to 1 hour
- After 1 hour, Vitess automatically runs force cutover

**Aggressive cutover behavior:**

- Vitess kills blocking queries and transactions on the first cutover attempt
- The cutover proceeds without waiting for blocking operations to complete

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
