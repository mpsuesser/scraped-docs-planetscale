---
url: https://planetscale.com/docs/agent-setup/prompt
title: "Prompt"
description: ""
access_date: 2026-08-20T18:00:05.296Z
current_date: 2026-08-20T18:00:05.296Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Agent setup prompt

> PlanetScale instructions for AI agents to install skills, configure MCP, and authenticate the CLI.

**Platform availability:** Vitess and Postgres

PlanetScale instructions for setting up AI agents with PlanetScale Postgres and Vitess/MySQL.

Use the [Markdown export](https://planetscale.com/docs/agent-setup/prompt.md) when pasting this prompt into an agent session. Agents with the PlanetScale CLI installed can also fetch setup instructions in JSON:

```bash theme={null}
pscale agent-guide --format json
```

Commands on this page require `pscale` 0.292.0 or later. Install or upgrade the CLI in the prerequisites below before running the setup steps.

Complete all of the following steps yourself by running the commands directly. Do not ask the user to run setup commands unless a step requires browser approval they must perform.

***

## Install PlanetScale Skills and MCP

Use the section for your agent below. Skills cover operational workflows (assessment, safety review, Insights, schema recommendations). The hosted MCP server gives MCP clients access to PlanetScale account data.

### Prerequisites

Install the PlanetScale CLI if it is not already available (**0.292.0 or later**):

```bash theme={null}
brew install pscale
brew upgrade pscale
```

Verify the CLI version and agent automation support:

```bash theme={null}
pscale version
pscale agent-guide --format json
pscale auth check --format json
```

If `agent-guide` is unknown or `auth check --format json` prints nothing, upgrade with `brew upgrade pscale`.

If `auth check` returns `"status": "action_required"`, follow `issues` and `next_steps` in the JSON (typically `pscale auth login --format json` or service-token flags for CI).

Always pass `--format json` on `pscale` commands in automation. Put `--org` on resource subcommands (`database`, `branch`, `sql`, …), not on root `pscale`.

***

### Install skills (all agents)

**Setup script (recommended):**

```bash theme={null}
git clone https://github.com/planetscale/skills.git
cd skills && script/setup
```

The script installs to every agent it finds (`~/.cursor/skills/`, `~/.claude/skills/`, `~/.agents/skills/`). For other agents, pass the skills directory explicitly:

```bash theme={null}
script/setup ~/.codex/skills
```

**Skills CLI:**

```bash theme={null}
npx skills add planetscale/skills -g -y
```

After installing, load skill **`14-pscale-cli-automation`** for CLI conventions and **`00-safe-orchestrator`** when the user asks for a full PlanetScale assessment. See [github.com/planetscale/skills](https://github.com/planetscale/skills).

***

### Cursor, GitHub Copilot, and VS Code

**MCP** — add to `.cursor/mcp.json`, `.vscode/mcp.json`, or your agent's MCP config under `"mcpServers"`:

```json theme={null}
"planetscale": {
  "url": "https://mcp.pscale.dev/mcp/planetscale"
}
```

OAuth triggers automatically on first PlanetScale MCP tool use. See [PlanetScale MCP server](../connect/mcp.md) for details.

**Skills** — use `script/setup` above or install into `<project>/.cursor/skills/` for project-scoped workflows.

***

### Claude Code

Hosted MCP (recommended):

```bash theme={null}
claude mcp add --transport http "planetscale" https://mcp.pscale.dev/mcp/planetscale
```

Install skills with `npx skills add planetscale/skills -g -y` or `script/setup`.

***

### Zed

Add the hosted MCP server to your Zed settings, using the URL above. See
[PlanetScale MCP server](../connect/mcp.md) for the current setup steps.

Install skills with `script/setup` or `npx skills add planetscale/skills -g -y`.

***

### Headless / CI (no browser)

Use a [PlanetScale service token](../cli/service-tokens.md) instead of OAuth login. Pass credentials on each command:

```bash theme={null}
pscale auth check --format json \
  --service-token-id "$PLANETSCALE_SERVICE_TOKEN_ID" \
  --service-token "$PLANETSCALE_SERVICE_TOKEN"
```

**Do not retry** these under service-token auth. The failure is by design. See the [service token automation matrix](../cli.md#service-token-automation) for full guidance.

| Command                            | Exact error                                                                               | Use instead                                                        |
| :--------------------------------- | :---------------------------------------------------------------------------------------- | :----------------------------------------------------------------- |
| `pscale org show`                  | `not authenticated yet. Please run 'pscale auth login'`                                   | `pscale org list --format json`, then `PLANETSCALE_ORG` or `--org` |
| `pscale service-token list`        | `pscale service-token list is unavailable when authenticated with a service token`        | `pscale api organizations/<org>/service-tokens --format json`      |
| `pscale service-token show-access` | `pscale service-token show-access is unavailable when authenticated with a service token` | `pscale api organizations/<org>/service-tokens/<id> --format json` |

***

## Project context (application repositories)

In the user's application repo, add or update a **project** `AGENTS.md` with:

* PlanetScale organization, database, branch, and engine (Postgres or Vitess)
* Whether agents may run write SQL or only read-only queries
* Approval rules for schema changes, Traffic Control, webhooks, and credentials

Do not edit project `AGENTS.md` without user approval. See skill `09-mcp-agent-operating-model` in [planetscale/skills](https://github.com/planetscale/skills).

***

## Verify setup

Confirm **`pscale` 0.292.0+** and JSON auth work, then run:

```bash theme={null}
pscale agent-guide --format json
pscale auth check --format json
pscale org list --format json
```

When setup is complete, tell the user:

```text theme={null}
PlanetScale Agent Setup Complete

✓ Skills   installed (planetscale/skills)
✓ MCP      https://mcp.pscale.dev/mcp/planetscale
✓ CLI      pscale auth check --format json

⚡ Restart your agent to load MCP servers
```

***

## Resources

| Resource                           | URL                                                                       |
| ---------------------------------- | ------------------------------------------------------------------------- |
| This prompt (Markdown)             | `https://planetscale.com/docs/agent-setup/prompt.md`                      |
| CLI agent guide                    | `pscale agent-guide --format json` or [CLI agent-guide](../cli/agent-guide.md) |
| PlanetScale skills                 | [github.com/planetscale/skills](https://github.com/planetscale/skills)    |
| MCP setup                          | [PlanetScale MCP server](../connect/mcp.md)                                    |
| AI & automation overview           | [AI-enhanced development](../connect/ai-tooling.md)                            |
| `pscale sql` (non-interactive SQL) | [CLI sql](../cli/sql.md)                                                       |

These instructions are published at `https://planetscale.com/docs/agent-setup/prompt.md` so you can re-verify their authenticity at any time.
