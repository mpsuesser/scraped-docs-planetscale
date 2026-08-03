---
url: https://planetscale.com/docs/vitess/operating-without-foreign-key-constraints
title: "Operating Without Foreign Key Constraints"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Operating without foreign key constraints

> A **foreign key** is a logical association of rows between two tables, in a parent-child relationship.

export const PlatformAvailability = ({current, vitess, postgres}) => {
  const docsHref = path => {
    if (!path) return path;
    const normalized = path.startsWith('/') ? path : `/${path}`;
    return normalized;
  };
  const labels = {
    vitess: 'Vitess',
    postgres: 'Postgres'
  };
  if (current === 'both') {
    return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
        <span data-engine="both" data-state="current" aria-current="true" className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
          Vitess and Postgres
        </span>
      </div>;
  }
  const hasVitess = current === 'vitess' || Boolean(vitess);
  const hasPostgres = current === 'postgres' || Boolean(postgres);
  const only = !(hasVitess && hasPostgres);
  const engines = [];
  if (current === 'vitess' || current === 'postgres') engines.push(current);
  if (hasVitess && current !== 'vitess') engines.push('vitess');
  if (hasPostgres && current !== 'postgres') engines.push('postgres');
  return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
      {engines.map(engine => {
    const isCurrent = current === engine;
    const href = docsHref(engine === 'vitess' ? vitess : postgres);
    const label = only ? `${labels[engine]} only` : labels[engine];
    const state = isCurrent || !href ? 'current' : 'link';
    if (isCurrent || !href) {
      return <span key={engine} data-engine={engine} data-state={state} aria-current={isCurrent ? 'true' : undefined} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
              {label}
            </span>;
    }
    return <a key={engine} href={href} data-engine={engine} data-state={state} title={`View ${labels[engine]} documentation`} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
            {label}
            <svg aria-hidden="true" width="12" height="12" viewBox="0 0 12 12" fill="none" className="shrink-0">
              <path d="M2.5 6h7M6.5 3l3 3-3 3" stroke="currentColor" strokeWidth="1.5" strokeLinecap="round" strokeLinejoin="round" />
            </svg>
          </a>;
  })}
    </div>;
};

<PlatformAvailability current="vitess" />

## Overview

A row in a "parent" table may be referenced by one or more rows in a "child" table. A foreign key typically suggests how you should `JOIN` tables in most queries. A table can also refer to itself, as a special case.

A **`FOREIGN KEY` *constraint* is a database construct**, an implementation that *forces* the foreign key relationship's integrity (referential integrity). Namely, it ensures that a child table can only reference a parent table when the appropriate row *exists* in the parent table. A constraint also prevents the existence of "orphaned rows" in different methods, as you'll see described soon.

At PlanetScale, we don't *recommend* using foreign key constraints. However, if you still want to use them, you can enable [support for foreign key constraints](foreign-key-constraints.md) in your database settings page.

We still encourage you to use the relational model and associate tables by "pointing" rows from one table to another with foreign keys, just not with the `CONSTRAINT ... FOREIGN KEY` definition.

We'll soon cover an example of a schema with and without foreign key constraints to clarify this slight difference.

## Why does PlanetScale not recommend constraints?

There are a few significant technical reasons why we do not recommend foreign key constraints for some applications:

* MySQL foreign keys introduce increased locking for data and metadata changes. Expect degraded performance, especially in high concurrency workloads.
* Once two tables engage in a foreign key constraint relationship, it is not possible to change the data types for the columns used by the foreign key. For example, it is impossible to change a column from `INT` to `BIGINT`, neither on a parent nor child table.
* As applications evolve, there is a need for schema refactoring. It is a more complex process involving more steps to refactor a schema containing foreign key constraints.
* `FOREIGN KEY` constraints are difficult to maintain once your data grows and is split over multiple database servers. This typically happens when you introduce functional partitioning/sharding and/or horizontal sharding.

<Note>
  You can still think in terms of foreign key relationships; of parent tables and child tables; of rows referencing each
  other. You can structure your tables in the exact same way without FOREIGN KEY constraints as you would with the
  constraints. It's just how these relationships are enforced that changes.
</Note>

To understand how to work without foreign key constraints, we must first understand foreign key functionality.

## Types and behavior of FOREIGN KEY constraints

Consider the following trivial parent-child table relationship:

```sql theme={null}
CREATE TABLE parent_table (
  id INT NOT NULL,
  PRIMARY KEY (id)
);

CREATE TABLE child_table (
  id INT NOT NULL,
  parent_id INT,
  PRIMARY KEY (id),
  KEY parent_id_idx (parent_id),
  CONSTRAINT `child_parent_fk` FOREIGN KEY (parent_id) REFERENCES parent_table(id) ON DELETE NO ACTION
);
```

<Tip>
  In MySQL, foreign keys are only implemented by the storage engine layer, namely the default and popular InnoDB engine.
  A FOREIGN KEY constraint isn't a separate entity. It lives within the child's table space.
</Tip>

`FOREIGN KEY` constraints are available for row deletion (`ON DELETE`) and row updates (`ON UPDATE`). In this document, we discuss `ON DELETE` as it is the more impactful of the two. The discussion is relevant to `ON UPDATE` constraints, as well.

Foreign keys further support three types of action (illustrated below for `ON DELETE`):

### `ON DELETE CASCADE`

This is the most greedy, or ambitious action type. If you `DELETE` a row from a parent table, any referencing rows in a child table are subsequently deleted within the same transaction. This operation runs recursively for all of a parent's children, as well as for their children, should they also employ `ON DELETE CASCADE`.

`ON DELETE CASCADE` is a risky and resource-consuming action. You intend to `DELETE` a single row, but end up deleting hundreds, thousands, or more, rows in multiple tables. What seemed like a simple transaction now turns into a massive operation, that involves excessive locking, increased MVCC overhead, impact on replication lag, and more.

But perhaps the greatest danger is the potential unexpected loss of data. Whether an unsuspecting developer simply assumes a `DELETE FROM parent_table WHERE id=3` will at most delete one row, down to surprising behavior such as in `REPLACE INTO` queries, which actually run an implicit `DELETE`, leading to mass destruction of data.

The use of `ON DELETE CASCADE` is controversial. Use it with great care. Consider using `NO ACTION` instead.

### `ON DELETE SET NULL`

With this setup, a `DELETE` on a parent (e.g. `DELETE FROM parent_table WHERE id=3`) will set the referencing column on children (e.g. `parent_id` column in `child_table`) to `NULL` for matching rows. It effectively leads to orphaned rows, not very differently from having no foreign key constraints at all.

One advantage is that it's easy to identify the orphan rows: those, and only those rows, will have `NULL` for parent-referencing columns.

Like `CASCADE`, a single row deletion on the parent may lead to multiple rows updated on child tables. This again may cause large transactions, excessive locking, and replication lag.

### `ON DELETE NO ACTION`

Possibly the single most important feature of foreign keys, a `DELETE` on a parent will fail if child rows exist that reference the parent's row. To `DELETE` a row from a parent, the app/user must first `DELETE` the referencing rows from all children. Recursively, if those are further referenced by other tables, the app/user must first `DELETE` rows from grandchildren, and so forth.

This action (or lack thereof) type forces the app to have stronger ownership of its data. An app written to work with `ON DELETE NO ACTION` will organically evolve *knowing* which tables reference which other tables, and will have established `DELETE`/`UPDATE` flows that iterate through tables in the correct order to satisfy referential integrity.

## How does your schema look without FOREIGN KEY constraints?

The above schema would look exactly the same, minus the `CONSTRAINT` clause:

```sql theme={null}
CREATE TABLE parent_table (
  id INT NOT NULL,
  PRIMARY KEY (id)
);

CREATE TABLE child_table (
  id INT NOT NULL,
  parent_id INT,
  PRIMARY KEY (id),
  KEY parent_id_idx (parent_id)
);
```

<Tip>
  Each FOREIGN KEY constraint requires an index covering the referenced column(s) on both sides of the connection. The index `_parent_id_idx` is **required** by the constraint. We can drop that key in our constraint-free table, depending on the type of queries we might use to retrieve data from the tables.
</Tip>

## Developing an app with no FOREIGN KEY constraints

Consider an app's behavior in an `ON DELETE NO ACTION` setup again: the app *knows* the table's dependencies, handles iteration order for `DELETE`/`UPDATE`. It does everything right.

What happens if we take away the `CONSTRAINT` at this point?

The app remains unchanged. It already runs the proper actions in the proper order. The app operation that ends up running the `DELETE FROM parent_table WHERE id=3` succeeds the app operations that `DELETE` the child table(s). While we lose the database's safety guarantee enforcing this behavior, we are perfectly capable of managing it on our own.

Likewise, an app that grows with a constraint-less schema organically learns to handle `DELETE` and `UPDATE` scenarios. It is in fact given some extra freedom because the *order* of operations is not enforced. This is an advantage because the app is not forced to `DELETE` thousands of dependent rows for each parent row deletion *at that same transaction*. The app may well postpone deletion as we discuss shortly.

Referential integrity is but one of many logical data integrity constraints. It just happens to be one that databases can enforce. Any sizeable application will maintain the integrity of its data with rules the database is unaware of.

## Cleaning up orphaned rows

Consider an `ON DELETE SET NULL` constraint. What happens to the child rows that were set to `NULL`? Typically, there is little or no damage in keeping them around. A query can, for example, `SELECT` rows `WHERE parent_id IS NOT NULL`. Better yet, child rows are often `JOIN`ed with their respective parent rows. Any query running such a `JOIN` will return empty since the parent row does not exist.

Eventually, those rows will pile up, and you will want to reclaim the space.

Without `FOREIGN KEY` constraints, the situation is very much the same, It is possible to `DELETE` a parent row without deleting its dependent children rows. A child row `JOIN`ed with a respective (deleted) parent row comes out empty. There is no `IS NOT NULL` to help you, but identifying those rows is still trivial. Similarly, there is little or no damage in keeping those rows around for a while.

And similarly, those rows eventually pile up.

A common practice, where appropriate, is to `DELETE` rows on a parent table, or perhaps also on a subset of children, but leave some other tables for offline batch processing.

At some convenient time, such as low traffic hours, the app or some batch job will purge orphaned rows. Consider this simplified query:

```sql theme={null}
DELETE FROM child_table LEFT JOIN parent_table ON (child_table.parent_id=parent_table.id) WHERE parent_table.id IS NULL
```

A single `DELETE` is likely a massive operation, which is to be avoided. A good practice is to break the statement into multiple small-scope statements, e.g. deleting `100` rows at a time.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
