---
url: https://planetscale.com/docs/postgres/extensions/pg_cron
title: "Pg_cron"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Dashboard Configuration

This extension requires activation via the PlanetScale Dashboard before it can be used. It must be enabled through shared libraries and requires a database restart.

To enable pg\_cron:

## Parameters

### cron.database\_name

- **Type**: String
- **Default**: `postgres`
- **Description**: Name of the database where pg\_cron is installed (via CREATE EXTENSION).

### cron.launch\_active\_jobs

- **Type**: Boolean
- **Default**: `true`
- **Description**: Switch to enable/disable running cron jobs - applies to all jobs.

### cron.log\_min\_messages

- **Type**: Select
- **Options**: error, warning, notice, info, log, debug
- **Default**: `warning`
- **Description**: Lowest severity messages to log from the launcher background worker.

### cron.log\_run

- **Type**: Boolean
- **Default**: `true`
- **Description**: Log all cron runs in the cron.job\_run\_details table.

### cron.log\_statement

- **Type**: Boolean
- **Default**: `true`
- **Description**: Log all cron statements before running them.

### cron.max\_running\_jobs

- **Type**: Integer
- **Default**: `1`
- **Minimum**: `1`
- **Description**: Maximum number of jobs that can run at once. Must be less than or equal to cluster-level max\_worker\_processes.

## Usage

After enabling the extension through the dashboard, you can install it in your database:

```sql
CREATE EXTENSION IF NOT EXISTS pg_cron;
```

Then you can schedule jobs using the `cron.schedule` function:

```sql
-- Schedule a job to run every minute
SELECT cron.schedule('job-name', '* * * * *', 'SELECT 1;');
```

## External Documentation

For more detailed information about pg\_cron usage and functionality, see the [official pg\_cron documentation](https://github.com/citusdata/pg_cron).
