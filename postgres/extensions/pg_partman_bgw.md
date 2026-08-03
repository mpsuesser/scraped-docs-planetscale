---
url: https://planetscale.com/docs/postgres/extensions/pg_partman_bgw
title: "Pg_partman_bgw"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

## Dashboard Configuration

This extension requires activation via the PlanetScale Dashboard before it can be used. It must be enabled through shared libraries and requires a database restart.

To enable pg\_partman\_bgw:

## Parameters

### pg\_partman\_bgw.interval

- **Type**: Integer
- **Default**: `3600`
- **Description**: Interval (in seconds) between partition maintenance runs.

### pg\_partman\_bgw.dbname

- **Type**: String
- **Default**: `postgres`
- **Description**: Database name where the background worker should connect.

### pg\_partman\_bgw.role

- **Type**: String
- **Default**: `postgres`
- **Description**: Role that the background worker should use when connecting.

### pg\_partman\_bgw.analyze

- **Type**: Boolean
- **Default**: `false`
- **Description**: Whether to run ANALYZE on newly created partitions.

### pg\_partman\_bgw.jobmon

- **Type**: Boolean
- **Default**: `true`
- **Description**: Enable job monitoring for partition maintenance activities.

## Usage

After enabling the extension through the dashboard, the background worker will automatically run partition maintenance based on your pg\_partman configuration. You’ll also need to install the pg\_partman extension:

```sql
CREATE EXTENSION IF NOT EXISTS pg_partman;
```

The background worker works in conjunction with pg\_partman to automatically maintain your partitioned tables.

## External Documentation

For more detailed information about pg\_partman and pg\_partman\_bgw usage, see the [official pg\_partman documentation](https://github.com/pgpartman/pg_partman).
