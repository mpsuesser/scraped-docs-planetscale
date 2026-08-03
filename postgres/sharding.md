---
url: https://planetscale.com/docs/postgres/sharding
title: "Sharding"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Horizontal sharding for Postgres

> Learn about Neki, PlanetScale's upcoming horizontal sharding solution for Postgres

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

<PlatformAvailability current="postgres" vitess="/vitess/sharding" />

<Info>
  Neki isn't publicly available yet, but we're already working privately with customers to begin moving their workloads over. Request early access at [`neki.dev`](https://neki.dev).
</Info>

## Introducing Neki

[Neki](https://planetscale.com/neki) is sharded Postgres by the team that operates some of the largest sharded [Vitess](../vitess.md) clusters in the world. We're building Neki in close collaboration with design partners who already operate Postgres at significant scale. Neki is architected from first principles to bring Vitess-level scale and reliability to Postgres workloads.

Vitess transformed MySQL sharding from an operational nightmare into an accessible, proven technology. It powers some of the largest sites on the internet: Slack, GitHub, Square, and hundreds more. Neki is being built from first principles to bring this same power to Postgres.

Neki is not a fork of Vitess. Vitess succeeds by leveraging MySQL's strengths and engineering around its weaknesses. Postgres is a fundamentally different database with its own architecture, replication model, and operational characteristics. To achieve Vitess-level capabilities for Postgres, we're building from scratch — applying everything we've learned from years of operating sharded databases at massive scale.

<CardGroup cols={2}>
  <Card title="Read the announcement" href="https://planetscale.com/blog/announcing-neki" icon="newspaper">
    Learn why we're building Neki and what it means for Postgres scaling.
  </Card>

  <Card title="Proven expertise at scale" icon="chart-line">
    Deep experience running Vitess-backed workloads in production.
  </Card>

  <Card title="Built from first principles" icon="compass-drafting">
    Postgres-first architecture — not a MySQL port.
  </Card>

  <Card title="Operational knowledge baked in" icon="gears">
    Built by engineers who operate sharded clusters every day.
  </Card>
</CardGroup>

## Neki features at a glance

* **Horizontal sharding for Postgres** — Scale beyond a single database instance by distributing data and load across multiple nodes.
* **Operational simplicity** — Automation-first design aimed at reducing toil and operational complexity common in hand-rolled sharding.
* **Production-informed architecture** — Failure handling, query routing, connection pooling, and distributed transaction patterns informed by operating sharded systems at scale.

## Why sharding matters

**Horizontal sharding** means splitting your data across multiple database instances (often called shards) so that each instance holds a subset of rows or partitions. Applications route queries to the right shard based on a shard key or routing layer. That differs from **vertical scaling**, where you add CPU, memory, or faster storage to a single server. Vertical scaling eventually hits practical ceilings on disk size and write throughput on one primary, and widens the blast radius when that node fails.

While a well-tuned single Postgres instance can scale further than many expect, horizontal sharding becomes the right choice when you need the benefits below — distributing load and data across several instances rather than growing one machine alone:

* **Scale beyond single-instance limits** — Handle multi-TB datasets and hundreds of thousands of queries per second
* **Improve write performance** — Distribute write load across multiple primary instances
* **Reduce single points of failure** — Isolate failures to individual shards rather than your entire dataset
* **Improve tenant isolation** — Isolate large or "noisy" customers so their workloads cannot impact others in a shared cluster

### When horizontal sharding makes sense

**Multi-tenant SaaS** often benefits when a few tenants dominate storage or query volume. Sharding by tenant (or a related key) can keep heavy tenants from starving others and can simplify capacity planning per segment.

**Very large datasets or I/O-heavy workloads** can exhaust the storage bandwidth or CPU of a single primary even after tuning. When you've addressed slow queries and optimized your storage class but still need more headroom, spreading data across shards increases scalability.

**Write-heavy applications** that bottleneck on a single writer can sometimes scale reads with replicas, but writes still concentrate on the primary. Sharding splits writes across multiple primaries.

### You might not need sharding

Before pursuing horizontal sharding, it's worth exploring whether your current performance challenges can be solved through optimization. We've helped many teams migrate to PlanetScale Postgres, dramatically reduce database load, and improve performance without the complexity of sharding.

[Query Insights](monitoring/query-insights.md) can identify slow queries, missing indexes, and inefficient query patterns that may be causing bottlenecks. Combined with [schema recommendations](monitoring/schema-recommendations.md), you can often achieve significant performance gains through targeted optimizations.

Our [Metal](../metal.md) nodes deliver exceptional raw performance with NVMe storage that handles throughput and latency far better than traditional cloud storage. Many workloads that appear to need sharding are actually hitting I/O limits that Metal eliminates entirely. For many workloads, moving to PlanetScale Postgres on Metal and fixing a few expensive queries offers years of headroom before sharding becomes necessary.

For teams currently facing Postgres scaling challenges, we recommend starting with these optimizations before considering sharding.

## How Neki approaches Postgres sharding

Neki applies operational experience from running large-scale sharded systems: architectural choices reflect how clusters fail, how they are upgraded, and where operators spend the most time.

It is **Postgres-native** — respecting replication, connections, and query execution in real deployments — and **built by operators, for operators**, with automation that targets reliability, scalability, and ease of use. Lessons from routing, pooling, failure handling, and sharding patterns that stay manageable at scale inform the design.

## Frequently asked questions

<AccordionGroup>
  <Accordion title="What is horizontal sharding?">
    Data (rows or logical partitions) is split across multiple database servers, and a router / proxy directs queries using a shard key. See [Why Sharding Matters](#why-sharding-matters) for definitions, routing detail, and how that compares to vertical scaling.
  </Accordion>

  <Accordion title="How does horizontal sharding differ from vertical scaling?">
    Vertical scaling grows one server; horizontal sharding adds instances and partitions data across them. See [Why Sharding Matters](#why-sharding-matters) for ceilings on vertical approaches and the tradeoffs sharding introduces.
  </Accordion>

  <Accordion title="When should I consider sharding my Postgres database?">
    When a well-tuned instance still cannot meet your goals after optimization. Many teams extend runway with query tuning and [Metal](../metal.md) first — see [You Might Not Need Sharding](#you-might-not-need-sharding). For typical workload patterns, see [When Horizontal Sharding Makes Sense](#when-horizontal-sharding-makes-sense).
  </Accordion>

  <Accordion title="Is Neki a fork of Vitess?">
    No, Vitess is tied to MySQL's protocol and semantics; Postgres differs in planner, replication, and ecosystem. For positioning and how we build Neki, see [Introducing Neki](#introducing-neki).
  </Accordion>

  <Accordion title="How does Neki relate to PlanetScale Postgres?">
    Neki is designed as PlanetScale's path to horizontal scale for Postgres workloads. Running on [PlanetScale Postgres](../postgres.md) today aligns your stack with the same platform and operational model Neki targets, which simplifies adoption when sharded Postgres fits your needs.
  </Accordion>

  <Accordion title="How can I stay informed about Neki?">
    Request access by filling out the form on [neki.dev](https://neki.dev) or email [neki@planetscale.com](mailto:neki@planetscale.com). More links are in [Stay Updated](#stay-updated) and [Related Resources](#related-resources).
  </Accordion>
</AccordionGroup>

## Stay updated

As Neki progresses towards general availability, we'll update this page with new information.

**We encourage you to check back regularly for updates:**

* **[Request access](https://neki.dev)** — Fill out the form on neki.dev to request access to Neki and receive updates on its progress.
* **[Read the PlanetScale blog](https://planetscale.com/blog)** — Follow our blog for announcements, technical deep-dives, and product updates.
* **[Contact the Neki team](mailto:neki@planetscale.com)** — Get in touch if you have questions or are interested in becoming a design partner.

## Related resources

* **[Neki product page](https://planetscale.com/neki)** — Learn more about Neki and why we're building it.
* **[Announcing Neki](https://planetscale.com/blog/announcing-neki)** — Read the launch post on why we're building Vitess-level scale for Postgres.
* **[Postgres vs Vitess](../postgres-vs-vitess.md)** — Compare PlanetScale's database products to choose the best fit.
* **[What is database sharding?](https://planetscale.com/blog/database-sharding)** — Learn the fundamentals of database sharding.
* **[Vitess sharding docs](../vitess/sharding.md)** — Explore how sharding works in Vitess for MySQL workloads.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
