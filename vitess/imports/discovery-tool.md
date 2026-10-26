---
url: https://planetscale.com/docs/vitess/imports/discovery-tool
title: "Discovery Tool"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Discovery Tool

> Assess your MySQL, MariaDB, or Vitess database and cloud infrastructure before migrating to PlanetScale Vitess.

This page covers Vitess.

Postgres version: [Postgres](https://planetscale.com/docs/postgres/imports/discovery-tool.md)

The PlanetScale Discovery Tool analyzes your existing MySQL-compatible database and cloud infrastructure to help plan your migration to PlanetScale Vitess. It collects metadata about your database configuration, schema structure, performance characteristics, replication topology, security settings, feature usage, and cloud resources. It never reads or stores actual table data.

The Discovery CLI also supports PostgreSQL discovery. See the [Postgres Discovery Tool guide](https://planetscale.com/docs/postgres/imports/discovery-tool.md) for PlanetScale Postgres-specific details.

![Where ps-discovery looks: cloud and vendor discovery, database discovery, and workload discovery](https://mintcdn.com/planetscale-2/YxnMMno34vnTE3Ub/images/imports/discovery-tool-topology.png?fit=max\&auto=format\&n=YxnMMno34vnTE3Ub\&q=85\&s=f2efb9d5a29c396484b0c677b60e66bf)

The tool produces a structured JSON report that PlanetScale uses to provide migration guidance tailored to your environment.

> [!NOTE]
>
> The discovery tool is open source and available on [GitHub](https://github.com/planetscale/ps-discovery). Essential setup steps are below. Advanced usage, troubleshooting, and detailed reference are available in the [full documentation](https://github.com/planetscale/ps-discovery/tree/main/docs).

## What it discovers

**Database analysis:**

* MySQL version, distribution, configuration, server variables, and cloud platform
* Schema structure, including databases, tables, columns, indexes, constraints, views, stored routines, triggers, and partitioning
* Performance statistics such as global status counter rates, process list summaries, InnoDB lock counters, and deadlock detection
* Replication configuration, including replica status, binary log inventory, binary log retention settings, and binary log format
* Security configuration, including SSL/TLS status, authentication plugin distribution, password policy settings, and aggregate privilege summaries
* Feature usage: full-text indexes, geospatial data types, foreign key constraints, table partitioning, InnoDB compression, XA transactions, prepared statements, Galera Cluster, and more

**Cloud infrastructure analysis:**

* Database instances, clusters, and their configurations
* RDS instances, Aurora clusters, Cloud SQL instances, and Azure Database for MySQL Flexible Servers
* VPC networking, subnets, security groups, firewall rules, and private connectivity
* Performance metrics from cloud monitoring services
* High availability and replica configurations

## Installation

The discovery tool requires Python 3.10 or later.

**1. Download and extract**

Download the latest release from [GitHub](https://github.com/planetscale/ps-discovery/releases) and extract it:

```bash theme={null}
unzip ps-discovery-2.2.0.zip
cd ps-discovery-2.2.0
```

**2. Run setup**

The setup script verifies your Python version, creates a virtual environment, installs dependencies, and writes a starter `config.yaml`:

```bash theme={null}
chmod +x setup.sh
./setup.sh
```

Select MySQL as the database engine during setup.

**3. Configure credentials**

Edit the generated configuration file to include your database and cloud provider credentials:

```bash theme={null}
nano config.yaml
```

At a minimum, configure your MySQL connection. See [Configuration](#configuration) below for the full format.

For advanced installation options, see the [Discovery Tool README](https://github.com/planetscale/ps-discovery/blob/main/README.md).

## Database user setup

Create a dedicated read-only user for the discovery tool. Connect to your MySQL-compatible database as a privileged user and run the following:

```sql theme={null}
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

> [!NOTE]
>
> On Amazon RDS, Aurora MySQL, Google Cloud SQL for MySQL, Azure Database for MySQL, MariaDB, and Percona Server, create the user through your administrative database user. Some managed services restrict access to certain system tables. The discovery tool reports those gaps and continues with the data it can collect.

### PlanetScale and Vitess credentials

For PlanetScale databases, your existing branch credentials are sufficient. The discovery tool automatically detects PlanetScale and Vitess environments and adapts its queries.

Add your branch credentials to `config.yaml`:

```yaml theme={null}
engine: mysql

mysql:
  host: aws.connect.psdb.cloud
  port: 3306
  username: your_branch_username
  password: your_branch_password
  ssl_mode: required
```

Then run discovery with the values from the config file:

```bash theme={null}
./ps-discovery --config config.yaml
```

**PlanetScale and Vitess-specific behavior:**

* The tool detects scoped `information_schema` in Vitess and automatically falls back to per-database iteration
* System databases such as `_vt`, `mysql`, and `performance_schema` are excluded
* Features not supported by Vitess are detected and reported

### MySQL cleanup

> [!WARNING]
>
> After MySQL discovery is complete, remove the `planetscale_discovery` user from your database. This user has read access to your schema and system metadata and should not be left in place.

```sql theme={null}
DROP USER IF EXISTS 'planetscale_discovery'@'%';
```

## Configuration

The discovery tool uses a YAML configuration file. Running `./ps-discovery` loads `./config.yaml` by default. Configure the `mysql` block for database discovery and enable any cloud providers you want included:

```yaml theme={null}
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
  azure:
    enabled: false

output:
  output_dir: ./mysql_discovery_output
```

Set `database` to a specific database name to focus analysis on one database:

```yaml theme={null}
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

```bash theme={null}
./ps-discovery
```

Or point to a specific config file:

```bash theme={null}
./ps-discovery --config config.yaml
```

The tool produces a timestamped JSON file in your configured output directory, such as `planetscale_discovery_results_20260708T072229.json`. Share this report with PlanetScale for migration planning assistance.

> [!NOTE]
>
> Once MySQL discovery is complete, remember to [clean up](#mysql-cleanup) the `planetscale_discovery` user you created on your source database.

## Cloud provider setup

Each cloud provider requires specific credentials and permissions. Below is a summary of what you need for each. For detailed instructions including IAM policies and API enablement steps, see the [provider documentation](https://github.com/planetscale/ps-discovery/tree/main/docs/providers).

### AWS (RDS / Aurora)

The tool discovers RDS instances, Aurora clusters, VPC networking, security groups, and CloudWatch metrics.

**Authentication** (choose one):

* AWS profile
* IAM instance profile when running on EC2
* Access key and secret key
* IAM role assumption for cross-account access

**Required permissions:**

* RDS: `DescribeDBInstances`, `DescribeDBClusters`, `DescribeDBSubnetGroups`, `DescribeDBClusterParameterGroups`, `DescribeDBParameterGroups`, `DescribeOptionGroups`
* EC2: `DescribeVpcs`, `DescribeSubnets`, `DescribeSecurityGroups`, `DescribeRouteTables`, `DescribeInternetGateways`, `DescribeNatGateways`, `DescribeVpcEndpoints`
* CloudWatch: `GetMetricData`, `ListMetrics`
* STS: `GetCallerIdentity`

**Configuration:**

```yaml theme={null}
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

```yaml theme={null}
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

* Service account key file
* Application Default Credentials
* Environment variables

**Required APIs** (must be enabled in your project):

* Cloud SQL Admin API
* Compute Engine API
* Cloud Monitoring API

**Configuration:**

```yaml theme={null}
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

```yaml theme={null}
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

### Azure (Database for MySQL)

The tool discovers Azure Database for MySQL Flexible Servers, virtual networks, network security groups, and firewall rules.

**Authentication** (choose one):

* Service principal with the Reader role (recommended)
* Azure CLI (`az login`) or a managed identity
* `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`, `AZURE_TENANT_ID`, and `AZURE_SUBSCRIPTION_ID` environment variables

**Required permissions:**

* Built-in Reader role at subscription or resource-group scope
* Resource providers registered on the subscription: `Microsoft.DBforMySQL` and `Microsoft.Network`

**Configuration:**

```yaml theme={null}
providers:
  azure:
    enabled: true
    subscription_id: 22222222-2222-2222-2222-222222222222
    credentials:
      tenant_id: 11111111-1111-1111-1111-111111111111
      client_id: 00000000-0000-0000-0000-000000000000
      client_secret: your-client-secret
    discover_all: true
```

You can also sign in with `az login` and omit the `credentials` block, or focus discovery on specific servers:

```yaml theme={null}
providers:
  azure:
    enabled: true
    subscription_id: 22222222-2222-2222-2222-222222222222
    discover_all: false
    resources:
      mysql_flexible_servers:
        - mysql-reporting
    regions:
      - eastus
```

## Performance and safety

The default database analyzers are safe to run against production databases. They use read-only queries against system catalogs and statistics views, with very low performance impact.

> [!WARNING]
>
> The discovery tool can query metadata and statistics across every accessible database when the `database` field is empty. For environments with many databases or very large schemas, consider targeting one database at a time or running the tool against a replica.

## Privacy and security

The discovery tool runs entirely on your infrastructure. No data is sent to external services during analysis.

**Collected:** Schema metadata, database configuration, usage statistics, replication metadata, infrastructure topology, aggregate security information, and feature usage.

**Not collected:** Table contents, row data, query text, slow query log entries, passwords, secrets, connection strings, application code, or individual grant details. Passwords are used only to establish the database connection and are never included in the output.

## Next steps

Once you have your discovery report, [share it with us](https://planetscale.com/contact) if you want tailored migration guidance. You can also follow one of our migration guides on your own:

> **[Database imports](https://planetscale.com/docs/vitess/imports/database-imports.md)**

> **[Migrate from AWS RDS](https://planetscale.com/docs/vitess/imports/aws-rds-migration-guide.md)**

> **[Migrate from Amazon Aurora](https://planetscale.com/docs/vitess/imports/amazon-aurora-migration-guide.md)**

> **[Migrate from Google Cloud SQL](https://planetscale.com/docs/vitess/imports/gcp-cloudsql-migration-guide.md)**

> **[Migrate from Azure](https://planetscale.com/docs/vitess/imports/azure-database-for-mysql-migration-guide.md)**

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
