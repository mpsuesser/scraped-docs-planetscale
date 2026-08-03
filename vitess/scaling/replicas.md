---
url: https://planetscale.com/docs/vitess/scaling/replicas
title: "Replicas"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Database replicas

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

<PlatformAvailability current="vitess" postgres="/postgres/scaling/replicas" />

## Overview

A replica is a continuously updated copy of your Vitess database. Replicas serve two main purposes:

* They provide an additional source for your data to be queried.
* They increase database availability by enabling fast failovers for maintenance or unexpected failure.

<Warning>
  Before utilizing replicas for reducing load on the primary, it's important to understand the trade-offs. For more information, see the [Data consistency and replication lag](#data-consistency-and-replication-lag) section.
</Warning>

For information on replicas running in additional regions, see: [Read-only regions](read-only-regions.md).

## How to query replicas

PlanetScale replicas can be used to read data and reduce load on the primary. PlanetScale does not automatically route queries to replicas unless you explicitly use a replica credential or tell your application to do so.

### 1. Create a global replica credential (recommended)

With global replica credentials, you can have one credential that will automatically route queries to your branch's replicas and read-only regions. If you do not have any read-only regions, it will route to the included replicas on [your plan](../../planetscale-plans.md). If you do have any read-only regions, the queries will route to the closest replica out of your read-only region replicas and included failover replicas.

To use this, create a new credential in the PlanetScale dashboard and select "**Replica**" as the connection type. You can then use the new credential's connection string to setup a replica-only database
connection.

If you have `pscale` installed locally, you can create a replica credential with the following command:

```shell theme={null}
pscale password create <database> <branch> <password_name> --replica
```

All queries made using this credential will be routed to the nearest replica, even as you add and remove read-only regions.

### 2. `USE @replica` (not recommended)

<Warning>
  We do not recommend using this method to query replicas. If you are currently using this method, we recommend switching to method 1.
</Warning>

If you are not using a global replica credential, then, by default, all queries are served by the primary. However you may route any `SELECT` queries to replicas by issuing the following command on a connection:

```sql theme={null}
USE @replica
```

Once this command is run, all subsequent `SELECT` queries on that connection will be served by your branch's replicas instead of the primary node in your cluster. Please note that this does not send queries to any read-only regions, if you want to route queries all replicas, including read-only regions, you should use a global replica credential (method 1 above).

<Tip>
  For querying replicas in separate regions, see: [Read-only regions](read-only-regions.md).
</Tip>

We highly recommend using a global replica credential to ensure that your queries are always routed to the nearest replica.

## High availability

Replicas within PlanetScale are used to enable high availability of your database. This is a part of the reason all production branches in PlanetScale are provided at least one replica. In situations where the underlying hardware or service hosting the primary MySQL node fails, our system will automatically elect a new primary node from the available replicas and reroute traffic to that new primary. This process is know as **reparenting** and typically is all handled within milliseconds or seconds.

Querying the primary during a reparent typically goes unnoticed, other than a bit of additional query latency.
This is because Vitess [buffers queries](https://vitess.io/docs/reference/features/vtgate-buffering) during this time.
You can incorporate retry logic into your application to handle the rare instances where an issue arises during reparenting.

When querying a replica during a reparent, you may encounter this error:

```
no healthy tablet available for 'keyspace:"${keyspace}" shard:"${shard}" tablet_type:REPLICA'
```

Incorporating retry logic into your application can help with this scenario as well.

## Multiple availability zones

In cloud architecture, regions are further broken down into data centers known as availability zones (or AZs for short). For example, the `us-east-1` region on AWS contains 6 default AZs available to customers starting with `us-east-1a` through `us-east-1f`. The infrastructure for all Base plan and Enterprise PlanetScale databases are distributed across 3 availability zones. In the instance of an AZ failure, your database will auto failover to an available AZ.

## Data consistency and replication lag

Whenever data is updated (`INSERT`, `UPDATE`, `DELETE`) on the primary node, those changes are synchronized to the replicas shortly after. The delay between when a primary is updated and the changes are applied to the replica is known as `replication lag`. Your databases replication lag is viewable on your [database dashboard](../architecture.md#replication-lag-at-a-glance). Replication lag can also be viewed on Datadog if you set up the [PlanetScale - Datadog integration](../integrations/datadog.md).

In Datadog, you can use the following formula to set alerts for replication lag:

```
(max:planetscale.replication_lag{ps_database:<DATABASE_NAME> ps_tablet_type:replica, ps_branch:<MAIN>})
```

Make sure you replace `<DATABASE_NAME>` with your PlanetScale database name and `<MAIN>` with the name of the branch for which you'd like to track replication lag.

It is important to be aware of replication lag whenever querying data from your replicas. For example, if you make an update and then immediately try to query for that updated data via a replica, it may not be available yet due to replication lag.

## When should you use replicas in your application?

Replicas are useful for offloading read-heavy workloads from the primary node. By using replicas, you can distribute the read load across multiple nodes, which can help improve the performance of your application. Some examples of where you might want to query a replica are scheduled jobs, analytics jobs, search features, or aggregate queries. Replicas can also be used to provide a read-only view of your data to users or applications that do not need to write data, such as when a user is logged out or writing one-off queries for debugging purposes.

## When are replicas used in PlanetScale?

We use replicas for every production database branch. The number of replicas for a given database depends on the selected plan for that database:

* **Base plan** — Base plan databases include 2 replicas per production branch distributed across multiple AZs in a given region.
* **Enterprise** — The Enterprise plan is customizable to fit the needs of your organization, and as such can have as many replicas as needed.

[Read-only regions](read-only-regions.md) also run replicas, hosted in a different geographical region. Each read-only region has its own cluster size and replica count, configured per keyspace independently of the primary region. It is important to note that the MySQL nodes in read-only regions are replicas intended only for reading data and are not eligible for failover if the primary node experiences an outage.

<Warning>
  Development branches DO NOT have replicas as they are intended for development only and are not designed with the same resiliency as production branches.
</Warning>

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
