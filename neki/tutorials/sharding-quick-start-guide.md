---
url: https://planetscale.com/docs/neki/tutorials/sharding-quick-start-guide
title: "Sharding Quick Start Guide"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

In [Database quickstart](planetscale-quick-start-guide.md) you created a single-shard Neki database with `categories` and `products`. Those tables stay on the original shard while you add a second shard and create an `orders` table sharded by a `tenant_id` column.

## Prerequisites

1. You will need [a PlanetScale account](https://auth.planetscale.com/sign-up)
2. An administrator of your PlanetScale organization must [join the Neki Platform Preview](../../neki.md#availability-and-access).

This quickstart configures the placement of an **empty** table. For a table that already contains rows, plan a [data migration](../data-migration.md).

## Add a shard

Use the same database and `main` branch from the database quickstart guide. Each new shard will use the selected configuration profile’s cluster size and replica count.

#### Dashboard

#### CLI

![Clusters Shards tab showing two ready shards, sh1 and sh2](https://mintcdn.com/planetscale-2/6i1lJ6cNfB-VSsaH/neki/tutorials/clusters-shards.png?w=2500&fit=max&auto=format&n=6i1lJ6cNfB-VSsaH&q=85&s=6bc50e541aba0aad6a40ca5bb9911f83)

Clusters Shards tab showing two ready shards, sh1 and sh2

![Clusters Shards tab showing two ready shards, sh1 and sh2](https://mintcdn.com/planetscale-2/6i1lJ6cNfB-VSsaH/neki/tutorials/clusters-shards-darkmode.png?w=2500&fit=max&auto=format&n=6i1lJ6cNfB-VSsaH&q=85&s=37d4d4981f803a66fc6fd623743713b9)

Clusters Shards tab showing two ready shards, sh1 and sh2

## Inspect the default topology

Go to **Clusters** > **Data topology** to view and edit the JSON document in the dashboard. Alternatively, make the following changes with the PlanetScale CLI.

A new Neki database starts with one unsharded shard group. That group is both the default and the [authoritative shard group](../data-topology.md#authoritative-shard-group).

```shellscript
pscale branch data-topology get <DATABASE_NAME> main --format json
```

The document looks like this, with your first shard’s UID in place of `<SHARD_1>`:

```json
{
  "shard_groups": [
    {
      "uid": "<SHARD_1>",
      "key_ranges": [
        { "shard_uid": "<SHARD_1>" }
      ]
    }
  ],
  "default_shard_group": "<SHARD_1>",
  "authoritative_shard_group": "<SHARD_1>"
}
```

`categories` and `products` inherited the default shard group, so they stay on `<SHARD_1>`.

The authoritative shard group must resolve to exactly one complete shard range. Neki uses that group for cluster-wide metadata and sequences, including identity columns. Changing it is a topology-wide operational change; verify that the new group is complete and available before applying the replacement document.

Save a copy of the current document before you replace it.

## Create the orders table

Create a schema for the `orders` table. Do not insert rows yet.

```sql
CREATE TABLE orders (
  id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  tenant_id bigint NOT NULL,
  product_id bigint NOT NULL,
  quantity integer NOT NULL
);
```

## Apply a two-shard topology

Replace `<SHARD_1>` and `<SHARD_2>` with the shard `name` from `pscale branch shard list`. The `orders_by_tenant` group splits the keyspace at `80`. `orders` uses `xxhash` on `tenant_id`. Everything else keeps the original default.

```json
{
  "shard_indexes": {
    "xxhash_tenant_id": {
      "type": "xxhash",
      "columns": ["tenant_id"]
    }
  },
  "shard_groups": [
    {
      "uid": "<SHARD_1>",
      "key_ranges": [
        { "shard_uid": "<SHARD_1>" }
      ]
    },
    {
      "uid": "orders_by_tenant",
      "default_shard_index": "xxhash_tenant_id",
      "key_ranges": [
        { "shard_uid": "<SHARD_1>", "end": "80" },
        { "shard_uid": "<SHARD_2>", "start": "80" }
      ]
    }
  ],
  "databases": {
    "postgres": {
      "schemas": {
        "public": {
          "tables": {
            "orders": {
              "shard_group": "orders_by_tenant"
            }
          }
        }
      }
    }
  },
  "default_shard_group": "<SHARD_1>",
  "authoritative_shard_group": "<SHARD_1>"
}
```

Save that document as `data-topology.json` and apply it via the CLI, or paste the same document into **Clusters** > **Data topology** and select **Save changes**.

```shellscript
pscale branch data-topology update <DATABASE_NAME> main \
  --format json < data-topology.json
```

Confirm the resolved bindings:

```shellscript
pscale branch data-topology ls <DATABASE_NAME> main
```

`orders` should list under `orders_by_tenant`. `categories` and `products` should still resolve to `<SHARD_1>`.

See [Data topology](../data-topology.md) for shard-index types, defaults, the dashboard editor, and the SQL `__neki.set_data_topology` metafunction.

## Insert and check routing

Insert two tenants. Neki hashes `tenant_id` and sends each row to the matching shard in `orders_by_tenant`.

```sql
INSERT INTO orders (tenant_id, product_id, quantity)
VALUES (1, 1, 2);

INSERT INTO orders (tenant_id, product_id, quantity)
VALUES (2, 1, 1);
```

A predicate on `tenant_id` can narrow the query to one shard:

```sql
EXPLAIN (NEKI_PLAN, COSTS OFF, FORMAT TEXT)
SELECT id, quantity
FROM orders
WHERE tenant_id = 1;
```

`Route [EqualUnique]` means the query can be sent to a single shard.

![Web console EXPLAIN (NEKI_PLAN) showing Route EqualUnique on orders_by_tenant](https://mintcdn.com/planetscale-2/6i1lJ6cNfB-VSsaH/neki/tutorials/explain-equal-unique.png?w=2500&fit=max&auto=format&n=6i1lJ6cNfB-VSsaH&q=85&s=452381ba2e2b2eb38c76cee82f567852)

Web console EXPLAIN (NEKI\_PLAN) showing Route EqualUnique on orders\_by\_tenant

![Web console EXPLAIN (NEKI_PLAN) showing Route EqualUnique on orders_by_tenant](https://mintcdn.com/planetscale-2/6i1lJ6cNfB-VSsaH/neki/tutorials/explain-equal-unique-darkmode.png?w=2500&fit=max&auto=format&n=6i1lJ6cNfB-VSsaH&q=85&s=6ec23c88db1fee4e4c7bbcf1937cfe74)

Web console EXPLAIN (NEKI\_PLAN) showing Route EqualUnique on orders\_by\_tenant

A query without that predicate can scatter across the group:

```sql
EXPLAIN (NEKI_PLAN, COSTS OFF, FORMAT TEXT)
SELECT count(*)
FROM orders;
```

`Route [Scatter]` means each shard computes a partial count, and Neki combines the results.

`<SHARD_1>` still holds `categories` and `products`.

Rows added to the `orders` table are now split across two shards.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
