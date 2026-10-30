---
url: https://planetscale.com/docs/ai-tooling
title: "Ai Tooling"
description: ""
access_date: 2026-10-30T18:26:51.105Z
current_date: 2026-10-30T18:26:51.105Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# AI-enhanced development

> Improve your database development experience with AI-powered tooling specifically designed for PlanetScale

Available for Vitess and Postgres.

## Agents

Use the [PlanetScale Bot for Grok Bot](https://planetscale.com/docs/agents/grok-bot.md) to answer database questions, investigate performance, and follow webhook events. See [Agents](https://planetscale.com/docs/agents.md) for the template and other ways to connect an agent to PlanetScale.

## Agent setup prompt

Official bootstrap instructions for AI agents — install skills, configure MCP, and authenticate the CLI. Fetch as Markdown for agent sessions:

> [card] [Agent setup prompt](https://planetscale.com/docs/agent-setup/prompt.md)
>
> Copy or fetch the [Markdown export](https://planetscale.com/docs/agent-setup/prompt.md) (`https://planetscale.com/docs/agent-setup/prompt.md` on production)

If the PlanetScale CLI is installed (**`pscale` 0.292.0+**), agents can also run:

```bash theme={null}
pscale agent-guide --format json
pscale auth check --format json
```

Upgrade with `brew upgrade pscale` if `agent-guide` is unknown.

See [CLI agent-guide](https://planetscale.com/docs/cli/agent-guide.md) for the JSON response fields.

## PlanetScale MCP Server

Authenticated MCP servers allow your AI tools to access information specific to your PlanetScale account.
Access your PlanetScale organizations, databases, branches, schema, and Insights data from within your development tools.

The hosted MCP server can be installed in development tools as well as other MCP-accessible tools like Claude in the web browser and desktop app.

> [card] [PlanetScale MCP Server](https://planetscale.com/docs/mcp-server.md)
>
> Install in Claude Code, Cursor, VS Code and more

## Automated Database Optimization

Use the MCP server with AI coding agents to continuously optimize your database. Agents can review production Insights data and Schema Recommendations, then open pull requests with performance improvements — on a recurring schedule.

> [card] [Self-improving database](https://planetscale.com/docs/self-improving-database.md)
>
> Set up automated agents that optimize your database daily

## PlanetScale Skills

Operational workflows for agents — read-only assessment, safety review, Insights analysis, schema recommendations, Traffic Control, and change gates — live in the public skills pack:

> [card] [planetscale/skills](https://github.com/planetscale/skills)
>
> Install assessment and automation skills for Cursor, Claude Code, and more

**Setup script (recommended):**

```bash theme={null}
git clone https://github.com/planetscale/skills.git
cd skills && script/setup
```

**Skills CLI:**

```bash theme={null}
npx skills add planetscale/skills -g -y
```

Load **`planetscale-pscale-cli-automation`** for CLI conventions and **`planetscale-safe-orchestrator`** for a full PlanetScale assessment. See the [skills README](https://github.com/planetscale/skills) for usage and the Class A–E safety model.

## Database Skills (MySQL / Vitess query patterns)

For MySQL- and Vitess-specific query and schema guidance (separate from the operational skills pack above):

```bash theme={null}
npx skills add planetscale/database-skills
```

Check out the [Database Skills](https://database-skills.com/) website for more information.

## Claude Code Plugin

The [PlanetScale Claude Code Plugin](https://github.com/planetscale/claude-plugin) is a convenient way to install the PlanetScale MCP server into Claude Code.

In Claude Code, add this GitHub repository as a marketplace, then install the plugin:

```bash theme={null}
/plugin marketplace add planetscale/claude-plugin
/plugin install planetscale@planetscale-plugins
```

You may need to restart your Claude Code session before the MCP server is available.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
