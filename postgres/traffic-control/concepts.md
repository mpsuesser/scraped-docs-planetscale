---
url: https://planetscale.com/docs/postgres/traffic-control/concepts
title: "Concepts"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Concepts

> Terminology and core concepts: resource budgets, rules, limits, and modes.

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

## Resource budgets

A **resource budget** is the primary Database Traffic Control® configuration mechanism. It defines how much database resource a specific slice of traffic is allowed to consume.

A resource budget:

* Applies to traffic that matches one or more **rules**, which can be a specific query, a system attribute, or a combination of SQLCommenter tags.
* Defines [**limits**](#resource-budget-limits) on resource consumption (capacity, rate, burst, concurrency)
* Handles responses in a [**mode**](#modes-enforce-warn-off) (Enforce, Warn, Off)
* Is scoped to a specific [database branch](../branching.md)

## Resource budget limits

When a query to the database arrives, Database Traffic Control first checks whether it matches any resource budget **rules**. If a match is found, the cost of executing the query is estimated, and the **limits** in that resource budget determine whether the query is allowed, warned, or blocked.

<Frame caption="Each resource budget contains optional settings to control the amount of resources a specific traffic slice can use.">
  <img className="hidden dark:block" src="https://mintcdn.com/planetscale-2/Pc1vKrfqALwLm39x/postgres/traffic-control/assets/create-new-resource-budget-dark.png?fit=max&auto=format&n=Pc1vKrfqALwLm39x&q=85&s=d39c9c83a2584223c84128b96267b4fc" alt="Create a new resource budget" width="1272" height="1787" data-path="postgres/traffic-control/assets/create-new-resource-budget-dark.png" />

  <img className="dark:hidden" src="https://mintcdn.com/planetscale-2/Pc1vKrfqALwLm39x/postgres/traffic-control/assets/create-new-resource-budget-light.png?fit=max&auto=format&n=Pc1vKrfqALwLm39x&q=85&s=df269f5829c69351a84889834280fac4" alt="Create a new resource budget" width="1272" height="1787" data-path="postgres/traffic-control/assets/create-new-resource-budget-light.png" />
</Frame>

Each resource budget has four optional, configurable limits:

* **Server share** - The max percentage of overall system resources available to queries managed by this budget.
* **Burst limit** - Burst limit is the maximum short-term CPU burst your server can use above its steady share, measured in whole-server seconds and refilled gradually over time.
* **Per-query limit** - The maximum resources any individual query can use, as a percentage of server resources in the first second.
* **Maximum concurrent queries** - Percentage of available worker processes this policy can use

### How resource budgets are estimated

Database Traffic Control estimates the cost of the query based on the recent behavior of similar queries. If the estimated cost exceeds the resource budget limits, the decision to block is enforced or a warning is issued, depending on the **mode** setting.

* There is insufficient capacity to handle the query: `fill_level + estimated_cost > capacity`
* The query is too expensive on its own: `estimated_cost > burst`

These estimates are not precise, real-time measurements. Database Traffic Control uses recent query behavior to make fast decisions about whether to allow, warn, or block a query before it runs. Each unique query pattern learns its own cost model over time, so estimates improve as the system observes more executions of the same query shape.

Under normal usage, queries flow through without noticeable impact. When a workload exceeds its configured limits, and the mode is set to **Enforce**, queries are blocked to keep other workloads healthy.

## Modes: Enforce, Warn, Off

Each resource budget has a **mode** that controls how strictly it is applied.

<Frame caption="You can set the mode of a resource budget to Enforce, Warn, or Off.">
  <img className="hidden dark:block" src="https://mintcdn.com/planetscale-2/Pc1vKrfqALwLm39x/postgres/traffic-control/assets/insights-traffic-enforce-banner-dark.png?fit=max&auto=format&n=Pc1vKrfqALwLm39x&q=85&s=2c7014d507eeebacb5675a32714fcc83" alt="Database Traffic Control mode enforcement" width="1416" height="206" data-path="postgres/traffic-control/assets/insights-traffic-enforce-banner-dark.png" />

  <img className="dark:hidden" src="https://mintcdn.com/planetscale-2/Pc1vKrfqALwLm39x/postgres/traffic-control/assets/insights-traffic-enforce-banner-light.png?fit=max&auto=format&n=Pc1vKrfqALwLm39x&q=85&s=86edb33c8a4e79d498b1e6ae2bc9243f" alt="Database Traffic Control mode enforcement" width="1416" height="206" data-path="postgres/traffic-control/assets/insights-traffic-enforce-banner-light.png" />
</Frame>

* **Enforce**
  * Limits are active
  * Queries that exceed limits are blocked
  * Violation counts are recorded and visible in the UI
  * Best choice for stabilizing workloads
  * Blocked queries return with the code **`53000`** and a message from `[PGINSIGHTS] Traffic Control:` and, the ID of the resource budget that blocked the query and the limit that was exceeded
* **Warn**
  * Limits are evaluated, but not enforced
  * Queries always continue
  * Violation counts are recorded and visible in the UI
  * Best choice for initial rollout or tuning
  * Query response contains a message indicating the query would have been blocked by Database Traffic Control, and which limit was exceeded
* **Off**
  * The resource budget does not participate in matching
  * No violations are recorded

It is best practice to roll out a new resource budget in stages:

1. Create a resource budget in **Warn** mode
2. Observe violation patterns and adjust limits
3. Switch to **Enforce** once you are confident in the configuration

### Warning thresholds on enforced budgets

Enforced resource budgets can also emit warnings before queries are blocked. You can set a separate **warning threshold** as a percentage of the burst limit, per-query limit, or concurrency limit. When a query's estimated cost crosses the warning threshold but stays below the enforced limit, the query runs normally and the client receives a SQL notice with the `[PGINSIGHTS] Traffic Control:` prefix — the same format used in Warn mode.

This gives you early visibility into workloads that are approaching their limits, so you can investigate and adjust before queries start getting blocked.

Warning thresholds are optional. If not set, only the enforced limits apply.

## Response messages

Resource budgets in **Warn** or **Enforce** mode attach a message to the SQL response when a limit would be or was exceeded.

Messages use the prefix **`[PGINSIGHTS] Traffic Control:`**, followed by the ID of the resource budget that blocked the query and the limit that was exceeded.

* **Enforce** — The query is blocked. The client receives a **SQL error** with **SQLSTATE `53000`** and a message that includes `[PGINSIGHTS] Traffic Control:` and the reason. Treat this as a failed statement and use retry or fallback logic as needed.
* **Warn** — The query still runs and returns a normal result. The client also receives a **SQL notice** (warning) whose text includes `[PGINSIGHTS] Traffic Control:` and the reason. Log or surface these notices for observability while tuning limits before enforcement.

Applications can detect Traffic Control specifically by **SQLSTATE `53000`** on blocked queries, by matching this prefix in error or notice text, or both.

## Rules

Resource budgets apply to specific traffic based on matching against a **rule**. Rules can be individual **queries**, **system attributes**, or **tags**.

System attributes and tags are matched on key-value pairs. System attributes are created automatically when using Postgres, while tags must be added to your queries.

You can combine rules with `AND` logic so that multiple rules must match, or `OR` logic so that any one of the rules must match, for the resource budget to apply.

<Frame caption="This rule will match any query that contains the `action='analytics'` tag">
  <img className="hidden dark:block" src="https://mintcdn.com/planetscale-2/Pc1vKrfqALwLm39x/postgres/traffic-control/assets/insights-traffic-edit-rules-dark.png?fit=max&auto=format&n=Pc1vKrfqALwLm39x&q=85&s=21ef3658e9528dcf0849bfb3e393e292" alt="Creating resource budget rules" width="1192" height="710" data-path="postgres/traffic-control/assets/insights-traffic-edit-rules-dark.png" />

  <img className="dark:hidden" src="https://mintcdn.com/planetscale-2/Pc1vKrfqALwLm39x/postgres/traffic-control/assets/insights-traffic-edit-rules-light.png?fit=max&auto=format&n=Pc1vKrfqALwLm39x&q=85&s=f043a46a9fad0836a517d597678eeecb" alt="Creating resource budget rules" width="1192" height="710" data-path="postgres/traffic-control/assets/insights-traffic-edit-rules-light.png" />
</Frame>

### System attribute rules

System attribute rules match key-value pairs that Postgres automatically provides for each connection. You can write rules that match against any of the following:

| Attribute          | Description                                       |
| ------------------ | ------------------------------------------------- |
| `username`         | The database role or user executing the query     |
| `remote_address`   | The client IP address of the connection           |
| `application_name` | The application name set by the connecting client |

To create a system attribute rule, traffic matching that attribute must have recently executed queries.

Usage examples include:

* Limit analytics queries by having them connect as a dedicated database user, like `analytics_user` and matching `username = analytics_user`
* Isolate traffic from a known internal service that connects from a fixed IP range by matching `remote_address`
* Target a specific service without creating separate database users by matching `application_name`

### Tag-based rules

Tag rules match against key-value pairs that your application attaches to queries using the [SQLCommenter](../monitoring/query-tags.md) format. A tag rule targets one or many queries that share the same tag.

To create a tag-based rule, recently executed queries must have included the tag.

Tag rules can also match each unique value for a selected tag key. This creates separate active budget tracking for each tag value, such as each application, worker pool, user, or tenant, without creating a separate rule for every value.

<Note>
  Rules that match each tag value use shared memory for every active value. The active values are stored in an LRU cache controlled by [`traffic_control.shm_size`](../extensions/pginsights.md#database-traffic-control-shared-memory-size). The default `64kB` size tracks about 600 active budget entries at once, while the maximum `1MB` size tracks about 10,000.

  You can use this with more total tag values than fit in the cache, as long as the number of recently active values stays within the configured size for the time window Traffic Control needs to remember.
</Note>

Usage examples include:

* Feature flags or code paths, such as `feature='beta_checkout'`
* Application tags, such as `app='web'`
* Route-level targeting, such as `route='api-export'`

For details on the tag format, how to add tags to your queries, and framework support, see [Query tags](../monitoring/query-tags.md).

### Query-based rules

Target a specific query by using the query itself as the rule.

You can create query-based rules by finding the query in the [Insights](../monitoring/query-insights.md) page and clicking **Create resource budget**.

<Frame>
  <img className="hidden dark:block" src="https://mintcdn.com/planetscale-2/Pc1vKrfqALwLm39x/postgres/traffic-control/assets/create-resource-budget-from-query-dark.png?fit=max&auto=format&n=Pc1vKrfqALwLm39x&q=85&s=f4f009c327dcaa6d7fdbbc0ddcbe5d6c" alt="Create resource budget from query" width="1536" height="172" data-path="postgres/traffic-control/assets/create-resource-budget-from-query-dark.png" />

  <img className="dark:hidden" src="https://mintcdn.com/planetscale-2/Pc1vKrfqALwLm39x/postgres/traffic-control/assets/create-resource-budget-from-query-light.png?fit=max&auto=format&n=Pc1vKrfqALwLm39x&q=85&s=777bdfbe53b04fab93600933c65d31c3" alt="Create resource budget from query" width="1536" height="172" data-path="postgres/traffic-control/assets/create-resource-budget-from-query-light.png" />
</Frame>

<Warning>
  This is recommended only for temporary cases where you need to test or limit the resources allocated to a specific query. Such as an emergency situation. Prefer system attribute or tag-based rules.
</Warning>

## Matching multiple rules

Resource budget rules may contain several attributes combined with `AND` or `OR` logic.

A query may also match more than one resource budget. For example, a query might match one resource budget based on its system attribute username and another based on an application tag.

When a query matches multiple resource budgets, it must satisfy **all** of them. If any matching resource budget would block the query, the query is blocked. Multiple resource budgets are combined with `AND` logic, not `OR`.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
