---
url: https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors/blob-in-unique-key
title: "Blob In Unique Key"
description: ""
access_date: 2026-10-01T18:05:48.418Z
current_date: 2026-10-01T18:05:48.418Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# BLOB_IN_UNIQUE_KEY schema lint error

> Fix primary keys that include BLOB or TEXT columns.

**Platform availability:** Vitess only

## What this error means

`BLOB_IN_UNIQUE_KEY` means a primary key includes a `BLOB` or `TEXT` column.

A common error message is:

```text theme={null}
table "documents" contains BLOB or TEXT column "body" in the primary key.
```

## Why PlanetScale rejects it

PlanetScale needs a stable and efficient unique row identity for online schema changes. `BLOB` and `TEXT` columns are not accepted for that row identity because they can be large and are typically indexed through prefixes rather than complete values.

## How to fix it

Use a surrogate primary key or a bounded column type that can be indexed fully.

Invalid:

```sql theme={null}
CREATE TABLE documents (
  body text NOT NULL,
  PRIMARY KEY (body(255))
);
```

Recommended:

```sql theme={null}
CREATE TABLE documents (
  id bigint unsigned NOT NULL,
  body text NOT NULL,
  PRIMARY KEY (id)
);
```

If the text value must be unique, consider adding a generated or application-managed hash column with a unique key after evaluating collision risk.

```sql theme={null}
CREATE TABLE documents (
  id bigint unsigned NOT NULL,
  body text NOT NULL,
  body_sha256 binary(32) NOT NULL,
  PRIMARY KEY (id),
  UNIQUE KEY documents_body_sha256_idx (body_sha256)
);
```

## Related docs

* [Changing primary and unique keys](../onlineddl-change-unique-keys.md)
* [MySQL compatibility](../../mysql-compatibility.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
