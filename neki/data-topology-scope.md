---
url: https://planetscale.com/docs/neki/data-topology-scope
title: "Data Topology Scope"
description: ""
access_date: 2026-11-19T21:15:20.896Z
current_date: 2026-11-19T21:15:20.896Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Data topology scope

> The relationship between Neki topology, shards, and logical objects in Postgres

The data topology controls how Neki routes queries and distributes data onto the
underlying resources.

The data topology document refers to Postgres objects (such as databases, schemas, tables, and sequences),
but it doesn't define their structure. You create those objects using regular
Data Definition Language (DDL) statements, independently of naming them in your topology.
This makes the topology language flexible and extensible while retaining Postgres
compatibility for the DDL syntax.

Neki routes queries and distributes data based on the current topology. You can
[view the topology document](https://planetscale.com/docs/neki/data-topology.md#view-and-update-the-topology)
in the PlanetScale dashboard or using the `__neki.get_data_topology` metafunction.
A new cluster starts with a topology stub naming the cluster's initial shard:

```json theme={null}
{
  "shard_groups": [
    {
      "uid": "shard-a",
      "key_ranges": [
        {
          "shard_uid": "shard-a"
        }
      ]
    }
  ],
  "default_shard_group": "shard-a",
  "authoritative_shard_group": "shard-a"
}
```

In this state, the cluster appears similar to a plain, unsharded
Postgres database with everything resolving to a single shard. This is a valid topology,
and you can use Neki in a single-shard configuration without thinking about
the topology document until your workload needs to grow beyond a single shard's capacity.

## Shards, topology, and logical objects

A Neki cluster contains shards and routers, a topology definition, and the
logical database objects. The topology does not implicitly change when you add
or remove shards or when you manipulate Postgres objects.
The topology must be adjusted as necessary, using reshard workflows or the
`__neki.set_data_topology` metafunction. For example:

* **Newly added shards** don't automatically appear in the topology. Introduce them
  through a reshard workflow (to redistribute an existing shard group) or by declaring
  a new shard group that uses them.
* **To delete a shard**, first move remaining data off the shard and remove it
  from the topology. A shard can't be deleted if it's referenced in the topology document.
* **The authoritative shard group** can't be removed or migrated to a different shard,
  so the cluster's initial shard can't be deleted. Leave it as a standalone shard
  and [size it](https://planetscale.com/docs/neki/cluster-configuration.md#configuration-profiles) for catalog work,
  sequence reservations, and any unsharded tables that stay on it. Use separate shards
  and shard groups for large application tables.
* **Newly added tables** are resolved using [topology defaults](https://planetscale.com/docs/neki/data-topology.md#schema-and-database-defaults)
  but don't automatically receive dedicated topology entries.

Keep in mind that the `__neki.set_data_topology` metafunction called with the `overwrite => true`
option replaces the entire current topology document with a new one. Neki validates
the new document for syntax and certain structural issues,
but it doesn't check if the logical objects exist or if their structure
aligns with the topology, and it doesn't redistribute existing data to match the new document.

The ability to replace a topology document is useful for making additive or
subtractive changes that don't break the existing logic: removing parts of the document
you no longer need, declaring routing rules for new tables, or setting up the
topology ahead of creating Postgres objects on a new cluster. To move existing
data around, use a [reshard workflow](https://planetscale.com/docs/neki/data-migration.md#reshard-existing-tables).

### Creating and removing logical objects

Neki doesn't prevent you from creating or removing logical Postgres objects of
your own accord. These operations fall into three main categories:

1. Changes that need a topology adjustment, such as creating a new reference
   table or a sharded table that overrides the default shard index.
2. Changes that are covered by the existing topology without adjustment,
   such as creating a sharded table that follows existing topology defaults.
3. Changes that don't involve Neki topology at all, such as creating a foreign key
   constraint or changing the data type for a secondary column.

As a best practice, if an object creation or removal also requires a topology
change, adjust the topology before manipulating the object.
For example, if you plan to add a table that requires a new topology entry
(routing rule, index override, etc.), updating the topology before creating the
table eliminates the risk of traffic reaching the table through topology defaults
before its routing is customized.

For objects that the topology document doesn't deal with, Neki maintains them on every
shard, but they're used independently by each shard as if it were a separate
Postgres instance. For example, a statement creating a foreign key constraint
fans out to all shards but is enforced locally and separately by each shard.

## Familiar terms

Some terms familiar from Postgres have a different meaning in Neki.

### Shard index

A shard index turns values from one or more table columns into routing values.
Neki compares those routing values with a shard group's key ranges to choose a destination shard.

Naming a column in the shard index doesn't create a Postgres index on that column
and doesn't require that column to be indexed. An index can still be necessary for optimal performance.

### Primary index

A primary index is a per-table override of the shard index that the table is routed by.
Without an override, the table is routed by its shard group's default shard index.
The per-table override is useful when different sharded tables contain columns that are named differently but contain the same logical data.

For example, a data model for an event booking application might contain multiple tables that will be sharded by venue:

```sql theme={null}
CREATE TABLE public.venues (
    id  bigint PRIMARY KEY
);

CREATE TABLE public.events (
    id        bigint PRIMARY KEY,
    venue_id  bigint NOT NULL -- Refers to public.venues.id
);

CREATE TABLE public.rooms (
    id        bigint PRIMARY KEY,
    venue_id  bigint NOT NULL -- Refers to public.venues.id
);
```

Columns storing the venue ID contain the same data but are named differently. It's a
good practice to use defaults for common properties and overrides for exceptions, so
in this example the topology could declare a default shard index on `venue_id`
and an override for `public.venues` to use the `id` column.

A primary index declared in a Neki topology is distinct
and separate from a primary index used as a constraint on the underlying Postgres table.
The column named in a topology override doesn't have to be a `PRIMARY KEY`
or a `UNIQUE NOT NULL` constraint at the Postgres level.

### Global secondary index

A global secondary index, or GSI, uses a lookup table to map another key to the shard key of an owner row.
Neki can read this mapping before routing a query to the owner table.

Declaring a GSI in the topology document does not create a single cluster-wide
Postgres index object or a unique constraint enforced across all shards.
Indexes and constraints are maintained and enforced independently on each shard,
but it's possible to implement global uniqueness using one of two techniques:
an unsharded GSI (lookup table) or a composite index prefixed with a shard key.

For the complete list of terms, see [Neki terminology](https://planetscale.com/docs/neki/terminology.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
