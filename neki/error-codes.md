---
url: https://planetscale.com/docs/neki/error-codes
title: "Error Codes"
description: ""
access_date: 2026-09-21T15:32:03.941Z
current_date: 2026-09-21T15:32:03.941Z
---

Neki is currently in Platform Preview. Platform Preview features are “Beta Features” under the PlanetScale Terms of Service or your applicable agreement with PlanetScale. Accordingly, Neki is subject to the limitations and disclaimers applicable to Beta Features and is not covered by any service level agreement.

When Neki’s router cannot plan or execute a statement because an implementation is still missing, it returns SQLSTATE `NK013` and a message in this form:

```text
not implemented: [108] This subquery shape is unavailable in ORDER BY.
```

The bracketed number is a stable catalog code. It identifies one specific implementation gap, and it keeps that meaning for the life of the product: a code is never reused for a different gap, even after the gap is closed. Quote it when you report a rejected query, and use it to match a rejection your application sees against the entry below.

`NK013` means the gap is one Neki intends to close. A limit that is a design decision rather than a missing implementation is reported as `not supported` instead, and the broad ones are listed in [Platform preview limitations](platform-preview-limitations.md).

## How to read an entry

Each entry gives the code, the message sentence the router renders after the bracketed code, an example that triggers it, and a note on which part of the query causes the rejection.

The examples use a small sharded schema: `users`, `orders`, and `products` are sharded on `user_id`, as is `user_tags`; `accounts` is sharded on `account_id` and owns [global secondary indexes](reference-tables-and-gsis.md) on `email` and `phone`; `notes` is unsharded; and tables named `ref_*` are [reference tables](reference-tables-and-gsis.md#reference-tables). A few entries name another small table whose own name describes the property that matters to that code. Where a rejection depends on how the data topology places the tables rather than on the SQL text, the note says so — the same statement can plan successfully with a different topology.

Codes that only an internal invariant can raise are not listed, because no query produces them. A code absent from this page is one an application is unlikely to meet.

A single statement can be rejected by more than one of these codes. Which one you see is the first gap the planner reaches, so fixing one can reveal another.

## Statements, transactions, and sessions

Codes raised while the router decides what a statement is and how the session should behave.

### 1 — SELECT INTO statements are unavailable.

```sql
SELECT user_id INTO users_copy FROM users WHERE user_id = 1;
```

The `INTO` clause is what Neki rejects. At the top level Postgres treats `SELECT ... INTO` as `CREATE TABLE AS`; write that form instead.

### 20 — This statement type is unavailable.

```sql
ALTER SYSTEM SET work_mem = '4MB';
```

The fallback for a statement no plan builder handles and for which the DDL surface has no more specific decision. A statement that the surface does recognize reports its own code instead.

### 21 — Reading an inheritance parent without ONLY is unavailable.

```sql
SELECT inheritance_id FROM inheritance_parent;
```

Depends on the schema: the rejection only occurs when `inheritance_parent` actually has child tables. Reading the parent without `ONLY` would have to expand to the children, so add `ONLY` to read just the parent.

### 22 — AND CHAIN is unavailable for this transaction command.

```sql
COMMIT AND CHAIN;
```

The `AND CHAIN` clause is the rejected part. Issue the `COMMIT` (or `ROLLBACK`, `END`, `ABORT`) and start the next transaction explicitly.

### 23 — This transaction command is unavailable.

```sql
SAVEPOINT s;
```

Raised for a transaction-control command the router has no handler for, as opposed to an unsupported option on a command it does handle.

### 24 — This transaction isolation level is unavailable.

```sql
BEGIN ISOLATION LEVEL SERIALIZABLE;
```

The isolation level named in the transaction characteristics is what is rejected, not the `BEGIN` itself.

### 25 — This transaction option is unavailable.

```sql
BEGIN DEFERRABLE;
```

A transaction option other than the isolation level — such as `DEFERRABLE` — is not accepted in the transaction characteristics.

### 26 — Atomic transaction mode is unavailable.

```sql
SET __neki.transaction_mode = 'atomic';
```

Atomic distributed transactions are not available; the session parameter hook rejects the value. Use the `single` or `multi` transaction mode.

### 27 — This SET statement variant is unavailable.

```sql
SET SESSION AUTHORIZATION 'someone';
```

A `SET` variant the router does not model, rather than an unsupported target variable.

### 28 — SET FROM CURRENT is unavailable.

```sql
ALTER DATABASE db SET work_mem FROM CURRENT;
```

The `FROM CURRENT` form is rejected; give the parameter an explicit value.

### 29 — SET TRANSACTION SNAPSHOT is unavailable.

```sql
SET TRANSACTION SNAPSHOT '00000003-0000001B-1';
```

Importing another session’s snapshot would have to be coordinated across every shard, so the statement is rejected outright.

### 30 — This statement cannot be analyzed with EXPLAIN neki\_plan.

```sql
EXPLAIN neki_plan LISTEN c;
```

The statement inside `EXPLAIN neki_plan` has no router plan to show. The statement itself may still be supported when run directly.

### 31 — Data-modifying CTEs cannot be analyzed with EXPLAIN neki\_plan.

```sql
EXPLAIN neki_plan WITH u AS (UPDATE users SET name = 'x' WHERE user_id = 1 RETURNING user_id) SELECT * FROM u;
```

The data-modifying CTE is the rejected part: `EXPLAIN neki_plan` does not render a plan for a statement whose CTE writes.

### 32 — This EXPLAIN option is unavailable.

```sql
EXPLAIN (BUFFERS) SELECT user_id FROM users;
```

An option in the `EXPLAIN` option list is not supported by the router’s `EXPLAIN` handling.

### 33 — This EXPLAIN format is unavailable.

```sql
EXPLAIN (FORMAT YAML) SELECT user_id FROM users;
```

The requested output format is what is rejected. Use a supported `FORMAT`.

## Topology and evaluation context

Codes that depend on how the data topology is configured, or on the context the router is evaluating in.

### 2 — Replication from a replica is unavailable.

```sql
SET __neki.target = 'replica';
CREATE PUBLICATION p FOR TABLE users;
```

Raised when a replication connection is pinned to a replica by the `__neki.target` session option. Remove the option or set it to `primary`. This code is fatal and ends the connection.

### 10 — Multi-column partitioning indexes are unavailable.

```sql
INSERT INTO multiple_column_primary_index (user_id, tenant_id) VALUES (1, 2);
```

Not a property of the query text: the rejection comes from the table’s shard index having more than one column, or a column that is an expression such as `user_id + tenant_id`. Any statement that has to route on that index is rejected.

### 11 — This shard index cannot route advisory locks.

```sql
SELECT pg_advisory_lock(1) FROM users WHERE user_id = 1;
```

Depends on the data topology. A shard index whose routing values cannot be derived from an advisory-lock key cannot place the lock on a single shard.

### 40 — COPY TO is unavailable for sharded tables.

```sql
COPY users TO STDOUT;
```

`COPY TO` is rejected because `users` is sharded — the direction of the copy plus the table’s placement, not the syntax. `COPY TO` supports unsharded tables only.

### 41 — File COPY is unavailable for sharded tables.

```sql
COPY users FROM '/tmp/users.csv';
```

File-based `COPY` against a sharded table. Use `COPY ... FROM STDIN` for a sharded table, or an unsharded table for file `COPY`.

### 42 — COPY TO is unavailable for reference tables.

```sql
COPY ref_countries TO STDOUT;
```

Same as code 40, for a reference table: the copy would have to pick one of the shard groups holding a copy of the rows.

### 43 — File COPY is unavailable for reference tables.

```sql
COPY ref_countries FROM '/tmp/countries.csv';
```

File-based `COPY` into a reference table. Use `COPY ... FROM STDIN` instead.

### 44 — COPY FROM cannot maintain this table’s secondary index.

```sql
COPY public.accounts (account_id, label) FROM STDIN;
```

Depends on the topology: the rejection occurs because the target table owns an active global secondary index, whose lookup rows `COPY FROM` cannot maintain. Load the table with `INSERT` instead.

### 45 — COPY does not support a multi-column primary shard index.

```sql
COPY multiple_column_primary_index (user_id, tenant_id) FROM STDIN;
```

Depends on the topology. `COPY FROM` into a sharded table needs a single-column shard key to route each row; a multi-column primary shard index is rejected.

### 46 — COPY FROM cannot evaluate this reference-table default consistently.

```sql
COPY ref_countries (country_id, name) FROM STDIN;
```

The omitted column’s default is volatile, and a reference table stores the same logical row in several shard groups, so each copy could receive a different value. Supply the column explicitly.

### 47 — COPY TO PROGRAM is unavailable.

```sql
COPY users TO PROGRAM 'cat';
```

The `TO PROGRAM` destination is rejected; it would run a command on a Postgres host.

### 48 — COPY from a query is unavailable.

```sql
COPY (SELECT user_id FROM users) FROM STDIN;
```

Copying *from* a query is not a supported source.

### 49 — COPY of a SELECT result is unavailable.

```sql
COPY (SELECT user_id FROM users) TO STDOUT;
```

The parenthesized `SELECT` source is what is rejected. Copy a table, or run the query and handle the rows in your application.

### 50 — COPY FROM with a WHERE clause is unavailable.

```sql
COPY public.notes FROM STDIN WHERE id > 1;
```

The `WHERE` clause on `COPY FROM` is rejected. Filter the data before feeding it to `COPY`.

### 51 — COPY FROM cannot evaluate this column default on the router.

```sql
COPY notes_with_identity (msg) FROM STDIN;
```

The identity column `id` is omitted from the column list, and its default has to be drawn on the router per row. Include the column and supply its values.

### 52 — This COPY option is unavailable for sharded COPY.

```sql
COPY users (user_id, name) FROM STDIN WITH (ON_ERROR ignore);
```

Depends on the topology: for a sharded target, `COPY` options such as `ON_ERROR`, `REJECT_LIMIT`, and `LOG_VERBOSITY` are rejected. The same option is accepted for an unsharded table.

### 53 — This input encoding is unavailable for sharded COPY.

```sql
COPY users (user_id, name) FROM STDIN WITH (ENCODING 'LATIN1');
```

The input encoding of a sharded `COPY` stream is what is rejected; send UTF-8.

### 54 — COPY is unavailable in a multi-statement query.

```sql
SELECT 1; COPY notes FROM STDIN;
```

`COPY` must be the only statement in the query. The second statement in the batch is what makes this fail.

### 55 — COPY is unavailable in the extended query protocol.

```sql
COPY notes FROM STDIN;
```

Reachable only over the extended query protocol — that is, when the client prepares and binds the statement. The same `COPY` works over the simple query protocol.

### 56 — COPY FROM a materialized view is unavailable.

```sql
COPY mv_users FROM STDIN;
```

The source is a materialized view rather than a table, so there is nothing for `COPY FROM` to write into.

## Advisory locks

Codes raised when the router cannot place an advisory lock on a single shard, or take it at all.

### 60 — A session cannot hold session advisory locks on multiple shards.

```sql
SELECT pg_advisory_lock(1) FROM users WHERE user_id = 1;
SELECT pg_advisory_lock(2) FROM users WHERE user_id = 900;
```

Depends on the data topology: rejected only when the second key routes to a different shard than the session already holds a session-level lock on. Release the first lock before taking a lock that lands elsewhere.

### 61 — Mixing session and transaction advisory locks is unavailable.

```sql
SELECT pg_advisory_lock(1), pg_advisory_xact_lock(1) FROM users WHERE user_id = 1;
```

The statement mixes a session-scoped and a transaction-scoped advisory lock. Use one scope.

### 62 — Advisory locks are unavailable in this evaluation context.

```sql
SELECT pg_advisory_xact_lock(user_id) FROM users;
```

The advisory-lock call sits in an evaluation context the engine cannot take a lock from — for example inside an expression evaluated per row on the router rather than as a routed statement.

### 139 — Advisory locks are unavailable in this plan position.

```sql
CREATE TABLE users_adv_lock AS SELECT pg_advisory_xact_lock(1);
```

The advisory-lock call lands in a plan position — the body of `CREATE TABLE AS` — where the router cannot take the lock. Take the lock in a separate statement.

## PL/pgSQL

Codes raised while translating or executing a PL/pgSQL routine on the router.

### 80 — Composite FOREACH targets are unavailable in PL/pgSQL.

```sql
CREATE FUNCTION f() RETURNS void LANGUAGE plpgsql AS $$DECLARE r record; BEGIN FOREACH r IN ARRAY ARRAY[1] LOOP END LOOP; END$$;
```

The `FOREACH` loop variable is a composite (record) type. Loop over a scalar target.

### 81 — This FOREACH target type is unavailable in PL/pgSQL.

```sql
CREATE FUNCTION f() RETURNS void LANGUAGE plpgsql AS $$DECLARE c cursor FOR SELECT 1; BEGIN FOREACH c IN ARRAY ARRAY[1] LOOP END LOOP; END$$;
```

The declared type of the `FOREACH` target is one the translator cannot use as a loop variable.

### 82 — Nested and array-slice assignment targets are unavailable in PL/pgSQL.

```sql
CREATE FUNCTION f() RETURNS void LANGUAGE plpgsql AS $$DECLARE a int[]; BEGIN a[1:2] := ARRAY[1,2]; END$$;
```

A nested or array-slice assignment target in PL/pgSQL. Assign the whole variable, or a single subscript.

### 83 — Non-scalar assignment targets are unavailable in PL/pgSQL.

```sql
CREATE FUNCTION f() RETURNS void LANGUAGE plpgsql AS $$DECLARE r record; BEGIN r := ROW(1,2); END$$;
```

The assignment target is a record or other non-scalar. Assign to its fields through `SELECT ... INTO`, or keep the value scalar.

### 84 — Record-field references in embedded SQL are unavailable in PL/pgSQL.

```sql
CREATE FUNCTION f() RETURNS int LANGUAGE plpgsql AS $$DECLARE r record; BEGIN SELECT r.a INTO r; RETURN 1; END$$;
```

A record *field* is referenced inside an embedded SQL statement. Copy the field into a scalar variable first and reference that.

### 85 — Composite INTO targets are unavailable in PL/pgSQL.

```sql
CREATE FUNCTION f() RETURNS void LANGUAGE plpgsql AS $$DECLARE r users; BEGIN SELECT * INTO r FROM users WHERE user_id = 1; END$$;
```

The `INTO` target is a composite variable. Select into a list of scalar variables instead.

### 86 — Record fields with varying runtime types are unavailable in PL/pgSQL.

```sql
CREATE FUNCTION f() RETURNS int LANGUAGE plpgsql AS $$DECLARE r record; BEGIN FOR r IN SELECT 1 AS a UNION SELECT 'x' AS a LOOP END LOOP; RETURN 1; END$$;
```

A record field whose runtime type differs between iterations. Give the field one stable type, or declare a typed row variable.

### 87 — Function configuration parameters are unavailable for router-side execution.

```sql
CREATE FUNCTION f() RETURNS int LANGUAGE plpgsql SET work_mem = '4MB' AS $$BEGIN RETURN 1; END$$;
```

The `SET` clause attached to the routine is what blocks it: a function with configuration parameters cannot be executed on the router.

### 88 — Polymorphic routines are unavailable for router-side execution.

```sql
CREATE FUNCTION f(anyelement) RETURNS anyelement LANGUAGE plpgsql AS $$BEGIN RETURN $1; END$$;
```

The routine’s polymorphic signature (`anyelement`, `anyarray`, and similar) is what is rejected for router-side execution. Declare concrete argument types.

### 90 — This PL/pgSQL statement is unavailable.

```sql
CREATE FUNCTION f() RETURNS void LANGUAGE plpgsql AS $$BEGIN GET DIAGNOSTICS STACKED x = RETURNED_SQLSTATE; END$$;
```

A PL/pgSQL statement the executor has no implementation for, as opposed to an unsupported option on one it does run.

### 91 — EXIT to a block label is unavailable in PL/pgSQL.

```sql
CREATE FUNCTION f() RETURNS void LANGUAGE plpgsql AS $$<<outer>> BEGIN EXIT outer; END$$;
```

`EXIT` naming a *block* label. `EXIT` from a loop, optionally naming the loop’s label.

### 92 — A bare RAISE is unavailable in PL/pgSQL.

```sql
CREATE FUNCTION f() RETURNS void LANGUAGE plpgsql AS $$BEGIN RAISE; END$$;
```

A bare `RAISE`, which re-raises the current exception, has no supported meaning without exception handlers (code 89). Raise a named condition explicitly.

### 93 — RAISE USING is unavailable in PL/pgSQL.

```sql
CREATE FUNCTION f() RETURNS void LANGUAGE plpgsql AS $$BEGIN RAISE EXCEPTION 'bad' USING HINT = 'fix it'; END$$;
```

The `USING` option list on `RAISE` is what is rejected; the plain `RAISE EXCEPTION 'bad'` form is fine.

### 94 — RETURN NEXT without an expression requires OUT parameters.

```sql
CREATE FUNCTION f() RETURNS SETOF int LANGUAGE plpgsql AS $$BEGIN RETURN NEXT; END$$;
```

`RETURN NEXT` with no expression only has a meaning for a function with `OUT` parameters. Give `RETURN NEXT` a value, or declare `OUT` parameters.

### 95 — RETURN QUERY EXECUTE is unavailable in PL/pgSQL.

```sql
CREATE FUNCTION f() RETURNS SETOF int LANGUAGE plpgsql AS $$BEGIN RETURN QUERY EXECUTE 'SELECT 1'; END$$;
```

The `EXECUTE` form of `RETURN QUERY` builds its query string at run time, which the router cannot plan. Use `RETURN QUERY` with a static query.

## Query shapes and routing

The largest group: codes raised while the planner turns a query into routed work. Many depend on the data topology, and the same statement can plan against an unsharded table and be rejected against a sharded one.

### 63 — This join type is unavailable.

```sql
SELECT 1 FROM users u FULL JOIN products p ON u.user_id > p.user_id;
```

The join *type* required by the plan has no router implementation at this point — for example a nested-loop or batched nested-loop join asked to run a join type it does not execute.

### 67 — Array-slice and multidimensional array assignment are unavailable.

```sql
UPDATE user_tags SET tag_ids[1:2] = ARRAY[7,8] WHERE user_id = 1;
```

The array *slice* on the assignment target is the rejected part. Assign to a single subscript, or replace the whole array value.

### 69 — Subqueries in VALUES are unavailable.

```sql
VALUES ((SELECT 1));
```

A subquery inside a `VALUES` cell. Compute the value in a `SELECT` and feed it in, or use `INSERT ... SELECT`.

### 100 — Subqueries are unavailable in this DML clause.

```sql
UPDATE users SET name = (SELECT 'x') FROM orders WHERE users.user_id = orders.user_id;
```

A subquery appears in a DML clause that cannot host one — here the `SET` value of an `UPDATE ... FROM`. Move the subquery into the `FROM` source and reference its column.

### 101 — Per-row router-only expression evaluation is unavailable here.

```sql
UPDATE users SET name = 'x' WHERE current_setting(name, true) = 'foo';
```

The expression must be evaluated on the router, but its value depends on each row (`name` is a column), so the router cannot compute it before the statement is routed.

### 102 — This RETURNING expression extraction is unavailable.

```sql
UPDATE users SET name = 'x' WHERE user_id = 1 RETURNING WITH (OLD AS o) now();
```

`RETURNING` mixes an `OLD` / `NEW` row alias with an expression the router must evaluate itself. Return the columns and compute the router-side expression in your application.

### 103 — This DML source join cannot be planned safely.

```sql
DELETE FROM users USING orders WHERE users.created_at < orders.created_at + (extract(epoch from now()) || ' seconds')::interval;
```

The predicate bridging the DML target to its `USING` / `FROM` source is not something the planner can evaluate on one side, so the join cannot be executed safely.

### 104 — This mutation cannot maintain active secondary indexes.

```sql
DELETE FROM accounts WHERE account_id = 1 RETURNING account_id;
```

Depends on the topology: `accounts` owns global secondary indexes, and this mutation shape cannot keep their lookup rows in step with the rows they point at.

### 105 — This CTE execution shape is unavailable on the router.

```sql
WITH a AS (SELECT user_id FROM users WHERE user_id = 1), b AS (SELECT user_id FROM a) UPDATE orders SET status = 'x' FROM b WHERE orders.user_id = b.user_id;
```

The CTE chain — `b` reading `a`, then feeding a DML `FROM` — is a shape the router has no execution strategy for. Flatten the CTEs into one, or materialize the intermediate result in your application.

### 106 — This window-function shape is unavailable across shards.

```sql
SELECT p.user_id, row_number() OVER (PARTITION BY p.user_id ORDER BY p.product_id) FROM users u JOIN products p ON u.user_id % 2 = p.user_id % 2;
```

The window function would have to be computed after a cross-shard join, so no shard sees the whole partition. Rejected only when the query actually spans shards.

### 107 — Expression extraction is unavailable in this clause.

```sql
SELECT count(*), date_trunc('day', now()) FROM users GROUP BY date_trunc('day', now());
```

A router-only expression (`now()`) appears in a clause the planner cannot lift it out of — here `GROUP BY`. Compute the value in your application and pass it as a parameter.

### 108 — This subquery shape is unavailable in ORDER BY.

```sql
SELECT name FROM users WHERE nextval('public.my_sequence') > 0 ORDER BY (SELECT max(total_amount) FROM orders);
```

The `ORDER BY` subquery cannot be evaluated once up front because the volatile `WHERE` expression forces per-row router evaluation. Remove the volatile predicate, or sort in your application.

### 109 — This quantified-subquery shape is unavailable.

```sql
SELECT * FROM users WHERE (user_id, name) > ALL(SELECT user_id, name FROM orders);
```

The quantified comparison uses a row constructor, so the `ALL` cannot be rewritten into a routable form. Compare a single column.

### 110 — This correlated-subquery shape cannot be decorrelated safely.

```sql
SELECT u.user_id FROM users u WHERE u.name = (SELECT min(o.status) FROM orders o WHERE o.total_amount = u.user_id GROUP BY o.status);
```

The correlated subquery has its own `GROUP BY`, so decorrelating it would change which groups exist. Rewrite it as an explicit join.

### 111 — This lateral or range-function shape is unavailable.

```sql
SELECT * FROM users, LATERAL (VALUES (users.user_id)) t(x);
```

The `LATERAL` item is a `VALUES` list referencing the outer row, which the planner cannot turn into a routable join input. Use a `LATERAL (SELECT ...)`, or join on the expression directly.

### 112 — This full outer join has no usable equality key.

```sql
SELECT t1.user_id, t2.user_id FROM user_tags t1 FULL OUTER JOIN user_tags t2 ON ROW(t1.user_id::smallint) = ROW(t2.user_id::numeric);
```

A full outer join needs an equality key it can hash on both sides; the row-constructor comparison across different numeric types does not give one. Compare plain columns of the same type.

### 113 — This cross-shard join expression cannot be assigned to one input.

```sql
SELECT 1 FROM users u JOIN products p ON u.user_id + p.user_id = 5;
```

The join predicate mixes columns from both inputs in a way that cannot be attributed to either side, so neither shard can evaluate it. Rejected only when the join is actually cross-shard.

### 114 — This secondary-index routing shape is unavailable.

```sql
SELECT phone FROM accounts WHERE email = 'a@example.com' AND category = 2;
```

Depends on the topology: the planner found a global secondary index for the predicate but the routing shape it would need — for instance a non-covering or multi-index lookup — is not implemented.

### 115 — Multiple routing-parameter values are unavailable.

```sql
SELECT name FROM users WHERE user_id = ANY($1);
```

The routing decision would need more than one value from a single parameter. Pass one routing value, or expand the list into separate predicates.

### 116 — This INSERT subquery shape is unavailable.

```sql
INSERT INTO users (user_id, name) VALUES (1, 'a' || (SELECT 'b'));
```

The subquery is nested inside a larger expression in the `VALUES` cell rather than being the whole cell. Use `INSERT ... SELECT`.

### 117 — This INSERT SELECT shape is unavailable.

```sql
INSERT INTO users (user_id, email, name) SELECT p.product_id, p.name, p.name FROM products p ON CONFLICT (user_id) DO UPDATE SET name = excluded.name;
```

The combination of an `INSERT ... SELECT` source with `ON CONFLICT DO UPDATE` against a sharded target is not implemented. Insert explicit `VALUES`, or drop the `DO UPDATE`.

### 118 — These INSERT values cannot be materialized on the router.

```sql
INSERT INTO users (user_id) VALUES (generate_series(1,3));
```

A set-returning function in a `VALUES` cell means the router cannot know the rows — and therefore the routing values — before executing. Expand the rows in your application, or use `INSERT ... SELECT`.

### 119 — This ON CONFLICT shape is unavailable.

```sql
INSERT INTO orders (order_id, user_id, order_date, status) VALUES (91021, 91020, DATE '2026-09-09', 'new'), (91022, 91020, DATE '2026-09-09', 'held') ON CONFLICT (order_id) DO NOTHING RETURNING order_id, user_id;
```

Depends on the topology: the `ON CONFLICT` arbiter index (`order_id`) does not cover the table’s shard key (`user_id`), so a conflict cannot be resolved on one shard. Arbitrate on an index that includes the shard key.

### 120 — This column default cannot be materialized on the router.

```sql
INSERT INTO users (name) VALUES ('foo');
```

The omitted column is the shard key and its default can only be evaluated on a shard — but the router needs the value first in order to pick that shard. Supply the shard key explicitly.

### 121 — This MERGE shape is unavailable.

```sql
MERGE INTO users t USING (SELECT 1 AS user_id) s ON t.user_id = s.user_id WHEN MATCHED THEN UPDATE SET name = 'x';
```

The `MERGE` source is a derived table, so the planner cannot route the merge to the target’s shards. Use a `MERGE` whose source is a table in the same shard group.

### 122 — This view cannot be routed safely.

```sql
UPDATE view_users SET name = 'x' WHERE user_id = 1;
```

Depends on the topology: Neki does not route a write through a view whose underlying table is sharded, whatever the predicate. Write to the base table.

### 123 — This materialized view cannot be routed safely.

```sql
SELECT * FROM mv_users WHERE user_id = 1;
```

Depends on the topology. A materialized view over sharded data has no routing information of its own, so the planner cannot decide which shards hold its rows.

### 124 — This set-operation shape is unavailable.

```sql
SELECT account_id FROM accounts WHERE email = 'a@example.com' INTERSECT SELECT user_id FROM products WHERE user_id = 500;
```

`INTERSECT` and `EXCEPT` are not planned for application tables; only `UNION` is. This limit does not apply to statements Neki forwards unchanged.

### 125 — This grouping-set form is unavailable.

```sql
SELECT name, count(*) FROM users GROUP BY GROUPING SETS ((name), ());
```

The `GROUPING SETS` (or `ROLLUP` / `CUBE`) form passes validation but has no cross-shard execution: each grouping set would need its own aggregation pass. Issue the grouping levels as separate queries.

### 126 — TABLESAMPLE is unavailable.

```sql
SELECT * FROM users TABLESAMPLE bernoulli(50);
```

The `TABLESAMPLE` clause is rejected; a per-shard sample would not be a sample of the whole table. Sample with a `WHERE` predicate such as `random() < 0.5`.

### 127 — Explicit derived-table column names are unavailable.

```sql
SELECT x FROM (SELECT user_id FROM users) v(x);
```

The explicit column-name list `v(x)` on the derived table is the rejected part. Alias the columns inside the subquery with `AS` instead.

### 128 — WHERE CURRENT OF is unavailable for DML.

```sql
DELETE FROM users WHERE CURRENT OF c;
```

`WHERE CURRENT OF` identifies a row by cursor position, which the router does not track across shards. Delete or update by key.

### 129 — DISTINCT cannot safely evaluate this volatile record expression.

```sql
SELECT DISTINCT ROW(random()) FROM users;
```

`DISTINCT` would have to compare a record value built from a volatile function, whose result changes per evaluation, so duplicate elimination is not well defined.

### 130 — This combination of select-list subqueries is unavailable.

```sql
SELECT nextval('public.my_sequence') + (SELECT max(order_id) FROM orders WHERE orders.user_id = users.user_id) FROM users;
```

One select-list expression combines a router-only value with a correlated subquery; the two need incompatible evaluation strategies in the same slot. Split them into separate expressions or queries.

### 131 — TRUNCATE RESTART IDENTITY with CASCADE is unavailable.

```sql
TRUNCATE users RESTART IDENTITY CASCADE;
```

`RESTART IDENTITY` together with `CASCADE` is rejected because the cascade set — and so the sequences to restart — is only known per shard. Truncate the tables explicitly.

### 132 — TRUNCATE RESTART IDENTITY cannot include descendant tables.

```sql
TRUNCATE inheritance_parent RESTART IDENTITY;
```

Depends on the schema: rejected when the table has descendants, since `TRUNCATE` would reach them and their sequences. Use `TRUNCATE ONLY`, or truncate each table separately.

### 133 — This whole-row reference is ambiguous to the SQL builder.

```sql
SELECT json_agg(t.*) FROM (SELECT order_id AS t FROM orders) t;
```

The whole-row reference `t.*` is shadowed by a column also named `t`, so the SQL builder cannot tell which the query means. Rename the column or the alias.

### 135 — Aggregates over columns of an enclosing query are unavailable.

```sql
SELECT (SELECT max(u.user_id)) FROM users u;
```

The aggregate’s argument belongs to the enclosing query, so the aggregation happens at the outer level from inside the subquery. Compute the aggregate in the outer select list.

### 136 — Correlated column references are unavailable in this clause.

```sql
SELECT user_id FROM users WHERE EXISTS (SELECT 1 FROM orders LIMIT users.user_id);
```

The correlated reference sits in `LIMIT` (or `OFFSET`), a clause that is evaluated once rather than per outer row. Pass the limit as a parameter.

### 137 — Reading a sequence or index as a relation is unavailable.

```sql
SELECT * FROM public.my_sequence;
```

The relation named in `FROM` is a sequence (or an index), not a table. Read a sequence with `nextval` / `currval`, or query `pg_catalog` for its metadata.

### 138 — FETCH FIRST WITH TIES is unavailable across shards.

```sql
SELECT name FROM users ORDER BY name FETCH FIRST 2 ROWS WITH TIES;
```

`WITH TIES` needs to know every row tied at the cut-off, which no single shard can determine. Use a plain `LIMIT`, or rank in a subquery and filter.

### 140 — Set-returning functions are unavailable in router-side ORDER BY.

```sql
SELECT u.user_id FROM users u LEFT JOIN orders o ON u.user_id = o.user_id ORDER BY generate_series(u.user_id::int, (u.user_id + 1)::int) LIMIT 2;
```

A set-returning function in `ORDER BY` must be expanded on the router, which cannot happen while merging sorted streams from several shards. Move the expansion into the `FROM` clause.

### 141 — INSERT OVERRIDING USER VALUE is unavailable.

```sql
INSERT INTO audit_log (user_id, action) OVERRIDING USER VALUE VALUES (100, 'login');
```

The `OVERRIDING USER VALUE` clause is the rejected part. Omit it, or use `OVERRIDING SYSTEM VALUE` where you need to write an `ALWAYS` identity column.

### 142 — Parameterized subscripts are unavailable in INSERT targets.

```sql
INSERT INTO user_tags (user_id, tag_ids[$1]) VALUES (1, 2);
```

The subscript in the `INSERT` target list is a parameter, so the router does not know which element is being written at plan time. Use a literal subscript, or write the whole array.

### 143 — OLD and NEW references are unavailable in sharded INSERT ON CONFLICT RETURNING.

```sql
INSERT INTO users (user_id, name, email) VALUES (91035, 'a', 'a@x') ON CONFLICT (user_id) DO NOTHING RETURNING old.user_id;
```

Depends on the topology: for a sharded target, `OLD` / `NEW` aliases in the `RETURNING` of an `ON CONFLICT` statement are not available. Return plain columns.

### 144 — Router-only expressions are unavailable in sharded INSERT ON CONFLICT RETURNING.

```sql
INSERT INTO users (user_id, name, email) VALUES (1, 'a', 'a@x') ON CONFLICT (user_id) DO NOTHING RETURNING now();
```

As code 143, for an expression the router must evaluate itself rather than an `OLD` / `NEW` reference. Return columns and compute the expression in your application.

### 145 — Updating an index column is unavailable.

```sql
UPDATE users SET user_id = nextval('public.my_sequence') WHERE user_id = 1;
```

The statement assigns to `user_id`, which is the table’s shard index column — changing it could move the row to another shard. Delete the row and insert the new one.

### 146 — This volatile router-only WHERE expression is unavailable.

```sql
UPDATE users SET name = 'x' WHERE COALESCE(nextval('public.my_sequence') > 0, false);
```

The volatile router-only call is buried inside another expression in `WHERE`, so the router cannot evaluate it once and route on the result. Compute the value first and pass it in.

### 147 — This volatile router-only UPDATE assignment is unavailable.

```sql
UPDATE users SET name = nextval('public.my_sequence')::text WHERE random() < 0.5;
```

Both the assigned value and the predicate are volatile router-only expressions, so the rows to change and the values to write cannot be determined together. Select the rows first, then update them by key.

### 149 — Window functions inside aggregates are unavailable.

```sql
SELECT count(*), row_number() OVER () FROM products;
```

A plain aggregate and a window function appear in the same select list, which would need two incompatible aggregation passes across shards. Compute the window function over a subquery that does the aggregation.

### 150 — This ORDER BY expression is unavailable for VALUES.

```sql
SELECT x FROM (VALUES (1),(2) ORDER BY column1 + 1) v(x);
```

The `ORDER BY` attached to a `VALUES` list is an expression rather than a plain column reference. Sort in the enclosing query instead.

### 151 — INTERSECT and EXCEPT subqueries are unavailable.

```sql
SELECT cardinality(ARRAY(SELECT unnest(ARRAY['a','b']) INTERSECT SELECT unnest(ARRAY['b','c'])));
```

The subquery’s top level is an `INTERSECT` (or `EXCEPT`). Rewrite it as a join or an `IN` / `NOT IN` predicate, or use array functions.

### 152 — This subquery statement type is unavailable.

```sql
SELECT (INSERT INTO users (user_id) VALUES (1) RETURNING user_id);
```

The statement used as a subquery is not a `SELECT`. Put a data-modifying statement in a CTE instead of a scalar subquery position.

### 153 — ARRAY subqueries are unavailable.

```sql
SELECT ARRAY(SELECT user_id FROM users WHERE user_id < 10);
```

The `ARRAY(...)` subquery constructor is the rejected part. Use `array_agg` over a normal subquery: `SELECT array_agg(user_id) FROM ...`.

## Evaluation engine

Codes raised when the router — rather than a shard — has to compute an expression and has no implementation for it. A query that a shard can evaluate on its own is unaffected.

### 200 — This built-in function is unavailable in the evaluation engine.

```sql
SELECT eqsel(NULL, 0, NULL, 0);
```

The built-in function has a catalog entry but no router implementation, so it cannot be evaluated when the router — rather than a shard — has to compute it. Many of these are planner-support and selectivity functions with no meaning in a query.

### 202 — This built-in type input function is unavailable in the evaluation engine.

```sql
SELECT 'x'::gtsvector;
```

The literal has to be parsed by `gtsvectorin`, which the evaluation engine does not implement, so the router cannot build the value. `brin_bloom_summary` and `brin_minmax_multi_summary` behave the same way.

### 203 — This type cast is unavailable in the evaluation engine.

```sql
SELECT '1'::unknown::int2vector;
```

The cast itself has no implementation in the evaluation engine. Cast through a type the engine supports, or let the shard evaluate the expression.

### 210 — User-defined functions are unavailable in the evaluation engine.

```sql
CREATE FUNCTION double(int) RETURNS int LANGUAGE sql AS 'SELECT $1 * 2';
SELECT name FROM users WHERE user_id = double(2);
```

Rejected only when the call has to be evaluated on the router — here to derive the routing value. The same function called in a predicate a shard evaluates is fine.

### 211 — User-defined operators are unavailable in the evaluation engine.

```sql
CREATE OPERATOR === (LEFTARG = int, RIGHTARG = int, FUNCTION = int4eq);
SELECT name FROM users WHERE user_id === 1;
```

As code 210, for a user-defined operator the router would have to apply itself.

### 212 — User-defined aggregate transition functions are unavailable in the evaluation engine.

```sql
CREATE AGGREGATE my_sum(int) (SFUNC = int4pl, STYPE = int);
SELECT my_sum(user_id) FROM users;
```

A user-defined aggregate whose transition function the router would have to run in order to combine partial results from several shards. Rejected when the query spans shards.

### 213 — User-defined aggregate final functions are unavailable in the evaluation engine.

```sql
CREATE AGGREGATE my_sum(int) (SFUNC = int4pl, STYPE = int);
SELECT my_sum(user_id) FROM users;
```

As code 212, for the aggregate’s final function — the step that turns the combined transition state into the result.

### 214 — User-defined aggregate moving transition functions are unavailable in the evaluation engine.

```sql
CREATE AGGREGATE my_sum(int) (SFUNC = int4pl, STYPE = int, MSFUNC = int4pl, MINVFUNC = int4mi, MSTYPE = int);
SELECT my_sum(user_id) OVER (ORDER BY user_id ROWS 1 PRECEDING) FROM users;
```

The moving-aggregate transition function of a user-defined aggregate, needed for a window frame that slides. Use a built-in aggregate.

### 215 — User-defined aggregate moving inverse functions are unavailable in the evaluation engine.

```sql
CREATE AGGREGATE my_sum(int) (SFUNC = int4pl, STYPE = int, MSFUNC = int4pl, MINVFUNC = int4mi, MSTYPE = int);
SELECT my_sum(user_id) OVER (ORDER BY user_id ROWS 1 PRECEDING) FROM users;
```

As code 214, for the moving-aggregate inverse function, which removes rows leaving the frame.

### 216 — User-defined aggregate moving final functions are unavailable in the evaluation engine.

```sql
CREATE AGGREGATE my_sum(int) (SFUNC = int4pl, STYPE = int, MSFUNC = int4pl, MINVFUNC = int4mi, MSTYPE = int);
SELECT my_sum(user_id) OVER (ORDER BY user_id ROWS 1 PRECEDING) FROM users;
```

As code 214, for the moving-aggregate final function.

## DDL

Codes raised by the DDL surface, mostly for objects that are local to one Postgres host and so would not reach a shard that joins the cluster later.

### 300 — Creating an operator class is unavailable.

```sql
CREATE OPERATOR CLASS cls DEFAULT FOR TYPE int USING btree AS OPERATOR 1 <;
```

An operator class created through the router is not carried to a shard that joins the cluster later. Use one Postgres provides, or install one through an extension.

### 301 — Creating an operator family is unavailable.

```sql
CREATE OPERATOR FAMILY fam USING btree;
```

As code 300: an operator family created here would not reach a later-joining shard.

### 302 — Creating a text search configuration is unavailable.

```sql
CREATE TEXT SEARCH CONFIGURATION public.neki_config (COPY = pg_catalog.simple);
```

Router-side text-search evaluation supports only Postgres’s built-in configurations, so a user-created one could not be used by a routed text-search expression.

### 303 — Creating a text search dictionary is unavailable.

```sql
CREATE TEXT SEARCH DICTIONARY public.neki_simple (TEMPLATE = pg_catalog.simple);
```

As code 302, for a text-search dictionary.

### 304 — Event triggers are unavailable.

```sql
CREATE EVENT TRIGGER e ON ddl_command_start EXECUTE FUNCTION f();
```

Event triggers fire on DDL, which the router executes on every shard, so the trigger’s behavior is not well defined. `ALTER EVENT TRIGGER` is rejected the same way.

### 305 — Rewrite rules are unavailable.

```sql
CREATE RULE r AS ON DELETE TO public.users DO INSTEAD NOTHING;
```

A rewrite rule would change a statement’s meaning below the router, after planning and routing. Use a trigger, or do the rewrite in your application.

### 306 — Large objects are unavailable.

```sql
SELECT lo_create(0);
```

Large objects live in one Postgres host’s `pg_largeobject` and are not carried to a shard that joins later. Store the data in a Neki table or in object storage.

### 307 — Creating a procedural language is unavailable.

```sql
CREATE LANGUAGE plperl;
```

A procedural language created through the router is not carried to a later-joining shard. Use a built-in language, or one a supported extension installs.

### 308 — Creating a tablespace is unavailable.

```sql
CREATE TABLESPACE ts LOCATION '/tmp/ts';
```

A tablespace is storage local to one Postgres host, so it is not carried to a later-joining shard. Use the default tablespace.

### 309 — Creating an access method is unavailable.

```sql
CREATE ACCESS METHOD am TYPE INDEX HANDLER f;
```

An access method needs a C handler local to the host and is not carried to a later-joining shard. Use a built-in method, or install one through an extension.

### 310 — Creating a collation is unavailable.

```sql
CREATE COLLATION c (LOCALE = 'C');
```

A collation created through the router is not carried to a later-joining shard, and rows sorted with it could then compare differently per shard. Use a collation Postgres already provides.

### 311 — Creating a conversion is unavailable.

```sql
CREATE CONVERSION cv FOR 'UTF8' TO 'LATIN1' FROM f;
```

As code 310, for an encoding conversion.

### 312 — Foreign data wrappers are unavailable.

```sql
CREATE FOREIGN DATA WRAPPER w;
```

Foreign data wrappers reach outside the cluster from a single host. Import the data into a Neki table, or reach the remote system from your application. `ALTER FOREIGN DATA WRAPPER` is rejected the same way.

### 313 — Creating a foreign server is unavailable.

```sql
CREATE SERVER srv FOREIGN DATA WRAPPER w;
```

A foreign server exists only to serve foreign data wrappers and foreign tables, neither of which Neki supports.

### 314 — Foreign tables are unavailable.

```sql
CREATE FOREIGN TABLE ft (a int) SERVER srv;
```

A foreign table’s data lives outside the cluster, so the router has no topology for it and cannot route to it.

### 315 — User mappings are unavailable.

```sql
CREATE USER MAPPING FOR testuser SERVER srv;
```

User mappings apply only to foreign data wrappers, which Neki does not support. `ALTER` and `DROP USER MAPPING` are rejected the same way.

### 316 — Creating a text search parser is unavailable.

```sql
CREATE TEXT SEARCH PARSER p (START = f, GETTOKEN = g, END = h, LEXTYPES = i);
```

A text-search parser needs host-local C functions and is not carried to a later-joining shard. Use the built-in parser, or one an extension installs.

### 317 — Creating a text search template is unavailable.

```sql
CREATE TEXT SEARCH TEMPLATE tm (LEXIZE = f);
```

As code 316, for a text-search template.

### 318 — Creating a transform is unavailable.

```sql
CREATE TRANSFORM FOR int LANGUAGE sql (FROM SQL WITH FUNCTION f(int), TO SQL WITH FUNCTION g(int));
```

A transform created through the router is not carried to a later-joining shard. Install one through an extension.

### 319 — Changing composite-type attributes is unavailable.

```sql
ALTER TYPE pair ADD ATTRIBUTE third int;
```

Changing a composite type’s attributes rewrites every table using that type, which the router does not coordinate across shards. Create a new type and migrate to it.

### 320 — Shared library loading is unavailable.

```sql
LOAD 'libexample';
```

`LOAD` loads a shared library into one Postgres backend, and it is not carried to a later-joining shard. Install the library on every shard host before starting Neki.

### 321 — IMPORT FOREIGN SCHEMA is unavailable.

```sql
IMPORT FOREIGN SCHEMA remote FROM SERVER srv INTO public;
```

`IMPORT FOREIGN SCHEMA` creates foreign tables, which Neki does not support (code 314).

### 323 — Per-column statistics overrides are unavailable.

```sql
ALTER TABLE t ALTER COLUMN a SET STATISTICS 100;
```

A per-column statistics target is planner tuning local to one Postgres instance, so setting it through the router would not apply uniformly.

### 324 — Per-column foreign-table options are unavailable.

```sql
ALTER FOREIGN TABLE ft ALTER COLUMN a OPTIONS (ADD b 'c');
```

Per-column foreign-table options presuppose foreign tables, which Neki does not support (code 314).

### 325 — This function language is unavailable.

```sql
CREATE FUNCTION f() RETURNS int LANGUAGE c AS 'lib', 'sym';
```

The function’s language is the rejected part: only SQL and PL/pgSQL are supported. A language other than those needs a handler local to the host.

### 326 — This procedure language is unavailable.

```sql
CREATE PROCEDURE p() LANGUAGE plperl AS 'return 1';
```

As code 325, for `CREATE PROCEDURE`.

### 327 — CREATE LANGUAGE with a handler is unavailable.

```sql
CREATE LANGUAGE l HANDLER h;
```

The `HANDLER` clause names a host-local C function. Install the language through a supported extension instead.

### 328 — Renaming a domain constraint is unavailable.

```sql
ALTER DOMAIN positive_int RENAME CONSTRAINT positive_int_check TO positive_check;
```

Renaming a domain constraint is rejected, while `ALTER DOMAIN ... RENAME TO`, `SET SCHEMA`, and `OWNER TO` are supported.

### 329 — ALTER DOMAIN is unavailable.

```sql
ALTER DOMAIN positive_int SET DEFAULT 1;
```

Changing a domain’s default or constraints affects every column using the domain, across shards, so the statement is rejected.

### 330 — The IS\_TEMPLATE database option is unavailable.

```sql
CREATE DATABASE db IS_TEMPLATE true;
```

The `IS_TEMPLATE` option is what is rejected; `ALTER DATABASE ... IS_TEMPLATE` reports the same code.

### 331 — Database tablespace selection is unavailable.

```sql
CREATE DATABASE db TABLESPACE ts;
```

The `TABLESPACE` option on a database selects host-local storage (code 308). `ALTER DATABASE ... SET TABLESPACE` reports the same code.

### 332 — This database locale is unavailable to the router.

```sql
CREATE DATABASE db LOCALE 'xx_not_a_locale';
```

The named locale cannot be resolved by the routers, so the database would exist in a state the router could not read back. Use a locale every router provides, or the `icu` or `builtin` locale provider.

### 333 — Renaming an enum label used as a sharding key is unavailable.

```sql
ALTER TYPE mood RENAME VALUE 'sad' TO 'unhappy';
```

Depends on the topology: rejected only when that enum label is used as a sharding key, since renaming it would change where existing rows route. The same statement is allowed for an enum not used as a sharding key.

### 334 — Column storage selection is unavailable.

```sql
ALTER TABLE t ALTER COLUMN a SET STORAGE EXTERNAL;
```

The `SET STORAGE` action is the rejected part: Neki cannot preserve a column’s storage setting for a shard that joins the cluster later, so the setting would not be uniform across shards.

### 335 — Column compression selection is unavailable.

```sql
ALTER TABLE t ALTER COLUMN a SET COMPRESSION pglz;
```

As code 334, for a column’s compression method.

### 336 — Changing a routing column’s generated expression is unavailable.

```sql
ALTER TABLE regional_accounts ALTER COLUMN account_id
SET EXPRESSION AS (region_id * 1000000 + local_id);
```

Depends on the topology: `account_id` is both a generated column and the shard key. Changing or dropping its generation expression could send existing rows to another shard without moving them. The same change is allowed for a generated column that does not drive routing.

### 338 — Temporary tables, views and sequences are unavailable.

```sql
CREATE TEMP TABLE session_cart (sku text PRIMARY KEY, quantity integer NOT NULL);
```

A temporary relation lives in the PostgreSQL backend that created it, and the router holds no shard backend for a session. The relation would be created on each shard’s pooled backend, where no later statement could see it, so the statement is rejected instead. `CREATE TEMP TABLE ... AS`, `CREATE TEMP VIEW`, `CREATE TEMP SEQUENCE`, and a name qualified with `pg_temp` report the same code. Create a regular relation and drop it when done.
