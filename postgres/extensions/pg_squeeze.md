---
url: https://planetscale.com/docs/postgres/extensions/pg_squeeze
title: "Pg_squeeze"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

## Dashboard Configuration

This extension requires activation via the PlanetScale Dashboard before it can be used. It must be enabled through shared libraries and requires a database restart.

To enable pg\_squeeze:

## Parameters

### squeeze.max\_xlock\_time

- **Type**: Integer (milliseconds)
- **Default**: `0`
- **Minimum**: `0`
- **Description**: The maximum time the processed table may be locked exclusively.

### squeeze.worker\_autostart

- **Type**: String
- **Default**: `postgres`
- **Description**: Space-separated list of databases to start background workers for automatically.

### squeeze.workers\_per\_database

- **Type**: Integer
- **Default**: `1`
- **Minimum**: `1`
- **Description**: Maximum number of worker processes launched per database. Must be less than or equal to cluster-level max\_worker\_processes.

## Usage

After enabling the extension through the dashboard, you can install it in your database:

```sql
CREATE EXTENSION IF NOT EXISTS pg_squeeze;
```

Then you can schedule tables for squeezing:

```sql
-- Schedule a table to be squeezed
SELECT squeeze.squeeze_table('public', 'your_table_name');
```

Only the [default role](../connecting/roles.md#default-role) has the permissions necessary to start a squeeze process.

## External Documentation

For more detailed information about pg\_squeeze usage and configuration, see the [official pg\_squeeze documentation](https://github.com/cybertec-postgresql/pg_squeeze/).
