---
url: https://planetscale.com/docs/vitess/cluster-configuration/parameters
title: "Parameters"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

PlanetScale for Vitess lets you adjust a curated set of VTGate, VTTablet, and MySQL parameters directly from the [Clusters page](../cluster-configuration.md) in the dashboard.

The defaults for each parameter depend on the size of your cluster. They are chosen to optimize performance, resource usage, and connection handling for each cluster size, but you can fine-tune them when your workload calls for it.

## Where parameters are configured

| Scope | Component | Where it applies |
| --- | --- | --- |
| Per branch | **VTGate** | Affects all VTGates that route queries for the branch. |
| Per keyspace, per branch | **VTTablet** | Affects every VTTablet (primary and replicas) in the keyspace. |
| Per keyspace, per branch | **MySQL (`mysqld`)** | Applied to the MySQL instances backing the keyspace’s tablets. |

VTGate parameters are set once per branch. VTTablet and MySQL parameters are set per keyspace.

## How to configure parameters

You must be a database or organization administrator to modify these settings.

You can stack changes across VTGate, VTTablets, and MySQL into a single batch before applying.

To revert a parameter to its default, clear the override and apply the change.

## Tracking changes

The **Changes** tab on the Clusters page shows the history of parameter modifications: which parameter was changed, the original and updated values, who made the change, the status, and the start and end time of the rollout.

Resizing a cluster will adjust size-dependent defaults automatically, but only for parameters you have **not** overridden. If you have set a parameter explicitly, that override is preserved across cluster size changes.

## Configurable parameters

PlanetScale for Vitess exposes the following parameters. Defaults vary by cluster size; values shown in the dashboard reflect the current default for your cluster.

### VTGate (per branch)

| Category | Parameter | Description |
| --- | --- | --- |
| **Query execution** | `query-timeout` | Timeout for queries sent to VTGate, in milliseconds. `0` disables the timeout. |
|  | `max_memory_rows` | Maximum number of rows held in memory for intermediate query results. |
|  | `max_payload_size` | Maximum query payload size in bytes. `0` for unlimited. |
|  | `warn_memory_rows` | Warning threshold for in-memory result rows. |
|  | `warn_payload_size` | Warning threshold for query payload size, in bytes. |
| **Failover buffering** | `buffer_size` | Maximum number of buffered requests during a failover. |
|  | `buffer_window` | Duration requests are buffered during a failover. |
|  | `buffer_max_failover_duration` | Maximum total duration to buffer a failover before draining. |
| **Replication** | `discovery_low_replication_lag` | Lag threshold below which replicas are considered fully caught up. |
|  | `discovery_high_replication_lag_minimum_serving` | Minimum number of replicas to keep serving when lag is high. |
|  | `legacy_replication_lag_algorithm` | Use the legacy algorithm for replication lag detection. |
| **Health** | `healthcheck_timeout` | Timeout for VTGate health checks against VTTablets. |
|  | `min_number_serving_vttablets` | Minimum number of VTTablets that must be serving for the tablet type to be considered healthy. |
| **Network** | `grpc_max_message_size` | Maximum gRPC message size, in bytes. |

### VTTablet (per keyspace, per branch)

| Category | Parameter | Description |
| --- | --- | --- |
| **Connection pools** | `queryserver-config-pool-size` | Size of the connection pool for serving queries. |
|  | `queryserver-config-stream-pool-size` | Size of the pool used for streaming queries. |
|  | `queryserver-config-idle-timeout` | How long idle connections in the query pool stay open. |
|  | `queryserver-config-query-pool-timeout` | Maximum time to wait for a query pool connection. |
|  | `queryserver-config-txpool-timeout` | Maximum time to wait for a transaction pool connection. |
| **Transactions** | `queryserver-config-transaction-cap` | Maximum number of concurrent transactions. |
|  | `queryserver-config-transaction-timeout` | Maximum duration of a single transaction. |
| **Query execution** | `queryserver-config-query-timeout` | Maximum query duration before VTTablet kills the query. |
|  | `queryserver-config-max-result-size` | Maximum number of rows returned by a single query. |
|  | `queryserver-config-warn-result-size` | Warning threshold for query result size. |
|  | `queryserver-config-query-cache-memory` | Memory used by the query plan cache. |
| **Protection** | `enable_hot_row_protection` | Detect and serialize transactions contending on the same row. |
|  | `hot_row_protection_concurrent_transactions` | Concurrent transactions allowed per hot row. |
| **VReplication** | `vreplication_copy_phase_max_innodb_history_list_length` | Pause copy if the InnoDB history list grows beyond this size. |
|  | `vreplication_copy_phase_duration` | Maximum duration of a single VReplication copy phase. |
|  | `vreplication_experimental_flags` | Bitmask of experimental VReplication features to enable. |
|  | `vreplication_max_time_to_retry_on_error` | Maximum time to retry a failing VReplication stream before giving up. |
|  | `vreplication-parallel-insert-workers` | Number of parallel insert workers used during VReplication copy. |
| **Schema** | `track_schema_versions` | Enable historical tracking of schema versions. |
|  | `schema-version-max-age-seconds` | Maximum age of tracked schema versions, in seconds. |
| **Replication** | `throttle_tablet_types` | Comma-separated tablet types that participate in throttling decisions. |
| **Network** | `grpc_max_message_size` | Maximum gRPC message size, in bytes. |

### MySQL (per keyspace, per branch)

| Category | Parameter | Description |
| --- | --- | --- |
| **Connections** | `wait_timeout` | Seconds an idle non-interactive connection stays open. |
|  | `connect_timeout` | Seconds the server waits for a connect packet before responding with `Bad handshake`. |
| **Network** | `max_allowed_packet` | Maximum size of one packet or any generated/intermediate string. |
| **Memory** | `join_buffer_size` | Buffer used by joins that do not use indexes. |
|  | `sort_buffer_size` | Buffer used by sessions that perform sorts. |
|  | `tmp_table_size` | Maximum size of internal in-memory temporary tables. |
|  | `max_heap_table_size` | Maximum size of user-created `MEMORY` tables. |
| **InnoDB** | `innodb_lock_wait_timeout` | Seconds an InnoDB transaction waits for a row lock. |
|  | `innodb_buffer_pool_dump_pct` | Percentage of the buffer pool’s most recent pages dumped at shutdown. |
|  | `innodb_io_capacity` | Baseline I/O operations per second available to InnoDB background tasks. |
|  | `innodb_io_capacity_max` | Maximum I/O operations per second available to InnoDB background tasks. |
|  | `innodb_adaptive_hash_index` | Enable the InnoDB adaptive hash index (AHI). Can block queries during deploy request cutovers on tables with many foreign key child tables; see [Adaptive hash index](../foreign-key-constraints.md#adaptive-hash-index). |
|  | `innodb_log_buffer_size` | Size of the buffer that holds redo log data before flushing. |
|  | `innodb_max_purge_lag` | Delay DML when the history list exceeds this length. |
|  | `innodb_max_purge_lag_delay` | Maximum delay, in microseconds, applied when `innodb_max_purge_lag` is exceeded. |
|  | `innodb_purge_batch_size` | Number of undo log pages purged in one batch. |
|  | `innodb_purge_rseg_truncate_frequency` | How often the purge system truncates rollback segments. |
|  | `innodb_purge_threads` | Number of background purge threads. |
|  | `innodb_write_io_threads` | Number of I/O threads used for write operations. |
|  | `innodb_redo_log_capacity` | Total size of the InnoDB redo log. |
| **Replication** | `binlog_row_value_options` | Enables partial JSON logging in row-based binlog events. |
|  | `binlog_row_image` | Controls which columns are included in row-based binlog events (`full`, `minimal`, `noblob`). |
|  | `replica_parallel_workers` | Number of parallel applier threads on replicas. |
| **Transactions** | `transaction_isolation` | Default isolation level for new transactions. |

Some MySQL parameters have version-specific defaults (for example, 8.0 vs. 8.4). The dashboard always reflects the value that applies to your branch’s MySQL version.

## Configuring parameters via the API and CLI

The same parameters are available through the [PlanetScale API](https://planetscale.com/docs/api). The API uses a draft/submit workflow that mirrors the dashboard: list parameters for a branch, queue one or more changes, then submit the batch to apply them. See the API reference for the available operations under **Vitess parameters** and **Config changes**.

The `pscale` CLI does not currently expose commands for tuning these parameters. Use the dashboard or the API.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
