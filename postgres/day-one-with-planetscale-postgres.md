---
url: https://planetscale.com/docs/postgres/day-one-with-planetscale-postgres
title: "Day One With Planetscale Postgres"
description: ""
access_date: 2026-10-20T18:01:41.586Z
current_date: 2026-10-20T18:01:41.586Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Day one with PlanetScale Postgres

> Top ten things you should be aware of to kill your p99, lower your egress, and get your application flying.

Just created a new PlanetScale Postgres database? Don't miss the platform's best features to get started.

## Agent setup

Run the [Agent setup prompt](../agent-setup/prompt.md) to have your AI agent assess your database configuration, install the PlanetScale skills and suggest improvements.

## Top ten features

### 1. [Migrate](imports/postgres-imports.md) an existing database

We have guides and tools available to help migrate from multiple sources. Start with our [migration assessment tool](https://planetscale.com/liftoff) for instant feedback on migration complexity, potential blockers, and the recommended migration path.

### 2. Create [roles and connections](connecting/roles.md)

Create user-defined roles for application connections to enable least-privilege permissions and credential rotation without downtime.

Your cluster's primary database has a local [PgBouncer](connecting/pgbouncer.md), but dedicated PgBouncers for primaries and replicas are available as an optional add-on for improved connection resilience and scalability.

### 3. Browse [Query Insights](monitoring/query-insights.md)

Built-in monitoring for past and current queries. Includes latency percentiles, QPS, rows read/written, and per-query pattern analytics. Use it to find bottlenecks before they become outages.

As you begin receiving traffic, Insights will alert you or your agent to errors, anomalies, and provide schema recommendations to improve performance.

> [!TIP]
>
> Watch "[Agents and Infrastructure](https://www.youtube.com/watch?v=zxvyO5vnknI)" with PlanetScale CEO Sam Lambert for a live demo of how infrastructure can be safely maintained and improved by a team of agents.

Query Insights data is available in the dashboard, API, MCP, and CLI.

[Fix Database Faults with PlanetScale Insights](https://www.youtube-nocookie.com/embed/OAPHvq51hWU?rel=0)

### 4. Install the [PlanetScale MCP server](../mcp-server.md)

Give your agent access to Query Insights' production data alongside your application code. With this combined context, it's the best way to do deep, automated analysis on database health and performance.

[The PlanetScale MCP server: AI for your database](https://www.youtube-nocookie.com/embed/d3bc3PryOfY?rel=0)

### 5. Explore the [pscale CLI](https://planetscale.com/docs/postgres/postgres-cli) and [PlanetScale API](../api/reference/getting-started-with-planetscale-api.md)

Perform administrative actions against your databases from the command line, or the same workflows programmatically via the API. Create branches, manage roles and service tokens, and wire PlanetScale into CI/CD.

The pscale CLI is the best way [automate processes with GitHub Actions](integrations/github-actions.md).

### 6. Configure [Database Traffic Control®](traffic-control.md)

Define resource budgets to prioritize traffic slices by throttling others. Fine-grained limits on CPU, I/O, and concurrency per query pattern or tag. Exclusive to PlanetScale Postgres.

[Introducing Database Traffic Control](https://www.youtube-nocookie.com/embed/YgrEGqABD8U?rel=0)

### 7. [Tag](monitoring/query-tags.md) your queries

Embed SQLCommenter key-value tags in SQL comments to closely monitor related queries and define Traffic Control rules by application, route, or feature.

### 8. Create your first [branch](branching.md)

Perform development and testing in isolated database environments per branch. Your new database started with a default branch called `main`. Development branches run on low-cost `PS-DEV` instances and can be created empty or from a backup.

### 9. Protect yourself with [pg\_strict](extensions/pg_strict.md)

Per-role safety guardrails that block dangerous query shapes, like `UPDATE`/`DELETE` without a `WHERE` clause, before they run.

[Protect your database. Use the pg\_strict Postgres extension.](https://www.youtube-nocookie.com/embed/noPnqjXsZxs?rel=0)

### 10. Enable and install [extensions](extensions.md)

Install standard and community extensions from the dashboard or via `CREATE EXTENSION`. Some require a cluster restart and must be enabled in the dashboard. Vote for new extensions at [ps-extensions.io](https://ps-extensions.io).

### 11. Join the Discord community

Bonus! Ask questions and share what you're building.

[Join the PlanetScale Discord community](https://pscale.link/community)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
