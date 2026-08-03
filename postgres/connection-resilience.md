---
url: https://planetscale.com/docs/postgres/connection-resilience
title: "Connection Resilience"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Connection resilience guide

> Configure PlanetScale Postgres for maximum availability in demanding OLTP workloads.

**Platform availability:** Postgres only

## Summary

* Use dedicated PgBouncers (not local) for production OLTP
* Connect via port 6432 with `username|bouncer-name` format
* Keep transactions under 3 seconds for clean failovers
* Add retry logic for read queries to handle transient failures

## Overview

Production teams running OLTP workloads need connection strategies that maximize availability during failovers and planned maintenance. This guide covers how to configure PlanetScale Postgres for resilience and what to expect during failovers.

For background on PlanetScale Postgres cluster architecture, see [operations philosophy](operations-philosophy.md).

## Connection interruption

Postgres design dictates connection interruptions for several reasons:

* **Parameter changes** — Changing certain [parameters](cluster-configuration/parameters.md) requires connection interruption.
* **Cluster resizing** — If you're upgrading which cluster size you're running on, or the amount of CPU/memory, connections will be interrupted as we move the cluster to new hardware.
* **Enabling extensions** — The design of the extension ecosystem means most new extensions need connection interruption.
* **PlanetScale routine maintenance** — Very occasionally, PlanetScale has to perform routine maintenance on your database (e.g. for operating system updates and other infrastructure changes). We notify your organization's admin contacts at least 24 hours before scheduled maintenance (typically weeks beforehand).
* **PlanetScale emergency maintenance** — Critical security vulnerabilities may require immediate patching to protect your data. These patches are deployed as soon as possible and may not include advance notice.

Many applications don't tolerate a Postgres connection being closed. Typically this results in an HTTP 500 error and an error in your exception tracker/logs.

## Use Dedicated PgBouncer

PlanetScale Postgres offers one connection path that is significantly more resilient in the face of maintenance and other failovers, which is a dedicated PgBouncer. We strongly recommend production customers use a dedicated PgBouncer. A dedicated PgBouncer allows us to turn all planned failovers into a brief period of elevated latency with no connection drops (using `PAUSE`).

It's worth noting that PgBouncer has several important compatibility problems that can cause unexpected application issues. See [transaction pooling limitations](connecting/pgbouncer.md#limitations-of-transaction-pooling).

See the [PgBouncer documentation](connecting/pgbouncer.md) for configuration options and the [connections overview](connecting.md) for a comparison of all connection methods.

If you have replica traffic that is production critical, we also recommend using dedicated PgBouncers for your replicas.

Without a dedicated PgBouncer, every connection interruption means every client connection gets terminated. Your application must detect the closed connection, reconnect, and retry the failed query. During a failover, this can cause a burst of errors and retries across your entire application fleet.

If you cannot use a dedicated PgBouncer due to compatibility issues, you will see errors during failovers.
We recommend tuning timeouts (see below), but also to expect brief error windows during these events.

### Dedicated bouncer maintenance

Configuration changes to dedicated bouncers are very safe. When you modify a dedicated bouncer's settings, we create a new bouncer with the updated configuration and drain connections from the old one over 24 hours. New connections go to the new bouncer immediately; existing connections continue on the old bouncer until they disconnect naturally or the drain period ends.

This means:

* Bouncer configuration changes cause no connection drops for well-behaved clients
* Clients that hold connections indefinitely (beyond 24 hours) will eventually be disconnected
* Long-lived connections will continue using old settings until they reconnect

## Failover behavior

### What happens during a failover

PlanetScale failovers typically complete in seconds. See [operations philosophy](operations-philosophy.md#minimizing-the-impact-of-configuration-changes) for details on our failover approach. During a planned failover (e.g. maintenance, parameter changes, etc.), we can use `PAUSE` with a dedicated PgBouncer to replace connection interruptions with a latency increase on starting new transactions.

### How PAUSE keeps connections alive

Dedicated PgBouncers use PgBouncer's `PAUSE` command during failovers to maintain client connections:

1. PAUSE stops assigning new transactions to backend Postgres connections
2. New queries queue in PgBouncer rather than erroring
3. Once the new primary/replica is ready, RESUME allows queued queries to execute

Clients see a brief latency spike while queries are paused, but their connections stay alive. They don't need to reconnect or retry.

## Long-running queries and failovers

### Why long queries delay failovers

Fast failovers depend on clean primary shutdowns. When you change a configuration or PlanetScale needs to failover, PlanetScale asks Postgres to shut down gracefully. If queries are still running, Postgres waits for them to complete. Long-running queries can extend unavailability from a few seconds to 30 seconds (at which point we hard cancel queries).

### Keeping transactions short

For OLTP workloads, keep transactions as short as possible (at least under 3 seconds, but ideally a few milliseconds max per transaction for performance-critical workloads).

Move network calls, external API requests, and heavy computation outside the transaction boundary. Do that work before `BEGIN` or after `COMMIT`.

Use `statement_timeout` to bound how long individual queries can run. Route analytical queries and batch jobs to replicas where they won't block failovers on the primary.

## Monitoring query latency

Use [Query Insights](monitoring/query-insights.md) to identify slow queries that could block failovers.

## Timeouts

### Postgres timeouts

Use Postgres timeouts to prevent runaway queries from blocking failovers:

* **`statement_timeout`** — Aborts any statement that takes longer than the specified duration. Set this to bound individual query execution time.
* **`transaction_timeout`** — Aborts any transaction that exceeds the specified duration. This catches cases where an application opens a transaction but takes too long between statements (e.g. due to network calls or slow application logic within the transaction).
* **`idle_in_transaction_session_timeout`** — Terminates any session that has been idle inside an open transaction for longer than the specified duration. This is an important safety net for preventing abandoned or forgotten transactions from holding locks and blocking other writes. If your application occasionally leaves transactions open due to unhandled exceptions or network issues, this timeout will clean them up automatically.

Treat these as session-level controls. Set them at the role level with `ALTER ROLE ... SET`:

```sql theme={null}
ALTER ROLE app_user SET statement_timeout = '2s';
ALTER ROLE app_user SET transaction_timeout = '3s';
ALTER ROLE app_user SET idle_in_transaction_session_timeout = '30s';
```

### Application timeouts

Configure timeouts in your connection pool and database driver:

* **Connection timeout** — How long to wait when establishing a new connection. Set this low enough to fail fast during outages rather than queueing requests indefinitely.
* **Query timeout** — Application query timeout should be set slightly higher than your Postgres `statement_timeout` to allow Postgres to cancel the query cleanly.
* **Pool checkout timeout** — How long to wait for a connection from the pool. During failovers, this determines how long requests queue before failing.

Most connection pools also support health checks (e.g. `test on borrow`) to detect stale connections before using them.

## Retries

Adding retry logic to your application improves availability by handling transient failures from network issues, brief unavailability windows, and connection drops. Read queries are safe to retry since they don't modify data. Write queries require more care—only retry if you can verify the original request didn't complete, or if the operation is idempotent.

For read-heavy workloads, implementing retries in your database client or application layer can significantly reduce user-visible errors during failovers and intermittent network issues.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
