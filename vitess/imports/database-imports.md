---
url: https://planetscale.com/docs/vitess/imports/database-imports
title: "Database Imports"
description: ""
access_date: 2026-10-02T19:13:10.531Z
current_date: 2026-10-02T19:13:10.531Z
---

## Overview

You can import an existing internet-accessible MySQL database into a PlanetScale Vitess database with **no downtime**. An import has two parts:

1. An [external keyspace](../cluster-configuration.md#create-an-external-keyspace) connects your production branch to your existing MySQL database.
2. A [Vitess MoveTables](../../cli/move-tables.md) workflow copies the tables from the external keyspace into a PlanetScale keyspace, keeps them in sync while your application keeps running, and switches traffic to PlanetScale when you’re ready.

You run the import with the [`pscale` CLI](../../cli.md). The **Workflows** page in the dashboard shows the progress of the import, but it is read-only: every step on this page is a CLI command.

You must be an [Organization Administrator](../../security/access-control.md#organization-administrator) or a [Database Administrator](../../security/access-control.md#database-administrator) of the database to run an import. To run an import with a [service token](../../api/service-tokens.md), give it the `create_branch`, `read_workflow`, `write_workflow`, and `delete_workflow` permissions on the database, plus `delete_production_branch` to delete the external keyspace at the end.

Before you begin, it may be helpful to check out our [general MySQL compatibility guide](../troubleshooting/mysql-compatibility.md).

## Import process overview

1. **Prepare your source database** - Check its server settings, create a user for PlanetScale, and allow PlanetScale’s IP addresses
2. **Create your PlanetScale database** - Create the Vitess database you are importing into
3. **Create an external keyspace** - Connect the production branch to your source database. PlanetScale checks connectivity, server settings, user grants, and schema compatibility first
4. **Start the import** - Create a MoveTables workflow from the external keyspace to your PlanetScale keyspace
5. **Monitor the import** - Watch the copy and replication progress, then verify the data
6. **Connect your application to PlanetScale** - Test your application against PlanetScale while your source database is still serving traffic
7. **Switch traffic** - Move reads, then writes, to PlanetScale
8. **Complete the import** - Stop replication and disconnect your source database

It’s recommended to avoid all schema changes / DDL (Data Definition Language) statements during an import on both your source database and the PlanetScale database. This includes `CREATE`, `DROP`, `ALTER`, `TRUNCATE`, etc.

The examples on this page import a MySQL database named `commerce` into a PlanetScale database named `commerce` in the `acme` organization.

## Step 1: Prepare your source database

### Server configuration

These server settings need to be set correctly for the import to work:

| Variable | Required value | Documentation |
| --- | --- | --- |
| `gtid_mode` | `ON` | [Documentation](https://dev.mysql.com/doc/refman/8.0/en/replication-options-gtids.html#sysvar_gtid_mode) |
| `log_bin` | `ON` | [Documentation](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) |
| `binlog_format` | `ROW` | [Documentation](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_binlog_format) |
| `binlog_row_image` | `FULL` or `NOBLOB` | [Documentation](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_binlog_row_image) |
| `expire_logs_days` \* | `>= 2` | [Documentation](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_expire_logs_days) |
| `binlog_expire_logs_seconds` \* | `>= 172800` | [Documentation](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_binlog_expire_logs_seconds) |
| `sql_mode` | Includes `NO_ZERO_IN_DATE` and `NO_ZERO_DATE`, and does not include `ANSI_QUOTES` | [Documentation](https://dev.mysql.com/doc/refman/8.0/en/sql-mode.html) |
| `max_connections` | `>= 10` | [Documentation](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_max_connections) |

**\*** Either `expire_logs_days` or `binlog_expire_logs_seconds` needs to be set. If both are set, `binlog_expire_logs_seconds` takes precedence. On Amazon RDS and Aurora, set `binlog retention hours` to at least 48 instead. See the [provider-specific migration guides](#provider-specific-migration-guides) for how to change these settings.

MySQL 5.6, 5.7, and 8.0 are supported. The source database must not already contain a `_vt` database.

### Create a user for PlanetScale

PlanetScale connects to your source database with a single user. That user needs replication privileges, read and write access to the database you’re importing, and access to the `ps_import_*` database that PlanetScale creates on your source to track replication. See [Import user permissions](import-tool-user-requirements.md) for the full list of grants and a script that creates the user.

### Allow PlanetScale’s IP addresses

Allow connections from PlanetScale’s IP addresses for your database’s region in your database firewall or security group. See [Import public IP addresses](import-tool-migration-addresses.md) for where to find them.

## Step 2: Create your PlanetScale database

Create the Vitess database you’re importing into:

```shellscript
pscale database create commerce --org acme --region us-east --cluster-size PS_10 --wait
```

Pick a [region](../../plans/regions.md) close to your source database and a [cluster size](../scaling/cluster-sizing.md) with enough storage for your data. You can also create the database from the dashboard with “ **New database** ” > “ **Create database** ”.

We recommend using the same name as the database you’re importing from to avoid updating any database name references throughout your application code. Your PlanetScale database starts with one keyspace, named after the database. That keyspace is the **target** of the import. If you’d prefer to use a different database name, make sure to update your app where applicable once you fully switch over to PlanetScale.

## Step 3: Create an external keyspace

An external keyspace connects your production branch to your source database. It is the **source** of the import. Give it a name that is different from your PlanetScale keyspace, such as `commerce_source`.

### Connection settings

| Setting | `pscale keyspace create-external` flag | Description |
| --- | --- | --- |
| Host name | `--host` | The address where your database is hosted. |
| Port | `--port` | The port where your database is hosted. The default MySQL port is `3306`. |
| Database name | `--source-database` | The exact name of the database you want to import on your MySQL server. |
| Username | `--username` | The [user you created for PlanetScale](import-tool-user-requirements.md). |
| Password | `--password` | The password for that user. |
| SSL verification mode | `--ssl-mode` | `disabled`, `preferred`, `required`, `verify_ca`, or `verify_identity`. |
| SSL client certificate and key | `--ssl-client-certificate`, `--ssl-client-key` | Authenticate with mTLS instead of a password. Pass the PEM contents or a path to a PEM file. |
| SSL CA certificate chain | `--ssl-certificate-authority` | If your database server has a certificate with a non-trusted root CA, provide the full CA certificate chain here. |
| SSL server name override | `--ssl-server-name` | Override the server name for SSL certificate verification. |
| Minimum TLS version | `--min-tls-version` | The oldest TLS version PlanetScale may use. The default is TLS 1.2. |

If your database server has a valid SSL certificate, set the SSL verification mode to `required` or higher. For more information about certificates from a Certificate Authority, check out our [Secure connections documentation](../connecting/secure-connections.md#certificate-authorities).

### Check your source database

Run `create-external` with `--dry-run` to check your source database without creating anything:

```shellscript
pscale keyspace create-external commerce main commerce_source \
  --org acme \
  --host db.example.com \
  --source-database commerce \
  --username migration_user \
  --password <PASSWORD> \
  --ssl-mode required \
  --dry-run
```

PlanetScale checks:

- **Connectivity** - It can connect with the credentials and SSL/TLS settings you provided.
- **Server configuration** - The [server settings](#server-configuration) above are correct.
- **User grants** - The user has the [required grants](import-tool-user-requirements.md).
- **Schema compatibility** - Every table can be imported. See [Schema compatibility](#schema-compatibility) below.

If PlanetScale cannot connect, or the server settings or grants are wrong, it won’t create the external keyspace. Fix the issue on your source database and run the check again. The [Import troubleshooting guide](import-troubleshooting.md) covers each error.

### Schema compatibility

The check also reports tables that can’t be imported:

- **Missing unique key** - All tables must have a unique, not-null key. See our [Changing unique keys documentation](../schema-changes/onlineddl-change-unique-keys.md) for more info.
- **Invalid charset** - We support `utf8`, `utf8mb4`, `utf8mb3`, `latin1`, and `ascii`. Tables with other charsets will be flagged.
- **Unsupported storage engines** - Only `InnoDB` is supported.
- **Unsupported partitioning** - Only `RANGE` partitioning is supported, without subpartitions.

Schema errors don’t stop you from creating the external keyspace. Fix those tables on your source database, or leave them out of the import with `--exclude-tables` in the next step.

If your database uses foreign key constraints, PlanetScale turns on [foreign key support](../foreign-key-constraints.md) for your PlanetScale database when you create the external keyspace. See [Foreign key constraints](#foreign-key-constraints) before you start the import.

### Create the external keyspace

Once the check passes, run the same command with `--wait` instead of `--dry-run`:

```shellscript
pscale keyspace create-external commerce main commerce_source \
  --org acme \
  --host db.example.com \
  --source-database commerce \
  --username migration_user \
  --password <PASSWORD> \
  --ssl-mode required \
  --wait
```

PlanetScale sizes the external keyspace from the size of your source data.

You can also create the external keyspace from the dashboard: open your production branch, click “ **Clusters** ”, then “ **New keyspace** ” > “ **Create external keyspace** ”. The form checks your source database the same way before it creates the keyspace, and lists the IP addresses to allow.

## Step 4: Start the import

Create a MoveTables workflow that copies the tables from the external keyspace into your PlanetScale keyspace:

```shellscript
pscale branch vtctl move-tables create commerce main \
  --org acme \
  --workflow import_commerce \
  --source-keyspace commerce_source \
  --target-keyspace commerce \
  --all-tables \
  --defer-secondary-keys \
  --format json
```

The workflow starts right away. You will pass the workflow name and the target keyspace to every later command.

### Import options

- **Tables** - `--all-tables` imports every table. To import only some tables, pass `--tables` with a comma-separated list instead. To import every table except a few, combine `--all-tables` with `--exclude-tables`.
- **Defer secondary index creation** - `--defer-secondary-keys` creates secondary (non-primary) indexes after the data is copied instead of during the copy. Maintaining many indexes while inserting data is slow, so this can make your import significantly faster (often 2-3x faster for tables with multiple indexes). Secondary indexes are created during the copy unless you pass this flag. Don’t use it if your database has foreign key constraints.
- **DDL handling** - `--on-ddl` controls what happens if schema changes (like `ALTER TABLE`, `ADD INDEX`, etc.) run on your source database while the import is running:
	- `STOP` (default, recommended) - The workflow stops when a schema change is detected. After you review the change, restart the workflow with `pscale branch vtctl move-tables start`. This is the safest option because it lets you verify the schema changes won’t cause issues before continuing.
		- `IGNORE` - Schema changes are skipped and won’t be applied to your PlanetScale database. Your import continues without interruption, but your schemas will diverge. Only use this if you’re confident you don’t need these changes or plan to apply them manually to your PlanetScale database later.
		- `EXEC` - Schema changes are applied to your PlanetScale database while the import continues running. If applying a schema change fails (for example, if it’s not compatible with Vitess), the workflow stops and you’ll need to restart it.
		- `EXEC_IGNORE` - Attempts to apply schema changes but keeps running even if they fail.

`EXEC_IGNORE` can lead to schema mismatches between your external database and PlanetScale database, potentially causing data inconsistencies or unexpected behavior. Only use this if you understand the risks and have a plan to handle failures.

See the [`move-tables` reference](../../cli/move-tables.md) for every option.

### Foreign key constraints

If your database uses foreign key constraints:

- **Import all tables** - Pass `--all-tables` without `--exclude-tables` so referential integrity stays intact.
- **Use an atomic copy** - Pass `--atomic-copy`, and don’t pass `--defer-secondary-keys`. Foreign key constraints need their indexes to exist during the copy.
- **Import retries** - An atomic copy holds a long-running transaction on your source database, which can increase load. If it fails, it starts over from the beginning instead of resuming where it left off.

```shellscript
pscale branch vtctl move-tables create commerce main \
  --org acme \
  --workflow import_commerce \
  --source-keyspace commerce_source \
  --target-keyspace commerce \
  --all-tables \
  --atomic-copy \
  --format json
```

For more information about foreign key support and limitations, see our [foreign key constraints documentation](../foreign-key-constraints.md).

## Step 5: Monitor your import

Check the progress of the import with `status`:

```shellscript
pscale branch vtctl move-tables status commerce main \
  --org acme \
  --workflow import_commerce \
  --target-keyspace commerce \
  --format json
```

The output shows:

- **`table_copy_state`** - Rows and bytes copied so far for each table that is still copying
- **`shard_streams`** - Each replication stream’s state and any error message
- **`traffic_state`** - Whether reads and writes have switched to PlanetScale

Streams are `Copying` while the initial data is copied, then `Running` once the import is replicating new changes from your source database. A `Lagging` stream is running but hasn’t caught up with recent changes. A `Stopped` or `Error` stream includes a message that explains why. The output also includes a `next_steps` field with the command to run next.

You can also follow the import in the dashboard. Click “ **Workflows** ” in the left nav, select your production branch, and open the workflow to see its streams, per-table copy progress, replication lag, and traffic routing.

To pause the import, run `pscale branch vtctl move-tables stop`. Run `pscale branch vtctl move-tables start` to resume it.

### Verify data (optional)

Once the copy completes and the streams are `Running`, you can verify that the data in PlanetScale matches your source database with a [VDiff](https://vitess.io/docs/reference/vreplication/vdiff/):

```shellscript
pscale branch vtctl vdiff create commerce main \
  --org acme \
  --workflow import_commerce \
  --target-keyspace commerce \
  --format json
```

The output includes the VDiff’s `uuid`. Pass it to `vdiff show` and repeat until the VDiff completes:

```shellscript
pscale branch vtctl vdiff show commerce main \
  --org acme \
  --workflow import_commerce \
  --target-keyspace commerce \
  --uuid <VDIFF_UUID> \
  --format json
```

The dashboard also shows the VDiff’s progress on the workflow page. If the VDiff reports mismatches, don’t switch traffic until you’ve resolved them.

## Step 6: Connect your application to PlanetScale

Once the streams are `Running`, you can connect your application to PlanetScale while your source database stays the authoritative source. Until you switch traffic, PlanetScale routes queries for the imported tables to your source database through the external keyspace, so reads and writes still happen there.

[Create a password](../connecting/connection-strings.md) for your production branch and point a test deployment of your application at PlanetScale. This is the ideal time to test your application end-to-end before switching traffic.

## Step 7: Switch traffic

When you’re ready, switch traffic to PlanetScale. You can switch replica traffic first to test reads, then switch primary traffic.

1. **Switch replica traffic** - Serve read queries sent to replicas from PlanetScale while writes still go to your source database. This is an optional intermediate step that lets you test read traffic separately.
	```shellscript
	pscale branch vtctl move-tables switch-traffic commerce main \
	  --org acme \
	  --workflow import_commerce \
	  --target-keyspace commerce \
	  --tablet-types REPLICA,RDONLY \
	  --format json
	```
2. **Switch primary traffic** - Serve both reads and writes from PlanetScale.
	```shellscript
	pscale branch vtctl move-tables switch-traffic commerce main \
	  --org acme \
	  --workflow import_commerce \
	  --target-keyspace commerce \
	  --tablet-types PRIMARY \
	  --format json
	```

Pass `--dry-run` to see what a switch would do without applying it.

**Critical: Update connection strings before switching primary traffic**

You must update your application’s connection string to point to PlanetScale **before** switching primary traffic. If you switch primary traffic while your application is still connected to your external database, you will create a split-brain scenario where:

- PlanetScale believes it is serving all traffic (reads and writes)
- Your application continues writing to the external database
- The two databases diverge, causing data inconsistency and potential data loss

Always verify your application is connected to PlanetScale before proceeding with the primary traffic switch.

After primary traffic switches, PlanetScale replicates writes back to your source database, so both stay in sync until you complete the import. If something goes wrong, switch traffic back to your source database:

```shellscript
pscale branch vtctl move-tables reverse-traffic commerce main \
  --org acme \
  --workflow import_commerce \
  --target-keyspace commerce \
  --format json
```

## Step 8: Complete the import

Once you’ve switched all traffic to PlanetScale and verified everything is working:

`--keep-data` must be `true` when you complete an import. Your source database’s tables are never dropped or renamed. `--keep-routing-rules=false` removes the routing rules that sent queries to the external keyspace. Write both flags with an `=`: a space-separated value such as `--keep-data false` is read as `--keep-data=true`.

**What happens when you complete:**

- Replication from PlanetScale back to your source database stops
- The routing rules for the imported tables are removed
- The workflow no longer appears on the Workflows page

**What happens when you delete the external keyspace:**

- PlanetScale disconnects from your source database
- The source database’s credentials are removed from PlanetScale

**Important**

Completing the workflow is not reversible. Make sure your application is running smoothly on PlanetScale before completing the import. Don’t delete the external keyspace until the workflow is complete.

Once the import is complete, you can drop the `ps_import_*` database from your source database and remove the user you created for PlanetScale.

### Cancel an import

To stop an import before you complete it, cancel the workflow:

```shellscript
pscale branch vtctl move-tables cancel commerce main \
  --org acme \
  --workflow import_commerce \
  --target-keyspace commerce \
  --keep-data=false \
  --keep-routing-rules=false \
  --format json
```

`--keep-data=false` deletes the data already copied into your PlanetScale keyspace. The external keyspace stays connected, so you can fix the problem and start a new workflow. Delete the external keyspace if you don’t plan to try again.

## Next steps

You just migrated your database to PlanetScale. Here are some things you can do next:

- [Create a development branch](../schema-changes/branching.md) - Use branching in your development workflow.
- [Create a deploy request](../schema-changes/deploy-requests.md#create-a-deploy-request) - Test schema changes in dev branches before pushing to production.

## Provider-specific migration guides

For detailed instructions on preparing your external database for import, see our provider-specific guides:

- [Amazon Aurora](amazon-aurora-migration-guide.md)
- [AWS RDS for MySQL](aws-rds-migration-guide.md)
- [Azure Database for MySQL](azure-database-for-mysql-migration-guide.md)
- [DigitalOcean MySQL](digitalocean-database-migration-guide.md)
- [Google Cloud SQL](gcp-cloudsql-migration-guide.md)
- [MariaDB](mariadb-migration-guide.md)

## Troubleshooting

For detailed troubleshooting guidance, see our [Import troubleshooting guide](import-troubleshooting.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
