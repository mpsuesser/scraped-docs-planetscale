---
url: https://planetscale.com/docs/vitess/sharding/keyspaces
title: "Keyspaces"
description: ""
access_date: 2026-10-22T18:21:49.469Z
current_date: 2026-10-22T18:21:49.469Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# What is a keyspace

> In Vitess, a keyspace is a logical database that maps to one or many MySQL instances.

Keyspaces are used to group MySQL instances, typically to shard certain tables in your database cluster. While a keyspace may contain several MySQL instances across many shards, to your application, it will appear as a single database.

All PlanetScale Vitess clusters have one or more keyspaces.

## Unsharded keyspaces

If you only have a single, unsharded keyspace in your database cluster, then your keyspace maps directly to your database cluster in PlanetScale. For example, if you have an unsharded PlanetScale database named `gymtracker` with the default primary and 2 replicas, you likely only have a single keyspace. You can reference this keyspace in the same way you normally would your database:

```sql theme={null}
use gymtracker;
show tables;
```

Unless your database cluster is sharded or you have created multiple unsharded keyspaces (uncommon), you don't necessarily need to understand the concept of a keyspace. Keyspaces become important once you start to consider sharding.

## Sharded keyspaces

As your database grows, you may wish to shard some tables in your database cluster. To do this, you will create a new keyspace and add the shards to the sharded keyspace.

The following diagram depicts a PlanetScale database with 2 keyspaces: one unsharded and one sharded. The unsharded keyspace has the default 1 primary and 2 replicas. The sharded keyspace contains two shards, each with 1 primary and 2 replicas.

![Keyspace diagram](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/sharding/keyspace-diagram.png?fit=max\&auto=format\&n=Lta43VIYjNTnQ47e\&q=85\&s=2df1ab1c1dd28419f3ec9293e554d6a8)

When a new request comes in, it first goes through our Global Edge Network layer. This Edge layer manages the connection and sends it to the correct Vitess cluster in PlanetScale. From here, the VTGates will parse the incoming query and determine the correct keyspace and shard to route it to.

If you want to directly target a keyspace, you can do that with the `use` syntax, but this time, specify the keyspace name. For example, if we created a sharded keyspace for `gymtracker`, we may call it `gymtracker_sharded`. You can directly target the keyspace with the following:

```sql theme={null}
use gymtracker_sharded;
show tables;
```

## Routing queries in your application

One of the many perks of Vitess/PlanetScale is that you can distribute your data across hundreds of MySQL instances without complicating your application code. As mentioned earlier, all of these keyspaces and shards appear as a single MySQL instance to your application. Once you add multiple keyspaces, you no longer need to specify a database name in your application's database connection configuration code. Our Global Edge Network will correctly route you to the correct Vitess cluster, and the VTGates in your cluster will handle routing the request to the correct keyspace and shard.

Some frameworks and ORMs require a database name is specified. In these scenarios, you can set the database name to `@primary`, and your requests will be automatically routed to the correct keyspace/shard. Alternatively, if you have specific queries that you wish to send to replicas, you can use `@replica`.

## Modifying keyspaces in PlanetScale

Having 1 unsharded keyspace and 1 sharded is a typical setup for a database that needs sharding. On the [Clusters page](https://planetscale.com/docs/vitess/cluster-configuration.md), you are able to customize the number of shards in the sharded keyspace. You can also adjust the instance size for each primary and replica, and you can add additional replicas beyond the default of two if needed. On a sharded keyspace, you can enable [per-shard sizing](https://planetscale.com/docs/vitess/cluster-configuration/per-shard-sizing.md) to size individual shards separately.

To get a better sense of this, or to configure your keyspaces, click on your [Clusters](https://planetscale.com/docs/vitess/cluster-configuration.md) tab in your dashboard. If you have an existing unsharded database, you'll see that database listed there as an unsharded keyspace. If you click "New keyspace", you're able to configure a brand new keyspace here. Production branches can also add an [external keyspace](https://planetscale.com/docs/vitess/cluster-configuration.md#create-an-external-keyspace) that connects to an existing MySQL database.

The most common use case for creating a new keyspace is to shard one or multiple tables.

All of your keyspaces are separate databases. And, again, sharded keyspaces hold multiple databases. However, with the power of Vitess, your application views these all as a single database. It uses the VTGate load balancer to route queries to the correct keyspace, and then the correct shard, and finally the correct primary or replica, as configured.

For more information about modifying a keyspace, please see the [Cluster configuration documentation](https://planetscale.com/docs/vitess/cluster-configuration.md).

## Limitations

PlanetScale currently supports a maximum of 2048 tables per keyspace. If you exceed this limit, you may want to consider sharding — [distributing your tables across multiple keyspaces](https://planetscale.com/docs/vitess/sharding.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
