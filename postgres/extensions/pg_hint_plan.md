---
url: https://planetscale.com/docs/postgres/extensions/pg_hint_plan
title: "Pg_hint_plan"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Dashboard Configuration

The pg\_hint\_plan extension requires activation via the PlanetScale Dashboard before it can be used. It must be enabled through shared libraries and requires a database restart.

To enable pg\_hint\_plan:

## Parameters

### pg\_hint\_plan.enable\_hint

- **Type**: Boolean
- **Default**: `true`
- **Description**: Enable/disable hint functionality.

### pg\_hint\_plan.enable\_hint\_table

- **Type**: Boolean
- **Default**: `false`
- **Description**: Enable hint table functionality.

### pg\_hint\_plan.parse\_messages

- **Type**: Select
- **Options**: error, warning, notice, info, log, debug
- **Default**: `info`
- **Description**: Control parsing message output.

### pg\_hint\_plan.debug\_print

- **Type**: Select
- **Options**: off, on, detailed, verbose
- **Default**: `off`
- **Description**: Enable debug output.

### pg\_hint\_plan.message\_level

- **Type**: Select
- **Options**: error, warning, notice, info, log, debug
- **Default**: `info`
- **Description**: Set message verbosity level.

## Usage

Unlike most PostgreSQL extensions, `pg_hint_plan` does not require `CREATE EXTENSION` to function. Once enabled through the dashboard, it’s automatically loaded and available for use. You only need to run `CREATE EXTENSION` if you plan to use the hint table functionality (when `pg_hint_plan.enable_hint_table` is enabled).

After enabling the extension through the dashboard, you can optionally install it in your database for hint table functionality:

```sql
-- Only required if using hint tables
CREATE EXTENSION IF NOT EXISTS pg_hint_plan;
```

Then you can use hints in your queries:

```sql
/*+
    SeqScan(t1)
    IndexScan(t2 t2_pkey)
*/
SELECT * FROM table1 t1 JOIN table2 t2 ON t1.id = t2.id;
```

## External Documentation

For more detailed information about pg\_hint\_plan usage and hint syntax, see the [official pg\_hint\_plan documentation](https://github.com/ossc-db/pg_hint_plan).
