---
url: https://planetscale.com/docs/cli/agent-guide
title: "Agent Guide"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The agent-guide command

Shows guidance for AI agents and automation using `pscale`. Use **`--format json`** for a machine-readable bootstrap response with conventions, hosted MCP details, skills install commands, and suggested next steps.

**Requires `pscale` 0.292.0 or later.** Install or upgrade with `brew install pscale` or `brew upgrade pscale`.

For a copy-paste agent setup prompt, see [Agent setup prompt](../agent-setup/prompt.md) ([Markdown export](https://planetscale.com/docs/agent-setup/prompt.md)).

**Usage:**

```shellscript
pscale agent-guide --format json
```

### JSON response fields

| Field | Description |
| --- | --- |
| `status` | `"ok"` when the guide loaded successfully |
| `guide` | Full CLI agent guide text (same content as the CLI repo `AGENTS.md`) |
| `first_command` | Suggested first command (`pscale auth check --format json`) |
| `agent_guide_command` | Command to re-fetch this guide |
| `hosted_mcp_url` | Hosted PlanetScale MCP server URL |
| `mcp_docs_url` | Link to MCP documentation |
| `skills_repo_url` | [planetscale/skills](https://github.com/planetscale/skills) repository |
| `skills_setup_command` | Clone + `script/setup` one-liner |
| `skills_npx_command` | `npx skills add planetscale/skills -g -y` |
| `skills_cli_automation_skill` | Skill id for CLI conventions (`14-pscale-cli-automation`) |
| `supported_engines` | `mysql`, `postgresql` |
| `conventions` | Short list of CLI rules for agents |
| `next_steps` | Commands to run after reading the guide |

### Global flags

| **Flag** | **Description** |
| --- | --- |
| `-f`, `--format json` | **Required for automation** — JSON on stdout |
| `--api-url <URL>` | Non-production API base URL |
| `--config <FILE>` | Config file (default `$HOME/.config/planetscale/pscale.yml`) |
| `-h`, `--help` | Help for `agent-guide` |

## Examples

**Human-readable output (default):**

```shellscript
pscale agent-guide
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
