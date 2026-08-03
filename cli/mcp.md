---
url: https://planetscale.com/docs/cli/mcp
title: "Mcp"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The mcp command

Manage PlanetScale model context protocol (MCP) configuration. PlanetScale recommends the **hosted** MCP server:

```text
https://mcp.pscale.dev/mcp/planetscale
```

See [PlanetScale MCP server](../connect/mcp.md) for full setup instructions across editors and agents.

**Usage:**

```shellscript
pscale mcp <SUB-COMMAND> <FLAG>
```

### Available sub-commands

| **Sub-command** | **Product** | **Description** |
| --- | --- | --- |
| `install` | Postgres, Vitess | Install or print hosted MCP setup for a target editor |
| `server` | Postgres, Vitess | Start the legacy local stdio MCP server (deprecated — prefer hosted MCP) |

### The install sub-command

**Usage:**

```shellscript
pscale mcp install --target <TARGET> --format json
```

| **Flag** | **Description** |
| --- | --- |
| `--target` | **Required.** Supported values: `cursor`, `claude-code`, `zed` |
| `-f`, `--format json` | JSON response with `status`, `message`, `next_steps`, and setup details |

**Cursor** — writes hosted MCP config to `~/.cursor/mcp.json` when possible.

**Claude Code** and **Zed** — return `action_required` JSON with the command or docs URL to run (OAuth on first MCP use).

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `--target` | Target for MCP installation. Supported values: `cursor`, `claude-code`, `zed`. |
| `-h`, `--help` | View help for `mcp` command |

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

## Examples

### Install hosted MCP for Cursor

**Command:**

```shellscript
pscale mcp install --target cursor --format json
```

**Output (JSON):**

```json
{
  "status": "ok",
  "target": "cursor",
  "hosted_mcp_url": "https://mcp.pscale.dev/mcp/planetscale",
  "message": "Hosted MCP server configured for cursor"
}
```

### Claude Code setup instructions

**Command:**

```shellscript
pscale mcp install --target claude-code --format json
```

Returns `action_required` JSON with the `claude mcp add` command in `next_steps`.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
