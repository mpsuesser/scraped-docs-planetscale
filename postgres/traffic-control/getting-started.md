---
url: https://planetscale.com/docs/postgres/traffic-control/getting-started
title: "Getting Started"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Ensure your application handles blocked queries gracefully

Database Traffic Control allows you to categorize and place resource budgets on your query traffic. This is useful to opt for the failure of individual **queries** as opposed to the failure of your entire **application**.

Before you start, ensure that your application can handle blocked queries gracefully without crashing or cascading failures. For example, a blocked analytics query should not take down your checkout flow. This might mean adding try/catch blocks, implementing circuit breakers, or providing fallback behavior when a query is rejected.

Your application may also need retry logic for any blocked queries. For example, you can retry the query after a short delay while waiting for resources to become available.

With these in place, you can configure your first resource budget.

### Restart may be required

If your PlanetScale Postgres cluster existed before the launch of Database Traffic Control, you may need to update and restart your cluster to enable this feature.

## 1\. Open the Database Traffic Control page

You should now see:

- A branch selector
- A time range selector (for example, past 15 minutes, past hour, past day)
- A graph for query violations
- A table of existing resource budgets, if you have any

## 2\. Create your first resource budget in Warn mode

Start with a simple, low‑risk resource budget for a non‑critical workload, such as analytics.

To create a resource budget that matches a specific query, you will need to find that query in the Insights page and click **Create resource budget**. It is preferred to create resource budgets based on system attributes or tags.

New resource budgets are started in **Warn** mode, which means they will not block any queries yet. It will only record where your limits would have been exceeded.

## 3\. Observe and tune in “Warn” mode

Let the resource budget run in Warn mode for at least a few traffic cycles (hours or days, depending on your workload).

On the Database Traffic Control page:

- Use the **time range selector** to examine recent activity.
- Look at the **violations graph** to see when and how often your resource budget would have triggered.
- Check the **resource budgets table** for the violation count on your new resource budget.

Based on what you see:

- If violations are **very frequent** during normal operations, your limits may be too strict. Consider increasing capacity or concurrency.
- If violations are **rare or non‑existent**, your limits may be too generous to provide real protection. Consider tightening them slightly.

Aim for a configuration where:

- Normal, expected workloads do not hit the limits
- Misconfigured jobs or runaway queries quickly produce visible violations

Queries that would exceed the resource budget in **Warn** mode still return results, but the driver typically receives a **notice** whose text includes `[PGINSIGHTS] Traffic Control:` and the reason—use that for logging and tuning before you enable **Enforce**.

## 4\. Move to Enforce

Once you are comfortable with how the resource budget behaves in Warn mode:

From this point on:

- Queries that exceed the configured limits for that workload will be blocked
- Violations will continue to appear in the graph and the resource budgets table

## Approaches to adoption

Database Traffic Control is flexible enough to support different organizational philosophies. Two common approaches are:

### Restrictive by default

Start by creating a broad resource budget that applies to most or all traffic with conservative limits. Then create additional resource budgets for specific workloads that need higher limits.

This approach is useful when you want tight control over resource usage and prefer to set higher limits for workloads that need them.

### Permissive by default

Leave most traffic unconstrained and only create resource budgets for specific workloads you want to limit, such as analytics, integrations, or known-heavy features.

This approach is useful when you want minimal friction for most workloads and only need to rein in specific problem areas.

Both approaches are valid. The best approach for your organization may depend on your operational culture, the diversity of your workloads, and the level of visibility you already have into query behavior.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
