---
url: https://planetscale.com/docs/vitess/scaling/cluster-sizing
title: "Cluster Sizing"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

You can easily upsize and downsize your database cluster from within the PlanetScale dashboard. This documentation covers some information about selecting a cluster size upon database creation as well as how to upsize and downsize.

If you are on a consumption commitment plan, please be aware that any changes in cluster size will be reflected against your monthly or annual consumption commitment amount. Changes to the originally selected cluster size may cause you to utilize this amount either more quickly or slowly. If you have further questions, please reach out to your account manager or our [Support](https://planetscale.com/contact) team.

## Selecting a cluster size

Selecting the correct cluster size for your database can have a dramatic impact on how it performs and how much it costs.

A good rule of thumb is when you notice CPU usage is consistently at or close to 100% for an extended period of time, you may benefit from [upsizing your cluster](../../plans/planetscale-skus.md#upsizing-and-downsizing-clusters). Conversely, if your CPU usage is consistently below 50%, you may be able to downsize. You can monitor your CPU usage by clicking on your database, clicking “Primary” in your architecture diagram, and referencing the chart under “Metrics and performance”.

For Metal instances, you have to consider both the compute and the storage, as storage does not autoscale. For more information about adjusting a Metal instance, see [Upgrading an existing database to Metal](../../metal/create-a-metal-database.md#upgrading-an-existing-database-to-metal).

There are also special cases where you may want to temporarily upsize out of caution if you’re anticipating a large spike in traffic, such as during a launch or event. In these cases, you can easily [upsize](../../plans/planetscale-skus.md#upsizing-and-downsizing-clusters) ahead of your event, and then downsize after. Changing cluster sizes is a seamless operation that requires no downtime.

If you are switching between [network-attached storage](../../plans/planetscale-skus.md#network-attached-storage) (Amazon Elastic Block Storage or Google Persistent Disk) and [Metal](../../plans/planetscale-skus.md#metal), or changing the size of your Metal instance, be aware this switch takes additional time. Again, no downtime is required.

### Comparing PlanetScale to other database providers

If you are migrating from an existing cloud provider with resource-based pricing, be sure to compare your currently selected instance with our available cluster sizes.

Keep in mind, each database comes with a production branch with two replicas, as well as 1,440 hours worth of development branches. The development branches essentially equate to two extra “always on” databases. In many cases, you can deprecate your dev/staging databases that you pay extra for with other providers in favor of the development branches. In the end, this usually results in significant cost savings.

Databases in PlanetScale also come with additional beneficial infrastructure that is not easily configured or available in other hosted database solutions. For more information on what is provisioned with each database, read our [Architecture](../architecture.md) doc.

If you are unsure which plan or cluster size is right for your application, [contact us](https://planetscale.com/contact) to get further assistance.

Our self-serve plans are flexible enough to handle the majority of customers. However, there are several use cases where you may need a more custom plan. This is where our Enterprise offerings shine.

### Upsizing and downsizing clusters

As your application scales, upgrading or downgrading your database cluster is a seamless operation that does not involve any downtime.

To change cluster sizes, go to your PlanetScale dashboard, click on your database, click [“Clusters”](../cluster-configuration.md), select the new cluster size for the [keyspace](../sharding/keyspaces.md) you wish to configure, and click “Update”.

If you have [maintenance schedules](../../plans/managed/maintenance-schedules.md) enabled, changes to cluster size will roll out during your scheduled maintenance window.

The time it takes to change sizes depends on the size and region of your database. Larger databases may take 20 minutes to upsize/downsize. However, this is all done online, so you will not experience any downtime. Keep in mind, once you update your cluster size, you cannot change sizes again until the first size change completes.

When you choose to change cluster size on network-attached storage drives, we upgrade each of your replicas one by one: delete the tablet container, create a new tablet container of the new size, attach the persistent volume, start it up, and connect it to the primary. Once that’s complete, we fail the primary over to one of those new replicas, and do the same thing to the old primary.

On Metal, this works a little differently. Instead of upgrading replicas one by one, we spin up 3 new Metal instances in addition to your existing replicas. We catch the three new instances up by restoring a backup and then catching them up via MySQL replication. When those replicas are fully caught up, we reparent the primary to one of them, and begin to remove the old tablets. For this reason, you may notice a minimum of 5 replicas as you’re upgrading your Metal instance size.

## Sharding

You can create sharded keyspaces on any plan by adding a new sharded keyspace using the [Clusters page](../cluster-configuration.md) and running an [unsharded to sharded workflow](../sharding/sharding-quickstart.md) in your dashboard.

If you would like additional support from our expert team, our [Enterprise plan](../../planetscale-plans.md#enterprise-plan) may be a good fit. [Get in touch](https://planetscale.com/contact) for a quick assessment.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
