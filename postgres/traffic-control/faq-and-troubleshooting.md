---
url: https://planetscale.com/docs/postgres/traffic-control/faq-and-troubleshooting
title: "Faq And Troubleshooting"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Behavior and expectations

Does Database Traffic Control change my schema, indexes, or query plans?

No. Database Traffic Control does not modify your schema or indexes, and it does not directly change how queries are planned. It focuses on whether queries are allowed to consume resources based on your configured resource budgets.

Is Database Traffic Control applied per cluster or per branch?

Resource budgets are configured **per [database branch](../branching.md)**. Each branch can have its own set of resource budgets.

What happens when a query exceeds a resource budget in Enforce mode?

Database Traffic Control blocks the query and records a **violation**.

The blocked query returns an error to the client with **SQLSTATE `53000`**. The error message includes **`[PGINSIGHTS] Traffic Control:`** and details about why the query was blocked (for example, which limit was exceeded). Violations are visible in the Database Traffic Control page.

Does Database Traffic Control affect queries in Warn mode?

In **Warn** mode, matching queries are **not** blocked or throttled; they complete normally.

The client still receives a **SQL notice** (warning) whose text includes **`[PGINSIGHTS] Traffic Control:`** and details about which limit would have been exceeded. Resource budgets also record **violations** for the UI. Violations are visible in the Database Traffic Control page.

## Resource budget matching

Can a query match multiple resource budgets?

Yes. A query can match more than one resource budget if its attributes satisfy multiple matching criteria. For example, a query might match one resource budget based on its username and another based on an application tag.

When a query matches multiple resource budgets, it must satisfy **all** of them. If any matching resource budget would block the query, the query is blocked. Resource budgets are combined with AND logic, not OR.

How do I know which resource budgets a query matched?

The Database Traffic Control UI shows violations per resource budget. If a query is blocked, you can see which resource budget caused the violation. If a query matches multiple resource budgets but only one blocks it, the violation is attributed to the blocking resource budget.

My SQLCommenter tags are not showing up in Insights. What's wrong?

The most common cause is tag placement. Tags must appear **before** the semicolon that ends the SQL statement. See the [Query tags troubleshooting](../monitoring/query-tags.md#troubleshooting) section for the correct format and other common issues.

## Understanding and responding to high violation rates

I see a sudden spike in violations. What should I do?

1. Narrow the **time range** in the Database Traffic Control UI to the window where the spike occurs.
2. Identify which resource budget has the highest violation count.
3. Determine which workload or system attribute that resource budget targets.
4. Use [Insights](../monitoring/query-insights.md) to see what changed (new deployment, new dashboard, larger batch job, etc.).
5. Decide whether the workload is misbehaving or the limits are too strict:
	- If the workload is expected and healthy, consider relaxing limits slightly.
		- If the workload is unexpected or clearly problematic, work with the owning team to correct it.

Violations are constantly high for a critical application workload. Is that bad?

Consistently high violations for a key application workload suggest a mismatch between your resource budget limits and real usage. Either:

- The application’s normal behavior exceeds the resource budget limits, in which case you should adjust the resource budget; or
- The application is issuing more or heavier queries than intended, in which case you should treat this as a performance issue.
- Your queries might be poorly optimized, in which case you should inspect results in [Insights](../monitoring/query-insights.md) for more details, or debug queries with the [PlanetScale MCP Server](../../connect/mcp.md).
- Your database may be under-provisioned, in which case you should consider [upgrading your cluster](../cluster-configuration.md) to a larger size.

In any case, do not ignore sustained high violation counts for important workloads.

## Quickly rolling back a misconfiguration

I enabled Enforce and now legitimate traffic is failing. How do I revert quickly?

You have several options:

- Change the resource budget **Mode** from **Enforce** back to **Warn** — For new queries, this stops enforcing the resource budget while still recording violations.
- Temporarily set the resource budget **Mode** to **Off** — Use this for emergency rollback when you need to fully disable the resource budget.
- Loosen the resource budget’s limits — Increase capacity, burst, or concurrency to better match real traffic.

After stabilizing the system, review what went wrong before re‑enabling enforcement.

## Configuration tips

How do I block expensive single queries immediately?

Create a resource budget that matches only that query, then set its allowed resource usage to `0` across the board. This will block all executions of that query.

How does concurrency interact with parallel queries?

Concurrency is expressed as a percentage of Postgres worker processes. A query with a parallel execution plan uses multiple worker processes, so a single parallel query may consume more than one unit of concurrency. Keep this in mind when setting concurrency limits for workloads that run parallel queries.

Can I control how much shared memory is used for resource budgets?

Yes. The [`traffic_control.shm_size`](../extensions/pginsights.md#database-traffic-control-shared-memory-size) parameter controls how much shared memory is allocated to store resource budgets. The default is `64kB`.

This is especially important for rules that match each unique value for a tag key. The default size tracks about 600 active budget entries at once. The maximum size is `1MB`, which tracks about 10,000 active budget entries.

The entries are stored in an LRU cache, so the limit applies to recently active values, not the total number of possible values. For example, a database can have more than 10,000 tenants as long as fewer than 10,000 are active during the time window Traffic Control needs to remember.

## Interaction with connection pooling and clients

How does Database Traffic Control interact with connection pooling?

Database Traffic Control is concerned with the queries that reach the database, regardless of whether they come from a pool or direct connections.

See [Connection pooling](../connecting/pgbouncer.md) for more information.

Can I rely on Database Traffic Control instead of client‑side rate limiting?

Database Traffic Control protects your database, but it does not replace all forms of client‑side rate limiting. You may still need application‑level controls to:

- Protect upstream services
- Enforce per‑user or per‑tenant limits that are not visible at the database layer
- Set appropriate query timeouts

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
