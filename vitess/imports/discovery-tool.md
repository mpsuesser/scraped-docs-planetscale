---
url: https://planetscale.com/docs/vitess/imports/discovery-tool
title: "Discovery Tool"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

The PlanetScale Discovery Tool analyzes your existing MySQL-compatible database and cloud infrastructure to help plan your migration to PlanetScale Vitess. It collects metadata about your database configuration, schema structure, performance characteristics, replication topology, security settings, feature usage, and cloud resources. It never reads or stores actual table data.

The Discovery CLI also supports PostgreSQL discovery. See the [Postgres Discovery Tool guide](../../postgres/imports/discovery-tool.md) for PlanetScale Postgres-specific details.

The tool produces a structured JSON report that PlanetScale uses to provide migration guidance tailored to your environment.

The discovery tool is open source and available on [GitHub](https://github.com/planetscale/ps-discovery). Essential setup steps are below. Advanced usage, troubleshooting, and detailed reference are available in the [full documentation](https://github.com/planetscale/ps-discovery/tree/main/docs).

## What it discovers

**Database analysis:**

- MySQL version, distribution, configuration, server variables, and cloud platform
- Schema structure, including databases, tables, columns, indexes, constraints, views, stored routines, triggers, and partitioning
- Performance statistics such as global status counter rates, process list summaries, InnoDB lock counters, and deadlock detection
- Replication configuration, including replica status, binary log inventory, binary log retention settings, and binary log format
- Security configuration, including SSL/TLS status, authentication plugin distribution, password policy settings, and aggregate privilege summaries
- Feature usage: full-text indexes, geospatial data types, foreign key constraints, table partitioning, InnoDB compression, XA transactions, prepared statements, Galera Cluster, and more

**Cloud infrastructure analysis:**

- Database instances, clusters, and their configurations
- RDS instances, Aurora clusters, and Cloud SQL instances
- VPC networking, subnets, security groups, firewall rules, and private connectivity
- Performance metrics from cloud monitoring services
- High availability and replica configurations

## Installation

The discovery tool requires Python 3.10 or later.

For advanced installation options, see the [Discovery Tool README](https://github.com/planetscale/ps-discovery/blob/main/README.md).

## Database user setup

Create a dedicated read-only user for the discovery tool. Connect to your MySQL-compatible database as a privileged user and run the following:

```sql
-- Create a dedicated user for database discovery
CREATE USER 'planetscale_discovery'@'%' IDENTIFIED BY 'secure_password_here';

-- Grant read access for schema analysis
GRANT SELECT ON *.* TO 'planetscale_discovery'@'%';

-- Grant process privilege for performance analysis and SHOW PROCESSLIST
GRANT PROCESS ON *.* TO 'planetscale_discovery'@'%';

-- Grant replication client for binary log and replica status analysis
GRANT REPLICATION CLIENT ON *.* TO 'planetscale_discovery'@'%';

-- Apply changes
FLUSH PRIVILEGES;
```

On Amazon RDS, Aurora MySQL, Google Cloud SQL for MySQL, MariaDB, and Percona Server, create the user through your administrative database user. Some managed services restrict access to certain system tables. The discovery tool reports those gaps and continues with the data it can collect.

### PlanetScale and Vitess credentials

For PlanetScale databases, your existing branch credentials are sufficient. The discovery tool automatically detects PlanetScale and Vitess environments and adapts its queries.

Add your branch credentials to `config.yaml`:

```yaml
engine: mysql

mysql:
  host: aws.connect.psdb.cloud
  port: 3306
  username: your_branch_username
  password: your_branch_password
  ssl_mode: required
```

Then run discovery with the values from the config file:

```shellscript
./ps-discovery --config config.yaml
```

**PlanetScale and Vitess-specific behavior:**

- The tool detects scoped `information_schema` in Vitess and automatically falls back to per-database iteration
- System databases such as `_vt`, `mysql`, and `performance_schema` are excluded
- Features not supported by Vitess are detected and reported

### MySQL cleanup

After MySQL discovery is complete, remove the `planetscale_discovery` user from your database. This user has read access to your schema and system metadata and should not be left in place.

```sql
DROP USER IF EXISTS 'planetscale_discovery'@'%';
```

## Configuration

The discovery tool uses a YAML configuration file. Running `./ps-discovery` loads `./config.yaml` by default. Configure the `mysql` block for database discovery and enable any cloud providers you want included:

```yaml
engine: mysql

mysql:
  host: your-db-host.example.com
  port: 3306
  database: "" # Leave empty to discover all databases
  username: planetscale_discovery
  password: secure_password_here
  ssl_mode: required

providers:
  aws:
    enabled: true
    regions:
      - us-east-1
  gcp:
    enabled: false

output:
  output_dir: ./mysql_discovery_output
```

Set `database` to a specific database name to focus analysis on one database:

```yaml
engine: mysql

mysql:
  host: your-db-host.example.com
  port: 3306
  database: my_application_db
  username: planetscale_discovery
  password: secure_password_here
  ssl_mode: required
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

The tool produces a timestamped JSON file in your configured output directory, such as `planetscale_discovery_results_20260708T072229.json`. Share this report with PlanetScale for migration planning assistance.

Once MySQL discovery is complete, remember to [clean up](#mysql-cleanup) the `planetscale_discovery` user you created on your source database.

## Cloud provider setup

Each cloud provider requires specific credentials and permissions. Below is a summary of what you need for each. For detailed instructions including IAM policies and API enablement steps, see the [provider documentation](https://github.com/planetscale/ps-discovery/tree/main/docs/providers).

### AWS (RDS / Aurora)

The tool discovers RDS instances, Aurora clusters, VPC networking, security groups, and CloudWatch metrics.

**Authentication** (choose one):

- AWS profile
- IAM instance profile when running on EC2
- Access key and secret key
- IAM role assumption for cross-account access

**Required permissions:**

- RDS: `DescribeDBInstances`, `DescribeDBClusters`, `DescribeDBSubnetGroups`, `DescribeDBClusterParameterGroups`, `DescribeDBParameterGroups`, `DescribeOptionGroups`
- EC2: `DescribeVpcs`, `DescribeSubnets`, `DescribeSecurityGroups`, `DescribeRouteTables`, `DescribeInternetGateways`, `DescribeNatGateways`, `DescribeVpcEndpoints`
- CloudWatch: `GetMetricStatistics`, `ListMetrics`
- STS: `GetCallerIdentity`

**Configuration:**

```yaml
providers:
  aws:
    enabled: true
    regions:
      - us-east-1
      - us-west-2
    discover_all: true
    # Authentication - choose one approach:
    # Option 1: Use an AWS profile
    credentials:
      profile: migration-discovery
    # Option 2: Assume a role
    # credentials:
    #   role_arn: "arn:aws:iam::123456789012:role/PlanetScaleDiscoveryRole"
    #   external_id: "unique-external-id"
```

You can also focus discovery on specific AWS resources:

```yaml
providers:
  aws:
    enabled: true
    discover_all: false
    resources:
      rds_instances:
        - production-db-1
        - staging-db-1
      aurora_clusters:
        - prod-cluster
    regions:
      - us-east-1
```

### Google Cloud (Cloud SQL)

The tool discovers Cloud SQL instances, VPC networks, firewall rules, and Cloud Monitoring metrics.

**Authentication** (choose one):

- Service account key file
- Application Default Credentials
- Environment variables

**Required APIs** (must be enabled in your project):

- Cloud SQL Admin API
- Compute Engine API
- Cloud Monitoring API

**Configuration:**

```yaml
providers:
  gcp:
    enabled: true
    project_id: your-project-id
    regions:
      - us-central1
      - us-east1
    discover_all: true
    credentials:
      service_account_key: /path/to/ps-discovery-key.json
```

You can also focus discovery on specific Google Cloud resources:

```yaml
providers:
  gcp:
    enabled: true
    project_id: your-project-id
    discover_all: false
    resources:
      cloud_sql_instances:
        - prod-db-instance
        - staging-db-instance
    regions:
      - us-central1
```

## Performance and safety

The default database analyzers are safe to run against production databases. They use read-only queries against system catalogs and statistics views, with very low performance impact.

The discovery tool can query metadata and statistics across every accessible database when the `database` field is empty. For environments with many databases or very large schemas, consider targeting one database at a time or running the tool against a replica.

## Privacy and security

The discovery tool runs entirely on your infrastructure. No data is sent to external services during analysis.

**Collected:** Schema metadata, database configuration, usage statistics, replication metadata, infrastructure topology, aggregate security information, and feature usage.

**Not collected:** Table contents, row data, query text, slow query log entries, passwords, secrets, connection strings, application code, or individual grant details. Passwords are used only to establish the database connection and are never included in the output.

## Next steps

Once you have your discovery report, [share it with us](https://planetscale.com/contact) if you want tailored migration guidance. You can also follow one of our migration guides on your own:

## Database import workflow

## Migrate from AWS RDS

## Migrate from Amazon Aurora

## Migrate from Google Cloud SQL

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
