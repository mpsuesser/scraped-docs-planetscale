---
url: https://planetscale.com/docs/metal
title: "Metal"
description: ""
access_date: 2026-09-02T21:57:53.710Z
current_date: 2026-09-02T21:57:53.710Z
---

This translates to significant latency reduction, more consistent IO performance, and unlimited I/O Operations Per Second (IOPS). Metal is an excellent choice for high-IOPS and other performance-critical workloads. With Metal, your database now has the ability to use modern NVMe SSD technology to its full potential.

Metal nodes for Postgres now [start at $50/month](https://planetscale.com/blog/50-dollar-planetscale-metal-is-ga-for-postgres).

## Using a Metal database

PlanetScale databases come in two main flavors: **Metal** and **network-attached storage**. When you create a database on PlanetScale, you can choose between these two options:

The storage for Metal databases does not autoscale. It is important to keep a close eye on the storage capacity of Metal databases, and upgrade well before running out of space.

When you create a Metal database, you must choose a drive size up front.

You should select a drive size that best suits your current data size, while also taking into account growth trends. When the time comes that you need more storage, we make it easy to upgrade to larger NVMe drives with just a few clicks of a button. You can learn more about creating and resizing Metal databases in our [creation and upgrade documentation](metal/create-a-metal-database.md).

## Monitoring your storage

Fixed-sized drives also means that you must closely monitor how much storage your database is using. You can do so by looking at the storage information on the PlanetScale dashboard:

We will send you email notices when your database storage reaches the following thresholds: 60%, 75%, 85%, 90%, 95%. We will also email you when we estimate that your storage will run out in 1 week and 24 hours, based on recent usage trends.

Reaching or getting close to a drive’s max capacity is dangerous and can lead to failures. It’s important to closely monitor your database’s disk usage in the dashboard and check your regular storage email notifications. We have an additional safeguard in place to protect your data: When we detect that your Metal disk has 6GiB or less of available space, we automatically switch it to read-only mode until resized. Ideally, you should resize to a larger drive long before reaching this point.

## Workload suitability

Using Metal is a clear win for many workloads. Many of our current Metal customers have been able to either (A) save money, (B) increase performance, or (C) do both at the same time by switching to Metal. The per-GB cost of metal storage is more affordable than network-attached storage with high IOPS capacity, and the improved IO performance allows you to use smaller compute instances in some cases.

There are some scenarios where a network-attached storage database may be a better choice. Here, we provide some general suggestions to help you choose the ideal type of database for your needs. If you’d like a more personalized assessment, please [reach out to support](https://planetscale.com/contact?initial=support) with the specifics of your workload.

Generally, these types of database workloads are ideal for Metal:

- If your workload has significant I/O demands, Metal is an ideal choice. A network-attached storage database has limitations on how quickly it can read and write data due to the additional network hops. Metal databases allow you to unlock the full potential of modern NVMe technology, providing ultra-high throughput.
- If you have experience running up against the limits of AWS EBS IOPS or have a large `gp3` or `io2` `EBS` volume bill. Metal provides unlimited IOPS and will likely yield performance improvement, cost savings, or both. There is no need to pay extra for access to the I/O throughput of the local drive.
- If low-latency database performance is critical to your business needs.
- If you are concerned about long-tail p99+ performance.

However, there are some scenarios where choosing network-attached storage may still be preferable:

- Very small databases that have both low compute and low storage requirements may be better suited for network-attached storage.
- Databases where the majority of active rows fit in RAM. In this case, the I/O demand on the storage is probably low, and you won’t see as much of a performance boost by using Metal.
- If you frequently resize your database, Metal may not be the best option. Metal instance resizes generally take longer than a network-attached storage resize as they require copying data between drives.

## Metal Performance

We’ve mentioned several times that Metal can provide you better performance. What does that look like in practice? Let’s look at two examples.

### PlanetScale Insights

Below is a screenshot showing the p50 and p95 response times for the database that was powering PlanetScale Insights as of Q4 2024.

You can pretty clearly see when the database was switched over from network-attached storage instance types to Metal. The p50 response times were cut in half, and the p95 had approximately a 7x improvement.

The workload was and continues to be very I/O bound. The Insights database ingests a large amount of time-series data, and is frequently queried to pull the data that we use to generate graphs in Insights. Metal provides a huge improvement for this type of workload.

### Large, sharded database

One of our existing customers runs several large, sharded databases. We migrated these databases to Metal during the internal release of our product. Below is a screenshot of the p99 latencies of a set of shards that we migrated from network-attached storage database to Metal instances.

Though their p99 response times were already very good, Metal was able to further cut it in half.

### Cost and performance

We migrated yet another large customer during our internal release. After switching to Metal, they saw a significant improvement in API call latency for one of their critical APIs.

We can clearly see that starting on Dec 20, the long tail of latency was reduced significantly. This is due to the lower latency and improved consistency of local NVMe disk performance.

This same customer also saw some significant cost savings with their move to Metal.

The performance of the database improved and the AWS costs to run dropped from over $100 per day to ~$30 per day.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
