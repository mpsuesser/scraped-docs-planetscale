---
url: https://planetscale.com/docs/neki/error-codes
title: "Error Codes"
description: ""
access_date: 2026-10-27T16:28:43.980Z
current_date: 2026-10-27T16:28:43.980Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Neki error codes

> The numeric codes Neki reports with a not implemented error, the query shapes that trigger them, and what to do instead.

> [!NOTE]
>
> Neki is currently in Platform Preview. Platform Preview features are “Beta Features” under the PlanetScale Terms of Service or your applicable agreement with PlanetScale. Accordingly, Neki is subject to the limitations and disclaimers applicable to Beta Features and is not covered by any service level agreement.

When Neki's router cannot plan or execute a statement because an
implementation is still missing, it returns SQLSTATE `NK013` and a message in
this form:

```
not implemented: [108] This subquery shape is unavailable in ORDER BY.
```

The bracketed number is a stable catalog code. It identifies one specific
implementation gap, and it keeps that meaning for the life of the product: a
code is never reused for a different gap, even after the gap is closed. Quote
it when you report a rejected query, and use it to match a rejection your
application sees against the entry below.

`NK013` means the gap is one Neki intends to close. A limit that is a design
decision rather than a missing implementation is reported as `not supported`
instead, and the broad ones are listed in [Platform preview
limitations](https://planetscale.com/docs/neki/platform-preview-limitations.md). For how other Neki errors use
the DETAIL and HINT fields, see [Error hints and
details](https://planetscale.com/docs/neki/error-hints-and-details.md).

## How to read an entry

Each entry gives the code, the message sentence the router renders after the
bracketed code, an example that triggers it, and a note on which part of the
query causes the rejection.

The examples use the [example schema](https://planetscale.com/docs/neki/example-schema.md). A few
entries name another small table whose own name describes the property that
matters to that code. Where a rejection depends on how the data topology places
the tables rather than on the SQL text, the note says so — the same statement
can plan successfully with a different topology.

Codes that only an internal invariant can raise are not listed, because no
query produces them. A code absent from this page is one an application is
unlikely to meet.

A single statement can be rejected by more than one of these codes. Which one
you see is the first gap the planner reaches, so fixing one can reveal
another.

## Statements, transactions, and sessions

Codes raised while the router decides what a statement is and how the session should behave.

### 1 — SELECT INTO statements are unavailable.

```sql theme={null}
SELECT user_id INTO users_copy FROM users WHERE user_id = 1;
```

The `INTO` clause is what Neki rejects. At the top level Postgres treats `SELECT ... INTO` as `CREATE TABLE AS`; write that form instead.

### 20 — This statement type is unavailable.

```sql theme={null}
ALTER SYSTEM SET work_mem = '4MB';
```

The fallback for a statement no plan builder handles and for which the DDL surface has no more specific decision. A statement that the surface does recognize reports its own code instead.

### 21 — Reading an inheritance parent without ONLY is unavailable.

```sql theme={null}
CREATE TABLE inheritance_parent (inheritance_id bigint PRIMARY KEY);
CREATE TABLE inheritance_child () INHERITS (inheritance_parent);
SELECT inheritance_id FROM inheritance_parent;
```

The rejection only occurs when `inheritance_parent` has child tables. Reading the parent without `ONLY` would have to expand to the children, so add `ONLY` to read just the parent.

### 22 — AND CHAIN is unavailable for this transaction command.

```sql theme={null}
COMMIT AND CHAIN;
```

The `AND CHAIN` clause is the rejected part. Issue the `COMMIT` (or `ROLLBACK`, `END`, `ABORT`) and start the next transaction explicitly.

### 23 — This transaction command is unavailable.

```sql theme={null}
PREPARE TRANSACTION 'gid';
```

Raised for a transaction-control command the router has no handler for. `BEGIN`, `COMMIT`, `ROLLBACK`, `SAVEPOINT`, `RELEASE SAVEPOINT`, and `ROLLBACK TO SAVEPOINT` succeed. `PREPARE TRANSACTION`, `COMMIT PREPARED`, and `ROLLBACK PREPARED` return this code.

### 26 — Atomic transaction mode is unavailable.

```sql theme={null}
SET __neki.tx_mode = 'atomic';
```

Atomic distributed transactions are not available. `__neki.tx_mode` accepts `multi` (the default) and `single`.

### 28 — SET FROM CURRENT is unavailable.

```sql theme={null}
SET work_mem FROM CURRENT;
```

The `FROM CURRENT` form is rejected; give the parameter an explicit value.

### 29 — SET TRANSACTION SNAPSHOT is unavailable.

```sql theme={null}
SET TRANSACTION SNAPSHOT '00000003-0000001B-1';
```

Importing another session's snapshot would have to be coordinated across every shard, so the statement is rejected outright.

### 30 — This statement cannot be analyzed with EXPLAIN neki\_plan.

```sql theme={null}
EXPLAIN (NEKI_PLAN, ANALYZE) CREATE TABLE users_copy AS SELECT 1;
```

`EXPLAIN (NEKI_PLAN, ANALYZE)` runs the statement to show its router plan, and some statement types, such as `CREATE TABLE AS`, have none to show. The statement itself may still be supported when run directly.

### 31 — Data-modifying CTEs cannot be analyzed with EXPLAIN neki\_plan.

```sql theme={null}
EXPLAIN (NEKI_PLAN, ANALYZE) WITH u AS (UPDATE users SET name = 'x' WHERE user_id = 1 RETURNING user_id) SELECT * FROM u;
```

The data-modifying CTE is the rejected part: `EXPLAIN (NEKI_PLAN, ANALYZE)` does not run a statement whose CTE writes.

### 32 — This EXPLAIN option is unavailable.

```sql theme={null}
EXPLAIN (NEKI_PLAN, GENERIC_PLAN) SELECT user_id FROM users;
```

`GENERIC_PLAN` is one of the PostgreSQL `EXPLAIN` options the `NEKI_PLAN` renderer does not honor; the message names the option. Drop it, or run the `EXPLAIN` without `NEKI_PLAN`.

### 518 — This configuration parameter value is unavailable.

```sql theme={null}
SET client_min_messages = 'debug1';
```

The parameter accepts only some of its PostgreSQL values on Neki; the error's DETAIL names them.

### 563 — Changing DateStyle, TimeZone, IntervalStyle, extra\_float\_digits, or client\_encoding while a cursor over a router-computed query holds rows is unavailable.

```sql theme={null}
BEGIN;
DECLARE c CURSOR FOR SELECT g FROM generate_series(1, 5) g;
FETCH 1 FROM c;
SET DateStyle = 'SQL, DMY';
FETCH 1 FROM c;
```

The router encodes a cursor's rows under the output settings in force at its first read, so it cannot honor a `FETCH` after one of them changes. Change the setting before the first `FETCH`.

### 660 — A cursor over this query shape is unavailable.

```sql theme={null}
DECLARE c CURSOR WITH HOLD FOR SELECT count(*) FROM users;
```

A `WITH HOLD` cursor over a query the router computes, here an aggregate across shards, is refused; the message names the shape. Declare the cursor without `WITH HOLD` inside a transaction.

### 817 — Planning against … is unavailable: earlier DDL in this transaction is not reflected in the router's schema view.

```sql theme={null}
BEGIN;
CREATE AGGREGATE my_moving_sum(int) (SFUNC = int4pl, STYPE = int, MSFUNC = int4pl, MINVFUNC = int4mi, MSTYPE = int);
SELECT my_moving_sum(user_id) OVER (ORDER BY user_id ROWS 1 PRECEDING) FROM users;
```

The router plans against its own view of the schema, which DDL earlier in the same transaction has not reached yet. Commit the DDL first.

## Topology and evaluation context

Codes that depend on how the data topology is configured, or on the context the router is evaluating in.

### 10 — Multi-column partitioning indexes are unavailable.

```sql theme={null}
INSERT INTO devices (owner_id, device_no) VALUES (1, 2);
```

`devices` is sharded on `owner_id + device_no`, an expression over two columns, and `INSERT ... VALUES` cannot route on it. A `columns` list with more than one entry is rejected when the topology is applied; an expression over one column, such as `lower(email)`, routes.

### 11 — This shard index cannot route advisory locks.

```sql theme={null}
SELECT __neki.set_data_topology($${
  "authoritative_shard_group": "meta",
  "shard_indexes": {
    "modulo_user_id": {"type": "modulo", "columns": ["user_id"], "index_params": {"modulus": "2"}}
  },
  "shard_groups": [
    {"uid": "meta", "key_ranges": [{"shard_uid": "shard-meta"}]},
    {"uid": "user_data", "default_shard_index": "modulo_user_id",
     "key_ranges": [{"shard_uid": "shard-a", "end": "80"}, {"shard_uid": "shard-b", "start": "80"}]}
  ],
  "databases": {"app": {"advisory_lock_shard_group": "user_data"}}
}$$, true);
```

The topology names `user_data` as the advisory-lock shard group, and `user_data` routes on a `modulo` shard index. Neki hashes advisory-lock keys with xxhash, so a multi-shard advisory-lock group must use an `xxhash` default shard index, as the Example schema's does.

### 41 — File COPY is unavailable for sharded tables.

Client `COPY TO STDOUT` works for sharded tables, reference tables, and materialized views, including text, CSV, and binary. The codes in this section are for file `COPY`.

```sql theme={null}
COPY users FROM '/tmp/users.csv';
```

File-based `COPY` against a sharded table. Use `COPY ... FROM STDIN` for a sharded table, or an unsharded table for file `COPY`.

### 43 — File COPY is unavailable for reference tables.

```sql theme={null}
COPY ref_countries FROM '/tmp/countries.csv';
```

File-based `COPY` into a reference table. Use `COPY ... FROM STDIN` instead.

### 44 — COPY FROM cannot maintain this table's secondary index.

```sql theme={null}
COPY public.accounts (account_id, label) FROM STDIN;
```

Depends on the topology: the rejection occurs because the target table owns an active global secondary index, whose lookup rows `COPY FROM` cannot maintain. Load the table with `INSERT` instead.

### 45 — COPY does not support a multi-column primary shard index.

```sql theme={null}
COPY devices (owner_id, device_no) FROM STDIN;
```

`COPY FROM` routes each row on a single-column shard key, and `devices` is sharded on an expression over two columns.

### 46 — COPY FROM cannot evaluate this reference-table default consistently.

```sql theme={null}
ALTER TABLE ref_countries ADD COLUMN created_at timestamptz DEFAULT clock_timestamp();
COPY ref_countries (country_id, name) FROM STDIN;
```

The omitted column's default is volatile, and a reference table stores the same logical row in several shard groups, so each copy could receive a different value. Supply the column explicitly.

### 47 — COPY TO PROGRAM is unavailable.

```sql theme={null}
COPY users TO PROGRAM 'cat';
```

The `TO PROGRAM` destination is rejected; it would run a command on a Postgres host.

### 49 — COPY of a SELECT result is unavailable.

```sql theme={null}
COPY (SELECT user_id FROM users) TO STDOUT;
```

The parenthesized `SELECT` source is what is rejected. Copy a table, or run the query and handle the rows in your application.

### 50 — COPY FROM with a WHERE clause is unavailable.

```sql theme={null}
COPY public.notes FROM STDIN WHERE id > 1;
```

The `WHERE` clause on `COPY FROM` is rejected. Filter the data before feeding it to `COPY`.

### 51 — COPY FROM cannot evaluate this column default on the router.

```sql theme={null}
COPY users (user_id, name, email) FROM STDIN;
```

The column list omits `created_at`, whose default `now()` the router would have to evaluate per row. Include the column and supply its values.

### 52 — This COPY option is unavailable for sharded COPY.

```sql theme={null}
COPY users (user_id, name, email, created_at) FROM STDIN WITH (ON_ERROR ignore);
```

Depends on the topology: for a sharded target, `COPY` options such as `ON_ERROR`, `REJECT_LIMIT`, and `LOG_VERBOSITY` are rejected. The same option is accepted for an unsharded table.

### 53 — This input encoding is unavailable for sharded COPY.

```sql theme={null}
COPY users (user_id, name, email, created_at) FROM STDIN WITH (ENCODING 'SJIS');
```

The router frames the rows of a sharded `COPY` stream byte by byte, which is unsafe for an encoding such as `SJIS`, whose multi-byte characters can contain a backslash byte. Send UTF-8.

### 54 — COPY is unavailable in a multi-statement query.

```sql theme={null}
SELECT 1; COPY notes FROM STDIN;
```

`COPY` must be the only statement in the query. The second statement in the batch is what makes this fail.

### 55 — COPY is unavailable in the extended query protocol.

```sql theme={null}
COPY notes FROM STDIN;
```

Reachable only over the extended query protocol — that is, when the client prepares and binds the statement. The same `COPY` works over the simple query protocol.

### 350 — Notify channel routing requires an xxhash shard index.

```sql theme={null}
SELECT __neki.set_data_topology($${
  "authoritative_shard_group": "meta",
  "shard_indexes": {
    "modulo_user_id": {"type": "modulo", "columns": ["user_id"], "index_params": {"modulus": "2"}}
  },
  "shard_groups": [
    {"uid": "meta", "key_ranges": [{"shard_uid": "shard-meta"}]},
    {"uid": "user_data", "default_shard_index": "modulo_user_id",
     "key_ranges": [{"shard_uid": "shard-a", "end": "80"}, {"shard_uid": "shard-b", "start": "80"}]}
  ],
  "databases": {"app": {"notify_shard_group": "user_data"}}
}$$, true);
```

As code 11, for `notify_shard_group`: Neki hashes notification channel names with xxhash, so a multi-shard notify group must use an `xxhash` default shard index.

### 531 — COPY FROM PROGRAM is unavailable.

```sql theme={null}
COPY users FROM PROGRAM 'cat users.csv';
```

Use `COPY ... FROM STDIN` and send the data from the client.

## Advisory locks

Codes raised when the router cannot place an advisory lock on a single shard, or take it at all.

### 60 — A session cannot hold session advisory locks on multiple shards.

```sql theme={null}
SELECT pg_advisory_lock(1);
SELECT pg_advisory_lock(2);
```

Depends on the data topology: `user_data` is the advisory-lock shard group, and keys 1 and 2 hash to different shards. A session holds its session-level locks on one shard, so release the first lock before taking one that lands elsewhere.

### 61 — Mixing session and transaction advisory locks is unavailable.

```sql theme={null}
SELECT pg_advisory_lock(1), pg_advisory_xact_lock(1) FROM users WHERE user_id = 1;
```

The statement mixes a session-scoped and a transaction-scoped advisory lock. Use one scope.

### 139 — Advisory locks are unavailable in this plan position.

```sql theme={null}
CREATE TABLE users_adv_lock AS SELECT pg_advisory_xact_lock(1);
```

The advisory-lock call lands in a plan position — the body of `CREATE TABLE AS` — where the router cannot take the lock. Take the lock in a separate statement.

## PL/pgSQL

Codes raised while translating or executing a PL/pgSQL routine on the router.

### 82 — Nested and array-slice assignment targets are unavailable in PL/pgSQL.

```sql theme={null}
DO $$DECLARE a int[]; BEGIN a[1:2] := ARRAY[1,2]; END$$;
```

A nested or array-slice assignment target in PL/pgSQL. Assign the whole variable, or a single subscript.

### 85 — Composite INTO targets are unavailable in PL/pgSQL.

```sql theme={null}
DO $$DECLARE c refcursor := 'c'; u users; BEGIN OPEN c FOR SELECT * FROM users; FETCH c INTO u; END$$;
```

The `FETCH` target `u` is a named composite (`users`) variable. Fetch into a `record` variable, or into a list of scalar variables.

### 86 — Record fields with varying runtime types are unavailable in PL/pgSQL.

```sql theme={null}
DO $$DECLARE r record; x text; BEGIN IF random() > 2 THEN SELECT 1 AS a INTO r; ELSE SELECT 'x'::text AS a INTO r; END IF; x := r.a; END$$;
```

`r.a` is an `integer` after one branch and `text` after the other, so the read has no single type. Give the field the same type on every path, for example with a cast.

### 87 — Function configuration parameters are unavailable for router-side execution.

```sql theme={null}
CREATE FUNCTION pinned_to_primary() RETURNS int LANGUAGE plpgsql SET __neki.target = 'primary' AS $$BEGIN RETURN 1; END$$;
SELECT pinned_to_primary();
```

The function's `SET` clause names a parameter the router cannot scope around a call, here the Neki session option `__neki.target`. Set it on the session instead.

### 88 — Polymorphic routines are unavailable for router-side execution.

```sql theme={null}
CREATE FUNCTION identity_of(anyelement) RETURNS anyelement LANGUAGE plpgsql AS $$BEGIN RETURN $1; END$$;
SELECT identity_of(1);
```

The routine's polymorphic signature (`anyelement`, `anyarray`, and similar) is what is rejected for router-side execution. Declare concrete argument types.

### 90 — This PL/pgSQL statement is unavailable.

```sql theme={null}
DO $$DECLARE x text; BEGIN GET DIAGNOSTICS x = PG_CONTEXT; END$$;
```

A PL/pgSQL statement form the executor has no implementation for, here `GET DIAGNOSTICS ... PG_CONTEXT`.

### 91 — EXIT to a block label is unavailable in PL/pgSQL.

```sql theme={null}
DO $$<<outer>> BEGIN EXIT outer; END$$;
```

`EXIT` naming a *block* label. `EXIT` from a loop, optionally naming the loop's label.

### 575 — RETURN QUERY EXECUTE of a FETCH or SQL EXECUTE into a function result with a domain column is unavailable unless the cursor was opened by a PL/pgSQL OPEN over a static query.

```sql theme={null}
CREATE DOMAIN positive_int AS int CHECK (VALUE > 0);
CREATE FUNCTION fetch_positive() RETURNS SETOF positive_int LANGUAGE plpgsql AS $$BEGIN RETURN QUERY EXECUTE 'FETCH ALL FROM c'; END$$;
BEGIN;
DECLARE c CURSOR FOR SELECT 1::positive_int;
SELECT * FROM fetch_positive();
```

The cursor was opened by SQL `DECLARE`, so the router cannot check its rows against the function's domain result. Open the cursor with a PL/pgSQL `OPEN` over a static query.

### 757 — DISTINCT, and an ORDER BY, GROUP BY or DISTINCT ON key naming the result column, are unavailable in a PL/pgSQL assignment that coerces its value or assigns an array element.

```sql theme={null}
DO $$DECLARE n int; BEGIN n := DISTINCT 1.5; END$$;
```

The assignment coerces its value (here `numeric` to `int`) and its query uses `DISTINCT`. Cast the value in the query, or assign without `DISTINCT`.

### 807 — RETURN QUERY in a PL/pgSQL function that returns SETOF record without OUT parameters is unavailable.

```sql theme={null}
CREATE FUNCTION all_users() RETURNS SETOF record LANGUAGE plpgsql AS $$BEGIN RETURN QUERY SELECT user_id, name FROM users; END$$;
SELECT * FROM all_users() AS t(user_id bigint, name varchar);
```

Declare `OUT` parameters, or return a named composite type.

### 837 — A record or composite INTO target of a non-constant EXECUTE command string is unavailable in PL/pgSQL.

```sql theme={null}
DO $$DECLARE r record; t text := 'users'; BEGIN EXECUTE 'SELECT * FROM ' || t || ' LIMIT 1' INTO r; END$$;
```

The `EXECUTE` command string is built at run time, so the router cannot know the shape of the record it fills. Select into scalar variables, or use a constant command string.

### 842 — A record loop target of a non-constant FOR IN EXECUTE command string is unavailable in PL/pgSQL.

```sql theme={null}
DO $$DECLARE r record; t text := 'users'; BEGIN FOR r IN EXECUTE 'SELECT * FROM ' || t LOOP END LOOP; END$$;
```

As code 837, for the loop variable of `FOR ... IN EXECUTE`.

### 844 — Reading a PL/pgSQL cursor over a data-modifying query or an EXPLAIN after search\_path, the role, or the shard or sidecar target changed since its OPEN is unavailable.

```sql theme={null}
DO $$DECLARE c refcursor; x text; BEGIN OPEN c FOR EXPLAIN SELECT * FROM users; SET search_path = pg_catalog; FETCH c INTO x; END$$;
```

The cursor is over an `EXPLAIN`, and `search_path` changed between its `OPEN` and the `FETCH`. Read the cursor before changing the setting.

### 857 — FETCH into a record variable requires the cursor to be opened by a PL/pgSQL OPEN or cursor FOR loop over a static query whose row shape, collations and nested record shapes included, equals a query the fetching function opens a cursor over; a cursor opened with a dynamic command string, by SQL DECLARE, or over a query of another shape has none.

```sql theme={null}
DO $$DECLARE c refcursor := 'c'; r record; BEGIN OPEN c FOR EXECUTE 'SELECT 1 AS a'; FETCH c INTO r; END$$;
```

The cursor was opened with a dynamic command string, so the router cannot know the shape of the record it fills. Open it over a static query, or fetch into scalar variables.

## Query shapes and routing

The largest group: codes raised while the planner turns a query into routed work. Many depend on the data topology, and the same statement can plan against an unsharded table and be rejected against a sharded one.

### 67 — Array-slice and multidimensional array assignment are unavailable.

```sql theme={null}
DO $$DECLARE a int[] := '{{1,2},{3,4}}'; BEGIN a[1] := 5; END$$;
```

Assigning one element of a multidimensional array is the rejected part, as is assigning an array slice. Replace the whole array value instead.

### 69 — Subqueries in VALUES are unavailable.

```sql theme={null}
VALUES ((SELECT 1));
```

A subquery inside a `VALUES` cell. Compute the value in a `SELECT` and feed it in, or use `INSERT ... SELECT`.

### 100 — Subqueries are unavailable in this DML clause.

```sql theme={null}
UPDATE users SET name = (SELECT 'x') FROM orders WHERE users.user_id = orders.user_id;
```

A subquery appears in a DML clause that cannot host one — here the `SET` value of an `UPDATE ... FROM`. Move the subquery into the `FROM` source and reference its column.

### 101 — Per-row router-only expression evaluation is unavailable here.

```sql theme={null}
UPDATE users SET name = 'x' WHERE current_setting(name, true) = 'foo';
```

The expression must be evaluated on the router, but its value depends on each row (`name` is a column), so the router cannot compute it before the statement is routed.

### 102 — This RETURNING expression extraction is unavailable.

```sql theme={null}
UPDATE users SET name = 'x' WHERE user_id = 1 RETURNING WITH (OLD AS o) now();
```

`RETURNING` mixes an `OLD`/`NEW` row alias with an expression the router must evaluate itself. Return the columns and compute the router-side expression in your application.

### 103 — This DML source join cannot be planned safely.

```sql theme={null}
DELETE FROM users USING orders WHERE users.created_at < orders.created_at + (extract(epoch from now()) || ' seconds')::interval;
```

The predicate bridging the DML target to its `USING`/`FROM` source is not something the planner can evaluate on one side, so the join cannot be executed safely.

### 104 — This mutation cannot maintain active secondary indexes.

```sql theme={null}
DELETE FROM accounts WHERE account_id = 1 RETURNING account_id;
```

Depends on the topology: `accounts` owns global secondary indexes, and this mutation shape cannot keep their lookup rows in step with the rows they point at.

### 106 — This window-function shape is unavailable across shards.

```sql theme={null}
SELECT p.user_id, row_number() OVER (PARTITION BY p.user_id ORDER BY p.product_id) FROM users u JOIN products p ON u.user_id % 2 = p.user_id % 2;
```

The window function would have to be computed after a cross-shard join, so no shard sees the whole partition. Rejected only when the query actually spans shards.

### 107 — Expression extraction is unavailable in this clause.

```sql theme={null}
SELECT count(*), date_trunc('day', now()) FROM users GROUP BY date_trunc('day', now());
```

A router-only expression (`now()`) appears in a clause the planner cannot lift it out of — here `GROUP BY`. Compute the value in your application and pass it as a parameter.

### 108 — This subquery shape is unavailable in ORDER BY.

```sql theme={null}
SELECT name FROM users WHERE nextval('public.my_sequence') > 0 ORDER BY (SELECT max(total_amount) FROM orders);
```

The `ORDER BY` subquery cannot be evaluated once up front because the volatile `WHERE` expression forces per-row router evaluation. Remove the volatile predicate, or sort in your application.

### 109 — This quantified-subquery shape is unavailable.

```sql theme={null}
SELECT * FROM users WHERE (user_id, name) > ALL(SELECT user_id, name FROM orders);
```

The quantified comparison uses a row constructor, so the `ALL` cannot be rewritten into a routable form. Compare a single column.

### 110 — This correlated-subquery shape cannot be decorrelated safely.

```sql theme={null}
SELECT u.user_id FROM users u WHERE u.name = (SELECT min(o.status) FROM orders o WHERE o.total_amount = u.user_id GROUP BY o.status);
```

The correlated subquery has its own `GROUP BY`, so decorrelating it would change which groups exist. Rewrite it as an explicit join.

### 111 — This lateral or range-function shape is unavailable.

```sql theme={null}
SELECT k FROM jsonb_each((SELECT '{"a": 1}'::jsonb)) AS s(k, v);
```

A subquery as the argument of a function in `FROM` is the rejected part. Compute the argument in a CTE or a join instead.

### 112 — This full outer join has no usable equality key.

```sql theme={null}
SELECT 1 FROM users u FULL JOIN products p ON u.user_id > p.user_id;
```

A full outer join needs an equality key it can hash on both sides; `>` does not give one. Join on an equality.

### 116 — This INSERT subquery shape is unavailable.

```sql theme={null}
INSERT INTO users (user_id, name) VALUES (1, 'a' || (SELECT 'b'));
```

The subquery is nested inside a larger expression in the `VALUES` cell rather than being the whole cell. Use `INSERT ... SELECT`.

### 117 — This INSERT SELECT shape is unavailable.

```sql theme={null}
INSERT INTO users (user_id, email, name) SELECT p.product_id, p.name, p.name FROM products p ON CONFLICT (user_id) DO UPDATE SET name = excluded.name;
```

The combination of an `INSERT ... SELECT` source with `ON CONFLICT DO UPDATE` against a sharded target returns this code, except when the source is one table in the same shard group and the inserted shard key is copied from that table's shard key. Insert explicit `VALUES`, or drop the `DO UPDATE`. `INSERT ... SELECT` into a table sharded by an expression over more than one column also returns this code. `INSERT ... VALUES` into that table returns code 10.

### 118 — These INSERT values cannot be materialized on the router.

```sql theme={null}
INSERT INTO users (user_id) VALUES (generate_series(1,3));
```

A set-returning function in a `VALUES` cell means the router cannot know the rows — and therefore the routing values — before executing. Expand the rows in your application, or use `INSERT ... SELECT`.

### 119 — This ON CONFLICT shape is unavailable.

```sql theme={null}
INSERT INTO orders (order_id, user_id, order_date, status) VALUES (91021, 91020, DATE '2026-09-09', 'new'), (91022, 91020, DATE '2026-09-09', 'held') ON CONFLICT (order_id) DO NOTHING RETURNING order_id, user_id;
```

Depends on the topology: the `ON CONFLICT` arbiter index (`order_id`) does not cover the table's shard key (`user_id`), so a conflict cannot be resolved on one shard. Arbitrate on an index that includes the shard key.

### 120 — This column default cannot be materialized on the router.

```sql theme={null}
ALTER TABLE ref_countries ADD COLUMN seq bigint GENERATED BY DEFAULT AS IDENTITY;
UPDATE ref_countries SET seq = DEFAULT;
```

A reference table's copies must all store the same value, so the router evaluates the default itself, and it cannot draw an identity value. Supply the value explicitly.

### 121 — This MERGE shape is unavailable.

```sql theme={null}
MERGE INTO users t USING (SELECT 1 AS user_id) s ON t.user_id = s.user_id WHEN MATCHED THEN UPDATE SET name = 'x';
```

The `MERGE` source is a derived table, so the planner cannot route the merge to the target's shards. Use a `MERGE` whose source is a table in the same shard group.

### 122 — This view cannot be routed safely.

```sql theme={null}
CREATE VIEW view_users AS SELECT user_id, name FROM users;
UPDATE view_users SET name = 'x' WHERE user_id = 1;
```

Depends on the topology: Neki does not route a write through a view whose underlying table is sharded, whatever the predicate. Write to the base table.

### 123 — This materialized view cannot be routed safely.

```sql theme={null}
CREATE MATERIALIZED VIEW mv_users AS SELECT user_id, name FROM users;
SELECT * FROM mv_users WHERE user_id = 1;
```

Depends on the topology. A materialized view over sharded data stores one shard's result in a single heap, which a read cannot reassemble.

### 124 — This set-operation shape is unavailable.

```sql theme={null}
SELECT account_id FROM accounts WHERE email = 'a@example.com' INTERSECT SELECT user_id FROM products WHERE user_id = 500;
```

`INTERSECT` and `EXCEPT` are not planned for application tables; only `UNION` is. This limit does not apply to statements Neki forwards unchanged.

### 125 — This grouping-set form is unavailable.

```sql theme={null}
SELECT name, count(*) FROM users GROUP BY GROUPING SETS ((name), ());
```

The `GROUPING SETS` (or `ROLLUP`/`CUBE`) form passes validation but has no cross-shard execution: each grouping set would need its own aggregation pass. Issue the grouping levels as separate queries.

### 126 — TABLESAMPLE is unavailable.

```sql theme={null}
SELECT * FROM users TABLESAMPLE bernoulli(50);
```

The `TABLESAMPLE` clause is rejected; a per-shard sample would not be a sample of the whole table. Sample with a `WHERE` predicate such as `random() < 0.5`.

### 128 — WHERE CURRENT OF is unavailable for DML.

```sql theme={null}
DELETE FROM users WHERE CURRENT OF c;
```

`WHERE CURRENT OF` identifies a row by cursor position, which the router does not track across shards. Delete or update by key.

### 129 — DISTINCT cannot safely evaluate this volatile record expression.

```sql theme={null}
SELECT DISTINCT ROW(random()) FROM users;
```

`DISTINCT` would have to compare a record value built from a volatile function, whose result changes per evaluation, so duplicate elimination is not well defined.

### 130 — This combination of select-list subqueries is unavailable.

```sql theme={null}
SELECT nextval('public.my_sequence') + (SELECT max(order_id) FROM orders WHERE orders.user_id = users.user_id) FROM users;
```

One select-list expression combines a router-only value with a correlated subquery; the two need incompatible evaluation strategies in the same slot. Split them into separate expressions or queries.

### 131 — TRUNCATE RESTART IDENTITY with CASCADE is unavailable.

```sql theme={null}
TRUNCATE users RESTART IDENTITY CASCADE;
```

`RESTART IDENTITY` together with `CASCADE` is rejected because the cascade set — and so the sequences to restart — is only known per shard. Truncate the tables explicitly.

### 132 — TRUNCATE RESTART IDENTITY cannot include descendant tables.

```sql theme={null}
CREATE TABLE logs (log_id bigint GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY);
CREATE TABLE logs_archive () INHERITS (logs);
TRUNCATE logs RESTART IDENTITY;
```

Depends on the schema: rejected when the table has descendants, since `TRUNCATE` would reach them and their sequences. Use `TRUNCATE ONLY`, or truncate each table separately.

### 133 — This whole-row reference is ambiguous to the SQL builder.

```sql theme={null}
SELECT json_agg(t.*) FROM (SELECT order_id AS t FROM orders) t;
```

The whole-row reference `t.*` is shadowed by a column also named `t`, so the SQL builder cannot tell which the query means. Rename the column or the alias.

### 135 — Aggregates over columns of an enclosing query are unavailable.

```sql theme={null}
SELECT (SELECT max(u.user_id)) FROM users u;
```

The aggregate's argument belongs to the enclosing query, so the aggregation happens at the outer level from inside the subquery. Compute the aggregate in the outer select list.

### 136 — Correlated column references are unavailable in this clause.

```sql theme={null}
SELECT user_id FROM users WHERE EXISTS (SELECT 1 FROM orders LIMIT users.user_id);
```

The correlated reference sits in `LIMIT` (or `OFFSET`), a clause that is evaluated once rather than per outer row. Pass the limit as a parameter.

### 138 — FETCH FIRST WITH TIES is unavailable across shards.

```sql theme={null}
SELECT name FROM users ORDER BY name FETCH FIRST 2 ROWS WITH TIES;
```

`WITH TIES` needs to know every row tied at the cut-off, which no single shard can determine. Use a plain `LIMIT`, or rank in a subquery and filter.

### 140 — Set-returning functions are unavailable in router-side ORDER BY.

```sql theme={null}
SELECT u.user_id FROM users u LEFT JOIN orders o ON u.user_id = o.user_id ORDER BY generate_series(u.user_id::int, (u.user_id + 1)::int) LIMIT 2;
```

A set-returning function in `ORDER BY` must be expanded on the router, which cannot happen while merging sorted streams from several shards. Move the expansion into the `FROM` clause.

### 141 — INSERT OVERRIDING USER VALUE is unavailable.

```sql theme={null}
CREATE TABLE audit_log (log_id bigint GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY, user_id bigint, action text);
INSERT INTO audit_log (user_id, action) OVERRIDING USER VALUE VALUES (100, 'login');
```

The `OVERRIDING USER VALUE` clause is the rejected part. Omit it, or use `OVERRIDING SYSTEM VALUE` where you need to write an `ALWAYS` identity column.

### 142 — Parameterized subscripts are unavailable in INSERT targets.

```sql theme={null}
INSERT INTO user_tags (user_id, tag_ids[$1]) VALUES (1, 2);
```

The subscript in the `INSERT` target list is a parameter, so the router does not know which element is being written at plan time. Use a literal subscript, or write the whole array.

### 143 — OLD and NEW references are unavailable in sharded INSERT ON CONFLICT RETURNING.

```sql theme={null}
INSERT INTO users (user_id, name, email) VALUES (1, 'a', 'a@example.com'), (900, 'b', 'b@example.com') ON CONFLICT (user_id) DO NOTHING RETURNING old.user_id;
```

Depends on the topology: for a sharded target, `OLD`/`NEW` aliases in the `RETURNING` of an `ON CONFLICT` statement are not available. Return plain columns.

### 144 — Router-only expressions are unavailable in sharded INSERT ON CONFLICT RETURNING.

```sql theme={null}
INSERT INTO users (user_id, name, email) VALUES (1, 'a', 'a@example.com'), (900, 'b', 'b@example.com') ON CONFLICT (user_id) DO NOTHING RETURNING now();
```

As code 143, for an expression the router must evaluate itself rather than an `OLD`/`NEW` reference. Return columns and compute the expression in your application.

### 145 — Updating an index column is unavailable.

```sql theme={null}
UPDATE users SET user_id = nextval('public.my_sequence') WHERE user_id = 1;
```

The statement assigns to `user_id`, which is the table's shard index column — changing it could move the row to another shard. Delete the row and insert the new one.

### 146 — This volatile router-only WHERE expression is unavailable.

```sql theme={null}
UPDATE users SET name = 'x' WHERE COALESCE(nextval('public.my_sequence') > 0, false);
```

The volatile router-only call is buried inside another expression in `WHERE`, so the router cannot evaluate it once and route on the result. Compute the value first and pass it in.

### 147 — This volatile router-only UPDATE assignment is unavailable.

```sql theme={null}
UPDATE users SET name = nextval('public.my_sequence')::text WHERE random() < 0.5;
```

Both the assigned value and the predicate are volatile router-only expressions, so the rows to change and the values to write cannot be determined together. Select the rows first, then update them by key.

### 149 — Window functions inside aggregates are unavailable.

```sql theme={null}
SELECT count(*), row_number() OVER () FROM products;
```

A plain aggregate and a window function appear in the same select list, which would need two incompatible aggregation passes across shards. Compute the window function over a subquery that does the aggregation.

### 150 — This ORDER BY expression is unavailable for VALUES.

```sql theme={null}
SELECT x FROM (VALUES (1),(2) ORDER BY column1 + 1) v(x);
```

The `ORDER BY` attached to a `VALUES` list is an expression rather than a plain column reference. Sort in the enclosing query instead.

### 151 — INTERSECT and EXCEPT subqueries are unavailable.

```sql theme={null}
SELECT cardinality(ARRAY(SELECT unnest(ARRAY['a','b']) INTERSECT SELECT unnest(ARRAY['b','c'])));
```

The subquery's top level is an `INTERSECT` (or `EXCEPT`). Rewrite it as a join or an `IN`/`NOT IN` predicate, or use array functions.

### 152 — This subquery statement type is unavailable.

```sql theme={null}
SELECT (VALUES (1));
```

A `VALUES` list used as a scalar subquery is not a `SELECT`. Write `(SELECT 1)` instead.

### 153 — ARRAY subqueries are unavailable.

```sql theme={null}
SELECT user_id FROM users ORDER BY ARRAY(SELECT order_id FROM orders WHERE user_id = 1);
```

An `ARRAY(...)` subquery the router would have to evaluate, here in `ORDER BY`, is the rejected part. Use `array_agg` over a normal subquery: `SELECT array_agg(order_id) FROM ...`.

### 576 — A whole-row reference to a join is unavailable.

```sql theme={null}
SELECT j FROM (users u JOIN orders o ON u.user_id = o.user_id) AS j;
```

`j` names the whole row of an aliased join. Select the columns you need instead.

### 597 — Nested grouped expressions in SELECT targets are unavailable.

```sql theme={null}
SELECT count(*) + (random() + length(name)) FROM users GROUP BY random() + length(name);
```

The grouped expression appears nested inside a larger target expression. Select the grouped expression on its own, or group by a column.

### 733 — Tables "…" from different schemas cannot both appear unaliased in one query.

```sql theme={null}
CREATE SCHEMA archive;
CREATE TABLE archive.notes (id bigint PRIMARY KEY, body text);
SELECT 1 FROM public.notes, archive.notes;
```

Both tables are named `notes`. Give one of them an alias.

### 739 — JSON\_ARRAY(query) evaluated by the router is unavailable.

```sql theme={null}
SELECT JSON_ARRAY(SELECT 1);
```

`JSON_ARRAY` over a subquery, evaluated by the router. Use `json_agg` over the subquery instead.

### 822 — A LATERAL XMLTABLE or JSON\_TABLE is unavailable.

```sql theme={null}
SELECT * FROM users u, LATERAL JSON_TABLE(to_jsonb(u.name), '$' COLUMNS (a text PATH '$')) jt;
```

A `LATERAL` `JSON_TABLE` or `XMLTABLE` in `FROM` is the rejected part.

### 824 — Table "…" shares its name with another table in the query, which a reference past a column alias list needs to tell apart.

```sql theme={null}
SELECT (SELECT a FROM orders u LIMIT 1) FROM users u(a) WHERE u.a = 1;
```

The outer `users u(a)` renames its first column to `a`, and the subquery's `orders u` reuses the name `u`. Give the two tables distinct aliases.

### 945 — Table "…" inside an aliased join cannot share its name with another table in the query.

```sql theme={null}
SELECT 1 FROM (public.users JOIN orders USING (user_id)) j, pg_catalog.pg_class users;
```

`public.users` sits inside the aliased join `j`, and the alias `users` names another table. Give the tables distinct names.

## Evaluation engine

Codes raised when the router — rather than a shard — has to compute an expression and has no implementation for it. A query that a shard can evaluate on its own is unaffected.

### 64 — Set-returning function … has no opcode factory.

```sql theme={null}
SELECT * FROM pg_lock_status();
```

The router has no implementation of this set-returning built-in function, so it cannot run it.

### 200 — Built-in function … is unavailable in the evaluation engine.

```sql theme={null}
SELECT pg_relation_size('users');
```

The built-in function has a catalog entry but no router implementation, so it cannot be evaluated when the router — rather than a shard — has to compute it.

### 202 — Built-in type input function … is unavailable in the evaluation engine.

```sql theme={null}
SELECT 'x'::text::gtsvector;
```

The literal has to be parsed by `gtsvectorin`, which the evaluation engine does not implement, so the router cannot build the value. `brin_bloom_summary` and `brin_minmax_multi_summary` behave the same way.

## DDL

Codes raised by the DDL surface, mostly for objects that are local to one Postgres host and so would not reach a shard that joins the cluster later.

### 300 — Creating an operator class is unavailable.

```sql theme={null}
CREATE OPERATOR CLASS cls DEFAULT FOR TYPE int USING btree AS OPERATOR 1 <;
```

An operator class created through the router is not carried to a shard that joins the cluster later. Use one Postgres provides, or install one through an extension.

### 301 — Creating an operator family is unavailable.

```sql theme={null}
CREATE OPERATOR FAMILY fam USING btree;
```

As code 300: an operator family created here would not reach a later-joining shard.

### 302 — Creating a text search configuration is unavailable.

```sql theme={null}
CREATE TEXT SEARCH CONFIGURATION public.neki_config (COPY = pg_catalog.simple);
```

Router-side text-search evaluation supports only Postgres's built-in configurations, so a user-created one could not be used by a routed text-search expression.

### 303 — Creating a text search dictionary is unavailable.

```sql theme={null}
CREATE TEXT SEARCH DICTIONARY public.neki_simple (TEMPLATE = pg_catalog.simple);
```

As code 302, for a text-search dictionary.

### 304 — Event triggers are unavailable.

```sql theme={null}
CREATE EVENT TRIGGER e ON ddl_command_start EXECUTE FUNCTION f();
```

Event triggers fire on DDL, which the router executes on every shard, so the trigger's behavior is not well defined. `ALTER EVENT TRIGGER` is rejected the same way.

### 305 — Rewrite rules are unavailable.

```sql theme={null}
CREATE RULE r AS ON DELETE TO public.users DO INSTEAD NOTHING;
```

A rewrite rule would change a statement's meaning below the router, after planning and routing. Use a trigger, or do the rewrite in your application.

### 308 — Creating a tablespace is unavailable.

```sql theme={null}
CREATE TABLESPACE ts LOCATION '/tmp/ts';
```

A tablespace is storage local to one Postgres host, so it is not carried to a later-joining shard. Use the default tablespace.

### 309 — Creating an access method is unavailable.

```sql theme={null}
CREATE ACCESS METHOD am TYPE INDEX HANDLER f;
```

An access method needs a C handler local to the host and is not carried to a later-joining shard. Use a built-in method, or install one through an extension.

### 310 — Creating a collation is unavailable.

```sql theme={null}
CREATE COLLATION c (LOCALE = 'C');
```

A collation created through the router is not carried to a later-joining shard, and rows sorted with it could then compare differently per shard. Use a collation Postgres already provides.

### 311 — Creating a conversion is unavailable.

```sql theme={null}
CREATE CONVERSION cv FOR 'UTF8' TO 'LATIN1' FROM f;
```

As code 310, for an encoding conversion.

### 312 — Foreign data wrappers are unavailable.

```sql theme={null}
CREATE FOREIGN DATA WRAPPER w;
```

Foreign data wrappers reach outside the cluster from a single host. Import the data into a Neki table, or reach the remote system from your application. `ALTER FOREIGN DATA WRAPPER` is rejected the same way.

### 313 — Creating a foreign server is unavailable.

```sql theme={null}
CREATE SERVER srv FOREIGN DATA WRAPPER w;
```

A foreign server exists only to serve foreign data wrappers and foreign tables, neither of which Neki supports.

### 314 — Foreign tables are unavailable.

```sql theme={null}
CREATE FOREIGN TABLE ft (a int) SERVER srv;
```

A foreign table's data lives outside the cluster, so the router has no topology for it and cannot route to it.

### 315 — User mappings are unavailable.

```sql theme={null}
CREATE USER MAPPING FOR testuser SERVER srv;
```

User mappings apply only to foreign data wrappers, which Neki does not support. `ALTER` and `DROP USER MAPPING` are rejected the same way.

### 316 — Creating a text search parser is unavailable.

```sql theme={null}
CREATE TEXT SEARCH PARSER p (START = f, GETTOKEN = g, END = h, LEXTYPES = i);
```

A text-search parser needs host-local C functions and is not carried to a later-joining shard. Use the built-in parser, or one an extension installs.

### 317 — Creating a text search template is unavailable.

```sql theme={null}
CREATE TEXT SEARCH TEMPLATE tm (LEXIZE = f);
```

As code 316, for a text-search template.

### 318 — Creating a transform is unavailable.

```sql theme={null}
CREATE TRANSFORM FOR int LANGUAGE sql (FROM SQL WITH FUNCTION f(int), TO SQL WITH FUNCTION g(int));
```

A transform created through the router is not carried to a later-joining shard. Install one through an extension.

### 319 — Changing composite-type attributes is unavailable.

```sql theme={null}
ALTER TYPE pair ADD ATTRIBUTE third int;
```

Changing a composite type's attributes rewrites every table using that type, which the router does not coordinate across shards. Create a new type and migrate to it.

### 320 — Shared library loading is unavailable.

```sql theme={null}
LOAD 'libexample';
```

`LOAD` loads a shared library into one Postgres backend, and it is not carried to a later-joining shard. Install the library on every shard host before starting Neki.

### 321 — IMPORT FOREIGN SCHEMA is unavailable.

```sql theme={null}
IMPORT FOREIGN SCHEMA remote FROM SERVER srv INTO public;
```

`IMPORT FOREIGN SCHEMA` creates foreign tables, which Neki does not support (code 314).

### 323 — Per-column statistics overrides are unavailable.

```sql theme={null}
ALTER TABLE t ALTER COLUMN a SET STATISTICS 100;
```

A per-column statistics target is planner tuning local to one Postgres instance, so setting it through the router would not apply uniformly.

### 324 — Per-column foreign-table options are unavailable.

```sql theme={null}
ALTER FOREIGN TABLE ft ALTER COLUMN a OPTIONS (ADD b 'c');
```

Per-column foreign-table options presuppose foreign tables, which Neki does not support (code 314).

### 325 — This function language is unavailable.

```sql theme={null}
CREATE FUNCTION f() RETURNS int LANGUAGE c AS 'lib', 'sym';
```

The function's language is the rejected part: only SQL and PL/pgSQL are supported. A language other than those needs a handler local to the host.

### 326 — This procedure language is unavailable.

```sql theme={null}
CREATE PROCEDURE p() LANGUAGE plperl AS 'return 1';
```

As code 325, for `CREATE PROCEDURE`.

### 327 — CREATE LANGUAGE with a handler is unavailable.

```sql theme={null}
CREATE LANGUAGE l HANDLER h;
```

The `HANDLER` clause names a host-local C function. Install the language through a supported extension instead.

### 328 — Renaming a domain constraint is unavailable.

```sql theme={null}
ALTER DOMAIN positive_int RENAME CONSTRAINT positive_int_check TO positive_check;
```

Renaming a domain constraint is rejected, while `ALTER DOMAIN ... RENAME TO`, `SET SCHEMA`, and `OWNER TO` are supported.

### 329 — ALTER DOMAIN is unavailable.

```sql theme={null}
ALTER DOMAIN positive_int SET DEFAULT 1;
```

Changing a domain's default or constraints affects every column using the domain, across shards, so the statement is rejected.

### 330 — The IS\_TEMPLATE database option is unavailable.

```sql theme={null}
CREATE DATABASE db IS_TEMPLATE true;
```

The `IS_TEMPLATE` option is what is rejected; `ALTER DATABASE ... IS_TEMPLATE` reports the same code.

### 331 — Database tablespace selection is unavailable.

```sql theme={null}
CREATE DATABASE db TABLESPACE ts;
```

The `TABLESPACE` option on a database selects host-local storage (code 308). `ALTER DATABASE ... SET TABLESPACE` reports the same code.

### 332 — This database locale is unavailable to the router.

```sql theme={null}
CREATE DATABASE db LOCALE 'xx_not_a_locale';
```

The named locale cannot be resolved by the routers, so the database would exist in a state the router could not read back. Use a locale every router provides, or the `icu` or `builtin` locale provider.

### 333 — Renaming an enum label used as a sharding key is unavailable.

```sql theme={null}
ALTER TYPE region RENAME VALUE 'eu' TO 'europe';
```

`regional_users` is sharded on the enum column `region`, so renaming a label would change where existing rows route. The same statement is allowed for an enum not used as a sharding key.

### 334 — Column storage selection is unavailable.

```sql theme={null}
ALTER TABLE t ALTER COLUMN a SET STORAGE EXTERNAL;
```

The `SET STORAGE` action is the rejected part: Neki cannot preserve a column's storage setting for a shard that joins the cluster later, so the setting would not be uniform across shards.

### 335 — Column compression selection is unavailable.

```sql theme={null}
ALTER TABLE t ALTER COLUMN a SET COMPRESSION pglz;
```

As code 334, for a column's compression method.

### 336 — Changing a routing column's generated expression is unavailable.

```sql theme={null}
ALTER TABLE regional_accounts ALTER COLUMN account_id SET EXPRESSION AS (region_id * 2000000 + local_id);
```

`account_id` is both a generated column and the shard key of `regional_accounts`. Changing or dropping its generation expression could send existing rows to another shard without moving them. The same change is allowed for a generated column that does not drive routing.

### 337 — Restarting an identity column through ALTER TABLE is unavailable.

```sql theme={null}
ALTER TABLE notes ADD COLUMN seq bigint GENERATED BY DEFAULT AS IDENTITY;
ALTER TABLE notes ALTER COLUMN seq RESTART;
```

Neki cannot reset the router's reservations for the identity sequence. Restart the column's owned sequence with `ALTER SEQUENCE` instead.

### 338 — Temporary tables, views and sequences are unavailable.

```sql theme={null}
CREATE TEMP TABLE session_cart (sku text PRIMARY KEY, quantity integer NOT NULL);
```

A temporary relation lives in the PostgreSQL backend that created it, and the router holds no shard backend for a session. The relation would be created on each shard's pooled backend, where no later statement could see it, so the statement is rejected instead. `CREATE TEMP TABLE ... AS`, `CREATE TEMP VIEW`, `CREATE TEMP SEQUENCE`, and a name qualified with `pg_temp` report the same code. Create a regular relation and drop it when done.

### 339 — Temporary types and domains are unavailable.

```sql theme={null}
CREATE TYPE pg_temp.mood AS ENUM ('happy', 'sad');
```

A temporary type would be created on each shard's pooled backend, where it outlives the session. Create a regular type and drop it when done.

### 340 — Temporary functions, procedures, aggregates and operators are unavailable.

```sql theme={null}
CREATE FUNCTION pg_temp.one() RETURNS int LANGUAGE sql AS 'SELECT 1';
```

A temporary routine would be created on each shard's pooled backend, where it outlives the session. Create a regular routine and drop it when done.

### 341 — Temporary statistics are unavailable.

```sql theme={null}
CREATE STATISTICS pg_temp.users_stats ON user_id, name FROM users;
```

Temporary statistics would be created on each shard's pooled backend, where they outlive the session. Create regular statistics and drop them when done.

### 342 — This database encoding is unavailable to the router.

```sql theme={null}
CREATE DATABASE reports ENCODING 'EUC_TW';
```

The router reads and evaluates text as UTF-8. Use `UTF8`, the default, or another encoding the router supports.

### 343 — Converting an existing table to a typed table is unavailable.

```sql theme={null}
CREATE TYPE note_type AS (id bigint, body text);
ALTER TABLE notes OF note_type;
```

Neki cannot restore the typed-table relationship when a shard joins.

### 347 — Attaching or detaching partitions with different routing is unavailable.

```sql theme={null}
CREATE TABLE events (event_id bigint, event_date date) PARTITION BY RANGE (event_date);
CREATE TABLE events_2026 (event_id bigint, event_date date);
ALTER TABLE events ATTACH PARTITION events_2026 FOR VALUES FROM ('2026-01-01') TO ('2027-01-01');
```

Depends on the topology: the partition is not configured in the data topology, so attaching it would change how its rows are routed.

### 348 — Changing all triggers through ALTER TABLE is unavailable.

```sql theme={null}
ALTER TABLE users DISABLE TRIGGER ALL;
```

Neki cannot restore an `ALL` change to a shard that joins later. Name the trigger, or use the `USER` form.

### 351 — LISTEN, UNLISTEN and NOTIFY are unavailable in stored expressions shards evaluate.

```sql theme={null}
CREATE TABLE notifications AS SELECT pg_notify('c', 'p');
```

A stored expression runs on the shard that performs the write, not on the notification channel's shard, so the notification would never reach a listener.

### 515 — A recursive CTE with a composite-typed column is unavailable on the router.

```sql theme={null}
CREATE TYPE user_location AS (region_id integer, city text);
ALTER TABLE users ADD COLUMN location user_location;
WITH RECURSIVE r AS (SELECT u.user_id, u.location FROM users u WHERE u.user_id = 1 UNION ALL SELECT u.user_id, u.location FROM users u JOIN r ON u.location = r.location AND u.user_id = r.user_id + 1) SELECT * FROM r;
```

The recursive CTE crosses shards and carries a composite column, whose type the router cannot carry through this recursive plan.

### 554 — A multiply referenced MATERIALIZED CTE returning an anonymous record with a non-static shape is unavailable on the router.

```sql theme={null}
WITH c AS MATERIALIZED (SELECT CASE WHEN user_id > 0 THEN ROW(user_id) ELSE ROW(user_id, user_id) END AS r, user_id FROM users) SELECT r FROM c UNION ALL SELECT r FROM c;
```

The two references share a materialized result, but the record has a different number of fields in each `CASE` branch. Use a fixed record shape.

### 568 — Creating user-defined base types is unavailable.

```sql theme={null}
CREATE TYPE base_type (INPUT = int4in, OUTPUT = int4out, INTERNALLENGTH = 4);
```

Neki rejects both shell declarations and complete definitions of user-defined
base types. Install a custom base type through an extension available on every
shard.

### 573 — A data-modifying CTE statement that reads a written table outside a pre-write stage is unavailable on the router.

```sql theme={null}
WITH batch AS MATERIALIZED (SELECT user_id FROM users), updated AS (UPDATE users t SET name = 'x' FROM batch b WHERE t.user_id = b.user_id RETURNING 1) SELECT count(*), (SELECT count(*) FROM updated) FROM users WHERE name = 'x';
```

The outer query reads `users`, which the data-modifying CTE writes, outside the stage that runs before the write.

### 579 — This data-modifying CTE body is unavailable on the router.

```sql theme={null}
WITH m AS (MERGE INTO users t USING ref_countries s ON t.user_id = s.country_id WHEN MATCHED THEN UPDATE SET name = 'x' RETURNING t.user_id) SELECT * FROM m;
```

The `MERGE` body cannot be hosted by the router CTE materialization boundary.

### 626 — Refreshing a database collation version is unavailable.

```sql theme={null}
ALTER DATABASE postgres REFRESH COLLATION VERSION;
```

Neki manages collation versions across shards.

### 631 — Nested CTE name shadowing is unavailable on the router.

```sql theme={null}
WITH c AS (SELECT user_id FROM users WHERE user_id = 2) SELECT c.user_id, d.user_id AS du FROM c, (WITH c AS (SELECT user_id FROM orders WHERE user_id = 2) SELECT user_id FROM c) d;
```

The CTE `c` inside the derived table shadows the outer CTE `c`. Give the inner CTE a distinct name.

### 645 — A data-modifying CTE body whose output differs from its write's RETURNING columns is unavailable on the router.

```sql theme={null}
WITH upd AS (UPDATE users SET name='x' WHERE user_id IN (SELECT user_id FROM orders WHERE order_id IN (1,3)) RETURNING user_id, now()) SELECT * FROM upd;
```

The router-computed `now()` output does not match the write producer's `RETURNING` columns. This rejection depends on the query plan and data topology.

### 696 — ALTER EXTENSION membership changes are unavailable.

```sql theme={null}
ALTER EXTENSION plpgsql ADD TABLE notes;
```

Develop extension membership outside Neki, then apply an extension update with `ALTER EXTENSION ... UPDATE`.

### 700 — Changing base-type I/O options is unavailable.

```sql theme={null}
ALTER TYPE mood SET (RECEIVE = int4recv);
```

The `SET` option changes a base type's input or output functions. Neki cannot
refresh the router's type metadata after that change, so it rejects the
statement before it reaches a shard.

### 726 — A data-modifying CTE needing a join parameter at its execution fence is unavailable on the router.

```sql theme={null}
WITH a AS NOT MATERIALIZED (INSERT INTO users (user_id, name, email) VALUES (1, 'a', 'a@example.com') RETURNING user_id), b AS (UPDATE orders SET status = 'x' WHERE user_id IN (SELECT user_id FROM a) RETURNING order_id, user_id) SELECT count(*) FROM a JOIN b USING (user_id) WHERE a.user_id IN (SELECT user_id FROM a);
```

The `UPDATE` in `b` needs a value from `a` that is not available where the router runs it.

### 786 — A data-modifying CTE whose rows the statement reads, writing a table with triggers, is unavailable on the router.

```sql theme={null}
CREATE FUNCTION log_price_change() RETURNS trigger LANGUAGE plpgsql AS $$BEGIN RETURN NULL; END$$;
CREATE TRIGGER products_price_changed AFTER UPDATE ON products FOR EACH STATEMENT EXECUTE FUNCTION log_price_change();
WITH batch AS MATERIALIZED (SELECT product_id FROM products ORDER BY product_id LIMIT 10), updated AS (UPDATE products p SET price = p.price * 2 FROM batch b WHERE p.product_id = b.product_id RETURNING p.product_id, p.price) SELECT u.product_id, u.price, (SELECT count(*) FROM batch) AS batch_size FROM updated u;
```

The router runs this `UPDATE` once for each shard, so the statement-level trigger `products_price_changed` would fire more than once for one statement. Any trigger on the written table that fires for the CTE's command (`UPDATE` or `DELETE`) causes this rejection, also when the trigger is disabled. The router rejects the statement for the same reason when the written table:

* is referenced by a foreign key whose `ON UPDATE` or `ON DELETE` action for that command is `CASCADE`, `SET NULL` or `SET DEFAULT`, because PostgreSQL runs that action as a trigger;
* has a rewrite rule for that command. Neki rejects `CREATE RULE` (code 305), so only a rule created outside Neki can cause this;
* is a partition, a partitioned table, or an inheritance parent that the statement writes without `ONLY`.

Run the write as a statement of its own, or remove the trigger. This rejection depends on the data topology.

### 801 — A whole-row CTE reference with a target-list subquery is unavailable on the router.

```sql theme={null}
WITH c AS (SELECT EXISTS(SELECT 1 FROM users) AS e FROM orders) SELECT c FROM c;
```

The whole-row reference includes a subquery that the planner has extracted from the CTE target list. Select the individual columns instead of the whole row.

### 810 — A NOT MATERIALIZED SELECT CTE read more than once by a statement with a data-modifying CTE is unavailable on the router.

```sql theme={null}
WITH batch AS NOT MATERIALIZED (SELECT user_id FROM users WHERE user_id > 1 ORDER BY user_id LIMIT 10), updated AS (UPDATE users SET name = 'x' FROM batch WHERE users.user_id = batch.user_id RETURNING 1) SELECT coalesce(max(batch.user_id), 1), count(*), (SELECT count(*) FROM updated) FROM batch;
```

`batch` is read by the `UPDATE` and by the outer query. Mark it `MATERIALIZED`.

### 816 — A data-modifying CTE body without verifiable write output columns is unavailable on the router.

```sql theme={null}
WITH d AS (DELETE FROM orders o USING (SELECT 'x' AS s) v WHERE o.status = v.s) SELECT 1;
```

The router cannot verify the write output columns of this `DELETE ... USING` CTE, which has no `RETURNING`. This rejection depends on the data topology.

### 821 — A CTE exposing an unnamed subquery column is unavailable on the router.

```sql theme={null}
WITH c(x) AS MATERIALIZED (SELECT EXISTS(SELECT 1 FROM users) FROM orders) SELECT c.x FROM c;
```

The extracted subquery has no output name in the CTE body. Give the expression an explicit alias inside the CTE.

### 896 — SECURITY LABEL statements are unavailable.

```sql theme={null}
SECURITY LABEL ON TYPE mood IS 'classification';
```

Neki does not yet install a security label provider, so it rejects `SECURITY
LABEL` statements for all object types.

### 900 — This CREATE TABLE AS body cannot be populated safely.

```sql theme={null}
CREATE TABLE users_copy AS SELECT * FROM users;
```

Depends on the topology: the new table has no routing configured, so the router cannot tell where its rows would be read. Create the table `WITH NO DATA`, add routing for it, then fill it with `INSERT ... SELECT`.

A topology update is a complete replacement, not a partial patch. Retrieve the current document, add the new table to the shard group the source rows are read from — beside `users`, under `databases.app.schemas.public.tables` — and re-apply it:

```json theme={null}
"users_copy": { "shard_group": "user_data" }
```

The group's default shard index routes on `user_id`, which the copied table keeps.

### 902 — CREATE TABLE AS EXECUTE is unavailable.

```sql theme={null}
PREPARE recent_users AS SELECT user_id FROM users;
CREATE TABLE users_copy AS EXECUTE recent_users;
```

Write the prepared query into the `CREATE TABLE AS` in place of `EXECUTE`.

### 905 — GROUP BY or DISTINCT on column "…" of CTE "…", which an outer join inside the CTE body can null, is unavailable on the router.

```sql theme={null}
WITH c AS (SELECT o.user_id, u2.user_id AS uid FROM users u2 LEFT JOIN orders o ON u2.user_id = o.user_id) SELECT DISTINCT c.user_id FROM c LEFT JOIN users u ON u.user_id = c.uid;
```

`c.user_id` comes from the nullable side of the CTE's `LEFT JOIN`, and the outer query deduplicates on it. Mark the CTE `AS MATERIALIZED`, or write it as a derived table.

### 933 — This nested CTE inside a data-modifying statement is unavailable on the router.

```sql theme={null}
WITH a AS (SELECT user_id FROM users WHERE user_id = 1), b AS (SELECT user_id FROM a) UPDATE orders SET status = 'cascaded' FROM b WHERE orders.user_id = b.user_id AND orders.user_id = 1 RETURNING orders.order_id;
```

The CTE chain feeds a data-modifying statement. This rejection depends on the query plan and data topology.

### 961 — This nested recursive CTE is unavailable on the router.

```sql theme={null}
WITH RECURSIVE r AS (SELECT user_id FROM users WHERE user_id = 1 UNION ALL SELECT u.user_id FROM users u JOIN r ON u.user_id = r.user_id), cA AS (SELECT user_id FROM r) SELECT user_id FROM r UNION SELECT user_id FROM cA;
```

The recursive CTE is nested through another CTE reference. This rejection depends on the query plan and data topology.

### 967 — A nested CTE referenced more than once is unavailable on the router.

```sql theme={null}
WITH c1 AS (SELECT user_id FROM users WHERE name = 'x'), c2 AS MATERIALIZED (SELECT user_id FROM c1) SELECT user_id FROM c2 UNION ALL SELECT user_id FROM c2;
```

A CTE body references another CTE and is itself referenced more than once. This rejection depends on the query plan and data topology.

### 972 — An unreferenced data-modifying CTE that references another CTE is unavailable on the router.

```sql theme={null}
WITH a AS (UPDATE users SET name='a' WHERE user_id = 1 RETURNING user_id), b AS (UPDATE orders SET status='x' WHERE user_id IN (SELECT user_id FROM a) RETURNING order_id) SELECT 1;
```

The final query does not reference the second data-modifying CTE, whose body reads the first CTE. The router cannot execute this dependency chain as an unreferenced body.

### 981 — Changing a shard-index routing column is unavailable.

```sql theme={null}
ALTER TABLE users ALTER COLUMN user_id TYPE text;
```

`user_id` drives routing, and changing it would alter routing without moving existing rows.

### 992 — A data-modifying CTE body with ON CONFLICT is unavailable on the router.

```sql theme={null}
WITH x AS MATERIALIZED (INSERT INTO users (user_id, name, email) VALUES (1, 'a', 'b') ON CONFLICT (user_id) DO NOTHING RETURNING name) SELECT * FROM x;
```

The data-modifying CTE uses `ON CONFLICT`. This rejection depends on the query plan and data topology.

### 1018 — A data-modifying CTE returning an anonymous record with a non-static shape is unavailable on the router.

```sql theme={null}
WITH d AS (DELETE FROM users RETURNING CASE WHEN user_id % 2 = 1 THEN ROW(user_id) ELSE ROW(user_id, name) END AS r) SELECT r FROM d UNION ALL SELECT r FROM d;
```

The `RETURNING` record has a different number of fields in each `CASE` branch. The router cannot serialize this record through the CTE materialization boundary. Use a fixed record shape.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
