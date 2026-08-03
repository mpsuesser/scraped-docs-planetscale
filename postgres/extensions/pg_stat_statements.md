---
url: https://planetscale.com/docs/postgres/extensions/pg_stat_statements
title: "Pg_stat_statements"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

## Dashboard Configuration

This extension requires activation via the PlanetScale Dashboard before it can be used. It must be enabled through shared libraries and requires a database restart.

To enable pg\_stat\_statements:

## Parameters

### pg\_stat\_statements.max

- **Type**: Integer
- **Default**: `5000`
- **Description**: Maximum number of statements tracked by the extension.

### pg\_stat\_statements.track

- **Type**: Select
- **Options**: top, all
- **Default**: `top`
- **Description**: Which statements to track. ‘top’ tracks top-level statements only, ‘all’ tracks all statements including nested ones.

### pg\_stat\_statements.track\_utility

- **Type**: Boolean
- **Default**: `true`
- **Description**: Whether to track utility statements (such as PREPARE, EXPLAIN, etc.).

### pg\_stat\_statements.track\_planning

- **Type**: Boolean
- **Default**: `false`
- **Description**: Whether to track planning statistics for statements.

### pg\_stat\_statements.save

- **Type**: Boolean
- **Default**: `true`
- **Description**: Whether to save statement statistics across server restarts.

## Usage

After enabling the extension through the dashboard, you can install it in your database:

```sql
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;
```

Then you can query the statistics view:

```sql
-- View the most time-consuming queries
SELECT
    query,
    calls,
    total_exec_time,
    mean_exec_time,
    rows
FROM pg_stat_statements
ORDER BY total_exec_time DESC
LIMIT 10;
```

## External Documentation

For more detailed information about pg\_stat\_statements usage and the available statistics, see the [official PostgreSQL documentation](https://www.postgresql.org/docs/current/pgstatstatements.html).
