---
url: https://planetscale.com/docs/postgres/monitoring/pganalyze
title: "Pganalyze"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

## Overview

pganalyze is an advanced monitoring and analytics platform built specifically for PostgreSQL databases, enabling visibility into query performance, system activity, and logs. To use this with PlanetScale, you must run an instance of the [pganalyze collector](https://github.com/pganalyze/collector). This is a monitoring tool designed to collect PostgreSQL logs, query statistics, and system metrics.

Data is sent to the pganalyze platform through this collector for visualization, alerting, and analysis, providing deep insights into your database’s performance.

PlanetScale provides [Query Insights](query-insights.md) and [detailed metrics](metrics.md) as a part of every Postgres database. These combined with our in-app logs view typically provide more than enough detail to monitor cluster health and query performance. We recommend pganalyze for those who have specific additional analytics needs not covered by our built-in tools.

## Prerequisites

Before setting up the pganalyze collector, you’ll need:

- A [pganalyze account](https://pganalyze.com/)
- A PlanetScale Postgres database
- If you want to use log analytics, you’ll also need a [PlanetScale Service Token](../../api/reference/service-tokens.md) with appropriate permissions. This guide assumes you will set up log analytics.

## Getting started

### Step 1: Create a PlanetScale Service Token

First, create a Service Token in your PlanetScale organization that the collector will use to access your database branch information and logs:

### Step 2: Create a pganalyze API key

### Step 3: Get your PlanetScale database connection details

You’ll need the following information from your PlanetScale database: your **organization name** (the organization slug), your **database name** (the name of your Postgres database), and the **branch name** that you want to monitor (for example, `main`).

You will also need to create a role that the pganalyze collector will connect to your database with. Follow our [connections quickstart guide](../connecting/quickstart.md) for instructions on how to create a new role. When doing so, ensure to give it a descriptive name (e.g., `pganalyze-collector`) and enable only the `pg_monitor` role permission.

After creating this role, you must run a few additional SQL commands to ensure it has the necessary schema and access for monitoring. Run the following to create the pganalyze schema:

```sql
CREATE SCHEMA pganalyze;
GRANT USAGE ON SCHEMA pganalyze TO your_username;
GRANT USAGE ON SCHEMA public TO your_username;
```

Note that `your_username` should not include the `.branch_id` at the end of it. So if the username you were given from the PlanetScale UI is `pscale_api_abcd.efgh` you should use `pscale_api_abcd` for these statements.

Finally, add these two functions to this newly-created schema:

```sql
DROP FUNCTION IF EXISTS pganalyze.get_column_stats;
CREATE FUNCTION pganalyze.get_column_stats() RETURNS TABLE(
  schemaname name, tablename name, attname name, inherited bool, null_frac real, avg_width int, n_distinct real, correlation real
) AS $$
  /* pganalyze-collector */
  SELECT schemaname, tablename, attname, inherited, null_frac, avg_width, n_distinct, correlation
  FROM pg_catalog.pg_stats
  WHERE schemaname NOT IN ('pg_catalog', 'information_schema') AND tablename <> 'pg_subscription';
$$ LANGUAGE sql VOLATILE SECURITY DEFINER;

DROP FUNCTION IF EXISTS pganalyze.get_relation_stats_ext;
CREATE FUNCTION pganalyze.get_relation_stats_ext() RETURNS TABLE(
  statistics_schemaname text, statistics_name text,
  inherited boolean, n_distinct pg_ndistinct, dependencies pg_dependencies,
  most_common_val_nulls boolean[], most_common_freqs float8[], most_common_base_freqs float8[]
) AS
$$
  /* pganalyze-collector */ SELECT statistics_schemaname::text, statistics_name::text,
  (row_to_json(se.*)::jsonb ->> 'inherited')::boolean AS inherited, n_distinct, dependencies,
  most_common_val_nulls, most_common_freqs, most_common_base_freqs
  FROM pg_catalog.pg_stats_ext se
  WHERE schemaname NOT IN ('pg_catalog', 'information_schema') AND tablename <> 'pg_subscription';
$$ LANGUAGE sql VOLATILE SECURITY DEFINER;
```

You can also read about this in [the pganalyze docs](https://pganalyze.com/docs/install/self_managed/01_create_monitoring_user).

### Step 4: Enable the pg\_stat\_statements extension

On PlanetScale, you must first enable the `pg_stat_statements` extension in the UI:

1. From your database dashboard, navigate to the **Clusters** page then to the **Extensions** tab.
2. Find `pg_stat_statements` and click the checkbox to enable it, tuning parameters from the defaults if needed.
3. Enqueue and apply the change.

After enabling in the UI, you must also enable the extension in your database by running:

```sql
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;
```

Verify the extension is enabled by running:

```sql
SELECT * FROM pg_extension WHERE extname = 'pg_stat_statements';
```

## Configuration

The pganalyze collector is configured using a configuration file. Create a file named `pganalyze_collector.conf`:

```ini
[pganalyze]
# pganalyze API key
api_key = your_pganalyze_api_key

[planetscale_production]
# PlanetScale Postgres connection (for query stats and monitoring)
db_host = your_db_host
db_port = 5432
db_name = your_database_name
db_username = your_username.your_branch_id
db_password = your_password
db_sslmode = require

# Specify type and scope for pganalyze
api_system_type = planetscale
api_system_scope = your_database_name/your_branch_name

# PlanetScale integration (for log collection)
planetscale_org = your_org_name
planetscale_database = your_database_name
planetscale_branch = your_branch_name
planetscale_token_id = your_service_token_id
planetscale_token_secret = your_service_token_secret
```

The `db_username` should include your branch ID in the format `username.branch_id`. You can find this in the connection string provided by PlanetScale.

## Installation and deployment

The collector can be downloaded using [pre-built packages](https://pganalyze.com/docs/collector/packages) or optionally built from source. The pganalyze website has [instructions for installing it](https://pganalyze.com/docs/install/self_managed/03_install_the_collector) on various operating systems and Docker. Choose whichever is most appropriate for your development environment.

When deploying the collector, use the configuration options covered in the previous section.

## Verifying the setup

After deploying the collector, you should verify that it is working correctly.

If data is not appearing, check the collector logs to see if there are authentication or connectivity issues between pganalyze and PlanetScale.

## Limitations

Current settings / limitations of the pganalyze integration with PlanetScale Postgres:

- **System metrics**: Detailed system metrics (CPU, memory, disk I/O) from the database host are not available through the collector. Use [PlanetScale’s Prometheus metrics](prometheus-postgres.md) for system-level monitoring.
- **Log polling interval**: Logs are collected every 30 seconds, matching the collector’s default polling interval.

## Next steps

- **Explore pganalyze**: Learn about [query performance insights](https://pganalyze.com/docs/query-performance) and [log insights](https://pganalyze.com/log-insights)
- **Monitor additional metrics**: Complement pganalyze monitoring with [Prometheus metrics](prometheus-postgres.md)
- **Leverage PlanetScale Insights and schema recommendations**: Use [PlanetScale Insights](query-insights.md) for actionable query analytics, and explore [schema recommendations](schema-recommendations.md) to improve your database structure alongside pganalyze monitoring.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
