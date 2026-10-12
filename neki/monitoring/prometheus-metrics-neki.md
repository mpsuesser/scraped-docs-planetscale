---
url: https://planetscale.com/docs/neki/monitoring/prometheus-metrics-neki
title: "Prometheus Metrics Neki"
description: ""
access_date: 2026-10-12T17:26:53.947Z
current_date: 2026-10-12T17:26:53.947Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Prometheus metrics for PlanetScale Neki

> PlanetScale Neki exposes a Prometheus-compatible endpoint per branch that lets you scrape metrics for routers, sidecars, and Postgres instances.

**Platform availability:** [Vitess](../../vitess/integrations/prometheus-metrics.md) and [Postgres](../../postgres/monitoring/prometheus-metrics-postgres.md)

## Overview

PlanetScale serves a Prometheus-compatible metrics endpoint for each Neki branch. Prometheus finds these endpoints through HTTP service discovery. The same discovery list covers every active branch in your organization, including Neki branches.

If you use Datadog, the Datadog agent can scrape the same Prometheus endpoint.

### Connect Prometheus

You need Prometheus 3.x, or a Prometheus-compatible engine such as VictoriaMetrics, that supports [HTTP SD](https://prometheus.io/docs/prometheus/latest/http_sd/).

1. Create a [service token](../../api/reference/service-tokens.md) in your organization settings. Save the token ID and the token.
2. Grant the token the `read_metrics_endpoints` permission.
3. Add a scrape configuration. Replace `${ORG}`, `${TOKEN_ID}`, and `${TOKEN}` with your values:

```yaml theme={null}
scrape_configs:
  - job_name: "${ORG}-neki"
    http_sd_configs:
    - url: https://api.planetscale.com/v1/organizations/${ORG}/metrics
      authorization:
        type: "token"
        credentials: "${TOKEN_ID}:${TOKEN}"
      refresh_interval: 10m
```

Each discovered target carries a signature that expires after one day. Keep the refresh interval short so Prometheus always holds a valid signature.

Each target returns the metrics of one branch. Prometheus attaches the `planetscale_organization_name`, `planetscale_database_name`, and `planetscale_branch_name` target labels, so you can tell branches apart. Every series also carries `planetscale_database_branch_id`.

For managed Prometheus services that do not scrape, run Prometheus yourself and forward the metrics with `remote_write`.

## Metrics

PlanetScale Neki emits the following metrics to be scraped. Every metric name starts with `planetscale_`.

A histogram appears as three series with the suffixes `_bucket`, `_sum`, and `_count`. Counters and gauges appear under the name shown.

## Router Metrics

Routers accept application connections, plan each statement, and send work to shards. These metrics describe the statements that routers handle.

| **Name & Description** | **Type** | **Tags** |
| :- | :- | :- |
| **planetscale\_neki\_router\_queries\_total**  The total number of queries routed, by query type | Counter | cluster, database, planetscale\_cell, planetscale\_cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_router, query\_type |
| **planetscale\_neki\_router\_query\_duration\_seconds**  The distribution of query latency, in seconds, through the router | Histogram | cluster, database, le, planetscale\_cell, planetscale\_cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_router |
| **planetscale\_neki\_router\_query\_errors\_total**  The total number of query errors returned by the router, by error class | Counter | cluster, database, planetscale\_cell, planetscale\_cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_router, error\_class |

## Sidecar Pool Metrics

Each sidecar keeps a connection pool to its Postgres instance for each database. These metrics describe the pool. Use them to see when clients wait for a connection.

| **Name & Description** | **Type** | **Tags** |
| :- | :- | :- |
| **planetscale\_neki\_sidecar\_pool\_capacity**  The configured maximum number of connections in a sidecar pool | Gauge | cluster, database, planetscale\_cell, planetscale\_cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_neki\_sidecar\_pool\_connections\_active**  The number of open connections in a sidecar pool, checked out or idle | Gauge | cluster, database, planetscale\_cell, planetscale\_cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_neki\_sidecar\_pool\_connections\_in\_use**  The number of connections in a sidecar pool that clients currently hold | Gauge | cluster, database, planetscale\_cell, planetscale\_cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_neki\_sidecar\_pool\_connections\_available**  The number of idle connections in a sidecar pool that a client can take | Gauge | cluster, database, planetscale\_cell, planetscale\_cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_neki\_sidecar\_pool\_connections\_waiting**  The number of clients waiting for a connection from a sidecar pool | Gauge | cluster, database, planetscale\_cell, planetscale\_cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_neki\_sidecar\_pool\_waits\_total**  The number of checkouts that waited for a pooled Postgres connection | Counter | cluster, database, planetscale\_cell, planetscale\_cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_neki\_sidecar\_pool\_wait\_seconds\_total**  The total seconds that checkouts spent waiting for a pooled Postgres connection. Recorded only once a checkout has waited | Counter | cluster, database, planetscale\_cell, planetscale\_cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |

## Postgres Metrics

These metrics come from the Postgres instances in each shard. Series from a primary carry `planetscale_role="primary"`. Series from a replica carry `planetscale_role="replica"`.

| **Name & Description** | **Type** | **Tags** |
| :- | :- | :- |
| **planetscale\_postgres\_connection\_state**  The count and state of Postgres connections | Gauge | cluster, planetscale\_cell, planetscale\_component, planetscale\_connection\_state, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_postgres\_wal\_archiver\_succeeded\_count**  The count of successfully archived WALs. Primary only | Gauge | cluster, planetscale\_cell, planetscale\_component, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_postgres\_wal\_archiver\_failed\_count**  The count of unsuccessfully archived WALs. Primary only | Gauge | cluster, planetscale\_cell, planetscale\_component, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_postgres\_wal\_archiver\_last\_age\_succeeded**  The age of the last successfully archived WAL. Primary only | Gauge | cluster, planetscale\_cell, planetscale\_component, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_postgres\_wal\_archiver\_lag\_bytes**  WAL bytes written but not yet archived (current LSN minus last archived LSN). Primary only | Gauge | cluster, planetscale\_cell, planetscale\_component, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_postgres\_wal\_size\_bytes**  The cumulative disk size of WALs waiting to be archived | Gauge | cluster, planetscale\_cell, planetscale\_component, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_postgres\_settings\_max\_connections**  The current value of the max\_connections setting | Gauge | cluster, planetscale\_cell, planetscale\_component, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_postgres\_settings\_max\_wal\_size\_bytes**  The current value of the max\_wal\_size\_bytes setting | Gauge | cluster, planetscale\_cell, planetscale\_component, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_postgres\_replica\_lag\_seconds**  Replica lag in fine-grained seconds from Postgres. Replicas only | Gauge | cluster, planetscale\_cell, planetscale\_component, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_postgres\_locks**  The count of current lock modes | Gauge | cluster, planetscale\_cell, planetscale\_component, planetscale\_database\_branch\_id, planetscale\_lock\_mode, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_postgres\_database\_xact\_commit\_total**  The total committed transactions on Postgres databases | Counter | cluster, planetscale\_cell, planetscale\_component, planetscale\_database\_branch\_id, planetscale\_database\_name, planetscale\_pod, planetscale\_role, planetscale\_shard |

## Backup Metrics

| **Name & Description** | **Type** | **Tags** |
| :- | :- | :- |
| **planetscale\_backup\_restore\_active**  Whether a backup restore is active, tagged with the current phase (1 = active) | Gauge | cluster, planetscale\_component, planetscale\_database\_branch\_id, planetscale\_phase, planetscale\_pod, planetscale\_shard |
| **planetscale\_backup\_fetch\_percent**  The percentage progress of a backup fetch operation (0-100) | Gauge | cluster, planetscale\_component, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_shard |

## Infrastructure Metrics

These metrics describe the pods that run your branch. Router pods carry `planetscale_router`. Sidecar pods carry `planetscale_shard` and `planetscale_role`.

| **Name & Description** | **Type** | **Tags** |
| :- | :- | :- |
| **planetscale\_pods\_cpu\_util\_percentages**  The CPU utilization percentage of router and sidecar pods | Gauge | cluster, planetscale\_cell, planetscale\_component, planetscale\_container, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_router, planetscale\_shard |
| **planetscale\_pods\_mem\_util\_percentages**  The memory utilization percentage of router and sidecar pods | Gauge | cluster, planetscale\_cell, planetscale\_component, planetscale\_container, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_router, planetscale\_shard |
| **planetscale\_pods\_iops\_total**  The total IOPS (Input/Output Operations Per Second) of sidecar pods | Counter | cluster, planetscale\_cell, planetscale\_component, planetscale\_container, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_volume\_available\_bytes**  The available storage space in bytes on Postgres volumes | Gauge | cluster, planetscale\_cell, planetscale\_component, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_volume\_capacity\_bytes**  The total storage capacity in bytes on Postgres volumes | Gauge | cluster, planetscale\_cell, planetscale\_component, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_pods\_mem\_rss\_bytes**  The RSS memory usage in bytes of sidecar pods | Gauge | cluster, planetscale\_cell, planetscale\_component, planetscale\_container, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_pods\_mem\_mmap\_bytes**  The memory-mapped file usage in bytes of sidecar pods | Gauge | cluster, planetscale\_cell, planetscale\_component, planetscale\_container, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_pods\_mem\_active\_cache\_bytes**  The active cache memory in bytes of sidecar pods | Gauge | cluster, planetscale\_cell, planetscale\_component, planetscale\_container, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_pods\_mem\_inactive\_cache\_bytes**  The inactive cache memory in bytes of sidecar pods | Gauge | cluster, planetscale\_cell, planetscale\_component, planetscale\_container, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_shard |
| **planetscale\_pods\_container\_status\_restarts\_total**  The total container restarts, across all restart reasons. Join **planetscale\_pods\_container\_last\_terminated\_reason** to attribute them | Counter | cluster, planetscale\_cell, planetscale\_component, planetscale\_container, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_router, planetscale\_shard |
| **planetscale\_pods\_container\_last\_terminated\_reason**  The reason a container was last terminated (1 = the current reason). Join against **planetscale\_pods\_container\_status\_restarts\_total** to attribute restarts to a reason | Gauge | cluster, planetscale\_cell, planetscale\_component, planetscale\_container, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_restart\_reason, planetscale\_router, planetscale\_shard |
| **planetscale\_pods\_container\_waiting\_reason**  The reason a container is waiting, such as CrashLoopBackOff or ImagePullBackOff | Gauge | cluster, planetscale\_cell, planetscale\_component, planetscale\_container, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_router, planetscale\_shard, planetscale\_waiting\_reason |
| **planetscale\_pods\_status\_phase**  The pod status phase (Running, Pending, Failed, Succeeded, Unknown). Carries the pod's role, so join it to break container restarts down by role | Gauge | cluster, planetscale\_cell, planetscale\_component, planetscale\_database\_branch\_id, planetscale\_phase, planetscale\_pod, planetscale\_role, planetscale\_router, planetscale\_shard |

## Example Queries

Replace `your-branch-id` with your branch ID.

### Average router query latency

```promql theme={null}
sum by (planetscale_router) (
  rate(planetscale_neki_router_query_duration_seconds_sum{planetscale_database_branch_id="your-branch-id"}[5m])
)
/
sum by (planetscale_router) (
  rate(planetscale_neki_router_query_duration_seconds_count{planetscale_database_branch_id="your-branch-id"}[5m])
)
```

### Sidecar pool utilization

The fraction of the pool that clients currently hold:

```promql theme={null}
planetscale_neki_sidecar_pool_connections_in_use{planetscale_database_branch_id="your-branch-id"}
/
planetscale_neki_sidecar_pool_capacity{planetscale_database_branch_id="your-branch-id"}
```

### Average time a checkout waits for a pooled connection

```promql theme={null}
rate(planetscale_neki_sidecar_pool_wait_seconds_total{planetscale_database_branch_id="your-branch-id"}[5m])
/
rate(planetscale_neki_sidecar_pool_waits_total{planetscale_database_branch_id="your-branch-id"}[5m])
```

## Attributing Container Restarts

`planetscale_pods_container_status_restarts_total` counts every restart and carries no role or reason label. Join the metric that carries the one you want.

To break restarts down by role, join `planetscale_pods_status_phase`:

```promql theme={null}
sum by (planetscale_role) (
  increase(planetscale_pods_container_status_restarts_total[1h])
  * on (planetscale_pod) group_left(planetscale_role)
  group by (planetscale_pod, planetscale_role) (planetscale_pods_status_phase)
)
```

To break restarts down by reason as well, filter against `planetscale_pods_container_last_terminated_reason` one minute at a time and join the role onto the result:

```promql theme={null}
sum by (planetscale_role) (
  sum_over_time((
    increase(planetscale_pods_container_status_restarts_total[1m])
    and on (planetscale_pod, planetscale_container)
    planetscale_pods_container_last_terminated_reason{planetscale_restart_reason="OOMKilled"}
  )[1h:1m])
  * on (planetscale_pod) group_left(planetscale_role)
  group by (planetscale_pod, planetscale_role) (planetscale_pods_status_phase)
)
```

The gauge only reports a container's latest termination reason, so the query attributes each minute's restarts to the reason current in that minute, then sums over the hour. Change `1h` to your own window and leave the `1m` alone. A container that restarts twice inside one minute under two different reasons has both restarts attributed to the later one. Router pods carry no role, so their restarts appear in a series without a role label.

## Tag Glossary

* **cluster**: The PlanetScale cluster identifier
* **planetscale\_cluster**: The name of the Neki cluster that serves the branch
* **planetscale\_database\_branch\_id**: The unique identifier for the database branch
* **planetscale\_pod**: The Kubernetes pod name
* **planetscale\_container**: The container name (for example postgres, walg-daemon, router)
* **planetscale\_component**: The Neki component that owns the pod: nkrouter (router), nktablet (sidecar and Postgres), or nkadmin (admin)
* **planetscale\_router**: The router group name. Present on router pods
* **planetscale\_shard**: The shard identifier. Present on sidecar pods and Postgres metrics
* **planetscale\_role**: The role of the sidecar and its Postgres instance (for example primary or replica). Absent on router pods
* **planetscale\_cell**: The PlanetScale cell identifier
* **planetscale\_phase**: The pod's status phase (Running, Pending, Failed, Succeeded, Unknown), or the phase of a backup restore
* **database**: The database that a router session connected to, or the database that a sidecar pool serves
* **query\_type**: The type of SQL statement, such as SELECT or INSERT
* **error\_class**: The two-character class of the Postgres error code (SQLSTATE) of a failed query
* **le**: The upper bound of a histogram bucket (less than or equal to)
* **planetscale\_connection\_state**: The state of database connections
* **planetscale\_lock\_mode**: The Postgres lock mode
* **planetscale\_database\_name**: The Postgres database name
* **planetscale\_restart\_reason**: Reason for a container's **most recent** termination (OOMKilled, Error, Completed). Carried by **planetscale\_pods\_container\_last\_terminated\_reason**
* **planetscale\_waiting\_reason**: Reason a container is waiting (CrashLoopBackOff, ImagePullBackOff, and others). Carried by **planetscale\_pods\_container\_waiting\_reason**

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
