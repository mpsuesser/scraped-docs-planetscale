---
url: https://planetscale.com/docs/cli/traffic-control
title: "Traffic Control"
description: ""
access_date: 2026-08-20T18:00:05.296Z
current_date: 2026-08-20T18:00:05.296Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The traffic-control command

Manage [Database Traffic Control](../postgres/traffic-control.md) budgets and rules for a branch. This command is only supported for Postgres databases.

**Usage:**

```shellscript
pscale traffic-control [command]
```

### Available sub-commands

| **Sub-command** | **Description** |
| --- | --- |
| `budget` | Manage traffic budgets |
| `rule` | Manage traffic rules |

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for `traffic-control` command |
| `--org string` | The organization for the current user |

### Global flags

| **Command** | **Description** |
| --- | --- |
| `--api-token <TOKEN>` | The API token to use for authenticating against the PlanetScale API. |
| `--api-url <URL>` | The base URL for the PlanetScale API. Default is `https://api.planetscale.com/`. |
| `--config <CONFIG_FILE>` | Config file. Default is `$HOME/.config/planetscale/pscale.yml`. |
| `--debug` | Enable debug mode. |
| `-f`, `--format <FORMAT>` | Show output in a specific format. Possible values: `human` (default), `json`, `csv`. |
| `--no-color` | Disable color output. |
| `--service-token <TOKEN>` | The service token for authenticating. |
| `--service-token-id <TOKEN_ID>` | The service token ID for authenticating. |

---

## The budget sub-command

Manage traffic budgets.

**Usage:**

```shellscript
pscale traffic-control budget [command]
```

### Available sub-commands

| **Sub-command** | **Description** |
| --- | --- |
| `create` | Create a traffic budget |
| `delete` | Delete a traffic budget |
| `list` | List traffic budgets |
| `show` | Show a traffic budget |
| `update` | Update a traffic budget |

### The budget list sub-command

List the traffic budgets for a branch, including their IDs.

**Usage:**

```shellscript
pscale traffic-control budget list <database> <branch> [flags]
```

**Available flags:**

| **Flag** | **Description** |
| --- | --- |
| `--fingerprint string` | Only list budgets with a rule for this query fingerprint |

### The budget create sub-command

Create a traffic budget.

**Usage:**

```shellscript
pscale traffic-control budget create <database> <branch> [flags]
```

**Available flags:**

| **Flag** | **Description** |
| --- | --- |
| `--name string` | Name of the traffic budget (required) |
| `--mode string` | Mode of the budget: `enforce`, `warn`, or `off` (default `warn`) |
| `--rate int` | Rate at which capacity refills, as a percentage of server resources (0-100). Unlimited when not set. |
| `--capacity int` | Maximum capacity that can be banked (0-6000). Unlimited when not set. |
| `--burst int` | Maximum capacity a single query can consume (0-6000). Unlimited when not set. |
| `--concurrency int` | Percentage of available worker processes (0-100). Unlimited when not set. |

### The budget update sub-command

Update a traffic budget.

**Usage:**

```shellscript
pscale traffic-control budget update <database> <branch> <budget-id> [flags]
```

A budget’s `<budget-id>` can be retrieved by running the `show`, `create`, or `list` commands.

**Available flags:**

Same flags as [`budget create`](#the-budget-create-sub-command).

### The budget show sub-command

Show a traffic budget.

**Usage:**

```shellscript
pscale traffic-control budget show <database> <branch> <budget-id> [flags]
```

### The budget delete sub-command

Delete a traffic budget.

**Usage:**

```shellscript
pscale traffic-control budget delete <database> <branch> <budget-id> [flags]
```

**Available flags:**

| **Flag** | **Description** |
| --- | --- |
| `--force` | Delete a budget without confirmation |

**Aliases:** `delete`, `rm`

---

## The rule sub-command

Manage traffic rules.

**Usage:**

```shellscript
pscale traffic-control rule [command]
```

### Available sub-commands

| **Sub-command** | **Description** |
| --- | --- |
| `create` | Create a traffic rule on a budget |
| `delete` | Delete a traffic rule from a budget |

### The rule create sub-command

Create a traffic rule on a budget.

**Usage:**

```shellscript
pscale traffic-control rule create <database> <branch> <budget-id> [flags]
```

This command adds a rule to the budget. Rules are combined via OR, tags within a rule are combined via AND. Create multiple rules to get the desired AND/OR behavior.

**Available flags:**

| **Flag** | **Description** |
| --- | --- |
| `--fingerprint string` | SQL fingerprint to match |
| `--keyspace string` | Keyspace to match |
| `--kind string` | Kind of rule (default `match`) |
| `--tag stringArray` | Tag in the format `key=<key>,value=<value>,source=<source>` (repeatable) |

### The rule delete sub-command

Delete a traffic rule from a budget.

**Usage:**

```shellscript
pscale traffic-control rule delete <database> <branch> <budget-id> <rule-id> [flags]
```

**Available flags:**

| **Flag** | **Description** |
| --- | --- |
| `--force` | Delete a rule without confirmation |

**Aliases:** `delete`, `rm`

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
