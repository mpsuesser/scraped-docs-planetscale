---
url: https://planetscale.com/docs/vitess/pricing
title: "Pricing"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Pricing

> A single Vitess database cluster includes the resources equivalent to 5 always-on instances within the base monthly plan cost.

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

<PlatformAvailability current="vitess" postgres="/postgres/pricing" />

Vitess clusters are charged based on the following:

* Cluster size and [region](#region-pricing)
* Storage size (for network-attached storage clusters)
* [Branch](schema-changes/branching.md) hours beyond the included 1440 hours
* Number of [replicas](scaling/replicas.md)
* Number and size of [VTGates](scaling/vtgates.md)
* [Read-only regions](scaling/read-only-regions.md)

## Branches

Each database includes one production branch that provides a primary instance and two replica instances spread across availability zones.
The primary serves all queries by default, but the replicas can be used to serve read traffic, and are also used to maintain high-availability of your cluster.

In addition to the three instances used by your production branch, \~1440 hours of [development branch](../planetscale-plans.md#development-branches) time per month is included.
The development branch time works out to two "always on" database instances, though we do recommend spinning them up and down and as needed during your development cycle.

These development branches can replace your existing development or staging environment on other providers.
Instead of purchasing 2-3 databases on PlanetScale to recreate your environment, you can just purchase one database cluster and use [branching](schema-changes/branching.md) for development.

You can upsize and downsize your cluster at any time. Pricing is prorated to the millisecond, so if you temporarily upsize, you will only be charged for the larger cluster size for the time that it was running. Billing for a new cluster size begins as soon as you begin the resize. You can also spin up additional production branches at any timing for additional cost. The pricing for these is also prorated.

## Read-only regions

[Read-only regions](scaling/read-only-regions.md) are an additional cost. Each read-only region is configured per keyspace with its own cluster size and replica count: the read-only region rate for the selected cluster size covers a single replica, and each additional replica is billed at the standard additional replica rate. Storage for read-only regions is billed as standalone storage and does not count toward your plan's included storage.

See the [read-only regions pricing documentation](scaling/read-only-regions.md#availability-and-pricing) for per-region rates and storage costs.

<Note>
  Cluster size options are capped at `PS-160` until you have a successfully paid an invoice of at least \$100. If you need larger sizes immediately, please [contact us](https://planetscale.com/contact) to unlock all sizes. You can find the full list of cluster sizes in our [Plans documentation](../planetscale-plans.md#base-plan).
</Note>

## Region pricing

### Amazon Web Services

<CardGroup>
  <Card title="ap-northeast-1 (Tokyo)" href="https://planetscale.com/pricing?region=ap-northeast" icon="angles-right" horizontal />

  <Card title="ap-south-1 (Mumbai)" href="https://planetscale.com/pricing?region=ap-south" icon="angles-right" horizontal />

  <Card title="ap-southeast-1 (Singapore)" href="https://planetscale.com/pricing?region=ap-southeast" icon="angles-right" horizontal />

  <Card title="ap-southeast-2 (Sydney)" href="https://planetscale.com/pricing?region=aws-ap-southeast-2" icon="angles-right" horizontal />

  <Card title="eu-central-1 (Frankfurt)" href="https://planetscale.com/pricing?region=eu-central" icon="angles-right" horizontal />

  <Card title="ca-central-1 (Montreal)" href="https://planetscale.com/pricing?region=aws-ca-central-1" icon="angles-right" horizontal />

  <Card title="eu-west-1 (Dublin)" href="https://planetscale.com/pricing?region=eu-west" icon="angles-right" horizontal />

  <Card title="eu-west-2 (London)" href="https://planetscale.com/pricing?region=aws-eu-west-2" icon="angles-right" horizontal />

  <Card title="sa-east-1 (Sao Paulo)" href="https://planetscale.com/pricing?region=aws-sa-east-1" icon="angles-right" horizontal />

  <Card title="us-east-1 (N. Virginia)" href="https://planetscale.com/pricing?region=us-east" icon="angles-right" horizontal />

  <Card title="us-east-2 (Ohio)" href="https://planetscale.com/pricing?region=us-east-2" icon="angles-right" horizontal />

  <Card title="us-west-2 (Oregon)" href="https://planetscale.com/pricing?region=us-west" icon="angles-right" horizontal />
</CardGroup>

### Google Cloud

<CardGroup>
  <Card title="asia-northeast3 (Seoul, South Korea)" href="https://planetscale.com/pricing?region=gcp-asia-northeast3" icon="angles-right" horizontal />

  <Card title="europe-west4 (Eemshaven, Netherlands)" href="https://planetscale.com/pricing?region=gcp-europe-west4" icon="angles-right" horizontal />

  <Card title="northamerica-northeast1 (Montréal, Québec)" href="https://planetscale.com/pricing?region=gcp-northamerica-northeast1" icon="angles-right" horizontal />

  <Card title="us-central1 (Council Bluffs, Iowa)" href="https://planetscale.com/pricing?region=gcp-us-central1" icon="angles-right" horizontal />

  <Card title="us-east1 (Moncks Corner, South Carolina)" href="https://planetscale.com/pricing?region=gcp-us-east1" icon="angles-right" horizontal />

  <Card title="us-east4 (Ashburn, Virginia)" href="https://planetscale.com/pricing?region=gcp-us-east4" icon="angles-right" horizontal />
</CardGroup>

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
