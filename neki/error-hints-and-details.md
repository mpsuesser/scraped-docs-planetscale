---
url: https://planetscale.com/docs/neki/error-hints-and-details
title: "Error Hints And Details"
description: ""
access_date: 2026-09-28T06:33:45.799Z
current_date: 2026-09-28T06:33:45.799Z
---

Neki is currently in Platform Preview. Platform Preview features are “Beta Features” under the PlanetScale Terms of Service or your applicable agreement with PlanetScale. Accordingly, Neki is subject to the limitations and disclaimers applicable to Beta Features and is not covered by any service level agreement.

A Postgres error or notice can include more than its one-line message. The [protocol](https://www.postgresql.org/docs/current/protocol-error-fields.html) defines optional fields beside it, and two of them matter most to an application developer:

- **DETAIL** gives more facts about what went wrong.
- **HINT** suggests what to do about it.

Neki generally follows [Postgres’s own convention](https://www.postgresql.org/docs/current/error-style-guide.html) for these fields. The primary message stays short and factual, facts that do not fit on one line go in DETAIL, and advice goes in HINT. Configure the client tools and application logging you use against a Neki database to show DETAIL and HINT, and keep notices enabled so this guidance remains visible.

## What Neki puts in these fields

### Postgres errors keep their fields

Neki reproduces Postgres’s error messages, including their DETAIL and HINT text. An error that a shard’s Postgres raises reaches the client with its fields intact. Anything you rely on these fields for in Postgres works the same way on Neki:

```text
ERROR:  function unknown_fn(integer) does not exist
HINT:  No function matches the given name and argument types. You might need to add explicit type casts.
```

### Neki points to the Neki way of doing it

Some statements behave differently on a sharded database, and some have a Neki-specific replacement. When Neki rejects one of these, or answers it differently from single-node Postgres, the HINT usually names the Neki mechanism to use instead. The message on its own only says that something failed.

A plain `EXPLAIN` of a statement that needs router processing fails, and the hint names the Neki `EXPLAIN` option that shows the plan:

```sql
EXPLAIN SELECT user_id FROM users ORDER BY user_id;
```

```text
ERROR:  This statement requires Neki router processing
HINT:  Use \`EXPLAIN (NEKI_PLAN)\` (with ANALYZE to execute) to see Neki's plan
```

This error has SQLSTATE `NK017`. It marks an `EXPLAIN` shape that has no faithful single-server answer on a sharded topology, and the HINT is always set. See [Query planning](query-planning.md) for `NEKI_PLAN`.

Reading `pg_locks` from a [replica](replicas.md) session fails, because advisory locks exist only on the primary. The hint names the setting to change:

```text
ERROR:  reading pg_catalog.pg_locks under a non-primary session target
HINT:  advisory locks are held on the primary and are never replicated: set __neki.target to primary before the read
```

A replication connection must name the shard it reads from. The hint shows the connection option to add:

```text
FATAL:  replication connections must target a specific shard
HINT:  Set the target shard in the connection string, e.g. options=-c __neki.shard=<shard-uid>.
```

### Notices carry advice too

Neki also reports conditions that do not fail a statement as `NOTICE` or `WARNING` messages, and these can carry a hint of their own. When a database’s notifications span more than one shard, `pg_notification_queue_usage()` returns the fullest queue among them and adds this notice:

```text
NOTICE:  pg_notification_queue_usage() reports the fullest queue across this database's notify shard group
HINT:  For the per-shard breakdown, query __neki.notification_queue_usage().
```

Text-search notices use DETAIL for facts that do not fit in the primary message. When an input word is too long to index, Neki reports the limit separately:

```text
NOTICE:  word is too long to be indexed
DETAIL:  Words longer than 2047 characters are ignored.
```

### Where the fields are sparse

A query that hits an implementation gap still being worked on fails with SQLSTATE `NK013` and a catalog code, as described in [Error codes](error-codes.md). These rejections often carry no HINT. The catalog code and its entry are what tell you how to rewrite the query. The broad limits in [Platform preview limitations](platform-preview-limitations.md) also aren’t always reported with a hint. For everything else, expect Neki to use DETAIL and HINT the way Postgres does, and to point to a Neki mechanism where one exists.

## Keep hints and details visible

Look for these settings in the tools and code that talk to Neki:

- **`psql`.** `\set VERBOSITY terse` and `\set VERBOSITY sqlstate` drop DETAIL and HINT from every error and notice. Keep the default, `default`, or use `verbose`.
- **`client_min_messages`.** Setting it to `warning` or higher in a session, role, or connection string stops notices from reaching the client, including the DDL propagation notice. Leave it at the default, `notice`.
- **libpq-based clients.** `PQsetErrorVerbosity` with `PQERRORS_TERSE` or `PQERRORS_SQLSTATE` leaves DETAIL and HINT out of the formatted message. The fields are still available through `PQresultErrorField`.
- **Drivers and ORMs.** Most drivers expose the fields separately from the message, for example `hint` and `detail` on the error object, and many application error handlers log only the message. Log the DETAIL and HINT fields too, and forward notices to your logs if your driver delivers them through a callback.
- **Error-reporting and monitoring pipelines.** Make sure the fields survive when an error is serialized into your logs, alerts, or exception tracker.

When you report a problem with a rejected query, include the full error: the SQLSTATE, the message, and any DETAIL and HINT.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
