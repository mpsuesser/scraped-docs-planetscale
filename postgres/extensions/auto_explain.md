---
url: https://planetscale.com/docs/postgres/extensions/auto_explain
title: "Auto_explain"
description: ""
access_date: 2026-09-04T17:35:45.309Z
current_date: 2026-09-04T17:35:45.309Z
---

## Dashboard Configuration

This extension requires activation via the PlanetScale dashboard before it can be used. It is loaded through `session_preload_libraries` and does not require a database restart.

To enable auto\_explain:

## Parameters

### auto\_explain.log\_min\_duration

- **Type**: Integer (milliseconds)
- **Default**: `-1`
- **Description**: Minimum statement duration to log a plan for. Set to `-1` to disable logging by duration.

### auto\_explain.log\_analyze

- **Type**: Boolean
- **Default**: `false`
- **Description**: Uses `EXPLAIN ANALYZE` for logged plans to include actual execution statistics.

### auto\_explain.log\_buffers

- **Type**: Boolean
- **Default**: `false`
- **Description**: Includes buffer usage statistics in logged plans.

### auto\_explain.log\_wal

- **Type**: Boolean
- **Default**: `false`
- **Description**: Includes WAL usage statistics in logged plans.

### auto\_explain.log\_timing

- **Type**: Boolean
- **Default**: `true`
- **Description**: Includes per-node timing information in logged plans.

### auto\_explain.log\_triggers

- **Type**: Boolean
- **Default**: `false`
- **Description**: Includes trigger execution statistics in logged plans.

### auto\_explain.log\_verbose

- **Type**: Boolean
- **Default**: `false`
- **Description**: Uses `EXPLAIN VERBOSE` output for logged plans.

### auto\_explain.log\_format

- **Type**: Select
- **Options**: text, xml, json, yaml
- **Default**: `text`
- **Description**: Output format used when logging plans.

### auto\_explain.log\_level

- **Type**: Select
- **Default**: `log`
- **Description**: Log severity used when writing query plans.

### auto\_explain.log\_nested\_statements

- **Type**: Boolean
- **Default**: `false`
- **Description**: Logs plans for nested statements that run inside functions.

### auto\_explain.sample\_rate

- **Type**: Decimal
- **Default**: `1`
- **Minimum**: `0`
- **Maximum**: `1`
- **Description**: Fraction of statements that are sampled for plan logging.

## Usage

`auto_explain` is not installed with `CREATE EXTENSION`. After enabling it in the dashboard, sessions load it through `session_preload_libraries`.

You can verify and tune settings in SQL:

```sql
SHOW session_preload_libraries;

SET auto_explain.log_min_duration = '250ms';
SET auto_explain.sample_rate = 0.25;
```

Logged plans appear in your cluster logs when statements match the configured thresholds.

## External Documentation

For more detail about `auto_explain` behavior and parameter semantics, see the [official PostgreSQL documentation](https://www.postgresql.org/docs/current/auto-explain.html).
