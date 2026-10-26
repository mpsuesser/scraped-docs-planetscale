---
url: https://planetscale.com/docs/postgres/traffic-control/getting-started
title: "Getting Started"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Getting started

> Adopt Database Traffic Control gradually. Start with ample limits, observe real behavior, and then tighten.

This page covers Postgres.

## Ensure your application handles blocked queries gracefully

Database Traffic Control allows you to categorize and place resource budgets on your query traffic.
This is useful to opt for the failure of individual **queries** as opposed to the failure of your entire **application**.

Before you start, ensure that your application can handle blocked queries gracefully without crashing or cascading failures. For example, a blocked analytics query should not take down your checkout flow. This might mean adding try/catch blocks, implementing circuit breakers, or providing fallback behavior when a query is rejected.

Your application may also need retry logic for any blocked queries. For example, you can retry the query after a short delay while waiting for resources to become available.

With these in place, you can configure your first resource budget.

### Restart may be required

If your PlanetScale Postgres cluster existed before the launch of Database Traffic Control, you may need to update and restart your cluster to enable this feature.

## 1. Open the Database Traffic Control page

**Step 1**

From the PlanetScale dashboard, select your **Postgres database**.

**Step 2**

Go to the **Insights** page for that branch.

**Step 3**

Choose the **Database Traffic Control** page.

You should now see:

* A branch selector
* A time range selector (for example, past 15 minutes, past hour, past day)
* A graph for query violations
* A table of existing resource budgets, if you have any

## 2. Create your first resource budget in Warn mode

Start with a simple, low‑risk resource budget for a non‑critical workload, such as analytics.

> [!NOTE]
>
> To create a resource budget that matches a specific query, you will need to find that query in the Insights page and click **Create resource budget**. It is preferred to create resource budgets based on system attributes or tags.

**Step 1**

On the Database Traffic Control page, click **New resource budget**.

**Step 2**

Give the resource budget a name (for example, `OLAP user limits`).

**Step 3**

Select the **Branch** you want to configure (for example, `main`).

**Step 4**

Select the key/value pair that will be used to match the resource budget rule. You can select any existing tags that have been included in recent queries, or alternatively create a new key/value pair in advance of those tags.

For example to match any query including `username='olap_user'` you would select `username` as the key and `olap_user` as the value.

**Step 5**

Configure the limits applied to queries matching the resource budget rule. For now, keep them on the higher side of the slider, as you can always tighten them later.

* **Server share**
* **Burst limit**
* **Per-query limit**
* **Maximum concurrent queries**

New resource budgets are started in **Warn** mode, which means they will not block any queries yet. It will only record where your limits would have been exceeded.

## 3. Observe and tune in "Warn" mode

Let the resource budget run in Warn mode for at least a few traffic cycles (hours or days, depending on your workload).

On the Database Traffic Control page:

* Use the **time range selector** to examine recent activity.
* Look at the **violations graph** to see when and how often your resource budget would have triggered.
* Check the **resource budgets table** for the violation count on your new resource budget.

Based on what you see:

* If violations are **very frequent** during normal operations, your limits may be too strict. Consider increasing capacity or concurrency.
* If violations are **rare or non‑existent**, your limits may be too generous to provide real protection. Consider tightening them slightly.

Aim for a configuration where:

* Normal, expected workloads do not hit the limits
* Misconfigured jobs or runaway queries quickly produce visible violations

Queries that would exceed the resource budget in **Warn** mode still return results, but the driver typically receives a **notice** whose text includes `[PGINSIGHTS] Traffic Control:` and the reason—use that for logging and tuning before you enable **Enforce**.

## 4. Move to Enforce

Once you are comfortable with how the resource budget behaves in Warn mode:

**Step 1**

Communicate with the team that owns the affected workload.

**Step 2**

On the Database Traffic Control page, edit the resource budget.

**Step 3**

Change **Mode** from **Warn** to **Enforce**.

**Step 4**

Save the resource budget.

From this point on:

* Queries that exceed the configured limits for that workload will be blocked
* Violations will continue to appear in the graph and the resource budgets table

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

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
