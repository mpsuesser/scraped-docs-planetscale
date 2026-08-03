---
url: https://planetscale.com/docs/postgres/imports/heroku
title: "Heroku"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

The [PlanetScale Heroku Migrator](https://github.com/planetscale/heroku-migrator) helps you migrate your Heroku Postgres database to PlanetScale with minimal downtime. It runs as a temporary Heroku app that copies your data and keeps both databases in sync until you’re ready to cut over. The entire process is managed through a web dashboard.

Under the hood, the migrator uses [Bucardo](https://bucardo.org/Bucardo/), an open-source trigger-based replication tool. Heroku does not support logical replication, so trigger-based replication is the most reliable migration strategy with the least downtime.

Before beginning your migration, we recommend running our [migration assessment tool](https://planetscale.com/liftoff) for instant feedback on migration complexity, potential blockers, and the recommended migration path. We also offer complimentary hands-on migration assistance for Heroku migrations on a case-by-case basis. [Reach out](https://planetscale.com/contact) to learn more.

**Databases over ~1 TB:** Heroku force-restarts dynos every 24 hours, which resets the initial data copy. If your database is large enough that the copy could take longer than 24 hours, do not deploy this tool on Heroku. Run the Docker container on your own infrastructure instead. See [Heroku’s 24-hour restart limit](#7-heroku-24-hour-restart-limit) for details.

## Before you start

### 1\. Get your Heroku database credentials

You’ll need your Heroku Postgres connection URL. Run this command to get it:

```shellscript
heroku config:get DATABASE_URL -a your-app-name
```

It will look something like:

```text
postgres://username:password@host:5432/dbname
```

Copy this value. You’ll paste it as the `HEROKU_URL` when deploying the migrator.

### 2\. Create your PlanetScale database and get credentials

Follow the [PlanetScale Postgres quickstart](../tutorials/planetscale-postgres-quickstart.md) to create a database and generate a password. When creating the password, make sure you select the **Postgres** permission. Copy the Postgres connection string. This is your `PLANETSCALE_URL`.

### 3\. Check your Heroku Postgres extensions

The migrator replicates your data, but it doesn’t install Postgres extensions. You need to make sure any extensions you use on Heroku are also enabled on PlanetScale **before** starting the migration.

Run this command to see which extensions your Heroku database uses:

```shellscript
heroku pg:psql -a your-app-name -c "SELECT extname, extversion FROM pg_extension WHERE extname != 'plpgsql' ORDER BY extname;"
```

For each extension listed, enable it on your PlanetScale database before starting the migration. See the [PlanetScale Postgres extensions documentation](../extensions.md) for supported extensions and how to enable them.

### 4\. Check for blocking vacuum processes

The migrator creates triggers on your Heroku tables to track changes. In rare cases, a long-running autovacuum process can block trigger creation, which can also block your application’s queries. Before starting the migration, check for wraparound vacuum processes:

```shellscript
heroku pg:locks -a your-app-name
```

If you see any `VACUUM` queries with `(to prevent wraparound)` in the output, wait for them to finish before starting the migration.

### 5\. Size your PlanetScale database

**Cluster size:** Choose a PlanetScale cluster with similar CPU and RAM to your Heroku Postgres plan. You don’t need to get this exactly right. [Resizing in PlanetScale is an online operation](../cluster-configuration.md) with no downtime, and you are only billed for the time you use.

**Storage:** Make sure your PlanetScale database has at least **twice the storage** that Heroku reports using. Bucardo is not very space-efficient during migration, and Postgres disk usage can vary significantly between providers. It’s not uncommon for a database to use 50% more or less space on PlanetScale than on Heroku. Automatic vacuuming will reclaim the extra space over time after the migration completes.

To check your current Heroku storage usage:

```shellscript
heroku pg:info -a your-app-name
```

Look for the “Data Size” field. If your Heroku database uses 10 GB, provision at least 20 GB on PlanetScale. You can adjust storage at any time via the [Storage tab in Cluster Configuration](../cluster-configuration/cluster-storage.md).

### 6\. Consider your database size and performance

The migrator uses trigger-based replication. When the migration starts, it installs triggers on your Heroku database tables to track every insert, update, and delete so changes can be replicated to PlanetScale.

- **Small to medium databases** (under 10 million rows): You likely won’t notice any performance impact.
- **Large databases** (tens of millions to billions of rows): The initial data copy puts additional read load on your Heroku database. The triggers add a small amount of write overhead. If your database is already under heavy load, consider:
	- **Upgrade your Heroku Postgres plan temporarily.** A larger plan gives your database more headroom during the migration. You can downgrade after.
		- **Run the migration during off-peak hours.** Start the initial copy when your app has less traffic.
		- **Use the Pause button.** The migration dashboard has a Pause Sync button that stops replication without losing any data. If you notice performance issues, pause the sync, wait for things to settle, then resume. All changes are tracked while paused and will catch up when you resume.

### 7\. Heroku 24-hour restart limit

Heroku restarts every dyno at least once every 24 hours. If a restart happens during the initial data copy, the copy starts over from the beginning.

Copy speed varies by database, but most users can expect around **100 GB per hour**. Actual throughput depends on the number and size of indexes, average row width, network conditions between Heroku and PlanetScale, and the configuration of the target database. A 500 GB database might finish in 5 hours or might take 8+, depending on these factors.

If your Heroku database is large enough that the initial copy could take close to or longer than 24 hours, **do not deploy this tool on Heroku**. Instead, run the container somewhere that won’t force-restart it, such as an AWS EC2 instance, ECS task, or GCP VM.

The container is standard Docker, so deploying elsewhere is just `docker run` with the same environment variables:

```shellscript
docker build -t heroku-migrator .
docker run -d \
  -e HEROKU_URL="postgres://..." \
  -e PLANETSCALE_URL="postgresql://..." \
  -e PASSWORD="your-password" \
  -p 8080:8080 \
  heroku-migrator
```

For databases under ~1 TB, Heroku is typically fine. For anything larger, use a host without forced restarts to avoid re-copying data.

## Deploy the migrator

Deploy the migrator as a temporary Heroku app. You can use the Deploy to Heroku button in the [heroku-migrator repository](https://github.com/planetscale/heroku-migrator), or deploy manually:

### Which dyno size should I use?

The migrator runs PostgreSQL and Bucardo inside the dyno, so it needs more memory than a typical web app.

- **Standard-1x (512 MB)**: Fine for small databases (under 1 million rows, fewer than 20 tables).
- **Standard-2x (1 GB)**: Recommended for most migrations. Handles databases with tens of millions of rows.
- **Performance-M (2.5 GB)**: For very large databases with wide rows, many tables, or if you see R14 memory errors on Standard-2x.

This is a temporary app. You’ll delete it after the migration is complete, so the cost is minimal. When in doubt, start with Standard-2x.

## How the migration works

Once you open the dashboard and click **Start Migration**, the process follows these steps:

### Step 1: Setup

The migrator copies your database structure (tables, indexes, constraints) from Heroku to PlanetScale and configures replication. This is fully automatic and typically takes a minute or two.

### Step 2: Data sync

All existing rows are copied from Heroku to PlanetScale (the “initial copy”). Once that finishes, the migrator enters real-time replication mode, where every new write to your Heroku database is automatically replicated to PlanetScale.

Your Heroku app continues running normally throughout this entire process.

For large databases, the initial copy can take hours. The dashboard shows progress and you can safely close the browser and come back later. If you need to reduce load on your Heroku database, use the **Pause Sync** button. Changes are still tracked while paused and will catch up when you resume.

### Step 3: Switch traffic

When the dashboard shows your databases are in sync, you’re ready to cut over. Click **Switch Traffic** to block writes on your Heroku database. This runs a SQL `REVOKE` command that removes `INSERT`, `UPDATE`, and `DELETE` privileges from your Heroku database user:

```sql
REVOKE INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public FROM your_heroku_user;
```

After this, your app can still **read** from Heroku, but any **write** query will fail with a permission error. This ensures no new data is written to Heroku while you switch over.

Then update your app to use PlanetScale:

```shellscript
heroku config:set DATABASE_URL="your-planetscale-connection-string" -a your-app-name
```

Your app restarts and begins using PlanetScale. Test it to make sure everything works.

If something goes wrong, click **Revert Switch** in the dashboard. This runs the inverse `GRANT` command to restore write access to your Heroku database, so your app can write to Heroku again immediately.

During **Switch Traffic** or **Revert Switch**, you may see PostgreSQL warnings about `pg_stat_statements` privileges in the dashboard output. These warnings are expected on some Heroku Postgres setups and do not indicate a failure.

### Step 4: Complete

Once you’ve verified everything is working on PlanetScale, click **Complete Migration** in the dashboard. This removes the replication triggers from your Heroku database.

## Cleanup

After completing the migration:

1. Delete the migration app: `heroku apps:destroy my-migration`
2. Remove the Heroku Postgres add-on from your main app when you’re confident everything is working.

To verify that all replication objects were removed from Heroku, you can run:

```sql
SELECT count(*) FROM pg_trigger WHERE tgname LIKE 'bucardo_%';
SELECT count(*) FROM pg_namespace WHERE nspname = 'bucardo';
```

Both queries should return `0`.

## Environment variables

| Variable | Required | Description |
| --- | --- | --- |
| `HEROKU_URL` | Yes | Heroku Postgres connection URL |
| `PLANETSCALE_URL` | Yes | PlanetScale Postgres connection URL |
| `PASSWORD` | Yes | Password to access the migration dashboard |
| `DISABLE_NOTIFICATIONS` | No | Set to `true` to disable migration progress notifications to PlanetScale (enabled by default) |

## Running another migration

If you want to run another migration test after finishing one:

1. Create a fresh PlanetScale target (database/branch and credentials) and use that as the new `PLANETSCALE_URL`.
2. Keep or reset `HEROKU_URL` depending on your source test dataset.
3. Update the migrator app config vars:

```shellscript
heroku config:set \
  HEROKU_URL="postgres://..." \
  PLANETSCALE_URL="postgresql://..." \
  PASSWORD="your-password" \
  -a my-migration
```

4. Open the dashboard and start a new run.

Using a fresh PlanetScale target for each rerun keeps validation clean and avoids mixing data from previous migration attempts.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
