---
url: https://planetscale.com/docs/agent-setup/prompt
title: "Prompt"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Agent setup prompt

> PlanetScale instructions for AI agents to install skills, configure MCP, and authenticate the CLI.

export const PlatformAvailability = ({current, vitess, postgres}) => {
  const docsHref = path => {
    if (!path) return path;
    const normalized = path.startsWith('/') ? path : `/${path}`;
    return normalized;
  };
  const labels = {
    vitess: 'Vitess',
    postgres: 'Postgres'
  };
  if (current === 'both') {
    return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
        <span data-engine="both" data-state="current" aria-current="true" className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
          Vitess and Postgres
        </span>
      </div>;
  }
  const hasVitess = current === 'vitess' || Boolean(vitess);
  const hasPostgres = current === 'postgres' || Boolean(postgres);
  const only = !(hasVitess && hasPostgres);
  const engines = [];
  if (current === 'vitess' || current === 'postgres') engines.push(current);
  if (hasVitess && current !== 'vitess') engines.push('vitess');
  if (hasPostgres && current !== 'postgres') engines.push('postgres');
  return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
      {engines.map(engine => {
    const isCurrent = current === engine;
    const href = docsHref(engine === 'vitess' ? vitess : postgres);
    const label = only ? `${labels[engine]} only` : labels[engine];
    const state = isCurrent || !href ? 'current' : 'link';
    if (isCurrent || !href) {
      return <span key={engine} data-engine={engine} data-state={state} aria-current={isCurrent ? 'true' : undefined} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
              {label}
            </span>;
    }
    return <a key={engine} href={href} data-engine={engine} data-state={state} title={`View ${labels[engine]} documentation`} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
            {label}
            <svg aria-hidden="true" width="12" height="12" viewBox="0 0 12 12" fill="none" className="shrink-0">
              <path d="M2.5 6h7M6.5 3l3 3-3 3" stroke="currentColor" strokeWidth="1.5" strokeLinecap="round" strokeLinejoin="round" />
            </svg>
          </a>;
  })}
    </div>;
};

<PlatformAvailability current="both" />

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

Or use the CLI helper for Cursor:

```bash theme={null}
pscale mcp install --target cursor --format json
```

OAuth triggers automatically on first PlanetScale MCP tool use. See [PlanetScale MCP server](../connect/mcp.md) for details.

**Skills** — use `script/setup` above or install into `<project>/.cursor/skills/` for project-scoped workflows.

***

### Claude Code

Hosted MCP (recommended):

```bash theme={null}
claude mcp add --transport http "planetscale" https://mcp.pscale.dev/mcp/planetscale
```

Or ask the CLI for the current command:

```bash theme={null}
pscale mcp install --target claude-code --format json
```

Install skills with `npx skills add planetscale/skills -g -y` or `script/setup`.

***

### Zed

See the current MCP docs — the CLI prints setup instructions:

```bash theme={null}
pscale mcp install --target zed --format json
```

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
