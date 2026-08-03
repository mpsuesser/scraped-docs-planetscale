---
url: https://planetscale.com/docs/postgres/monitoring/prometheus-metrics-postgres
title: "Prometheus Metrics Postgres"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Prometheus metrics for PlanetScale Postgres

> PlanetScale Postgres exposes a Prometheus-compatible endpoint per-branch that allows you to scrape metrics for your database.

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

<PlatformAvailability current="postgres" vitess="/vitess/integrations/prometheus-metrics" />

## Overview

See our [Prometheus integration](../../vitess/integrations/prometheus.md) documentation for how to set Prometheus up to automatically discover and scrape metrics for your database branches.

If you're using Datadog, see our [Datadog tutorial](prometheus-metrics-datadog-postgres.md) for how to setup your Datadog agent to scrape metrics for your branch.

## Metrics

PlanetScale Postgres emits the following metrics to be scraped.

## Database Metrics

| **Name & Description**                                                                                                                                                                                                                    | **Type** | **Tags**                                                                                                                                                   |
| :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **planetscale\_postgres\_connection\_state**  The count and state of Postgres connections                                                                                                                                                 | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_connection\_state, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_postgres\_wal\_archiver\_succeeded\_count**  The count of successfully archived WALs                                                                                                                                       | Counter  | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component                                 |
| **planetscale\_postgres\_wal\_archiver\_failed\_count**  The count of unsuccessfully archived WALs                                                                                                                                        | Counter  | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component                                 |
| **planetscale\_postgres\_wal\_archiver\_last\_age\_succeeded**  The age of the last successfully archived WAL                                                                                                                             | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component                                 |
| **planetscale\_postgres\_wal\_archiver\_lag\_bytes**  WAL bytes written but not yet archived (current LSN minus last archived LSN)                                                                                                        | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component                                 |
| **planetscale\_postgres\_wal\_size\_bytes**  The cumulative disk size of WALs waiting to be archived                                                                                                                                      | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component                                 |
| **planetscale\_postgres\_settings\_max\_connections**  The current value of the max\_connections setting                                                                                                                                  | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component                                 |
| **planetscale\_postgres\_settings\_max\_wal\_size\_bytes**  The current value of the max\_wal\_size\_bytes setting                                                                                                                        | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component                                 |
| **planetscale\_postgres\_settings\_max\_slot\_wal\_keep\_size\_bytes**  The current value of the max\_slot\_wal\_keep\_size setting (-1 means unlimited)                                                                                  | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component                                 |
| **planetscale\_postgres\_replication\_slot\_max\_wal\_retained\_bytes**  The largest amount of WAL retained behind any logical replication slot on the primary; grows toward max\_slot\_wal\_keep\_size as a slot's consumer falls behind | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component                                 |
| **planetscale\_postgres\_replication\_slot\_min\_safe\_wal\_size\_bytes**  The smallest remaining headroom before a logical replication slot risks invalidation; absent when max\_slot\_wal\_keep\_size is unlimited                      | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component                                 |
| **planetscale\_postgres\_replication\_slots\_lost**  The number of logical replication slots on the primary that have been invalidated (lost) and must be resynced                                                                        | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component                                 |
| **planetscale\_postgres\_replica\_lag\_seconds**  Replica lag in fine-grained seconds from Postgres                                                                                                                                       | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component                                 |
| **planetscale\_postgres\_locks**  Count of current lock modes                                                                                                                                                                             | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_lock\_mode, planetscale\_role, planetscale\_cell, planetscale\_component        |
| **planetscale\_postgres\_database\_xact\_commit\_total**  Total committed transactions on Postgres databases                                                                                                                              | Counter  | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_database\_name, planetscale\_role, planetscale\_cell, planetscale\_component    |
| **planetscale\_backup\_restore\_active** Determines if a backup restore is active, tagged with the current phase (1 = active)                                                                                                             | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_phase, planetscale\_component                                                   |
| **planetscale\_backup\_fetch\_percent** Percentage progress of a backup fetch operation (0-100)                                                                                                                                           | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_component                                                                       |

## Edge Metrics

| **Name & Description**                                                                                                                            | **Type** | **Tags**                                                                                                       |
| :------------------------------------------------------------------------------------------------------------------------------------------------ | :------- | :------------------------------------------------------------------------------------------------------------- |
| **planetscale\_edge\_postgres\_active\_connections**  The number of active Postgres and PgBouncer connections to the branch                       | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_port, planetscale\_region                             |
| **planetscale\_edge\_postgres\_connection\_drops\_total**  The total number of Postgres and PgBouncer connections that have been dropped          | Counter  | cluster, planetscale\_database\_branch\_id, planetscale\_port, planetscale\_region                             |
| **planetscale\_edge\_postgres\_connection\_errors\_total**  The total number of Postgres and PgBouncer connections that have resulted in an error | Counter  | cluster, planetscale\_database\_branch\_id, planetscale\_port, planetscale\_region                             |
| **planetscale\_edge\_postgres\_bytes\_sent\_total**  The total number of Postgres and PgBouncer bytes sent                                        | Counter  | cluster, planetscale\_database\_branch\_id, planetscale\_port, planetscale\_connector\_id, planetscale\_region |
| **planetscale\_edge\_postgres\_bytes\_received\_total**  The total number of Postgres and PgBouncer bytes received                                | Counter  | cluster, planetscale\_database\_branch\_id, planetscale\_port, planetscale\_connector\_id, planetscale\_region |

## PgBouncer Metrics

| **Name & Description**                                                                                                               | **Type** | **Tags**                                                                                                                                                                         |
| :----------------------------------------------------------------------------------------------------------------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **planetscale\_pgbouncer\_total\_peers**  The total count of peered processes PgBouncer is running                                   | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component                               |
| **planetscale\_pgbouncer\_cpu\_util\_per\_peer\_percentages**  CPU utilization percentage of PgBouncer peered processes              | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component                               |
| **planetscale\_pgbouncer\_current\_connections**  The current count PgBouncer connections to Postgres                                | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component                               |
| **planetscale\_pgbouncer\_current\_client\_connections**  The current count client connections to PgBouncer                          | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component                               |
| **planetscale\_pgbouncer\_pools\_client**  The count and state of PgBouncer client connections                                       | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_pgbouncer\_pool, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_pgbouncer\_pools\_client\_maxwait\_seconds**  How long the first client connection has waited to connect to PgBouncer | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component                               |
| **planetscale\_pgbouncer\_pools\_server**  The count and state of PgBouncer server connections                                       | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_pgbouncer\_pool, planetscale\_role, planetscale\_cell, planetscale\_component |

## Infrastructure Metrics

| **Name & Description**                                                                               | **Type** | **Tags**                                                                                                                                                                         |
| :--------------------------------------------------------------------------------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **planetscale\_pods\_cpu\_util\_percentages**  CPU utilization percentage of database pods           | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component                               |
| **planetscale\_pods\_mem\_util\_percentages**  Memory utilization percentage of database pods        | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component                               |
| **planetscale\_pods\_iops\_total**  Total IOPS (Input/Output Operations Per Second) of database pods | Counter  | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component                               |
| **planetscale\_volume\_available\_bytes**  Available storage space in bytes on Postgres volumes      | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component                                                       |
| **planetscale\_volume\_capacity\_bytes**  Total storage capacity in bytes on Postgres volumes        | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component                                                       |
| **planetscale\_pods\_mem\_rss\_bytes**  RSS memory usage in bytes of database pods                   | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component                               |
| **planetscale\_pods\_mem\_mmap\_bytes**  Memory-mapped file usage in bytes of database pods          | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component                               |
| **planetscale\_pods\_mem\_active\_cache\_bytes**  Active cache memory in bytes of database pods      | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component                               |
| **planetscale\_pods\_mem\_inactive\_cache\_bytes**  Inactive cache memory in bytes of database pods  | Gauge    | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component                               |
| **planetscale\_pods\_container\_restarts\_total**  Total container restart events detected           | Counter  | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component, planetscale\_restart\_reason |

## Tag Glossary

* **cluster**: The PlanetScale cluster identifier
* **planetscale\_access**: The access path on bytes sent/received metrics (**public** for traffic over the public internet, or **private** for traffic over AWS PrivateLink or GCP Private Service Connect)
* **planetscale\_database\_branch\_id**: The unique identifier for the database branch
* **planetscale\_pod**: The Kubernetes pod name
* **planetscale\_container**: The container name (postgres, pgbouncer, walg-daemon)
* **planetscale\_role**: The database role (primary, replica)
* **planetscale\_cell**: The PlanetScale cell identifier
* **planetscale\_component**: The PlanetScale component identifier
* **planetscale\_connection\_state**: The state of database connections
* **planetscale\_lock\_mode**: The PostgreSQL lock mode
* **planetscale\_database\_name**: The PostgreSQL database name
* **planetscale\_port**: The connection port number
* **planetscale\_region**: The geographic region
* **planetscale\_pgbouncer\_pool**: The PgBouncer connection pool identifier
* **planetscale\_connector\_id**: The edge connector identifier
* **planetscale\_restart\_reason**: The reason for container restart

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
