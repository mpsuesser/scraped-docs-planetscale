---
url: https://planetscale.com/docs/neki/transactions
title: "Transactions"
description: ""
access_date: 2026-10-15T11:57:20.597Z
current_date: 2026-10-15T11:57:20.597Z
---

Routing one statement to several Postgres shards does not turn those shards into one transactional database.

## Reads do not share a snapshot

An ordinary multi-shard read does not establish one shared Postgres snapshot across its destinations. Concurrent writes can therefore cause different shards to reflect different points in time within the same result.

## Multi-shard writes are not atomic

Neki commits each shard separately. It does not use two-phase commit to make every shard succeed or fail together. If one shard fails after another commits, the statement can leave different outcomes on the participating shards.

## Enforce a one-shard transaction

The default transaction routing mode is `multi`, which permits a transaction to reach more than one shard without making its commit atomic. Set the mode to `single` before starting a transaction when the application wants Neki to prevent that transaction from expanding to another shard:

```sql
SET __neki.tx_mode = 'single';
BEGIN;
```

Distributed and atomic cross-shard transactions are not yet supported. Keep a transaction on one shard when its writes must commit or roll back together.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
