---
url: https://planetscale.com/docs/vitess/imports/postgres
title: "Postgres"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

**PlanetScale now supports Postgres**. This guide is for migrating from Postgres to PlanetScale’s Vitess product. You can still use these scripts if you would like to utilize [Vitess](../../vitess.md). If you prefer to stay on Postgres, refer to the [Postgres import guides](../../postgres/imports/postgres-imports.md).

Many customers choose to migrate from Postgres to PlanetScale for Vitess in order to leverage the performance and scalability offered by PlanetScale and Vitess.

Use this guide if you are importing from platforms like Aurora Postgres, RDS Postgres, Neon, Supabase, and other Postgres instances.

## Postgres to MySQL

Vitess is built on MySQL. MySQL and Postgres are similar in that they are both relational databases, broadly function similarly, and are used for similar purposes by many organizations. When you look more closely, there are a number of small differences in types, indexing, and SQL language features. When added up, it makes for enough difference that migrating between the two is typically not straightforward.

We provide scripts to make moving your data from Postgres to PlanetScale for Vitess easier. However, moving the data is only a part of the challenge. It is up to you to ensure that your application servers, which connect to your database, are also set up to cut over to MySQL. If you are using an ORM, this could be as simple as changing the target database engine. In some cases, you will have to update your queries manually to follow MySQL syntax.

## Migration Guides

We have two recommendations for how to migrate your Postgres database to PlanetScale for Vitess. Both of these solutions leverage the AWS Database Migration Service to handle conversions between Postgres and MySQL types.

1. Use our [Postgres to PlanetScale for Vitess](postgres-planetscale-migration-guide.md) guide along with our [postgres-planetscale scripts](https://github.com/planetscale/migration-scripts/tree/main/postgres-planetscale) to import directly from a Postgres source to a PlanetScale target. This technique is simpler than option 2, as it does not require an intermediate MySQL database, but operates more slowly at a rate of only a few gigabytes per hour.
2. Use our [Postgres to MySQL + PlanetScale for Vitess](postgres-mysql-planetscale-migration-guide.md) guide along with our [postgres-mysql-planetscale scripts](https://github.com/planetscale/migration-scripts/tree/main/postgres-mysql-planetscale) to import from Postgres to an RDS MySQL instance, and then use PlanetScale’s built-in [import tool](database-imports.md) to bring the data in from MySQL. This technique is more complex since it requires an intermediary MySQL database. However, this technique can be 10x or more faster. This is recommended for larger databases.

In some cases, using these scripts and following the necessary steps from our docs are all it takes to get your data imported to PlanetScale.

However, there are many possible edge cases and specific Postgres configurations that may cause the scripts to fail. We encourage customers to make modifications to the scripts as needed to support their specific database environment.

If you encounter issues while importing from a Postgres database, please [reach out to support](https://planetscale.com/contact?initial=support) for assistance.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
