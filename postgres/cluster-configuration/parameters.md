---
url: https://planetscale.com/docs/postgres/cluster-configuration/parameters
title: "Parameters"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Cluster configuration parameters

> You can configure your PlanetScale Postgres cluster settings in the "**Parameters**" tab on the Clusters page for your database.

export const PlatformAvailability = ({current, vitess, postgres}) => {
  const docsHref = path => {
    if (!path) return path;
    const normalized = path.startsWith('/') ? path : `/${path}`;
    return normalized;
  };
  const labels = {
    vitess: 'Vitess',
    postgres: 'Postgres'
  };
  if (current === 'both') {
    return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
        <span data-engine="both" data-state="current" aria-current="true" className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
          Vitess and Postgres
        </span>
      </div>;
  }
  const hasVitess = current === 'vitess' || Boolean(vitess);
  const hasPostgres = current === 'postgres' || Boolean(postgres);
  const only = !(hasVitess && hasPostgres);
  const engines = [];
  if (current === 'vitess' || current === 'postgres') engines.push(current);
  if (hasVitess && current !== 'vitess') engines.push('vitess');
  if (hasPostgres && current !== 'postgres') engines.push('postgres');
  return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
      {engines.map(engine => {
    const isCurrent = current === engine;
    const href = docsHref(engine === 'vitess' ? vitess : postgres);
    const label = only ? `${labels[engine]} only` : labels[engine];
    const state = isCurrent || !href ? 'current' : 'link';
    if (isCurrent || !href) {
      return <span key={engine} data-engine={engine} data-state={state} aria-current={isCurrent ? 'true' : undefined} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
              {label}
            </span>;
    }
    return <a key={engine} href={href} data-engine={engine} data-state={state} title={`View ${labels[engine]} documentation`} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
            {label}
            <svg aria-hidden="true" width="12" height="12" viewBox="0 0 12 12" fill="none" className="shrink-0">
              <path d="M2.5 6h7M6.5 3l3 3-3 3" stroke="currentColor" strokeWidth="1.5" strokeLinecap="round" strokeLinejoin="round" />
            </svg>
          </a>;
  })}
    </div>;
};

<PlatformAvailability current="postgres" vitess="/vitess/cluster-configuration/parameters" />

You can configure your PlanetScale Postgres cluster settings in the "**Parameters**" tab on the Clusters page for your database.

The defaults for each parameter depend on the configuration of your cluster. The defaults have been chosen to optimize performance, resource usage, and connection handling for each cluster size. However, you are able to fine-tune each of these settings as needed.

## Configuring parameter values

You must be a database or organization administrator to modify these settings.

1. From the PlanetScale organization dashboard, select the desired database
2. Navigate to the **Clusters** page from the menu on the left
3. Choose the branch whose parameters you'd like to configure in the "**Branch**" dropdown
4. Select the **Parameters** tab
5. Search for a specific parameter or scroll through the page to see all configurable parameters
6. Update the value for the parameter(s) you wish to adjust
7. Click "**Queue parameter changes**"
8. Once you're ready to apply the changes, click "**Apply changes**"

### Configuring parameters with the CLI

You can also inspect and update parameters with the [PlanetScale CLI](../../cli/branch.md):

```bash theme={null}
# List the available parameters, their current values, and allowed ranges
pscale branch parameters list <DATABASE_NAME> <BRANCH_NAME>

# Queue and apply a parameter change (repeat --parameters to set several at once)
pscale branch resize <DATABASE_NAME> <BRANCH_NAME> --parameters pgconf.max_connections=200 --wait

# Check on or cancel a change request
pscale branch resize status <DATABASE_NAME> <BRANCH_NAME>
pscale branch resize cancel <DATABASE_NAME> <BRANCH_NAME>
```

Parameters are addressed as `namespace.name`, for example `pgconf.max_connections` for PostgreSQL
settings or `pgbouncer.default_pool_size` for PgBouncer settings. See the
[`branch` command reference](../../cli/branch.md) for all flags.

## Tracking changes to parameters

You can click on the "Changes" tab on the Clusters page to view a log of any changes made to your parameter settings. The log will include the settings affected, the original and updated values, status, user that made the changes, start time, and end time.

<Note>
  When updating a cluster's size, some [parameters](parameters.md) will automatically
  be adjusted. Each cluster size is associated with default parameter settings, changing the cluster size will also
  update those defaults. The exception to this is if you manually override a default parameter setting. In that case, a
  cluster size adjustment will not automatically change that setting.
</Note>

## Parameter change types

PostgreSQL parameter changes fall into two categories based on how they are applied:

* **Reloadable changes**: The parameter can be updated without restarting PostgreSQL, resulting in zero downtime
* **Restart-required changes**: PostgreSQL requires a cluster restart for the parameter to take effect

Parameters that require a restart are marked with a ✅ in the "Restart Required" column in the [parameter reference table](#default-parameter-values) below.

### Restart behavior for production clusters

When you apply parameters that require a restart, PlanetScale performs a rolling restart process to minimize downtime:

1. Configuration changes are first applied to replica instances and they are restarted
2. Once replicas are ready, a switchover promotes one replica to become the new primary
3. The configuration is applied to the former primary (now a replica) and it is restarted

<img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/cluster-configuration/config-change-restart.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=f3d127b5d60863c67275ba78ddbb75d2" alt="Config change with restart" width="3501" height="1169" data-path="postgres/cluster-configuration/config-change-restart.png" />

This rolling restart process minimizes downtime, but there remains a brief several-second window of unavailability during the primary switchover. All direct database connections will be terminated during this process, so your application should implement connection retry logic. With the exception of the `Number of processes` parameter for PgBouncer, PgBouncer connections persist through all parameter changes and do not require reconnection.

Some parameters are [required by PostgreSQL](https://www.postgresql.org/docs/current/hot-standby.html#HOT-STANDBY-ADMIN) to be applied to the primary before replicas, which may result in a slightly longer unavailability period.

## Default parameter values

The following table shows the default values for parameters that are displayed by default to customers. You can find additional parameters in the search field.

| Parameter                             | Restart Required            | Description                                                                                                                                                |
| ------------------------------------- | --------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **PgBouncer**                         |                             |                                                                                                                                                            |
| Number of processes                   | ✅ (restarts PGBouncer only) | Sets the number of PgBouncer processes that will run on each node in this branch's cluster                                                                 |
| default\_pool\_size                   |                             | Sets the number of server connections to allow per user/database pair                                                                                      |
| max\_client\_conn                     |                             | Sets the maximum number of client connections allowed                                                                                                      |
| max\_db\_client\_connections          |                             | Sets the maximum number of client connections allowed per database (regardless of user). 0 is unlimited                                                    |
| max\_db\_connections                  |                             | Sets the maximum number of server connections allowed per database (regardless of user). 0 is unlimited                                                    |
| max\_prepared\_statements             |                             | Sets the maximum number of client-prepared statements available across server connections                                                                  |
| max\_user\_connections                |                             | Sets the maximum number of server connections allowed per user (regardless of database). 0 is unlimited                                                    |
| server\_lifetime                      |                             | Sets how long an unused server connection stays open                                                                                                       |
| server\_idle\_timeout                 |                             | Sets how long an idle server connection stays open                                                                                                         |
| **Resource usage**                    |                             |                                                                                                                                                            |
| effective\_io\_concurrency            |                             | Sets the number of simultaneous requests that can be handled efficiently by the disk subsystem                                                             |
| effective\_cache\_size                |                             | Sets the planner's assumption about the total size of the data caches                                                                                      |
| huge\_pages                           | ✅                           | Controls whether huge pages are requested for the main shared memory area                                                                                  |
| maintenance\_io\_concurrency          |                             | Sets the number of simultaneous requests that can be handled efficiently by the disk subsystem for maintenance operations                                  |
| maintenance\_work\_mem                |                             | Sets the maximum memory to be used for maintenance operations                                                                                              |
| max\_parallel\_maintenance\_workers   |                             | Sets the maximum number of parallel processes per maintenance operation                                                                                    |
| max\_parallel\_workers                |                             | Sets the maximum number of parallel workers that can be active at one time                                                                                 |
| max\_parallel\_workers\_per\_gather   |                             | Sets the maximum number of parallel processes per executor node                                                                                            |
| max\_worker\_processes                | ✅                           | Sets the maximum number of background processes that the cluster can support                                                                               |
| shared\_buffers                       | ✅                           | Sets the amount of memory the database server uses for shared memory buffers                                                                               |
| work\_mem                             |                             | Sets the amount of memory the database will use for internal operations like sorting and hashing                                                           |
| **Write-ahead log**                   |                             |                                                                                                                                                            |
| max\_slot\_wal\_keep\_size            |                             | Sets the maximum WAL size that can be reserved by replication slots                                                                                        |
| max\_wal\_size                        |                             | Sets the WAL size that triggers a checkpoint                                                                                                               |
| min\_wal\_size                        |                             | Sets the minimum size to shrink the WAL to                                                                                                                 |
| wal\_buffers                          | ✅                           | Sets the number of disk-page buffers in shared memory for WAL                                                                                              |
| wal\_level                            | ✅                           | Sets the level of information written to the WAL                                                                                                           |
| **Query tuning**                      |                             |                                                                                                                                                            |
| deadlock\_timeout                     |                             | Sets the maximum time to wait on a lock before checking for deadlocks                                                                                      |
| default\_statistics\_target           |                             | Sets the default statistics target for table columns without a column-specific target set                                                                  |
| random\_page\_cost                    |                             | Sets the planner's estimate of the cost of a nonsequentially fetched disk page                                                                             |
| seq\_page\_cost                       |                             | Sets the planner's estimate of the cost of a sequentially fetched disk page                                                                                |
| **Connections and authentication**    |                             |                                                                                                                                                            |
| max\_connections                      | ✅                           | Sets the maximum number of concurrent connections                                                                                                          |
| **Replication**                       |                             |                                                                                                                                                            |
| hot\_standby\_feedback                |                             | Sends feedback to the primary about queries being executed on the standby. Required for logical replication failover                                       |
| max\_logical\_replication\_workers    | ✅                           | Sets the maximum number of logical replication workers                                                                                                     |
| max\_replication\_slots               | ✅                           | Sets the maximum number of replication slots that the server can support                                                                                   |
| max\_sync\_workers\_per\_subscription |                             | Sets the maximum number of synchronization workers per subscription                                                                                        |
| max\_wal\_senders                     | ✅                           | Sets the maximum number of WAL senders                                                                                                                     |
| sync\_replication\_slots              |                             | Enables standbys to synchronize logical replication streams from the primary. Required for logical replication failover                                    |
| **Failover**                          |                             |                                                                                                                                                            |
| failover\_delay                       |                             | Sets the time to wait before triggering a failover to drain inflight transactions                                                                          |
| **Statistics**                        |                             |                                                                                                                                                            |
| track\_io\_timing                     |                             | Enables timing of database I/O calls. This may cause significant overhead                                                                                  |
| **Logging**                           |                             |                                                                                                                                                            |
| log\_lock\_waits                      |                             | Logs the duration of lock waits that exceed the deadlock\_timeout                                                                                          |
| log\_min\_duration\_statement         |                             | Sets the minimum execution time above which all statements will be logged                                                                                  |
| log\_line\_prefix                     |                             | Sets the prefix format for each log line. Uses PostgreSQL format sequences such as %m (timestamp), %p (process ID), %d (database name), and %u (user name) |
| **Client connection defaults**        |                             |                                                                                                                                                            |
| shared\_preload\_libraries            | ✅                           | Specifies shared libraries to preload into the server at server start                                                                                      |
| **Autovacuum**                        |                             |                                                                                                                                                            |
| autovacuum\_vacuum\_scale\_factor     |                             | Specifies a fraction of the table size to add to autovacuum\_vacuum\_threshold when deciding whether to trigger a VACUUM                                   |
| autovacuum\_analyze\_scale\_factor    |                             | Specifies a fraction of the table size to add to autovacuum\_analyze\_threshold when deciding whether to trigger an ANALYZE                                |

## Parameters not in the dashboard

The Parameters tab covers the most common cluster-level settings, but PostgreSQL exposes many more configuration parameters than what appears in the dashboard. If a parameter isn't listed in the dashboard, that doesn't mean it can't be changed — it means it's typically managed at a different scope.

You can set many other PostgreSQL runtime parameters directly via SQL at the **session**, **role**, or **database** level using standard PostgreSQL commands:

* **Session** — Applies only to the current connection. Resets when the connection closes.

  ```sql theme={null}
  SET idle_in_transaction_session_timeout = '30s';
  ```

* **Role** — Applies to all future sessions for a specific database role. Useful for enforcing defaults per application user.

  ```sql theme={null}
  ALTER ROLE app_user SET idle_in_transaction_session_timeout = '30s';
  ```

* **Database** — Applies to all future sessions connecting to a specific database.

  ```sql theme={null}
  ALTER DATABASE mydb SET idle_in_transaction_session_timeout = '30s';
  ```

These SQL-level settings take effect immediately for new sessions and do not require a cluster restart.

### Common examples

Some frequently useful parameters that can be set via SQL include:

* **`idle_in_transaction_session_timeout`** — Terminates sessions that sit idle inside an open transaction for too long. This is an important safety net for preventing long-lived transactions from holding locks and blocking writes. See also the PgBouncer [`idle_transaction_timeout`](../connecting/pgbouncer.md#advanced-pgbouncer-settings) setting for a complementary connection-pooler-level control.
* **`statement_timeout`** and **`transaction_timeout`** — Bound how long individual queries and transactions can run. See the [connection resilience guide](../connection-resilience.md#postgres-timeouts) for recommended configuration.
* **`lock_timeout`** — Prevents statements from waiting indefinitely to acquire locks. Particularly useful for DDL operations to avoid blocking other queries.
* **`application_name`** — Identifies your application in monitoring tools and `pg_stat_activity`.

<Note>
  Dashboard parameters are applied at the cluster level and are managed through PlanetScale's rolling restart process. SQL-level settings (`SET`, `ALTER ROLE`, `ALTER DATABASE`) operate at a different scope and do not interact with or override cluster-level parameters set in the dashboard. SQL-level settings take precedence for the sessions they apply to.
</Note>

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
