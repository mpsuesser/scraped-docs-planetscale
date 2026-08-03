---
url: https://planetscale.com/docs/cli/sql
title: "Sql"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The sql command

Execute a single SQL query against a database branch **without** an interactive shell. Intended for agents and scripts. For interactive sessions, use [`pscale shell`](shell.md) instead.

**Requires `pscale` 0.292.0 or later.**

**Usage:**

```shellscript
pscale sql <database> <branch> --org <org> --query "<SQL>" <FLAG>
```

Place **positional arguments first**, then flags. **`--org` is required.**

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `--query <SQL>` | SQL query to execute **(required)** |
| `--org <org>` | Organization name **(required)** |
| `--role <role>` | Access level: `reader` (default), `writer`, `readwriter`, `admin` |
| `--replica` | Route reads to replicas |
| `--dbname <name>` | PostgreSQL database name (default `postgres`) |
| `--keyspace <name>` | MySQL/Vitess keyspace (default `@primary`, same as `pscale shell`) |
| `--force` | Allow destructive SQL after explicit user approval (see below) |
| `-h`, `--help` | Help for `sql` |

Unlike [`pscale shell`](shell.md), the default `--role` is **`reader`**. Pass `--role admin` (or `writer` / `readwriter`) for writes.

### Destructive SQL

Queries containing **`DELETE`**, **`DROP`**, or **`TRUNCATE`** as statement keywords are blocked unless `--force` is passed.

With `--format json`, blocked queries return `"status": "action_required"` and `"query_kind": "destructive"`. Agents must **ask the user to approve**, then re-run with `--force`. Never use `--force` without explicit user approval.

### JSON output

Use `--format json` for automation.

**Success:** `status`, `database`, `branch`, `kind` (`mysql` or `postgresql`), `role`, `row_count`, `columns`, `rows`; `replica` when `--replica` was used.

**Errors:** one JSON object on stdout with `status: "error"`, `error`, and `next_steps`.

**Destructive guard:** `status: "action_required"`, `query_kind: "destructive"`, `issues`, and `next_steps` (includes a `--force` retry command).

### Global flags

| **Flag** | **Description** |
| --- | --- |
| `-f`, `--format json` | JSON on stdout (recommended for agents) |
| `--api-token`, `--service-token-id`, `--service-token` | Authentication |
| `--api-url <URL>` | API base URL |
| `--config <FILE>` | Config file |
| `--debug` | Debug mode |
| `--no-color` | Disable color output |

## Examples

**Read query (default reader role):**

```shellscript
pscale sql <database> <branch> --org <org> --format json --query "SELECT 1"
```

**Read from replica:**

```shellscript
pscale sql <database> <branch> --org <org> --format json --replica --query "SELECT COUNT(*) FROM users"
```

**PostgreSQL with explicit database name:**

```shellscript
pscale sql <database> <branch> --org <org> --format json --dbname postgres --query "SELECT version()"
```

**MySQL multi-keyspace:**

```shellscript
pscale sql <database> <branch> --org <org> --format json --keyspace <keyspace> --query "SELECT 1"
```

**Destructive SQL (after user approval):**

```shellscript
pscale sql <database> <branch> --org <org> --format json --force --query "DELETE FROM staging_rows WHERE id = 1"
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
