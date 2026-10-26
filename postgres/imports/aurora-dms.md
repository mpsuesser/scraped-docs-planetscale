---
url: https://planetscale.com/docs/postgres/imports/aurora-dms
title: "Aurora Dms"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Migrate from Aurora using Amazon DMS with CloudFormation

> This method uses Infrastructure as Code with Step Functions workflow automation for a completely managed migration experience.

This page covers Postgres.

> [!NOTE]
>
> Before beginning your migration, we recommend running our [migration assessment tool](https://planetscale.com/liftoff) for instant feedback on migration complexity, potential blockers, and the recommended migration path.

[Amazon Database Migration Service (DMS)](https://aws.amazon.com/dms/) provides a fully automated approach to migrate your PostgreSQL database to PlanetScale Postgres.

> [!NOTE]
>
> Want expert guidance for your migration? PlanetScale's [migration services](https://planetscale.com/migrate) are available to help you plan and execute a smooth, successful move.

## Overview

This automated migration method:

**Step 1**

**Pre-migration schema setup** (essential for production)

**Step 2**

Deploys DMS infrastructure via CloudFormation template

**Step 3**

Configures source and target database endpoints automatically

**Step 4**

Creates Step Functions workflow for automated migration orchestration

**Step 5**

Provides built-in monitoring, notifications, and automated cleanup

**Step 6**

Requires minimal manual intervention - mostly console clicks

> [!WARNING]
>
> **Critical: AWS DMS Schema Object Limitations**
>
> AWS DMS **only migrates table data and primary keys**. All other PostgreSQL schema objects must be handled separately:
>
> * Secondary indexes
> * Sequences and their current values
> * Views, functions, and stored procedures
> * Constraints (foreign keys, unique, check)
> * Triggers and custom data types
>
> Deploy your complete Aurora schema to PlanetScale BEFORE starting DMS migration to preserve performance and avoid application errors.

> [!NOTE]
>
> This method requires an AWS account and will incur AWS DMS charges. The CloudFormation template includes cost optimization features. Review [AWS DMS pricing](https://aws.amazon.com/dms/pricing/) before proceeding.

## General Prerequisites

Before starting the migration:

* Have an active AWS user with CloudFormation, EC2, DMS, SNS, and Step Functions permissions
* Source Aurora database accessible from AWS VPC
* Connection details for your PlanetScale Postgres database from the console
* VPC with at least 2 subnets in different Availability Zones

## Source database prerequisites

The Task that AWS DMS runs will automatically perform these 7 validation checks before starting the migration. Confirm before starting this process that they will succeed.

| Check Name | What It Validates | Required Action (if needed) |
| :- | :- | :- |
| Database Version Compatibility | Verifies your PostgreSQL version is supported by AWS DMS | Ensure you're running a supported PostgreSQL version (9.4+) |
| Target Database Privileges | Confirms PlanetScale user has sufficient permissions | No action should be needed - PlanetScale credentials include required permissions |
| Replication Slots Available | Checks that replication slots are available for CDC | Verify `max_replication_slots >= 1` in Aurora parameter group |
| Source Database Read Privileges | Validates source user can read all tables for migration | Ensure source user has SELECT privileges on all tables to migrate |
| WAL Level Configuration | Confirms WAL level is set to 'logical' for CDC replication | Set `wal_level = logical` in Aurora parameter group (requires restart) |
| WAL Sender Timeout | Ensures WAL sender timeout is at least 10 seconds | Set `wal_sender_timeout >= 10000` (10 seconds) in parameter group |
| Maximum WAL Senders | Verifies enough WAL sender processes for CDC | Set `max_wal_senders >= 2` in Aurora parameter group |

## Step 1: Pre-Migration Schema Setup

Deploy your complete Aurora schema to PlanetScale BEFORE starting the CloudFormation stack. This ensures optimal performance and prevents application errors.

### Extract and Apply Schema

**Step 1**

Extract your complete schema from Aurora:

```bash theme={null}
pg_dump -h aurora-cluster-endpoint.amazonaws.com -p 5432 \
        -U username -d database --schema-only \
        --no-owner --no-privileges -f aurora_schema.sql
```

**Step 2**

Apply the schema to PlanetScale:

```bash theme={null}
psql -h your-planetscale-host -p 5432 -U username -d database -f aurora_schema.sql
```

> [!NOTE]
>
> **Foreign Key Constraints**
>
> If the schema application fails due to foreign key constraint issues, you can temporarily remove them from the schema file and apply them after DMS completes the data migration.

### Verify Schema Application

Quickly verify your schema was applied successfully:

```sql theme={null}
-- Check that tables and sequences exist
SELECT
    (SELECT COUNT(*) FROM information_schema.tables WHERE table_schema = 'public') as tables,
    (SELECT COUNT(*) FROM information_schema.sequences WHERE sequence_schema = 'public') as sequences,
    (SELECT COUNT(*) FROM pg_indexes WHERE schemaname = 'public') as indexes;
```

## Step 2: Check DMS IAM Roles

Before deploying, check if DMS service roles already exist in your AWS account:

**Step 1**

Go to [IAM Console](https://console.aws.amazon.com/iam/)

**Step 2**

Click "Roles" in the left sidebar

**Step 3**

Search for these role names:

* `dms-vpc-role`
* `dms-cloudwatch-logs-role`

- **If neither role exists**: Set `CreateDMSRoles` parameter to `true` in Step 4
- **If both roles exist**: Set `CreateDMSRoles` parameter to `false` in Step 4
- **If one role exists but not the other**: Consider manually creating the roles per guidance in the [AWS DMS documentation](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_VPC_Endpoints.html#CHAP_VPC_Endpoints.prereq) and set `CreateDMSRoles` parameter to `false` in Step 4

## Step 3: Download CloudFormation Template

Get the CloudFormation template:

**Step 1**

Visit: [https://github.com/planetscale/migration-scripts/tree/main/postgres-planetscale](https://github.com/planetscale/migration-scripts/tree/main/postgres-planetscale)

**Step 2**

Right-click on `aurora-to-ps-dms.yaml` → "Save link as"

**Step 3**

Save the file to your computer

## Step 4: Deploy CloudFormation Stack

**Step 1**

Navigate to [AWS CloudFormation Console](https://console.aws.amazon.com/cloudformation/)

**Step 2**

Click **"Create stack"** → **"With new resources (standard)"**

**Step 3**

Select **"Upload a template file"**

**Step 4**

Click **"Choose file"** and select the downloaded template

**Step 5**

Click **"Next"**

### Configure Stack Parameters

**Stack name**: `postgres2planetscale` or any name you want but note that overly-long names can cause resource creation issues

#### VPC Information

* **VPC ID**: Select your VPC from dropdown
* **Subnet IDs**: Select 2+ subnets in different AZs which are "public" subnets in that they have route tables through an Internet Gateway (IGW) and NACLs that allow outbound routing. See [Subnet types](https://docs.aws.amazon.com/vpc/latest/userguide/configure-subnets.html#subnet-types) in the AWS documentation for more information

#### Source Database (Your Aurora Postgres database)

* **Source Endpoint Host**: Your Aurora hostname (primary write endpoint is best, not a proxy)
* **Source Port**: `5432` (or your custom port, valid range: 1024-65535)
* **Source Database Name**: Your database name (1-63 characters, must start with a letter, alphanumeric and underscore only)
* **Source Username**: Your database username (1-63 characters, must start with a letter, alphanumeric and underscore only)
* **Source Password**: Your database password (4-128 characters, will be hidden in console)

#### Target Database (PlanetScale Postgres)

From your PlanetScale console connection details:

* **Target Endpoint Host**: PlanetScale host (from connection details)
* **Target Port**: `5432` (standard PostgreSQL port)
* **Target Database Name**: PlanetScale database name (1-63 characters, must start with a letter, alphanumeric and underscore only)
* **Target Username**: PlanetScale username (1-63 characters, must start with a letter, alphanumeric and underscore only)
* **Target Password**: PlanetScale password (4-128 characters, will be hidden in console)

#### Additional Features

* **DMS Instance Class**: `dms.c6i.xlarge` (template default, can select from dropdown)
  * Options include: dms.t3.micro, dms.t3.small, dms.t3.medium, dms.t3.large, dms.c6i.large, dms.c6i.xlarge, dms.c6i.2xlarge, dms.c6i.4xlarge
  * Recommended: dms.c6i.large or larger for production workloads
* **Migration Type**: `full-load-and-cdc` (recommended)
  * Options: `full-load`, `cdc`, `full-load-and-cdc`
* **Migration Bucket Name**: Base name for S3 bucket that stores DMS assessment reports
  * Must be 3-35 characters, start/end with lowercase letter or number
  * Can contain lowercase letters, numbers, hyphens, and periods
  * Region and account ID will be automatically appended to create unique bucket name
  * Example: `my-migration-bucket` becomes `my-migration-bucket-us-east-1-123456789012`
* **Enable Automation**: `true` ⭐ **Important: This creates the Step Functions workflow**
* **Create DMS Roles**: `true` or `false` (based on Step 2 findings)
* **Notification Email**: Your email address for migration status updates and alerts

> [!NOTE]
>
> **Schema-First Approach Built-In**
>
> The CloudFormation template is pre-configured for schema-first migrations with:
>
> * `TargetTablePrepMode: DO_NOTHING` (automatically set, uses your existing schema)
> * Enhanced performance tuning settings
> * Built-in row-level validation
> * Batch processing optimizations
> * Memory tuning for large datasets
>
> Complete Step 1 (pre-migration schema setup) before deploying this stack for optimal results.

**Step 1**

Click **"Next"** → **"Next"** → Check **"I acknowledge that AWS CloudFormation might create IAM resources"**

**Step 2**

Click **"Submit"**

## Step 5: Wait for Stack Completion

**Step 1**

Stay on the CloudFormation console

**Step 2**

Click on your stack name to view details

**Step 3**

Watch the **"Events"** tab for progress

**Step 4**

Wait for stack status to show **`CREATE_COMPLETE`** (typically 10-15 minutes)

## Step 6: Confirm your email notification subscription

**Step 1**

Check the inbox for the email used above

**Step 2**

You will get an email from `no-reply@sns.amazonaws.com` "DMS Migration Workflow Notifications"

**Step 3**

Click the link for **Confirm Subscription**

Note that after the migration task completes and the stack is deleted, you would receive no further communications from this AWS SNS Topic, but other SNS topics may use the same address.

## Step 7: Get Workflow Input Configuration

**Step 1**

In your completed CloudFormation stack, click the **"Outputs"** tab

**Step 2**

Find the output key **`StepFunctionsPayloadTemplate`**

**Step 3**

**Copy the entire JSON value** (this contains the configuration for your migration) The JSON should look like the following example:

```json theme={null}
{
    "replicationInstanceArn": "arn:aws:dms:us-east-2:1234567890:rep:YMZ2AH4YAJCRNJKOWBR7EEIRGE",
    "sourceEndpointArn": "arn:aws:dms:us-east-2:1234567890:endpoint:SIVNPTNFJZDCVK4ODTN6ZLONN4",
    "targetEndpointArn": "arn:aws:dms:us-east-2:1234567890:endpoint:MLSCVENBKVBWJKRVJ27EWB32IU",
    "replicationTaskArn": "arn:aws:dms:us-east-2:1234567890:task:QZCBNW565VH2JG2KE5UXX42LS4",
    "sourceEndpointHost": "prod-aurora-cluster.cluster-abc1234567.us-east-2.rds.amazonaws.com",
    "sourcePort": "5432"
}
```

## Step 8: Locate Step Functions Workflow

**Step 1**

While still in the **"Outputs"** tab find the key **`StepFunctionsConsoleURL`**

**Step 2**

Click on the URL link to open the AWS Step Functions console for the workflow created here

The workflow includes these automated steps:

* **Network Connectivity Check**: Tests connections to both source and target databases
* **Security Group Auto-Fix**: Automatically corrects Aurora security group settings if DMS connectivity fails
* **Pre-Migration Validation**: Validates database schemas, table structures, and data types with row-level validation
* **Migration Task Start**: Launches optimized DMS full-load and CDC replication with performance tuning
* **Progress Monitoring**: Continuously monitors migration progress with enhanced error handling and batch processing
* **Built-in Optimization**: Uses tuned task settings for improved throughput and memory management

## Step 9: Start Migration Workflow

**Step 1**

In the Step Functions state machine, click **"Start execution"**

**Step 2**

**Execution name**: Leave as auto-generated

**Step 3**

**Input**: **Paste the JSON** you copied from CloudFormation outputs

**Step 4**

Click **"Start execution"**

The workflow will automatically:

* Test database connections
* Start the DMS migration task
* Monitor progress
* Send notifications
* Handle errors and retries

## Step 10: Monitor Migration Progress

There are several tools you can use to monitor the progress and if necessary troubleshoot potential failures.

### DMS Console

**Step 1**

Go to [DMS Console](https://console.aws.amazon.com/dms/)

**Step 2**

Click **"Tasks"**

**Step 3**

Select the task **Identifier** for this migration

**Step 4**

View your task for detailed table-level progress

### Step Functions Console

**Step 1**

Watch the visual workflow progress in the Step Functions console

**Step 2**

Each step will show green (success), red (failure), or blue (in progress)

**Step 3**

Click on individual steps to see detailed logs

### CloudWatch Dashboard

**Step 1**

Navigate to [CloudWatch Console](https://console.aws.amazon.com/cloudwatch/)

**Step 2**

Click **"Dashboards"** → **"Automatic Dashboards"** → **"DMS-Migration-Dashboard"**

**Step 3**

Monitor key metrics:

* Full load progress percentage
* CDC latency
* Error counts
* Throughput

### Wait for automated emails

You will receive an email once the migration has reached a 100% full load and CDC replication is ongoing.

If the workflow does fail at any point, you will instead receive an email with where the failure occurred and then you can review the previously mentioned tools for more information.

## Step 11: Post-Migration Sequence Synchronization

After DMS completes, sequences need their values synchronized:

> [!WARNING]
>
> **Critical: Sequence Synchronization**
>
> Sequence values must be set ahead of Aurora values to prevent duplicate key errors when applications start using PlanetScale.

### Get Current Sequence Values from Aurora

```sql theme={null}
-- Run on Aurora database to get all current sequence values
SELECT
    sequence_name,
    last_value,
    'SELECT setval(''' || sequence_name || ''', ' || (last_value + 1000) || ');' as update_command
FROM information_schema.sequences
WHERE sequence_schema = 'public'
ORDER BY sequence_name;
```

### Update Sequences in PlanetScale

```sql theme={null}
-- For each sequence, run the update command from above
-- Example commands (values set ahead of Aurora):
SELECT setval('users_id_seq', 16234);  -- Aurora value + 1000
SELECT setval('orders_id_seq', 99765);  -- Aurora value + 1000
SELECT setval('products_id_seq', 6432);  -- Aurora value + 1000

-- Verify sequence values are ahead of Aurora
SELECT sequence_name, last_value
FROM information_schema.sequences
WHERE sequence_schema = 'public'
ORDER BY sequence_name;
```

### Apply Remaining Constraints

Now apply foreign key constraints that were deferred:

```sql theme={null}
-- Apply foreign key constraints
\i constraints.sql

-- Verify constraints were applied successfully
SELECT conname, contype, conrelid::regclass AS table_name
FROM pg_constraint
WHERE connamespace = 'public'::regnamespace
  AND contype = 'f'  -- foreign key constraints
ORDER BY conrelid::regclass::text;
```

## Step 12: Application Cutover

When the Step Functions workflow or DMS task itself indicates migration is ready (Status is "Load completed, replication ongoing"), then you can begin your cutover process.

### Comprehensive Pre-Cutover Validation

> [!WARNING]
>
> **Complete Validation Required**
>
> Validate ALL schema objects and data integrity before cutover. Missing objects will cause application failures.

```sql theme={null}
-- Validate table row counts match Aurora
SELECT
    schemaname,
    tablename,
    n_tup_ins as estimated_rows
FROM pg_stat_user_tables
WHERE schemaname = 'public'
ORDER BY tablename;
```

### Pre-Cutover Checklist (Automated)

AWS DMS ensures:

* &#x20;Full load is 100% complete
* &#x20;CDC latency is under 5 seconds
* &#x20;Data validation passes
* &#x20;Both databases are synchronized

### Cutover Process

**Step 1**

**Complete sequence synchronization and constraint application** using steps above

**Step 2**

**Run comprehensive validation** to ensure all objects are functional

**Step 3**

**Put application in maintenance mode**, pause all writes from your application to Aurora

**Step 4**

**Wait for DMS to confirm final sync**

**Step 5**

**Update your application's database connection strings** to use PlanetScale details

**Step 6**

**Restart or redeploy your application**

**Step 7**

**Test critical functionality**, especially features using sequences and indexes

## Automated Cleanup (mostly)

> [!WARNING]
>
> **Schema Objects and Cleanup**
>
> The CloudFormation stack cleanup **does not** affect your migrated schema objects in PlanetScale. Your indexes, sequences, and other objects remain intact.

**Step 1**

Go to your CloudFormation stack

**Step 2**

Click **"Delete"**

**Step 3**

Click the **"Delete"** confirmation popup

> [!NOTE]
>
> The first time you attempt to delete the stack, the full process will fail to delete some of the resources. Minimally the S3 bucket created to store the DMS pre-migration test reports will need to be manually emptied before it can be deleted. If the Step Functions workflow had to modify Aurora security group, then the rule added needs to be deleted as well. Both of these resources could be safely left behind, however the S3 bucket's data will incur ongoing charges.

**Step 4**

In the CloudFormation stack **"Resources"** tab, find the resources where deletion failed

**Step 5**

Find the Resource named **PreMigrationAssessmentBucket**

**Step 6**

Click to open the link under the **Physical ID** heading for this resource
This will take you to the Amazon S3 console for this bucket. The assessment-folder is a versioned object in S3, which means that directly deleting it here does not actually remove it, but instead places a deleted marker on the version.

**Step 7**

To fully empty the bucket you will need to navigate to the main console page. Click **"Buckets"** from the top nav, or **"General purpose buckets"** from the left nav

**Step 8**

Select the bucket used, it will start with the name of the CloudFormation stack

**Step 9**

Click **"Empty"**

**Step 10**

You can now re-attempt the stack deletion from the 1st step here

## Troubleshooting

### Stack Creation Issues

**Permission Errors:**

* Ensure your AWS user has CloudFormation, DMS, Step Functions, and IAM permissions
* Check that you acknowledged IAM resource creation during stack creation

**Network Issues:**

* Verify your VPC allows internet access for DMS to reach PlanetScale
* Check security groups allow port 5432 access
* Ensure subnets are in different Availability Zones

### Step Functions Workflow Issues

**Workflow Creation Fails:**

* Verify you copied the complete JSON from CloudFormation outputs
* Check that the Step Functions execution role exists

**Migration Task Fails:**

* Check Step Functions execution details for specific error messages
* Verify database connection details are correct
* Ensure source database has logical replication enabled

### Connection Problems

**Source Database:**

* Verify hostname, port, username, and password
* Check that source database allows connections from DMS subnet
* Ensure database has logical replication enabled (`rds.logical_replication = 1` for RDS)

**Target Database (PlanetScale):**

* Double-check connection details from PlanetScale console
* Verify PlanetScale database is active and accessible
* Test connectivity from AWS region to PlanetScale

### Schema-Related Issues

**"sequence does not exist" errors after cutover:**

```sql theme={null}
-- Check if sequences exist
SELECT * FROM information_schema.sequences WHERE sequence_name = 'your_sequence';

-- Recreate missing sequence
CREATE SEQUENCE your_sequence START WITH 1;
SELECT setval('your_sequence', (SELECT MAX(id) FROM your_table));
```

**Application slowness after migration:**

* Missing indexes are the most common cause
* Run `EXPLAIN ANALYZE` on slow queries to identify missing indexes
* Apply indexes from your schema extraction

**Foreign key constraint violations:**

```sql theme={null}
-- Find constraint violations before applying constraints
SELECT COUNT(*) FROM child_table c
WHERE NOT EXISTS (SELECT 1 FROM parent_table p WHERE p.id = c.parent_id);
```

**Function/view dependency errors:**

* Apply objects in correct order: sequences → indexes → views → functions → constraints
* Check for Aurora-specific functions that may need modification for PlanetScale

**Permission errors during schema application:**

* Ensure PlanetScale user has CREATE privileges
* Check if objects already exist and need to be dropped first

## Step Functions Workflow Benefits

Using the automated Step Functions workflow provides:

* **Visual Progress Tracking**: See each migration phase in real-time
* **Automatic Error Handling**: Built-in retry logic and error notifications
* **Audit Trail**: Complete log of migration steps and timings

## Advanced Options

### CloudFormation Template Optimizations

The updated CloudFormation template includes these performance enhancements:

**Task Configuration Improvements:**

* `BatchApplyEnabled: true` - Improves target database write performance
* `ValidationMode: ROW_LEVEL` - Built-in data validation with 10K failure tolerance
* Memory tuning: 2GB total memory limit with optimized batch sizing
* Enhanced CDC processing with 5-second commit timeout
* Statement caching for improved query performance

**Monitoring Enhancements:**

* Comprehensive logging for all DMS components
* CloudWatch integration for real-time metrics
* Automated failure handling and notifications

**Schema-First Integration:**

* `TargetTablePrepMode: DO_NOTHING` preserves your pre-deployed schema
* `FullLoadIgnoreConflicts: true` handles edge cases gracefully
* Optimized for existing table structures and indexes

### Custom Migration Settings

Modify template parameters for:

* Different DMS instance sizes
* Custom migration types (full-load only, CDC only)
* Extended monitoring periods
* Custom notification settings

### Multiple Database Migration

Deploy multiple stacks with different names to migrate multiple databases in parallel.

## Migration Considerations

Before migration, review:

> **[PostgreSQL version compatibility](https://planetscale.com/docs/postgres/imports/postgres-imports.md#postgresql-version-compatibility)**

> **[Extension support limitations](https://planetscale.com/docs/postgres/imports/postgres-imports.md#extension-support)**

> **[Third-party enhancement restrictions](https://planetscale.com/docs/postgres/imports/postgres-imports.md#third-party-enhancements-and-tools)**

**Important:** Allow additional time for post-migration schema object setup. Aurora databases with many indexes or complex constraints may require several hours for complete schema migration.

## Support and Resources

> **[PlanetScale migration scripts](https://github.com/planetscale/migration-scripts)**

> **[AWS Documentation](https://docs.aws.amazon.com/cloudformation/)**

> **[PlanetScale Support](https://planetscale.com/contact?initial=support)**

For simpler migrations, consider [pg\_dump/restore](https://planetscale.com/docs/postgres/imports/postgres-migrate-dumprestore.md), or [logical replication](https://planetscale.com/docs/postgres/imports/postgres-migrate-walstream.md) methods.

**Post-Migration Success Checklist:**

* All schema objects migrated and validated
* Sequence values synchronized with Aurora
* Application performance matches pre-migration levels
* All critical application features tested
* Constraints and foreign keys working correctly
* No application errors in logs for 24+ hours
* Query performance baseline established

**Migration Timeline Expectations with Optimized Template:**

* Schema setup: 30 minutes - 2 hours (depending on complexity)
* DMS full load: Improved by \~25-40% due to batch processing optimizations
  * Small databases (under 10GB): 30 minutes - 2 hours
  * Medium databases (10-100GB): 2-6 hours
  * Large databases (100GB+): 4-12 hours
* Sequence synchronization: 5-15 minutes
* Validation and cutover: 30-60 minutes
* Total downtime for cutover: 5-30 minutes

**Performance Improvements:**

* Batch apply processing reduces target database load
* Enhanced memory management improves large table handling
* Row-level validation catches issues early without stopping migration

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
