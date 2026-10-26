---
url: https://planetscale.com/docs/postgres/scaling/dedicated-read-replicas
title: "Dedicated Read Replicas"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Dedicated read replicas

> Independently sized dedicated read replicas for scaling Postgres reads across locations.

This page covers Postgres.

Vitess version: [Vitess](https://planetscale.com/docs/vitess/scaling/read-only-regions.md)

## Overview

Dedicated read replicas maintain continuously updated copies of a production branch in locations you select. Use them to serve read traffic closer to your applications and users or to isolate read-heavy workloads from the primary cluster.

A dedicated read replica differs from the [replicas in your primary cluster](https://planetscale.com/docs/postgres/scaling/replicas.md):

* Primary-cluster replicas provide high availability and read capacity within the primary location.
* Dedicated read replicas are distincy Postgres nodes from the primary cluster and provide independently sized read capacity in a location you select.

Each dedicated read replica has a unique name and contains one or more read-only instances. Configure its location, cluster size, replica count, storage, and supported Postgres parameters independently of the primary cluster. You can create multiple dedicated read replicas in the same location.

Dedicated read replicas can be created in the same cloud region as the primary or different ones from the same cloud privider.
You may not, for example, have your primary in an AWS region and a dedicated read replica in a GCP region.

Database administrators and organization administrators can create and manage dedicated read replicas.

> [!WARNING]
>
> PlanetScale asynchronously replicates changes to dedicated read replicas. Applications that query them must tolerate [replication lag and temporarily stale results](https://planetscale.com/docs/postgres/scaling/replicas.md#data-consistency-and-replication-lag).

## Create a dedicated read replica

**Step 1**

In the [PlanetScale dashboard](https://app.planetscale.com), select your database and production branch.

**Step 2**

Select **Clusters** in the left navigation, then select the **Dedicated read replicas** tab.

**Step 3**

Select **Add a dedicated read replica**.

**Step 4**

Enter a unique name, then choose the location, number of replicas, cluster size, and storage settings. The dashboard shows the estimated monthly compute and storage cost.

**Step 5**

Select **Add dedicated read replica**. The dedicated read replica appears with a **Provisioning** status until it is ready.

## Change a dedicated read replica

Set the cluster size, replica count, storage, and supported Postgres parameters independently for each dedicated read replica.

**Step 1**

Open the production branch's **Clusters** page and select **Dedicated read replicas**.

**Step 2**

Change the **Replica size** or **Number of replicas** for the cluster.

**Step 3**

For network-attached storage, configure the minimum disk size, storage limit, autoscaling, IOPS, or throughput.

**Step 4**

To change a supported Postgres parameter, expand **Customize cluster parameters** and enter the new value.

**Step 5**

Select **Save replica changes**. Changes apply immediately.

The **Changes** tab shows the status and history of dedicated read replica changes.

Storage settings apply independently to each dedicated read replica. For storage controls and limits, see [Cluster storage configuration](https://planetscale.com/docs/postgres/cluster-configuration/cluster-storage.md).

Set parameter values on a dedicated read replica at or above the corresponding primary-cluster values. Changing a parameter restarts the dedicated read replica.

For PlanetScale Metal, a dedicated read replica size must have enough storage for the primary cluster's current disk usage and required headroom.

## Connect to a dedicated read replica

Managed roles and passwords work across the primary cluster and its dedicated read replicas. The host and username are different for each dedicated read replica. The dashboard displays the connection details at creation time.

**Step 1**

Open the production branch and select **Connect**.

**Step 2**

Create or select a role.

**Step 3**

Under **Connection target**, choose the named dedicated read replica that you want to connect to.

**Step 4**

Copy the connection string or individual connection values that the dashboard provides.

You can also select a read-only connection target from a role's details page under **Settings** > **Roles**.

> [!NOTE]
>
> The generated username ends in `|replica`, which distributes connections across the read-only instances in the selected cluster.

Dedicated read replicas reject `INSERT`, `UPDATE`, `DELETE`, and other write operations.

To retrieve connection details from the CLI, pass the dedicated read replica name to `pscale role get`:

```bash theme={null}
pscale role get <database> <branch> <role-id> --dedicated-read-replica <dedicated-read-replica-name>
```

## Monitor a dedicated read replica

Prometheus metrics for a dedicated read replica use `planetscale_database_branch_id` to identify the replica and `planetscale_upstream_database_branch_id` to identify its parent database branch. See the [Prometheus metrics reference](https://planetscale.com/docs/postgres/monitoring/prometheus-metrics-postgres.md).

## Manage dedicated read replicas with the API

The [PlanetScale API](https://planetscale.com/docs/api/reference/getting-started-with-planetscale-api.md) supports dedicated read replica management:

* [List dedicated read replicas](https://planetscale.com/docs/api/reference/list_dedicated_read_replicas.md)
* [Create a dedicated read replica](https://planetscale.com/docs/api/reference/create_dedicated_read_replica.md)
* [Get a dedicated read replica](https://planetscale.com/docs/api/reference/get_dedicated_read_replica.md)
* [Update a dedicated read replica](https://planetscale.com/docs/api/reference/update_dedicated_read_replica.md)
* [Delete a dedicated read replica](https://planetscale.com/docs/api/reference/delete_dedicated_read_replica.md)
* [List dedicated read replica change requests](https://planetscale.com/docs/api/reference/list_dedicated_read_replica_change_requests.md)

Creating a dedicated read replica requires a unique name and region slug. The API defaults to one instance and the primary cluster's size when you omit `replicas` and `cluster_size`.

Create and update requests accept a `storage` object containing `minimum_storage_bytes`, `maximum_storage_bytes`, `storage_autoscaling`, `storage_iops`, and `storage_throughput_mibs`.

Get, update, and delete requests identify a dedicated read replica by name. To retrieve role connection details for a specific cluster, pass its name in the [`dedicated_read_replica` query parameter](../../api/reference/get_role.md).

## Delete a dedicated read replica

**Step 1**

Open the production branch's **Clusters** page and select **Dedicated read replicas**.

**Step 2**

Select **Delete replica cluster** for the cluster you want to remove.

**Step 3**

Confirm by selecting **Delete replica cluster**.

> [!WARNING]
>
> Deleting a dedicated read replica is irreversible and stops its connection endpoint.

## Pricing

The dashboard displays the estimated monthly compute and storage cost when you create or change a dedicated read replica. PlanetScale prorates charges when you create, delete, or resize clusters.

Compute charges consist of:

* The location's dedicated read replica rate for the first instance.
* The standard additional-replica rate for every instance after the first.

For network-attached storage, PlanetScale bills every instance in a dedicated read replica for its configured storage, IOPS, and throughput. PlanetScale Metal cluster prices include their local storage.

Cross-region replication traffic for Postgres dedicated read replicas is billed per GB with no included allowance. See [cross-region dedicated read replica traffic pricing](https://planetscale.com/docs/postgres/pricing.md#cross-region-dedicated-read-replica-traffic).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
