---
url: https://planetscale.com/docs/postgres/web-console
title: "Web Console"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Web console

> Interactive interface for running PostgreSQL queries and DDL against PlanetScale Postgres database branches.

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

<PlatformAvailability current="postgres" vitess="/vitess/web-console" />

<Note>
  The web console is currently in beta for **Postgres** databases.
</Note>

## Get started

The PlanetScale web console can be used to query any database branch; however, it is [disabled for production branches](web-console.md#enable-for-production-branches) by default to protect production data.

To access the web console, navigate to a database, and click on the "Console" tab in the page navigation. From here, you can select which branch you'd like to connect to by selecting it in the dropdown and clicking "Connect".

Once you have accessed the web console, you can run queries against your database branch or apply DDL. A branch can contain multiple [logical databases](https://planetscale.com/blog/approaches-to-tenancy-in-postgres); use the **Database** dropdown in the console header to choose which one to connect to.

The following are examples of PostgreSQL statements and meta commands you may find useful within the web console:

Use `\l` to see a list of logical databases on the branch.

Use `\dt` to see a list of tables in the current database.

Use `\d table_name` to obtain information about a given table's structure.

Use `EXPLAIN` or `EXPLAIN ANALYZE` in front of `SELECT`, `INSERT`, `UPDATE`, and `DELETE` statements to learn how the database is executing a query. This can be useful for optimizing slow queries.

## Logical databases

After connecting to a branch, use the **Database** dropdown in the console header to choose which logical database to connect to. The default database name is `postgres`.

You can also switch databases from the console prompt with `\connect database_name` or `\c database_name`, just like in `psql`. Run `\l` to list the available databases on the branch.

Switching databases reconnects the console and clears the current output.

## Primary and replica connections

After connecting to a branch, use the **Server** dropdown in the console header to choose which node to connect to:

* **Primary** — Connect to the primary node for read and write queries.
* **Replica** — Connect to a read replica for read-only queries.

If a branch has no replicas, the console connects to the primary and the **Replica** option is unavailable.

Switching between primary and replica reconnects the console and clears the current output.

### Connection roles

The console creates a short-lived Postgres role for each connection. The inherited permissions depend on the selected server and your [organization or database role](../security/access-control.md).

Tables created during a web console session are reassigned to the `postgres` role shortly after the session ends. Until reassignment completes, newly created tables may temporarily appear owned by the console's short-lived role.

#### Development branches

All organization roles (member, analyst, administrator) have full read and write access to development branch consoles on the primary. On a replica, the console always uses a read-only role.

#### Production branches

Production branch console access also requires the [web console setting](#enable-for-production-branches) to be enabled. Once enabled, access depends on your role:

| Role                                       | Console access | Primary permissions                                 |
| :----------------------------------------- | :------------- | :-------------------------------------------------- |
| Organization or database **Administrator** | Read and write | `pg_read_all_data`, `pg_write_all_data`, `postgres` |
| Organization or database **Analyst**       | Read-only      | `pg_read_all_data` only                             |
| **Member**                                 | No access      | —                                                   |

On a **replica**, the console always uses a read-only role with `pg_read_all_data`, regardless of your primary permissions.

For more information about these permissions, see [Role management](connecting/roles.md).

## Supported console commands

| Command                              | Description                             |
| :----------------------------------- | :-------------------------------------- |
| ?, \\?                               | Synonym for `help`                      |
| clear                                | Clear the current input statement       |
| help, \h                             | Display list of commands                |
| ;                                    | Send SQL statement to server            |
| \connect \[database], \c \[database] | Connect to a different logical database |
| \d \[name]                           | List relations or describe a table      |
| \dt \[pattern]                       | List tables                             |
| \dv \[pattern]                       | List views                              |
| \di \[pattern]                       | List indexes                            |
| \ds \[pattern]                       | List sequences                          |
| \da \[pattern]                       | List aggregate functions                |
| \db \[pattern]                       | List tablespaces                        |
| \dc \[pattern]                       | List casts                              |
| \dconfig \[pattern]                  | List configuration parameters           |
| \dD \[pattern]                       | List domains                            |
| \det \[pattern]                      | List foreign tables                     |
| \dn \[pattern]                       | List schemas                            |
| \do \[pattern]                       | List operators                          |
| \dO \[pattern]                       | List collations                         |
| \dp, \z \[pattern]                   | List access privileges                  |
| \dT \[pattern]                       | List data types                         |
| \du \[pattern]                       | List roles                              |
| \l \[pattern]                        | List databases                          |
| \df \[pattern]                       | List functions                          |
| \dx \[pattern]                       | List extensions                         |

## Enable for production branches

By default, the web console is disabled for production branches to protect production data.

Database admins can enable the web console for production branches on the "Settings" page for the given database,
`app.planetscale.com/<org>/<database>/settings`.

Select the checkbox for "Allow web console access to production branches", then scroll down and click the "Save database settings" button to save your changes.

This will enable the ability to use the web console to run queries against production branches for the given database.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
