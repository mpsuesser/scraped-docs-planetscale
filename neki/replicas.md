---
url: https://planetscale.com/docs/neki/replicas
title: "Replicas"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Database replicas

> Use Neki replicas for high availability and read-only traffic.

Each Neki shard on a production branch has one primary Postgres instance and at least two replicas. The primary accepts writes. Replicas copy changes from the primary, can serve read-only queries, and can take over if the primary becomes unavailable.

A new production configuration profile starts with two replicas. You can add
replicas for a configuration profile on the **Clusters** page. The change
applies to every shard assigned to that profile. Each new replica is restored
from the shard's last backup, then joined to the primary to start
replication. See [Replicas and backups](backups.md#replicas-and-backups).

## High availability

Neki uses Postgres physical replication to copy write-ahead log (WAL) records
from each shard's primary to its replicas. If a primary becomes unavailable,
Neki can promote an eligible replica and update the shard topology to use the
new primary.

A failover can briefly interrupt queries or connections while the shard changes
primaries. Applications should retry transient connection and query failures.

If a replica needs a WAL segment that no source can supply, Neki fences that
replica from normal use. Once the shard primary is reachable, Neki restores
the replica from the last backup and the [admin](overview.md#the-admin)
joins it to the primary so replication can resume. This repairs the replica
without changing the shard primary.

Replication durability and read visibility are separate. WAL can be durable on
a replica before that replica has replayed the change. A successful write
therefore does not guarantee that an immediate replica read will return that
change. Send reads that require the latest committed data to the primary.

## Send reads to replicas

Neki sends reads to shard primaries by default.
To use replicas, select **Route queries to a replica** on the **Connect** page or set the Neki target when the connection starts.

For `psql`, use `PGOPTIONS`:

```bash theme={null}
PGOPTIONS='-c __neki.target=REPLICA' \
  psql 'host=<HOST> port=5432 user=<USERNAME> password=<PASSWORD> dbname=postgres sslnegotiation=direct sslmode=verify-full sslrootcert=system'
```

You can also change the target before starting a transaction:

```sql theme={null}
SET __neki.target = 'replica';
```

Replica connections are read-only. Inserts, updates, and deletes fail instead
of being sent to a primary. If no eligible replica is available for a
destination shard, the query also fails instead of falling back to that shard's
primary.

## How Neki chooses a replica

For each shard, the router starts with replicas that are serving
and have known replication lag. It then orders the candidates using recency,
locality, and session affinity.

| Policy   | Default behavior                                                                    |
| :------- | :---------------------------------------------------------------------------------- |
| Recency  | Prefer replicas with no more than 30 seconds of reported lag                        |
| Locality | Within the same lag tier, prefer a replica in the router's availability zone        |
| Affinity | For reads, choose again for each statement to spread reads across eligible replicas |

The default maximum reported lag is 15 minutes. A replica above that limit, or
one whose lag is unknown, is not eligible for reads. A replica between the
preferred and maximum thresholds is used only when no preferred replica is
available.

The `__neki.replica_recency`, `__neki.replica_locality`, and
`__neki.replica_affinity` settings can make these preferences stricter, disable
them, or keep a session on the same preferred replica. See [Choosing where
reads run](query-planning.md#choosing-where-reads-run) for the supported
values.

When a query reaches more than one shard, Neki chooses a replica separately for
each shard. Cross-shard transactions do not provide atomic commit across all
shards, so the combined result can reflect slightly different points in time.

## Replication lag and read consistency

Replication lag measures how far a replica is behind its primary. Zero seconds
means the replica has replayed everything it has received from the primary. A
higher value means recent changes may not be visible there yet.

Replica reads work well for dashboards, analytics, exports, and other read-heavy
workloads that can tolerate some delay. Use a primary connection for
read-after-write paths or any query that must return the newest committed data.

Replica reads do not guarantee
read-your-writes or monotonic reads. A later read can move to another
eligible replica and see older data than an earlier read.

Session affinity reduces how often a connection moves between replicas, but it
does not provide either guarantee. Neki can choose another replica when the
preferred one is unavailable, no longer meets the routing policy, or fails
before serving a query.

## Monitor replicas

The database dashboard shows replication lag on each replica card. A value of
`0s` means that replica is caught up. Each replica also has a chart shortcut
that opens the **Shards** tab of the **Metrics** page with that node selected.

If a replica has high or unknown lag, Neki can exclude it from read traffic.
Check the node's replication lag, health, CPU, memory, and IOPS on **Shards**,
then compare its disk, storage, and WAL activity on **Storage**.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
