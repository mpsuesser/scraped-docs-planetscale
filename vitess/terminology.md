---
url: https://planetscale.com/docs/vitess/terminology
title: "Terminology"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale terminology

> Here we provide definitions for technologies and concepts you will see throughout our documentation and product. Some of these are common terms in the databases space, while others are Vitess or PlanetScale specific.

**Platform availability:** Vitess only

## Database technologies

### MySQL

MySQL is the world's most popular relational database.
Many of the worlds largest web applications are powered by MySQL behind the scenes including Slack, X, GitHub, JD.com, and many others.
MySQL is open-source, has existed for 30+ years, and is currently maintained by Oracle.

### Vitess

[Vitess](https://vitess.io) is an open-source project created by engineers at YouTube in the early 2010's.
It was created to solve their MySQL scalability challenges.
Vitess works in tandem with MySQL and provides proxying, orchestration, monitoring, and sharding capabilities.
A Vitess-powered MySQL database can scale to petabytes of data and millions of queries per second.

### PlanetScale

PlanetScale is a complete database platform that emphasizes reliability, scalability, and developer productivity.
PlanetScale offers two database engines: [Vitess](../vitess.md) (MySQL-compatible) and [Postgres](../postgres.md).
PlanetScale Vitess databases are powered by Vitess and MySQL. PlanetScale makes it easy to spin up, resize, manage, and work with databases both for small organizations and large enterprises. Additionally, PlanetScale employs the majority of the Vitess core maintainers.

## PlanetScale concepts

### Database

In the PlanetScale app, users can create one or more **Databases**.
Each database belongs to an **Organization**.
Each database uses either [Vitess](../vitess.md) or [Postgres](../postgres.md).

Vitess databases use MySQL under the hood. If you've used vanilla MySQL in the past, you may be used to one MySQL server managing multiple logical databases. In a PlanetScale Vitess database, you cannot use `CREATE DATABASE` to create multiple logical databases. Instead, you can achieve a similar outcome by creating multiple **keyspaces**.

[PlanetScale Postgres](../postgres.md) databases do not have this limitation. You can use `CREATE DATABASE` to create multiple PostgreSQL databases within a single PlanetScale Postgres database.

### Keyspace

A **Keyspace** represents a single, logical database.
When you create a new PlanetScale database, it contains a single, default keyspace of the same name as your database.
More keyspaces can be added using the [Clusters](cluster-configuration.md) page.
Each keyspace has its own primary and replicas.
The "keyspace" terminology [comes from Vitess](https://vitess.io/docs/concepts/keyspace/).
[Read more about keyspaces](sharding/keyspaces.md).

### Branch

A typical flow on GitHub is to work on new feature in branches.
When development is complete, the developer can create a pull request which then gets merged into `main`.
PlanetScale allows you to work with your database in a similar fashion via **branches**.
Every database has a default branch, often named `main`.
You can create branches of your database, in which you can make schema changes without affecting your production tables and data.

### Deploy request

When you are ready to bring the changes from a branch into your production database, you can create a **Deploy request** (or **DR** for short).
This is akin to a pull request on GitHub.
Deploy requests can be created, reviewed by peers, and deployed to the main database.
Deploy requests give you the ability to change the schema of your database with 0 downtime.
We also give you the ability to revert deploy requests for a short window of time after being deployed, in case you encounter an issue with your application.

Deploy requests are currently limited to Vitess databases.

## Vitess concepts

### VTGate

Every PlanetScale database comes with at least 3 **VTGates**.
A VTGate is the layer of Vitess that acts as a proxy between your application servers and the MySQL instances.
VTGates play an important role in allowing your applications to treat the database as a single server, even if your data is actually spread across many keyspaces and shards.
VTGates handle query buffering, distributing your queries across shards, and other important functions for high-availability.

### Shard

Vitess keyspaces can be unsharded or [sharded](sharding.md).
A sharded keyspace is one where the data of tables is spread out across multiple primaries, rather than all going to one.
The way data is sharded is specified via the [Vindex](sharding/vindexes.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
