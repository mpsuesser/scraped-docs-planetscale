---
url: https://planetscale.com/docs/vitess/scaling/vtgates
title: "Vtgates"
description: ""
access_date: 2026-08-05T18:42:40.123Z
current_date: 2026-08-05T18:42:40.123Z
---

A VTGate, or Vitess Gateway, is the layer of Vitess that acts as a proxy between your application servers and the MySQL instances.

When an application needs to connect to a Vitess cluster, it does not make connections directly to MySQL. Instead, connections are made to our [Global Edge Network](https://planetscale.com/blog/introducing-global-replica-credentials#building-planetscale-global-network), which then routes them to a [VTGate](https://vitess.io/docs/concepts/vtgate/). The VTGate layer acts as the entry point to the cluster, proxies connections, and handles routing of incoming queries to the appropriate [keyspace](../sharding/keyspaces.md) and/or [shard](../sharding.md).

## Managing your VTGate layer

From the Clusters page:

- Adjust the size of your VTGates, e.g. `VTG-5` to `VTG-10`
- Adjust the number of VTGates your cluster has (only configurable for `VTG-320` and larger). By default, 1 VTGate is deployed per availability zone (for a total of 3 VTGates). You can adjust this up to 24 VTGates per AZ, for a total of 72 VTGates.
- Turn on VTGate autoscaling (for `VTG-320` and larger)
- View your VTGate CPU and memory utilization for the past hour up through the past week
- View any past changes to your VTGates

![VTGate page in PlanetScale dashboard](https://mintcdn.com/planetscale-2/g0AZZQkXmTSBYuKj/images/vtgates/vtgates-panel.png?w=2500&fit=max&auto=format&n=g0AZZQkXmTSBYuKj&q=85&s=0b9e35fa3fbfa512bea01279ec8a914a)

VTGate page in PlanetScale dashboard

![VTGate autoscaling configuration](https://mintcdn.com/planetscale-2/g0AZZQkXmTSBYuKj/images/vtgates/vtgates-panel-darkmode.png?w=2500&fit=max&auto=format&n=g0AZZQkXmTSBYuKj&q=85&s=db4bcdf5d5e51ce731c4b89bf7c03a2d)

VTGate autoscaling configuration

## Adjusting the size and number of VTGates

To modify the size or number of VTGates, click on your database in the dashboard, “Clusters” in the sidebar, choose the branch you want to modify from the dropdown, and then click on the “VTGates” tab at the top.

From here, you can see the current VTGate utilization, adjust your VTGate size, and modify the number of VTGates per availability zone (for `VTG-320` and larger). Note the price difference on this page after you adjust. This will be added to your monthly bill going forward. When you’re satisfied, click “Save changes”. It will take some time to update the VTGate configuration. This is an online operation and does not cause downtime.

### Configuring VTGates with the CLI

You can also view and resize VTGates with the [PlanetScale CLI](../../cli/branch.md). Only production branches can be resized.

```shellscript
# Show the current VTGate configuration
pscale branch vtgate show <DATABASE_NAME> <BRANCH_NAME>

# Queue a VTGate resize
pscale branch vtgate resize <DATABASE_NAME> <BRANCH_NAME> \
  --vtgate-size VTG_320 \
  --vtgate-autoscaling \
  --vtgate-count 2 \
  --vtgate-max-count 8 \
  --vtgate-target-cpu-utilization 50

# Check on or cancel a resize request
pscale branch vtgate resize status <DATABASE_NAME> <BRANCH_NAME>
pscale branch vtgate resize cancel <DATABASE_NAME> <BRANCH_NAME>
```

CLI size SKUs use underscores (for example `VTG_320`). Hyphens are accepted and converted. See the [`branch` command reference](../../cli/branch.md) for all flags.

## VTGate default sizes

By default, every PlanetScale cluster comes with three VTGates, each in a different availability zone.

The default size of your VTGates depends on the size of your cluster’s largest keyspace:

| Cluster size | Default VTGate size |
| --- | --- |
| `PS-10` | `VTG-5` |
| `PS-20` | `VTG-5` |
| `PS-40` | `VTG-5` |
| `PS-80` | `VTG-5` |
| `PS-160` | `VTG-10` |
| `PS-320` | `VTG-20` |
| `PS-400` | `VTG-40` |
| `PS-640` | `VTG-40` |
| `PS-700` | `VTG-80` |
| `PS-900` | `VTG-80` |
| `PS-1280` | `VTG-80` |
| `PS-1400+` | `VTG-320` |

PlanetScale will automatically adjust your VTGate size whenever you resize your cluster. For example, if you resize your cluster from `PS-10` to `PS-160`, your VTGates will automatically resize from `VTG-5` to `VTG-10`.

If you have made any modifications to the default VTGate configuration, then PlanetScale will not automatically resize your VTGates during a cluster size change.

## VTGate credits and pricing

Every PlanetScale cluster includes VTGate credits that cover the cost of a VTGate configuration that will work well for most workloads. These credits automatically scale with your cluster size and complexity:

- Larger cluster sizes include more VTGate credits
- Additional keyspaces increase your VTGate credits
- Additional shards increase your VTGate credits

If your specific workload requires more powerful VTGates, you can upgrade them manually through the dashboard or [CLI](../../cli/branch.md). Any VTGate usage that exceeds your included credits will be billed as an additional charge.

Here are some examples of how VTGate credits work:

**Example: Single keyspace**

- Cluster: One unsharded `PS-10` (1/8 vCPU, 1GB memory)
- Included credits cover: `VTG-5` VTGates (1/16 vCPU, 128MB memory each)

**Example: Multiple keyspaces**

- Cluster: Two unsharded `PS-10` clusters across two keyspaces
- The additional keyspace doubles your VTGate credit allowance

**Example: Sharded cluster**

- Cluster: Two keyspaces
	- One unsharded `PS-10` cluster
		- One sharded keyspace with two `PS-10` shards
- Credits are tripled due to the additional keyspace and shards

## Autoscaling VTGates

You have the option to automatically scale the number of your VTGates based on VTGate CPU utilization.

To turn on autoscaling, go to the Clusters page, click “VTGates”, click the “Autoscaling” tab, and check the “Use horizontal autoscaling” box. You can choose from 40%, 50%, 60%, and 70% utilization.

When the average CPU utilization of your VTGates exceeds the chosen threshold, the system will automatically increase the number of VTGates to accommodate. When CPU utilization falls below this target, it will scale in — downsizing the number of VTGates.

VTGates in each availability zone will scale independently based on their individual utilization. This means that if one zone experiences higher load, it may scale up while others remain at their current number.

| VTGate size | Maximum number of VTGates per availability zone |
| --- | --- |
| `VTG-320` | 16 |
| `VTG-640` | 16 |
| `VTG-1280` | 128 |

You will be billed for the actual VTGate usage during autoscaling periods. If your VTGates frequently scale up to handle load, this can result in significant additional costs beyond your included VTGate credits.

## When to increase the number of VTGates

There are several indicators that your VTGates may need more resources:

### High resource utilization

Monitor your VTGate CPU and memory usage in the PlanetScale dashboard. If you consistently see:

- CPU utilization above 70%
- Memory utilization above 80%

This indicates your VTGates are under heavy load and you should consider either increasing the VTGate size or adding more VTGates to your cluster.

### Connection errors

If you encounter either of these errors, it typically means your VTGates are overwhelmed and unable to handle incoming connections:

```text
ERROR HY000 (1105): unavailable: vtgate connection error: no endpoints, after 1 attempts
```

```text
Error 1105 (HY000): unavailable: vtgate connection error: no healthy endpoints, after 1 attempts
```

These errors occur when VTGates are resource-constrained and cannot accept new connections. To resolve this:

1. First, check your VTGate CPU and memory metrics
2. Consider upgrading to a larger VTGate size
3. For `VTG-320` and larger, you can also increase the number of VTGates per availability zone
4. Enable autoscaling if your workload has variable load patterns

## VTGate Resources

Each VTGate size has varying levels of compute power and memory provisioned. Because an individual VTGate is run in every availability zone, your total amount of available resources is three times larger than the resources allocated to each instance.

|  | **Processor** | **Memory** |
| --- | --- | --- |
| **VTG-5** | 1/16 vCPU | 128 MB RAM |
| **VTG-10** | 1/8 vCPU | 256 MB RAM |
| **VTG-20** | 1/4 vCPU | 512 MB RAM |
| **VTG-40** | 1/2 vCPU | 1 GB RAM |
| **VTG-80** | 1 vCPU | 2 GB RAM |
| **VTG-320** | 4 vCPU | 8 GB RAM |
| **VTG-640** | 8 vCPU | 16 GB RAM |
| **VTG-1280** | 16 vCPU | 32 GB RAM |

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
