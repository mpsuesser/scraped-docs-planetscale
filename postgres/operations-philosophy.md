---
url: https://planetscale.com/docs/postgres/operations-philosophy
title: "Operations Philosophy"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale Postgres operations philosophy

> PlanetScale has a high standard for uptime and availability of Postgres databases.

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

The foundation of achieving this goal in the cloud is our custom Kubernetes operator, the software that manages new database creation, version upgrades, resizes, failovers, and other operations in an inherently failure-prone environment. For more detail on how the operator orchestrates clusters, see the [Postgres architecture overview](postgres-architecture.md#custom-operator).

This document presents our architecture and philosophy on operating PlanetScale Postgres databases.
All customers should read it, as it also covers what can be expected during failovers, querying replicas vs the primary, and the tradeoffs of using direct vs pooled connections.

## Database cluster architecture

The cloud is an inherently failure-prone environment.
Servers can experience degraded performance or become unavailable at any time and for unknown reasons.
Because of this, we must treat all cloud server nodes as ephemeral.

We can embrace failure and build database clusters that are resilient in the cloud environment by understanding our constraints and leveraging the cloud's elasticity within known and practical limits.

Every production database cluster in PlanetScale has a primary and two replicas.
This allows us to embrace server failures through failovers.
Some consider failovers as a problem, but we see that as a fundamental building block for operating reliable databases.
Tolerating the brief disruptions produced by failovers has huge upside: PlanetScale can rapidly deliver changes which continuously increase the quality, reliability, and performance of customer databases.

<img src="https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/postgres-arch-diagram.png?fit=max&auto=format&n=NAfHErQ6-kE8SaMw&q=85&s=c20f53698428814045e9bc242956ffd9" alt="Primary and Replicas" width="1754" height="1200" data-path="postgres/postgres-arch-diagram.png" />

This is the most proven architecture for highly-available database clusters for several reasons:

1. Replicating data to multiple servers is crucial for data durability, especially in a cloud environment.
   All data is replicated 3 ways at minimum.
2. Each instance is in a distinct availability zone.
   This means we can be resilient to zone-wide incidents.
3. Replicas can receive read queries that can tolerate replication lag.
   This allows offloading work from the primary, reserving it for writes and critical reads.
4. In the event of an incident in the primary's availability zone, we can failover to a healthy replica in a different availability zone.
   Likewise, when performing a planned upgrade on the primary instance, we first promote a healthy replica to become primary before upgrading the old primary (now demoted to a replica)
   Failovers take on the order of seconds, not minutes or hours, getting you back online quickly.

Having a three-node configuration as the starting point for all clusters allows PlanetScale to operate these databases in ways many other providers cannot.
Failovers are used to replace primaries when they go down unexpectedly.
The same, proven cluster failover techniques are used for other changes like upgrading instances, version patches, and reconfiguration.
This means customers benefit from tried-and-true database management techniques to increase availability, and minimize (though not eliminate) downtime during these operations.

## Continual improvement

Most Postgres database platforms shy away from version updates and other incremental improvements due to their disruptive nature.
Many services require minutes or even hours of downtime to handle this.

The PlanetScale philosophy is different.
To give our customers the best possible Postgres experience, we believe that regular version bumps, bugfixes, and quality-of-life improvements are necessary.
Image upgrades are performed in cases of emergencies (such as patching security issues) or when initiated by the user via the PlanetScale application or API.

When these upgrades occur, they require node failovers, which lead to a short period of database unavailability (seconds).

## The ideal Postgres experience

PlanetScale provides a well-rounded, out-of-the-box Postgres experience while also giving users what they need to tune it to their liking.
This shows up in several aspects of the product:

* Each database is given custom default tunings depending on CPU, RAM, and disk characteristics.
  Users can adjust and carefully monitor the effects of their own changes via the Clusters and Insights pages.
* In addition, we tune `effective_io_concurrency` based on the IOPS values specific to your database.
  For example, each Metal node type has unique and very large IOPS capacity, so we customize it on a per-instance-type basis.
  But we also let you experiment with your own tunings.
* Users have lots of freedom to customize their Postgres roles via our UI, giving them fine-grained control over permissions.
* Both direct-to-Postgres and PgBouncer connections are available by default.
  Each has tradeoffs, and we let you choose what makes the most sense for your applications.

The aim is to set you up with a strong foundation, and give users the tools they need to tune for their use-cases.

## Minimizing the impact of configuration changes

PlanetScale takes the database unavailability produced by upgrades extremely seriously.
We monitor this downtime across the fleet, and make continual efforts to reduce its impact.
We apply context-appropriate, rather than a one-size fits all, upgrade strategies to ensure that a given upgrade takes no more time than absolutely necessary.

PlanetScale allows manual configuration of a number of parameters via the [clusters](cluster-configuration.md) page.
Each configuration change falls into one of two categories:

* Reloadable changes: The change can be applied with no need to restart Postgres.
  When applying these, there is zero downtime.
* Restart-required: Postgres requires the server be restarted for the change to take effect.
  When applying these, you can expect a brief period of server unavailability due to the restart.

In the latter case, what we do is more sophisticated than a simple server restart.
Because we have a primary and two replicas, we typically start these configuration changes by applying them to and restarting the replicas.
Once these are ready, we do a switchover from the old primary to one of the replicas with the upgraded configuration.
The config is then applied and the final instance restarted, now demoted to a replica.

<img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/config-change-restart.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=f4424ed370e5b727bc300b5bade335fe" alt="Config change with restart" width="3501" height="1169" data-path="postgres/config-change-restart.png" />

This process allows us to minimize end-user downtime.
There remains an unavoidable several-second window of unavailability.
In addition, all direct connections will be terminated, so your application should have retry logic to accommodate for this.
There are a few configuration options that are [required by Postgres](https://www.postgresql.org/docs/current/hot-standby.html#HOT-STANDBY-ADMIN) to be applied to the primary before replicas.
For these, you will notice a slightly longer unavailability period.

Normally a primary shutdown proceeds in a fast and orderly manner within a second or two, allowing the operator to promote a replica to primary.
In cases where the operator is not able to quickly and cleanly shutdown the primary due to unresponsive user queries or transactions, the operator will failover to a replica after a timeout of 30 seconds.

PgBouncer connections will persist through all config changes.

## Database cluster sizing

PlanetScale does not support autoscaling of CPU and RAM.
Instead, users are put in the driver's seat to choose the hardware the database runs on, and are given tools to closely monitor performance and adjust resources when needed.
There are two main ways to increase the compute capacity of a cluster: Adding replicas and resizing all nodes.

### Adding Replicas

Each production PlanetScale database (excluding single node) has 1 primary and 2 [replicas](scaling/replicas.md).
In this standard configuration, read traffic can be [routed to a replica](scaling/replicas.md#how-to-query-postgres-replicas) rather than relying on the primary for everything.
It's important not to over-stress your replicas, however.
If all three nodes in the cluster experience too much strain, failovers will be more disruptive, since the cluster will temporarily go from 3 nodes down to 2.
Reads from replicas must also be able to tolerate replication lag.

If you only need to increase the read capacity of the cluster, a good option can be to add 1 or several additional replicas.
This is done through the [Clusters](cluster-configuration.md) menu, and has no negative impact on the other nodes when being added.

### Resizing a cluster

Resizing a cluster leads to database connections being terminated.
Therefore, it's important for your application to have connection retry logic.

Consider the case where you need to upgrade from an `M-160` database cluster to an `M-320`, doubling the compute resources of each node.
After applying the upgrade, three new `M-320` nodes are created.
These are caught up with the primary through a combination of a backup restore and data replication.

<img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/cluster-resize-1.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=75487a1f038e75172bac84e1af3a424c" alt="Cluster Resize 1" width="2162" height="1187" data-path="postgres/cluster-resize-1.png" />

Once these new `M-320` replicas are sufficiently caught up, the operator transitions primaryship to one of the new `M-320` nodes.
After this, the old `M-160` replicas are decommissioned, using the new ones for all replica traffic.
During each node replacement, connections to the decommissioned node will be destroyed.
New connections will need to be made to re-establish with one of the new nodes.

<img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/cluster-resize-2.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=a9f1f7f07b884fa33ab45a8b2a587e22" alt="Cluster Resize 2" width="2181" height="932" data-path="postgres/cluster-resize-2.png" />

During the upgrade process, all database connections will be terminated.
Normally a primary promotion proceeds in a fast and orderly manner in less than 5 seconds.
In cases where the operator is not able to quickly and cleanly shutdown the primary due to unresponsive user queries or transactions, the operator will failover to a replica after a timeout of 30 seconds.

For all the steps leading up to the node replacement, we keep your existing `M-160` database cluster fully functional.
Only brief periods of unavailability are required to ensure we can smoothly replace each node.

Many ORMs have built-in connection retry logic to help with these scenarios.
It's likely your application already has such capabilities if you are using libraries like Rails ActiveRecord, Laravel Eloquent, and Drizzle.

## Unplanned failures

In a cloud-native environment, server failure is to be expected.
This is unavoidable, so we embrace the behavior and have proven failover systems in place to address issues as quickly as possible.
PlanetScale has executed millions of failovers across our Postgres and Vitess database clusters.
They are a well-exercised path and are proven to be an invaluable mechanism for database operations and handling node failures.

### Replicas

Replica health is important to keep your PlanetScale database cluster operating smoothly.
Replica failures are automatically detected and automatically replaced.
There will be a period of time where your cluster has only the primary and a single replica while the replacement replica is created and data restored to it.
This time varies from minutes to hours depending on database size and your instance type.

<img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/failed-replica.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=0ab7de4bfa00c15e925e8aff8fa48540" alt="Failed replica" width="1784" height="1008" data-path="postgres/failed-replica.png" />

Any connections routed to the failed replica will be terminated and need to be re-established.
When re-established, we will route the connections to the other available replica.

### Primaries

The PlanetScale operator will detect a primary failure within seconds and immediately begin the process of replacing the node.
The new primary is chosen based on which one is the most caught-up from the state of the primary.
All Postgres databases are run with semi-sync replication, meaning that at least one replica should have received all the writes that the primary had seen.
Once chosen, this replica is promoted to primary and a new node is brought up to replace it.

<img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/failed-primary.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=38b162ee4ab9e2d09c23199d99e3daed" alt="Failed primary" width="2717" height="1047" data-path="postgres/failed-primary.png" />

The primary failure, detection, and election process typically takes 10 seconds or less when all other components of the cluster are healthy.
During this time your primary database will be inaccessible and no connections can be made.
The time it takes for the new replica to catch-up to the primary varies from minutes to hours depending on database size and your instance type.

For detailed guidance on configuring your application for maximum availability during failovers, see the [connection resilience guide](connection-resilience.md).

## Image upgrades

We have fast iteration cycles at PlanetScale, always aiming to improve the features and capabilities of our customer's Postgres databases.
Postgres minor version updates, PgBouncer updates, and adding support for new extensions all require us to update the container images for your database.
When this happens, the Kubernetes pods that power your primary and replicas are updated.

<img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/image-upgrade.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=f2f03e225e63fc1d0606456053b21490" alt="Image upgrade" width="3153" height="1113" data-path="postgres/image-upgrade.png" />

As with other operations described earlier, this leads to terminated connections and a brief period of database inaccessibility.
Image upgrades are performed in cases of emergencies (such as patching security issues) or when initiated by the user via the PlanetScale application or API.

## Disk availability, scaling, and cost

For databases using network-attached storage, autoscaling is enabled by default.
This is to protect availability of your database.
Reaching 100% full on a storage volume leads to database downtime.

<img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/disk-autoscaling.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=9c69d1e0e590defb94d73d0633ea0aab" alt="Disk autoscaling" width="2000" height="1378" data-path="postgres/disk-autoscaling.png" />

Cloud providers like AWS and GCP limit how frequently network-attached disks can be resized.
In both cases, there is a multi-hour cooldown period between resizing operations.
Also, these volumes typically do not support shrinking.

When our disk auto-scaler is able to spread out disk scale-up sufficiently, no downtime is needed to scale the disks.
This is true in the vast majority of cases.

When data growth is rapid, the auto-scaler may need to complete a **surge resize** to support the writes.
In this case, PlanetScale creates brand new database nodes with larger network-attached storage volumes.
Once ready, the nodes in your cluster get replaced with these new ones to increase capacity.
If the surge autoscaler is able to complete the resize before your disk fills, downtime will be minimal for growing the disk (seconds).
If your disk fills before the new disks are ready, you will experience a longer period of downtime.

We make every effort to keep your network-attached storage disk from filling, but it's important for the database administrators to pay close attention to storage and take manual intervention when necessary.
