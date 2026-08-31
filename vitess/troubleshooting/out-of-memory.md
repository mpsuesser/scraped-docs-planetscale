---
url: https://planetscale.com/docs/vitess/troubleshooting/out-of-memory
title: "Out Of Memory"
description: ""
access_date: 2026-08-31T07:29:59.083Z
current_date: 2026-08-31T07:29:59.083Z
---

An out of memory (OOM) event occurs when a process on a tablet consumes more memory than is available on the cluster. When this happens, the affected process is automatically restarted to free up memory.

Each tablet runs two main processes in separate containers: **`vttablet`** (the query-serving proxy) and **`mysqld`** (the MySQL server). Either can run out of memory independently. It is possible for `vttablet` to be OOM-killed under load while `mysqld` memory looks stable, so overall pod memory can appear healthy even during an OOM.

## Impact and risks

When an OOM event occurs:

- **Process restart required**: The affected container (`vttablet` or `mysqld`) is restarted to recover. Repeated OOMs show up as a container that keeps restarting (`CrashLoopBackOff`).
- **Brief unavailability**: The affected tablet is unavailable while it restarts. If a shard’s primary is repeatedly OOM-killed, writes to that shard can be disrupted until it recovers or is resized.
- **In-flight queries fail**: Queries and uncommitted transactions running on the affected tablet at the time of the restart are terminated.

## Primary resolution: Upgrade cluster size

The most direct solution to OOM events is to increase your cluster size to provide more memory. See [cluster sizing](../scaling/cluster-sizing.md) for the available sizes and their memory, and [cluster configuration](../scaling/cluster-configuration.md) for how to resize. Resizing is performed with minimal downtime.

## Common causes of high memory usage

### Heavy write or bulk operations

Large writes, deletes, or backfills can drive `vttablet` memory up sharply. A single large `DELETE`, in particular, can hold a lot of state in memory while it runs, so breaking these into smaller batches keeps memory bounded.

**Recommendations:**

- Break large `DELETE` / `UPDATE` /backfill operations into smaller batches.
- Avoid running several large bulk operations concurrently.

### Memory-intensive queries

Certain query patterns can consume large amounts of memory, for example large `ORDER BY` sorts, `GROUP BY` / `DISTINCT` over large result sets, hash joins on large tables, and queries that return very large result sets.

**Recommendations:**

- Add indexes so sorts and lookups don’t have to be done in memory.
- Use `LIMIT` and pagination instead of returning large result sets at once.

### Too many connections

Each connection consumes memory. A large number of open connections, whether intentional or due to connection leaks, increases memory pressure, especially on smaller cluster sizes.

**Recommendations:**

- Ensure your application closes connections and uses a connection pool.
- Reduce the number of concurrent connections where possible.

## Monitoring and prevention

- **Watch for the OOM banner**: When a branch experiences OOM kills, a banner on the branch dashboard shows how many occurred in the selected period.
- **Scrape it into your own monitoring**: The `planetscale_pods_container_restarts_total` metric carries a `planetscale_restart_reason` label; filter on `planetscale_restart_reason="OOMKilled"` to alert on OOMs. See the [Prometheus metrics](../integrations/prometheus-metrics.md) reference.

Some workloads spike memory so quickly that the increase is not captured on the memory graph, which is sampled periodically. An OOM kill is recorded as an event, so it remains visible even when no corresponding spike appears on the memory graph.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
