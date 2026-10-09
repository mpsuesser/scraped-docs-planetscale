---
url: https://planetscale.com/docs/vitess/schema-changes/value-does-not-fit-column
title: "Value Does Not Fit Column"
description: ""
access_date: 2026-10-09T23:32:13.485Z
current_date: 2026-10-09T23:32:13.485Z
---

A deploy request can fail while copying a table because existing values do not fit the column’s new type.

PlanetScale copies the table in the background and inserts existing rows into a new table that uses the deployed schema. The copy runs under strict SQL mode, so a value that would be silently truncated or rounded in a lenient MySQL session is rejected instead. The column name in quotes is the one to fix.

| Error | Common cause |
| --- | --- |
| `Data truncated for column 'status'` (errno 1265) | Changing a column to `ENUM` or `SET` when some rows hold a value that is not in the list. Converting text to a number, or lowering `DECIMAL` scale. |
| `Data too long for column 'title'` (errno 1406) | Shortening a `VARCHAR` or `CHAR`, or changing `TEXT` to a smaller type, when some rows are longer than the new length. |
| `Out of range value for column 'score'` (errno 1264) | Changing to a smaller integer type, from `SIGNED` to `UNSIGNED` when negative values exist, or lowering `DECIMAL` precision. |
| `Incorrect integer value: 'abc' for column 'id'` (errno 1366) | Changing text to a numeric type when some rows are not numeric. Also `Incorrect string value` when changing to a character set that cannot store some existing characters. |
| `Incorrect datetime value: '1785419821' for column 'created'` (errno 1292) | Changing a text or numeric column to `DATE`, `DATETIME`, or `TIMESTAMP` when some rows are not valid dates. |
| `Invalid JSON text ... for column 'payload'` (errno 3140) | Changing a text column to `JSON` when some rows are not valid JSON. |

```text
vreplication: terminal error: task error: failed inserting rows: Data truncated for column 'visibility' at row 98 (errno 1265) (sqlstate 01000)
```

## How to fix it

Find the rows whose values do not fit the new definition, then update or delete them. For example:

```sql
-- Values not in the new ENUM list
SELECT id, visibility FROM groups WHERE visibility NOT IN ('public', 'private');

-- Values longer than the new VARCHAR(100)
SELECT id FROM posts WHERE CHAR_LENGTH(title) > 100;

-- Values outside the range of the new SMALLINT UNSIGNED
SELECT id FROM scores WHERE score < 0 OR score > 65535;

-- Values that are not valid JSON
SELECT id FROM events WHERE NOT JSON_VALID(payload);
```

Then [retry the failed tables](deploy-requests.md#retry-a-partially-failed-deploy). Retrying before every offending row is updated will cause another failure.

If the existing values are correct and the new type is too strict, change the column definition on your branch instead, and open a new deploy request.

The copy runs while your application keeps writing to the table. If the application writes a value that does not fit during the copy, the deploy fails with the same error. Make sure new writes also fit the new type before retrying.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
