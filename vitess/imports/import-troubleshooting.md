---
url: https://planetscale.com/docs/vitess/imports/import-troubleshooting
title: "Import Troubleshooting"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Import troubleshooting

**Platform availability:** Vitess only

## Overview

This guide covers common issues you might run into when importing a database to PlanetScale and how to fix them.

## Connection issues

### Can't connect to external database

If the connection test fails, here's what to check:

**Verify your credentials locally**

Try connecting with the same credentials using the MySQL CLI:

```sql theme={null}
mysql -u <USERNAME> -p <PASSWORD> -h <HOST> -P <PORT> -D <DATABASE>
```

If this works locally but not in PlanetScale, the issue is likely network-related.

**Check IP allowlist**

Make sure you've added all PlanetScale IP addresses to your database's firewall or security group. The specific IPs depend on which region you selected for your PlanetScale database. See our [Import tool public IP addresses](import-tool-migration-addresses.md) page.

**Verify database is publicly accessible**

PlanetScale needs to reach your database over the internet. Check that:

* Your database has a public IP address
* Public access is enabled in your database settings
* No VPN or private network is required

**SSL/TLS issues**

If you're getting SSL-related errors:

* Try setting SSL verification mode to `Disabled` to test if SSL is the issue
* If your database uses self-signed certificates, provide the full CA certificate chain
* For managed databases (RDS, Azure, etc.), use `Required` or `Verify CA` mode

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

For managed databases, update this in your parameter group or server parameters.

### Binlog retention too short

**Error:** `"binlog_expire_logs_seconds" must be > 172800` (or similar for `expire_logs_days`)

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

Foreign key imports hold a long-running transaction, which can be slow on large databases.

**Solution:**

Connect to a read replica instead of your primary database. This reduces load and can improve performance.

### Import failed and won't resume

Unlike regular imports, foreign key imports must start from the beginning if they fail.

**Solution:**

Before retrying:

1. Fix any errors that caused the failure
2. Make sure your binlog retention is long enough for the full import
3. Consider importing during off-peak hours
4. Ensure your replica (if using one) is healthy and has minimal replication lag

### Can't select specific tables

When foreign keys are detected, all tables are automatically selected to maintain referential integrity. This is expected behavior.

If you really only need specific tables, you'll need to:

1. Remove foreign key constraints from your source database
2. Import only the tables you need
3. Recreate foreign key constraints in PlanetScale after import

Note: We recommend importing all tables to avoid referential integrity issues.

## Validation errors with skip option

### Validation failed but can skip

For certain validation failures, you'll see an option to skip and continue. This forces all tables to be imported.

**When to skip:**

* You understand the risks
* You'll fix the issues in PlanetScale after import
* The validation is a false positive

**When NOT to skip:**

* Server configuration issues (GTID, binlog) - these will cause the import to fail later
* You're not sure what the error means
* Production import (always fix issues first)

If you skip validation errors, you won't be able to select specific tables - everything gets imported.

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

### Logs show errors

Check the logs section for specific error messages. Common ones:

**"Access denied"** - Permission issues. See [user requirements](import-tool-user-requirements.md).

**"Table doesn't exist"** - Schema may have changed during import. Don't modify schema during import.

**"Deadlock found"** - Usually temporary. The import will retry.

**Connection lost** - Network issue or source database restarted. The import will retry.

### Import stuck in "Copying" phase

The copy phase can take a while for large databases. Check:

* Look at per-table progress indicators to see if it's actually stuck or just slow
* Check logs for any errors
* Verify source database is responding

If truly stuck:

1. Check source database for locks or slow queries
2. Verify network connectivity
3. Look for errors in logs

## Permission errors

### MySQL error 1045: Access denied

**Error:** `Access denied for user 'migration_user'@'%'`

**Solution:**

Check that your migration user has all required permissions. See our [import tool user requirements](import-tool-user-requirements.md).

For foreign key imports, the user needs either:

* `FLUSH_TABLES` or `RELOAD` privileges (preferred)
* `LOCK TABLES` privilege (minimum)

Verify grants:

```sql theme={null}
SHOW GRANTS FOR 'migration_user'@'%';
```

### Can't create *vt or ps\_import* databases

**Solution:**

Grant the migration user permissions on these databases:

```sql theme={null}
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, ALTER
  ON `ps\_import\_%`.* TO 'migration_user'@'%';
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, ALTER
  ON `_vt`.* TO 'migration_user'@'%';
```

## Traffic switching issues

### Can't switch replica traffic

Make sure:

* Replication lag is low (under a few seconds)
* Import is in "Running" state
* No errors in logs

### Can't switch primary traffic

Make sure:

* Replica traffic has been switched first
* Your application is connected to PlanetScale
* Replication lag is minimal

### Data inconsistency after switching

If you notice missing or stale data after switching traffic:

1. Check replication lag - it may still be catching up
2. Verify your application is actually connecting to PlanetScale
3. Check for any errors in workflow logs

Don't complete the import until you've verified data consistency.

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

**Solution:** Set Binlog Retention Period to 86400 seconds (24 hours minimum, max available).

### GCP Cloud SQL

**Problem:** Binary logging disabled

**Solution:** Enable Point in Time Recovery (PITR) from the GCP console.

## Still stuck?

If you've tried the solutions above and are still having issues:

1. Check your database's error logs
2. Review our [general MySQL compatibility guide](../troubleshooting/mysql-compatibility.md)
3. Look at the specific provider guide for your database
4. Check workflow logs in PlanetScale for detailed error messages
5. [Contact PlanetScale support](https://planetscale.com/contact?initial=support)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
