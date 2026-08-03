---
url: https://planetscale.com/docs/vitess/monitoring/connections
title: "Connections"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Inspect live Vitess connections

> Watch the live MySQL process list on a Vitess primary tablet, and cancel a query or terminate a connection from the PlanetScale CLI.

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

export const YouTubeEmbed = ({id, title}) => {
  return <Frame>
      <iframe src={`https://www.youtube-nocookie.com/embed/${id}?rel=0`} title={title} className="aspect-video w-full" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" />
    </Frame>;
};

<PlatformAvailability current="vitess" postgres="/postgres/monitoring/connections" />

## Overview

The `pscale branch connections` command shows what is running on a Vitess database branch right now: the
active sessions on a primary tablet, what each is executing, and how long it has been running. If a
session is stuck, you can cancel its query or terminate the connection directly.

<YouTubeEmbed id="j67zO-MixK0" title="See what's happening in your database RIGHT NOW" />

It is the live equivalent of running `SHOW FULL PROCESSLIST` and `KILL` against a primary, without
opening a MySQL shell or holding database credentials. The command detects the branch engine
automatically; this guide covers Vitess branches. For Postgres, see
[Inspect live Postgres connections](../../postgres/monitoring/connections.md).

There are two ways to use it:

* **Interactively**, with `pscale branch connections top` — a terminal UI for watching activity live and
  taking action from the keyboard.
* **From scripts or AI agents**, with `pscale branch connections show` plus the `kill` command —
  one-shot, structured output designed to be safe to automate.

The full command and flag reference lives in the [`connections` CLI reference](../../cli/connections.md).

## Scope and limitations

For Vitess, `connections` targets one keyspace and one shard. If a branch has more than one keyspace, or a keyspace is
sharded, pass `--keyspace` and `--shard` to choose the tablet. An interactive `top` prompts you to
pick when a target is missing.

## Watch live activity with `top`

Run `top` in an interactive terminal to launch the live view:

```bash theme={null}
pscale branch connections top <database> <branch>
```

Target a specific keyspace and shard:

```bash theme={null}
pscale branch connections top <database> <branch> --keyspace <keyspace> --shard <shard>
```

The view refreshes about once per second; press `space` to pause. You can scrub back through the
recent in-memory history without leaving the live view: `[` and `]` step one sample at a time, `{` and
`}` jump to the oldest or newest, and `}` returns to live-follow.

### Reading the table

Each row is one session on the primary tablet, sorted by duration with the longest-running first. The
columns are:

| Column     | Meaning                                                              |
| :--------- | :------------------------------------------------------------------- |
| (marker)   | `▶` marks the selected row.                                          |
| `PID`      | The MySQL thread ID — the connection's identity.                     |
| `TABLET`   | The tablet the session is running on (shown on wider terminals).     |
| `STATE`    | The session's command/state, for example `Query` or `Query/running`. |
| `DURATION` | How long the session has been running its current work.              |
| `USER`     | The session's MySQL user.                                            |
| `DB`       | The session's current database.                                      |
| `QUERY`    | A preview of the current statement.                                  |

### Inspecting a session

Press `enter` on a row to open the detail view. It shows the session's full metadata — `pid`,
`tablet`, `state`, `duration`, `user`, `database`, `client_addr`, `connection_id`, and `query_id` —
followed by the complete statement text, scrollable and never truncated. Press `esc` to go back, and
`?` at any time for the in-app key reference.

### Capture and replay

You can record a session to a trace file and review it later:

```bash theme={null}
# record while you watch
pscale branch connections top <database> <branch> --capture trace.jsonl

# or record headlessly, e.g. during an incident
pscale branch connections top <database> <branch> --capture trace.jsonl --duration 30m

# replay the trace in the same UI
pscale branch connections top --replay trace.jsonl
```

In the live view, press `C` to start or stop capturing on the fly. The same `[ ] { }` history keys work
in replay to move through the recording.

## Take action on a connection

Vitess sessions support two actions:

| Action           | TUI key   | What it does                                                                                                              |
| :--------------- | :-------- | :------------------------------------------------------------------------------------------------------------------------ |
| **Cancel query** | `c`       | Runs `KILL QUERY` against the session's `query_id`. The connection stays open; only the running statement is interrupted. |
| **Terminate**    | `shift+K` | Runs `KILL` against the session's `connection_id`. The whole connection is dropped.                                       |

In the TUI, the action applies to the selected session and asks for confirmation (`y`/`N`) before it
runs. There is no transaction-level kill for Vitess.

## Inspect and act from scripts or AI agents

For automation — including AI agents that cannot drive an interactive UI — use `show` with
`--format json`, then act with the IDs it returns.

```bash theme={null}
pscale branch connections show <database> <branch> --keyspace commerce --shard -80 --format json
```

For a Vitess branch, the envelope reports `"database_kind": "mysql"` and a `topology` object with the
`keyspace`, `shard`, and `tablet` the rows came from. Each connection carries tablet-qualified
`connection_id` and `query_id` values (for example `zone1-1001-101`). Pass those to the action
commands:

```bash theme={null}
pscale branch connections kill <database> <branch> <query_id> --query   # KILL QUERY
pscale branch connections kill <database> <branch> <connection_id>      # KILL
```

<Warning>
  The action sub-commands take effect immediately and have **no confirmation prompt** — the confirmation only exists in
  the interactive TUI. Always target a session by the `query_id` or `connection_id` from a recent `show`.
</Warning>

## Permissions

Access to Connections is tied to your user account and your role on the database, and what you can do
depends on whether the branch is a [production branch](../schema-changes/branching.md). It is
available to signed-in users (for the CLI, authenticate with `pscale auth login`), not to service
tokens.

There are two levels of access:

* **View** — see the connection list and the `top` view.
* **Act** — cancel a query or terminate a connection.

Production branches are gated more tightly than development branches:

| Role                                       | View dev | Act dev | View production | Act production |
| :----------------------------------------- | :------: | :-----: | :-------------: | :------------: |
| Organization Member                        |     ✓    |    ✓    |                 |                |
| Organization Analyst                       |     ✓    |    ✓    |        ✓        |                |
| Organization Administrator                 |     ✓    |    ✓    |        ✓        |        ✓       |
| Database Administrator (for that database) |     ✓    |    ✓    |        ✓        |        ✓       |

In short: any organization member can view and act on development branches; viewing a production branch requires Analyst or higher; acting on a production branch requires Administrator. `Database Administrator` access can be granted directly or through a [team](../../security/teams.md) — everyone on a team that includes the database has `Database Administrator` access to it.

If you do not have the required role, the command returns a permission error (HTTP 403) for the action you attempted. See [Access control](../../security/access-control.md) for the full role and team model.

## How fresh the data is

`connections` polls about once per second. Each refresh runs `SHOW FULL PROCESSLIST` on the primary
tablet, so on a very busy primary you can raise `--interval` to poll less often. The `captured_at`
timestamp, and the freshness indicator in the TUI header, always tell you how old the current snapshot
is.

## What the data maps to

If you know MySQL, these map to what you would expect:

* A **connection** (or session) is a MySQL thread, identified by its `PID`.
* A **query** is the statement that connection is currently running. Cancel it with `kill --query`
  (`KILL QUERY`).
* A **tablet** is the primary serving the keyspace and shard you targeted. The `connection_id` and
  `query_id` are qualified by it.

## Related documentation

<CardGroup>
  <Card title="connections CLI reference" href="/docs/cli/connections" icon="angles-right" horizontal />

  <Card title="Query Insights" href="/docs/vitess/monitoring/query-insights" icon="angles-right" horizontal />

  <Card title="PlanetScale CLI environment setup" href="/docs/cli/planetscale-environment-setup" icon="angles-right" horizontal />
</CardGroup>

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
