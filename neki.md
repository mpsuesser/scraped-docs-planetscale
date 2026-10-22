---
url: https://planetscale.com/docs/neki
title: "Neki"
description: ""
access_date: 2026-10-22T18:21:49.469Z
current_date: 2026-10-22T18:21:49.469Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Neki

> Horizontal sharding for Postgres

> [!NOTE]
>
> Neki is currently in Platform Preview. Platform Preview features are “Beta Features” under the PlanetScale Terms of Service or your applicable agreement with PlanetScale. Accordingly, Neki is subject to the limitations and disclaimers applicable to Beta Features and is not covered by any service level agreement.

Neki is the best way to scale a highly-available Postgres database. It provides sharding, zero-downtime operations, online DDL, replication workflows, and sophisticated cluster management capabilities, all using real Postgres.

Neki abstracts all of this capability behind a single connection string that speaks the Postgres wire protocol.
It accomplishes this by placing a sophisticated proxy (router) between clients and the Postgres nodes.
This router parses, plans, and coordinates all of the Postgres traffic, routing it to the correct nodes in an extremely efficient way.

All of these capabilities come with the other PlanetScale features like [Insights](https://planetscale.com/docs/neki/monitoring/query-insights.md), [anomalies](https://planetscale.com/docs/neki/monitoring/anomalies.md), and [schema recommendations](https://planetscale.com/docs/neki/monitoring/schema-recommendations.md).
This makes it a perfect solution for anything from a massive database storing hundreds of terabytes across many shards, all the way down to small, unsharded Postgres databases with a handful of gigabytes.

You do not have to shard to use Neki. A new database starts as an unsharded cluster. You still get all the benefits of zero-downtime upgrades, cluster management, connection pooling and more.
Whether you later shard depends on your traffic, access patterns, and data — see [When to shard](https://planetscale.com/docs/neki/when-to-shard.md).

> **[Architecture](https://planetscale.com/docs/neki/overview.md)**
>
> The components of a Neki deployment and how they fit together.

> **[When to shard](https://planetscale.com/docs/neki/when-to-shard.md)**
>
> Whether sharding is the right answer to the limits you're hitting.

> **[Quickstart](https://planetscale.com/docs/neki/tutorials/planetscale-quick-start-guide.md)**
>
> Create your first Neki database, connect, and insert data.

> **[Neki vs PlanetScale Postgres](https://planetscale.com/docs/neki/coming-from-postgres.md)**
>
> The upgrade from a single primary, and the habits that change.

## Availability and access

Neki is currently available through the PlanetScale Platform Preview. An organization administrator can select **Join the Platform Preview** from the organization dashboard or open **Organization settings** > **Platform Preview** and accept the preview terms. Neki then appears as a database engine.

## From the maintainers of Vitess

Neki is built by PlanetScale, the maintainers of [Vitess](https://planetscale.com/docs/vitess.md).
Vitess already runs some of the largest services on the internet, including Slack, Square, and Cursor.

Neki is not a Vitess port.
It is built from scratch to specifically solve the scalability and operational challenges of Postgres.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
