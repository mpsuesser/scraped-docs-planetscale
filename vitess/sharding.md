---
url: https://planetscale.com/docs/vitess/sharding
title: "Sharding"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Sharding with PlanetScale

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

<PlatformAvailability current="vitess" postgres="/postgres/sharding" />

<Info>
  **Looking for Postgres sharding?** We're building [Neki](https://planetscale.com/neki) — sharded Postgres by the team behind Vitess. [Learn more](../postgres/sharding.md).
</Info>

<Note>
  You can create sharded keyspaces on any plan by adding a new sharded keyspace using the [Clusters page](cluster-configuration.md) and running an [unsharded to sharded workflow](sharding/sharding-quickstart.md) in your dashboard.

  If you would like additional support from our expert team, our [Enterprise plan](../planetscale-plans.md#enterprise-plan) may be a good fit. [Get in touch](https://planetscale.com/contact) for a quick assessment.
</Note>

## Sharding with PlanetScale

PlanetScale allows you to break up a large monolithic database and spread the data out across multiple servers.
This [reduces the load on a single database](https://planetscale.com/blog/one-million-queries-per-second-with-mysql) by distributing it across many.
PlanetScale achieves this by building our product on top of [Vitess](https://vitess.io/), which provides a transparent [sharding solution](sharding.md) for MySQL databases.

### Sharding without application changes

Sharding is a proven database architecture used by many organizations to help them scale up when database demand grows.
When reaching this point, many organizations choose to scale and shard their database manually.
This typically involves adding a bunch of sharding-specific logic to the application layer and/or creating a whole new proxy component to manage the shards.
In addition to this, managing a large fleet of disparate database servers is a significant operational challenge.

When using PlanetScale, customers can keep sharding logic out of their applications and let us take the burden of infrastructure management.
This is because Vitess allows you to create sharded databases and have them appear to the application layer as a single, unified database.
This means simpler application architecture, allowing your developers to worry less about the database and more on their work.

PlanetScale abstracts away all of this complexity using the **VTGate** layer of Vitess.
The VTGates act as the entryway into a Vitess database cluster.
They handle incoming connections and route queries to the appropriate MySQL instances.

When you're at the point where you've maxed out your vertical scaling efforts and you know you need to shard your database, PlanetScale allows you to do so *without* the burden of rearchitecting your entire application.

## How does our sharding process work?

When it comes time to shard your database, we recommend following our [sharding quickstart](sharding/sharding-quickstart.md) guide. If you need assistance identifying the best [sharding scheme](https://vitess.io/docs/reference/features/sharding/#sharding-scheme) for your database, or are interested in expert-level support, our [Enterprise plan](https://planetscale.com/enterprise) may be a better option for you. [Get in touch](https://planetscale.com/contact) for more information.

PlanetScale uses an explicit sharding system.
This means that, if you are going to horizontally shard your data, we have to tell Vitess which sharding strategy to use for each sharded table.
This involves some conversations to understand what your application does and the size, schema, and queries of your database.
Once we have this complete view of your application, our next objective is to identify a sharding key.

The sharding key, or [Primary Vindex](https://vitess.io/docs/reference/features/vindexes) is what determines how the rows of your sharded tables will be distributed across servers.
For each table you want to shard, we must choose which column to use for the Vindex, and what [type of vindex](https://vitess.io/docs/reference/features/vindexes/#predefined-vindexes) to use with it.
Each shard will cover a range of keyspace ID values, so this mapping is used to identify which shard a row is in.

To determine a Primary Vindex, our team will analyze your schema and query patterns. We generally will ask you for the following information:

1. A copy of your schema.
2. Some indication of the size of each table in your database. We can typically gather this information by looking at `AUTO_INCREMENT` values, but may require additional context in some cases.
3. Information about your common query patterns — typically your most frequently used 50-100 queries.

Using this holistic view of your database, we can work to determine a good candidate for the Primary Vindex. During this analysis, we also begin to determine which tables should be sharded, whether you'll require secondary Vindexes ([Lookup Vindexes](https://vitess.io/docs/reference/features/vindexes/#functional-and-lookup-vindex)), the strategy for tables that don't contain our chosen Primary Vindex, and more.

While no sharding strategy can ever optimize for *all* query patterns, this deep analysis makes the strategy as efficient as possible for the majority of queries, especially the most frequently used queries.

And, again, this is all done without requiring you to rearchitect your application.

## When should you consider sharding a table

We generally recommend sharding when you're running into issues with:

* Application performance due to **data size** (this can vary widely, but when your database exceeds 250 GB or a particular table becomes especially large, sharding may be something to explore)
* Hitting vertical limits with **write throughput**
* Hitting replica limits with **read throughput**

We often see customers running into other infrastructure challenges before they really see their application performance impacted by large amounts of data. As your data grows, you may find your backups are becoming unreliable and time-consuming. Or you may have no way to test those large backups.

If it feels like some things are starting to break down in your infrastructure, but you aren't sure if sharding is the solution, we can still help you identify if it's the correct strategy for scaling your database.

If you would like to explore whether or not sharding is the right solution for you, [don't hesitate to reach out](https://planetscale.com/contact), and we'll be in touch.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
