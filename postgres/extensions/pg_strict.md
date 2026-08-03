---
url: https://planetscale.com/docs/postgres/extensions/pg_strict
title: "Pg_strict"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

## Overview

pg\_strict intercepts queries at parse time and blocks operations that are likely to be mistakes. When enabled, it prevents accidental mass updates or deletes that could affect your entire table.

pg\_strict configuration only applies to connections established after the change. It does not impact existing connections, even if they would violate the newly-configured rules.

## Enabling pg\_strict

pg\_strict is configured per-role when creating new roles in the dashboard. To add pg\_strict to existing roles, see [per-role configuration](#per-role-configuration).

## Modes

Each pg\_strict setting supports three modes:

| Mode | Behavior |
| --- | --- |
| `off` | Disabled, standard PostgreSQL behavior |
| `warn` | Log a warning but allow the query to run |
| `on` | Block the query with an error |

## What it blocks

We’re actively expanding pg\_strict with additional safety checks. More configurations will be added soon to help prevent other common mistakes.

### UPDATE without WHERE

When `pg_strict.require_where_on_update` is enabled, UPDATE statements must include a WHERE clause:

```sql
-- Blocked (affects all rows)
UPDATE users SET status = 'inactive';

-- Allowed (targets specific rows)
UPDATE users SET status = 'inactive' WHERE last_login < '2024-01-01';
```

### DELETE without WHERE

When `pg_strict.require_where_on_delete` is enabled, DELETE statements must include a WHERE clause:

```sql
-- Blocked (deletes all rows)
DELETE FROM sessions;

-- Allowed (targets specific rows)
DELETE FROM sessions WHERE expired_at < NOW();
```

## Overriding for one-off operations

For intentional bulk operations, use `SET LOCAL` within a transaction to temporarily disable a specific check:

```sql
BEGIN;
SET LOCAL pg_strict.require_where_on_delete = off;
DELETE FROM temp_import_data;  -- Allowed within this transaction
COMMIT;
-- Setting restored after commit
```

## Database and role configuration

Settings can be applied at the database level (affects all roles) or per-role.

### Database-wide default

Enable pg\_strict for all connections to a database:

```sql
ALTER DATABASE postgres SET pg_strict.require_where_on_update = 'on';
ALTER DATABASE postgres SET pg_strict.require_where_on_delete = 'on';
```

### Per-role configuration

Configure pg\_strict for existing or new roles:

```sql
-- App role: block dangerous queries
ALTER ROLE app_service SET pg_strict.require_where_on_update = 'on';
ALTER ROLE app_service SET pg_strict.require_where_on_delete = 'on';

-- Migration role: warn only
ALTER ROLE migration_user SET pg_strict.require_where_on_update = 'warn';
ALTER ROLE migration_user SET pg_strict.require_where_on_delete = 'warn';

-- Admin role: full access
ALTER ROLE dba_admin SET pg_strict.require_where_on_update = 'off';
ALTER ROLE dba_admin SET pg_strict.require_where_on_delete = 'off';
```

This allows you to set a strict default at the database level while relaxing restrictions for specific roles that need to perform bulk operations.
