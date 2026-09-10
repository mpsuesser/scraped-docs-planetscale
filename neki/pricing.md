---
url: https://planetscale.com/docs/neki/pricing
title: "Pricing"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

Each Neki branch has its own database infrastructure. Its estimated monthly price depends on the Postgres clusters provisioned for its shards, its router compute, and its storage, backup, and network usage.

The PlanetScale dashboard shows the rates available to your organization. SKU prices vary by cloud provider, region, and your organization’s commercial terms. Use the dashboard estimate or the [pricing calculator](https://planetscale.com/pricing) for the current rate of a specific size. Do not assume a us-east-1 example applies everywhere.

## pricing.md

For an agent-friendly catalog of live cluster SKU rates and example SQL, fetch `https://planetscale.com/pricing.md`.

## What appears on the bill

| Charge | What you pay for |
| --- | --- |
| **Shard compute** | The selected cluster SKU, applied once per shard |
| **Additional replicas** | Additional replicas added to each shard |
| **Router compute** | The selected router SKU and number of routers |
| **Network-attached storage** | Allocated disk, plus IOPS and throughput above the included baselines |
| **Backup storage** | Compressed backup and WAL data above the included allowance |
| **Network transfer** | Public egress and private traffic above the included allowance |

A configuration profile has no compute cost by itself. Adding a shard adds another copy of that profile’s cluster SKU to the bill. Routers are not multiplied by shard count.

There are no upfront commitments. Clusters are billed based on the selected instance size and prorated to the millisecond. Router compute is billed based on the selected router size and number of routers and prorated to the millisecond.

## Cluster SKU rates

Every shard assigned to a configuration profile uses that profile’s cluster size. Neki does not offer a single-node configuration. The highly available SKU rate covers **one primary and two replicas**.

```text
shard compute = highly available SKU rate × shards on the profile
```

The size menu in **Clusters** shows the monthly rate, additional-replica rate, vCPU, and memory for each option. Metal SKUs include local NVMe storage in that rate. Network-attached storage SKUs bill network-attached storage separately.

If four shards share one profile, you pay four times that profile’s highly available SKU rate.

See [Cluster sizing](cluster-configuration/cluster-sizing.md).

## Additional replicas

You can add replicas above the two included with the SKU. You cannot set the replica count to anything lower than `2`. Extra replicas use the SKU’s additional-replica rate, which is about one third of the highly available rate.

```text
additional replica compute = additional-replica rate × extra replicas × shards on the profile
```

Each extra network-attached storage replica also gets its own network-attached disk. Those volumes do not receive the 10 GB included allowance; you pay for the full allocated size. See [Database replicas](replicas.md).

## Router SKU rates

Every Neki branch has routers, which use a separate SKU from shard compute. A production branch starts with one router in each availability zone. Upsizing shards does not change the router size.

The monthly production rate shown for a router size covers the three-router baseline: one router in each of three availability zones (AZ).

For a fixed production router configuration with the same replica count in every AZ:

```text
router compute = router-size monthly rate × replicas per AZ
```

When AZs do not have the same number of running routers, production billing uses the observed total:

```text
router compute = router-size monthly rate × observed running routers ÷ 3
```

A development branch uses one AZ, so its development router rate applies to each running router without the three-AZ divisor. Autoscaling and size changes are prorated to the millisecond.

## Storage

We use GB as **[gibibytes, otherwise known as binary gigabytes](https://simple.wikipedia.org/wiki/Gibibyte)**, to calculate storage and usage limits. For reference, 1 binary gigabyte is equivalent to 2^30 bytes.

### Storage types explained

PlanetScale offers two storage options with different pricing models:

| [PlanetScale Metal](../metal.md) | [Network-attached storage](cluster-configuration.md#configure-profile-storage) |
| --- | --- |
| **All-inclusive pricing** | **Configuration-based pricing** |
| Storage is included in the cluster SKU. High-performance NVMe SSDs are attached directly to each server. Choose a larger cluster size to get more storage. Best for high-performance applications that need predictable costs. | The first 10 GB is included on each primary and included replica volume. Additional storage is billed separately from compute. Add storage without changing the cluster size, with optional automatic disk scaling. Best for applications with variable storage needs or cost optimization. |

### Metal

Local NVMe capacity is included in the Metal SKU rate. To get more local storage, choose a larger Metal size.

### Network-attached storage

Network-attached storage clusters bill allocated disk separately from compute. Neki measures capacity per shard and attributes it to that shard’s configuration profile. You pay for current allocated capacity, not the configured minimum.

AWS gp3 volumes include 10 GB, 3,000 IOPS, and 125 MiB/s per volume on the primary and the two replicas that come with the SKU. Charges apply only to capacity and provisioned IOPS or throughput above those baselines.

GCP uses Hyperdisk Balanced. IOPS and throughput scale with disk size at no separate charge; you pay for allocated disk.

| Usage | Included per volume | Additional monthly rate (AWS us-east-1) |
| --- | --- | --- |
| Primary and included replica volumes | First 10 GB | $0.125 per GB |
| Additional replica volumes | None | $0.125 per GB |
| Provisioned IOPS | First 3,000 IOPS | $0.009 per IOPS |
| Provisioned throughput | First 125 MiB/s | $0.073 per MiB/s |

The configured IOPS and throughput apply to each network volume on every shard in the profile. Regional rates are below.

Configure minimum disk, autoscaling, IOPS, and bandwidth from the profile **Storage** tab.

### AWS storage rates

| Region | Storage (per GB/month) | Additional IOPS (per IOPS/month) | Additional throughput (per MiB/s/month) |
| --- | --- | --- | --- |
| ap-northeast-1 (Tokyo) | $0.150 | $0.011 | $0.088 |
| ap-south-1 (Mumbai) | $0.143 | $0.103 | $0.084 |
| ap-southeast-1 (Singapore) | $0.150 | $0.011 | $0.088 |
| ap-southeast-2 (Sydney) | $0.150 | $0.011 | $0.088 |
| ca-central-1 (Montreal) | $0.138 | $0.010 | $0.081 |
| eu-central-1 (Frankfurt) | $0.149 | $0.011 | $0.088 |
| eu-west-1 (Dublin) | $0.138 | $0.010 | $0.081 |
| eu-west-2 (London) | $0.145 | $0.011 | $0.084 |
| sa-east-1 (Sao Paulo) | $0.238 | $0.018 | $0.139 |
| us-east-1 (N. Virginia) | $0.125 | $0.009 | $0.073 |
| us-east-2 (Ohio) | $0.125 | $0.009 | $0.073 |
| us-west-2 (Oregon) | $0.125 | $0.009 | $0.073 |

### GCP storage rates

| Region | Storage (per GB/month) |
| --- | --- |
| asia-northeast3 (Seoul) | $0.312 |
| europe-west1 (Belgium) | $0.24 |
| europe-west4 (Netherlands) | $0.264 |
| northamerica-northeast1 (Montréal) | $0.264 |
| us-central1 (Iowa) | $0.24 |
| us-east1 (South Carolina) | $0.24 |
| us-east4 (Virginia) | $0.264 |

## Backups

Each branch includes backup storage equal to **twice the allocated disk** on that branch. Required schedules (every 12 hours, two-day retention) use this allowance.

Compressed backup data and WAL above the included amount are billed at **$0.023 per GB per month**.

A protected backup that outlives the default two-day window continues to contribute storage until you turn protection off or delete it. See [Back up and restore](backups.md).

## Network transfer

Replication between a shard’s primary and its replicas, and backup traffic, are not billed as network transfer.

### Public traffic

Outgoing public traffic (egress) is billed. Public ingress is free.

| Branch type | Included public egress |
| --- | --- |
| Production | 100 GB per month |
| Development | 10 GB per month |

### Private traffic

AWS PrivateLink and GCP Private Service Connect traffic — ingress and egress — is billed at **$0.01 per GB**, regardless of region. Each branch includes the same allowance as public traffic: 100 GB for production, 10 GB for development.

### Public egress rates

| Cloud | Region | Egress (per GB) |
| --- | --- | --- |
| AWS | ap-northeast-1 (Tokyo) | $0.101 |
| AWS | ap-south-1 (Mumbai) | $0.096 |
| AWS | ap-southeast-1 (Singapore) | $0.096 |
| AWS | ap-southeast-2 (Sydney) | $0.111 |
| AWS | ca-central-1 (Montreal) | $0.060 |
| AWS | eu-central-1 (Frankfurt) | $0.060 |
| AWS | eu-west-1 (Dublin) | $0.060 |
| AWS | eu-west-2 (London) | $0.060 |
| AWS | sa-east-1 (Sao Paulo) | $0.137 |
| AWS | us-east-1 (N. Virginia) | $0.060 |
| AWS | us-east-2 (Ohio) | $0.060 |
| AWS | us-west-2 (Oregon) | $0.060 |
| GCP | asia-northeast3 (Seoul) | $0.117 |
| GCP | europe-west1 (Belgium) | $0.060 |
| GCP | europe-west4 (Netherlands) | $0.060 |
| GCP | northamerica-northeast1 (Montréal) | $0.060 |
| GCP | us-central1 (Iowa) | $0.060 |
| GCP | us-east1 (South Carolina) | $0.060 |
| GCP | us-east4 (Virginia) | $0.060 |

## Development branches

A development branch is billed on its own. It uses development SKU rates and the development network allowance. Each development shard is highly available (primary plus at least two replicas). Compute and router charges are prorated to the millisecond and end when you delete the branch.

Restoring a backup or point-in-time recovery always creates a new branch, so the restored environment is an additional billed branch until you delete it.

## Estimate a monthly total

| Component | How the SKU or usage rate is applied |
| --- | --- |
| Shard compute | Highly available SKU rate × shards, plus additional-replica rate × extra replicas × shards |
| Router compute | Production: router SKU rate × observed running routers ÷ 3. Development: router SKU rate × running routers |
| Storage | Allocated disk and provisioned IOPS/throughput above the per-volume allowances, per shard |
| Backups | Compressed backup and WAL above 2× allocated disk, at $0.023 per GB-month |
| Network | Public egress and private traffic above the branch allowance |
| **Estimated monthly total** | **Sum of the components above** |

Actual charges differ from a full-month estimate when resources or usage change during the billing period.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
