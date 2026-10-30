---
url: https://planetscale.com/docs/neki/monitoring/connections
title: "Connections"
description: ""
access_date: 2026-10-30T18:26:51.105Z
current_date: 2026-10-30T18:26:51.105Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Inspect live Neki connections

> See live client sessions on your Neki routers, and cancel a query or terminate a session from the dashboard or the PlanetScale CLI.

## Overview

Connections shows the client sessions currently open on a Neki branch's routers. If a session is stuck, you can cancel
its query or terminate the session.

> [!NOTE]
>
> Connections only shows router sessions, not the connections between routers and each shard's Postgres instances.

PlanetScale's own administrative sessions and non-client backends aren't shown.

## View connections in the dashboard

Select a database and branch, then select **Connections**. The list refreshes automatically and shows one row per
router session:

| Column | Meaning |
| :- | :- |
| PID | The session's process ID on its router. PIDs are unique per router, not per branch. |
| Router group | The router group the session is connected to. |
| Availability zone | The zone of the router serving the session. |
| State | The session state, for example `active`, `idle`, or `idle in transaction`. |
| Duration | Time since the session's transaction started, or its query if it has no open transaction, or the session itself if neither. |
| User | The database role the client connected as. |
| Application | The client's `application_name`. |
| Query | The session's current query, or its most recent one if it's idle. |

Select a row to see the session's details, including the shard backends serving it.

## View connections in the CLI

`show` prints the current sessions once, and `top` opens a live terminal view:

```bash theme={null}
pscale branch connections show <database> <branch>
pscale branch connections top <database> <branch>
```

`show` prints each session with its IDs and shard backends. Add `--format json` for scripts and AI agents.

In `top`, press `s` to switch the sort between transaction start and duration, and `enter` to open a session's details.

### Capture and replay

Record a `top` session to a trace file and replay it later:

```bash theme={null}
# record while you watch
pscale branch connections top <database> <branch> --capture trace.jsonl

# record without the interactive view
pscale branch connections top <database> <branch> --capture trace.jsonl --duration 30m

# replay the trace
pscale branch connections top --replay trace.jsonl
```

In the live view, press `C` to start or stop a capture. The capture includes the last few minutes of history that `top`
already has in memory. If you didn't pass `--capture`, the trace is written to `connections-<timestamp>.jsonl` in the
current directory.

## Cancel a query or terminate a session

| Action | Dashboard / `top` | CLI command | What happens |
| :- | :- | :- | :- |
| **Cancel query** | Cancel query / `c` | `kill <database> <branch> <query_id> --query` | The router cancels the statement the session is running. The connection stays open, and the client receives a canceled-query error (SQLSTATE `57014`). If the session was in a transaction, the transaction is aborted and the client must roll back before running more statements, as in Postgres. |
| **Kill transaction** | Terminate transaction / `k` | `kill-transaction <database> <branch> <transaction_id>` | Terminates the session, but only if it still has the open transaction you saw. |
| **Force terminate** | Terminate connection / `K` | `kill <database> <branch> <connection_id>` | The router cancels anything the session is running, then closes the client's connection. Any open transaction is rolled back, the same as if the client had disconnected. |

Before acting, PlanetScale checks that the session is still the one you saw. If the query finished, the transaction
ended, or the PID now belongs to a new session, the action is rejected.

In the CLI, the action commands take effect immediately with no confirmation prompt. Scripts and AI agents should get
IDs from `show --format json`, act on one session at a time, and run `show` again to confirm the result. An agent should
get a person's approval before running an action.

## Permissions

Connections is available to signed-in users (for the CLI, authenticate with `pscale auth login`) but not when using
service tokens. What you can do depends on your role and whether the branch is a production branch. Acting means
cancelling a query or terminating a session.

| Role | View dev | Act dev | View production | Act production |
| :- | :-: | :-: | :-: | :-: |
| Organization Member | ✓ | ✓ | | |
| Organization Analyst | ✓ | ✓ | ✓ | |
| Organization Administrator | ✓ | ✓ | ✓ | ✓ |
| Database Administrator (for that database) | ✓ | ✓ | ✓ | ✓ |

Database Administrator access can be granted directly or through a [team](https://planetscale.com/docs/security/teams.md). If you don't have the
required role, the request fails with a permission error. See [Access control](https://planetscale.com/docs/security/access-control.md) for the full
role model.

## Related documentation

> [card] [connections CLI reference](https://planetscale.com/docs/cli/connections.md)

> [card] [Monitoring overview](https://planetscale.com/docs/neki/monitoring.md)

> [card] [Query Insights](https://planetscale.com/docs/neki/monitoring/query-insights.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
