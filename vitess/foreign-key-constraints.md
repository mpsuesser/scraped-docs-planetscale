---
url: https://planetscale.com/docs/vitess/foreign-key-constraints
title: "Foreign Key Constraints"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## What is a foreign key constraint?

A **foreign key** is a logical association of rows between two tables in a parent-child relationship. A row in a “parent” table may be referenced by one or more rows in a “child” table. A foreign key typically suggests how you should `JOIN` tables in most queries.

A **`FOREIGN KEY` *constraint*** is a database construct, an implementation that *forces* the foreign key relationship’s integrity (referential integrity). Namely, it ensures that a child table can only reference a parent table when the appropriate row *exists* in the parent table. A constraint also prevents the existence of “orphaned rows” in different methods.

### Advantages and disadvantages of foreign key constraints

Foreign key constraints have advantages and disadvantages. While foreign key constraints can help ensure referential integrity, they will cause degraded performance in high concurrency workloads and introduce more complexity in the database. Often, foreign key constraints become problematic when operating on a large scale. You can read more [why we do not recommend foreign key constraints](operating-without-foreign-key-constraints.md#why-does-planetscale-not-recommend-constraints) for some applications.

We recommend weighing the advantages and disadvantages for your specific application when using foreign key constraints. If you decide to enforce referential integrity at the application level instead of at the database level, see our documentation on [how to design systems that maintain referential integrity without foreign key constraints](strategies-for-maintaining-referential-integrity.md).

## Foreign key constraints on PlanetScale

### Prerequisites

You must enable foreign key constraint support on your database in the PlanetScale database settings.

Before you enable foreign key constraint support, there are a few important things to know:

- **No open deploy requests:** You cannot have any open deploy requests before enable foreign key constraint support.
- **Possible orphaned rows on reverts:** Some deploy requests may result in orphaned rows if you revert them. You will be warned before you deploy changes.
- **Database upgrades:** When you enable foreign key constraint support, we upgrade the MySQL version in some cases. You should experience no downtime during this upgrade, but it can take a few minutes to complete. Older databases may take longer.

### How to enable foreign key constraints

Foreign key constraints can be enabled on a **per database** level, not the organization level.

## Database imports with foreign key constraints

You can import a database with foreign key constraints to PlanetScale. We will automatically detect them after successfully connecting to your external database. If we find any foreign key constraints, we automatically enable foreign key constraint support and continue the import process.

We recommend using a replica as your source when doing database imports with foreign key constraints. Read more in the [database import documentation](imports/database-imports.md).

## Limitations

For most cases, foreign key constraints should work as expected in PlanetScale. There are a few cases to be aware of as they are currently unsupported or result in less ideal behavior.

### Sharded versus unsharded environments

Currently, the foreign key constraints are only supported in unsharded environments. If you use PlanetScale in a sharded environment, contact your PlanetScale account manager for more information.

### Deploy request limitations

#### Referential integrity of existing columns

Deploy requests do not validate the referential integrity of *existing* columns. `ALTER TABLE… ADD FOREIGN KEY…` does not validate existing row relations within the context of a deploy request. Unlike standard MySQL, it is possible to add the foreign key constraint to a table with orphaned rows, and they will remain orphaned. In standard MySQL, adding a foreign key is a blocking operation, and it fails if any orphaned rows are found.

#### Revert limitations

In some cases, a revert of a deploy request can result in orphaned rows. When you revert:

- Dropping a foreign key constraint: Once a foreign key constraint is dropped, new data written to the table is less constrained. Reverting this change may result in data that is inconsistent with the dropped foreign key constraint.
- Dropping a table with foreign key constraints: When a table with foreign key constraints is dropped, the parent table(s) will continue to be written to. If this change is reverted, data in the table that was dropped may no longer be consistent with its foreign key constraints.

#### Adaptive hash index

When a deploy request modifies a table that is referenced by foreign key child tables, MySQL obtains a metadata lock and, inside that lock, clears adaptive hash index (AHI) entries for every child table with a foreign key reference to the table being modified. Clearing AHI for each child entails a full scan of the InnoDB buffer pool.

On the primary, queries block for the duration of the scan while that metadata lock is held. The same work replays on replicas via the binlog, which can raise replication lag high enough that those replicas stop serving queries.

Risk is highest when all of the following are true:

- AHI is enabled (`innodb_adaptive_hash_index`)
- The modified table has many child tables with foreign key constraints
- The InnoDB buffer pool is large

To mitigate this risk, consider disabling AHI either permanently, or temporarily before deploying a schema change. Set `innodb_adaptive_hash_index` to `false` in [cluster configuration parameters](cluster-configuration/parameters.md). Many workloads see little or no noticeable query impact with AHI off.

### Unsupported queries

The following queries are currently unsupported when the query leads to `child` table `CASCADE` or `SET NULL`:

#### INSERT... ON DUPLICATE KEY UPDATE statement

For example:

```sql
INSERT INTO tbl (id, col) values (1, 2) ON DUPLICATE KEY UPDATE SET col = 3;
```

#### REPLACE INTO... SELECT... statement

For example:

```sql
REPLACE INTO tbl SELECT * FROM tbl2;
```

#### UPDATE for a foreign key column statement and referencing the column being updated in the same UPDATE statement

For example:

```sql
UPDATE tbl SET col = 5, fk_col = col + 1;
```

The workaround is to separate the updates for conflicting columns into separate schema changes.

#### Cyclic foreign keys

It is possible to create self-referencing tables as well as a groups of tables which compose a cyclic foreign key (where tables reference each other in a loop). There is no restriction to the schema design, but in some scenarios queries will be rejected:

- A cycle where the participating foreign keys all have `ON DELETE CASCADE` rule. Example:

```sql
CREATE TABLE \`employee\` (
    \`id\` bigint unsigned NOT NULL AUTO_INCREMENT,
    \`manager_id\` bigint unsigned NOT NULL,
    PRIMARY KEY (\`id\`),
    KEY \`idx_manager_id\` (\`manager_id\`),
    CONSTRAINT \`self_referencing_key_with_cascade\` FOREIGN KEY (\`manager_id\`) REFERENCES \`employee\` (\`id\`) ON DELETE CASCADE
);
```

- A cycle where all rules are either `SET NULL` or `CASCADE`, and the loop ends up having columns reference themselves. Example:

```sql
CREATE TABLE \`t1\` (
    \`id\` int NOT NULL,
    \`i\` int,
    PRIMARY KEY (\`id\`),
    KEY \`i_fk\` (\`i\`),
    CONSTRAINT \`i_fk\` FOREIGN KEY (\`i\`) REFERENCES \`t2\` (\`j\`) ON UPDATE CASCADE ON DELETE SET NULL
);
CREATE TABLE \`t2\` (
    \`id\` int NOT NULL,
    \`j\` int,
    PRIMARY KEY (\`id\`),
    KEY \`j_fk\` (\`j\`),
    CONSTRAINT \`j_fk\` FOREIGN KEY (\`j\`) REFERENCES \`t1\` (\`i\`) ON UPDATE SET NULL ON DELETE CASCADE
);
```

#### Foreign key constraint names change on every deployment

This is mainly due to the MySQL limitation (compatible with ANSI SQL specification), where constraint names must be unique to the schema.

For example, in your database branch, you may run the following:

```sql
create table parent (id int primary key);
create table child (id int primary key, pid int, constraint pchild_fk foreign key (pid) references parent (id));
```

But after you deploy your schema changes:

```sql
show create table child \G
CREATE TABLE \`child\` (
  \`id\` int NOT NULL,
  \`pid\` int DEFAULT NULL,
  PRIMARY KEY (\`id\`),
  KEY \`pchild_fk_5vtaqz7kepok6wa91vryrkrje\` (\`pid\`),
  CONSTRAINT \`pchild_fk_5vtaqz7kepok6wa91vryrkrje\` FOREIGN KEY (\`pid\`) REFERENCES \`parent\` (\`id\`)
)
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
