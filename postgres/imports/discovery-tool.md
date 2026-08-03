---
url: https://planetscale.com/docs/postgres/imports/discovery-tool
title: "Discovery Tool"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

The PlanetScale Discovery Tool analyzes your existing PostgreSQL database and cloud infrastructure to help plan your migration to PlanetScale Postgres. It collects metadata about your database configuration, schema structure, performance characteristics, security settings, and cloud resources. It never reads or stores actual table data.

The Discovery CLI also supports MySQL and MySQL-compatible database discovery. See the [MySQL setup guide](https://github.com/planetscale/ps-discovery/blob/main/docs/mysql.md) for MySQL, Vitess, and PlanetScale-specific details.

The tool produces a structured JSON report that PlanetScale uses to provide migration guidance tailored to your environment. You can upload this report to our [migration assessment tool](https://planetscale.com/liftoff) for instant feedback on your migration readiness.

The discovery tool is open source and available on [GitHub](https://github.com/planetscale/ps-discovery). Essential setup steps are below. Advanced usage, troubleshooting, and detailed reference are available in the [full documentation](https://github.com/planetscale/ps-discovery/tree/main/docs).

## What it discovers

**Database analysis:**

- PostgreSQL version, configuration, and installed extensions
- Schema structure, including schemas, tables, columns, indexes, constraints, and sizes
- Performance statistics such as cache hit ratios, table/index usage, and active locks
- Security configuration: roles, permissions, and SSL settings
- Feature usage: foreign data wrappers, partitioning, PostGIS, and more

**Cloud infrastructure analysis:**

- Database instances, clusters, and their configurations
- Supabase, Heroku Postgres, and Neon project metadata
- VPC networking, subnets, security groups, and firewall rules
- Performance metrics from cloud monitoring services
- High availability and replica configurations

## Installation

The discovery tool requires Python 3.10 or later.

For advanced installation options, see the [Discovery Tool README](https://github.com/planetscale/ps-discovery/blob/main/README.md).

## Database user setup

Create a dedicated read-only user for the discovery tool. Connect to your PostgreSQL database as a superuser or privileged role and run the following:

```sql
-- Create a dedicated user for database discovery
CREATE USER planetscale_discovery WITH PASSWORD 'secure_password_here';

-- Grant basic connection and usage permissions
GRANT CONNECT ON DATABASE your_database TO planetscale_discovery;
GRANT USAGE ON SCHEMA public TO planetscale_discovery;
GRANT USAGE ON SCHEMA information_schema TO planetscale_discovery;

-- Grant read access to all tables and views
GRANT SELECT ON ALL TABLES IN SCHEMA public TO planetscale_discovery;
GRANT SELECT ON ALL TABLES IN SCHEMA information_schema TO planetscale_discovery;
GRANT SELECT ON ALL TABLES IN SCHEMA pg_catalog TO planetscale_discovery;

-- Grant permissions for system catalogs and statistics
GRANT SELECT ON pg_stat_database TO planetscale_discovery;
GRANT SELECT ON pg_stat_user_tables TO planetscale_discovery;
GRANT SELECT ON pg_stat_user_indexes TO planetscale_discovery;
GRANT SELECT ON pg_stat_activity TO planetscale_discovery;
GRANT SELECT ON pg_stat_replication TO planetscale_discovery;
GRANT SELECT ON pg_settings TO planetscale_discovery;
GRANT SELECT ON pg_database TO planetscale_discovery;
GRANT SELECT ON pg_user TO planetscale_discovery;
GRANT SELECT ON pg_roles TO planetscale_discovery;
GRANT SELECT ON pg_user_mappings TO planetscale_discovery;

-- For foreign data wrapper analysis
GRANT SELECT ON pg_foreign_server TO planetscale_discovery;
GRANT SELECT ON pg_foreign_data_wrapper TO planetscale_discovery;

-- For advanced performance analysis (if pg_stat_statements is enabled)
GRANT SELECT ON pg_stat_statements TO planetscale_discovery;

-- For replication analysis
GRANT SELECT ON pg_stat_wal_receiver TO planetscale_discovery;
GRANT SELECT ON pg_stat_subscription TO planetscale_discovery;

-- For PostgreSQL 10+ enhanced privileges (recommended)
GRANT pg_read_all_stats TO planetscale_discovery;
GRANT pg_read_all_settings TO planetscale_discovery;
```

If your database has additional schemas beyond `public`, repeat the `GRANT USAGE ON SCHEMA` and `GRANT SELECT ON ALL TABLES IN SCHEMA` statements for each schema you want analyzed.

### PostgreSQL cleanup

After PostgreSQL discovery is complete, remove the `planetscale_discovery` user from your database. This user has read access to your schema and system catalogs and should not be left in place.

```sql
DROP USER IF EXISTS planetscale_discovery;
```

If the user owns any objects, reassign ownership first:

```sql
REASSIGN OWNED BY planetscale_discovery TO postgres;
DROP OWNED BY planetscale_discovery;
DROP USER planetscale_discovery;
```

## Configuration

The discovery tool uses a YAML configuration file. Running `./ps-discovery` loads `./config.yaml` by default. Configure the `database` block for database discovery and enable any cloud providers you want included:

```yaml
engine: postgres

database:
  host: your-db-host.example.com
  port: 5432
  database: your_database
  username: planetscale_discovery
  password: secure_password_here
  ssl_mode: require

providers:
  aws:
    enabled: true
    regions:
      - us-east-1
  gcp:
    enabled: false
  supabase:
    enabled: false
  heroku:
    enabled: false
  neon:
    enabled: false

output:
  output_dir: ./discovery_output
```

## Running discovery

Run discovery with the generated `config.yaml`:

```shellscript
./ps-discovery
```

Or point to a specific config file:

```shellscript
./ps-discovery --config config.yaml
```

The tool produces a timestamped JSON file in your configured output directory, such as `planetscale_discovery_results_20260708T072229.json`. Upload this report to the [migration assessment tool](https://planetscale.com/liftoff) for instant feedback, or share it with PlanetScale for migration planning assistance.

Once PostgreSQL discovery is complete, remember to [clean up](#postgresql-cleanup) the `planetscale_discovery` user you created on your source database.

## Cloud provider setup

Each cloud provider requires specific credentials and permissions. Below is a summary of what you need for each. For detailed instructions including IAM policies and API enablement steps, see the [provider documentation](https://github.com/planetscale/ps-discovery/tree/main/docs/providers).

For third-party hosted Postgres providers, the discovery tool supports Supabase, Heroku, and Neon.

### AWS (RDS / Aurora)

The tool discovers RDS instances, Aurora clusters, VPC networking, security groups, and CloudWatch metrics.

**Authentication** (choose one):

- IAM instance profile (recommended when running on EC2)
- Access key and secret key
- IAM role assumption (for cross-account access)

**Required permissions:**

- RDS: `DescribeDBInstances`, `DescribeDBClusters`, `DescribeDBSubnetGroups`
- EC2: `DescribeVpcs`, `DescribeSubnets`, `DescribeSecurityGroups`, `DescribeRouteTables`
- CloudWatch: `GetMetricStatistics`, `ListMetrics`

**Configuration:**

```yaml
providers:
  aws:
    enabled: true
    regions:
      - us-east-1
      - us-west-2
    # Authentication - choose one approach:
    # Option 1: Use instance profile or environment variables (recommended)
    # Option 2: Explicit credentials
    access_key_id: AKIA...
    secret_access_key: ...
```

### Google Cloud (Cloud SQL / AlloyDB)

The tool discovers Cloud SQL instances, AlloyDB clusters, VPC networks, firewall rules, and Cloud Monitoring metrics.

**Authentication** (choose one):

- Application Default Credentials (recommended)
- Service account key file

**Required APIs** (must be enabled in your project):

- Cloud SQL Admin API
- Compute Engine API
- Cloud Monitoring API
- AlloyDB API (if using AlloyDB)

**Configuration:**

```yaml
providers:
  gcp:
    enabled: true
    project_id: your-project-id
    # Optional: path to service account key
    credentials_file: /path/to/service-account-key.json
```

### Supabase

The tool discovers project metadata, database configuration, PgBouncer settings, and connection details.

**Authentication:**

- [Personal Access Token](https://supabase.com/dashboard/account/tokens) (recommended, read-only)

**Configuration:**

```yaml
providers:
  supabase:
    enabled: true
    access_token: sbp_...
```

### Neon

The tool discovers Neon project metadata, branch topology, compute endpoints, autoscaling configuration, connection pooling, and database names.

**Authentication:**

- API key from Neon. See [Manage API keys](https://neon.com/docs/manage/api-keys) in the Neon docs.
- Or the `NEON_API_KEY` environment variable

**Configuration:**

```yaml
providers:
  neon:
    enabled: true
    api_key: your-neon-api-key
    discover_all: true
```

### Heroku

The tool discovers Heroku Postgres add-ons across all your apps, including plan details, database sizes, replica configurations, and connection pooling.

**Authentication:**

- API key from the [Heroku dashboard](https://dashboard.heroku.com/account)
- Or a Heroku CLI authorization token

**Configuration:**

```yaml
providers:
  heroku:
    enabled: true
    api_key: your-heroku-api-key
```

## Performance and safety

The default database analyzers are safe to run against production databases. They use read-only queries against system catalogs and statistics views, with very low performance impact.

The optional **data size analyzer** performs sampling queries against actual tables and can have a significant performance impact on large databases. If you need this analysis, consider running it against a read replica and starting with a low sampling percentage. This analyzer is disabled by default and must be explicitly opted into via configuration.

## Privacy and security

The discovery tool runs entirely on your infrastructure. No data is sent to external services during analysis.

**Collected:** Schema metadata, database configuration, usage statistics, infrastructure topology, and role names.

**Not collected:** Table contents, row data, application queries, passwords, or secrets. Passwords are used only to establish the database connection and are never included in the output.

## Get an instant migration assessment

Once you have your discovery report, upload it to our [migration assessment tool](https://planetscale.com/liftoff) for immediate feedback. The tool analyzes your report and provides:

- An overview of your existing database and optionally hosting provider
- Extension compatibility issues, schema defects, and any migration blockers
- A recommended migration path with main steps involved and the estimated duration

All analysis happens client-side in your browser — your discovery data is never sent to a server.

## Next steps

If you want tailored guidance from our migration team, [share your discovery report with us](https://planetscale.com/contact). You can also follow one of our migration guides on your own:

## Migrate using pgdump/restore

## Migrate using WAL streaming

## Migrate using Amazon DMS

## Migrate from Heroku

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
