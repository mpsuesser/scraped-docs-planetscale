---
url: https://planetscale.com/docs/neki/terminology
title: "Terminology"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Neki terminology

> Definitions for the Postgres, PlanetScale, and Neki concepts used throughout the documentation.

## Database technologies

### Postgres

Postgres is an open-source relational database. Applications connect to a
Neki database using the Postgres wire protocol and standard
Postgres clients.

### Neki

Neki is PlanetScale's distributed Postgres system. It places routers in front
of Postgres shards and provides query routing, topology management, failover,
sharding, and online data-movement workflows.

### PlanetScale

PlanetScale is a managed database platform focused on reliability, scalability,
and developer productivity. PlanetScale operates the infrastructure around Neki
and provides the application, APIs, and workflows used to create and manage
databases.

## PlanetScale concepts

### Organization

An organization contains PlanetScale databases and the people who can access
them. Organization settings control membership, billing, and access to shared
resources.

### Database

A database is the top-level resource an application connects to. It belongs to
an organization and contains one or more branches.

### Branch

A branch is an isolated database environment. A database has a default branch,
often named `main`, and can have additional branches for development and
testing.

Production branches are intended for production workloads and use a highly
available configuration. Development branches are intended for development and
testing.

### Configuration profile

A configuration profile is a set of infrastructure settings shared by one or
more shards. It defines the cluster size, replica count, Postgres version,
parameters, and enabled extensions. Changing a profile applies the new settings
to every shard assigned to it.

### Cluster size

A cluster size defines the CPU, memory, and storage resources assigned to a
Postgres instance. Every shard assigned to a configuration profile uses the
profile's selected cluster size.

## Neki concepts

### Router

A router accepts Postgres connections from applications. It plans each query,
uses the data topology to find its destination shards, sends work to those
shards, and combines results when a query reaches more than one shard.

Routers do not store durable application data and are interchangeable.

### Admin

The admin monitors the Postgres instances in a Neki cluster through
their sidecars. It analyzes cluster health and coordinates repairs, including
replica recovery and primary failover. This control-plane service is different
from a database administrator or Postgres role. Its selected size establishes
a capacity baseline, and PlanetScale automatically increases its memory when
needed.

### Orchestration layer

The orchestration layer, implemented by the Neki operator, reconciles the
configuration requested through PlanetScale into running routers, Admin
services, shards, and Postgres instances. It coordinates rolling changes,
resizing, backups, restores, and replacement instances and reports their
readiness and progress. It is outside the SQL query path and does not determine
data placement.

### Postgres instance

A Postgres instance is one Postgres server within a shard. A serving instance
can be the primary or a replica.

### Primary

The primary is the writable Postgres instance for a shard. Each shard has one
primary at a time.

### Replica

A replica is a read-only Postgres instance that copies changes from the
primary through Postgres physical replication. Replicas provide read capacity
and can be promoted during failover.

### Shard

A shard is a set of Postgres instances that replicate the same data. It has one
primary and zero or more replicas.

Provisioning a shard creates database capacity. It does not assign tables or
rows to that shard; the [data topology](data-topology.md) controls data
placement.

### Shard group

A shard group is a routing layout that maps key ranges to physical shards.
Tables in the same group can keep related rows together when they use the same
shard index.

A shard group in the data topology is different from a PlanetScale
configuration profile. A configuration profile controls infrastructure
settings; a shard group controls data placement and query routing.

### Data topology

The data topology is Neki's routing map. It defines shard groups, shard indexes,
key ranges, table bindings, and the shards that own each range.

### Shard index

A shard index turns values from one or more table columns into routing values.
Neki compares those routing values with a shard group's key ranges to choose a
destination shard.

### Key range

A key range assigns part of a shard group's routing space to a physical shard.
Together, a shard group's key ranges determine where its rows belong.

### Authoritative shard group

The authoritative shard group provides the canonical Postgres object
identifiers (OIDs) and schema events for a database. Neki rewrites result sets
so clients always see those OIDs. Sequence metadata operations also use this
group by default. It contains exactly one key range that spans the entire
keyspace.

### Reference table

A reference table stores a copy of the same logical rows in multiple shard
groups. This allows local reads and joins without routing every lookup back to
one owner shard.

### Global secondary index

A global secondary index, or GSI, uses a lookup table to map another key to the
shard key of an owner row. Neki can read this mapping before routing a query to
the owner table. Applications cannot query, write to, or change the lookup
table directly through a router.

### Replicator

Replicator is the Neki component that copies existing rows and then streams
changes from a source to a target. It runs the workflows used to move tables,
reshard data, and apply online schema changes.

### Workflow

A workflow is the multi-shard unit of a Replicator migration. It tracks an
online data movement operation while the database continues serving traffic.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
