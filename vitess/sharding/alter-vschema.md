---
url: https://planetscale.com/docs/vitess/sharding/alter-vschema
title: "Alter Vschema"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# ALTER VSCHEMA syntax reference

> A complete reference for the ALTER VSCHEMA statements you can use to modify the VSchema of a keyspace.

export const PlatformAvailability = ({current, vitess, postgres}) => {
  const docsHref = path => {
    if (!path) return path;
    const normalized = path.startsWith('/') ? path : `/${path}`;
    return normalized;
  };
  const labels = {
    vitess: 'Vitess',
    postgres: 'Postgres'
  };
  if (current === 'both') {
    return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
        <span data-engine="both" data-state="current" aria-current="true" className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
          Vitess and Postgres
        </span>
      </div>;
  }
  const hasVitess = current === 'vitess' || Boolean(vitess);
  const hasPostgres = current === 'postgres' || Boolean(postgres);
  const only = !(hasVitess && hasPostgres);
  const engines = [];
  if (current === 'vitess' || current === 'postgres') engines.push(current);
  if (hasVitess && current !== 'vitess') engines.push('vitess');
  if (hasPostgres && current !== 'postgres') engines.push('postgres');
  return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
      {engines.map(engine => {
    const isCurrent = current === engine;
    const href = docsHref(engine === 'vitess' ? vitess : postgres);
    const label = only ? `${labels[engine]} only` : labels[engine];
    const state = isCurrent || !href ? 'current' : 'link';
    if (isCurrent || !href) {
      return <span key={engine} data-engine={engine} data-state={state} aria-current={isCurrent ? 'true' : undefined} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
              {label}
            </span>;
    }
    return <a key={engine} href={href} data-engine={engine} data-state={state} title={`View ${labels[engine]} documentation`} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
            {label}
            <svg aria-hidden="true" width="12" height="12" viewBox="0 0 12 12" fill="none" className="shrink-0">
              <path d="M2.5 6h7M6.5 3l3 3-3 3" stroke="currentColor" strokeWidth="1.5" strokeLinecap="round" strokeLinejoin="round" />
            </svg>
          </a>;
  })}
    </div>;
};

<PlatformAvailability current="vitess" />

The [VSchema](vschema.md) describes how your data is organized across keyspaces and shards: which tables exist in each keyspace, how sharded tables map rows to shards via [Vindexes](vindexes.md), and how IDs are generated with [sequence tables](sequence-tables.md).

`ALTER VSCHEMA` statements let you modify the VSchema using SQL over a regular MySQL connection to your database branch, as an alternative to editing the VSchema JSON directly in the [Clusters page](../cluster-configuration.md) or with the [pscale CLI](../../cli/keyspace.md).

<Warning>
  `ALTER VSCHEMA` statements do not work on a branch with [safe migrations](../schema-changes/safe-migrations.md) enabled, which is the recommended setup for production branches.
  Instead, run them on a [development branch](../schema-changes/branching.md) and promote the changes to production with a [deploy request](../schema-changes/deploy-requests.md).
  See [Modifying VSchema](vschema.md#modifying-vschema) for the full workflow.
</Warning>

<Note>
  `ALTER VSCHEMA` only changes Vitess routing metadata.
  It never creates, alters, or drops the underlying MySQL tables — you manage those with regular DDL.
</Note>

## Statement overview

| Statement                                                                 | Purpose                                           |
| ------------------------------------------------------------------------- | ------------------------------------------------- |
| [`ALTER VSCHEMA ADD TABLE`](#add-a-table)                                 | Add a table to an unsharded keyspace's VSchema    |
| [`ALTER VSCHEMA DROP TABLE`](#drop-a-table)                               | Remove a table from a keyspace's VSchema          |
| [`ALTER VSCHEMA ADD SEQUENCE`](#add-a-sequence)                           | Register a table as a sequence table              |
| [`ALTER VSCHEMA DROP SEQUENCE`](#drop-a-sequence)                         | Remove a sequence table from the VSchema          |
| [`ALTER VSCHEMA ... ADD AUTO_INCREMENT`](#add-an-auto-increment-column)   | Back a column with a sequence table               |
| [`ALTER VSCHEMA ... DROP AUTO_INCREMENT`](#drop-an-auto-increment-column) | Remove the sequence backing from a table          |
| [`ALTER VSCHEMA CREATE VINDEX`](#create-a-vindex)                         | Define a Vindex in a keyspace                     |
| [`ALTER VSCHEMA DROP VINDEX`](#drop-a-vindex)                             | Remove a Vindex definition from a keyspace        |
| [`ALTER VSCHEMA ... ADD VINDEX`](#add-a-vindex-to-a-table)                | Attach a Vindex to one or more columns of a table |
| [`ALTER VSCHEMA ... DROP VINDEX`](#drop-a-vindex-from-a-table)            | Detach a Vindex from a table                      |

## How keyspaces are resolved

Every `ALTER VSCHEMA` statement operates on exactly one keyspace.
Vitess determines which one as follows:

1. If the table name is qualified (`keyspace.table`), the qualifier wins.
2. Otherwise, the keyspace you are currently connected to (or selected with `USE`) is used.
3. If neither is available, the statement fails with an error.

```sql theme={null}
-- Explicit keyspace qualifier
ALTER VSCHEMA ADD TABLE product.product;

-- Uses the current keyspace
USE product;
ALTER VSCHEMA ADD TABLE product;
```

<Note>
  Keyspace, table, and column names that contain special characters (such as hyphens) or collide with reserved words (such as `binary`) must be quoted with backticks, just like in regular MySQL statements:

  ```sql theme={null}
  ALTER VSCHEMA ON `my-sharded-keyspace`.users ADD VINDEX shard_index(id) USING xxhash;
  ```
</Note>

## Managing tables

### Add a table

```sql theme={null}
ALTER VSCHEMA ADD TABLE [<keyspace>.]<table_name>;
```

Adds a table to the VSchema of an **unsharded** keyspace.
In an unsharded keyspace, tables carry no Vindex information, so the VSchema entry is simply the table name.

```sql theme={null}
-- Example
ALTER VSCHEMA ADD TABLE product.product;
```

This statement fails if:

* The keyspace is sharded. In a sharded keyspace, every table needs a primary Vindex, so use [`ALTER VSCHEMA ... ADD VINDEX`](#add-a-vindex-to-a-table) instead — it creates the table entry for you.
* The table is already present in the VSchema.

### Drop a table

```sql theme={null}
ALTER VSCHEMA DROP TABLE [<keyspace>.]<table_name>;
```

Removes a table from the keyspace's VSchema.
Unlike `ADD TABLE`, this works on both sharded and unsharded keyspaces.
The underlying MySQL table is not touched.

```sql theme={null}
-- Example
ALTER VSCHEMA DROP TABLE product.product;
```

This statement fails if the table is not present in the VSchema.

## Managing sequences

[Sequence tables](sequence-tables.md) provide auto-increment-style ID generation for sharded tables.
The sequence table itself must live in an **unsharded** keyspace.

### Add a sequence

```sql theme={null}
ALTER VSCHEMA ADD SEQUENCE [<keyspace>.]<sequence_table_name>;
```

Registers a table in an unsharded keyspace as a sequence table (the VSchema entry gets `"type": "sequence"`).

```sql theme={null}
-- Example
ALTER VSCHEMA ADD SEQUENCE product.customer_seq;
```

The backing MySQL table must be created separately, with the required schema and the `vitess_sequence` comment, and seeded with a single row:

```sql theme={null}
CREATE TABLE customer_seq (
  id BIGINT,
  next_id BIGINT,
  cache BIGINT,
  PRIMARY KEY (id)
) COMMENT 'vitess_sequence';

INSERT INTO customer_seq (id, next_id, cache) VALUES (0, 1, 1000);
```

This statement fails if:

* The keyspace is sharded.
* A table with that name is already present in the VSchema.

### Drop a sequence

```sql theme={null}
ALTER VSCHEMA DROP SEQUENCE [<keyspace>.]<sequence_table_name>;
```

Removes a sequence table from the keyspace's VSchema.
The backing MySQL table is not touched.

```sql theme={null}
-- Example
ALTER VSCHEMA DROP SEQUENCE product.customer_seq;
```

This statement fails if:

* The keyspace is sharded.
* The sequence is not present in the VSchema.

## Managing auto-increment columns

### Add an auto-increment column

```sql theme={null}
ALTER VSCHEMA ON [<keyspace>.]<table_name>
  ADD AUTO_INCREMENT <column_name> USING [<keyspace>.]<sequence_table_name>;
```

Configures a column to be populated from a [sequence table](sequence-tables.md) whenever an `INSERT` does not provide a value for it — typically the primary key of a sharded table.
The sequence table is usually in a different (unsharded) keyspace, in which case it must be qualified.

```sql theme={null}
-- Example
ALTER VSCHEMA ON customer.customer ADD AUTO_INCREMENT customer_id USING product.customer_seq;
```

This statement fails if:

* The table is not present in the keyspace's VSchema. Add its primary Vindex first with [`ALTER VSCHEMA ... ADD VINDEX`](#add-a-vindex-to-a-table).
* The table already has an auto-increment column configured. A table can only have one; drop the existing one first.

### Drop an auto-increment column

```sql theme={null}
ALTER VSCHEMA ON [<keyspace>.]<table_name> DROP AUTO_INCREMENT;
```

Removes the sequence backing from a table.
Inserts will no longer pull values from the sequence table.

```sql theme={null}
-- Example
ALTER VSCHEMA ON customer.customer DROP AUTO_INCREMENT;
```

This statement fails if:

* The table is not present in the keyspace's VSchema.
* The table has no auto-increment column configured.

## Managing Vindexes

A [Vindex](vindexes.md) definition lives at the keyspace level and consists of a name, a type, and optional parameters.
Tables then reference a Vindex by name, binding it to one or more of their columns.
The first Vindex on a table is its **primary Vindex** — the one that decides which shard each row lives on.

You will usually use [`ALTER VSCHEMA ... ADD VINDEX`](#add-a-vindex-to-a-table), which defines the Vindex and attaches it to a table in one statement.
`CREATE VINDEX` exists for defining a Vindex ahead of time, without attaching it to any table.

### Create a Vindex

```sql theme={null}
ALTER VSCHEMA CREATE VINDEX [<keyspace>.]<vindex_name> USING <vindex_type>
  [WITH <param1>=<value1>, <param2>=<value2>, ...];
```

Defines a Vindex in the keyspace without attaching it to a table.

`<vindex_type>` is one of the [predefined Vindex types](https://vitess.io/docs/reference/features/vindexes/#predefined-vindexes), such as `xxhash`, `hash`, `unicode_loose_xxhash`, `binary`, `numeric`, `consistent_lookup`, or `consistent_lookup_unique`.

```sql theme={null}
-- Example
ALTER VSCHEMA CREATE VINDEX customer.shard_index USING xxhash;
```

This statement fails if a Vindex with that name already exists in the keyspace.

<Warning>
  Defining a Vindex is what makes a keyspace sharded: if this is the first Vindex in the keyspace, Vitess flips the keyspace to sharded in its VSchema.
  There is no such thing as a Vindex on an unsharded keyspace, so only run Vindex statements against keyspaces that are meant to be sharded.
</Warning>

### Drop a Vindex

```sql theme={null}
ALTER VSCHEMA DROP VINDEX [<keyspace>.]<vindex_name>;
```

Removes a Vindex definition from the keyspace.

```sql theme={null}
-- Example
ALTER VSCHEMA DROP VINDEX customer.shard_index;
```

This statement fails if:

* The Vindex does not exist in the keyspace.
* Any table still uses the Vindex. Detach it from every table first with [`ALTER VSCHEMA ... DROP VINDEX`](#drop-a-vindex-from-a-table).

### Add a Vindex to a table

```sql theme={null}
ALTER VSCHEMA ON [<keyspace>.]<table_name>
  ADD VINDEX <vindex_name> (<column_name1> [, <column_name2> ...])
  [USING <vindex_type> [WITH <param1>=<value1>, <param2>=<value2>, ...]];
```

Attaches a Vindex to one or more columns of a table.
This is the statement that makes a table sharded: the first Vindex added to a table becomes its **primary Vindex**.
If the table is not yet in the VSchema, the entry is created automatically.

The `USING` clause is optional and controls whether the Vindex definition is created along the way:

* **With `USING`**: if no Vindex named `vindex_name` exists in the keyspace, it is created with the given type and parameters. If it already exists, the type, owner, and parameters must match the existing definition exactly.
* **Without `USING`**: the Vindex named `vindex_name` must already exist in the keyspace (created earlier via `CREATE VINDEX` or a previous `ADD VINDEX ... USING`).

```sql theme={null}
-- Add a primary Vindex (creates both the Vindex and the table entry)
ALTER VSCHEMA ON customer.customer ADD VINDEX shard_index(customer_id) USING xxhash;

-- Reuse an existing Vindex on another table (no USING clause)
ALTER VSCHEMA ON customer.corder ADD VINDEX shard_index(customer_id);

-- A multi-column lookup Vindex, defined inline (note the backtick-quoted
-- comma-separated column list in the from parameter)
ALTER VSCHEMA ON customer.corder ADD VINDEX corder_region_idx(region, corder_id)
  USING consistent_lookup
  WITH owner=corder, table=`product.corder_region_idx`, from=`region,corder_id`, to=`keyspace_id`;
```

This statement fails if:

* A Vindex with the same name is already attached to the table.
* `USING` is given but the existing Vindex definition has a different type, owner, or parameters.
* `USING` is omitted and no Vindex with that name exists in the keyspace.

<Warning>
  The order in which Vindexes are attached matters: the first one is the primary Vindex.
  There is no statement to reorder Vindexes on a table, so add the primary Vindex first.
  If you get the order wrong, detach the table's Vindexes with [`ALTER VSCHEMA ... DROP VINDEX`](#drop-a-vindex-from-a-table) and re-add them, primary first.
</Warning>

<Warning>
  Like `CREATE VINDEX`, adding the first Vindex to any table in a keyspace marks that keyspace as sharded in its VSchema.
  Most keyspaces become sharded through this statement rather than through a standalone `CREATE VINDEX`.
</Warning>

### Drop a Vindex from a table

```sql theme={null}
ALTER VSCHEMA ON [<keyspace>.]<table_name> DROP VINDEX <vindex_name>;
```

Detaches a Vindex from a table.
The keyspace-level Vindex definition is kept; use [`ALTER VSCHEMA DROP VINDEX`](#drop-a-vindex) to remove it entirely.
If this was the last Vindex on the table, the table entry is removed from the VSchema as well.

```sql theme={null}
-- Example
ALTER VSCHEMA ON customer.corder DROP VINDEX corder_region_idx;
```

This statement fails if:

* The table is not present in the keyspace's VSchema.
* No Vindex with that name is attached to the table.

## Vindex parameters

The `WITH` clause passes key-value parameters to the Vindex.
Which parameters are valid depends on the Vindex type — most functional Vindexes such as `xxhash` take none, while [lookup Vindexes](https://vitess.io/docs/reference/features/vindexes/#lookup-vindex-types) require several:

| Parameter | Meaning                                                                                                                                        |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| `owner`   | The table that owns the lookup Vindex. Vitess keeps the lookup table up to date as rows are inserted, updated, and deleted in the owner table. |
| `table`   | The lookup table that stores the mapping, as `keyspace.table`.                                                                                 |
| `from`    | Comma-separated list of column(s) in the lookup table that hold the input value(s).                                                            |
| `to`      | The column in the lookup table that holds the keyspace ID.                                                                                     |

Only parameter values that contain special characters need backticks — such as the dot in a qualified `table` reference or the comma in a multi-column `from` list. Plain values like `owner=corder` are left unquoted:

```sql theme={null}
ALTER VSCHEMA ON customer.corder ADD VINDEX corder_keyspace_idx(corder_id)
  USING consistent_lookup_unique
  WITH owner=corder, table=`product.corder_keyspace_idx`, from=corder_id, to=keyspace_id;
```

See the [Vitess Vindex documentation](https://vitess.io/docs/reference/features/vindexes/) for the full list of Vindex types and their parameters.

## A complete example

This example shards a `customer` keyspace while keeping reference data and sequence tables in an unsharded `product` keyspace.

```sql theme={null}
-- Register a table in the unsharded keyspace
ALTER VSCHEMA ADD TABLE product.product;

-- Shard the customer table on customer_id
ALTER VSCHEMA ON customer.customer ADD VINDEX shard_index(customer_id) USING xxhash;

-- Give it sequence-backed IDs (the customer_seq MySQL table must already exist
-- and be seeded — see "Add a sequence" above for the CREATE TABLE and INSERT)
ALTER VSCHEMA ADD SEQUENCE product.customer_seq;
ALTER VSCHEMA ON customer.customer ADD AUTO_INCREMENT customer_id USING product.customer_seq;

-- Shard the corder table on customer_id too, so a customer's orders
-- live on the same shard as the customer
ALTER VSCHEMA ON customer.corder ADD VINDEX shard_index(customer_id);
ALTER VSCHEMA ADD SEQUENCE product.corder_seq;
ALTER VSCHEMA ON customer.corder ADD AUTO_INCREMENT corder_id USING product.corder_seq;

-- Add a unique lookup Vindex so corder rows can also be found by corder_id
ALTER VSCHEMA ADD TABLE product.corder_keyspace_idx;
ALTER VSCHEMA ON customer.corder ADD VINDEX corder_keyspace_idx(corder_id)
  USING consistent_lookup_unique
  WITH owner=corder, table=`product.corder_keyspace_idx`, from=corder_id, to=keyspace_id;
```

## Verifying your changes

`ALTER VSCHEMA` changes take effect immediately.
You can inspect the resulting VSchema over the same MySQL connection:

```sql theme={null}
-- List all keyspaces and whether they are sharded
SHOW VSCHEMA KEYSPACES;

-- List all tables in the current keyspace's VSchema
SHOW VSCHEMA TABLES;

-- List all Vindexes in the current keyspace
SHOW VSCHEMA VINDEXES;

-- List the Vindexes attached to a specific table
SHOW VSCHEMA VINDEXES ON customer.corder;
```

<Note>
  `SHOW VSCHEMA TABLES` and `SHOW VSCHEMA VINDEXES` (without `ON`) report on the keyspace your connection is currently using — there is no keyspace-qualifier form. Switch keyspaces with `USE <keyspace>` to inspect a different one.
</Note>

You can also view the full VSchema JSON in the [Clusters page](../cluster-configuration.md) or fetch it with the [pscale CLI](../../cli/keyspace.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
