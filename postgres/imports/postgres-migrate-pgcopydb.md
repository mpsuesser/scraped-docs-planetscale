---
url: https://planetscale.com/docs/postgres/imports/postgres-migrate-pgcopydb
title: "Postgres Migrate Pgcopydb"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

[pgcopydb](https://github.com/planetscale/pgcopydb) is a tool that automates migrating data between two running PostgreSQL servers. PlanetScale maintains a fork that supports PostgreSQL 17 and 18, improved filtering, resilient retry, and improved CDC support. This guide uses PlanetScale’s fork throughout.

PlanetScale also provides [helper scripts](https://github.com/planetscale/migration-scripts/tree/main/pgcopydb-helpers) that automate the setup, monitoring, cutover, and cleanup of the migration process. PlanetScale has used pgcopydb and these scripts to migrate multi-terabyte databases from live customer environments, with speeds as fast as 2 TB per hour using [Metal](../../metal.md).

pgcopydb is a highly capable tool with extensive tunables and flags for handling a wide range of migration scenarios. The helper scripts in this guide use sensible defaults, but you can customize them for your specific environment. See the [pgcopydb documentation](https://github.com/planetscale/pgcopydb) for in-depth coverage of all available options. We recommend testing the migration on a staging environment or from a backup/snapshot before running it on production, and always ensure you have a backup of your source database before starting.

## How pgcopydb works

Traditional `pg_dump | pg_restore` pipelines write all data to intermediate files on disk before restoring to the target. This doubles disk I/O, creates storage bottlenecks, and builds indexes one at a time. For large databases, this can take days.

pgcopydb takes a different approach:

1. **Direct streaming with no intermediate files.** Table data flows directly from source to target using PostgreSQL’s native COPY protocol. No dump files are written to disk. Data moves over the network in a single pass.
2. **Parallel operations at every stage.** pgcopydb runs multiple operations concurrently:
	- Multiple tables are copied in parallel
		- Large tables are automatically split into partitions and copied by multiple workers simultaneously
		- Indexes are built concurrently on the target, not sequentially like `pg_restore`
		- VACUUM, sequence resets, and constraint creation all run in parallel
3. **Live migration with Change Data Capture (CDC).** With the `--follow` flag, pgcopydb captures changes from the source via logical decoding while the initial copy is still running. Changes are prefetched to disk during the copy phase and replayed once the copy finishes. The target stays in sync with the source, and cutover requires only a brief pause while the final changes apply.

Internally, pgcopydb proceeds through these phases:

| Phase | Description |
| --- | --- |
| **Catalog source** | Query source metadata, apply filters, estimate table sizes |
| **Dump schema** | Export schema using `pg_dump` with a consistent snapshot |
| **Restore schema** | Create tables and types on the target with `pg_restore` |
| **Copy table data** | Stream data in parallel using COPY protocol |
| **Restore post-data** | Create views, materialized views, and functions |
| **Create indexes** | Build all indexes concurrently |
| **Create constraints** | Apply foreign keys and check constraints |
| **Vacuum and analyze** | Update table statistics and reclaim space |
| **Reset sequences** | Synchronize sequence values from source |
| **Finalize** | Complete post-data restoration |

When `--follow` is enabled, CDC runs alongside these phases. Changes are prefetched during the copy and switch to live replay once the copy completes. The migration is ready for cutover when CDC lag drops to near zero.

## Overview

The migration follows this high-level flow:

If you do not need near-zero downtime and can tolerate a maintenance window, you can use pgcopydb without CDC (omit the `--follow` flag). This is simpler but requires stopping writes to the source for the duration of the copy.

## 1\. Prepare your environment

The migration runs from a dedicated **migration instance**, a compute instance (EC2, GCE, or similar) with network access to both your source database and PlanetScale. This instance runs pgcopydb and the helper scripts. It does not store any persistent data beyond the migration working directory.

PlanetScale provides infrastructure-as-code templates that provision a fully configured migration instance with pgcopydb v0.18.0, PostgreSQL 17 client tools, and the helper scripts pre-installed. Choose the template that matches your cloud provider and provisioning tool:

#### AWS CloudFormation

Upload the [CloudFormation template](https://github.com/planetscale/migration-scripts/tree/main/pgcopydb-templates/aws-cloudformation) to CloudFormation and fill in VPC ID, Subnet ID, instance type, and volume size. Or deploy via the CLI:

```shellscript
aws cloudformation create-stack \
  --stack-name planetscale-migration \
  --template-body file://pgcopydb-migration-instance.yaml \
  --parameters \
    ParameterKey=VpcId,ParameterValue=vpc-xxxxxxxxx \
    ParameterKey=SubnetId,ParameterValue=subnet-xxxxxxxxx \
    ParameterKey=InstanceType,ParameterValue=c7i.xlarge \
    ParameterKey=VolumeSize,ParameterValue=1000 \
  --capabilities CAPABILITY_NAMED_IAM
```

**Key parameters:**

- `VpcId` / `SubnetId` — The VPC and public subnet where the instance will launch. The subnet must have an internet gateway route for PlanetScale connectivity.
- `InstanceType` — `c7i.xlarge` for databases under 100 GB, `c7i.2xlarge` for 100–500 GB, `c7i.4xlarge` for 500 GB+.
- `VolumeSize` — EBS io2 Block Express volume in GB. Choose based on your source database size (500, 1000, 3000, 6000, or 12000).
- `KeyPairName` — Optional. Leave empty to use EC2 Instance Connect for SSH access.

**Connect to the instance** via SSM Session Manager (no SSH key needed):

```shellscript
aws ssm start-session --target INSTANCE_ID
```

You can also use EC2 Instance Connect. Open the instance in the EC2 console and click **Connect** for browser-based SSH.

#### AWS Terraform

Save the [Terraform template](https://github.com/planetscale/migration-scripts/tree/main/pgcopydb-templates/aws-terraform) files (`pgcopydb-migration-instance.tf` and `user-data.sh`) in the same directory and deploy:

```shellscript
terraform init
terraform apply \
  -var="vpc_id=vpc-xxxxxxxxx" \
  -var="subnet_id=subnet-xxxxxxxxx" \
  -var="instance_type=c7i.xlarge" \
  -var="volume_size=1000"
```

**Key variables:**

- `vpc_id` / `subnet_id` — The VPC and public subnet where the instance will launch. The subnet must have an internet gateway route for PlanetScale connectivity.
- `instance_type` — `c7i.xlarge` for databases under 100 GB, `c7i.2xlarge` for 100–500 GB, `c7i.4xlarge` for 500 GB+.
- `volume_size` — EBS io2 Block Express volume in GB (500, 1000, 3000, 6000, or 12000).
- `key_pair_name` — Optional. Leave empty to use EC2 Instance Connect for SSH access.
- `region` — AWS region (default: `us-east-1`).

**Connect to the instance** via SSM Session Manager:

```shellscript
aws ssm start-session --target $(terraform output -raw instance_id)
```

You can also use EC2 Instance Connect. Open the instance in the EC2 console and click **Connect** for browser-based SSH.

#### GCP Terraform

Save the [Terraform template](https://github.com/planetscale/migration-scripts/tree/main/pgcopydb-templates/gcp-terraform) files (`pgcopydb-migration-instance.tf` and `startup-script.sh`) in the same directory and deploy:

```shellscript
terraform init
terraform apply \
  -var="project_id=my-project" \
  -var="vpc_name=my-vpc" \
  -var="subnet_name=my-subnet" \
  -var="service_account_email=sa@my-project.iam.gserviceaccount.com" \
  -var="machine_type=n2-standard-8" \
  -var="disk_size_gb=500"
```

**Key variables:**

- `project_id` — Your GCP project ID.
- `vpc_name` / `subnet_name` — The VPC and subnet where the instance will launch.
- `service_account_email` — Service account to attach to the instance.
- `machine_type` — `n2-standard-8` or `c3d-standard` family. Size based on your source database.
- `disk_size_gb` — Boot disk size in GB (100–10000). Choose based on your source database size.
- `region` / `zone` — GCP region and zone (default: `us-central1-a`).

**Connect to the instance** via IAP tunnel:

```shellscript
gcloud compute ssh $(terraform output -raw instance_name) \
  --zone=$(terraform output -raw zone) \
  --project=YOUR_PROJECT \
  --tunnel-through-iap
```

You can also use the GCP Console. Open the instance in Compute Engine and click **SSH** for browser-based access.

If you prefer to set up your own instance manually, see the [pgcopydb installation documentation](https://github.com/planetscale/pgcopydb#install) and the [helper scripts README](https://github.com/planetscale/migration-scripts/tree/main/pgcopydb-helpers) for manual installation steps.

Once your migration instance is running, **connect to it** using one of the methods shown above. All commands and scripts in the remaining steps are run from the migration instance. The examples in this guide assume you are logged in as the `ubuntu` user on an instance created by the templates, with helper scripts installed in `/home/ubuntu/`.

### Configure connection credentials

The templates create a `~/.env` file with placeholder connection strings. Update this file with your actual source and target credentials:

```shellscript
# Source Database
PGCOPYDB_SOURCE_PGURI="postgresql://user:password@source-host:5432/dbname?sslmode=require"

# Target Database (PlanetScale)
PGCOPYDB_TARGET_PGURI="postgresql://user:password@target-host.connect.psdb.cloud:5432/dbname?sslmode=require"
```

All helper scripts read from this file automatically.

This file contains database credentials. Restrict permissions with `chmod 600 ~/.env`.

## 2\. Prepare your source database

### Create a migration user

The migration user needs read access to all schemas and tables being migrated. For CDC migrations using `--follow`, it also needs the `REPLICATION` attribute. If you are using a dedicated migration user rather than the database owner, create one and grant the required permissions:

```sql
-- Create the migration user
CREATE ROLE migration_user WITH LOGIN PASSWORD 'your-strong-password' REPLICATION;

-- Connect and schema access
GRANT CONNECT ON DATABASE mydb TO migration_user;
GRANT USAGE ON SCHEMA public TO migration_user;

-- Read access to all tables and sequences
GRANT SELECT ON ALL TABLES IN SCHEMA public TO migration_user;
GRANT SELECT ON ALL SEQUENCES IN SCHEMA public TO migration_user;

-- For future tables created before migration starts
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO migration_user;

-- Prevent idle replication connection from being dropped during long COPY phases
ALTER ROLE migration_user SET wal_sender_timeout = 0;
```

Repeat the `GRANT USAGE`, `GRANT SELECT`, and `ALTER DEFAULT PRIVILEGES` statements for each schema being migrated.

Setting `wal_sender_timeout` to `0` on the source prevents the replication slot from being dropped during long COPY phases. The initial data copy can take hours on large databases, and the default timeout (60s) may cause PostgreSQL to drop the idle replication connection before CDC streaming begins.

Once the user is created, update the `PGCOPYDB_SOURCE_PGURI` in your `~/.env` file on the migration instance with the new credentials.

### Enable logical replication (CDC only)

For CDC migrations using `--follow`, logical replication must be enabled on the source. How to enable it depends on your platform:

- **Amazon RDS / Aurora:** Set `rds.logical_replication = 1` in the parameter group and reboot the instance.
- **Google Cloud SQL:** Set the `cloudsql.logical_decoding` database flag to `on` and restart the instance.
- **Google AlloyDB:** Set `alloydb.logical_decoding = on` and restart the instance.
- **Supabase:** Logical replication is enabled by default. Make sure you use a **direct connection** string (not pooled) from your Supabase dashboard under Project Settings > Database.
- **Self-hosted PostgreSQL:** Set `wal_level = logical` in `postgresql.conf` and restart PostgreSQL.

Verify logical replication is enabled:

```sql
SHOW wal_level;  -- should return 'logical'
```

### Fix replica identity (CDC migrations only)

For CDC migrations, tables without a primary key need `REPLICA IDENTITY FULL` set so that UPDATE and DELETE operations can be replicated. The [fix-replica-identity script](https://github.com/planetscale/migration-scripts/blob/main/pgcopydb-helpers/fix-replica-identity.sh) identifies affected tables and applies the change:

```shellscript
~/fix-replica-identity.sh
```

The script queries the source for tables with default replica identity that lack a primary key or unique index, previews the `ALTER TABLE` statements, and prompts for confirmation before applying.

This script requires table ownership on the source, not just `SELECT`. If you are using a dedicated migration user, grant it membership in the role(s) that own the affected tables:

```sql
GRANT <owner_role> TO migration_user;
```

After running the script, revoke the membership:

```sql
REVOKE <owner_role> FROM migration_user;
```

Setting `REPLICA IDENTITY FULL` increases WAL volume for UPDATE and DELETE operations on affected tables, as PostgreSQL must log full row contents instead of just key columns. Consider the impact on your source database’s disk usage and replication throughput.

## 3\. Prepare your PlanetScale database

Create a new database in the [PlanetScale dashboard](https://app.planetscale.com/new) or using the [PlanetScale CLI](../../cli.md).

When configuring the database:

- Select the correct **cloud region**. You typically want to use the same region as your source database and application infrastructure.
- Choose **Postgres** as the database type.
- Choose the best **storage option** for your needs. For high-performance, low-latency I/O, use [PlanetScale Metal](../../metal.md). For more flexible storage options, choose “Elastic Block Storage” (AWS) or “Persistent Disk” (GCP).

### Configure disk size

Set your disk size to at least **150%** of your source database size. For example, if your source is 330 GB, provision at least 500 GB. The overhead accounts for data growth during import and table/index bloat that occurs during the copy process.

- **Network-attached storage (EBS, Persistent Disk):** Configure the disk in advance by navigating to **Clusters > Storage** and setting the **Minimum disk size**. AWS and GCP limit how frequently disks can be resized, so the disk may not be able to resize fast enough during a large import.
- **Metal:** Choose the disk size when creating the cluster. You can resize after import and cleanup.

### Enable extensions

Review the extensions installed on your source database and compare them against the [PlanetScale supported extensions](../extensions.md) list. Any extensions your application depends on should be enabled on PlanetScale before running the migration. Some extensions require enabling through the PlanetScale dashboard and may need a cluster restart. See the [extensions documentation](../extensions.md#configuring-extensions-in-the-dashboard) for details.

### Use the Default role

Use the [**Default role**](../connecting/roles.md#default-role) on your PlanetScale database for the migration. This role has the necessary permissions to create schemas, tables, indexes, and manage replication. You can find the Default role credentials by navigating to your database in the [PlanetScale dashboard](https://app.planetscale.com/) and clicking **Connect**.

Copy the connection credentials into the `PGCOPYDB_TARGET_PGURI` in your `~/.env` file.

### Compare and configure parameters

Use the [compare-pg-params script](https://github.com/planetscale/migration-scripts/blob/main/pgcopydb-helpers/compare-pg-params.sh) to compare PostgreSQL parameters between your source and PlanetScale target to identify any differences that could affect application behavior:

```shellscript
~/compare-pg-params.sh
```

This script checks 50+ performance-relevant parameters across resource usage, query tuning, WAL configuration, connections, replication, autovacuum, statistics, and shared libraries. The output shows a side-by-side comparison with indicators for which parameters require a restart versus a reload to change.

Review the output and adjust parameters on your PlanetScale database as needed through the **Clusters > Parameters** page. Note that many parameters on a PlanetScale cluster are already tuned appropriately, so copying values directly from another provider may not be beneficial.

You should also increase `max_worker_processes` above its default value. This allows pgcopydb to run more parallel workers during the copy phase. You can decrease this value after the migration is complete.

### Run the preflight check

The [preflight check script](https://github.com/planetscale/migration-scripts/blob/main/pgcopydb-helpers/preflight-check.sh) validates all migration prerequisites before you start:

```shellscript
~/preflight-check.sh
```

This checks:

**Source database:**

- Connectivity
- `wal_level = logical`
- Replication permissions (`REPLICATION`, `SUPERUSER`, or `rds_replication`)
- Available replication slots
- Available WAL senders
- No leftover pgcopydb replication slots from previous attempts
- `wal_sender_timeout` configuration
- No pending prepared transactions

**Target database:**

- Connectivity
- Replication permissions
- No leftover pgcopydb schema from previous attempts
- Extension compatibility (all source extensions are available on the target)

**Migration instance:**

- `~/filters.ini` exists
- `pgcopydb` binary is on `PATH`

The output is color-coded: **PASS**, **WARN**, or **FAIL**. Resolve all FAILs before proceeding.

## 4\. Configure filtering

The `~/filters.ini` file controls which schemas, tables, extensions, and event triggers are excluded from the migration. Edit this file to match your source environment:

Do not place comments inside sections. pgcopydb treats `#` characters as literal object names. Place all comments before the first section header.

Any extensions not supported by PlanetScale should be added to the `[exclude-extension]` section. If unsupported extensions are not excluded, the migration may fail when trying to create those objects on the target. Check the [PlanetScale extensions documentation](../extensions.md) to verify which extensions are supported.

The following are starting-point filters for common source providers. Copy the one that matches your source and adjust as needed:

#### AWS RDS/Aurora

```ini
[exclude-extension]
rds_tools
aws_commons
aws_s3
pg_repack
```

#### Supabase

```ini
[exclude-schema]
auth
supabase_functions
extensions
storage
cron
realtime
supabase_migrations
net
_supabase
graphql
graphql_public

[exclude-extension]
pg_net
pg_graphql
pgsodium
supabase_vault
pg_repack
http
pg_stat_monitor

[exclude-event-trigger]
supabase_functions.hooks
```

#### Google AlloyDB

```ini
[exclude-schema]
ai
google_ml
helpobj
perfsnap
pgsnap

[exclude-extension]
g_stats
google_columnar_engine
google_db_advisor
google_ml_integration
```

## 5\. Run the migration

Start the migration using the [start-migration-screen script](https://github.com/planetscale/migration-scripts/blob/main/pgcopydb-helpers/start-migration-screen.sh) which runs in a detached [screen](https://www.gnu.org/software/screen/) session so it survives SSH disconnects:

```shellscript
~/start-migration-screen.sh
```

To attach and watch the migration progress:

```shellscript
screen -r migration
```

To detach without stopping the migration, press `Ctrl-A D`.

### What happens during migration

The [run-migration script](https://github.com/planetscale/migration-scripts/blob/main/pgcopydb-helpers/run-migration.sh) executes pgcopydb with the following settings:

- **16 parallel table COPY workers** (`TABLE_JOBS=16`)
- **12 parallel index creation workers** (`INDEX_JOBS=12`)
- Tables larger than **50 GB** are automatically split into parts for parallel copying
- The [`wal2json`](https://github.com/eulerto/wal2json) logical decoding plugin is used for CDC

The migration proceeds through the phases described in [How pgcopydb works](#how-pgcopydb-works). All output is logged to `~/migration_YYYYMMDD-HHMMSS/migration.log`.

### Tuning parallelism

You can tune the parallel job counts by editing the variables at the top of `~/run-migration.sh` before starting:

```shellscript
TABLE_JOBS=16   # Parallel COPY workers (keep below source and target vCPU count)
INDEX_JOBS=12   # Parallel index workers (keep below target vCPU count)
```

Higher values speed up the migration but put more load on both databases. As a guideline, keep `TABLE_JOBS` below the lesser of your source and target vCPU counts, and keep `INDEX_JOBS` below the target vCPU count.

## 6\. Monitor progress

### During the copy phase

Use the [check-migration-status script](https://github.com/planetscale/migration-scripts/blob/main/pgcopydb-helpers/check-migration-status.sh) to see a real-time dashboard of migration progress:

```shellscript
~/check-migration-status.sh
```

This displays:

- Phase status (1-10: pending, running, or done)
- Copy progress, including tables in progress, completed, and percentage
- Index and constraint creation progress
- Data transferred in GB
- Error counts with context
- Elapsed runtime
- Active database operations on the target (COPY, CREATE INDEX, VACUUM, ALTER TABLE with durations)

You can run this as frequently as you like to check status.

### During CDC replication

Once the initial copy completes and pgcopydb enters the CDC phase, use the [check-cdc-status script](https://github.com/planetscale/migration-scripts/blob/main/pgcopydb-helpers/check-cdc-status.sh) to monitor replication lag:

```shellscript
~/check-cdc-status.sh
```

This displays:

- Apply LSN and Streaming LSN
- Apply backlog gap in MB/GB
- Apply rate (GB/hr)
- Estimated time to catch up
- Source replication slot health
- Total end-to-end lag

When the apply backlog gap drops below 100 MB, the script shows **“CDC IS CAUGHT UP”** and you are ready to proceed with cutover.

## 7\. Prepare for cutover

While CDC is running and caught up, prepare your PlanetScale database for production traffic before performing the cutover.

**Create application roles.** pgcopydb does not migrate roles from the source database. The Default role used for migration has elevated privileges and should not be used by your application. Create dedicated roles with appropriate permissions for your application’s needs using PlanetScale’s built-in role management. See [Managing roles](../connecting/roles.md) for details.

**Confirm application connectivity.** Verify that your application can connect to the PlanetScale database using the new role credentials. Test read queries to confirm connectivity, but do not write to the target while CDC is still replicating.

**Configure observability.** Set up monitoring for your PlanetScale database so you have visibility from the moment you cut over. See [Prometheus integration](../monitoring/prometheus-postgres.md) and [Metrics](../monitoring/metrics.md) for available options.

**Prepare connection strings.** Have your new PlanetScale connection strings ready to deploy in your application configuration. If your application uses environment variables or a secrets manager, stage the new values so the switch can happen quickly.

**Configure connection pooling if needed.** If your application requires dedicated connection pooling, set up [PgBouncer](../connecting/pgbouncer.md) ahead of the cutover.

## 8\. Cutover

The cutover process switches your application from the source database to PlanetScale. This is the only point where downtime occurs.

After switching traffic to PlanetScale, new writes will not be replicated back to the source. Ensure you are fully ready before performing the cutover. We recommend keeping the source database available for a few days in case you need to verify any data.

## 9\. Clean up

After a successful cutover, clean up replication artifacts on the source database:

```shellscript
~/drop-replication-slots.sh
```

The [drop-replication-slots script](https://github.com/planetscale/migration-scripts/blob/main/pgcopydb-helpers/drop-replication-slots.sh) removes:

- The logical replication slot on the source (terminates the active consumer if needed)
- The replication origin on the target
- The pgcopydb sentinel schema on the target

Always run this cleanup after migration. Unconsumed replication slots cause WAL to accumulate on the source database until the disk fills, which can take down your source database.

You should also:

- **Remove the migration user on the source database.** If you created a dedicated user for pgcopydb to connect to your source, drop it now that the migration is complete:
	```sql
	DROP USER IF EXISTS migration_user;
	```
- Decrease `max_worker_processes` on the PlanetScale database back to its default value.
- Run `VACUUM ANALYZE` on the target to reclaim any bloat from the import. You can also use [pg\_squeeze](../extensions/pg_squeeze.md) for online table compaction.

## Resuming after interruption

If the migration is interrupted by a crash, reboot, or connection failure, use the [resume-migration script](https://github.com/planetscale/migration-scripts/blob/main/pgcopydb-helpers/resume-migration.sh) to continue from where it left off:

```shellscript
# Resume the most recent migration
~/resume-migration.sh

# Or specify an explicit migration directory
~/resume-migration.sh ~/migration_YYYYMMDD-HHMMSS
```

The script backs up pgcopydb’s internal tracking database before resuming and uses `--not-consistent` to allow resuming from a mid-transaction state. It passes `--split-tables-larger-than` to match `run-migration.sh`, since pgcopydb requires catalog consistency between the original run and the resume.

Do not use `pgcopydb --restart`. It wipes the CDC directory and internal tracking databases without cleaning the target database or correcting previous failures. To start over, use `~/target-clean.sh` + `~/drop-replication-slots.sh` + `~/start-migration-screen.sh` instead.

### Starting over

If you need to restart the migration from scratch, first clean the target database and replication state:

```shellscript
# Wipe all migrated objects from the target
~/target-clean.sh

# Remove replication slots from the source
~/drop-replication-slots.sh

# Start a fresh migration
~/start-migration-screen.sh
```

The [target-clean script](https://github.com/planetscale/migration-scripts/blob/main/pgcopydb-helpers/target-clean.sh) drops all non-default schemas, materialized views, publications, subscriptions, event triggers, standalone custom types, and partitioned tables. It preserves `pg_catalog`, `information_schema`, and `pscale_extensions`. The script previews what will be dropped and prompts for confirmation before proceeding.

## Troubleshooting

### Checking logs

All migration output is captured in `~/migration_YYYYMMDD-HHMMSS/migration.log`. Common diagnostic commands:

```shellscript
# Find errors
grep ERROR ~/migration_*/migration.log

# Check pg_restore errors
grep "errors ignored on restore" ~/migration_*/migration.log

# Verify extension filtering
grep s_depend ~/migration_*/migration.log

# Check the exit code (last line of log, success = "Exit code: 0")
tail -1 ~/migration_*/migration.log
```

### Common issues

**Preflight check shows `wal_level` is not `logical`:** Changing `wal_level` requires a database restart. On managed services like RDS, update the parameter group and reboot the instance. On self-managed PostgreSQL, update `postgresql.conf` and restart.

**Extension filtering not working:** If extension-owned objects are still being copied despite `[exclude-extension]` entries in `filters.ini`, verify that the extension names match exactly and that the extensions are actually installed on the source database.

**Migration fails with restore errors:** pgcopydb tolerates up to 10 `pg_restore` errors by default. Check the migration log for specific errors. Common causes include unsupported extensions or objects that depend on excluded schemas. Adjust your `filters.ini` and restart.

**Replication slot already exists:** If a previous migration attempt left a replication slot, run `~/drop-replication-slots.sh` to clean it up before starting a new migration.

**Tables missing data after resume:** If the resume did not recover correctly, run `~/target-clean.sh` and `~/drop-replication-slots.sh`, then start the migration over with `~/start-migration-screen.sh`.

## Next steps

After your migration is complete:

- Review [Query Insights](../monitoring/query-insights.md) to monitor query performance on PlanetScale.
- Set up [Metrics](../monitoring/metrics.md) and alerting for your new database.
- Review [Schema recommendations](../monitoring/schema-recommendations.md) for optimization suggestions.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
