---
url: https://planetscale.com/docs/postgres/imports/heroku
title: "Heroku"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Migrate from Heroku to PlanetScale

> Migrate from Heroku Postgres to PlanetScale with minimal downtime using the Heroku Migrator tool.

This page covers Postgres.

The [PlanetScale Heroku Migrator](https://github.com/planetscale/heroku-migrator) helps you migrate your Heroku Postgres database to PlanetScale with minimal downtime. It runs as a temporary Heroku app that copies your data and keeps both databases in sync until you're ready to cut over. The entire process is managed through a web dashboard.

Under the hood, the migrator uses [Bucardo](https://bucardo.org/Bucardo/), an open-source trigger-based replication tool. Heroku does not support logical replication, so trigger-based replication is the most reliable migration strategy with the least downtime.

Before beginning your migration, we recommend running our [migration assessment tool](https://planetscale.com/liftoff) for instant feedback on migration complexity, potential blockers, and the recommended migration path. We also offer complimentary hands-on migration assistance for Heroku migrations on a case-by-case basis. [Reach out](https://planetscale.com/contact) to learn more.

[Automate your Heroku database migration, fast!](https://www.youtube-nocookie.com/embed/92VnUY9_Hpk?rel=0)

> [!WARNING]
>
> **Databases over \~1 TB:** Heroku force-restarts dynos every 24 hours, which resets the initial data copy. If your database is large enough that the copy could take longer than 24 hours, do not deploy this tool on Heroku. On AWS, use the [CloudFormation template](https://github.com/planetscale/heroku-migrator/tree/main/templates/aws-cloudformation). See [Heroku's 24-hour restart limit](#7-heroku-24-hour-restart-limit) for details.

## Before you start

> [!WARNING]
>
> **Do not make schema changes during the migration.** Bucardo discovers your tables, columns, and constraints when replication is configured and installs triggers based on that snapshot. Running any DDL on Heroku after the migration starts will break replication. If you need to ship a schema change, finish or abort the migration first.

### 1. Get your Heroku database credentials

You'll need your Heroku Postgres connection URL. Run this command to get it:

```sh theme={null}
heroku config:get DATABASE_URL -a your-app-name
```

It will look something like:

```
postgres://username:password@host:5432/dbname
```

Copy this value. You'll paste it as the `HEROKU_URL` when deploying the migrator.

### 2. Create your PlanetScale database and get credentials

Follow the [PlanetScale Postgres quickstart](https://planetscale.com/docs/postgres/tutorials/planetscale-postgres-quickstart.md) to create a database and generate a password. When creating the password, make sure you select the **Postgres** permission. Copy the Postgres connection string. This is your `PLANETSCALE_URL`.

### 3. Check your Heroku Postgres extensions

The migrator replicates your data, but it doesn't install Postgres extensions. You need to make sure any extensions you use on Heroku are also enabled on PlanetScale **before** starting the migration.

Run this command to see which extensions your Heroku database uses:

```sh theme={null}
heroku pg:psql -a your-app-name -c "SELECT extname, extversion FROM pg_extension WHERE extname != 'plpgsql' ORDER BY extname;"
```

For each extension listed, enable it on your PlanetScale database before starting the migration. See the [PlanetScale Postgres extensions documentation](https://planetscale.com/docs/postgres/extensions.md) for supported extensions and how to enable them.

If `pg_partman` is in that list, read [pg\_partman support](#pg_partman-support) before you start. It needs one manual step.

### 4. Check for blocking vacuum processes

The migrator creates triggers on your Heroku tables to track changes. In rare cases, a long-running autovacuum process can block trigger creation, which can also block your application's queries. Before starting the migration, check for wraparound vacuum processes:

```sh theme={null}
heroku pg:locks -a your-app-name
```

If you see any `VACUUM` queries with `(to prevent wraparound)` in the output, wait for them to finish before starting the migration.

### 5. Size your PlanetScale database

**Cluster size:** Choose a PlanetScale cluster with similar CPU and RAM to your Heroku Postgres plan. You don't need to get this exactly right. [Resizing in PlanetScale is an online operation](https://planetscale.com/docs/postgres/cluster-configuration.md) with no downtime, and you are only billed for the time you use.

**Storage:** Make sure your PlanetScale database has at least **twice the storage** that Heroku reports using. Bucardo is not very space-efficient during migration, and Postgres disk usage can vary significantly between providers. It's not uncommon for a database to use 50% more or less space on PlanetScale than on Heroku. Automatic vacuuming will reclaim the extra space over time after the migration completes.

To check your current Heroku storage usage:

```sh theme={null}
heroku pg:info -a your-app-name
```

Look for the "Data Size" field. If your Heroku database uses 10 GB, provision at least 20 GB on PlanetScale. You can adjust storage at any time via the [Storage tab in Cluster Configuration](https://planetscale.com/docs/postgres/cluster-configuration/cluster-storage.md).

### 6. Consider your database size and performance

The migrator uses trigger-based replication. When the migration starts, it installs triggers on your Heroku database tables to track every insert, update, and delete so changes can be replicated to PlanetScale.

* **Small to medium databases** (under 10 million rows): You likely won't notice any performance impact.
* **Large databases** (tens of millions to billions of rows): The initial data copy puts additional read load on your Heroku database. The triggers add a small amount of write overhead. If your database is already under heavy load, consider:
  * **Upgrade your Heroku Postgres plan temporarily.** A larger plan gives your database more headroom during the migration. You can downgrade after.
  * **Run the migration during off-peak hours.** Start the initial copy when your app has less traffic.
  * **Use the Pause button, but be aware of the phase.** Once the dashboard shows "Your databases are in sync", **Pause Sync** stops data transfer to PlanetScale while changes continue to queue. Resuming catches up cleanly. Do not pause while the dashboard still says "Copying data to PlanetScale". Bucardo cannot resume a partial `COPY`, so resuming restarts the initial copy from scratch.

### 7. Heroku 24-hour restart limit

Heroku restarts every dyno at least once every 24 hours. If a restart happens during the initial data copy, the copy starts over from the beginning.

Copy speed varies by database, but most users can expect around **200 GB per hour**. Actual throughput depends on the number and size of indexes, average row width, network conditions between Heroku and PlanetScale, and the configuration of the target database.

> [!WARNING]
>
> If your Heroku database is large enough that the initial copy could take close to or longer than 24 hours, **do not deploy this tool on Heroku**. Run it on infrastructure that will not force-restart the container.

**On AWS, use the CloudFormation template.** That is the default way to run the migrator outside Heroku. The [aws-cloudformation templates](https://github.com/planetscale/heroku-migrator/tree/main/templates/aws-cloudformation) provision an EC2 instance, build the container, and run it under systemd with no 24-hour restart. The dashboard binds to localhost only, so you reach it through an SSH or SSM tunnel.

On other hosts, build and run the container yourself with the same environment variables you would set on Heroku, including index-rebuild tuning:

```sh theme={null}
docker build -t heroku-migrator .
docker run -d \
  -e HEROKU_URL="postgres://..." \
  -e PLANETSCALE_URL="postgresql://..." \
  -e PASSWORD="your-password" \
  -e INDEX_REBUILD_WORKERS=4 \
  -e MAINTENANCE_WORK_MEM=1GB \
  -e PARALLEL_MAINTENANCE_WORKERS=2 \
  -p 8080:8080 \
  heroku-migrator
```

For databases under \~1 TB, Heroku is typically fine. For anything larger, use a host without forced restarts to avoid re-copying data.

## Deploy the migrator

Deploy the migrator as a temporary Heroku app. You can use the Deploy to Heroku button in the [heroku-migrator repository](https://github.com/planetscale/heroku-migrator), or deploy manually:

**1. Clone the repository**

```sh theme={null}
git clone https://github.com/planetscale/heroku-migrator.git
cd heroku-migrator
```

**2. Create a Heroku app**

```sh theme={null}
heroku create my-migration --stack container
```

**3. Set the required config vars**

```sh theme={null}
heroku config:set \
  HEROKU_URL="postgres://..." \
  PLANETSCALE_URL="postgresql://..." \
  PASSWORD="choose-a-password"
```

Optionally, tune the post-copy index rebuild (defaults shown; see [Faster initial copy](#faster-initial-copy-deferred-index-rebuild)). You can also change the parallel-jobs count from the dashboard before the copy starts:

```sh theme={null}
heroku config:set \
  INDEX_REBUILD_WORKERS=4 \
  MAINTENANCE_WORK_MEM=1GB \
  PARALLEL_MAINTENANCE_WORKERS=2
```

**4. Deploy**

```sh theme={null}
git push heroku main
```

**5. Resize the dyno (recommended)**

```sh theme={null}
heroku ps:resize web=standard-2x -a my-migration
```

**6. Open the dashboard**

```sh theme={null}
heroku open
```

You'll be prompted for a password. Enter the `PASSWORD` you set above. The username is `admin`.

### Which dyno size should I use?

The migrator runs PostgreSQL and Bucardo inside the dyno, so it needs more memory than a typical web app. Memory usage scales with both **data size** and **write volume**.

* **Standard-1x (512 MB)**: Small databases with low write volume (under 1 million rows, fewer than 20 tables).
* **Standard-2x (1 GB)**: Databases under 50 GB with moderate write volume.
* **Performance-M (2.5 GB)**: Databases between 50-100 GB, high write throughput, or many tables. Also use this if you see R14 memory errors on Standard-2x.
* **Performance-L (14 GB)**: Databases over 100 GB, or any database with very high write volume. The initial copy and ongoing replication for large databases can consume several GB of RAM. Smaller dynos will OOM and crash.

## How the migration works

Once you open the dashboard and click **Start Migration**, the process follows these steps:

### Step 1: Setup

The migrator copies your database structure (tables, indexes, constraints) from Heroku to PlanetScale and configures replication. This is fully automatic and typically takes a minute or two. It then stops and waits for you: no data is copied until you start the next step.

Bookkeeping schemas (`bucardo`, `_ps_migrator`, `pscale_extensions`), extension home schemas, extension-owned tables, and tables the migrator cannot put a trigger on are excluded from the dump and the sync.

### Step 2: Data sync

Click **Start Data Copy** to begin. All existing rows are copied from Heroku to PlanetScale (the "initial copy"). By default, secondary and unique indexes are dropped on PlanetScale before that copy and rebuilt afterward. See [Faster initial copy](#faster-initial-copy-deferred-index-rebuild). Once the copy and index rebuild finish, the migrator enters real-time replication mode, where every new write to your Heroku database is automatically replicated to PlanetScale.

Your Heroku app continues running normally throughout this entire process.

For large databases, the initial copy can take hours. The dashboard shows progress and you can safely close the browser and come back later. The **Pause Sync** button behaves differently depending on the phase:

* **While the initial copy is running** (dashboard title "Copying data to PlanetScale"): Bucardo cannot resume a partial `COPY`. Pausing and then resuming will restart the initial copy from scratch.
* **Once you reach delta replication** (dashboard title "Your databases are in sync"): pausing is safe. Changes are queued while paused and apply on Resume.

In either case, Bucardo's triggers stay on your Heroku database while paused, so writes still carry trigger overhead. Use **Abort Migration** if you need to remove the triggers entirely.

### Step 3: Switch traffic

When the dashboard shows your databases are in sync, you're ready to cut over. Click **Switch Traffic** to block writes on your Heroku database. This runs a SQL `REVOKE` command that removes `INSERT`, `UPDATE`, and `DELETE` privileges from your Heroku database user, once for each schema being migrated:

```sql theme={null}
REVOKE INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA your_schema FROM your_heroku_user;
```

> [!NOTE]
>
> For production apps, consider enabling Heroku maintenance mode before clicking **Switch Traffic**. The migrator blocks writes on the source database, but it does not update your application's config vars, restart dynos, or verify the app against PlanetScale. Maintenance mode keeps user traffic out of the handoff window while you update and test the app.

After this, your app can still **read** from Heroku, but any **write** query will fail with a permission error. This ensures no new data is written to Heroku while you switch over.

**Verify the migration.** Now that writes are blocked, the source is frozen and the two databases can fully match. Click **Verify Migration** in the dashboard to run an automated source-vs-target comparison. It usually finishes in under a minute and checks:

* **Tables, columns, indexes, constraints, and sequences** all present and matching on PlanetScale.
* **Extensions** present and the same version.
* **Row counts** on a random sample of up to 10 tables (skipping any over 10 GB, with a 60s-per-table cap).
* **Pending replication** — every queued change must have been applied to PlanetScale. If you run this right after Switch Traffic, it often still reports a backlog, which fails the check. Wait a minute and re-run.

The check does not modify either database, so you can re-run it as many times as you like.

Then update your app to use PlanetScale:

```sh theme={null}
heroku config:set DATABASE_URL="your-planetscale-connection-string" -a your-app-name
```

> [!WARNING]
>
> If your app has a Heroku Postgres add-on attached, `DATABASE_URL` may be owned by the add-on attachment rather than being a normal config var. In that case, `heroku config:set DATABASE_URL=...` can fail with `Cannot overwrite attachment values`. Rehearse this before you click **Switch Traffic**.

Your app restarts and begins using PlanetScale. Test it to make sure everything works.

If something goes wrong, click **Revert Switch** in the dashboard. This runs the inverse `GRANT` command to restore write access to your Heroku database, so your app can write to Heroku again immediately.

> [!NOTE]
>
> During **Switch Traffic** or **Revert Switch**, you may see PostgreSQL warnings about `pg_stat_statements` privileges in the dashboard output. These warnings are expected on some Heroku Postgres setups and do not indicate a failure.

### Step 4: Complete

Once you've verified everything is working on PlanetScale, click **Complete Migration** in the dashboard. This removes the replication triggers from your Heroku database.

## Faster initial copy (deferred index rebuild)

You have two options for how indexes are handled during the initial copy.

### Option A: Deferred index rebuild (default)

This is the default behavior (`DISABLE_INDEX_DEFERRAL` is `false`). To speed up the initial copy, the migrator drops the target's **secondary and unique indexes before copying** and rebuilds them **after** the copy completes.

How it works:

1. After the schema is copied, the migrator records the exact definition of every secondary and unique index (and any foreign keys that depend on a dropped unique) in a `_ps_migrator` schema on the target, then drops them. **Primary keys, exclusion constraints, and `REPLICA IDENTITY USING INDEX` indexes are kept.**
2. The initial copy runs against the index-light tables.
3. When the copy finishes the indexes are rebuilt in parallel (`INDEX_REBUILD_WORKERS` at a time). Progress and any failures are shown on the dashboard.
4. Once each index is rebuilt, replication resumes automatically. If any index fails, the run **pauses for review** so you can fix the issue and **Retry Failed Indexes**, or **Proceed Anyway** (replication resumes without the failed indexes).

The parallelism, per-job memory, and parallel-maintenance-workers of this rebuild are tunable with `INDEX_REBUILD_WORKERS`, `MAINTENANCE_WORK_MEM`, and `PARALLEL_MAINTENANCE_WORKERS`. Peak memory on the target is roughly `INDEX_REBUILD_WORKERS × MAINTENANCE_WORK_MEM`. You can also change `INDEX_REBUILD_WORKERS` from the dashboard before starting the copy.

### Option B: Keep all indexes in place

If you'd rather not drop and rebuild anything, set `DISABLE_INDEX_DEFERRAL=true`. All indexes stay in place for the entire initial copy. The trade-off is a **slower initial copy**, because every index is maintained row-by-row as data is copied.

When `DISABLE_INDEX_DEFERRAL=true`, there is no post-copy rebuild, so `INDEX_REBUILD_WORKERS`, `MAINTENANCE_WORK_MEM`, and `PARALLEL_MAINTENANCE_WORKERS` have no effect and the dashboard hides the index-rebuild tuning control.

## pg\_partman support

pg\_partman is supported, with one manual step: pause pg\_partman maintenance before you start, and resume it on PlanetScale after cutover. Everything else is automated. The migrator replicates your **leaf partitions** and recreates the partitioned parent through the schema copy. The `partman` schema is not replicated, because its config and template tables can't be.

> [!WARNING]
>
> Pause the job that calls `partman.run_maintenance_proc()` (usually a pg\_cron entry) and leave it paused until after cutover. Bucardo's relation list is fixed when the sync is created. A partition that maintenance creates after that point is **never replicated, and nothing reports an error**. With pg\_partman's default `automatic_maintenance = 'on'`, this can happen unattended.

Right after the data copy, the migrator reads your partition configuration from the source and recreates it on PlanetScale, so maintenance keeps working after cutover. Two cases still need your attention, both reported in the Setup Log:

* **Sub-partitioned sets.** pg\_partman's own dump function covers single-level sets only, so if you use sub-partitioning (`partman.part_config_sub`) re-register those by hand after cutover.
* **A partition set that failed to register on PlanetScale.** This shows as a warning and does not stop the migration; all your data is still copied. Only pg\_partman's settings for that table are missing on PlanetScale, so no new partitions will be created for it until you register it again with `partman.create_parent()` after cutover, using the same settings as on Heroku.

Then resume maintenance on PlanetScale.

## Cleanup

After completing the migration:

1. Delete the migration app: `heroku apps:destroy my-migration`
2. Remove the Heroku Postgres add-on from your main app when you're confident everything is working.

To verify that all replication objects were removed from Heroku, you can run:

```sql theme={null}
SELECT count(*) FROM pg_trigger WHERE tgname LIKE 'bucardo_%';
SELECT count(*) FROM pg_namespace WHERE nspname = 'bucardo';
```

Both queries should return `0`.

## Running another migration

If you want to run another migration test after finishing one:

1. Create a fresh PlanetScale target (database/branch and credentials) and use that as the new `PLANETSCALE_URL`.
2. Keep or reset `HEROKU_URL` depending on your source test dataset.
3. Update the migrator app config vars:

```sh theme={null}
heroku config:set \
  HEROKU_URL="postgres://..." \
  PLANETSCALE_URL="postgresql://..." \
  PASSWORD="your-password" \
  -a my-migration
```

4. Open the dashboard and start a new run.

Using a fresh PlanetScale target for each rerun keeps validation clean and avoids mixing data from previous migration attempts.

## Environment variables

| Variable | Required | Description |
| :- | :- | :- |
| `HEROKU_URL` | Yes | Heroku Postgres connection URL |
| `PLANETSCALE_URL` | Yes | PlanetScale Postgres connection URL |
| `PASSWORD` | Yes | Password to access the migration dashboard |
| `DISABLE_NOTIFICATIONS` | No | Set to `true` to disable migration progress notifications to PlanetScale (enabled by default) |
| `INDEX_REBUILD_WORKERS` | No | How many indexes to rebuild in parallel after the initial copy (default `4`). See [Faster initial copy](#faster-initial-copy-deferred-index-rebuild). |
| `MAINTENANCE_WORK_MEM` | No | `maintenance_work_mem` applied to each index build, e.g. `1GB` (default `1GB`). Allocated per concurrent build. |
| `PARALLEL_MAINTENANCE_WORKERS` | No | `max_parallel_maintenance_workers` per index build (default `2`). |
| `DISABLE_INDEX_DEFERRAL` | No | Set to `true` to keep all indexes in place during the initial copy (disables the speedup; default `false`). When `true`, the three rebuild variables have no effect. |

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
