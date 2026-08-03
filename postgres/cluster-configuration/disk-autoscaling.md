---
url: https://planetscale.com/docs/postgres/cluster-configuration/disk-autoscaling
title: "Disk Autoscaling"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

PlanetScale storage autoscaling is only for network-attached storage database clusters. For [PlanetScale Metal](../../metal.md) clusters, you need to increase the cluster instance size to increase storage space.

Cloud providers like AWS and GCP limit how frequently network-attached disks can be resized. In both cases, there is a multi-hour cooldown period between resizing operations. These volumes also typically do not support shrinking. PlanetScale disk autoscaling handles the automatic increasing and decreasing of disk size beyond these AWS and GCP limitations.

**Disk autoscaling is enabled by default upon database creation.**

We provide three scaling modes designed to optimize cost and performance while maintaining data availability:

- **In-place growth mode** — This is the default scaling mode that expands storage capacity by resizing existing volumes directly, without requiring failovers or connection disruption. This method leverages AWS EBS’s native resize capability.
- **Surge growth mode** — Surge growth creates new volumes with larger capacity and orchestrates failover to the new storage, circumventing [AWS EBS resize limitations](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyVolume.html).
- **Shrink mode** — In shrink mode, the autoscaler reduces storage capacity for underutilized volumes. This can help to optimize costs after a surge event.

Autoscaling will grow the cluster’s storage without requiring you to make any configuration changes. The new additional space will become available as soon as the scaling action has completed.

There are pricing implications when you enable disk autoscaling. You will be billed for the *allocated* disk size, not the actual total storage. Make sure you enable shrink mode to automatically adjust down to optimize costs.

## Autoscaling thresholds and behaviors

When enabled, disk surge and shrink autoscaling will kick in when your disk utilization reaches the following thresholds:

### Surge mode thresholds

Automatic disk growth activates when disk utilization reaches these thresholds:

- **70%** for disks smaller than 4 TiB
- **25%** for disks larger than or equal to 4 TiB

### Suggested shrink thresholds

A disk shrink will be suggested in the “Clusters” configuration storage tab when disk utilization falls below these thresholds:

- **12.5%** for disks smaller than 1 TiB
- **15%** for disks between 1 TiB and 2 TiB
- **25%** for disks larger than 2 TiB

For example, if you have a 200 GiB disk allocation and are only using 20 GiB, we will suggest the disk can be shrunk because it’s below the existing 12.5% threshold.

### Key disk autoscaling behavior:

- Cluster storage can only scale once in a multi-hour period
- Cluster storage scales proportionally based on current size
	- Smaller disks receive larger percentage increases, while larger disks receive smaller percentage increases
- All disks grow by a minimum of 50% when autoscaling occurs
- A disk is never shrunk automatically and disk shrinking must be initiated manually
- If you need to scale cluster storage by more than 200% within 24 hours, manually scale disk size ahead of time
- Autoscaling will not scale past your configured [**Storage limits**](#storage-limits)

## Surge growth mode

Our surge growth creates new volumes with larger capacity and orchestrates failover to the new storage, circumventing AWS EBS resize limitations.

When our disk autoscaler is able to spread out disk scale-up sufficiently, no downtime is required to scale the disks. When data growth is rapid, the autoscaler may need to complete a **surge resize** to support the writes. In this case, PlanetScale creates brand new, larger network-attached storage volumes to replace the old ones. Surge growth causes a brief failover event that severs existing database connections. Applications must handle connection recovery.

If the surge autoscaler is able to complete the resize before your disk fills, downtime will be minimal for growing the disk (seconds). If your disk fills before the new disks are ready, you will experience a longer period of downtime.

We make every effort to keep your network-attached storage disk from filling, but it’s important for the database administrators to pay close attention to storage and take manual intervention when necessary.

## Manually shrinking

A disk can be manually shrunk by going to “Clusters” > “Storage” and modifying “Minimum disk size”. The UI will indicate if the disk can shrink and the minimum volume size following the [suggested shrink thresholds](#suggested-shrink-thresholds).

When initiated, the disk scaler reduces storage capacity for underutilized volumes through surge operations, as AWS EBS does not support in-place volume shrinking.

Shrink operations cause a brief failover event that severs existing database connections. Applications must handle connection recovery.

## Enable or disable disk autoscaling

Disk autoscaling is enabled by default upon database creation. Both of these options can be configured by going to “Clusters” > “Storage” > and clicking the “Enable autoscaling” checkbox.

## Storage limits

The storage limit sets the maximum amount of storage that can be allocated to your database cluster through autoscaling. This acts as a ceiling to prevent unlimited storage growth and helps control costs.

When autoscaling is enabled, your storage can grow from the minimum disk size up to the storage limit you specify. The storage limit should be set higher than your initial disk size to allow for growth while providing a reasonable upper bound for your storage costs.

The maximum disk size for network-attached storage is 65536 GB (64 TiB).
