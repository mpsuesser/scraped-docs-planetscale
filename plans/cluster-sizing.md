---
url: https://planetscale.com/docs/plans/cluster-sizing
title: "Cluster Sizing"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

You can easily upsize and downsize your database cluster from within the PlanetScale dashboard. This documentation covers some information about selecting a cluster size upon database creation as well as how to upsize and downsize.

If you are on a consumption commitment plan, please be aware that any changes in cluster size will be reflected against your monthly or annual consumption commitment amount. Changes to the originally selected cluster size may cause you to utilize this amount either more quickly or slowly. If you have further questions, please reach out to your account manager or our [Support](https://planetscale.com/contact) team.

## Selecting a cluster size

Selecting the correct cluster size for your database can have a dramatic impact on how it performs and how much it costs.

A good rule of thumb is when you notice CPU usage is consistently at or close to 100% for an extended period of time, you may benefit from [upsizing your cluster](planetscale-skus.md#upsizing-and-downsizing-clusters). Conversely, if your CPU usage is consistently below 50%, you may be able to downsize. You can monitor your CPU usage by clicking on your database, clicking “Primary” in your architecture diagram, and referencing the chart under “Metrics and performance”.

For Metal instances, you have to consider both the compute and the storage, as storage does not autoscale. For more information about adjusting a Metal instance, see [Upgrading an existing database to Metal](../metal/create-a-metal-database.md#upgrading-an-existing-database-to-metal).

There are also special cases where you may want to temporarily upsize out of caution if you’re anticipating a large spike in traffic, such as during a launch or event. In these cases, you can easily [upsize](planetscale-skus.md#upsizing-and-downsizing-clusters) ahead of your event, and then downsize after.

If you are switching between [network-attached storage](planetscale-skus.md#network-attached-storage) (Amazon Elastic Block Storage or Google Persistent Disk) and [Metal](planetscale-skus.md#metal), or changing the size of your Metal instance, be aware this switch takes additional time.

### Comparing PlanetScale to other database providers

If you are migrating from an existing cloud provider with resource-based pricing, be sure to compare your currently selected instance with our available cluster sizes.

Keep in mind, each database comes with a production branch with two replicas. Vitess databases include 1,440 hours worth of development branches. The development branches essentially equate to two extra “always on” databases. In many cases, you can deprecate your dev/staging databases that you pay extra for with other providers in favor of the development branches. In the end, this usually results in significant cost savings.

Databases in PlanetScale also come with additional beneficial infrastructure that is not easily configured or available in other hosted database solutions. For more information on what is provisioned with each database, read our [Vitess Architecture](../vitess/architecture.md) and [Postgres Architecture](../vitess/architecture.md) docs.

If you are unsure which plan or cluster size is right for your application, [contact us](https://planetscale.com/contact) to get further assistance.

Our self-serve plans are flexible enough to handle the majority of customers. However, there are several use cases where you may need a more custom plan. This is where our Enterprise offerings shine.

## Sharding with Vitess

You can create sharded Vitess keyspaces on any plan by adding a new sharded keyspace using the [cluster configuration page](../vitess/cluster-configuration.md) and running an [unsharded to sharded workflow](../vitess/sharding/sharding-quickstart.md) in your dashboard.

If you would like additional support from our expert team, our [Enterprise plan](../planetscale-plans.md#enterprise-plan) may be a good fit. [Get in touch](https://planetscale.com/contact) for a quick assessment.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
