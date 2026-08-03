---
url: https://planetscale.com/docs/postgres/imports/postgres-migrate-dms
title: "Postgres Migrate Dms"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

Before beginning your migration, we recommend running our [migration assessment tool](https://planetscale.com/liftoff) for instant feedback on migration complexity, potential blockers, and the recommended migration path.

[Amazon Database Migration Service (DMS)](https://aws.amazon.com/dms/) provides a managed migration service that can handle complex database migrations with built-in monitoring, error handling, and data validation. This method is ideal for large, complex databases that require robust migration capabilities.

Want expert guidance for your migration? PlanetScale’s [migration services](https://planetscale.com/migrate) are available to help you plan and execute a smooth, successful move.

## Overview

This migration method involves:

**Critical: AWS DMS Schema Object Limitations**

AWS DMS **only migrates table data and primary keys**. All other PostgreSQL schema objects must be handled separately:

- Secondary indexes
- Sequences and their current values
- Views, functions, and stored procedures
- Constraints (foreign keys, unique, check)
- Triggers and custom data types

Deploy your complete schema to PlanetScale BEFORE starting DMS migration to preserve performance and avoid application errors.

This method requires an AWS account and will incur AWS DMS charges. Review [AWS DMS pricing](https://aws.amazon.com/dms/pricing/) before proceeding.

**For Aurora users**: Consider the [Aurora to PlanetScale CloudFormation & DMS tutorial](aurora-dms.md) for a fully automated approach using CloudFormation templates and Step Functions workflows instead of manual DMS setup.

## Prerequisites

Before starting the migration:

- Active AWS account with appropriate DMS permissions
- Source PostgreSQL database accessible from AWS (consider VPC configuration)
- Connection details for your PlanetScale Postgres database from the console
- Ensure the disk on your PlanetScale database has at least 150% of the capacity of your source database. If you are migrating to a PlanetScale database backed by network-attached storage, you can [resize](../cluster-configuration/cluster-storage.md) your disk manually by setting the “Minimum disk size.” If you are using Metal, you will need to select a size when first creating your database. For example, if your source database is 330GB, you should have at least 500GB of storage available on PlanetScale.
- Understanding of your data transformation requirements (if any)
- Network connectivity between AWS and both source and target databases

## Step 1: Pre-Migration Schema Setup

Deploy your complete schema to PlanetScale BEFORE starting DMS migration. This ensures optimal performance and prevents application errors.

### Extract and Apply Schema

**Foreign Key Constraints**

If the schema application fails due to foreign key constraint issues, you can temporarily remove them from the schema file and apply them after DMS completes the data migration.

### Verify Schema Application

Quickly verify your schema was applied successfully:

```sql
-- Check that tables and sequences exist
SELECT
    (SELECT COUNT(*) FROM information_schema.tables WHERE table_schema = 'public') as tables,
    (SELECT COUNT(*) FROM information_schema.sequences WHERE sequence_schema = 'public') as sequences,
    (SELECT COUNT(*) FROM pg_indexes WHERE schemaname = 'public') as indexes;
```

## Step 2: Set Up AWS DMS

### Create DMS Replication Instance

### Configure Security Groups

Ensure your replication instance can connect to:

- Source PostgreSQL database (port 5432)
- PlanetScale Postgres (port 5432)
- Internet for PlanetScale connectivity

## Step 3: Create Source Endpoint

### Configure PostgreSQL source endpoint:

### Advanced settings for PostgreSQL:

```yaml
Extra connection attributes:
pluginName=test_decoding;
slotName=dms_slot_planetscale;
captureDDLs=false;
maxFileSize=32768;
```

## Step 4: Create Target Endpoint

### Configure PlanetScale Postgres target endpoint:

### SSL Configuration:

```yaml
SSL mode: require
```

## Step 5: Test Endpoints

## Step 6: Create Migration Task

### Configure the migration task:

### Task Settings

**Option 1: Schema-first approach** (recommended for production):

```json
{
  "TargetMetadata": {
    "TargetSchema": "",
    "SupportLobs": true,
    "FullLobMode": true,
    "LobChunkSize": 32,
    "LimitedSizeLobMode": false,
    "LobMaxSize": 0,
    "InlineLobMaxSize": 32,
    "BatchApplyEnabled": true,
    "TaskRecoveryTableEnabled": false
  },
  "FullLoadSettings": {
    "TargetTablePrepMode": "DO_NOTHING",
    "CreatePkAfterFullLoad": false,
    "StopTaskCachedChangesApplied": false,
    "MaxFullLoadSubTasks": 8,
    "TransactionConsistencyTimeout": 600,
    "CommitRate": 10000,
    "FullLoadIgnoreConflicts": true
  },
  "ValidationSettings": {
    "EnableValidation": true,
    "ValidationMode": "ROW_LEVEL",
    "ThreadCount": 5,
    "FailureMaxCount": 10000,
    "TableFailureMaxCount": 1000
  },
  "ChangeProcessingTuning": {
    "StatementCacheSize": 50,
    "CommitTimeout": 5,
    "BatchApplyPreserveTransaction": true,
    "BatchApplyTimeoutMin": 1,
    "BatchApplyTimeoutMax": 30,
    "MinTransactionSize": 5000,
    "MemoryKeepTime": 60,
    "BatchApplyMemoryLimit": 1000,
    "MemoryLimitTotal": 2048
  },
  "Logging": {
    "EnableLogging": true,
    "LogComponents": [
      {
        "Id": "TRANSFORMATION",
        "Severity": "LOGGER_SEVERITY_DEFAULT"
      },
      {
        "Id": "SOURCE_UNLOAD",
        "Severity": "LOGGER_SEVERITY_DEFAULT"
      },
      {
        "Id": "TARGET_LOAD",
        "Severity": "LOGGER_SEVERITY_DEFAULT"
      },
      {
        "Id": "SOURCE_CAPTURE",
        "Severity": "LOGGER_SEVERITY_DEFAULT"
      },
      {
        "Id": "TARGET_APPLY",
        "Severity": "LOGGER_SEVERITY_DEFAULT"
      }
    ]
  },
  "ControlTablesSettings": {
    "historyTimeslotInMinutes": 5,
    "ControlSchema": "",
    "HistoryTableEnabled": false,
    "SuspendedTablesTableEnabled": false,
    "StatusTableEnabled": false,
    "FullLoadExceptionTableEnabled": false
  }
}
```

**Option 2: Standard approach** (matches CloudFormation template):

```json
{
  "TargetMetadata": {
    "SupportLobs": true,
    "FullLobMode": true,
    "LobChunkSize": 32,
    "BatchApplyEnabled": true,
    "TaskRecoveryTableEnabled": false
  },
  "FullLoadSettings": {
    "TargetTablePrepMode": "DROP_AND_CREATE",
    "CreatePkAfterFullLoad": false,
    "MaxFullLoadSubTasks": 8,
    "TransactionConsistencyTimeout": 600,
    "CommitRate": 10000,
    "FullLoadIgnoreConflicts": true
  },
  "ValidationSettings": {
    "EnableValidation": true,
    "ValidationMode": "ROW_LEVEL",
    "ThreadCount": 5,
    "FailureMaxCount": 10000,
    "TableFailureMaxCount": 1000
  },
  "Logging": {
    "EnableLogging": true,
    "LogComponents": [
      {
        "Id": "SOURCE_UNLOAD",
        "Severity": "LOGGER_SEVERITY_DEFAULT"
      },
      {
        "Id": "SOURCE_CAPTURE",
        "Severity": "LOGGER_SEVERITY_DEFAULT"
      },
      {
        "Id": "TARGET_LOAD",
        "Severity": "LOGGER_SEVERITY_DEFAULT"
      },
      {
        "Id": "TARGET_APPLY",
        "Severity": "LOGGER_SEVERITY_DEFAULT"
      },
      {
        "Id": "TASK_MANAGER",
        "Severity": "LOGGER_SEVERITY_DEFAULT"
      }
    ]
  }
}
```

**Configuration Comparison:**

| Setting | Schema-First | Standard | Notes |
| --- | --- | --- | --- |
| TargetTablePrepMode | DO\_NOTHING | DROP\_AND\_CREATE | Schema-first uses existing schema |
| ChangeProcessingTuning | Included | Not needed | Extra optimization for manual setup |
| Logging Components | 5 components | 5 components | Both include all DMS components |
| ValidationSettings | Same | Same | Both use row-level validation |

**When to use each approach:**

- **Schema-First**: Production systems, complex schemas, performance-critical applications
- **Standard**: Simple migrations, dev/test environments, when schema objects aren’t critical during migration

## Step 7: Configure Table Mappings

### Basic table mapping (migrate all tables):

```json
{
  "rules": [
    {
      "rule-type": "selection",
      "rule-id": "1",
      "rule-name": "1",
      "object-locator": {
        "schema-name": "public",
        "table-name": "%"
      },
      "rule-action": "include",
      "filters": []
    }
  ]
}
```

### Advanced table mapping with transformations:

```json
{
  "rules": [
    {
      "rule-type": "selection",
      "rule-id": "1",
      "rule-name": "1",
      "object-locator": {
        "schema-name": "public",
        "table-name": "%"
      },
      "rule-action": "include"
    },
    {
      "rule-type": "transformation",
      "rule-id": "2",
      "rule-name": "2",
      "rule-target": "schema",
      "object-locator": {
        "schema-name": "public"
      },
      "rule-action": "rename",
      "value": "public"
    }
  ]
}
```

## Step 8: Start Migration Task

## Step 9: Monitor Migration Progress

### Key metrics to monitor:

- **Full load progress**: Percentage of tables loaded
- **CDC lag**: Latency between source and target
- **Error count**: Any migration errors
- **Throughput**: Records per second

### Using CloudWatch:

Set up [CloudWatch alarms](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Monitoring.html) for:

- High CDC latency
- Migration errors
- Task failures

```shellscript
# CLI command to check task status
aws dms describe-replication-tasks \
    --filters Name=replication-task-id,Values=your-task-id
```

## Step 10: Verify Data Migration

### Check table counts and data integrity:

```sql
-- Run on both source and target databases
SELECT
    schemaname,
    tablename,
    n_tup_ins as estimated_rows,
    n_tup_upd as updated_rows,
    n_tup_del as deleted_rows
FROM pg_stat_user_tables
ORDER BY schemaname, tablename;
```

### Validate specific data:

```sql
-- Compare checksums for critical tables
SELECT count(*), md5(string_agg(column_name::text, ''))
FROM your_important_table
ORDER BY primary_key;
```

## Step 11: Prepare for Cutover

### Monitor CDC lag:

Ensure CDC latency is minimal (under 5 seconds) before cutover:

```sql
-- Check DMS validation status
SELECT * FROM awsdms_validation_failures_v1;
```

### Test application connectivity:

1. Create a read-only connection to PlanetScale Postgres
2. Test critical application queries with EXPLAIN ANALYZE
3. Verify performance matches expectations (indexes should be working)
4. Test sequence-dependent operations (INSERT operations)

## Step 12: Post-Migration Sequence Synchronization

After DMS completes, sequences need their values synchronized:

**Critical: Sequence Synchronization**

Sequence values must be set ahead of source database values to prevent duplicate key errors when applications start using PlanetScale.

### Get Current Sequence Values from Source

```sql
-- Run on source database to get all current sequence values
SELECT
    sequence_name,
    last_value,
    'SELECT setval(''' || sequence_name || ''', ' || (last_value + 1000) || ');' as update_command
FROM information_schema.sequences
WHERE sequence_schema = 'public'
ORDER BY sequence_name;
```

### Update Sequences in PlanetScale

```sql
-- For each sequence, run the update command from above
-- Example commands (values set ahead of source):
SELECT setval('users_id_seq', 16234);  -- Source value + 1000
SELECT setval('orders_id_seq', 99765);  -- Source value + 1000
SELECT setval('products_id_seq', 6432);  -- Source value + 1000

-- Verify sequence values are ahead of source
SELECT sequence_name, last_value
FROM information_schema.sequences
WHERE sequence_schema = 'public'
ORDER BY sequence_name;
```

### Apply Remaining Constraints

Now apply foreign key constraints that were deferred:

```sql
-- Apply foreign key constraints
\i constraints.sql

-- Verify constraints were applied successfully
SELECT conname, contype, conrelid::regclass AS table_name
FROM pg_constraint
WHERE connamespace = 'public'::regnamespace
  AND contype = 'f'  -- foreign key constraints
ORDER BY conrelid::regclass::text;
```

## Step 13: Comprehensive Pre-Cutover Validation

**Complete Validation Required**

Validate ALL schema objects and data integrity before cutover. Missing objects will cause application failures.

```sql
-- Validate table row counts match source
SELECT
    schemaname,
    tablename,
    n_tup_ins as estimated_rows
FROM pg_stat_user_tables
WHERE schemaname = 'public'
ORDER BY tablename;
```

## Step 14: Perform Cutover

When ready to switch to PlanetScale Postgres:

### Stop the migration task:

```shellscript
aws dms stop-replication-task \
    --replication-task-arn arn:aws:dms:region:account:task:task-id
```

## Step 15: Cleanup

The task configuration above is already optimized for schema-first migrations. Key settings:

- **DO\_NOTHING** prep mode preserves your existing schema
- **Row-level validation** ensures data integrity
- **Batch processing** optimizations improve performance
- **Memory tuning** handles large datasets efficiently

**Automated vs Manual Configuration**

For Aurora migrations, consider using the [automated CloudFormation approach](aurora-dms.md) which includes these optimized settings and additional automation features.

After successful cutover and schema migration:

```shellscript
# Cleanup commands
aws dms delete-replication-task --replication-task-arn your-task-arn
aws dms delete-replication-instance --replication-instance-arn your-instance-arn
aws dms delete-endpoint --endpoint-arn your-source-endpoint-arn
aws dms delete-endpoint --endpoint-arn your-target-endpoint-arn
```

## Troubleshooting

### Common Issues:

**Connectivity problems:**

- Check security groups and network ACLs
- Verify endpoint configurations
- Test network connectivity from replication instance

**Performance issues:**

- Increase replication instance size
- Adjust parallel load settings
- Monitor source database performance

**Data type mapping issues:**

- Review [DMS data type mapping](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Reference.DataTypes.html)
- Use transformation rules for custom mappings

**Large object (LOB) handling:**

```json
{
  "TargetMetadata": {
    "SupportLobs": true,
    "FullLobMode": true,
    "LobChunkSize": 32768,
    "LimitedSizeLobMode": false
  }
}
```

### Schema-Related Troubleshooting:

**“sequence does not exist” errors:**

```sql
-- Check if sequence exists
SELECT * FROM information_schema.sequences WHERE sequence_name = 'your_sequence';

-- Recreate missing sequence
CREATE SEQUENCE your_sequence START WITH 1;
SELECT setval('your_sequence', (SELECT MAX(id) FROM your_table));
```

**Missing indexes causing performance issues:**

```sql
-- Find missing indexes by comparing to source
-- Run on source database to get index list
SELECT indexname, indexdef FROM pg_indexes WHERE schemaname = 'public';

-- Check query performance
EXPLAIN (ANALYZE, BUFFERS) SELECT * FROM your_table WHERE indexed_column = 'value';
```

**Foreign key constraint violations:**

```sql
-- Check for constraint violations before applying
SELECT COUNT(*) FROM child_table c
WHERE NOT EXISTS (SELECT 1 FROM parent_table p WHERE p.id = c.parent_id);

-- Apply constraints one by one to isolate issues
ALTER TABLE child_table ADD CONSTRAINT fk_name FOREIGN KEY (parent_id) REFERENCES parent_table(id);
```

**Functions/views with dependency errors:**

```sql
-- Check dependencies
SELECT * FROM pg_depend WHERE objid = 'your_function'::regproc;

-- Apply in dependency order: functions before views that use them
```

**Permission errors during schema application:**

- Ensure PlanetScale database user has CREATE permissions
- Check if objects already exist and need DROP statements
- Verify user has permissions on referenced objects

**Sequence values too low causing duplicate key errors:**

```sql
-- Check current sequence value vs max table value
SELECT last_value FROM your_sequence;
SELECT MAX(id) FROM your_table;

-- Update sequence to safe value
SELECT setval('your_sequence', (SELECT MAX(id) FROM your_table));
```

### Performance Optimization:

1. **Parallel loading**: Increase `MaxFullLoadSubTasks`
2. **Batch apply**: Enable for better target performance
3. **Memory allocation**: Increase replication instance size
4. **Network optimization**: Use placement groups for better network performance

## Cost Optimization

- **Instance sizing**: Start with smaller instances and scale up if needed
- **Multi-AZ**: Disable for dev/test migrations
- **Task lifecycle**: Delete resources immediately after successful migration
- **Data transfer**: Consider AWS region placement to minimize transfer costs

## Schema Considerations

Before migration, review:

## PostgreSQL version compatibility

## Extension support limitations

## Third-party enhancement restrictions

**Important:** Plan additional time for post-migration schema object setup. Complex databases may require several hours for index recreation and sequence synchronization.

**Performance Impact Note:** Large indexes can take hours to rebuild on populated tables. Consider the schema-first approach to avoid this performance penalty.

## Next Steps

After successful migration and schema setup:

**Success Criteria:**

- ✅ All schema objects validated and functional
- ✅ Sequence values synchronized and tested
- ✅ Query performance matches or exceeds source database
- ✅ No application errors in logs for 24+ hours
- ✅ All foreign key constraints working correctly

For simpler migrations, consider [pg\_dump/restore](postgres-migrate-dumprestore.md) or [WAL streaming](postgres-migrate-walstream.md) methods.

If you encounter issues during migration, please [reach out to support](https://planetscale.com/contact?initial=support) for assistance.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
