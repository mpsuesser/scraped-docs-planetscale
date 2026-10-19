---
url: https://planetscale.com/docs/neki/read-only-replicas
title: "Read Only Replicas"
description: ""
access_date: 2026-10-19T17:45:20.278Z
current_date: 2026-10-19T17:45:20.278Z
---

A read-only replica is a Postgres instance in a shard that copies changes from the primary and serves reads. Neki never promotes a read-only replica to primary. Use read-only replicas for long-running reads, such as analytics, reports, exports, and batch jobs. This work then does not use the [replicas](replicas.md) that serve live read traffic and that take over when the primary fails.

Read-only replicas give these guarantees:

- A read-only replica never becomes the primary, during a failover or at any other time.
- A read-only replica does not acknowledge commits. It does not make a write more durable.
- A read-only replica rejects writes.
- A session reads from read-only replicas only when it sets the `rdonly` target. Neki does not send other traffic to them.

## Read-only replicas and replicas

Neki uses the name `rdonly` for read-only replicas in routing targets and node roles. The two types of instance compare as follows:

|  | Replica | Read-only replica |
| --- | --- | --- |
| Can become the primary | Yes | No |
| Counts for write durability | Yes | No |
| Routing target | `replica` | `rdonly` |
| Node role in the API | `replica` | `rdonly` |
| Count per shard | At least 2 on a production branch | 0 to 8 |
| Cluster size and storage | From the configuration profile | From the configuration profile |

Both types use Postgres physical replication from the shard primary. After a failover, both types replicate from the new primary.

Read-only replicas add read capacity. They do not add availability. A shard can fail over only to a replica. For example, a development branch with one primary and two read-only replicas has no failover target.

## Add read-only replicas

The [configuration profile](cluster-configuration.md) sets the number of read-only replicas. Every shard assigned to the profile gets that number of read-only replicas. The count does not include the primary or the replicas.

To set the count in the dashboard:

You can also set the count when you create a configuration profile.

To set the count with the [PlanetScale CLI](../cli/branch.md), use the `--read-only-replicas` flag:

```shellscript
pscale branch config-profile update <DATABASE_NAME> <BRANCH_NAME> <PROFILE_NAME> --read-only-replicas 2
```

`pscale branch config-profile create` accepts the same flag. In the [PlanetScale API](../api/reference/update_shard_configuration_profile.md), the field is `read_only_replicas`.

An update creates a change request, as other configuration profile changes do. Use `pscale branch config-profile changes list` to follow the change.

These rules apply to the count:

- The value is 0 to 8 for each shard.
- Read-only replicas count toward the node limit of the cluster. A change that goes above the limit fails.
- PlanetScale spreads the read-only replicas across the availability zones of the shard. For example, two read-only replicas on a production branch go into two different availability zones.

A change to the read-only replica count does not move the primary. The change does not restart the primary or the replicas. Neki adds or removes only read-only replicas. To remove all read-only replicas, set the count to `0`.

## Send reads to read-only replicas

Neki sends reads to shard primaries by default. To use read-only replicas, select **Read-only replica** under **Query target** on the **Connect** page. The dashboard then adds `-c __neki.target=RDONLY` to the generated connection options. The **Read-only replica** option shows only when the branch has read-only replicas.

You can also set the Neki target to `rdonly` when the connection starts. For `psql`, use `PGOPTIONS`:

```shellscript
PGOPTIONS='-c __neki.target=RDONLY' \
  psql 'host=<HOST> port=5432 user=<USERNAME> password=<PASSWORD> dbname=postgres sslnegotiation=direct sslmode=verify-full sslrootcert=system'
```

You can also change the target before you start a transaction:

```sql
SET __neki.target = 'rdonly';
```

The target value is not case-sensitive.

These rules apply to a session with the `rdonly` target:

- Inserts, updates, deletes, and other writes fail with SQLSTATE `25006` (`read_only_sql_transaction`).
- If no eligible read-only replica is available for a destination shard, the query fails. Neki does not send the query to a replica or to the primary.
- Neki selects a read-only replica with the same recency, locality, and affinity policy that it uses for replicas. See [How Neki chooses a replica](replicas.md#how-neki-chooses-a-replica).

The replication lag and read consistency rules for replicas also apply to read-only replicas. See [Replication lag and read consistency](replicas.md#replication-lag-and-read-consistency).

## Changes and maintenance

A read-only replica uses the cluster size, storage, and Postgres parameters of its configuration profile. Some changes to the profile, such as a new cluster size, restart or replace each read-only replica. Queries with the `rdonly` target can fail while their read-only replica restarts. If your application uses the `rdonly` target, retry failed connections and queries.

If a shard has only one read-only replica, queries with the `rdonly` target fail while that read-only replica is not available. If your application cannot accept these failures, use two or more read-only replicas.

## Limitations

- A read-only replica has the same cluster size and storage as the other instances of its configuration profile. You cannot select a different size for read-only replicas.
- A restore from a backup does not copy the read-only replica count. The new branch has no read-only replicas. Add them after the restore.

## Pricing

PlanetScale bills each read-only replica at the additional-replica rate of the cluster size. The two replicas included with the cluster size do not cover read-only replicas. Each read-only replica on network-attached storage has its own volume, billed as an additional replica volume. See [Additional replicas](pricing.md#additional-replicas).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
