---
url: https://planetscale.com/docs/postgres/traffic-control/examples-and-recipes
title: "Examples And Recipes"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Examples and recipes

> Scenarios to help you design effective resource budgets.

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

export const YouTubeEmbed = ({id, title}) => {
  return <Frame>
      <iframe src={`https://www.youtube-nocookie.com/embed/${id}?rel=0`} title={title} className="aspect-video w-full" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" />
    </Frame>;
};

<PlatformAvailability current="postgres" />

This video walkthrough demonstrates a situation where high-priority traffic is degraded by slower, low-priority traffic and how to solve it with resource budgets.

<YouTubeEmbed id="OLfeiG63UfA" title="DEMO: Protect your database with Traffic Control" />

The following are more recipes and examples for common uses of Database Traffic Control.

## Example 1: Protect production from OLAP and ad‑hoc queries

**Problem**

Your analytics team and business intelligence (BI) tools run heavy queries on the same Postgres database as your user‑facing application. Occasionally, a dashboard or ad‑hoc query slows down the entire system.

**Approach**

1. [Create a dedicated database user](../connecting/roles.md), for example `olap_user`.
2. Require analytics tools and analysts to connect using `olap_user`.
3. In **Insights → Database Traffic Control**, click **New resource budget**:
   * **Name**: `OLAP user limits`
   * **Mode**: Warn (initially)
   * **Matching criteria**: System attribute `username = olap_user`
   * **Limits**:
     * Moderate server share and rate
     * Burst limit set low enough to prevent any single analytics query from consuming a large share of the budget
     * Concurrency set lower than the application users
4. Observe violations in **Warn** mode and adjust limits.
5. Switch the resource budget to **Enforce** once the pattern is stable.

**Outcome**

Analytics queries cannot consume unlimited concurrency or capacity. If a dashboard or exploratory query behaves badly, it hits the resource budget limits before impacting application traffic.

***

## Example 2: Tame a misbehaving integration or extract, transform, load (ETL) job

**Problem**

A third‑party integration or ETL job runs periodic backfills or syncs. When it runs, it spikes query volume and sometimes slows down production.

**Approach**

1. [Assign the integration its own database user](../connecting/roles.md), for example `integration_user`.
2. In **Insights → Database Traffic Control**, click **New resource budget**:
   * **Name**: `Integration limits`
   * **Mode**: Warn
   * **Matching criteria**: System attribute `username = integration_user`
   * **Limits**:
     * Conservative concurrency (for example, a small percentage of worker processes)
     * Modest capacity and rate suitable for the job's schedule
     * Burst low enough to prevent any single integration query from being too expensive
3. Let the resource budget run in Warn mode through several integration runs.
4. Tune limits so that normal runs do not trigger violations, but unexpected spikes do.
5. Change Mode to **Enforce**.

**Outcome**

The integration runs reliably, but if it is misconfigured or begins to behave differently, Database Traffic Control limits its impact and makes the problem visible via violations.

***

## Example 3: Soft‑launch a new feature with tight initial limits

**Problem**

You want to launch a new feature that requires several new queries. You are not yet confident about how those queries will behave in production.

**Approach**

1. Ensure your application tags queries from the new feature, for example `feature='new_dashboard'`.
2. In **Insights → Database Traffic Control**, click **New resource budget**:
   * **Name**: `New dashboard feature`
   * **Mode**: Warn
   * **Matching criteria**: Tag rule `feature` = `new_dashboard`
   * **Limits**:
     * Tight initial limits on server share, burst limit, and maximum concurrent queries
3. Launch the feature to a subset of users.
4. Watch violations and [Insights](../monitoring/query-insights.md) metrics to see how the feature behaves.
5. If everything looks healthy, gradually relax limits.
6. Once the feature is stable and widely rolled out, consider whether you still need a dedicated resource budget or whether the traffic can be absorbed into your general application limits.

**Outcome**

You can safely roll out the feature knowing that any unexpected behavior is constrained to a well‑defined resource budget, without needing to manage separate database users for each feature.

***

## Example 4: Target a specific application via tags

**Problem**

You want to limit or closely observe traffic from a particular application without relying on username matching. For example, a new frontend app is sending traffic to the same database as an existing monolith.

**Approach**

1. Ensure your application emits a tag identifying itself, for example `app='event-website'`.
2. In **Insights → Database Traffic Control**, click **New resource budget**:
   * **Name**: `Event website limits`
   * **Mode**: Warn (initially)
   * **Matching criteria**: Tag rule `app` = `event-website`
3. Configure limits appropriate for this app:
   * Server share and rate that reflect normal behavior for this traffic
   * Burst limit set low enough that a single query from this app cannot consume a disproportionate share of the budget
   * Maximum concurrent queries lower than core app traffic
4. Let the resource budget run in **Warn** mode while the app rolls out.
5. Review violations and performance:
   * If the app is noisy or misbehaving, you will see frequent violations
   * If it behaves as expected, violations should be rare or absent
6. When you are confident in the configuration, switch the resource budget to **Enforce** to actively constrain this traffic.

**Outcome**

You can control and observe traffic from a specific application based on tags, without changing your database users. This is especially useful when multiple apps share the same username for historical reasons.

***

## Example 5: Limit traffic from a specific API route

**Problem**

A specific API endpoint in your application generates heavy database queries. For example, an `/api/export` or `/api/search` route that allows users to request large result sets, and you want to prevent these queries from impacting the rest of your application.

**Approach**

1. Ensure your application uses [query tags](../monitoring/query-tags.md) to tag queries with the originating route, for example `route='api-export'`.
2. In **Insights → Database Traffic Control**, click **New resource budget**:
   * **Name**: `Export endpoint limits`
   * **Mode**: Warn (initially)
   * **Matching criteria**: Tag rule `route` = `/api/export`
   * **Limits**:
     * Lower maximum concurrent queries than general application traffic
     * Moderate server share and rate to allow exports to complete, but with limits
     * Burst limit is sized so that a single large export query cannot exhaust the budget
3. Monitor violations to understand typical export behavior.
4. Adjust limits so that normal exports succeed, but unusually large or concurrent requests are constrained.
5. Switch to **Enforce** once you are confident in the configuration.

**Outcome**

Heavy queries from specific endpoints are isolated from the rest of your application traffic. Users can still run exports, but a spike in export requests or an unusually expensive query will not degrade performance for other users.

***

These examples are starting points. As your organization gains experience, you can develop your own internal playbooks and standard resource budget templates for common workloads.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
