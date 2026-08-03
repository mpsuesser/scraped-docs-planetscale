---
url: https://planetscale.com/docs/plans/planetscale-skus
title: "Planetscale Skus"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Cluster sizes

> Here you'll find a complete list of all the database sizes available to you on PlanetScale.

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

<PlatformAvailability current="both" />

If your needs exceed what is available here, we can easily spin up additional sizes for you. Just [reach out](https://planetscale.com/contact) and let us know. We also support [sharding](https://planetscale.com/sharding) on Vitess clusters.

## Network-attached storage

**Network-attached storage** databases come with autoscaling storage and have varying levels of compute power.

|             | **Processor** | **Memory** |
| :---------- | :------------ | :--------- |
| **PS-10**   | 1/8 vCPU      | 1 GB RAM   |
| **PS-20**   | 1/4 vCPU      | 2 GB RAM   |
| **PS-40**   | 1/2 vCPU      | 4 GB RAM   |
| **PS-80**   | 1 vCPU        | 8 GB RAM   |
| **PS-160**  | 2 vCPUs       | 16 GB RAM  |
| **PS-320**  | 4 vCPUs       | 32 GB RAM  |
| **PS-400**  | 8 vCPUs       | 32 GB RAM  |
| **PS-640**  | 8 vCPUs       | 64 GB RAM  |
| **PS-700**  | 16 vCPUs      | 32 GB RAM  |
| **PS-900**  | 16 vCPUs      | 64 GB RAM  |
| **PS-1280** | 16 vCPUs      | 128 GB RAM |
| **PS-1400** | 32 vCPUs      | 64 GB RAM  |
| **PS-1800** | 32 vCPUs      | 128 GB RAM |
| **PS-2100** | 48 vCPUs      | 96 GB RAM  |
| **PS-2560** | 32 vCPUs      | 256 GB RAM |
| **PS-2700** | 48 vCPUs      | 128 GB RAM |
| **PS-2800** | 64 vCPUs      | 128 GB RAM |

## Metal

[Metal databases](../metal.md) are backed by locally-attached NVMe drives for storage, unlocking incredible performance and cost-efficiencies. Because the drives are locally-attached, you need to choose both your compute and storage resources when you create your database.
The storage options vary by cloud provided, so we break out the options into AWS and GCP sections.

### Metal options on AWS

|            | **Processor** | **Memory** | **NVMe Storage options** | **Notes**     |
| :--------- | :------------ | :--------- | :----------------------- | ------------- |
| **M-10**   | 1/8 vCPU      | 1 GB RAM   | configurable             | Postgres only |
| **M-20**   | 1/4 vCPU      | 2 GB RAM   | configurable             | Postgres only |
| **M-40**   | 1/2 vCPU      | 4 GB RAM   | configurable             | Postgres only |
| **M-80**   | 1 vCPU        | 8 GB RAM   | configurable             | Postgres only |
| **M-160**  | 2 vCPUs       | 16 GB RAM  | configurable             |               |
| **M-320**  | 4 vCPUs       | 32 GB RAM  | 229 GB                   |               |
| **M-320**  | 4 vCPUs       | 32 GB RAM  | 929 GB                   |               |
| **M-320**  | 4 vCPUs       | 32 GB RAM  | 2,490 GB                 |               |
| **M-640**  | 8 vCPUs       | 64 GB RAM  | 466 GB                   |               |
| **M-640**  | 8 vCPUs       | 64 GB RAM  | 1,866 GB                 |               |
| **M-640**  | 8 vCPUs       | 64 GB RAM  | 4,992 GB                 |               |
| **M-1280** | 16 vCPUs      | 128 GB RAM | 942 GB                   |               |
| **M-1280** | 16 vCPUs      | 128 GB RAM | 3,739 GB                 |               |
| **M-2560** | 32 vCPUs      | 256 GB RAM | 1,891 GB                 |               |
| **M-2560** | 32 vCPUs      | 256 GB RAM | 7,492 GB                 |               |

### Metal options on GCP

|            | **Processor** | **Memory** | **NVMe Storage options** |
| :--------- | :------------ | :--------- | :----------------------- |
| **M-160**  | 2 vCPUs       | 16 GB RAM  | 367 GB                   |
| **M-160**  | 2 vCPUs       | 16 GB RAM  | 742 GB                   |
| **M-160**  | 2 vCPUs       | 16 GB RAM  | 1,492 GB                 |
| **M-160**  | 2 vCPUs       | 16 GB RAM  | 2,992 GB                 |
| **M-160**  | 2 vCPUs       | 16 GB RAM  | 5,992 GB                 |
| **M-160**  | 2 vCPUs       | 16 GB RAM  | 8,992 GB                 |
| **M-320**  | 4 vCPUs       | 32 GB RAM  | 367 GB                   |
| **M-320**  | 4 vCPUs       | 32 GB RAM  | 742 GB                   |
| **M-320**  | 4 vCPUs       | 32 GB RAM  | 1,492 GB                 |
| **M-320**  | 4 vCPUs       | 32 GB RAM  | 2,992 GB                 |
| **M-320**  | 4 vCPUs       | 32 GB RAM  | 5,992 GB                 |
| **M-320**  | 4 vCPUs       | 32 GB RAM  | 8,992 GB                 |
| **M-640**  | 8 vCPUs       | 64 GB RAM  | 367 GB                   |
| **M-640**  | 8 vCPUs       | 64 GB RAM  | 742 GB                   |
| **M-640**  | 8 vCPUs       | 64 GB RAM  | 1,492 GB                 |
| **M-640**  | 8 vCPUs       | 64 GB RAM  | 2,992 GB                 |
| **M-640**  | 8 vCPUs       | 64 GB RAM  | 5,992 GB                 |
| **M-640**  | 8 vCPUs       | 64 GB RAM  | 8,992 GB                 |
| **M-1280** | 16 vCPUs      | 128 GB RAM | 742 GB                   |
| **M-1280** | 16 vCPUs      | 128 GB RAM | 1,492 GB                 |
| **M-1280** | 16 vCPUs      | 128 GB RAM | 2,992 GB                 |
| **M-1280** | 16 vCPUs      | 128 GB RAM | 5,992 GB                 |
| **M-1280** | 16 vCPUs      | 128 GB RAM | 8,992 GB                 |
| **M-2560** | 32 vCPUs      | 256 GB RAM | 1,492 GB                 |
| **M-2560** | 32 vCPUs      | 256 GB RAM | 2,992 GB                 |
| **M-2560** | 32 vCPUs      | 256 GB RAM | 5,992 GB                 |
| **M-2560** | 32 vCPUs      | 256 GB RAM | 8,992 GB                 |

## Selecting a cluster size

Selecting the correct cluster size for your database can have a dramatic impact on how it performs and how much it costs.

A good rule of thumb is when you notice CPU usage is consistently at or close to 100% for an extended period of time, you may benefit from [upsizing your cluster](#upsizing-and-downsizing-clusters). Conversely, if your CPU usage is consistently below 50%, you may be able to downsize. You can monitor your CPU usage by clicking on your database, clicking "Primary" in your architecture diagram, and referencing the chart under "Metrics and performance".

There are also special cases where you may want to temporarily upsize out of caution if you're anticipating a large spike in traffic, such as during a launch or event. In these cases, you can easily [upsize](#upsizing-and-downsizing-clusters) ahead of your event, and then downsize after. Changing cluster sizes is a seamless operation that requires no downtime.

If you are migrating from an existing cloud provider with resource-based pricing, be sure to compare your currently selected instance with our available cluster sizes.

Keep in mind, each database comes with a production branch with two replicas, as well as 1,440 hours worth of development branches (for Vitess databases). The development branches essentially equate to two extra "always on" databases. In many cases, you can deprecate your dev/staging databases that you pay extra for with other providers in favor of the development branches. In the end, this usually results in significant cost savings.

Databases in PlanetScale also come with additional beneficial infrastructure that is not easily configured or available in other hosted database solutions. For more information on what is provisioned with each database, read our [Vitess architecture](../vitess/architecture.md) and [Postgres architecture](../postgres/postgres-architecture.md) docs.

If you are unsure which plan or cluster size is right for your application, [contact us](https://planetscale.com/contact) to get further assistance.

Our self-serve plans are flexible enough to handle the majority of customers. However, there are several use cases where you may need a more custom plan. This is where our Enterprise offerings shine.

## Upsizing and downsizing clusters

As your application scales, upgrading or downgrading your cluster is a seamless operation that does not involve any downtime.

To change cluster sizes, go to your PlanetScale dashboard, click on your database, click the gear icon that specifies your current cluster size, select the new cluster size, and click "Update".

The time it takes to change sizes depends on the size and region of your database. Larger databases may take 20 minutes to upsize/downsize. However, this is all done online, so you will not experience any downtime. Keep in mind, once you update your cluster size, you cannot change sizes again until the first size change completes.

When you choose to change cluster size, we upgrade each of your replicas one by one: delete the tablet container, create a new tablet container of the new size, attach the persistent volume, start it up, and connect it to the primary. Once that's complete, we fail the primary over to one of those new replicas, and do the same thing to the old primary.

## Transaction pools (Vitess)

Each Vitess keyspace (logical database) has a predefined limit on the number of simultaneous transactions it supports.
This is also known as the *transaction pool*.
These limits are put in place as a protection mechanism for each keyspace.
The limits for each size are shown in the table below:

|                            | **Transaction Pool\*** |
| :------------------------- | :--------------------- |
| **PS-10** and **M-10**     | 70                     |
| **PS-20** and **M-20**     | 75                     |
| **PS-40** and **M-40**     | 75                     |
| **PS-80** and **M-80**     | 110                    |
| **PS-160** and **M-160**   | 158                    |
| **PS-320** and **M-320**   | 211                    |
| **PS-400**                 | 211                    |
| **PS-640** and **M-640**   | 281                    |
| **PS-700**                 | 211                    |
| **PS-900**                 | 281                    |
| **PS-1280** and **M-1280** | 375                    |
| **PS-1400**                | 281                    |
| **PS-1800**                | 375                    |
| **PS-2100**                | 328                    |
| **PS-2560** and **M-2560** | 500                    |
| **PS-2700**                | 438                    |
| **PS-2800**                | 375                    |

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
