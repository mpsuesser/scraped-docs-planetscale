---
url: https://planetscale.com/docs/postgres/monitoring/connections
title: "Connections"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Overview

The `pscale branch connections` command shows what is happening inside a Postgres database branch right now, including which sessions are active, which queries are running, what they are waiting on, and which connections are blocking others. If something is stuck, you can cancel the query or terminate the connection directly.

It is the live equivalent of reading `pg_stat_activity` on a self-managed Postgres, with the ability to act on what you find. This guide covers Postgres branches; the same command works for Vitess branches, with the differences described in [Inspect live Vitess connections](../../vitess/monitoring/connections.md).

There are two ways to use it:

- **Interactively**, with `pscale branch connections top` — a terminal UI for watching activity live, drilling into blocker chains, and taking action from the keyboard.
- **From scripts or AI agents**, with `pscale branch connections show` plus the `kill` and `kill-transaction` commands — one-shot, structured output designed to be safe to automate.

The full command and flag reference lives in the [`connections` CLI reference](../../cli/connections.md).

## Watch live activity with top

Run `top` in an interactive terminal to launch the live view:

```shellscript
pscale branch connections top <database> <branch>
```

The view refreshes about once per second; press `space` to pause. You can also scrub back through the recent in-memory history without leaving the live view: `[` and `]` step one sample at a time, `{` and `}` jump to the oldest or newest, and `}` returns to live-follow.

![The pscale branch connections top view, with a stuck checkout transaction blocking a queue of updates](https://mintcdn.com/planetscale-2/Kp51U6wavfDUDSGO/postgres/monitoring/live-connections-top.png?w=2500&fit=max&auto=format&n=Kp51U6wavfDUDSGO&q=85&s=6320dc1e325c1a0348ba6d6b21a31db7)

The pscale branch connections top view, with a stuck checkout transaction blocking a queue of updates

### Reading the table

Each row is one Postgres backend connection. The columns are:

| Column | Meaning |
| --- | --- |
| (marker) | `▶` marks the selected row. `R` marks a connection on a replica; primary connections are unmarked. |
| `PID` | The backend process ID — the connection’s identity. |
| `STATE` | The session state: `active`, `idle`, or `idle/xact` (idle in a transaction). |
| `BLOCK` | Contention signal. A number is how many other sessions this connection is blocking. `W` means it is itself waiting on a lock. `3 W` means both. `-` means quiet. |
| `WAIT` | What the session is waiting on, as `type/event` (for example `Lock/transactionid`). |
| `DURATION` | Time since the last transaction or query started. |
| `APP` | The client’s `application_name`. |
| `START` | When the transaction or query started (shown on wider terminals). |
| `QUERY` | A preview of the current query. |

State is color-coded so trouble stands out: active queries read clearly, idle sessions are muted, and idle-in-transaction sessions are highlighted because they tend to hold locks. Rows that are blocking other sessions are shaded more heavily as the blocking chain deepens.

Press `s` to cycle the sort order between transaction start, duration, and blocking impact.

### Header and freshness

The header shows the target (`database / branch`), a live connection count, the current sort, and how fresh the data is — for example `captured 17:10:46 (2s ago)`. If the data ages past a few refresh intervals it turns yellow, and if it becomes badly stale it turns red, so you always know whether you are looking at the current state. When you pause, the header still shows the data’s real age and adds a `paused` indicator.

If some instances in the branch are unreachable, a banner reports how many, and the rest of the rows still load.

### Drilling into blockers

Press `enter` on a row to open the detail view. It has two tabs:

- **Query** — the connection’s full metadata and complete query text, scrollable and never truncated.
- **Blockers** — the blocking graph: what this connection is **blocked by** upstream, and what it is **blocking** downstream.

Inside the detail view, `left` and `right` switch between the two tabs, and `b` jumps straight to Blockers. From the table you can also open a row directly onto a tab: `v` opens it on the Query tab and `b` opens it on the Blockers tab.

On the Blockers tab, press `enter` on a session in the chain to re-center the detail view on it, so you can walk a lock wait all the way to its root cause. Press `esc` to go back, and `?` at any time for the in-app key reference.

![The Blockers tab: one idle checkout-api transaction holding up the refund, payment, and cancel updates queued behind it](https://mintcdn.com/planetscale-2/Kp51U6wavfDUDSGO/postgres/monitoring/live-connections-blockers.png?w=2500&fit=max&auto=format&n=Kp51U6wavfDUDSGO&q=85&s=2c56ad3b52014f253b33d6105fcbfa70)

The Blockers tab: one idle checkout-api transaction holding up the refund, payment, and cancel updates queued behind it

### Capture and replay

You can record a session to a trace file and review it later:

```shellscript
# record while you watch
pscale branch connections top <database> <branch> --capture trace.jsonl

# or record headlessly, e.g. during an incident
pscale branch connections top <database> <branch> --capture trace.jsonl --duration 30m

# replay the trace in the same UI
pscale branch connections top --replay trace.jsonl
```

In the live view, press `C` to start or stop capturing on the fly. A capture started this way includes the recent history already buffered in memory — up to the last 300 samples, about five minutes at the default refresh interval — not just from the moment you pressed `C`, so you keep the lead-up to whatever you just noticed. The same `[ ] { }` history keys work in replay to move through the recording.

When you start a capture with `C` and did not pass `--capture`, pscale writes the trace to `connections-<timestamp>.jsonl` in the directory you launched from. These files hold the full buffered history, so they can reach a few megabytes. Clean them up or pass `--capture <path>` to control where they land.

## Take action on a connection

`connections` offers three actions, ordered by how much they disrupt the client — from cancelling one query to dropping the whole connection:

| Action | TUI key | What it does |
| --- | --- | --- |
| **Cancel query** | `c` | Cancels the running query (`pg_cancel_backend`). The connection stays open, so the client just sees a cancelled query and can keep going. |
| **Kill transaction** | `k` | Ends the whole connection (`pg_terminate_backend`) — the client is disconnected — but only if it is still running the transaction you saw. |
| **Force terminate** | `K` | Ends the connection (`pg_terminate_backend`) regardless of what it is running. Still verified by backend start, so it can’t hit a recycled PID. The client is disconnected. |

In the TUI, the action applies to the selected connection (or, on the Blockers tab, to the highlighted session in the chain).

**Every action verifies it is hitting the connection you saw.** Each one carries a guard from the snapshot: cancel query checks the query, kill transaction checks the transaction’s start, and force terminate checks the connection’s backend start. So kill transaction only fires if that exact transaction is still open — if it has committed or rolled back, the action is rejected and you re-list and try again. Force terminate has no such *transaction* check. It drops the connection no matter what it is now running while confirming the backend is the same one you saw. Reach for kill transaction first; use force terminate when a connection is fully idle or you specifically want to drop it.

Because the data can be up to about a second old (see [How fresh the data is](#how-fresh-the-data-is)), a connection you act on may have moved on by the time the action lands. That is expected and safe: kill transaction’s guard rejects the action rather than terminating different work, and you should run `show` again to confirm the outcome either way.

## Inspect and act from scripts or AI agents

For automation — including AI agents that cannot drive an interactive UI — use `show` with `--format json`, then act with the server-issued IDs it returns.

```shellscript
pscale branch connections show <database> <branch> --format json
```

The output is an envelope with `captured_at`, the `instances` in the branch, and the `connections`. Each connection carries opaque `query_id`, `transaction_id`, and `connection_id` fields. Pass those to the action commands:

```shellscript
pscale branch connections kill             <database> <branch> <query_id> --query
pscale branch connections kill-transaction <database> <branch> <transaction_id>
pscale branch connections kill             <database> <branch> <connection_id>
```

The recommended loop is:

The action sub-commands take effect immediately and have **no confirmation prompt** — the confirmation only exists in the interactive TUI. Always target a connection by the `query_id`, `transaction_id`, or `connection_id` from a recent `show`, never by PID or position.

If the data was stale, a destructive action may be rejected with a verification mismatch — the transaction or query changed since you listed it. That is the safety guard working: run `show` again and retry.

## Permissions

Access to Connections is tied to your user account and your role on the database, and what you can do depends on whether the branch is a [production branch](../branching.md). It is available to signed-in users (for the CLI, authenticate with `pscale auth login`), not to service tokens.

There are two levels of access:

- **View** — see the connection list and the `top` view.
- **Act** — cancel a query or terminate a connection.

Production branches are gated more tightly than development branches:

| Role | View dev | Act dev | View production | Act production |
| --- | --- | --- | --- | --- |
| Organization Member | ✓ | ✓ |  |  |
| Organization Analyst | ✓ | ✓ | ✓ |  |
| Organization Administrator | ✓ | ✓ | ✓ | ✓ |
| Database Administrator (for that database) | ✓ | ✓ | ✓ | ✓ |

In short: any organization member can view and act on development branches; viewing a production branch requires Analyst or higher; acting on a production branch requires Administrator. `Database Administrator` access can be granted directly or through a [team](../../security/teams.md) — everyone on a team that includes the database has `Database Administrator` access to it.

If you do not have the required role, the command returns a permission error (HTTP 403) for the action you attempted. See [Access control](../../security/access-control.md) for the full role and team model.

## How fresh the data is

`connections` polls about once per second, and snapshots are cached for roughly a second on the server, so the data you see can be up to about a second old. The `captured_at` timestamp (and the freshness indicator in the TUI header) always tells you exactly how old the current snapshot is. After you take an action, you may briefly see the connection in the next `show` because of that caching window; run `show` again a moment later to confirm.

If the server is briefly busy refreshing its view, `show` retries automatically for a couple of seconds. If it still cannot get fresh data, you see `server is warming up, please retry in a moment`.

## What the data maps to

If you know Postgres, these map to what you would expect:

- A **connection** (or session) is a Postgres backend process, identified by its `PID`.
- A **query** is the statement that connection is currently running. Cancel it with `kill --query`.
- A **transaction** is the open transaction on that connection. End it with `kill-transaction`.
- A **blocker** is a connection holding a lock that another connection is waiting on. The `BLOCK` column and the Blockers tab show these relationships.
- An **instance** is a database server in your branch — your primary and any replicas.

## Related documentation

## connections CLI reference

## Query Insights

## PlanetScale CLI environment setup

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
