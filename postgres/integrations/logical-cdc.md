---
url: https://planetscale.com/docs/postgres/integrations/logical-cdc
title: "Logical Cdc"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Logical replication and Change Data Capture (CDC)

> Change Data Capture (CDC) allows you to track and stream data changes from your PostgreSQL database to external systems in real-time. PlanetScale Postgres supports logical replication, enabling CDC through various tools and integrations.

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

<PlatformAvailability current="postgres" vitess="/vitess/etl" />

## What is logical replication?

Logical replication is a PostgreSQL feature that replicates data objects and their changes at the logical level, rather than the physical level. It works by:

1. **WAL Level**: The database must be configured with `wal_level = logical` to capture logical changes
2. **Publication**: Creates a logical replication stream on the source database
3. **Replication Slot**: Maintains position in the WAL stream and ensures data consistency
4. **Subscription/Consumer**: External tools consume the logical replication stream

This setup enables:

* Replication of individual transactions and row changes
* Selective replication of specific tables or databases
* Cross-version replication between different PostgreSQL versions
* CDC integration with external tools and data pipelines

## Configuration requirements

### PlanetScale cluster parameters

To enable logical replication on your PlanetScale Postgres cluster, configure these parameters in the **Clusters > Parameters** tab:

| Parameter                | Required Value | Description                                                                                                                                                                                                                                                                                                                                                      |
| ------------------------ | -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `wal_level`              | `logical`      | Setting the `wal_level` to `logical` enables logical replication, which captures row-level changes in a format that can be flexibly replayed on target systems.                                                                                                                                                                                                  |
| `max_replication_slots`  | 2 x replicas   | Set `max_replication_slots` to twice the number of replicas or subscribers. Each replica uses one slot, with the extra slots reserved for operations like failover.                                                                                                                                                                                              |
| `max_wal_senders`        | 2 x replicas   | Likewise, set `max_wal_senders` to twice the number of replicas or targets, and not less than `max_replication_slots`.                                                                                                                                                                                                                                           |
| `max_slot_wal_keep_size` | > 4GB          | The value of `max_slot_wal_keep_size` should be tuned to ensure you are keeping WAL files long enough for subscribers to consume them, while being sure that the source disk is not overrun by files. A reasonable starting point is > 4GB, and monitor your replication lag, your database's change rate (inserts, updates, deletes), and available disk space. |

In addition, for production environments, configure the following to ensure that your CDC stream is maintained during any switchover or failover. Without these settings, manual intervention will be required to restore data pipelines after these events.

| Parameter                | Required Value | Description                                                                   |
| ------------------------ | -------------- | ----------------------------------------------------------------------------- |
| `sync_replication_slots` | `on`           | Set to `on` to enable synchronization of replication slots to the subcribers. |
| `hot_standby_feedback`   | `on`           | Set to `on` to prevent query conflicts during replication.                    |

If your CDC client sleeps for long periods, a failover can proceed before slots are synced, which risks losing the logical slot. Use `logical_slot_sync_timeout` to bound how long failover waits for slot sync in that scenario. For more details, see [Postgres High Availability with CDC](https://planetscale.com/blog/postgres-ha-with-cdc).

| Parameter                   | Default | Description                                                              |
| --------------------------- | ------- | ------------------------------------------------------------------------ |
| `logical_slot_sync_timeout` | `300s`  | Time to wait for logical slot sync on standbys before failover proceeds. |

Also, in the **Cluster configuration > Parameters** tab of the dashboard UI, under the Failover section, add a comma-delimited list of the replication slot(s) you will create to preserve during any switchover or failover events.

Be sure to apply the queue of configuration changes before proceeding.

### Verify configuration

After setting these configuration parameters in the dashboard, you can verify them in the CLI. For example, to verify the WAL level:

```sql theme={null}
SHOW wal_level;
```

The result should show `logical`:

```sql theme={null}
 wal_level
-----------
 logical
```

### CDC tool configuration

Ensure your CDC tool is configured properly:

* **Airbyte**: Ensure replication slots are created with failover support ([Setup Guide](https://docs.airbyte.com/integrations/sources/postgres))
* **AWS DMS**: Manually create failover-enabled replication slots before configuring DMS ([Setup Guide](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.PostgreSQL.html))
* **ClickHouse**: See ClickPipes documentation for PlanetScale configuration ([Setup Guide](https://clickhouse.com/docs/en/integrations/clickpipes/postgres/source/planetscale))
* **Debezium**: Configure connector to use failover-enabled replication slots ([Setup Guide](https://debezium.io/documentation/reference/stable/connectors/postgresql.html))
* **Fivetran**: Create your own replication slot with `failover = true` ([Setup Guide](https://fivetran.com/docs/connectors/databases/postgresql))

<Warning>
  Some CDC tools create replication slots automatically. **You must verify** that any auto-created slots have `failover = true` enabled, or manually create the slots yourself with the proper configuration.
</Warning>

## Create and manage users

For production CDC deployments, create a dedicated replication role. You can do this through PlanetScale (API, dashboard, CLI, or Terraform) or with SQL as the default user.

### Create a role with REPLICATION via PlanetScale

You can create a PlanetScale [user-defined role](../connecting/roles.md) with the `REPLICATION` attribute enabled. When creating the role:

* Include the `postgres` inherited role.
* Enable the `REPLICATION` attribute on the role.

In the dashboard, select **WITH REPLICATION** when creating a role (Settings > Roles, or the Connect dialog). Through the API, pass `with_replication: true` when creating a role with `inherited_roles` that includes `postgres`.

Roles created this way appear in your database settings and can be managed through the dashboard, API, and CLI. Use this role's credentials to connect from your subscriber/consumer side.

### Create a user with SQL

Alternatively, log in as the default user and create a dedicated replication user with SQL:

```sql theme={null}
-- Create dedicated CDC user
CREATE USER cdc_user WITH REPLICATION PASSWORD 'strong_password';
```

The `WITH REPLICATION` clause allows the user to connect to the server using the replication protocol, to create and use replication slots, to stream WAL files, and to perform the logical decoding operations. You will configure this user to connect from your subscriber/consumer side.

Because of the edge connection settings, to log in as this user, add the branch ID after the username, like this:

```sql theme={null}
cdc_user.12345678
```

Some target systems, like Fivetran, will need to match on the exact login username for some operations after it has logged in. As a workaround for those cases, also create a user with the name + branch ID, like this:

```sql theme={null}
-- Create dedicated CDC user
CREATE USER "cdc_user.12345678" WITH REPLICATION PASSWORD 'strong_password';
```

To find the branch ID, look at the Settings > Roles page and observe the branch ID in roles that were created in the UI.

Roles created via SQL do not appear on the Settings > Roles page and must be managed directly in Postgres.

## Create and manage replication streams

### Create a replication slot

Using the dedicated replication role, create logical replication slots with the `failover` option enabled to preserve the slots during any switchover or failover events:

```sql theme={null}
SELECT pg_create_logical_replication_slot(
  'my_cdc_slot',            -- slot_name
  'pgoutput',               -- plugin
  false,                    -- temporary
  false,                    -- two_phase
  true                      -- failover = true (REQUIRED)
);
```

### Create initial publication

Some CDC tools require you to create publications to specify which tables to replicate. You will need to do this as the owner of the tables or the default role. This example uses the [default role](../connecting/roles.md#default-role).

```sql theme={null}
CREATE PUBLICATION my_cdc_publication FOR TABLE table1, table2;
```

### Add tables to a publication

Currently, tables must be added to the publication individually or as a comma-delimited list. Remember to update your publication when adding new tables that should be replicated.

```sql theme={null}
ALTER PUBLICATION my_cdc_publication ADD TABLE table3;
```

### Replica identity configuration

For complete change tracking of both row values before and after changes (as well as to support any tables without a primary key), set the replica identity to FULL:

```sql theme={null}
-- Enable full replica identity for complete change tracking
ALTER TABLE table1 REPLICA IDENTITY FULL;
ALTER TABLE table2 REPLICA IDENTITY FULL;
ALTER TABLE table3 REPLICA IDENTITY FULL;
```

### Verify publications

Issue the following to see active publications with tables. Do this as the default user.

```sql theme={null}
SELECT p.pubname,
       c.relname AS tablename
FROM pg_publication p
JOIN pg_publication_rel pr ON p.oid = pr.prpubid
JOIN pg_class c ON pr.prrelid = c.oid;
```

## Monitoring and troubleshooting

### PlanetScale metrics for CDC monitoring

PlanetScale provides built-in metrics that are essential for monitoring your CDC setup. Access these through your **Metrics dashboard** to track replication health and performance:

| Metric Category           | Key Indicators for CDC  | What to Monitor                                                                                   |
| ------------------------- | ----------------------- | ------------------------------------------------------------------------------------------------- |
| **WAL archival rate**     | Success/Failed counts   | Monitor failed WAL archival attempts that could impact CDC streams                                |
| **WAL archive age**       | Seconds behind          | Age of oldest unarchived WAL - should be under 60 seconds for healthy CDC                         |
| **WAL storage**           | Storage usage in MB     | Track WAL disk usage; high usage may indicate CDC consumers falling behind                        |
| **Replication lag**       | Lag in seconds          | Monitor delay between primary and replicas; high lag may indicate CDC consumer performance issues |
| **Transaction rate**      | Transactions per second | Track database workload intensity affecting CDC processing                                        |
| **Memory**                | RSS and Memory mapped   | Monitor memory pressure that could impact logical decoding performance                            |
| **Primary Storage Usage** | MB disk utilization     | Monitor disk utilization to be sure WAL files are being consumed quickly enough                   |

<Note>
  For detailed information about interpreting these metrics, see the [Cluster
  Metrics](../monitoring/metrics.md) documentation.
</Note>

### Monitoring replication lag

Check replication slot lag. The replication\_lag column shows how much WAL data the publisher is keeping because the subscriber has not confirmed or processed it yet. This value should be kept well below `max_wal_size`.

```sql theme={null}
SELECT
    slot_name,
    database,
    active,
    restart_lsn,
    confirmed_flush_lsn,
    pg_size_pretty(pg_wal_lsn_diff(pg_current_wal_lsn(), restart_lsn)) AS replication_lag
FROM pg_replication_slots
WHERE slot_type = 'logical';
```

### WAL retention and disk usage

Monitor WAL retention to prevent disk space issues. This is another way to see similar information, and will include any PlanetScale HA replicas.

```sql theme={null}
SELECT
    slot_name,
    pg_size_pretty(pg_wal_lsn_diff(pg_current_wal_lsn(), restart_lsn)) AS retained_wal
FROM pg_replication_slots;
```

### Common issues

**Issue**: WAL disk space growing rapidly\
**Cause**: Inactive or slow CDC consumers\
**Solution**: Remove unused slots or troubleshoot slow consumers

**Issue**: Failover breaks CDC stream\
**Cause**: Replication slot not properly synchronized\
**Solution**: Verify failover configuration and slot synchronization status

## Best practices

1. **Always enable failover**: **Never** deploy CDC to production without `failover = true` on replication slots and proper PlanetScale cluster configuration
2. **Verify configuration**: Double-check that both your CDC tool and PlanetScale settings are properly configured before going live
3. **Test failover scenarios**: Test actual failover events in staging environments to ensure your configuration works
4. **Regular monitoring**: Monitor replication lag, WAL retention, and slot synchronization status
5. **Slot cleanup**: Remove unused logical replication slots to prevent WAL accumulation
6. **CDC client resilience**: Ensure CDC clients can handle connection interruptions gracefully

## Security considerations

* Logical replication exposes table data - ensure proper access controls
* Use dedicated database users with minimal required privileges for CDC
* Consider network security when streaming to external systems
* Monitor for unauthorized replication slots

For more information about cluster configuration parameters, see the [Cluster configuration parameters](../cluster-configuration/parameters.md) documentation.
