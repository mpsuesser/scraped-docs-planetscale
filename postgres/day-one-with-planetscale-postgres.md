---
url: https://planetscale.com/docs/postgres/day-one-with-planetscale-postgres
title: "Day One With Planetscale Postgres"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

Just created a new PlanetScale Postgres database? Don’t miss the platform’s best features to get started.

## Agent setup

Run the [Agent setup prompt](../agent-setup/prompt.md) to have your AI agent assess your database configuration, install the PlanetScale skills and suggest improvements.

## Top ten features

### 1\. Migrate an existing database

We have guides and tools available to help migrate from multiple sources. Start with our [migration assessment tool](https://planetscale.com/liftoff) for instant feedback on migration complexity, potential blockers, and the recommended migration path.

### 2\. Create roles and connections

Create user-defined roles for application connections to enable least-privilege permissions and credential rotation without downtime.

Your cluster’s primary database has a local [PgBouncer](connecting/pgbouncer.md), but dedicated PgBouncers for primaries and replicas are available as an optional add-on for improved connection resilience and scalability.

### 3\. Browse Query Insights

Built-in monitoring for past and current queries. Includes latency percentiles, QPS, rows read/written, and per-query pattern analytics. Use it to find bottlenecks before they become outages.

As you begin receiving traffic, Insights will alert you or your agent to errors, anomalies, and provide schema recommendations to improve performance.

Watch “ [Agents and Infrastructure](https://www.youtube.com/watch?v=zxvyO5vnknI) ” with PlanetScale CEO Sam Lambert for a live demo of how infrastructure can be safely maintained and improved by a team of agents.

Query Insights data is available in the dashboard, API, MCP, and CLI.

### 4\. Install the PlanetScale MCP server

Give your agent access to Query Insights’ production data alongside your application code. With this combined context, it’s the best way to do deep, automated analysis on database health and performance.

### 5\. Explore the pscale CLI and PlanetScale API

Perform administrative actions against your databases from the command line, or the same workflows programmatically via the API. Create branches, manage roles and service tokens, and wire PlanetScale into CI/CD.

The pscale CLI is the best way [automate processes with GitHub Actions](integrations/github-actions.md).

### 6\. Configure Database Traffic Control®

Define resource budgets to prioritize traffic slices by throttling others. Fine-grained limits on CPU, I/O, and concurrency per query pattern or tag. Exclusive to PlanetScale Postgres.

### 7\. Tag your queries

Embed SQLCommenter key-value tags in SQL comments to closely monitor related queries and define Traffic Control rules by application, route, or feature.

### 8\. Create your first branch

Perform development and testing in isolated database environments per branch. Your new database started with a default branch called `main`. Development branches run on low-cost `PS-DEV` instances and can be created empty or from a backup.

### 9\. Protect yourself with pg\_strict

Per-role safety guardrails that block dangerous query shapes, like `UPDATE` / `DELETE` without a `WHERE` clause, before they run.

### 10\. Enable and install extensions

Install standard and community extensions from the dashboard or via `CREATE EXTENSION`. Some require a cluster restart and must be enabled in the dashboard. Vote for new extensions at [ps-extensions.io](https://ps-extensions.io/).

### 11\. Join the Discord community

Bonus! Ask questions and share what you’re building.

[Join the PlanetScale Discord community](https://pscale.link/community)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
