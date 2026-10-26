---
url: https://planetscale.com/docs/vitess/pricing
title: "Pricing"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Pricing

> A single Vitess database cluster includes the resources equivalent to 5 always-on instances within the base monthly plan cost.

This page covers Vitess.

Postgres version: [Postgres](https://planetscale.com/docs/postgres/pricing.md)

Vitess clusters are charged based on the following:

* Cluster size and [region](#region-pricing)
* Storage size (for network-attached storage clusters)
* [Branch](https://planetscale.com/docs/vitess/schema-changes/branching.md) hours beyond the included 1440 hours
* Number of [replicas](https://planetscale.com/docs/vitess/scaling/replicas.md)
* Number and size of [VTGates](https://planetscale.com/docs/vitess/scaling/vtgates.md)
* [Read-only regions](https://planetscale.com/docs/vitess/scaling/read-only-regions.md)

## Query live prices with SQL

Cluster SKU prices are available in a public, read-only Postgres table: `public.planetscale_prices`. You and your agent can query live prices with `SELECT`.

The role can only `SELECT` on that table. SSL is required. Connect through PgBouncer:

```
postgresql://pscale_api_hhdcchpiodja.dzken160d27q:pscale_pw_<PLANETSCALE_DATABASE_PASSWORD>@aws-us-east-1-3.pg.psdb.cloud:6432/postgres?sslmode=verify-full&sslrootcert=system
```

Each row is one priced cluster. Filter on `product` (`Postgres` or `Vitess`), `provider`, `region`, `machine_sku`, `replication_factor`, and `disk_sku` (empty string for `PS-*` network-attached SKUs). `price` is the monthly cluster cost in whole USD. It does not include extra storage, backups, egress, dedicated PgBouncers, or extra replicas.

```sql theme={null}
-- Postgres, 3-node HA PS-160 ARM in us-east-1
SELECT machine_sku, replication_factor, price
FROM planetscale_prices
WHERE product = 'Postgres'
  AND machine_sku = 'PS-160-ARM'
  AND provider = 'AWS'
  AND region = 'us-east-1'
  AND disk_sku = ''
  AND replication_factor = 3;
```

```sql theme={null}
-- Vitess, 3-node HA PS-160 in us-east-1
SELECT machine_sku, replication_factor, price
FROM planetscale_prices
WHERE product = 'Vitess'
  AND machine_sku = 'PS-160'
  AND provider = 'AWS'
  AND region = 'us-east-1'
  AND disk_sku = ''
  AND replication_factor = 3;
```

> [!NOTE]
>
> Not every SKU exists in every region. If a query returns no rows, that configuration is not sold there. Do not invent a price. Vitess SKUs in this catalog are mostly x86; there is no `PS-160-ARM` Vitess row.

Column meanings, more example queries, and an agent-friendly catalog are in [pricing.md](https://planetscale.com/pricing.md). The connection URI is also on the [pricing page](https://planetscale.com/pricing#pricing-database).

## Branches

Each database includes one production branch that provides a primary instance and two replica instances spread across availability zones.
The primary serves all queries by default, but the replicas can be used to serve read traffic, and are also used to maintain high-availability of your cluster.

In addition to the three instances used by your production branch, \~1440 hours of [development branch](https://planetscale.com/docs/planetscale-plans.md#development-branches) time per month is included.
The development branch time works out to two "always on" database instances, though we do recommend spinning them up and down and as needed during your development cycle.

These development branches can replace your existing development or staging environment on other providers.
Instead of purchasing 2-3 databases on PlanetScale to recreate your environment, you can just purchase one database cluster and use [branching](https://planetscale.com/docs/vitess/schema-changes/branching.md) for development.

You can upsize and downsize your cluster at any time. Pricing is prorated to the millisecond, so if you temporarily upsize, you will only be charged for the larger cluster size for the time that it was running. Billing for a new cluster size begins as soon as you begin the resize. You can also spin up additional production branches at any timing for additional cost. The pricing for these is also prorated.

## Read-only regions

[Read-only regions](https://planetscale.com/docs/vitess/scaling/read-only-regions.md) are an additional cost. Each read-only region is configured per keyspace with its own cluster size and replica count: the read-only region rate for the selected cluster size covers a single replica, and each additional replica is billed at the standard additional replica rate. Storage for read-only regions is billed as standalone storage and does not count toward your plan's included storage.

See the [read-only regions pricing documentation](https://planetscale.com/docs/vitess/scaling/read-only-regions.md#availability-and-pricing) for per-region rates and storage costs.

> [!NOTE]
>
> Cluster size options are capped at `PS-160` until you have a successfully paid an invoice of at least \$100. If you need larger sizes immediately, please [contact us](https://planetscale.com/contact) to unlock all sizes. You can find the full list of cluster sizes in our [Plans documentation](https://planetscale.com/docs/planetscale-plans.md#base-plan).

## Region pricing

### Amazon Web Services

> **[ap-northeast-1 (Tokyo)](https://planetscale.com/pricing?region=ap-northeast)**

> **[ap-south-1 (Mumbai)](https://planetscale.com/pricing?region=ap-south)**

> **[ap-southeast-1 (Singapore)](https://planetscale.com/pricing?region=ap-southeast)**

> **[ap-southeast-2 (Sydney)](https://planetscale.com/pricing?region=aws-ap-southeast-2)**

> **[eu-central-1 (Frankfurt)](https://planetscale.com/pricing?region=eu-central)**

> **[ca-central-1 (Montreal)](https://planetscale.com/pricing?region=aws-ca-central-1)**

> **[eu-west-1 (Dublin)](https://planetscale.com/pricing?region=eu-west)**

> **[eu-west-2 (London)](https://planetscale.com/pricing?region=aws-eu-west-2)**

> **[sa-east-1 (Sao Paulo)](https://planetscale.com/pricing?region=aws-sa-east-1)**

> **[us-east-1 (N. Virginia)](https://planetscale.com/pricing?region=us-east)**

> **[us-east-2 (Ohio)](https://planetscale.com/pricing?region=us-east-2)**

> **[us-west-2 (Oregon)](https://planetscale.com/pricing?region=us-west)**

### Google Cloud

> **[asia-northeast3 (Seoul, South Korea)](https://planetscale.com/pricing?region=gcp-asia-northeast3)**

> **[europe-west4 (Eemshaven, Netherlands)](https://planetscale.com/pricing?region=gcp-europe-west4)**

> **[northamerica-northeast1 (Montréal, Québec)](https://planetscale.com/pricing?region=gcp-northamerica-northeast1)**

> **[us-central1 (Council Bluffs, Iowa)](https://planetscale.com/pricing?region=gcp-us-central1)**

> **[us-east1 (Moncks Corner, South Carolina)](https://planetscale.com/pricing?region=gcp-us-east1)**

> **[us-east4 (Ashburn, Virginia)](https://planetscale.com/pricing?region=gcp-us-east4)**

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
