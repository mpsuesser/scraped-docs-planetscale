---
url: https://planetscale.com/docs/neki/example-schema
title: "Example Schema"
description: ""
access_date: 2026-10-22T18:21:49.469Z
current_date: 2026-10-22T18:21:49.469Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Example schema

> The tables and data topology used by the SQL examples in the Neki documentation

The SQL examples in the Neki documentation use one small example schema. This
page defines its tables and the data topology that places them on shards.

| Table | Placement |
| - | - |
| `users`, `orders`, `products`, `user_tags` | Sharded on `user_id` in the `user_data` shard group |
| `accounts` | Sharded on `account_id` in the `user_data` shard group. Owns the global secondary indexes (GSIs) `by_email` and `by_phone` |
| `accounts_by_email`, `accounts_by_phone` | Unsharded lookup tables for the `accounts` GSIs, in the `meta` shard group |
| `notes` | Unsharded, in the `meta` shard group |
| `ref_countries` | A reference table, with a copy on every shard in the `user_data` shard group |
| `regional_users` | Sharded on the enum column `region` in the `user_data` shard group |
| `regional_accounts` | Sharded on the generated column `account_id` in the `user_data` shard group |
| `devices` | Sharded on the expression `owner_id + device_no` in the `user_data` shard group |

## Tables

Every table is in the `public` schema of the `app` database:

```sql theme={null}
CREATE TABLE public.users (
    user_id    bigint PRIMARY KEY,
    name       varchar(255) NOT NULL,
    email      varchar(255) NOT NULL,
    created_at timestamp NOT NULL DEFAULT now()
);

CREATE TABLE public.orders (
    order_id     bigint PRIMARY KEY,
    user_id      bigint NOT NULL,
    order_date   date NOT NULL,
    total_amount numeric(10, 2),
    status       varchar(50),
    created_at   timestamp NOT NULL DEFAULT now()
);

CREATE INDEX orders_user_id_idx ON public.orders (user_id);

CREATE TABLE public.products (
    product_id  bigint PRIMARY KEY,
    user_id     bigint NOT NULL,
    name        varchar(255) NOT NULL,
    price       numeric(10, 2)
);

CREATE INDEX products_user_id_idx ON public.products (user_id);

CREATE TABLE public.user_tags (
    user_id bigint PRIMARY KEY,
    tag_ids integer[] NOT NULL
);

CREATE TABLE public.accounts (
    account_id bigint PRIMARY KEY,
    label      text,
    email      text NOT NULL,
    phone      text NOT NULL,
    category   integer
);

CREATE TABLE public.accounts_by_email (
    email      text PRIMARY KEY,
    account_id bigint NOT NULL
);

CREATE TABLE public.accounts_by_phone (
    phone      text PRIMARY KEY,
    account_id bigint NOT NULL
);

CREATE TABLE public.notes (
    id   bigint PRIMARY KEY,
    body text NOT NULL
);

CREATE TABLE public.ref_countries (
    country_id integer PRIMARY KEY,
    name       text NOT NULL
);

CREATE TYPE public.region AS ENUM ('eu', 'us');

CREATE TABLE public.regional_users (
    region  public.region NOT NULL,
    user_id bigint NOT NULL,
    PRIMARY KEY (region, user_id)
);

CREATE TABLE public.regional_accounts (
    region_id  integer NOT NULL,
    local_id   bigint NOT NULL,
    account_id bigint GENERATED ALWAYS AS (region_id * 1000000 + local_id) STORED PRIMARY KEY
);

CREATE TABLE public.devices (
    owner_id  bigint NOT NULL,
    device_no bigint NOT NULL,
    PRIMARY KEY (owner_id, device_no)
);
```

`users`, `orders`, `products`, and `user_tags` share the shard key `user_id`.
Rows with the same `user_id` are on the same shard, so Neki can send a join of
these tables on `user_id` to the shards as one query.

`orders` and `products` have a Postgres index on `user_id`. The other sharded
tables have their shard-key column as their primary key, which already indexes
it.

`accounts` is in the same shard group but uses a different shard key,
`account_id`. Its rows are not co-located with the rows of the tables sharded
on `user_id`.

`regional_users`, `regional_accounts`, and `devices` show other ways to choose
a shard key: an enum column, a generated column, and an expression over two
columns.

## Data topology

```json theme={null}
{
  "authoritative_shard_group": "meta",
  "shard_indexes": {
    "xxhash_user_id": {
      "type": "xxhash",
      "columns": ["user_id"]
    },
    "xxhash_account_id": {
      "type": "xxhash",
      "columns": ["account_id"]
    },
    "xxhash_region": {
      "type": "xxhash",
      "columns": ["region"]
    },
    "xxhash_device": {
      "type": "xxhash",
      "columns": ["owner_id + device_no"]
    }
  },
  "shard_groups": [
    {
      "uid": "meta",
      "key_ranges": [{ "shard_uid": "shard-meta" }]
    },
    {
      "uid": "user_data",
      "default_shard_index": "xxhash_user_id",
      "key_ranges": [
        { "shard_uid": "shard-a", "end": "80" },
        { "shard_uid": "shard-b", "start": "80" }
      ]
    }
  ],
  "databases": {
    "app": {
      "advisory_lock_shard_group": "user_data",
      "global_secondary_indexes": {
        "by_email": {
          "schema": "public",
          "table": "accounts_by_email",
          "owner_table": "accounts",
          "columns": ["email"],
          "unique": true,
          "enabled": true
        },
        "by_phone": {
          "schema": "public",
          "table": "accounts_by_phone",
          "owner_table": "accounts",
          "columns": ["phone"],
          "unique": true,
          "enabled": true
        }
      },
      "schemas": {
        "public": {
          "tables": {
            "users": {
              "shard_group": "user_data"
            },
            "orders": {
              "shard_group": "user_data"
            },
            "products": {
              "shard_group": "user_data"
            },
            "user_tags": {
              "shard_group": "user_data"
            },
            "accounts": {
              "shard_group": "user_data",
              "secondary_indexes": [
                {
                  "name": "by_email",
                  "columns": ["email"]
                },
                {
                  "name": "by_phone",
                  "columns": ["phone"]
                }
              ],
              "primary_indexes": [
                {
                  "shard_index": "xxhash_account_id"
                }
              ]
            },
            "regional_users": {
              "shard_group": "user_data",
              "primary_indexes": [{ "shard_index": "xxhash_region" }]
            },
            "regional_accounts": {
              "shard_group": "user_data",
              "primary_indexes": [{ "shard_index": "xxhash_account_id" }]
            },
            "devices": {
              "shard_group": "user_data",
              "primary_indexes": [{ "shard_index": "xxhash_device" }]
            },
            "accounts_by_email": {
              "shard_group": "meta"
            },
            "accounts_by_phone": {
              "shard_group": "meta"
            },
            "notes": {
              "shard_group": "meta"
            }
          },
          "reference_tables": {
            "ref_countries": {
              "shard_groups": ["user_data"]
            }
          }
        }
      }
    }
  }
}
```

The topology has two shard groups:

* `user_data` places the sharded tables on `shard-a` and `shard-b`. Its
  default shard index, `xxhash_user_id`, hashes `user_id`. The `accounts` and
  `regional_accounts` entries override it with `xxhash_account_id`, which
  hashes `account_id`; `regional_users` and `devices` override it with
  `xxhash_region` and `xxhash_device`. It is also the advisory-lock shard
  group, so Neki hashes each advisory-lock key to one of its shards.
* `meta` is the [authoritative shard
  group](https://planetscale.com/docs/neki/data-topology.md#authoritative-shard-group). Its one shard,
  `shard-meta`, holds the unsharded tables: `notes` and the two GSI lookup
  tables.

`ref_countries` is a reference table. Each shard in `user_data` holds a full
copy, so Neki can send a join of a sharded table with `ref_countries` to the
shards.

[Data topology](https://planetscale.com/docs/neki/data-topology.md) explains each part of the topology
document. [Reference tables and GSIs](https://planetscale.com/docs/neki/reference-tables-and-gsis.md) explains
the GSI and reference-table declarations.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
