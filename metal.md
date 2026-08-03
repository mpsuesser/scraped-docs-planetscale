---
url: https://planetscale.com/docs/metal
title: "Metal"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale Metal

> PlanetScale Metal databases are the same PlanetScale databases you know and love, powered by blazing-fast, locally-attached NVMe SSD drives instead of network-attached storage.

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

<Frame>
  <img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/metal/metal.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=29fca543db198da06b42f346f7e8f5ea" className="block dark:hidden" alt="Metal SSD" width="1944" height="622" data-path="metal/metal.png" />

  <img src="https://mintcdn.com/planetscale-2/g0AZZQkXmTSBYuKj/metal/metal-darkmode.png?fit=max&auto=format&n=g0AZZQkXmTSBYuKj&q=85&s=6adf3ee951d075f941d87f56f014e7c1" className="hidden dark:block" alt="Metal SSD" width="1942" height="622" data-path="metal/metal-darkmode.png" />
</Frame>

This translates to significant latency reduction, more consistent IO performance, and unlimited I/O Operations Per Second (IOPS).
Metal is an excellent choice for high-IOPS and other performance-critical workloads.
With Metal, your database now has the ability to use modern NVMe SSD technology to its full potential.

<Tip>
  Metal nodes for Postgres now [start at \$50/month](https://planetscale.com/blog/50-dollar-planetscale-metal-is-ga-for-postgres).
</Tip>

## Using a Metal database

PlanetScale databases come in two main flavors: **Metal** and **network-attached storage**.
When you create a database on PlanetScale, you can choose between these two options:

<Frame>
  <img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/metal/nas-and-metal.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=d5c52a5146457cd2bb41df0d411a3669" className="block dark:hidden" alt="Choose between network attached storage and Metal" width="2674" height="1428" data-path="metal/nas-and-metal.png" />

  <img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/metal/nas-and-metal-darkmode.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=fa699f1765175106fd5fb8308de3f3b6" className="hidden dark:block" alt="Choose between network attached storage and Metal" width="2674" height="1428" data-path="metal/nas-and-metal-darkmode.png" />
</Frame>

<Warning>
  The storage for Metal databases does not autoscale.
  It is important to keep a close eye on the storage capacity of Metal databases, and upgrade well before running out of space.
</Warning>

When you create a Metal database, you must choose a drive size up front.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/g0AZZQkXmTSBYuKj/metal/metal-drive-size.png?fit=max&auto=format&n=g0AZZQkXmTSBYuKj&q=85&s=4619ff56ac0b7927853673027c817f86" className="block dark:hidden" alt="Select storage drive size for a Metal database" width="3788" height="1944" data-path="metal/metal-drive-size.png" />

  <img src="https://mintcdn.com/planetscale-2/g0AZZQkXmTSBYuKj/metal/metal-drive-size-darkmode.png?fit=max&auto=format&n=g0AZZQkXmTSBYuKj&q=85&s=3d9f816558fdd0d2edb4a9b1a935ecce" className="hidden dark:block" alt="Select storage drive size for a Metal database" width="3790" height="1944" data-path="metal/metal-drive-size-darkmode.png" />
</Frame>

You should select a drive size that best suits your current data size, while also taking into account growth trends.
When the time comes that you need more storage, we make it easy to upgrade to larger NVMe drives with just a few clicks of a button.
You can learn more about creating and resizing Metal databases in our [creation and upgrade documentation](metal/create-a-metal-database.md).

## Monitoring your storage

Fixed-sized drives also means that you must closely monitor how much storage your database is using.
You can do so by looking at the storage information on the PlanetScale dashboard:

<Frame>
  <img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/metal/storage.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=83ff544d4bf373bbc2e1f31001cd6434" className="block dark:hidden" alt="Storage indicator in PlanetScale" width="1450" height="1290" data-path="metal/storage.png" />

  <img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/metal/storage-darkmode.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=810c6499b1c48ca25a206a6dd6fb118e" className="hidden dark:block" alt="Storage indicator in PlanetScale" width="1450" height="1290" data-path="metal/storage-darkmode.png" />
</Frame>

We will send you email notices when your database storage reaches the following thresholds: 60%, 75%, 85%, 90%, 95%.
We will also email you when we estimate that your storage will run out in 1 week and 24 hours, based on recent usage trends.

Reaching or getting close to a drive's max capacity is dangerous and can lead to failures.
It's important to closely monitor your database's disk usage in the dashboard and check your regular storage email notifications.
We have an additional safeguard in place to protect your data: When we detect that your Metal disk has 6GiB or less of available space, we automatically switch it to read-only mode until resized.
Ideally, you should resize to a larger drive long before reaching this point.

## Workload suitability

Using Metal is a clear win for many workloads.
Many of our current Metal customers have been able to either (A) save money, (B) increase performance, or (C) do both at the same time by switching to Metal.
The per-GB cost of metal storage is more affordable than network-attached storage with high IOPS capacity, and the improved IO performance allows you to use smaller compute instances in some cases.

There are some scenarios where a network-attached storage database may be a better choice.
Here, we provide some general suggestions to help you choose the ideal type of database for your needs.
If you'd like a more personalized assessment, please [reach out to support](https://planetscale.com/contact?initial=support) with the specifics of your workload.

Generally, these types of database workloads are ideal for Metal:

* If your workload has significant I/O demands, Metal is an ideal choice.
  A network-attached storage database has limitations on how quickly it can read and write data due to the additional network hops.
  Metal databases allow you to unlock the full potential of modern NVMe technology, providing ultra-high throughput.
* If you have experience running up against the limits of AWS EBS IOPS or have a large `gp3` or `io2` `EBS` volume bill.
  Metal provides unlimited IOPS and will likely yield performance improvement, cost savings, or both.
  There is no need to pay extra for access to the I/O throughput of the local drive.
* If low-latency database performance is critical to your business needs.
* If you are concerned about long-tail p99+ performance.

However, there are some scenarios where choosing network-attached storage may still be preferable:

* Very small databases that have both low compute and low storage requirements may be better suited for network-attached storage.
* Databases where the majority of active rows fit in RAM.
  In this case, the I/O demand on the storage is probably low, and you won't see as much of a performance boost by using Metal.
* If you frequently resize your database, Metal may not be the best option.
  Metal instance resizes generally take longer than a network-attached storage resize as they require copying data between drives.

## Metal Performance

We've mentioned several times that Metal can provide you better performance.
What does that look like in practice?
Let's look at two examples.

### PlanetScale Insights

Below is a screenshot showing the p50 and p95 response times for the database that was powering PlanetScale Insights as of Q4 2024.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/g0AZZQkXmTSBYuKj/metal/metal-insights-p50-p95.png?fit=max&auto=format&n=g0AZZQkXmTSBYuKj&q=85&s=72e13b6aa3194554e9df33b8c4f0784a" alt="The effect of Metal on the Insights database" width="2796" height="1906" data-path="metal/metal-insights-p50-p95.png" />
</Frame>

You can pretty clearly see when the database was switched over from network-attached storage instance types to Metal.
The p50 response times were cut in half, and the p95 had approximately a 7x improvement.

The workload was and continues to be very I/O bound.
The Insights database ingests a large amount of time-series data, and is frequently queried to pull the data that we use to generate graphs in Insights.
Metal provides a huge improvement for this type of workload.

### Large, sharded database

One of our existing customers runs several large, sharded databases.
We migrated these databases to Metal during the internal release of our product.
Below is a screenshot of the p99 latencies of a set of shards that we migrated from network-attached storage database to Metal instances.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/g0AZZQkXmTSBYuKj/metal/customer-p99.png?fit=max&auto=format&n=g0AZZQkXmTSBYuKj&q=85&s=a84d3817e70911f12f7efeb5f16d7fae" alt="The effect of Metal on a large sharded database" width="2956" height="1390" data-path="metal/customer-p99.png" />
</Frame>

Though their p99 response times were already very good, Metal was able to further cut it in half.

### Cost and performance

We migrated yet another large customer during our internal release.
After switching to Metal, they saw a significant improvement in API call latency for one of their critical APIs.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/g0AZZQkXmTSBYuKj/metal/customer-api-improvement.png?fit=max&auto=format&n=g0AZZQkXmTSBYuKj&q=85&s=6c4e45811787200857a4ec36facc8625" alt="API latency improvement" width="2854" height="1228" data-path="metal/customer-api-improvement.png" />
</Frame>

We can clearly see that starting on Dec 20, the long tail of latency was reduced significantly.
This is due to the lower latency and improved consistency of local NVMe disk performance.

This same customer also saw some significant cost savings with their move to Metal.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/g0AZZQkXmTSBYuKj/metal/customer-pricing-drop.png?fit=max&auto=format&n=g0AZZQkXmTSBYuKj&q=85&s=42a404dc0d886e8a93647798ed63e609" alt="Cost savings with Metal" width="2552" height="1728" data-path="metal/customer-pricing-drop.png" />
</Frame>

The performance of the database improved and the AWS costs to run dropped from over \$100 per day to \~\$30 per day.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
