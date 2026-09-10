---
url: https://planetscale.com/docs/neki/imports/troubleshooting
title: "Troubleshooting"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Troubleshoot a Neki migration

> Resolve discovery, topology-planning, data-movement, validation, and cutover problems.

Preserve the exact failing command, Postgres error, object name, and restore
log before retrying. A retry against a partially restored target can create a
different failure; use a fresh empty target unless the tool and recovery plan
explicitly support resumption.

## `NK013` for an unsupported `COPY` form

Compare the statement with the implemented [COPY
limits](../platform-preview-limitations.md#copy-limits). In particular, `COPY
TO` and file-based `COPY` do not support sharded or reference tables. `COPY
FROM STDIN` supports both, subject to its shard-key, option, and default-value
requirements.

`COPY FROM STDIN` against a sharded table is also rejected while that table has
an enabled global secondary index (GSI). COPY does not maintain the GSI lookup
rows, so allowing it would make indexed reads incomplete. Load the rows before
enabling the GSI, then backfill and verify the lookup table before cutover, or
use supported `INSERT` statements that maintain the index.

For `pg_restore`, restore into an unsharded Neki database. Moving the imported
rows into a sharded layout is a separate [data migration
workflow](../data-migration.md).

## An extension cannot be created

Preserve the extension name and the first `pg_restore` error. Compare the
source extension inventory from `pg_extension` with the target configuration
profile's **Extensions** tab. An extension reported by
`pg_available_extensions` but absent from the dashboard catalog is not
supported for customer use on Neki.

If the extension is supported, complete any required profile-level enablement
and wait for the configuration change before retrying on a fresh target. If it
is not supported, stop the restore and determine whether the dependent schema
objects and data can be migrated without it. Do not continue after
`--exit-on-error` stops the restore.

## Owner, role, or privilege errors

Use `--no-owner --no-privileges` for the dump-and-restore path. Create
application roles through PlanetScale, then apply only the grants those roles
need after the restore. Do not create or depend on source superuser roles.

## A retry fails against a partially restored target

Create a fresh Neki target and repeat the restore. Do not use a cleanup script
that drops and recreates the `public` schema; doing so can remove Neki-managed
objects required by the cluster.

## The target shard group includes the source shard

MoveTables and Reshard copy onto a disjoint placement. The target group's
shards cannot include a source shard. Create the destination shards required
by the planned key ranges and capacity, put only those shard UIDs in the target
group, and leave the import shard as the source and [authoritative shard
group](../data-topology.md). See [Data
migration](../data-migration.md#what-a-migration-workflow-moves) for the
placement requirements.

## Reshard refuses undeclared tables

Reshard requires a table entry in the data topology for every physical base
table that resolves to the source group, including tables that inherit that
group from a database, schema, or cluster default. Declare those tables, or
move tables that should stay unsharded onto the authoritative group, before
creating the workflow.

## `NK016` reports shared dependencies

An object used by a moving table is also used by a table that will remain on
the source placement. Shared objects can include sequences, functions used by
column defaults or checks, trigger functions, and functions used by expression
indexes. The error lists the conflicting object IDs.

Move all tables that use the shared objects together, or change the schema so
the moving and non-moving tables no longer share them. Inventory dependent
views separately because neither MoveTables nor Reshard automatically repoints
them.

## The workflow differ exhausts source connections

Do not switch traffic. Stop unrelated administrative work and check the source
profile's connection usage before retrying the differ. Copy streams, change
streaming, and differ workers use managed connections in addition to application
and monitoring sessions. Do not retry until the source can provide reliable
headroom for the workflow.

## The comparison does not finish or reports shard errors

Do not switch traffic. Results from an unfinished comparison are partial. A
table that never started may be absent from the report entirely, and a
zero-row result does not prove that an unfinished table is empty.

Resolve the reported shard errors, then run the comparison again. Approve
cutover only after every expected table finishes on every target shard and the
report finds no missing, extra, or mismatched rows.

## A dependent view returns stale rows after Reshard or MoveTables

The view can still resolve to the retained source copy of a table after the
table's traffic moves. Recreate or update the view for the new placement, then
compare its result with a direct query against the moved table before completing
the workflow or removing source rows.

## Row counts or checksums differ

Do not cut over. Confirm that source writes were stopped before the dump and
that every restore error was reviewed. Compare by primary-key ranges to locate
the first mismatch, then determine whether the dump, restore, unsupported
object, or application validation query caused it.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
