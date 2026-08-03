---
url: https://planetscale.com/docs/cli/connections
title: "Connections"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The connections command

Inspect and manage live connection activity for a database branch. Use it to monitor active sessions in real time, find queries that are blocking others, and cancel a query or terminate a connection without opening a SQL shell. It works for both Postgres and Vitess branches; the command detects the branch’s engine and adapts what it shows and what actions are available.

There are two ways to use it:

- `top` launches an interactive terminal UI to watch activity live and act from the keyboard.
- `show` and the action subcommands provide one-shot, scriptable output that works in pipelines and for AI agents. Pair `show --format json` with `kill` and `kill-transaction`.

For a walkthrough of the interactive workflow, see [Inspect live Postgres connections](../postgres/monitoring/connections.md) for Postgres branches, or [Inspect live Vitess connections](../vitess/monitoring/connections.md) for Vitess branches.

Access depends on your database role and whether the branch is a production branch. It also requires a signed-in user (authenticate with `pscale auth login`) rather than a service token. For the full breakdown, see Permissions for [Postgres](../postgres/monitoring/connections.md#permissions) or [Vitess](../vitess/monitoring/connections.md#permissions) branches.

Because it runs over a reserved administrative connection, `connections` keeps working even when the database has exhausted its normal connections, so you can still see what’s happening and cancel or terminate a connection to recover.

### Database engine support

The command surface is the same for both engines, but the engine determines how you target a branch and which actions apply.

|  | **Postgres** | **Vitess** |
| --- | --- | --- |
| Targeting flags | `--instance`, `--role` | `--keyspace`, `--shard` |
| Scope | All instances, including replicas (`--role replica`) | One primary tablet |
| `kill <connection-id>` | Yes (`pg_terminate_backend`) | Yes (`KILL`) |
| `kill <query-id> --query` | Yes (`pg_cancel_backend`) | Yes (`KILL QUERY`) |
| `kill-transaction` | Yes | No |
| Blocking/lock detail in `top` | Yes | No |

Vitess `show` and `top` read a single primary tablet. If a branch has more than one keyspace, or a keyspace is sharded, pass `--keyspace` and `--shard` to pick the tablet. An interactive `top` prompts you to choose when a target is missing; non-interactive runs return an error that lists the available keyspaces or shards. There is no cluster-wide or cross-shard view, and replica connections are not surfaced.

**Usage:**

```shellscript
pscale branch connections [sub-command]
```

### Available sub-commands

| **Sub-command** | **Product** | **Description** |
| --- | --- | --- |
| `show` | Postgres, Vitess | List current live connections once |
| `top` | Postgres, Vitess | Show live connection activity (interactive) |
| `kill` | Postgres, Vitess | Terminate a connection, or cancel its current query with `--query` |
| `kill-transaction` | Postgres | Terminate the connection running a transaction, by `transaction_id` |

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for `connections` command |
| `--org string` | The organization for the current user |
| `-f`, `--format <FORMAT>` | Show output in a specific format. Possible values: `human` (default), `json`, `csv`. |

## Examples

### The show sub-command

List the current live connections for a branch once and exit. This is the command to reach for in scripts and automation.

Human output uses vertical records (one field per line, like `psql` ’s `\x` mode) so query text and other fields are never truncated. Use `--format json` when a script or agent needs to read the `query_id`, `transaction_id`, and `connection_id` fields and act on them. JSON output is an envelope:

```json
{
  "database_kind": "postgresql",
  "captured_at": "2026-06-01T17:10:46Z",
  "instances": [{ "id": "...", "role": "primary" }],
  "connections": [
    {
      "pid": 12345,
      "instance": "...",
      "instance_role": "primary",
      "state": "active",
      "duration_ms": 8200,
      "wait_event_type": "Lock",
      "wait_event": "transactionid",
      "username": "app",
      "application_name": "checkout",
      "client_addr": "10.0.0.5",
      "query_text": "UPDATE orders SET ...",
      "blocked_by": [],
      "query_id": "...",
      "transaction_id": "...",
      "connection_id": "..."
    }
  ]
}
```

`connection_id` is always present. `transaction_id` is `null` when the connection has no open transaction, and `query_id` is `null` when it has no current query. Pass the value you want to act on to the corresponding action sub-command.

For Vitess branches, the envelope reports `"database_kind": "mysql"` and a `topology` object with the `keyspace`, `shard`, and `tablet` the rows came from instead of an `instances` list. Each row’s `connection_id` and `query_id` are tablet-qualified IDs (for example `zone1-1001-101`); blocking and wait-event fields are empty, and `transaction_id` is `null`.

**Usage:**

```shellscript
pscale branch connections show <database> <branch> [flags]
```

**Available flags:**

- `--instance string` - For Postgres, filter the list to a single instance, by `id` from the `show` response.
- `--role string` - For Postgres, filter the list to rows whose instance role is `primary` or `replica`. Cannot be combined with `--instance`.
- `--keyspace string` - For Vitess, target a specific keyspace.
- `--shard string` - For Vitess, target a specific shard.

**Example:**

```shellscript
pscale branch connections show <database> <branch> --format json
```

### The top sub-command

Show live connection activity. Run it in an interactive terminal to launch the TUI, where you can sort, inspect a connection, and act on connections by keyboard. For Postgres branches you can also drill into a connection’s blockers. See [Inspect live Postgres connections](../postgres/monitoring/connections.md) or [Inspect live Vitess connections](../vitess/monitoring/connections.md) for a full tour of the interface.

When output is piped or redirected, `top` runs headlessly and records snapshots to a trace file. `--capture` is required in this mode. You can replay a captured trace later with `--replay`.

For a Vitess branch with more than one keyspace or a sharded keyspace, pass `--keyspace` and `--shard`, or run interactively and `top` will prompt you to select a target.

**Usage:**

```shellscript
pscale branch connections top [database] [branch] [flags]
```

If you omit the branch, pscale uses the database’s branch automatically when there’s only one, and prompts you to choose when there are several.

**Available flags:**

- `--interval duration` - Refresh interval. Default is `1s`.
- `--capture string` - Write captured samples to a trace file. Required in headless mode.
- `--replay string` - Replay a previously captured trace file in the TUI. Mutually exclusive with `--capture`.
- `--duration duration` - Run for this duration. Default is to run until interrupted.
- `--instance string` - For Postgres, filter the live view to a single instance, by `id` from the snapshot response.
- `--role string` - For Postgres, filter the live view to rows whose instance role is `primary` or `replica`. Cannot be combined with `--instance`.
- `--keyspace string` - For Vitess, target a specific keyspace.
- `--shard string` - For Vitess, target a specific shard.

**Example:**

```shellscript
pscale branch connections top <database> <branch>
```

Target a specific Vitess shard:

```shellscript
pscale branch connections top <database> <branch> --keyspace commerce --shard -80
```

Capture an hour of activity to a trace file, then replay it:

```shellscript
pscale branch connections top <database> <branch> --capture trace.jsonl --duration 1h
pscale branch connections top --replay trace.jsonl
```

### The kill sub-command

Terminate a connection by its `connection_id`, or pass `--query` to cancel only the running query by its `query_id`. For Postgres, terminating a connection runs `pg_terminate_backend` and cancelling a query runs `pg_cancel_backend`, which stops the in-flight query but leaves the connection open. For Vitess, these map to `KILL` and `KILL QUERY`; pass the tablet-qualified ID from `show`.

Both forms are guarded against stale snapshots. Cancelling a query only takes effect if the connection is still running that same query. Terminating a connection verifies it is still the same connection you observed in `show` (matched by its backend start time), so it cannot act on a different connection that has reused the process ID. If the target has already moved on, the command has no effect.

This command takes effect immediately. There is no confirmation prompt. Run `show` first, confirm you have the correct target ID, and run `show` again afterward to verify the result.

**Usage:**

```shellscript
pscale branch connections kill <database> <branch> <connection-id>
pscale branch connections kill <database> <branch> <query-id> --query
```

**Available flags:**

- `--query` - Cancel the `query_id` instead of terminating the `connection_id`.
- `--keyspace string` - For Vitess, target a specific keyspace.
- `--shard string` - For Vitess, target a specific shard.

### The kill-transaction sub-command

Terminate the connection running a specific transaction, identified by its `transaction_id`. This runs `pg_terminate_backend`, but only if the connection is still running the same transaction you observed in `show`. If the transaction has already finished or moved on, the action is rejected so you do not terminate the wrong work. This is the safer of the two terminate paths, and the right tool for an idle-in-transaction session that is holding a lock.

This sub-command is Postgres only. Vitess branches do not expose transaction state; use `kill` to terminate the connection or `kill --query` to cancel its current query.

This command is destructive and takes effect immediately, with no confirmation prompt. It terminates a database connection. Run `show` first to confirm the `transaction_id`, and run `show` again afterward to verify.

**Usage:**

```shellscript
pscale branch connections kill-transaction <database> <branch> <transaction-id>
```

## Related documentation

## Inspect live Postgres connections

## Inspect live Vitess connections

## PlanetScale CLI environment setup

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
