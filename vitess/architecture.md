---
url: https://planetscale.com/docs/vitess/architecture
title: "Architecture"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale Vitess architecture

> PlanetScale's Vitess product is designed for reliability, scalability, and developer productivity.

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

<PlatformAvailability current="vitess" postgres="/postgres/postgres-architecture" />

## Overview

We achieve these goals through a combination of [MySQL](terminology.md#mysql), [Vitess](terminology.md#vitess), and our own application and ecosystem we have built atop these open-source technologies.
There is a great deal of infrastructure that enables our databases to be highly-available, secure, and resilient.
In this article, you'll learn about what powers PlanetScale databases and how you can view your database's configuration on our app.

## The infrastructure diagram

After creating a PlanetScale account and joining at least one organization, you can create a database.
Each new database has a single default [keyspace](terminology.md#keyspace) — a logical database — with the same name as the database.
On the dashboard of every PlanetScale database is a diagram outlining the infrastructure that powers the database.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-darkmode.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=a0b98ba80694ce13202e0446b1320c8b" alt="Architecture diagram for a PlanetScale database" className="hidden dark:block" width="2752" height="1830" data-path="images/architecture/diagram-darkmode.png" />

  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=9ec6943a8d9e7fa4d258fdc00fc5f8e1" alt="Architecture diagram for a PlanetScale database" className="block dark:hidden" width="2752" height="1830" data-path="images/architecture/diagram.png" />
</Frame>

By default, the architecture diagram will show the architecture for the keyspace corresponding to your default branch.
Here's how you can tell what keyspace and branch you are viewing the diagram of:

<Frame>
  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-labeled-darkmode.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=0c19a6663d8974e3560b1efb44fb4a14" alt="Architecture diagram for a PlanetScale database" className="hidden dark:block" width="2752" height="1830" data-path="images/architecture/diagram-labeled-darkmode.png" />

  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-labeled.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=f8dfbba16aeda9c7e58a199f7649e9ae" alt="Architecture diagram for a PlanetScale database" className="block dark:hidden" width="2752" height="1830" data-path="images/architecture/diagram-labeled.png" />
</Frame>

### Production branches

Production branches are designed for production workloads, and as such are given enough resources to ensure high availability.
By default, every production branch has a single primary MySQL instance and two replicas.
Each primary also comes with 3 [VTGates](terminology.md#vtgate) across 3 availability zones, which act as proxies for your MySQL instances.
These are all pictured in the diagram for a production branch:

<Frame>
  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-production-branch-darkmode.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=fae76e888489bd1ad196371840984b66" alt="Production branch architecture" className="hidden dark:block" width="2752" height="1830" data-path="images/architecture/diagram-production-branch-darkmode.png" />

  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-production-branch.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=d1688f64961aa7260622d92bfc9f0122" alt="Production branch architecture" className="block dark:hidden" width="2752" height="1830" data-path="images/architecture/diagram-production-branch.png" />
</Frame>

Generally, the application connecting to this database need not be aware of these various components.
One exception to this is if you are specifically trying to [send queries to a replica](scaling/replicas.md#how-to-query-replicas).

### Development branches

Development branches are specced to enable the development and testing of new features and are not designed for production workloads.
When a new development branch is created, a single MySQL node is created along with a VTGate that handles connections to that node.
This is reflected in the diagram of a development branch.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-dev-branch-darkmode.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=8af84c23026143d186b95ca04dfa558a" alt="Development branch architecture" className="hidden dark:block" width="2752" height="1580" data-path="images/architecture/diagram-dev-branch-darkmode.png" />

  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-dev-branch.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=eb7a0c3d1251f39dc99676a50c42e61b" alt="Development branch architecture" className="block dark:hidden" width="2752" height="1580" data-path="images/architecture/diagram-dev-branch.png" />
</Frame>

When you promote a development branch to production status, PlanetScale automatically adds additional replicas and VTGates deployed across multiple availability zones in a given region.

### Read-only regions

The primary of your database is the only node that can accept writes, and it resides in a single region.
You can add [read-only regions](scaling/read-only-regions.md) to each keyspace on a production branch, which adds replicas in another region that can be used to serve read traffic.
This can help reduce read latency for application servers that are distributed around the world.
Each read-only region's cluster size and number of replicas are configured per keyspace on the [Clusters page](cluster-configuration.md).

Below, you can see our database has the primary and two replicas in `us-east-2` with read-only replicas added in both `us-west-2` and `eu-central-1`.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/read-only-regions-darkmode.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=63e8d17106114d32799041ac83132f9a" alt="Production branch with read-only regions architecture" className="hidden dark:block" width="2752" height="2146" data-path="images/architecture/read-only-regions-darkmode.png" />

  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/read-only-regions.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=e04cbb60812049bae0cd8e02d6ecfea0" alt="Production branch with read-only regions architecture" className="block dark:hidden" width="2752" height="2146" data-path="images/architecture/read-only-regions.png" />
</Frame>

The read-only replicas can be identified by the blue globe icon.

## Infrastructure metrics

Each element within the infrastructure diagram for PlanetScale database branches can be selected to display additional metrics related to that element.
These metrics are displayed in expandable cards that present themselves when an element is selected.
By default, the cards display metrics from the last 6 hours but can be adjusted if additional data is needed.

### VTGates

The VTGate node displays the total number of VTGates that exist for a given branch, as well as the number of availability zones in which they live.
Selecting the VTGates node will show the following metrics:

* Number of connections.
* Latency.
* Queries received.
* CPU.
* Memory consumption.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/vtgate-metrics-darkmode.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=101ba1ab472ce99c15ee209a7bef292f" alt="VTGate metrics" className="hidden dark:block" width="1430" height="1585" data-path="images/architecture/vtgate-metrics-darkmode.png" />

  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/vtgate-metrics.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=7f63563d72d7aaed530b17d5146045a4" alt="VTGate metrics" className="block dark:hidden" width="1430" height="1590" data-path="images/architecture/vtgate-metrics.png" />
</Frame>

### MySQL nodes

Each MySQL node in the diagram will display whether it is the primary node or a replica, along with the region where that node is deployed to.
Clicking any of the MySQL nodes will display the following metrics:

* Database reads and writes for that node.
* Queries served.
* IOPS.
* CPU and Memory utilization.
* Storage utilization over the past week.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/vttablet-metrics-darkmode.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=1f99ad640627adec3239aafb512c1971" alt="Primary MySQL node metrics" className="hidden dark:block" width="1430" height="1584" data-path="images/architecture/vttablet-metrics-darkmode.png" />

  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/vttablet-metrics.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=67b38884dca40c084cd994284c7e2ae3" alt="Primary MySQL node metrics" className="block dark:hidden" width="1430" height="1591" data-path="images/architecture/vttablet-metrics.png" />
</Frame>

Selecting a replica will display the replication lag in addition to the other metrics.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/replication-lag-diagram-darkmode.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=31040c286cf3145491788525fd199ce2" alt="Replication lag diagram" className="hidden dark:block" width="1698" height="570" data-path="images/architecture/replication-lag-diagram-darkmode.png" />

  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/replication-lag-diagram.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=1336d1234b183ef4dfbcf226c291eb3b" alt="Replication lag diagram" className="block dark:hidden" width="1698" height="570" data-path="images/architecture/replication-lag-diagram.png" />
</Frame>

### Replication lag at a glance

Within the infrastructure diagram, you'll also notice that there is a number near the connection points for each replica.
These numbers are a way to read the replication lag between the Primary node and that given node at a glance.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/replication-lag-darkmode.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=9942676168a7773c3bdb6a0d2aba6fdd" alt="Replication lag" className="hidden dark:block" width="2936" height="1614" data-path="images/architecture/replication-lag-darkmode.png" />

  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/replication-lag.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=1470e0d88205aaaca54acd2d59f2b5ce" alt="Replication lag" className="block dark:hidden" width="2934" height="1612" data-path="images/architecture/replication-lag.png" />
</Frame>

### Database shards

If your database is [sharded](sharding.md), the infrastructure diagram will represent that as a green stack of shards.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/shard-stack-darkmode.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=2ba7f95f99f6bf073e605cc6908719e7" alt="Stacked shards" className="hidden dark:block" width="2936" height="1810" data-path="images/architecture/shard-stack-darkmode.png" />

  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/shard-stack.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=18c7dfaa2566c3329b47bdb6dca445aa" alt="Stacked shards" className="block dark:hidden" width="2936" height="1810" data-path="images/architecture/shard-stack.png" />
</Frame>

Selecting the stack from the diagram will open a card displaying all of the shards belonging to that keyspace.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/shard-list-darkmode.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=4907d1c5691e4038d432fbc5422d629b" alt="Shard list" className="hidden dark:block" width="1850" height="2124" data-path="images/architecture/shard-list-darkmode.png" />

  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/shard-list.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=9b6ce295f63cf8e865594329aa6a33fe" alt="Shard list" className="block dark:hidden" width="1850" height="2130" data-path="images/architecture/shard-list.png" />
</Frame>

After selecting a shard, you'll be able to choose to look at metrics for either that shard's primary or one of its replicas.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/shard-primary-replicas-list-darkmode.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=081bb8c7aaa3ff611b90cdbe200aee78" alt="Shard list" className="hidden dark:block" width="1850" height="2124" data-path="images/architecture/shard-primary-replicas-list-darkmode.png" />

  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/shard-primary-replicas-list.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=1f7ba70b238190e895f0cb0411e6327c" alt="Shard list" className="block dark:hidden" width="1850" height="2130" data-path="images/architecture/shard-primary-replicas-list.png" />
</Frame>

Selecting one will show you the metrics for that specific node in your database architecture.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/shard-primary-metrics-darkmode.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=e7010d80052a66b368562959c9bdd7bf" alt="Shard" className="hidden dark:block" width="1850" height="2184" data-path="images/architecture/shard-primary-metrics-darkmode.png" />

  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/shard-primary-metrics.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=95d49bd3c5929730dfaebb759604f1dc" alt="Shard" className="block dark:hidden" width="1850" height="2198" data-path="images/architecture/shard-primary-metrics.png" />
</Frame>

### Resizing

You can use the [Clusters page](cluster-configuration.md) menu to resize your keyspaces.
When a resize is in progress, this will be indicated at the top of the diagram.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-resize-darkmode.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=00834fa96e69e1cf737dde913dabb063" alt="Architecture diagram with resize indicator" className="hidden dark:block" width="2666" height="1546" data-path="images/architecture/diagram-resize-darkmode.png" />

  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/diagram-resize.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=6ec8d02f12cd08efd6d29668a491c57d" alt="Architecture diagram with resize indicator" className="block dark:hidden" width="2666" height="1546" data-path="images/architecture/diagram-resize.png" />
</Frame>

Click on "**View**" to see the status for each shard being resized:

<Frame>
  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/resize-progress-darkmode.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=ebe3bc35bd59e66a2cbf76299e6984dc" alt="Per-shard resize status" className="hidden dark:block" width="2410" height="1784" data-path="images/architecture/resize-progress-darkmode.png" />

  <img src="https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/architecture/resize-progress.png?fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=06fdecd2db2bf1fdace4f830b3d41f10" alt="Per-shard resize status" className="block dark:hidden" width="2282" height="1652" data-path="images/architecture/resize-progress.png" />
</Frame>

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
