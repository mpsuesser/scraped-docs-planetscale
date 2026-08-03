---
url: https://planetscale.com/docs/metal/metal-performance
title: "Metal Performance"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Upgrading to Metal

When upgrading your existing PlanetScale database to Metal, it’s useful to know where to look to see how the upgrade has improved performance. Here, we cover the two main places you can inspect: Insights and the database metrics panel.

The examples shown on this page use a Vitess database, but Metal is available for both Vitess and Postgres databases.

In this page, we will show the results of upgrading from a `PS-640` to an `M-640`. These both have 8 vCPUs and 64GB of RAM, but use a different underlying storage system that allows for the `M-640` to have improved performance. Let’s look at the effects of this in PlanetScale.

## Insights

After upgrading to Metal, Insights is a great place to look to see how performance has changed. When you visit the Insights page, there are several tabs you can use to view graphs for different statistics. The first and default one is a query latency chart.

### Query latency

After zooming in to the time period when we upgraded to Metal, here is what we see:

![Insights query latency](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/insights-p50-p95-p99.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=767013170e03be0f39cfeab8350dfa74)

Insights query latency

![Insights query latency](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/insights-p50-p95-p99-darkmode.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=82aeb2e039c456dbc319621cac212a90)

Insights query latency

Insights shows indicators on cluster resize and VTGate resize events. We can see in the graph above the purple vertical line indicates when we upgraded to Metal (plus or minus a few minutes). This clearly correlates with an order-of-magnitude or more improvement in p95, and p99 query latencies. You can also click the “p99.9” icon to enable this additional metric, and see yet another significant improvement.

![Insights query latency](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/insights-p50-p95-p99-p99.9.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=78a6ce607023918547e91944a4450459)

Insights query latency

![Insights query latency](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/insights-p50-p95-p99-p99.9-darkmode.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=cf81b66d3118c9cceffc43e8ffb4ff29)

Insights query latency

### Anomalies

The anomalies graph shows the % of queries that are considered anomalous, or slow-running.

![Insights anomalies](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/insights-anomalies.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=398f820dbdd41a854b04da1564580b61)

Insights anomalies

![Insights anomalies](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/insights-anomalies-darkmode.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=b55c161ebc545887034608a6850c41a2)

Insights anomalies

We already had a very small number (less than 0.3%) but even so we see an improvement.

### Queries

![Insights queries](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/insights-qps.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=99c20759cb9249a5bdff720ef5d21df8)

Insights queries

![Insights queries](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/insights-qps-darkmode.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=1669849d9c31782265135833dbc252c4)

Insights queries

We also see a drastic improvement in average QPS. Prior to switching over we were averaging around 2k queries per second, and after the average is closer to 14k QPS.

At the moment PlanetScale switched your traffic from the non-Metal database to the new Metal one, queries are buffered for a short span of time (seconds) to allow Vitess to do the failover to the new Metal primary and replicas. After the failover completed, the buffered queries are quickly pushed to the database to be fulfilled. This is a zero-downtime operation, but you may see a short spike in QPS or latency when the failover occurs.

In this example, we are using synthetic TPCC load. In a real production workload you likely would not see such a large QPS jump, as that is dependant on demand from connected application servers. This similarly applies for row reads and writes below. However, what you may find is that an instance size with the same compute specs is able to handle more QPS load before needing to upgrade. The lower IO latency and unlimited IOPS allow you to squeeze more work out of a given node in your database cluster.

### Rows read

This is the rows read graph:

![Insights rows read](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/insights-rows-read.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=7f43bf560a82ca4653fea4fc756f359f)

Insights rows read

![Insights rows read](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/insights-rows-read-darkmode.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=61019fad9d0689a13ce76e2b8f289b65)

Insights rows read

We can definitely see that there was an increase from the pre-Metal rows read. However, we also see a jump for a short burst before the switch over to Metal.

This increase appears due to the way upgrading to Metal works. When you upgrade a database to Metal, the (greatly simplified) steps that happen in the background are:

- (A) PlanetScale spins up three new NVMe-backed instances for you (one primary and two replicas)
- (B) Once these are up and running with all the necessary MySQL and Vitess components, we begin copying the data from your existing database onto the three NVMe drives, starting with restoring from the most recent backup.
- (C) Once the backup is restored, we catch the backup up to the current state of your other database. This can be as quick as a few minutes or seconds if there isn’t much new data, but can take some time, as we see here, if it has a lot of data to catch up.
- (D) Once the state of the old and new databases are identical, the cut over is made and the Metal instances begin serving your database traffic.
- (E) The old instances are torn down.

Depending on your database and growth rate, you may see a similar spike due to step (C). This could be as short as a minute, or as slow as over an hour, depending on the characteristics of your database workload.

### Rows written

We get a clear jump in rows written after the upgrade:

![Insights rows written](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/insights-rows-written.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=f1711404a8df82e8653228f697ad15b6)

Insights rows written

![Insights rows written](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/insights-rows-written-darkmode.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=3202039980541ee02fbd56fc324accf9)

Insights rows written

Unlimited IOPS allows for increased write throughput. We can see that before the switch to Metal, we were only utilizing 20-25% of our CPU capabilities, and much of this was due to being I/O bottlenecked for this particular workload.

![Insights rows written](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/metal/cpu-ram.png?w=2500&fit=max&auto=format&n=AJPY38bILe2zenXX&q=85&s=ca098e20f4969f0d7655a6107f62ac86)

Insights rows written

![Insights rows written](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/metal/cpu-ram-darkmode.png?w=2500&fit=max&auto=format&n=AJPY38bILe2zenXX&q=85&s=d83110201ab702ad55bc913a7a55d07a)

Insights rows written

Once we switched to Metal and had faster IO operations and unlimited IOPS, we were suddenly able to increase our CPU utilization to 80%+.

## Primary metrics

Another good place to look after upgrading to Metal is the metrics for your primary database node. From the database’s Dashboard page, click on your primary from the architecture diagram. A panel should display on the right side of your screen with metrics from this instance.

![Database primary metrics](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/primary-metrics.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=e68202030c9c723fcfbaa34168d2fe47)

Database primary metrics

![Database primary metrics](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/primary-metrics-darkmode.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=a748fc4ce23a2a1ab9656157b756ebe9)

Database primary metrics

If the change was recently made, you can change the graph to display metrics for only the past hour using the drop down. This gives you good insight into how the upgrade affected your IOPS capabilities, as well as CPU and RAM. Note specifically the large jump in IOPS after the switch to Metal.

## Why is Metal so much faster?

PlanetScale databases come in two main flavors: **Metal** and **network-attached storage**.

[**Network-attached storage**](../plans/planetscale-skus.md#network-attached-storage) (Amazon Elastic Block Storage or Google Persistent Disk) databases store all data on storage volumes that are attached to your database’s compute resources over the network. For databases in AWS we use Elastic Block Storage (EBS) and in GCP we use Persistent Disk. These network-attached storage solutions are convenient for several reasons. For one, it is easy to resize such storage volumes. PlanetScale leverages this to auto-scale your storage as the size of your database grows or shrinks, allowing you to pay a per-GB storage price.

This also means that you can pair a tiny compute instance with a large amount of storage. This works well for large data sets that are not frequently queried. The opposite is also true — you can pair a small amount of storage with a large compute instance for workloads that are heavily CPU-bound.

One disadvantage of using **network-attached storage** storage is I/O latency. Reads from and writes to disk need to make network round-trips to be fulfilled. The intra-AZ network speeds in AWS and GCP data centers are generally very good, but still slower than accessing a locally-attached solid-state drive. There is also the issue of IOPS. The popular `gp3` EBS volume class provides 3000 IOPS included, but using more than this requires paying for additional IO bandwidth, leading to more expensive databases.

These disadvantages do not just apply to PlanetScale. Many of the popular cloud-hosted database solutions, including those offered by Amazon and Google, use network-attached storage to simplify storage scalability. This convenience and scalability comes at a performance cost.

**Metal** databases store all data on locally-attached NVMe SSDs. Using direct-attached storage provides a clear solution to the performance issues described above. The removal of network round-trips for I/O operations means low-latency IO, and we sidestep the issue of needing to pay for increased IOPS completely. Your database now has the ability to use modern NVMe SSD technology to it’s full potential.

## Data durability

One of the most important aspects of a database is storing data durably, even in the face of unexpected outages or hardware failure.

By default, all databases on PlanetScale have their data replicated three times: once on the primary database node, and then again on each of the two replicas. More replicas can be added if desired.

When using a network-attached storage database, each of the three database instances stores a copy of the data on three distinct network-attached volumes. When using network-attached storage, the compute instances and storage volumes are decoupled. If one of your database compute nodes gets taken down due to an underlying hardware failure, the data is still preserved on the EBS or PD volume and can be quickly re-attached to a new node. PlanetScale handles the detection, re-attachment, and recovery from such failures automatically.

A Metal cluster also has high durability, but with different characteristics. Each of the three database instances (1 primary, 2 replicas) stores a copy of the data on the NVMe drives attached to the instance. However, in this case, if one of the three compute nodes goes down, the data also goes with it. This is why replication is so critical. In this scenario, we still have two other copies of the data, and PlanetScale will automatically detect and replace the node that failed, bringing the total back up to three.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
