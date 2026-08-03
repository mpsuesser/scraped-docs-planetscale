---
url: https://planetscale.com/docs/postgres/traffic-control/faq-and-troubleshooting
title: "Faq And Troubleshooting"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# FAQ and troubleshooting

> Common questions and troubleshooting help.

export const PlatformAvailability = ({current, vitess, postgres}) => {
  const docsHref = path => {
    if (!path) return path;
    const normalized = path.startsWith('/') ? path : `/${path}`;
    return normalized;
  };
  const labels = {
    vitess: 'Vitess',
    postgres: 'Postgres'
  };
  if (current === 'both') {
    return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
        <span data-engine="both" data-state="current" aria-current="true" className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
          Vitess and Postgres
        </span>
      </div>;
  }
  const hasVitess = current === 'vitess' || Boolean(vitess);
  const hasPostgres = current === 'postgres' || Boolean(postgres);
  const only = !(hasVitess && hasPostgres);
  const engines = [];
  if (current === 'vitess' || current === 'postgres') engines.push(current);
  if (hasVitess && current !== 'vitess') engines.push('vitess');
  if (hasPostgres && current !== 'postgres') engines.push('postgres');
  return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
      {engines.map(engine => {
    const isCurrent = current === engine;
    const href = docsHref(engine === 'vitess' ? vitess : postgres);
    const label = only ? `${labels[engine]} only` : labels[engine];
    const state = isCurrent || !href ? 'current' : 'link';
    if (isCurrent || !href) {
      return <span key={engine} data-engine={engine} data-state={state} aria-current={isCurrent ? 'true' : undefined} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
              {label}
            </span>;
    }
    return <a key={engine} href={href} data-engine={engine} data-state={state} title={`View ${labels[engine]} documentation`} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
            {label}
            <svg aria-hidden="true" width="12" height="12" viewBox="0 0 12 12" fill="none" className="shrink-0">
              <path d="M2.5 6h7M6.5 3l3 3-3 3" stroke="currentColor" strokeWidth="1.5" strokeLinecap="round" strokeLinejoin="round" />
            </svg>
          </a>;
  })}
    </div>;
};

<PlatformAvailability current="postgres" />

## Behavior and expectations

<AccordionGroup>
  <Accordion title="Does Database Traffic Control change my schema, indexes, or query plans?">
    No. Database Traffic Control does not modify your schema or indexes, and it does not directly change how queries are planned. It focuses on whether queries are allowed to consume resources based on your configured resource budgets.
  </Accordion>

  <Accordion title="Is Database Traffic Control applied per cluster or per branch?">
    Resource budgets are configured **per [database branch](../branching.md)**. Each branch can have its own set of resource budgets.
  </Accordion>

  <Accordion title="What happens when a query exceeds a resource budget in Enforce mode?">
    Database Traffic Control blocks the query and records a **violation**.

    The blocked query returns an error to the client with **SQLSTATE `53000`**. The error message includes **`[PGINSIGHTS] Traffic Control:`** and details about why the query was blocked (for example, which limit was exceeded). Violations are visible in the Database Traffic Control page.
  </Accordion>

  <Accordion title="Does Database Traffic Control affect queries in Warn mode?">
    In **Warn** mode, matching queries are **not** blocked or throttled; they complete normally.

    The client still receives a **SQL notice** (warning) whose text includes **`[PGINSIGHTS] Traffic Control:`** and details about which limit would have been exceeded. Resource budgets also record **violations** for the UI. Violations are visible in the Database Traffic Control page.
  </Accordion>
</AccordionGroup>

## Resource budget matching

<AccordionGroup>
  <Accordion title="Can a query match multiple resource budgets?">
    Yes. A query can match more than one resource budget if its attributes satisfy multiple matching criteria. For example, a query might match one resource budget based on its username and another based on an application tag.

    When a query matches multiple resource budgets, it must satisfy **all** of them. If any matching resource budget would block the query, the query is blocked. Resource budgets are combined with AND logic, not OR.
  </Accordion>

  <Accordion title="How do I know which resource budgets a query matched?">
    The Database Traffic Control UI shows violations per resource budget. If a query is blocked, you can see which resource budget caused the violation. If a query matches multiple resource budgets but only one blocks it, the violation is attributed to the blocking resource budget.
  </Accordion>

  <Accordion title="My SQLCommenter tags are not showing up in Insights. What's wrong?">
    The most common cause is tag placement. Tags must appear **before** the semicolon that ends the SQL statement. See the [Query tags troubleshooting](../monitoring/query-tags.md#troubleshooting) section for the correct format and other common issues.
  </Accordion>
</AccordionGroup>

## Understanding and responding to high violation rates

<AccordionGroup>
  <Accordion title="I see a sudden spike in violations. What should I do?">
    1. Narrow the **time range** in the Database Traffic Control UI to the window where the spike occurs.
    2. Identify which resource budget has the highest violation count.
    3. Determine which workload or system attribute that resource budget targets.
    4. Use [Insights](../monitoring/query-insights.md) to see what changed (new deployment, new dashboard, larger batch job, etc.).
    5. Decide whether the workload is misbehaving or the limits are too strict:
       * If the workload is expected and healthy, consider relaxing limits slightly.
       * If the workload is unexpected or clearly problematic, work with the owning team to correct it.
  </Accordion>

  <Accordion title="Violations are constantly high for a critical application workload. Is that bad?">
    Consistently high violations for a key application workload suggest a mismatch between your resource budget limits and real usage. Either:

    * The application's normal behavior exceeds the resource budget limits, in which case you should adjust the resource budget; or
    * The application is issuing more or heavier queries than intended, in which case you should treat this as a performance issue.
    * Your queries might be poorly optimized, in which case you should inspect results in [Insights](../monitoring/query-insights.md) for more details, or debug queries with the [PlanetScale MCP Server](../../connect/mcp.md).
    * Your database may be under-provisioned, in which case you should consider [upgrading your cluster](../cluster-configuration.md) to a larger size.

    In any case, do not ignore sustained high violation counts for important workloads.
  </Accordion>
</AccordionGroup>

## Quickly rolling back a misconfiguration

<AccordionGroup>
  <Accordion title="I enabled Enforce and now legitimate traffic is failing. How do I revert quickly?">
    You have several options:

    * Change the resource budget **Mode** from **Enforce** back to **Warn** — For new queries, this stops enforcing the resource budget while still recording violations.
    * Temporarily set the resource budget **Mode** to **Off** — Use this for emergency rollback when you need to fully disable the resource budget.
    * Loosen the resource budget's limits — Increase capacity, burst, or concurrency to better match real traffic.

    After stabilizing the system, review what went wrong before re‑enabling enforcement.
  </Accordion>
</AccordionGroup>

## Configuration tips

<AccordionGroup>
  <Accordion title="How do I block expensive single queries immediately?">
    Create a resource budget that matches only that query, then set its allowed resource usage to `0` across the board. This will block all executions of that query.
  </Accordion>

  <Accordion title="How does concurrency interact with parallel queries?">
    Concurrency is expressed as a percentage of Postgres worker processes. A query with a parallel execution plan uses multiple worker processes, so a single parallel query may consume more than one unit of concurrency. Keep this in mind when setting concurrency limits for workloads that run parallel queries.
  </Accordion>

  <Accordion title="Can I control how much shared memory is used for resource budgets?">
    Yes. The [`traffic_control.shm_size`](../extensions/pginsights.md#database-traffic-control-shared-memory-size) parameter controls how much shared memory is allocated to store resource budgets. The default is `64kB`.

    This is especially important for rules that match each unique value for a tag key. The default size tracks about 600 active budget entries at once. The maximum size is `1MB`, which tracks about 10,000 active budget entries.

    The entries are stored in an LRU cache, so the limit applies to recently active values, not the total number of possible values. For example, a database can have more than 10,000 tenants as long as fewer than 10,000 are active during the time window Traffic Control needs to remember.
  </Accordion>
</AccordionGroup>

## Interaction with connection pooling and clients

<AccordionGroup>
  <Accordion title="How does Database Traffic Control interact with connection pooling?">
    Database Traffic Control is concerned with the queries that reach the database, regardless of whether they come from a pool or direct connections.

    See [Connection pooling](../connecting/pgbouncer.md) for more information.
  </Accordion>

  <Accordion title="Can I rely on Database Traffic Control instead of client‑side rate limiting?">
    Database Traffic Control protects your database, but it does not replace all forms of client‑side rate limiting. You may still need application‑level controls to:

    * Protect upstream services
    * Enforce per‑user or per‑tenant limits that are not visible at the database layer
    * Set appropriate query timeouts
  </Accordion>
</AccordionGroup>

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
