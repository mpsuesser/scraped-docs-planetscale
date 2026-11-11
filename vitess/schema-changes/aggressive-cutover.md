---
url: https://planetscale.com/docs/vitess/schema-changes/aggressive-cutover
title: "Aggressive Cutover"
description: ""
access_date: 2026-11-11T21:19:24.875Z
current_date: 2026-11-11T21:19:24.875Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Aggressive deploy request cutover

This page covers Vitess.

## Overview

Cutover is the final step in an online schema migration where Vitess atomically swaps the original table with a newly created shadow table containing the updated schema. Completing the swap requires a brief metadata lock (MDL) on the table.

Long-running queries or transactions holding locks on the table can prevent Vitess from acquiring the MDL, delaying cutover.

Force cutover only applies after copy has finished. If the deploy failed while copying, see [Long-running transactions](https://planetscale.com/docs/vitess/schema-changes/long-running-transactions.md).

## Force cutover

When cutover is blocked, Vitess retries for up to **1 hour**, then automatically runs force cutover and kills the blocking queries and transactions.

To complete the cutover sooner, use the **Force cutover now** button on the deploy request page, or run:

```bash theme={null}
pscale deploy-request force-cutover <DATABASE_NAME> <DR_NUMBER>
```

This triggers force cutover immediately without waiting for the 1 hour timeout. Pass `--force` to skip the confirmation prompt.

> [!WARNING]
>
> Force cutover terminates active queries and transactions holding locks on the table. Understand which queries will be affected before using this option.

Having retry logic or a strategy to handle re-running killed queries is advised.

### Migrations outside deploy requests

The 1 hour automatic force cutover applies to deploy requests. Online DDL migrations you run directly, for example with `SET @@ddl_strategy='vitess'`, keep retrying cutover without forcing it. Each attempt can hold new queries on the table for up to about 10 seconds while it waits for the metadata lock.

To get the same behavior as deploy requests, include `--force-cut-over-after` in the DDL strategy:

```sql theme={null}
SET @@ddl_strategy='vitess --force-cut-over-after=1h';
```

To force cutover for a migration that is already waiting, run:

```sql theme={null}
ALTER VITESS_MIGRATION '<MIGRATION_UUID>' FORCE_CUTOVER;
```

## Aggressive cutover for a single deploy request

If you expect one deploy request to be blocked, opt it into aggressive cutover when you create it or any time before you deploy it. Vitess kills blocking queries and transactions on the first cutover attempt for that deploy request only. The database setting and other deploy requests are unchanged.

```bash theme={null}
pscale deploy-request create <DATABASE_NAME> <BRANCH_NAME> --aggressive-cutover
pscale deploy-request update <DATABASE_NAME> <DR_NUMBER> --aggressive-cutover
pscale deploy-request update <DATABASE_NAME> <DR_NUMBER> --aggressive-cutover=false
```

Through the API, pass `aggressive_cutover: true` when creating a deploy request, or `PUT /organizations/{organization}/databases/{database}/deploy-requests/{number}/aggressive-cutover` with `{ "enable": true }`. The deployment object reports the opt-in as `aggressive_cutover`.

## Aggressive cutover setting

For databases that regularly hit cutover delays, admins can enable **aggressive cutover** on the database **Settings** page under **Advanced settings** or with [`pscale database aggressive-cutover enable`](../../cli/database.md#database-level-aggressive-cutover). When enabled, Vitess kills blocking queries and transactions on the first cutover attempt instead of retrying for up to an hour. The setting applies to all future deploy requests.

### When to enable aggressive cutover

1. **Migration delayed notice**: Deploy requests showing "Migration delayed due to long-running transactions"
2. **Slow or long-running workloads**: Applications that consistently block cutover with slow queries or open transactions
3. **Frequent manual force cutover**: Regular use of **Force cutover now** on deploy requests

### Normal vs aggressive cutover behavior

**Normal cutover behavior:**

* Vitess attempts to acquire a metadata lock (MDL) on the table
* If blocked, it waits and retries for up to 1 hour
* After 1 hour, Vitess automatically runs force cutover

**Aggressive cutover behavior:**

* Vitess kills blocking queries and transactions on the first cutover attempt
* The cutover proceeds without waiting for blocking operations to complete

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
