---
url: https://planetscale.com/docs/vitess/mysql-compatibility
title: "Mysql Compatibility"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# MySQL compatibility

> PlanetScale Vitess is built on open-source Vitess, a database clustering system for horizontal scaling of MySQL.

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

<PlatformAvailability current="vitess" postgres="/postgres/postgres-compatibility" />

## Overview

PlanetScale databases run on MySQL 8. If you're [importing an existing database](imports/database-imports.md), PlanetScale supports MySQL database versions `5.7` through `8.0`.

New PlanetScale databases are created on MySQL 8 with character set `utf8mb4_0900_ai_ci`. PlanetScale supports `utf8`, `utf8mb4`, and `utf8mb3`, character sets. We also support `latin1` and `ascii` character sets, but do not recommend them.

## MySQL compatibility limitations

The following reference guide will cover some MySQL syntax, features, and more that PlanetScale either does not support or has limitations around. We are actively working on driving up compatibility, but it's an ongoing effort and will take some time to complete. See this [project board on GitHub](https://github.com/vitessio/vitess/projects/4) to learn what the Vitess team is currently focusing on.

If you're attempting to import a database using our Import tool, there are some additional requirements that you can find in our [Database imports documentation](foreign-key-constraints.md#limitations).

### Queries, functions, syntax, data types, and SQL modes

<Note>
  <Icon icon="exclamation" color="orange" /> = *Limitations in support*

  <Icon icon="xmark" color="red" /> = *Not supported*
</Note>

| Statement                     | Support                           | Description                                                                                                                                                                                                                                                |
| ----------------------------- | --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ALTER TABLE...RENAME COLUMN` | <Icon icon="xmark" color="red" /> | Renaming columns and tables may be destructive. See our [guide for column rename recommendations](schema-changes/handling-table-and-column-renames.md).                                                                                               |
| `CREATE DATABASE`             | <Icon icon="xmark" color="red" /> | You cannot `CREATE` a logical database within a PlanetScale Vitess database.                                                                                                                                                                               |
| `DROP DATABASE`               | <Icon icon="xmark" color="red" /> | You cannot `DROP` a logical database within a PlanetScale Vitess database.                                                                                                                                                                                 |
| `JSON_TABLE`                  | <Icon icon="xmark" color="red" /> | The [`JSON_TABLE` function](https://dev.mysql.com/doc/refman/8.0/en/json-table-functions.html#function_json-table) is not yet supported. All other [JSON SQL functions](https://dev.mysql.com/doc/refman/8.0/en/json-function-reference.html) should work. |
| `PROCEDURE`                   | <Icon icon="xmark" color="red" /> | We do not support any form of [stored routines](https://dev.mysql.com/doc/refman/8.0/en/stored-routines.html).                                                                                                                                             |
| `FUNCTION`                    | <Icon icon="xmark" color="red" /> | We do not support any form of [stored routines](https://dev.mysql.com/doc/refman/8.0/en/stored-routines.html).                                                                                                                                             |
| `TRIGGER`                     | <Icon icon="xmark" color="red" /> | We do not support any form of [stored routines](https://dev.mysql.com/doc/refman/8.0/en/stored-routines.html).                                                                                                                                             |
| `EVENT`                       | <Icon icon="xmark" color="red" /> | We do not support any form of [stored routines](https://dev.mysql.com/doc/refman/8.0/en/stored-routines.html).                                                                                                                                             |
| `LOAD DATA INFILE`            | <Icon icon="xmark" color="red" /> | Loading data via [`LOAD DATA INFILE` is not supported](https://github.com/vitessio/vitess/issues/2976).                                                                                                                                                    |
| `LOCK TABLES`                 | <Icon icon="xmark" color="red" /> | [`LOCK TABLES`](https://dev.mysql.com/doc/refman/8.0/en/lock-tables.html) is not supported.                                                                                                                                                                |
| `KILL`                        | <Icon icon="xmark" color="red" /> | We do not support killing queries or shards from the command line.                                                                                                                                                                                         |
| `:=`                          | <Icon icon="xmark" color="red" /> | The `:=` assignment operator is not yet supported.                                                                                                                                                                                                         |
| `SET GLOBAL time_zone`        | <Icon icon="xmark" color="red" /> | The global time zone is set to UTC and can not be modified.                                                                                                                                                                                                |
| `SET GLOBAL sql_mode`         | <Icon icon="xmark" color="red" /> | The global SQL mode can not be changed permanently. Set each new session's mode instead with `SET sql_mode`.                                                                                                                                               |
| `PIPES_AS_CONCAT`             | <Icon icon="xmark" color="red" /> | Enabling this SQL mode can interfere with Vitess' evalengine parsing the SQL queries so enabling it may result in incorrect or unexpected results. Please use MySQL's standard dialect instead, e.g. `CONCAT()`.                                           |
| `ANSI_QUOTES`                 | <Icon icon="xmark" color="red" /> | Enabling this SQL mode can interfere with Vitess' evalengine parsing the SQL queries so enabling it may result in incorrect or unexpected results. Please use MySQL's standard quotation instead.                                                          |
| `WITH RECURSIVE`              | <Icon icon="xmark" color="red" /> | Experimental support for recursive common table expressions (CTEs) was introduced in Vitess 21 for `SELECT` queries.                                                                                                                                       |

## Miscellaneous

| Action                        | Support                                    | Description                                                                                                                                                                                                                     |
| ----------------------------- | ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Empty schemas**             | <Icon icon="xmark" color="red" />          | Databases with empty schemas are invalid. You cannot deploy a schema change to production if no tables exist.                                                                                                                   |
| **Non-InnoDB Storage engine** | <Icon icon="xmark" color="red" />          | We only support [InnoDB](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html) storage engine.                                                                                                                    |
| **No applicable unique key**  | <Icon icon="xmark" color="red" />          | We require all tables have a [unique, non-null key](schema-changes/onlineddl-change-unique-keys.md) and that respective covered columns are shared between old and new schema.                                             |
| **Direct DDL**                | <Icon icon="xmark" color="red" />          | We do [not allow Direct DDL](schema-changes/how-online-schema-change-tools-work.md) on production branches when [safe migrations](schema-changes/safe-migrations.md) is enabled. This includes `TRUNCATE` statements. |
| **Binary log access**         | <Icon icon="xmark" color="red" />          | PlanetScale does not currently support binlog replication to external databases.                                                                                                                                                |
| **Large JSON documents**      | <Icon icon="exclamation" color="orange" /> | MySQL supports JSON documents up to 1 GB in size. However, we do not recommend to store more than a few MB in a JSON document for performance reasons.                                                                          |

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
