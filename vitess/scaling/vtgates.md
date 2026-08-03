---
url: https://planetscale.com/docs/vitess/scaling/vtgates
title: "Vtgates"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# VTGates

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

<PlatformAvailability current="vitess" />

A VTGate, or Vitess Gateway, is the layer of Vitess that acts as a proxy between your application servers and the MySQL instances.

When an application needs to connect to a Vitess cluster, it does not make connections directly to MySQL. Instead, connections are made to our [Global Edge Network](https://planetscale.com/blog/introducing-global-replica-credentials#building-planetscale-global-network), which then routes them to a [VTGate](https://vitess.io/docs/concepts/vtgate/). The VTGate layer acts as the entry point to the cluster, proxies connections, and handles routing of incoming queries to the appropriate [keyspace](../sharding/keyspaces.md) and/or [shard](../sharding.md).

## Managing your VTGate layer

From the Clusters page:

* Adjust the size of your VTGates, e.g. `VTG-5` to `VTG-10`
* Adjust the number of VTGates your cluster has (only configurable for `VTG-320` and larger). By default, 1 VTGate is deployed per availability zone (for a total of 3 VTGates). You can adjust this up to 24 VTGates per AZ, for a total of 72 VTGates.
* Turn on VTGate autoscaling (for `VTG-320` and larger)
* View your VTGate CPU and memory utilization for the past hour up through the past week
* View any past changes to your VTGates

<Frame>
  <img src="https://mintcdn.com/planetscale-2/g0AZZQkXmTSBYuKj/images/vtgates/vtgates-panel.png?fit=max&auto=format&n=g0AZZQkXmTSBYuKj&q=85&s=b27702d0cc3e50403d226b468fe5b07d" alt="VTGate page in PlanetScale dashboard" className="block dark:hidden" width="2822" height="1682" data-path="images/vtgates/vtgates-panel.png" />

  <img src="https://mintcdn.com/planetscale-2/g0AZZQkXmTSBYuKj/images/vtgates/vtgates-panel-darkmode.png?fit=max&auto=format&n=g0AZZQkXmTSBYuKj&q=85&s=7ca8faf33680e5eecb25fa2977100ae8" alt="VTGate autoscaling configuration" className="hidden dark:block" width="2820" height="1686" data-path="images/vtgates/vtgates-panel-darkmode.png" />
</Frame>

## Adjusting the size and number of VTGates

To modify the size or number of VTGates, click on your database in the dashboard, "Clusters" in the sidebar, choose the branch you want to modify from the dropdown, and then click on the "VTGates" tab at the top.

From here, you can see the current VTGate utilization, adjust your VTGate size, and modify the number of VTGates per availability zone (for `VTG-320` and larger). Note the price difference on this page after you adjust. This will be added to your monthly bill going forward. When you're satisfied, click "Save changes". It will take some time to update the VTGate configuration. This is an online operation and does not cause downtime.

## VTGate default sizes

By default, every PlanetScale cluster comes with three VTGates, each in a different availability zone.

The default size of your VTGates depends on the size of your cluster's largest keyspace:

| Cluster size | Default VTGate size |
| ------------ | ------------------- |
| `PS-10`      | `VTG-5`             |
| `PS-20`      | `VTG-5`             |
| `PS-40`      | `VTG-5`             |
| `PS-80`      | `VTG-5`             |
| `PS-160`     | `VTG-10`            |
| `PS-320`     | `VTG-20`            |
| `PS-400`     | `VTG-40`            |
| `PS-640`     | `VTG-40`            |
| `PS-700`     | `VTG-80`            |
| `PS-900`     | `VTG-80`            |
| `PS-1280`    | `VTG-80`            |
| `PS-1400+`   | `VTG-320`           |

PlanetScale will automatically adjust your VTGate size whenever you resize your cluster. For example, if you resize your cluster from `PS-10` to `PS-160`, your VTGates will automatically resize from `VTG-5` to `VTG-10`.

If you have made any modifications to the default VTGate configuration, then PlanetScale will not automatically resize your VTGates during a cluster size change.

## VTGate credits and pricing

Every PlanetScale cluster includes VTGate credits that cover the cost of a VTGate configuration that will work well for most workloads. These credits automatically scale with your cluster size and complexity:

* Larger cluster sizes include more VTGate credits
* Additional keyspaces increase your VTGate credits
* Additional shards increase your VTGate credits

If your specific workload requires more powerful VTGates, you can upgrade them manually through the dashboard. Any VTGate usage that exceeds your included credits will be billed as an additional charge.

Here are some examples of how VTGate credits work:

**Example: Single keyspace**

* Cluster: One unsharded `PS-10` (1/8 vCPU, 1GB memory)
* Included credits cover: `VTG-5` VTGates (1/16 vCPU, 128MB memory each)

**Example: Multiple keyspaces**

* Cluster: Two unsharded `PS-10` clusters across two keyspaces
* The additional keyspace doubles your VTGate credit allowance

**Example: Sharded cluster**

* Cluster: Two keyspaces
  * One unsharded `PS-10` cluster
  * One sharded keyspace with two `PS-10` shards
* Credits are tripled due to the additional keyspace and shards

## Autoscaling VTGates

You have the option to automatically scale the number of your VTGates based on VTGate CPU utilization.

To turn on autoscaling, go to the Clusters page, click "VTGates", click the "Autoscaling" tab, and check the "Use horizontal autoscaling" box. You can choose from 40%, 50%, 60%, and 70% utilization.

When the average CPU utilization of your VTGates exceeds the chosen threshold, the system will automatically increase the number of VTGates to accommodate. When CPU utilization falls below this target, it will scale in — downsizing the number of VTGates.

VTGates in each availability zone will scale independently based on their individual utilization. This means that if one zone experiences higher load, it may scale up while others remain at their current number.

| VTGate size | Maximum number of VTGates per availability zone |
| ----------- | ----------------------------------------------- |
| `VTG-320`   | 16                                              |
| `VTG-640`   | 16                                              |
| `VTG-1280`  | 128                                             |

<Note>
  You will be billed for the actual VTGate usage during autoscaling periods. If your VTGates frequently scale up to handle load, this can result in significant additional costs beyond your included VTGate credits.
</Note>

## When to increase the number of VTGates

There are several indicators that your VTGates may need more resources:

### High resource utilization

Monitor your VTGate CPU and memory usage in the PlanetScale dashboard. If you consistently see:

* CPU utilization above 70%
* Memory utilization above 80%

This indicates your VTGates are under heavy load and you should consider either increasing the VTGate size or adding more VTGates to your cluster.

### Connection errors

If you encounter either of these errors, it typically means your VTGates are overwhelmed and unable to handle incoming connections:

```
ERROR HY000 (1105): unavailable: vtgate connection error: no endpoints, after 1 attempts
```

```
Error 1105 (HY000): unavailable: vtgate connection error: no healthy endpoints, after 1 attempts
```

These errors occur when VTGates are resource-constrained and cannot accept new connections. To resolve this:

1. First, check your VTGate CPU and memory metrics
2. Consider upgrading to a larger VTGate size
3. For `VTG-320` and larger, you can also increase the number of VTGates per availability zone
4. Enable autoscaling if your workload has variable load patterns

## VTGate Resources

Each VTGate size has varying levels of compute power and memory provisioned. Because an individual VTGate is run in every availability zone, your total amount of available resources is three times larger than the resources allocated to each instance.

|              | **Processor** | **Memory** |
| ------------ | ------------- | ---------- |
| **VTG-5**    | 1/16 vCPU     | 128 MB RAM |
| **VTG-10**   | 1/8 vCPU      | 256 MB RAM |
| **VTG-20**   | 1/4 vCPU      | 512 MB RAM |
| **VTG-40**   | 1/2 vCPU      | 1 GB RAM   |
| **VTG-80**   | 1 vCPU        | 2 GB RAM   |
| **VTG-320**  | 4 vCPU        | 8 GB RAM   |
| **VTG-640**  | 8 vCPU        | 16 GB RAM  |
| **VTG-1280** | 16 vCPU       | 32 GB RAM  |

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
