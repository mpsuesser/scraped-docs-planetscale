---
url: https://planetscale.com/docs/connect/ai-tooling
title: "Ai Tooling"
description: ""
access_date: 2026-08-20T18:00:05.296Z
current_date: 2026-08-20T18:00:05.296Z
---

## Agent setup prompt

Official bootstrap instructions for AI agents — install skills, configure MCP, and authenticate the CLI. Fetch as Markdown for agent sessions:

## Agent setup prompt

Copy or fetch the [Markdown export](https://planetscale.com/docs/agent-setup/prompt.md) (`https://planetscale.com/docs/agent-setup/prompt.md` on production)

If the PlanetScale CLI is installed (**`pscale` 0.292.0+**), agents can also run:

```shellscript
pscale agent-guide --format json
pscale auth check --format json
```

Upgrade with `brew upgrade pscale` if `agent-guide` is unknown.

See [CLI agent-guide](../cli/agent-guide.md) for the JSON response fields.

## PlanetScale MCP Server

Authenticated MCP servers allow your AI tools to access information specific to your PlanetScale account. Access your PlanetScale organizations, databases, branches, schema, and Insights data from within your development tools.

The hosted MCP server can be installed in development tools as well as other MCP-accessible tools like Claude in the web browser and desktop app.

## PlanetScale MCP Server

Install in Claude Code, Cursor, VS Code and more

## Automated Database Optimization

Use the MCP server with AI coding agents to continuously optimize your database. Agents can review production Insights data and Schema Recommendations, then open pull requests with performance improvements — on a recurring schedule.

## Self-improving database

Set up automated agents that optimize your database daily

## PlanetScale Skills

Operational workflows for agents — read-only assessment, safety review, Insights analysis, schema recommendations, Traffic Control, and change gates — live in the public skills pack:

## planetscale/skills

Install assessment and automation skills for Cursor, Claude Code, and more

**Setup script (recommended):**

```shellscript
git clone https://github.com/planetscale/skills.git
cd skills && script/setup
```

**Skills CLI:**

```shellscript
npx skills add planetscale/skills -g -y
```

Load **`14-pscale-cli-automation`** for CLI conventions and **`00-safe-orchestrator`** for a full PlanetScale assessment. See the [skills README](https://github.com/planetscale/skills) for usage and the Class A–E safety model.

## Database Skills (MySQL / Vitess query patterns)

For MySQL- and Vitess-specific query and schema guidance (separate from the operational skills pack above):

```shellscript
npx skills add planetscale/database-skills
```

Check out the [Database Skills](https://database-skills.com/) website for more information.

## Claude Code Plugin

The [PlanetScale Claude Code Plugin](https://github.com/planetscale/claude-plugin) is a convenient way to install the PlanetScale MCP server into Claude Code.

In Claude Code, add this GitHub repository as a marketplace, then install the plugin:

```shellscript
/plugin marketplace add planetscale/claude-plugin
/plugin install planetscale@planetscale-plugins
```

You may need to restart your Claude Code session before the MCP server is available.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
