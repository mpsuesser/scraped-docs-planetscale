---
url: https://planetscale.com/docs/neki/reference-tables-and-gsis
title: "Reference Tables And Gsis"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

A reference table keeps a full copy of shared data on the shards that need it. A global secondary index (GSI) maps another lookup key to the shard key of the original row. Both give Neki a way to route queries that cannot rely on a table’s shard key alone.

Consider a sharded table that has a single shard index on the `tenant_id` column. Without a reference table or GSI, looking up a row by `email` instead of `tenant_id`, for example, may require searching every shard.

Both can avoid unnecessary scatter reads, but add storage and work to writes. The original table and row behind a GSI are called its owner table and owner row.

## Reference tables

Reference tables are best for shared data that every shard in a group needs. If the `customers` table is sharded across the `tenant_data` shard group and is often joined with common data from the `countries` table, keep a full copy of `countries` on each shard and declare it a reference table.

![A query joins customers to countries with a tenant_id predicate, routing through Neki to one shard in tenant_data. Postgres uses the local countries copy, while the other shards hold their own complete copies](https://mintcdn.com/planetscale-2/6i1lJ6cNfB-VSsaH/neki/reference-tables-and-gsis/reference-read.png?w=2500&fit=max&auto=format&n=6i1lJ6cNfB-VSsaH&q=85&s=e8989b220a0ac7497f9f219d1e5fc533)

A query joins customers to countries with a tenant\_id predicate, routing through Neki to one shard in tenant\_data. Postgres uses the local countries copy, while the other shards hold their own complete copies

![A query joins customers to countries with a tenant_id predicate, routing through Neki to one shard in tenant_data. Postgres uses the local countries copy, while the other shards hold their own complete copies](https://mintcdn.com/planetscale-2/6i1lJ6cNfB-VSsaH/neki/reference-tables-and-gsis/reference-read-darkmode.png?w=2500&fit=max&auto=format&n=6i1lJ6cNfB-VSsaH&q=85&s=801126719249c7a0f46b5cb44120aff5)

A query joins customers to countries with a tenant\_id predicate, routing through Neki to one shard in tenant\_data. Postgres uses the local countries copy, while the other shards hold their own complete copies

This tells Neki that `countries` data exists on every shard in the `tenant_data` shard group, and Neki can send the join to shards based on the predicates on `customers`. If a `tenant_id` predicate narrows the query to one shard, the join executes on one shard. If the query scatters, each shard joins its customer rows to its local `countries` copy. The final result is calculated by the router, but joins stay local to each shard because any row from `customers` is co-located with all rows from `countries`.

A query against `countries` alone can use any copy in the group.

The binding changes routing only. Neki assumes the copies already exist and contain the same rows. It does not verify them, so an incomplete copy can produce an incomplete result.

## Configure a reference table and GSI

The following complete topology declares:

- `countries` as a reference table expected on both shards in `tenant_data`.
- `users` as a table sharded by `tenant_id`.
- `users_by_email` as an unsharded lookup table for the unique `by_email` GSI.

```json
{
  "authoritative_shard_group": "meta",
  "shard_indexes": {
    "xxhash_tenant_id": {
      "type": "xxhash",
      "columns": ["tenant_id"]
    }
  },
  "shard_groups": [
    {
      "uid": "meta",
      "key_ranges": [
        { "shard_uid": "shard-a" }
      ]
    },
    {
      "uid": "tenant_data",
      "default_shard_index": "xxhash_tenant_id",
      "key_ranges": [
        { "shard_uid": "shard-a", "end": "80" },
        { "shard_uid": "shard-b", "start": "80" }
      ]
    }
  ],
  "databases": {
    "app": {
      "global_secondary_indexes": {
        "by_email": {
          "schema": "public",
          "table": "users_by_email",
          "owner_table": "users",
          "columns": ["email"],
          "owner_sk_columns": ["tenant_id"],
          "unique": true,
          "ignore_null": false,
          "enabled": false
        }
      },
      "schemas": {
        "public": {
          "tables": {
            "users": {
              "shard_group": "tenant_data",
              "secondary_indexes": [
                {
                  "name": "by_email",
                  "columns": ["email"]
                }
              ]
            },
            "users_by_email": {
              "shard_group": "meta"
            }
          },
          "reference_tables": {
            "countries": {
              "shard_groups": ["tenant_data"]
            }
          }
        }
      }
    }
  }
}
```

The Postgres tables must already exist on the shards named by their bindings. Applying the topology does not create `countries`, `users`, or `users_by_email`, and it does not copy existing rows.

The GSI has two linked declarations. The database-level `global_secondary_indexes.by_email` entry defines the lookup table and its owner. The `users.secondary_indexes` entry activates that GSI for owner reads and writes. The lookup table also needs an ordinary table binding so Neki can route lookup rows.

`owner_sk_columns` names the owner primary-index columns stored in each lookup row. It can be omitted when the owner has exactly one primary index whose `ignore_null` value is `false`; Neki infers that index. Name `owner_sk_columns` when the owner has more than one such primary index.

For a GSI, omitted scalar fields use their protobuf defaults: `schema` is resolved from the lookup table when unambiguous, and `unique`, `ignore_null`, and `enabled` default to `false`. A non-unique GSI must also set `owner_pk_columns` so lookup rows for different owner rows remain distinct.

A reference table can omit `shard_groups` only when a schema, database, or cluster default shard group resolves for it. Neki then uses that effective default. Listing the groups explicitly makes every copy location visible in the topology.

### Activate a GSI safely

An enabled GSI does not backfill historical lookup rows. Enabling an incomplete GSI can return incomplete results without an error. Use this sequence:

1. Create the lookup table with the required keys and constraints.
2. Declare both linked GSI entries and set `global_secondary_indexes.<name>.enabled` to `false`.
3. Backfill the lookup table while also following owner-table changes.
4. Verify every expected owner row has the correct lookup row.
5. Set `enabled: true` and apply the topology at cutover.

Keep the GSI disabled until both backfill and change capture are complete. Re-run verification immediately before activation.

## Reference-table writes

An ordinary `INSERT`, `UPDATE`, or `DELETE` against `countries` runs on every distinct shard that holds a copy. Neki sends the statements concurrently and waits for every shard before reporting completion.

For a group with four shards, one inserted row produces four shard executions and four stored copies. Postgres replicas and indexes add their usual storage overhead.

The router coordinates one backend transaction per participating shard. Those transactions commit independently, so a commit can succeed on some shards and fail on another.

## GSI mapping

In the example, `users` is sharded by `tenant_id`, but the application also needs to look up a user by email:

```sql
SELECT *
FROM users
WHERE email = 'adam@example.com';
```

The query has no `tenant_id`, so the table’s primary routing cannot identify one shard. A GSI on `email` adds a lookup table that maps the email address to the owning row’s `tenant_id`. For a supported single-table equality query, Neki reads that mapping and uses it to route the original query to the correct shard. The lookup table has its own placement, so this first step may itself reach one shard or several.

![An email predicate routes to the email_lookup GSI, which returns tenant_id 42. Neki uses that tenant_id to route the users query to shard-b in the tenant_data group](https://mintcdn.com/planetscale-2/6i1lJ6cNfB-VSsaH/neki/reference-tables-and-gsis/gsi-read.png?w=2500&fit=max&auto=format&n=6i1lJ6cNfB-VSsaH&q=85&s=83efa17dbd191bbac4e7dd6ed0105178)

An email predicate routes to the email\_lookup GSI, which returns tenant\_id 42. Neki uses that tenant\_id to route the users query to shard-b in the tenant\_data group

![An email predicate routes to the email_lookup GSI, which returns tenant_id 42. Neki uses that tenant_id to route the users query to shard-b in the tenant_data group](https://mintcdn.com/planetscale-2/6i1lJ6cNfB-VSsaH/neki/reference-tables-and-gsis/gsi-read-darkmode.png?w=2500&fit=max&auto=format&n=6i1lJ6cNfB-VSsaH&q=85&s=723ba7e5c1c3a1cdf50ec60f650fc387)

An email predicate routes to the email\_lookup GSI, which returns tenant\_id 42. Neki uses that tenant\_id to route the users query to shard-b in the tenant\_data group

A unique GSI can sometimes answer the query without sending an additional query to the `users` table. This covering path applies only when the lookup row contains every owner column the plan needs. Locking reads still fetch the owner row so Postgres locks the underlying record rather than the lookup entry.

A non-unique GSI may return several `tenant_id` values. Neki uses them to narrow the owner query to the shard(s) that contain matches. Because reading the GSI itself requires work, it’s best to use GSIs for highly selective lookups. Otherwise, if the looked up value appears across most shards, the work needed to run a GSI-assisted query approaches a scatter query, and the GSI also adds overhead on the write path.

[Query planning](query-planning.md#reading-a-query-plan) shows how to inspect the resulting plan.

## GSI mappings for writes

Neki maintains GSI lookup rows in the same request that writes the owner row. For an insert into `users`, the router first checks mappings owned by other tables that the new row references. It writes the `users` row, then writes the email GSI mapping owned by `users`.

Neki supports one narrow kind of GSI-column update. It updates the owner row, removes the old lookup row, and adds the new lookup row. All of these conditions must be true:

- The update affects one active, globally unique, single-column GSI.
- The `SET` list changes only that indexed column.
- The `WHERE` clause contains only equality predicates. They pin every shard-key column and a unique key, so the update reaches one row on one shard.
- The statement has no common table expression (CTE) or `RETURNING` clause.
- The new value can be computed on the shard. `DEFAULT` and values that the router must compute are not supported.

Neki rejects other index-column updates, including updates involving a non-unique or composite GSI or affecting more than one GSI.

## Global uniqueness

Postgres enforces uniqueness within each lookup-table shard. To make that constraint global, equal indexed values must always reach the same shard.

An unsharded lookup table meets that condition. For a sharded lookup table, every shard-key column must also be one of the GSI’s indexed columns. If `email_lookup` is sharded by `email`, duplicate addresses meet on the same shard and conflict.

Neki rejects a lookup table sharded by a column that is not part of the GSI. It also disables a unique GSI if the lookup table’s Postgres primary key no longer matches the indexed columns.

## GSI limits and fallbacks

| Condition | Outcome |
| --- | --- |
| A statement directly names a GSI lookup table | Reject the statement |
| Unsupported read shape, such as `IN`, a set operation, or a multi-table route | Use primary-index routing or scatter when possible; otherwise reject the query |
| `UPDATE` or `DELETE` routed through a GSI | Reject the statement |
| Single-row update that meets the supported globally unique GSI shape | Update the owner row and its lookup row |
| Supported lookup without all columns needed for covering | Read the lookup, then the owner row |
| Covering read with different row-level security (RLS) on the owner and lookup tables | Reject the query during planning |
| `ignore_null: true` and an indexed value is NULL | Do not create or verify a lookup row |
| `ignore_null: false` and an indexed value is NULL | Reject the write |
| Any owner-key value or computed lookup routing value is NULL | Reject the write |
| A required GSI column is absent from an insert | Reject the insert during planning |
| `DELETE ... USING` targets an active GSI owner | Reject the statement as unsupported |
| `COPY FROM` targets a sharded table with an enabled GSI | Reject the copy because `COPY` does not maintain lookup rows |
| The GSI is disabled | Stop maintenance and non-owner verification, but continue rejecting updates to its declared columns |
| A unique GSI’s lookup primary key no longer matches its indexed columns | Disable the GSI |

A covering read replaces the owner table with the lookup table in the SQL sent to Postgres. Different RLS could change which rows are visible, so Neki fails planning.

`ignore_null` applies only to indexed inputs. Owner keys and lookup routing values remain strict because index-driven reads cannot reach a NULL-keyed mapping.

For a non-unique GSI, Neki compares the estimated lookup cost with the fallback route. It uses the GSI only when the lookup is expected to cost less.

## Choose what to duplicate

A reference table duplicates the complete shared dataset on every shard it covers. A GSI duplicates only the columns needed to find the owner row. Use a reference table when the whole dataset is small and many shards need it for reads or joins. Use a GSI when a large sharded table needs selective access through another key.

Both choices add work to writes. Neither removes the need for a primary routing design that distributes load and keeps common queries narrow.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
