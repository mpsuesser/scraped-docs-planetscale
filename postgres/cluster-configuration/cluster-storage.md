---
url: https://planetscale.com/docs/postgres/cluster-configuration/cluster-storage
title: "Cluster Storage"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

For PlanetScale Postgres clusters launched on PlanetScale Metal instances, storage is scaled by directly scaling the cluster instance size. Storage autoscaling is not available for Metal clusters. To learn more see the documentation for [PlanetScale Metal](../../metal.md)

## Configuring storage settings

You must be a database or organization administrator to modify these settings. Adjusting these settings may incur additional charges. To learn more about pricing for storage, see [pricing](../pricing.md).

## Minimum disk size configuration

Configure the minimum disk size for your database cluster. This setting determines the initial storage capacity allocated to your database. The disk size is specified in GB and serves as the baseline storage allocation for your cluster.

The maximum disk size for network-attached storage is 65536 GB (64 TiB).

## Enable autoscaling

PlanetScale storage autoscaling is only for network-attached storage database clusters. For [PlanetScale Metal](../../metal.md) based clusters you will need to increase the cluster instance size.

Disk autoscaling is enabled by default upon database creation. Disk autoscaling automatically increases storage when your database approaches a disk size utilization threshold, preventing storage-related outages without manual intervention. You can also manually shrink your disk.

Autoscaling can be configured by going to “Clusters” > “Storage” > and clicking the “Enable autoscaling” checkbox.

For more information, see the [Disk autoscaling documentation](disk-autoscaling.md).

## Storage limit

The storage limit sets the maximum amount of storage that can be allocated to your database cluster through autoscaling. This acts as a ceiling to prevent unlimited storage growth and helps control costs.

When autoscaling is enabled, your storage can grow from the minimum disk size up to the storage limit you specify. The storage limit should be set higher than your initial disk size to allow for growth while providing a reasonable upper bound for your storage costs.

The maximum disk size for network-attached storage is 65536 GB (64 TiB).

## IOPS

Configure the maximum input/output operations per second for your database. This will be limited by your database cluster size and disk size.

### Storage volume type and IOPS

| Storage type | Default IOPS | Maximum IOPS |
| --- | --- | --- |
| AWS gp3 | 3,000 | 80,000 (at 160 GB or larger disk size) |

The default of **3,000 IOPS** is included with all AWS gp3 network-attached storage clusters at no additional cost. You are only charged for IOPS above this baseline if you manually increase the IOPS setting.

## Bandwidth

The maximum amount of data that can be read or written to your database in a single second. This will be limited by your database cluster size and configured IOPS.

### Storage volume type and bandwidth

| Storage type | Default bandwidth | Maximum bandwidth |
| --- | --- | --- |
| AWS gp3 | 125 MiB/s | 2,000 MiB/s (at 8,000 IOPS or higher) |

The default of **125 MiB/s** throughput is included with all AWS gp3 network-attached storage clusters at no additional cost. You are only charged for throughput above this baseline if you manually increase the bandwidth setting.

### Storage throughput limits

For databases created on AWS-based clusters the **maximum configurable throughput** your cluster can support is based on CPU architecture and cluster size.

| CPU Architecture | Cluster Size | Maximum Throughput (in MiB/s) |
| --- | --- | --- |
| AWS x86-64 | PS-DEV, PS-10, PS-20, PS-40, PS-80, PS-160, PS-320, PS-640, PS-1280, PS-2560 | 2000 |
| AWS aarch64/ARM64 | PS-DEV, PS-10, PS-20, PS-40, PS-80, PS-160, PS-320, PS-640, PS-1280 | 593 |
| AWS aarch64/ARM64 | PS-2560 | 2000 |

## Monthly storage cost

Displays the estimated monthly cost for your current storage configuration. If you adjust your storage configuration the number shown represents the new monthly estimate for the configured values. Billing for storage changes begins once the storage change has completed.

## Tracking changes to storage settings

You can click on the “Changes” tab on the Clusters page to view a log of any changes made to your storage settings. The log will include the settings affected, the original and updated values, status, user that made the changes, start time, and end time.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
