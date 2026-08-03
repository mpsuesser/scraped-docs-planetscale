---
url: https://planetscale.com/docs/vitess/troubleshooting/errors
title: "Errors"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale database error reference

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

This documentation covers some commonly encountered errors and approaches for addressing them.

<Note>
  If you are facing issues or have questions that were not answered in the documentation, the best course of action is to [open a support ticket](https://planetscale.com/contact).

  Additionally, you can find some broader limitations in the [PlanetScale system limits documentation](planetscale-system-limits.md).
</Note>

## MySQL and Vitess errors

PlanetScale Vitess databases are powered by Vitess and MySQL.

Many errors you encounter will contain a substring of the form `SQL Error [XXXX]` where `XXXX` is some integer number.
Often, these are errors that are passed along back to the client from MySQL.
There are hundreds of MySQL error codes documented on the [MySQL error codes page](https://dev.mysql.com/doc/mysql-errors/8.0/en/server-error-reference.html).

For example, say you encounter this error:

```
Error synchronizing data with database Reason:
SQL Error [1364] [HY000]:
target: unigate.-.primary:
vttablet: rpc
error: code = Unknown desc = (errno 1364) (sqlstate HY000) (CallerID: unsecure_grpc_client):
Sql: ...
BindVars: {}
```

The second line says `SQL Error [1364]`, which corresponds to the [ER\_NO\_DEFAULT\_FOR\_FIELD](https://dev.mysql.com/doc/mysql-errors/8.0/en/server-error-reference.html#error_er_no_default_for_field) error in MySQL.
Knowing this, you can make the appropriate changes to your query or schema to mitigate the error.
You can also search the MySQL docs and other online sources using the error code, as there is a long history of MySQL questions and answers online.

One specific code you may come across is `SQL Error [1105]`, which represents an [unknown error](https://dev.mysql.com/doc/mysql-errors/8.0/en/server-error-reference.html#error_er_unknown_error) in MySQL.
On PlanetScale, it's likely that such an error is actually coming from Vitess, which also has an [error documentation page](https://vitess.io/docs/reference/errors/query-serving/).
Note that a number of older Vitess errors used the `1105` code, but there are many other errors documented there as well.
We recommend you reference this for Vitess-specific errors.

Below, we document more common errors from our customers, and provide suggestions for how to address them.

| **Error**                                                                                                                                  | **Reason**                                                                                                                                                                                                                        | **How to address**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| ------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ResourceExhausted desc = transaction pool connection limit exceeded`                                                                      | Every PlanetScale database has limit on the number of concurrent transactions it can process. This message indicates you are hitting the limit.                                                                                   | This can be resolved in several ways. One option is to make changes to your application code that reduce the number of long-running transactions. If this is not feasible, you can also [size up your database](../cluster-configuration.md#adjust-your-cluster-size). This can allow long-running transactions to execute quicker, and you also may get a higher concurrent transaction limit.                                                                                                                                                                                      |
| `primary is not serving, there is a reparent operation in progress`                                                                        | Your primary database server is unavailable. This often happens due to an OOM error (Out Of Memory). You also may see this on dev branches if you happen to query them during an upgrade.                                         | In the short term, if this error message persists, we recommend you [reach out to support](https://planetscale.com/contact?initial=support). If this was caused by an OOM, you should see if there are changes you can make to your queries to reduce memory pressure on your database. This could mean consolidating queries or building indexes to reduce the number of pages needing to be brought into memory during query execution. If this is not possible, you will likely need to [upgrade](../cluster-configuration.md#adjust-your-cluster-size) to a larger cluster size. |
| `vttablet: (errno 2013) due to context deadline exceeded, elapsed time: ...`                                                               | Your query or transaction exceeded the default [20 second per-transaction timeout](planetscale-system-limits.md#query-limits).                                                                               | Consider options to reduce the time needed for the query/transaction in question to execute. Things that could help include: adding an index to speed up the query, breaking a large multi-query transaction up into multiple shorter ones. If you need some long-running transactions for analytical purposes, consider offloading those to a tool like [Airbyte](../integrations/airbyte.md) or [Stitch](../integrations/stitch.md).                                                                                                                                             |
| `SQL Error [1105] [HY000]: target: platform.-.primary: vttablet: rpc error: code = Aborted desc = transaction ... (exceeded timeout: 20s)` | Max transaction time exceeded.                                                                                                                                                                                                    | See row above.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| `vttablet: rpc error: code = Aborted desc = Row count exceeded 100000`                                                                     | You have hit the [100k limit](planetscale-system-limits.md#query-limits) for number of rows returned, updated, or deleted in a single query.                                                                 | If this is coming from a `SELECT` statement, consider narrowing the search or paginating results. If it is coming from an `UPDATE` or `DELETE`, consider performing updates or deletions in smaller batches.                                                                                                                                                                                                                                                                                                                                                                           |
| `vttablet: rpc error: code = ResourceExhausted desc = Out of sort memory, consider increasing server sort buffer size (errno 1038)`        | MySQL has a [buffer specifically used for sorting](https://dev.mysql.com/doc/refman/8.4/en/server-system-variables.html#sysvar_sort_buffer_size). This error is passed up from MySQL and indicates the buffer has been exhausted. | Buffer sizes for MySQL servers on PlanetScale come pre-configured, and we do not allow you to modify the system `sort_buffer_size` parameter. You may be able to get around this error by reducing the size of the result sets being sorted, adding a covering index to avoid performing a filesort during query execution, or increasing the sort buffer size for the individual sessions the query is running in.                                                                                                                                                                    |
| `unavailable: vtgate connection error: no endpoints, after 1 attempts`                                                                     | A connection could not be made to one of your VTGates. These show up as your "load balancers" in the PlanetScale UI.                                                                                                              | This can be caused by running queries that return very large result sets, exhausting the available memory on the load balancer. Please [contact support](https://planetscale.com/contact?initial=support) to discuss options for mitigating this.                                                                                                                                                                                                                                                                                                                                      |

## PlanetScale-specific errors

You also may encounter error messages in the PlanetScale UI or on specific PlanetScale features such as [safe migrations](../schema-changes/safe-migrations.md) or [workflows](../scaling/workflows.md).
Here, we document a selection of those errors you may run into.

| **Error**                                                                             | **Reason**                                                                                                                                          | **How to address**                                                                                                                                                                                                                                     |
| ------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `This deploy request is not deployable`                                               | Too many schema changes in one deploy request. If you are making a large number of schema changes in a single branch, you may encounter this error. | Divide up the schema changes into multiple incremental changes, and create separate branches and deploy requests for each.                                                                                                                             |
| `Data Definition Language is not supported on branches with safe migrations enabled.` | You attempted to run DDL on a branch with [safe migrations](../schema-changes/safe-migrations.md) enabled.                                        | We recommend you keep safe migrations enabled. If you need to change schema in such a branch, you can create a new branch, modify the schema, and then use a deploy request to bring that change into the original branch.                             |
| `not_found: branch is missing or sleeping: branch_id`                                 | The branch you are targeting is either [sleeping](https://planetscale.com/docs/plans/database-sleeping#what-is-database-sleeping) or has been deleted.                          | We recommend double checking that you are targeting the correct branch. Additionally, ensure the credentials are not tied to a deleted branch. [Reach out to support](https://planetscale.com/contact?initial=support) if you need further assistance. |

## Other errors

If you are encountering an error not listed here, we recommend you use the [MySQL](https://dev.mysql.com/doc/mysql-errors/8.0/en/server-error-reference.html) and [Vitess](https://vitess.io/docs/reference/errors/query-serving/) error documentation pages to narrow down your issue.
If you are unable to identify it on your own, please [reach out to support](https://planetscale.com/contact?initial=support).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
