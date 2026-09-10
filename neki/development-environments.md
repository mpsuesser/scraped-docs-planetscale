---
url: https://planetscale.com/docs/neki/development-environments
title: "Development Environments"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

Use a Neki development branch for application development, integration tests, schema rehearsal, and other non-production work. Each branch is isolated from its parent and has its own connection strings and infrastructure.

## Initialize the environment

[Development branches start empty](branching.md#development-branches-start-empty). Treat environment creation as a repeatable application setup:

This workflow catches migrations that accidentally depend on objects or data already present in a long-lived database.

## Test with representative topology

A new development branch begins with one managed shard. One shard is enough for most schema and compatibility tests, but it cannot reveal every distributed query problem.

When testing sharding behavior:

- Create the additional shards and data topology deliberately.
- Seed rows that cover every key range.
- Test single-shard and scatter query plans.
- Exercise joins, transactions, sequences, reference tables, and GSIs used by the application.
- Check [Query Insights](monitoring/query-insights.md) and router metrics for unexpected fan-out.

## Use production-like data safely

Restore a backup to a new branch when a test requires existing data. Apply your organization’s access-control, retention, and sensitive-data policies to the restored branch. Delete the branch and any retained manual backups after the test is complete.

Restore sizing determines the new branch type. Select a development cluster SKU for every configuration profile and a development router SKU when creating a disposable environment. If you omit sizing, the restore inherits the source profile and router settings; restoring a production backup without development overrides creates a production branch with production availability, branch limits, and billing. Development and production profile sizes cannot be mixed in one restore.

Do not use a development branch as a production failover target. To serve production traffic from a tested environment, [promote the branch](branching.md#promote-a-development-branch) first. Promotion is not a substitute for a production availability and recovery plan.

## Control cost

Development branches have separate shard, router, storage, backup, and network usage. Delete environments that are no longer needed and keep test backup retention intentional. See [Neki pricing](pricing.md#development-branches).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
