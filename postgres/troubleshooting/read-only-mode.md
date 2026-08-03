---
url: https://planetscale.com/docs/postgres/troubleshooting/read-only-mode
title: "Read Only Mode"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

Read-only mode preserves cluster functionality while preventing operations that could lead to instability, allowing you to remediate issues before they become critical.

When read-only mode is enabled, attempts to write to the cluster will see the following error:

```sql
invalid statement because cluster is read-only
```

We will also *pause* all logical replication subscriptions writing data into the cluster. Once your cluster is out of read-only mode you can unpause the subscribers by running

```text
ALTER SUBSCRIPTION sub_name ENABLE;
```

on each subscription.

There’s no way for a PlanetScale customer to enter or exit read-only mode, except by remediating the conditions that caused the cluster to enter that mode.

Here are the reasons we’ll put the cluster into read-only mode:

## Insufficient space

To protect your cluster from crashing, if a cluster has very little space left (typically 5% or less), we will automatically move it into read-only mode. The specific scenarios are:

| Storage Type | Scenario | Remediation |
| --- | --- | --- |
| **Network-attached storage** | Clusters with [Autoscaling](../cluster-configuration/cluster-storage.md#enable-autoscaling) disabled or where your current [disk size](../cluster-configuration/cluster-storage.md#minimum-disk-size-configuration) is approaching your configured [Autoscaling maximum](../cluster-configuration/cluster-storage.md#storage-limit) | Increase the [cluster disk size](../cluster-configuration/cluster-storage.md#minimum-disk-size-configuration) manually or increase the [storage limit on disk autoscaling](../cluster-configuration/cluster-storage.md#storage-limit) |
| **PlanetScale Metal** | When your data is approaching the storage size of your cluster | Increase the [cluster size](../../metal.md) |

Once you have remediated the issue, your cluster will automatically exit read-only mode.

PlanetScale will send notifications via email and webhook (if enabled) when your storage exceeds 60, 75, 85, 90, and 95 percent, respectively. You can also track disk utilization via [Metrics](../monitoring/metrics.md). Emails are sent to all Organization and Database administrators. It is critical that these email addresses are monitored regularly.

If the issue was temporary, say due to errant data being written by mistake, you could remove the data, perform a vacuum, and then reverse the remediation action. See [Cluster storage configuration](../cluster-configuration/cluster-storage.md) for more on reducing storage usage.

## Single Node Archiver lag

Single node Postgres clusters offer decreased durability compared to HA offerings. In HA, we use Postgres synchronous replication to guarantee every committed transaction reaches the primary and at least one replica. As part of our usual (we do this on HA too) durability posture, we archive postgres Write-Ahead Logs (WAL) to durable object storage (S3, GCP Cloud Storage). For single-node, we will enter read-only mode if this archiving process falls too far behind writes on your cluster.

If you see this happening frequently, we recommend sizing your cluster up, or moving to an HA cluster.
