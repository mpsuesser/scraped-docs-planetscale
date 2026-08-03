---
url: https://planetscale.com/docs/postgres/scaling/replicas
title: "Replicas"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Overview

Replicas serve two main purposes:

- They provide a way to reduce load on your primary instance by allowing you to read from a replica.
- They increase database availability by enabling fast failovers for maintenance or unexpected failure.

Before utilizing replicas for reducing load on the primary, it’s important to understand the trade-offs. For more information, see the [Data consistency and replication lag](#data-consistency-and-replication-lag) section.

## How to query Postgres replicas

Postgres replicas can be used to read data and reduce load on the primary. PlanetScale does not automatically route queries to replicas unless you explicitly use a replica credential or tell your application to do so.

**PlanetScale’s use of PgBouncer (port `6432`) does not support replica routing.** All connections through PgBouncer are automatically routed to the primary database, regardless of the username specification. Use direct connections (port `5432`) for replica access.

To query a replica:

1. Append `|replica` to the end of your username for the branch you want to target:

```shellscript
user=postgres.cnlmx96ec5kw|replica
```

If you’re using a URI-formatted connection string (for example, `DATABASE_URL`), URL-encode the pipe character in the username suffix. Use `%7Creplica` instead of `|replica`.

```text
postgresql://postgres.cnlmx96ec5kw%7Creplica:pscale_pw_xxx@host:5432/my_database?sslmode=verify-full&sslrootcert=/etc/ssl/certs/ca-certificates.crt
```

1. Make sure the port in your connection string is set to `5432`. To pool connections to replicas, use a [dedicated replica PgBouncer](../connecting/pgbouncer.md#dedicated-replica-pgbouncers) instead.

Your connection string should look something like this:

```shellscript
psql 'host=xxxxxxxxxx-useast1-1.horizon.psdb.cloud \
      port=5432 \
      user=postgres.xxxxxxxxxx|replica \
      password=pscale_pw_xxxxxxxxxxxxxxxxxx \
      dbname=my_database \
      sslnegotiation=direct \
      sslmode=verify-full \
      sslrootcert=system'
```

## High availability

Replicas within PlanetScale are used to enable high availability of your database. This is a part of the reason all production branches (excluding [single node](../cluster-configuration/single-node.md)) in PlanetScale are provided 2 replicas. In situations where the underlying hardware or service hosting the primary Postgres node fails, our system will automatically elect a new primary node from the available replicas and reroute traffic to that new primary. This process is known as **reparenting** and typically is all handled within milliseconds or seconds.

If you’re using [PgBouncer](../connecting/pgbouncer.md), querying the primary during a reparent typically goes unnoticed, other than a bit of additional query latency. This is because PlanetScale’s PgBouncer buffers queries under the hood during failovers and directs them to the new primary once the failover is complete.

## Multiple availability zones

In cloud architecture, regions are further broken down into logical groupings of data centers known as availability zones (or AZs for short). For example, the `us-east-1` region on AWS contains multiple AZs available to customers starting with `us-east-1a` through `us-east-1f`. The infrastructure for your PlanetScale Postgres database cluster is distributed across 3 availability zones. In the instance of an AZ failure, your database will automatically failover to an available AZ.

## Data consistency and replication lag

PlanetScale Postgres utilizes a consistency model in which data committed (`INSERT`, `UPDATE`, `DELETE`, etc) on the primary must be durably stored on and confirmed by at least one replica before the primary reports the commit succeeded to client.

This consistency model does mean that it’s possible for a commit to be visible to other clients/transactions on the primary or the replicas, before the primary reports commit success back to the client.

For development branches there is no data replication.

The delay between when a change is applied to a primary and the same change is applied to a replica is known as `replication lag`. Your database’s replication lag is viewable on your database main “Dashboard” page.

It is important to be aware of replication lag whenever querying data from your replicas. For example, if you make an update and then immediately try to query for that updated data via a replica, it may not be available yet due to replication lag.

## When should you use replicas in your application?

Replicas are useful for offloading read-heavy workloads from the primary node. By using replicas, you can distribute the read load across multiple nodes, which can help improve the performance of your application. Some examples of where you might want to query a replica are: scheduled jobs, analytics jobs, search features, or aggregate queries. Replicas can also be used to provide a read-only view of your data to users or applications that do not need to write data, such as when a user is logged out or writing one-off queries for debugging purposes.

## Configuring replicas for your database cluster

By default, production databases (excluding [single node](../cluster-configuration/single-node.md)) are created with 2 replicas. You may add additional replicas if you need to scale your read traffic. Adding additional replicase beyond the default does not guarantee an increase in availability of your database.

You are charged for additional replicas you add beyond the default. Billing for additional replicas begins once the replica change has completed. The additional cost is ~ 1/3 the original price of the base cluster running (initial primary + 2 replicas).

To see how to increase and manage the number of additional replicas you have, see [Cluster configuration](../cluster-configuration.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
