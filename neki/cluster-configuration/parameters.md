---
url: https://planetscale.com/docs/neki/cluster-configuration/parameters
title: "Parameters"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

Neki exposes separate parameters for the Postgres instances and connection pools in a configuration profile, the branch’s admin service, and each router group. Parameter changes are scoped to the selected profile, admin, or router.

## Parameter scopes

| Scope | What it controls | Where it applies |
| --- | --- | --- |
| **Configuration profile** | Postgres settings such as memory, autovacuum, write-ahead log, and connection limits | Every Postgres instance in every shard assigned to the profile |
| **Sidecars** | The connection pools between routers and Postgres instances | The sidecar beside every Postgres instance in every shard assigned to the configuration profile |
| **Replicator** | Connections used by the distributed replicator | The replicator for every shard assigned to the configuration profile |
| **Admin** | Cluster discovery, health checks, and recovery behavior | The admin service for the selected branch |
| **Router** | Replica-routing lag thresholds | Every router instance in the selected router group |

Different configuration profiles can use different Postgres parameter values. For example, a profile assigned to a memory-intensive shard can use different resource settings from the default profile.

The effective defaults shown in the dashboard can depend on the configuration profile’s cluster size, Postgres version, enabled extensions, and current PlanetScale configuration. Use the dashboard value as the default for the selected profile instead of assuming that one default applies to every profile.

## Configure Postgres parameters

You must be a database administrator to change a configuration profile.

The Postgres tab shows commonly adjusted settings first. Select **Show advanced parameters** to view settings that normally do not need to be changed. Searching includes both common and advanced parameters.

Each field shows its effective default. A configured value that differs from that default is highlighted, along with who changed it and when. Parameters that Postgres cannot apply without restarting are labeled **Requires restart**.

Parameter changes apply to every shard assigned to the configuration profile. Review the assigned shards before changing memory, worker, connection, or WAL settings.

### Defaults and profile changes

PlanetScale chooses resource-dependent defaults for each cluster size. When a profile’s cluster size or Postgres version changes, its default-owned parameters adopt the applicable defaults. Manually configured values are preserved unless the new configuration imposes a different valid range or another parameter constraint.

The API validates individual values and related settings together. For example:

- `huge_pages` and `shared_buffers` must use a supported combination.
- `max_replication_slots` must provide enough capacity for the profile’s replica configuration.
- Postgres restart-sensitive capacity settings cannot be increased and decreased in opposite directions in the same change.

If a change is rejected, the dashboard displays the validation error next to the affected parameter.

## Postgres parameter reference

The following parameters are currently available in the configuration-profile Postgres tab. **Common** parameters appear immediately. **Advanced** parameters appear in the advanced section or in search results. The dashboard remains authoritative for the valid range, allowed values, effective default, and availability for a particular profile.

### Autovacuum

| Parameter | Level | Restart | Description |
| --- | --- | --- | --- |
| `autovacuum` | Advanced | No | Starts the autovacuum subprocess. |
| `autovacuum_analyze_scale_factor` | Common | No | Sets the fraction of table changes added to the analyze threshold. |
| `autovacuum_max_workers` | Advanced | No | Sets the maximum number of autovacuum workers that can run at the same time. |
| `autovacuum_naptime` | Advanced | No | Sets the delay between autovacuum runs. |
| `autovacuum_vacuum_cost_delay` | Advanced | No | Sets the cost delay used by autovacuum. |
| `autovacuum_vacuum_cost_limit` | Advanced | No | Sets the cost limit available to autovacuum before it pauses. |
| `autovacuum_vacuum_insert_scale_factor` | Advanced | No | Sets the fraction of inserted rows added to the vacuum insert threshold. |
| `autovacuum_vacuum_insert_threshold` | Advanced | No | Sets the minimum number of inserted rows before vacuuming. |
| `autovacuum_vacuum_scale_factor` | Common | No | Sets the fraction of updated or deleted rows added to the vacuum threshold. |

### Client connection defaults

| Parameter | Level | Restart | Description |
| --- | --- | --- | --- |
| `lock_timeout` | Advanced | No | Sets how long a statement waits to acquire a lock before it is aborted. |
| `statement_timeout` | Advanced | No | Sets the maximum duration allowed for a statement. Set it to `0` to disable the timeout. |

### Connections and authentication

| Parameter | Level | Restart | Description |
| --- | --- | --- | --- |
| `max_connections` | Common | Yes | Sets the maximum number of concurrent Postgres connections. |

### Lock management

| Parameter | Level | Restart | Description |
| --- | --- | --- | --- |
| `deadlock_timeout` | Advanced | No | Sets how long Postgres waits on a lock before checking for a deadlock. |
| `max_locks_per_transaction` | Advanced | Yes | Sets the average number of object locks allocated for each transaction. |
| `max_pred_locks_per_page` | Advanced | No | Sets how many predicate-locked rows on one page cause Postgres to promote them to a page-level lock. |
| `max_pred_locks_per_relation` | Advanced | No | Sets how many predicate-locked pages and rows in one relation cause Postgres to promote them to a relation-level lock. |
| `max_pred_locks_per_transaction` | Advanced | Yes | Sets the average number of predicate locks allocated for each transaction. |

### Query tuning

| Parameter | Level | Restart | Description |
| --- | --- | --- | --- |
| `default_statistics_target` | Advanced | No | Sets the default statistics target for columns without a column-specific target. |
| `effective_cache_size` | Common | No | Sets the query planner’s assumption about the total size of available data caches. |
| `jit` | Advanced | No | Enables just-in-time compilation. |
| `jit_above_cost` | Advanced | No | Sets the estimated query cost above which just-in-time compilation is considered. |
| `random_page_cost` | Advanced | No | Sets the planner’s estimated cost of fetching a nonsequential disk page. |
| `seq_page_cost` | Advanced | No | Sets the planner’s estimated cost of fetching a sequential disk page. |

### Reporting and logging

| Parameter | Level | Restart | Description |
| --- | --- | --- | --- |
| `log_autovacuum_min_duration` | Advanced | No | Logs autovacuum operations that run for at least the configured duration. |
| `log_connections` | Advanced | No | Selects which stages of connection establishment and setup Postgres logs. |
| `log_line_prefix` | Advanced | No | Sets the information prefixed to each Postgres log line. |
| `log_lock_waits` | Common | No | Logs lock waits that exceed `deadlock_timeout`. |
| `log_min_duration_statement` | Common | No | Logs statements that run for at least the configured duration. |
| `log_statement` | Advanced | No | Selects which types of SQL statements Postgres logs. |
| `log_temp_files` | Advanced | No | Logs temporary files that meet the configured size threshold. |

### Replication

| Parameter | Level | Restart | Description |
| --- | --- | --- | --- |
| `max_logical_replication_workers` | Advanced | Yes | Sets the maximum number of logical replication workers. |
| `max_replication_slots` | Advanced | Yes | Sets the maximum number of replication slots. |
| `max_slot_wal_keep_size` | Advanced | No | Sets the maximum amount of WAL that replication slots can retain. |
| `max_sync_workers_per_subscription` | Advanced | No | Sets the maximum number of table and sequence synchronization workers for each subscription. |
| `max_wal_senders` | Advanced | Yes | Sets the maximum number of concurrent WAL sender processes. |

### Resource usage

| Parameter | Level | Restart | Description |
| --- | --- | --- | --- |
| `bgwriter_delay` | Advanced | No | Sets the delay between background-writer rounds. |
| `bgwriter_lru_maxpages` | Advanced | No | Sets the maximum number of least-recently-used buffers the background writer can flush per round. |
| `effective_io_concurrency` | Advanced | No | Sets the number of concurrent requests the storage subsystem can handle efficiently. |
| `huge_pages` | Advanced | Yes | Controls whether Postgres requests huge pages for its main shared-memory area. |
| `io_workers` | Advanced | No | Sets the number of I/O worker processes when `io_method` is `worker`. |
| `maintenance_io_concurrency` | Advanced | No | Sets the storage concurrency available to maintenance operations. |
| `maintenance_work_mem` | Advanced | No | Sets the maximum memory available to a maintenance operation. |
| `max_parallel_maintenance_workers` | Advanced | No | Sets the maximum number of parallel workers for one maintenance operation. |
| `max_parallel_workers` | Advanced | No | Sets the maximum number of parallel workers active at one time. |
| `max_parallel_workers_per_gather` | Common | No | Sets the maximum number of parallel workers for one executor node. |
| `max_worker_processes` | Advanced | Yes | Sets the maximum number of background processes. |
| `shared_buffers` | Common | Yes | Sets the memory Postgres uses for shared buffers. |
| `track_activity_query_size` | Advanced | Yes | Sets the amount of memory reserved for each query shown in `pg_stat_activity`. |
| `work_mem` | Common | No | Sets the memory available to query operations such as sorting and hashing. |

### Statistics

| Parameter | Level | Restart | Description |
| --- | --- | --- | --- |
| `track_io_timing` | Common | No | Collects timing statistics for database I/O operations. |

### Write-ahead log

| Parameter | Level | Restart | Description |
| --- | --- | --- | --- |
| `archive_timeout` | Advanced | No | Forces a WAL segment switch when the current segment has remained open for the configured duration. |
| `checkpoint_timeout` | Advanced | No | Sets the maximum time between automatic WAL checkpoints. |
| `max_wal_size` | Common | No | Sets the WAL size that triggers a checkpoint. |
| `min_wal_size` | Advanced | No | Sets the minimum size to which WAL can shrink. |
| `wal_buffers` | Advanced | Yes | Sets the number of shared-memory disk-page buffers used for WAL. |
| `wal_compression` | Common | No | Selects the compression method for full-page images in WAL. |
| `wal_level` | Advanced | Yes | Sets how much information Postgres writes to WAL. |

Extension preload settings are managed from the configuration profile’s **Extensions** tab rather than its **Postgres** tab.

## Configure replicator parameters

The configuration profile’s **Replicator** tab exposes connection management for the distributed replicator. Changes apply to every shard assigned to the profile.

| Parameter | Default | Restart | Description |
| --- | --- | --- | --- |
| `max-conns-per-server` | Profile-dependent | Yes | Maximum connections each distributed replicator can hold open to one Postgres server. The valid range is 4 through 16. |

## Configure connection pooling

Every Postgres instance runs one sidecar beside it. A configuration profile’s **Sidecars** tab controls the connection pools those sidecars use between routers and Postgres. Changes apply to every sidecar in every shard assigned to the profile.

| Parameter | Description |
| --- | --- |
| `external-conn-reservation` | Connections reserved for the replicator, backups, and metrics clients instead of application pools. |
| `pool-capacity` | Maximum number of Postgres connections in each database pool. |
| `pool-min-conns` | Minimum number of live connections the pool maintains. Set it to `0` to disable the minimum. |
| `pool-max-lifetime` | Base maximum lifetime of a connection before it is recycled. Actual lifetimes are jittered up to twice this value. Set it to `0` for no maximum. |
| `pool-max-wait-time` | Maximum time a query waits for a pooled connection when the pool is full. |
| `pool-idle-timeout` | Time after which an idle connection above the `pool-min-conns` minimum is closed. Set it to `0` to disable idle reaping. |
| `tx-idle-timeout` | Maximum time a connection can remain idle in an open transaction before the transaction is ended and the connection is recycled. Set it to `0` to disable the timeout. |

All seven settings are applied dynamically and do not restart Postgres. The dashboard shows the effective value and valid range for the selected profile.

`pool-min-conns` cannot exceed `pool-capacity`. The sum of `pool-capacity` and `external-conn-reservation` cannot exceed the profile’s available connection budget after PlanetScale’s internal reservations.

## Configure admin parameters

Admin parameters apply to the admin service for the selected branch.

| Parameter | Default | Description |
| --- | --- | --- |
| `discovery-poll-interval` | `5s` | How often the admin discovers sidecars and checks their health. |
| `recovery-poll-interval` | `10s` | How often the admin analyzes cluster state and starts repairs. |
| `acceptable-replication-lag` | `10s` | The maximum replication lag before the admin considers a replica lagging. |
| `planned-switchover-timeout` | `1m` | The default window for a planned switchover when the request does not specify one. |

The dashboard remains authoritative for each parameter’s current valid range. These admin recovery thresholds are separate from the router parameters that control which replicas can receive read traffic.

## Configure router parameters

Router parameters apply only to the selected router group.

| Parameter | Default | Description |
| --- | --- | --- |
| `insights-raw-queries` | `false` | Sends full query text for slow, large, or failed queries to Insights. Query text may contain sensitive data. |
| `replication-lag-tolerable-min` | `30s` | Replica reads prefer replicas with replication lag at or below this value. |
| `replication-lag-tolerable-max` | `15m` | Replica reads never route to a replica with replication lag above this value. |
| `idle-session-timeout` | `30m` | Disconnects a client that remains idle outside a transaction for longer than this value. Set it to `0` to disable the timeout. |
| `idle-in-transaction-session-timeout` | `5m` | Disconnects a client that remains idle inside a transaction for longer than this value. Set it to `0` to disable the timeout. |
| `stream-pool-size` | `10` | Number of streams kept ready for each sidecar connection. |
| `sidecar-conns` | Derived from router memory | Number of gRPC connections to each sidecar. When unset, Neki derives the value from router memory, up to 16. |

The minimum threshold must be less than or equal to the maximum threshold. The dashboard validates both values together when you apply the change.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
