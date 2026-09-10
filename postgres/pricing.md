---
url: https://planetscale.com/docs/postgres/pricing
title: "Pricing"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

## Overview

Understanding the relationship between **databases**, **branches**, and **clusters** is key to understanding your billing:

- **Database**: Your overall project (e.g., “my-ecommerce-app”)
- **Branch**: Isolated database deployments that provide you with separate environments for development and testing, as well as restoring from backups. [Learn more about branching](branching.md)
- **Cluster**: The underlying compute and storage infrastructure that powers each branch

**Each branch runs on its own dedicated cluster** and is billed separately based on its configuration and usage.

There are no upfront commitments, and as you adjust your configuration or usage changes, pricing will automatically adjust from that point.

## Query live prices with SQL

Cluster SKU prices are available in a public, read-only Postgres table: `public.planetscale_prices`. You and your agent can query live prices with `SELECT`.

The role can only `SELECT` on that table. SSL is required. Connect through PgBouncer:

```text
postgresql://pscale_api_hhdcchpiodja.dzken160d27q:pscale_pw_<PLANETSCALE_DATABASE_PASSWORD>@aws-us-east-1-3.pg.psdb.cloud:6432/postgres?sslmode=verify-full&sslrootcert=system
```

Each row is one priced cluster. Filter on `product` (`Postgres` or `Vitess`), `provider`, `region`, `machine_sku`, `replication_factor`, and `disk_sku` (empty string for `PS-*` network-attached SKUs). `price` is the monthly cluster cost in whole USD. It does not include extra storage, backups, egress, dedicated PgBouncers, or extra replicas.

```sql
-- Postgres, 3-node HA PS-160 ARM in us-east-1
SELECT machine_sku, replication_factor, price
FROM planetscale_prices
WHERE product = 'Postgres'
  AND machine_sku = 'PS-160-ARM'
  AND provider = 'AWS'
  AND region = 'us-east-1'
  AND disk_sku = ''
  AND replication_factor = 3;
```

```sql
-- Vitess, 3-node HA PS-160 in us-east-1
SELECT machine_sku, replication_factor, price
FROM planetscale_prices
WHERE product = 'Vitess'
  AND machine_sku = 'PS-160'
  AND provider = 'AWS'
  AND region = 'us-east-1'
  AND disk_sku = ''
  AND replication_factor = 3;
```

Not every SKU exists in every region. If a query returns no rows, that configuration is not sold there. Do not invent a price. Vitess SKUs in this catalog are mostly x86; there is no `PS-160-ARM` Vitess row.

Column meanings, more example queries, and an agent-friendly catalog are in [pricing.md](https://planetscale.com/pricing.md). The connection URI is also on the [pricing page](https://planetscale.com/pricing#pricing-database).

## pricing.md

For an agent-friendly catalog of live cluster SKU rates and example SQL, fetch `https://planetscale.com/pricing.md`.

For information on Vitess cluster pricing on the Base plan, see [Base plan cluster pricing](../plans/cluster-sizing.md).

## Cluster instance size

Each database branch runs on its own dedicated cluster infrastructure. Clusters are billed based on the selected instance size and prorated to the millisecond. Each instance size includes defined amounts of vCPU cores and memory, as well as storage based on the storage type selected ([network-attached storage](cluster-configuration/cluster-storage.md) or [PlanetScale Metal](../metal.md)). The dashboard and [pricing calculator](https://planetscale.com/pricing) show the current monthly rate for each size. How that rate is applied is described under [cluster pricing](#cluster-pricing).

You can [increase or decrease your cluster size](cluster-configuration.md) at any time. Pricing is prorated to the millisecond, so if you temporarily increase, you will only be charged for the larger cluster size for the time that it was running. Billing for a modified cluster size begins once the resize is completed. You can also [spin up additional production branches](branching.md) at any time for additional cost. The pricing for these is also prorated.

## Cluster disk storage

### Storage types explained

PlanetScale offers two storage options with different pricing models:

| [PlanetScale Metal](../metal.md) | [Network-Attached Storage](cluster-configuration/cluster-storage.md) |
| --- | --- |
| **All-inclusive pricing** | **Configuration-based pricing** |
| \- Storage cost is included in your cluster instance price   \- High-performance NVMe SSDs directly attached to your servers   \- To get more storage, you upgrade to a larger cluster size   \- Best for: High-performance applications that need predictable costs | \- First 10 GB included with each cluster   \- Additional storage is billed separately from your cluster instance configuration   \- Flexible scaling - add storage without changing cluster size   \- Automatic disk scaling with [autoscaling](cluster-configuration/cluster-storage.md#enable-autoscaling)   \- Best for: Applications with variable storage needs or cost optimization |

### Network-attached storage billing

For network-attached storage clusters, pricing varies by region and cloud provider:

- **Base storage**: you manually configure this in cluster settings, unless [autoscaling](cluster-configuration/cluster-storage.md#enable-autoscaling) is enabled
- **Additional IOPS**: you manually configure this in cluster settings (AWS only)
- **Additional Throughput**: you manually configure this in cluster settings (AWS only)

AWS gp3 and GCP Hyperdisk Balanced bill provisioned IOPS and throughput above their included baselines. GCP PD-SSD has volume-only pricing, with performance determined by disk size.

Billing for modified storage attributes begins once the change for the cluster has completed.

**What are IOPS and Throughput?**

- **IOPS** (Input/Output Operations Per Second): How many read/write operations your database can handle per second. Higher IOPS = faster response for many small database queries.
- **Throughput**: How much data can be transferred per second (MiB/s). Higher throughput = faster for large data operations like bulk imports.

**Default values for AWS gp3 storage:**

All network-attached storage clusters on AWS come with the following baseline performance included at no additional cost:

| Setting | Default value |
| --- | --- |
| IOPS | 3,000 IOPS |
| Throughput (Bandwidth) | 125 MiB/s |

These defaults apply to all PS-series cluster sizes (PS-5 through PS-2560). You only pay additional IOPS or throughput charges if you manually configure values above these baselines.

For more information on changing your cluster’s storage, see the [Cluster storage configuration](cluster-configuration/cluster-storage.md) documentation.

## Backups

### Included backup storage

PlanetScale automatically includes backup storage with every database branch at no extra cost. Each branch gets backup storage equal to **2x your disk size**.

Examples:

| Disk Size | Backup Storage Included |
| --- | --- |
| 25 GB | 50 GB |
| 50 GB | 100 GB |
| 1000 GB | 2000 GB |

For clusters with [network-attached storage](cluster-configuration/cluster-storage.md) with [autoscaling](cluster-configuration/cluster-storage.md#enable-autoscaling) enabled, **backup** storage changes as your **disk storage** autoscales.

When your backup and WAL storage exceeds the included amount, additional storage is billed at **$0.023 per GB per month**.

**Storage overage examples**:

| Storage Overage | Duration | Cost |
| --- | --- | --- |
| 100 GB | 30 days | $2.30 |
| 100 GB | 15 days | $1.15 |

### How backup billing works

**Storage calculation**: Both database data backups and WAL files are automatically compressed before being sent to backup storage. You’re billed only on the compressed size, which is typically much smaller than your active database size.

Backup storage usage billing data is updated hourly.

For more details on backup functionality, see [Back up and restore](backups.md).

**What affects storage costs**:

| Factor | Impact |
| --- | --- |
| **Database activity** | More active databases generate more WAL files |
| **Backup retention** | Longer retention periods increase storage usage |
| **Oldest backup age** | WAL files are kept as long as your oldest backup exists |

## Network data transfer

### Public traffic

Outgoing network traffic (egress) is billed based on the amount of data transferred out of PlanetScale Postgres instances to any other host or destination. You are not charged for data transfer associated with [replicating](scaling/replicas.md) data between primaries and replicas or [backups](backups.md). Each branch has a default amount of included public network egress that is aggregated for all cluster instances in the entire branch.

| Branch Type | Included Public Network Egress |
| --- | --- |
| Production | 100 GB per month |
| PS-5 non-HA | 10 GB per month |
| Development (PS-DEV) | 10 GB per month |

Fetch the current public egress rate for a region from the [`/www/egress-rates`](https://api.planetscale.com/www/egress-rates?region=us-east) endpoint. Pass a supported region identifier in the `region` query parameter.

Incoming public network traffic (ingress) is free.

### Private traffic

For databases using [private connections](https://planetscale.com/docs/postgres/connecting/private-connections) (AWS PrivateLink or GCP Private Service Connect), all network traffic over that connection — both egress and ingress — is charged at a flat rate of **$0.01/GB**, regardless of region. This replaces the standard regional egress pricing for traffic routed over private connections. Each branch has a default amount of included private network traffic that is aggregated for all cluster instances in the entire branch.

| Branch Type | Included Private Network Traffic |
| --- | --- |
| Production | 100 GB per month |
| PS-5 non-HA | 10 GB per month |
| Development (PS-DEV) | 10 GB per month |

## Additional replicas

Each production cluster includes 2 [replicas](scaling/replicas.md) (excluding [Single Node](cluster-configuration/single-node.md)) to provide high availability and additional read capacity alongside the primary. Certain read-heavy workloads may demand additional read replicas. If you need additional replicas beyond what is included, you can add them for an additional cost based on the branch’s instance size and storage requirements. The cost of an additional replica instance is about one third of the highly available SKU rate. New replica billing begins once the change has completed.

Replicas with network-attached storage do not receive the 10 GB included disk allowance. You pay for the full configured disk size on each extra replica, plus any IOPS or throughput you provision above the baselines.

Development branches and Single Node databases do not support replicas. See [Replicas](scaling/replicas.md).

## Cluster pricing

The dashboard and [pricing calculator](https://planetscale.com/pricing) show the monthly SKU rate for each cluster size in your region. That rate is applied as follows:

- **Single Node** — you pay the single-node SKU rate for the time that size is running.
- **Highly Available** — you pay the highly available SKU rate, which covers one primary and two replicas.
- **Resizes** — billing for the new size starts when the resize completes and is prorated to the millisecond.
- **Metal** — local NVMe storage is included in the Metal SKU rate. Network-attached storage is billed separately, as described under [Cluster disk storage](#cluster-disk-storage).

Query live SKU prices with SQL from the public `planetscale_prices` table. See [Query live prices with SQL](#query-live-prices-with-sql).

Visit the [pricing calculator](https://planetscale.com/pricing) for interactive, region-specific SKU rates.

## Storage pricing

Network-attached storage is billed per database instance. The primary and two replicas in an HA cluster each use the configured volume, IOPS, and throughput. Additional replicas use another full copy of the configured volume and do not receive the 10 GB included allowance.

For storage types with an included allowance, monthly volume cost before proration is:

```text
included-instance volume cost = max(average storage GB - 10 GB, 0) × volume rate × included instances
additional-replica volume cost = average storage GB × volume rate × additional replicas
```

IOPS and throughput costs before proration are:

```text
IOPS cost = max(provisioned IOPS - included IOPS, 0) × IOPS rate × total instances
throughput cost = max(provisioned MiB/s - included MiB/s, 0) × throughput rate × total instances
```

| Storage type | Included performance | Additional charges |
| --- | --- | --- |
| AWS gp3 | 3,000 IOPS and 125 MiB/s | Provisioned IOPS and throughput above the included baselines |
| GCP Hyperdisk Balanced | 3,000 IOPS and 140 MiB/s | Provisioned IOPS and throughput above the included baselines |
| GCP PD-SSD | Performance scales with disk size | No separate IOPS or throughput charge |

Volume rates vary by region. Fetch the current rate from the [`/www/storage-rates`](https://api.planetscale.com/www/storage-rates?region=us-east) endpoint:

- AWS regions return the gp3 volume rate.
- GCP regions return the Hyperdisk Balanced volume rate by default.
- Add `pd-ssd=true` for the GCP PD-SSD volume rate, for example [`gcp-us-central1&pd-ssd=true`](https://api.planetscale.com/www/storage-rates?region=gcp-us-central1&pd-ssd=true).

The endpoint returns the volume rate per GB per month. The dashboard shows the IOPS and throughput rates available for the selected storage type and region.

## PgBouncer pricing

The local PgBouncer on the primary is included in the cluster SKU rate. A dedicated PgBouncer is a separate SKU. Its cost is the selected size’s monthly rate multiplied by the configured **PgBouncers per availability zone**, prorated for the time each configuration runs.

```text
dedicated PgBouncer cost = PgBouncer SKU rate × PgBouncers per availability zone
```

Available dedicated sizes:

| Size | CPU | Memory |
| --- | --- | --- |
| PGB-5 | 1/16 vCPU | 128 MB |
| PGB-10 | 1/8 vCPU | 256 MB |
| PGB-20 | 1/4 vCPU | 512 MB |
| PGB-40 | 1/2 vCPU | 1 GB |
| PGB-80 | 1 vCPU | 2 GB |
| PGB-160 | 2 vCPU | 4 GB |

The dashboard and [pricing calculator](https://planetscale.com/pricing) show the current monthly rate for each size in your region. See [PgBouncer](connecting/pgbouncer.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
