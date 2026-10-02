---
url: https://planetscale.com/docs/vitess/imports/import-troubleshooting
title: "Import Troubleshooting"
description: ""
access_date: 2026-10-02T19:13:10.531Z
current_date: 2026-10-02T19:13:10.531Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Import troubleshooting

**Platform availability:** Vitess only

## Overview

This guide covers common issues you might run into when importing a database to PlanetScale and how to fix them.

Most problems show up when you create the external keyspace. Run [`pscale keyspace create-external`](../../cli/keyspace.md#create-an-external-keyspace) with `--dry-run` to check your source database without creating anything. Connection, server configuration, and user grant errors stop the external keyspace from being created. Schema errors are listed for each table but don't block creation.

## Connection issues

### Can't connect to external database

If PlanetScale can't connect to your database, here's what to check:

**Verify your credentials locally**

Try connecting with the same credentials using the MySQL CLI:

```bash theme={null}
mysql -u <USERNAME> -p -h <HOST> -P <PORT> -D <DATABASE>
```

If this works locally but not in PlanetScale, the issue is likely network-related.

**Check IP allowlist**

Make sure you've added all PlanetScale IP addresses to your database's firewall or security group. The specific IPs depend on which region you selected for your PlanetScale database. See our [Import public IP addresses](import-tool-migration-addresses.md) page.

**Verify database is publicly accessible**

PlanetScale needs to reach your database over the internet. Check that:

* Your database has a public IP address
* Public access is enabled in your database settings
* No VPN or private network is required

**SSL/TLS issues**

If you're getting SSL-related errors:

* Try `--ssl-mode disabled` to test if SSL is the issue
* If your database uses self-signed certificates, provide the full CA certificate chain
* For managed databases (RDS, Azure, etc.), use `--ssl-mode required` or `--ssl-mode verify_ca`

### Connection times out

If the connection attempt times out:

**Firewall rules**

Most timeouts are caused by firewall rules blocking PlanetScale IPs. Double-check:

* All IPs for your region are allowlisted
* The port (usually 3306) is open
* Any cloud provider security groups are configured correctly

**Database not running**

Verify your database server is running and accepting connections.

**Hostname/port incorrect**

Make sure you're using the correct hostname and port. For cloud providers:

* Use the cluster endpoint, not individual instance endpoints
* Default MySQL port is 3306, but some providers use different ports (DigitalOcean uses 25060)

## Server configuration errors

### GTID mode is OFF

**Error:** `external database settings are not compatible with PlanetScale: "gtid_mode" must be "ON", but found: "OFF"`

**Solution:**

You need to enable GTID mode in your database configuration.

For AWS RDS/Aurora:

1. Create a custom DB parameter group
2. Set `gtid-mode` to `ON`
3. Set `enforce_gtid_consistency` to `ON`
4. Apply the parameter group to your database
5. Reboot the database

For Azure:

1. Go to Server parameters
2. Set `gtid_mode` to `ON` (you may need to go through intermediate states: `OFF_PERMISSIVE` → `ON_PERMISSIVE` → `ON`)
3. Set `enforce_gtid_consistency` to `ON`
4. Save changes

For self-hosted MySQL/MariaDB:
Add to your `my.cnf` or `my.ini`:

```
gtid_mode = ON
enforce_gtid_consistency = ON
```

Then restart MySQL.

### Binary logging not enabled

**Error:** `external database settings are not compatible with PlanetScale: "log_bin" must be "ON", but found: "OFF"`

**Solution:**

For AWS RDS/Aurora:
Binary logging is tied to automated backups. Enable automated backups with a retention period >= 2 days.

For GCP Cloud SQL:
Enable Point in Time Recovery (PITR) from the console.

For self-hosted:
Add to your configuration:

```
log_bin = /var/log/mysql/mysql-bin.log
```

Restart MySQL.

### Wrong binlog format

**Error:** `"binlog_format" must be "ROW", but found: "MIXED"` or `"STATEMENT"`

**Solution:**

Set `binlog_format` to `ROW` in your database configuration, then restart.

If you see `"binlog_row_image" must be "FULL" or "NOBLOB"`, set `binlog_row_image` to `FULL`.

For managed databases, update this in your parameter group or server parameters.

### Binlog retention too short

**Error:** `"binlog_expire_logs_seconds" must be > 172800` (or similar for `expire_logs_days`). On AWS RDS and Aurora: `"binlog retention hours" must be at least 48 hours` or `binlog retention is not set on this AWS RDS database`.

**Solution:**

You need at least 48 hours of binlog retention for the import to work.

For AWS RDS/Aurora:

```sql theme={null}
CALL mysql.rds_set_configuration('binlog retention hours', 48);
```

Verify with:

```sql theme={null}
CALL mysql.rds_show_configuration;
```

For other platforms:
Set in your database configuration:

```
binlog_expire_logs_seconds = 172800
```

Or:

```
expire_logs_days = 3
```

### sql\_mode is not compatible

**Error:** `"sql_mode" cannot have "ANSI_QUOTES" enabled` or `PlanetScale requires "sql_mode" to have the following options set: "NO_ZERO_IN_DATE, NO_ZERO_DATE"`

**Solution:**

Remove `ANSI_QUOTES` from `sql_mode`, and make sure it includes `NO_ZERO_IN_DATE` and `NO_ZERO_DATE`. For managed databases, update this in your parameter group or server parameters.

### max\_connections is too low

**Error:** `PlanetScale requires external databases to support at least a max connection limit of 10`

**Solution:**

Set `max_connections` to at least 10. PlanetScale uses up to half of your `max_connections` for the import.

### Unsupported MySQL version

**Error:** `unsupported MySQL version detected`

**Solution:**

MySQL 5.6, 5.7, and 8.0 are supported. [Contact support](https://planetscale.com/contact?initial=support) if you need to import from a different version.

### Existing `_vt` database

**Error:** `external database may have existing Vitess state: found "_vt" Vitess state database`

**Solution:**

Your source database already has a `_vt` database, usually left behind by an earlier Vitess-based import or replication tool. Make sure nothing is using it, then drop it and try again:

```sql theme={null}
DROP DATABASE `_vt`;
```

## Schema compatibility issues

### No unique key on table

**Error:** Table has no unique key

**Solution:**

All tables must have a unique, not-null key. This is required for replication to work correctly.

Add a primary key or unique index to the table:

```sql theme={null}
ALTER TABLE your_table ADD PRIMARY KEY (id);
```

Or add a unique index:

```sql theme={null}
ALTER TABLE your_table ADD UNIQUE KEY unique_index (column1, column2);
```

See our [Changing unique keys documentation](../schema-changes/onlineddl-change-unique-keys.md) for more details.

### Invalid charset

**Error:** Table uses unsupported charset

**Solution:**

PlanetScale supports: `utf8`, `utf8mb4`, `utf8mb3`, `latin1`, and `ascii`.

Convert your table to a supported charset:

```sql theme={null}
ALTER TABLE your_table CONVERT TO CHARACTER SET utf8mb4;
```

We recommend `utf8mb4` as it has the widest character support.

### Table names with special characters

**Error:** Table name contains unsupported characters

**Solution:**

Rename tables that have characters outside the standard ASCII set:

```sql theme={null}
RENAME TABLE `special-table-name` TO `special_table_name`;
```

Ensure that any queries using the table name get updated as well.

### Views detected

Views aren't imported automatically. After your import completes, you'll need to manually recreate any views in your PlanetScale database.

### Unsupported storage engine

**Error:** Table uses non-InnoDB storage engine

**Solution:**

Convert your tables to InnoDB:

```sql theme={null}
ALTER TABLE your_table ENGINE=InnoDB;
```

Changing storage engines has significant performance impact. [Contact us](https://planetscale.com/contact?initial=support) if you are using a different storage engine on your source database and cannot change it prior to migrating.

## Foreign key import issues

### Import slower than expected

Foreign key imports use an atomic copy (`--atomic-copy`), which holds a long-running transaction on your source database. This can be slow on large databases.

**Solution:**

Run the import during off-peak hours, and make sure your source database has enough CPU and I/O headroom for the copy.

### Import failed and won't resume

Unlike regular imports, foreign key imports must start from the beginning if they fail.

**Solution:**

Before retrying:

1. Fix any errors that caused the failure
2. Make sure your binlog retention is long enough for the full import
3. Consider importing during off-peak hours

To retry, [cancel the workflow](database-imports.md#cancel-an-import) with `--keep-data=false` and create it again.

### Can't select specific tables

When your database has foreign keys, import every table with `--all-tables` to maintain referential integrity.

If you really only need specific tables, you'll need to:

1. Remove foreign key constraints from your source database
2. Import only the tables you need
3. Recreate foreign key constraints in PlanetScale after import

Note: We recommend importing all tables to avoid referential integrity issues.

## Schema errors when creating the external keyspace

### External keyspace created with schema errors

Schema errors, such as a table without a unique key, are listed for each table but don't stop the external keyspace from being created. Tables with schema errors will fail to import.

**Solution:**

* Fix the tables on your source database, then run `create-external --dry-run` again to confirm, or
* Leave those tables out of the import with `--exclude-tables` when you create the MoveTables workflow

Server configuration and user grant errors always need to be fixed first, because PlanetScale won't create the external keyspace until they pass.

## Import monitoring issues

### Replication lag is high

During the initial copy phase, high replication lag is normal. The lag should drop once the copy finishes.

**If lag stays high after copy completes:**

1. **Check source database load** - High write activity on source can cause lag
2. **Slow queries** - Look for slow queries or locks on the source database
3. **Network issues** - Check for network latency between source and PlanetScale
4. **Large transactions** - Very large transactions take time to replicate

**Solutions:**

* Reduce write load on source during import
* Wait for off-peak hours
* Check binlog retention isn't expiring before lag catches up

### Workflow stopped after a schema change

With the default `--on-ddl STOP`, the workflow stops when a schema change runs on your source database. The stream's message starts with `Stopped at DDL`.

**Solution:**

Review the schema change, apply it to your PlanetScale database if needed, then resume the workflow with `pscale branch vtctl move-tables start`.

### Streams show errors

Check the `message` on each stream in the [`status`](../../cli/move-tables.md) output, or the Streams panel on the workflow page in the dashboard. Common ones:

**"Access denied"** - Permission issues. See [user requirements](import-tool-user-requirements.md).

**"Table doesn't exist"** - Schema may have changed during import. Don't modify schema during import.

**"Deadlock found"** - Usually temporary. The import will retry.

**Connection lost** - Network issue or source database restarted. The import will retry.

### Import stuck in "Copying" phase

The copy phase can take a while for large databases. Check:

* Check `table_copy_state` in the `status` output, or the Tables panel on the workflow page, to see if it's actually stuck or just slow
* Check the stream messages for any errors
* Verify source database is responding

If truly stuck:

1. Check source database for locks or slow queries
2. Verify network connectivity
3. Look for errors in the stream messages

## Permission errors

### MySQL error 1045: Access denied

**Error:** `Access denied for user 'migration_user'@'%'`

**Solution:**

Check that your migration user has all required permissions. See our [import user permissions](import-tool-user-requirements.md).

For foreign key imports, the user needs either:

* `FLUSH_TABLES` or `RELOAD` privileges (preferred)
* `LOCK TABLES` privilege (minimum)

Verify grants:

```sql theme={null}
SHOW GRANTS FOR 'migration_user'@'%';
```

### Required grants are missing

**Error:** `external database does not have the required user grants: required privileges [...] are not present`

**Solution:**

Grant the privileges listed in the error. They must be granted directly to the user at the global or database level. See [Import user permissions](import-tool-user-requirements.md).

If the error says the permissions `do not match user`, the user was created for a specific host. Create it for the host `'%'` instead.

### Can't create the ps\_import database

**Solution:**

Grant the migration user permissions on the database PlanetScale creates to track replication:

```sql theme={null}
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, ALTER
  ON `ps\_import\_%`.* TO 'migration_user'@'%';
```

## Traffic switching issues

### Can't switch replica traffic

Make sure:

* Replication lag is low (under a few seconds). `switch-traffic` fails if lag is above `--max-replication-lag-allowed`
* Every stream is `Running`
* No stream shows an error

### Can't switch primary traffic

Make sure:

* Your application is connected to PlanetScale
* Replication lag is minimal
* Every stream is `Running`

### Data inconsistency after switching

If you notice missing or stale data after switching traffic:

1. Check replication lag - it may still be catching up
2. Verify your application is actually connecting to PlanetScale
3. Run a [VDiff](database-imports.md#verify-data-optional) to compare your source database with PlanetScale

Don't complete the import until you've verified data consistency.

## Completing the import

### Complete fails with `keep_data must be true`

**Error:** `keep_data must be true when the source keyspace is external`

**Solution:**

Completing an import never removes tables from your source database. Pass `--keep-data=true`, with an `=`, and leave out `--rename-tables`.

## Common provider-specific issues

### AWS RDS

**Problem:** Can't modify GTID settings on default parameter group

**Solution:** Create a custom DB parameter group with your MySQL version, modify settings there, then apply to your database.

**Problem:** Binary logs not enabled

**Solution:** Enable automated backups with retention >= 2 days.

### Azure

**Problem:** Can't set gtid\_mode directly to ON

**Solution:** Change through intermediate states: `OFF_PERMISSIVE` → `ON_PERMISSIVE` → `ON`

### DigitalOcean

**Problem:** ANSI\_QUOTES mode enabled

**Solution:** Remove ANSI\_QUOTES from Global SQL mode in Settings.

**Problem:** Binlog retention too short

**Solution:** Set Binlog Retention Period to at least 172800 seconds (48 hours).

### GCP Cloud SQL

**Problem:** Binary logging disabled

**Solution:** Enable Point in Time Recovery (PITR) from the GCP console.

## Still stuck?

If you've tried the solutions above and are still having issues:

1. Check your database's error logs
2. Review our [general MySQL compatibility guide](../troubleshooting/mysql-compatibility.md)
3. Look at the specific provider guide for your database
4. Check the `status` output or the workflow page in the dashboard for detailed error messages
5. [Contact PlanetScale support](https://planetscale.com/contact?initial=support)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
