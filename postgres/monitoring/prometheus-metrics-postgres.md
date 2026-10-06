---
url: https://planetscale.com/docs/postgres/monitoring/prometheus-metrics-postgres
title: "Prometheus Metrics Postgres"
description: ""
access_date: 2026-10-06T20:39:31.314Z
current_date: 2026-10-06T20:39:31.314Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Prometheus metrics for PlanetScale Postgres

> PlanetScale Postgres exposes a Prometheus-compatible endpoint per-branch that allows you to scrape metrics for your database.

**Platform availability:** [Vitess](../../vitess/integrations/prometheus-metrics.md) and Postgres

## Overview

See our [Prometheus integration](../../vitess/integrations/prometheus.md) documentation for how to set Prometheus up to automatically discover and scrape metrics for your database branches.

If you're using Datadog, see our [Datadog tutorial](prometheus-metrics-datadog-postgres.md) for how to setup your Datadog agent to scrape metrics for your branch.

## Metrics

PlanetScale Postgres emits the following metrics to be scraped.

For metrics emitted by a dedicated read replica, `planetscale_database_branch_id` identifies the replica and `planetscale_upstream_database_branch_id` identifies its parent database branch.

## Database Metrics

| **Name & Description** | **Type** | **Tags** |
| :- | :- | :- |
| **planetscale\_postgres\_connection\_state**  The count and state of Postgres connections | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_connection\_state, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_postgres\_wal\_archiver\_succeeded\_count**  The count of successfully archived WALs | Counter | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_postgres\_wal\_archiver\_failed\_count**  The count of unsuccessfully archived WALs | Counter | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_postgres\_wal\_archiver\_last\_age\_succeeded**  The age of the last successfully archived WAL | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_postgres\_wal\_archiver\_lag\_bytes**  WAL bytes written but not yet archived (current LSN minus last archived LSN) | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_postgres\_wal\_size\_bytes**  The cumulative disk size of WALs waiting to be archived | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_postgres\_settings\_max\_connections**  The current value of the max\_connections setting | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_postgres\_settings\_max\_wal\_size\_bytes**  The current value of the max\_wal\_size\_bytes setting | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_postgres\_settings\_max\_slot\_wal\_keep\_size\_bytes**  The current value of the max\_slot\_wal\_keep\_size setting (-1 means unlimited) | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_postgres\_replication\_slot\_max\_wal\_retained\_bytes**  The largest amount of WAL retained behind any logical replication slot on the primary; grows toward max\_slot\_wal\_keep\_size as a slot's consumer falls behind | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_postgres\_replication\_slot\_min\_safe\_wal\_size\_bytes**  The smallest remaining headroom before a logical replication slot risks invalidation; absent when max\_slot\_wal\_keep\_size is unlimited | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_postgres\_replication\_slots\_lost**  The number of logical replication slots on the primary that have been invalidated (lost) and must be resynced | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_postgres\_replica\_lag\_seconds**  Replica lag in fine-grained seconds from Postgres | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_postgres\_locks**  Count of current lock modes | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_lock\_mode, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_postgres\_wait\_events**  Count of active Postgres backends on the primary by wait event. An active backend with no wait event is running on CPU and is reported with wait event and wait event type **CPU**. Primary only; replicas emit no series | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_wait\_event\_type, planetscale\_wait\_event, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_postgres\_database\_xact\_commit\_total**  Total committed transactions on Postgres databases | Counter | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_database\_name, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_backup\_restore\_active** Determines if a backup restore is active, tagged with the current phase (1 = active) | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_phase, planetscale\_component |
| **planetscale\_backup\_fetch\_percent** Percentage progress of a backup fetch operation (0-100) | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_component |

## Edge Metrics

| **Name & Description** | **Type** | **Tags** |
| :- | :- | :- |
| **planetscale\_edge\_postgres\_active\_connections**  The number of active Postgres and PgBouncer connections to the branch | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_port, planetscale\_region |
| **planetscale\_edge\_postgres\_connection\_drops\_total**  The total number of Postgres and PgBouncer connections that have been dropped | Counter | cluster, planetscale\_database\_branch\_id, planetscale\_port, planetscale\_region |
| **planetscale\_edge\_postgres\_connection\_errors\_total**  The total number of Postgres and PgBouncer connections that have resulted in an error | Counter | cluster, planetscale\_database\_branch\_id, planetscale\_port, planetscale\_region |
| **planetscale\_edge\_postgres\_bytes\_sent\_total**  The total number of Postgres and PgBouncer bytes sent | Counter | cluster, planetscale\_database\_branch\_id, planetscale\_port, planetscale\_connector\_id, planetscale\_region |
| **planetscale\_edge\_postgres\_bytes\_received\_total**  The total number of Postgres and PgBouncer bytes received | Counter | cluster, planetscale\_database\_branch\_id, planetscale\_port, planetscale\_connector\_id, planetscale\_region |

## PgBouncer Metrics

| **Name & Description** | **Type** | **Tags** |
| :- | :- | :- |
| **planetscale\_pgbouncer\_total\_peers**  The total count of peered processes PgBouncer is running | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_pgbouncer\_cpu\_util\_per\_peer\_percentages**  CPU utilization percentage of PgBouncer peered processes | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_pgbouncer\_current\_connections**  The current count PgBouncer connections to Postgres | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_pgbouncer\_current\_client\_connections**  The current count client connections to PgBouncer | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_pgbouncer\_pools\_client**  The count and state of PgBouncer client connections | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_pgbouncer\_pool, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_pgbouncer\_pools\_client\_maxwait\_seconds**  How long the first client connection has waited to connect to PgBouncer | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_pgbouncer\_pools\_server**  The count and state of PgBouncer server connections | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_pgbouncer\_pool, planetscale\_role, planetscale\_cell, planetscale\_component |

## Infrastructure Metrics

| **Name & Description** | **Type** | **Tags** |
| :- | :- | :- |
| **planetscale\_pods\_cpu\_util\_percentages**  CPU utilization percentage of database pods | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_pods\_mem\_util\_percentages**  Memory utilization percentage of database pods | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_pods\_iops\_total**  Total IOPS (Input/Output Operations Per Second) of database pods | Counter | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_volume\_available\_bytes**  Available storage space in bytes on Postgres volumes | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_volume\_capacity\_bytes**  Total storage capacity in bytes on Postgres volumes | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_pods\_mem\_rss\_bytes**  RSS memory usage in bytes of database pods | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_pods\_mem\_mmap\_bytes**  Memory-mapped file usage in bytes of database pods | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_pods\_mem\_active\_cache\_bytes**  Active cache memory in bytes of database pods | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_pods\_mem\_inactive\_cache\_bytes**  Inactive cache memory in bytes of database pods | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component |
| **planetscale\_pods\_container\_status\_restarts\_total**  Total container restarts, across all restart reasons. Join **planetscale\_pods\_container\_last\_terminated\_reason** to attribute them | Counter | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_cell, planetscale\_component |
| **planetscale\_pods\_container\_last\_terminated\_reason**  Reason a container was last terminated (1 = the current reason). Join against **planetscale\_pods\_container\_status\_restarts\_total** to attribute restarts to a reason | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_cell, planetscale\_component, planetscale\_restart\_reason |
| **planetscale\_pods\_status\_phase**  Pod status phase (Running, Pending, Failed, Succeeded, Unknown). Carries the pod's role, so join it to break container restarts down by role | Gauge | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_phase, planetscale\_role, planetscale\_cell, planetscale\_component |

## Deprecated Metrics

These metrics are deprecated and will be removed eventually.

| **Name & Description** | **Type** | **Tags** |
| :- | :- | :- |
| **planetscale\_pods\_container\_restarts\_total**  Total container restart events detected. The reason label is attached to an all-reasons total and the counter cannot be observed at zero, so rates and increases over it are wrong in both directions. Replaced by planetscale\_pods\_container\_status\_restarts\_total. | Counter | cluster, planetscale\_database\_branch\_id, planetscale\_pod, planetscale\_container, planetscale\_role, planetscale\_cell, planetscale\_component, planetscale\_restart\_reason |

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

The gauge only reports a container's latest termination reason, so the query attributes each minute's restarts to the reason current in that minute, then sums over the hour. Change `1h` to your own window and leave the `1m` alone. A container that restarts twice inside one minute under two different reasons has both restarts attributed to the later one.

## Tag Glossary

* **cluster**: The PlanetScale cluster identifier
* **planetscale\_access**: The access path on bytes sent/received metrics (**public** for traffic over the public internet, or **private** for traffic over AWS PrivateLink or GCP Private Service Connect)
* **planetscale\_database\_branch\_id**: The unique identifier for the database branch
* **planetscale\_upstream\_database\_branch\_id**: The unique identifier for the parent database branch of a dedicated read replica. Present only on dedicated read replica metrics
* **planetscale\_pod**: The Kubernetes pod name
* **planetscale\_container**: The container name (postgres, pgbouncer, walg-daemon)
* **planetscale\_role**: The database role (primary, replica). Absent on pods that have no role, such as pgbouncer
* **planetscale\_cell**: The PlanetScale cell identifier
* **planetscale\_phase**: The pod's status phase (Running, Pending, Failed, Succeeded, Unknown)
* **planetscale\_component**: The PlanetScale component identifier
* **planetscale\_connection\_state**: The state of database connections
* **planetscale\_lock\_mode**: The PostgreSQL lock mode
* **planetscale\_wait\_event\_type**: The PostgreSQL wait event type of an active backend (for example Lock, LWLock, IO, Client), or **CPU** when the backend is not waiting
* **planetscale\_wait\_event**: The PostgreSQL wait event of an active backend (for example transactionid, DataFileRead, ClientRead), or **CPU** when the backend is not waiting
* **planetscale\_database\_name**: The PostgreSQL database name
* **planetscale\_port**: The connection port number
* **planetscale\_region**: The geographic region
* **planetscale\_pgbouncer\_pool**: The PgBouncer connection pool identifier
* **planetscale\_connector\_id**: The edge connector identifier
* **planetscale\_restart\_reason**: Reason for a container's **most recent** termination (OOMKilled, Error, Completed). Carried by **planetscale\_pods\_container\_last\_terminated\_reason**

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
