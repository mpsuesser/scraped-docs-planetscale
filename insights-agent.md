---
url: https://planetscale.com/docs/insights-agent
title: "Insights Agent"
description: ""
access_date: 2026-11-17T22:48:04.450Z
current_date: 2026-11-17T22:48:04.450Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Insights Agent

> Trigger agents that automatically fix schema issues and slower queries all from within the PlanetScale platform

Available for Vitess, Neki, and Postgres.

Insights Agent is an AI agent that combines [Query Insights](https://planetscale.com/docs/postgres/monitoring/query-insights.md) data, [schema recommendations](https://planetscale.com/docs/postgres/monitoring/schema-recommendations.md), and the code in your application's repository to improve your database. When the evidence supports a change, it opens a pull request with a single, focused improvement for you to review.

Insights Agent only makes suggestions.
It does not automatically merge pull requests, apply deploy requests, or write to your database.

## How it works

After connecting a GitHub or Cursor Origin repository to your PlanetScale database, you can initiate Insights agent runs.
These run in a sandbox with access to the code from the repository, plus context about your database, such as schema and Insights performance data.
Each Insights Agent run does the following:

1. **Collect evidence.** Insights Agent gathers data for the queries or recommendations the run targets, such as query patterns, rows read compared with rows returned, index usage, how often a query runs, and [query tags](https://planetscale.com/docs/postgres/monitoring/query-tags.md).
2. **Clone the repository.** The connected repository is cloned into a temporary sandbox.
3. **Analyze the code.** The agent finds the code responsible for the queries and decides whether a change is justified. Changes follow the conventions already in your repository, such as your ORM, framework index APIs, and migration tooling.
4. **Open a pull request.** If the agent finds a worthwhile change, it opens a pull request that explains what changed and why. On Vitess databases that meet the [requirements](#deploy-requests-on-vitess), it also opens a linked deploy request.

If the evidence doesn't support a change, the run finishes with a short explanation instead of a pull request.

## Prerequisites

In order to use, you must first have the following configured.

* **Insights Agent and LLM usage enabled** for your organization. Insights Agent is on by default. See [Turn Insights Agent on or off](#turn-insights-agent-on-or-off).
* **A connected repository.** An organization administrator connects a GitHub or Cursor Origin repository to the database. See [Connect a repository](#connect-a-repository).
* **A payment method on file.** Organizations without a payment method, or with unpaid invoices, can't start runs.
* **The Manage Insights Agent permission** to start runs or change schedules. See [Permissions](#permissions).

## Turn Insights Agent on or off

Insights Agent is on by default.
Organization administrators can turn it on or off from **Settings** > **AI features** in your organization.
It takes no action until a repository is connected to a database.
Insights Agent depends on the **LLM usage** setting, so turning off LLM usage also turns off Insights Agent.

![Organization AI features settings with LLM usage and Insights Agent both enabled](https://mintcdn.com/planetscale-2/zfLczGsVZ6WcfFEo/images/insights-agent/ai-features.png?fit=max\&auto=format\&n=zfLczGsVZ6WcfFEo\&q=85\&s=73470b2607d38fa6b27c0fd2bccbcccc)

## Connect a repository

You need to connect your database to the repository that contains its associated application code.

1. In the [PlanetScale dashboard](https://app.planetscale.com), select your database.
2. Go to **Settings** > **Integrations**.
3. Under **Repositories**, click **Connect Cursor Origin** or **Connect GitHub**.
4. Install the PlanetScale app on your account or organization when prompted, then choose a repository.

![Database Integrations settings with options to connect a Cursor Origin or GitHub repository](https://mintcdn.com/planetscale-2/zfLczGsVZ6WcfFEo/images/insights-agent/repository-integrations.png?fit=max\&auto=format\&n=zfLczGsVZ6WcfFEo\&q=85\&s=16938204096e2670d4f4fabe3c36bd09)

A database connects to one repository at a time. Several databases can connect to the same repository. To switch providers, disconnect the current repository first.

## Run types

### Slow query runs

Slow query runs look for slow queries in Insights and for changes that would speed them up.
That might be application code, such as an ORM call or a large list of literal values, the SQL itself, or a new index added through your migrations.
Slow query runs don't change database settings.

### Schema recommendation runs

Schema recommendation runs take open schema recommendations and make the equivalent change in your repository, including a migration in the format your project uses, if applicable.

Schema recommendation runs act on the following recommendation types:

* Adding indexes for inefficient queries
* Removing redundant indexes
* Dropping unused indexes
* Preventing primary key ID exhaustion
* Upgrading legacy character sets and collations

Insights Agent doesn't act on recommendations to drop unused tables or rebuild bloated tables and indexes.

## Start a run

### From a query or recommendation

Query detail pages and schema recommendation pages in Insights have a **Fix with agent** button.
Click it, check the connected repository, then click **Start agent run**.
This menu also lists previous runs for that query or recommendation.

![Fix with agent menu on a schema recommendation, showing the connected GitHub repository, the Start agent run button, and previous runs](https://mintcdn.com/planetscale-2/zfLczGsVZ6WcfFEo/images/insights-agent/fix-with-agent.png?fit=max\&auto=format\&n=zfLczGsVZ6WcfFEo\&q=85\&s=eb661e361cc6545637a0151bc1e123d2)

You can also start a run from the CLI or the [MCP server](https://planetscale.com/docs/mcp-server.md). Pass up to five `--fingerprint` values with their `--keyspace`, or up to five `--schema-recommendation` numbers. The two target types cannot be combined.

```bash theme={null}
pscale insights agent runs create <database> <branch> --org <org> --fingerprint <fingerprint> --keyspace <keyspace>
pscale insights agent runs create <database> <branch> --org <org> --schema-recommendation <number>
```

See [`pscale insights agent`](cli/insights.md#the-agent-sub-command) for list, show, and cancel.
From MCP, use `planetscale_start_insights_agent_run`, `planetscale_list_insights_agent_runs`, `planetscale_get_insights_agent_run`, and `planetscale_cancel_insights_agent_run`.

### On a schedule

Each run type has its own schedule on each branch.

1. Go to **Insights** > **Agent** and select a branch.
2. Turn on **Enable automatic recommendation runs** or **Enable automatic slow query runs**.
3. Set **Run at** to a time of day, and **Repeat** to **Daily**, **Weekly**, or **Monthly**.
   Weekly schedules run on a day of the week, and monthly schedules on a day of the month or the last day.

From the CLI, `<run-type>` is `slow_query` or `schema_recommendation`. `--frequency` is `day`, `week`, or `month`. Weekly schedules use `--day-of-week` from 0 (Sunday) to 6 (Saturday). Monthly schedules use `--day-of-month` from 1 to 31 (shorter months run on their last day). Flags you omit keep their current value.

```bash theme={null}
pscale insights agent schedules update <database> <branch> slow_query --org <org> --enabled --frequency week --day-of-week 1 --time 03:00 --time-zone America/New_York
pscale insights agent schedules update <database> <branch> schema_recommendation --org <org> --enabled=false
```

From MCP, `planetscale_update_insights_agent_schedule` changes a schedule and `planetscale_get_insights_agent_schedules` lists them.

Scheduled runs choose their own targets.
A scheduled run is skipped, at no cost, when there is nothing to work on:

* A recommendation run is skipped when the branch has no open recommendations the agent can act on, or when all of them already has an open pull request from Insights Agent.
* A slow query run is skipped when no queries from your application appeared in Insights in the last 24 hours.

Scheduled runs also avoid queries and recommendations that already have an open pull request from Insights Agent.

> [!NOTE]
>
> Schema recommendations are generated only for a database's default branch, so schedule recommendation runs on that branch.

## Follow a run

Go to **Insights** > **Agent** to see every run on a branch, with its status, its run type, whether it was scheduled or started manually, and who started it.

![Insights Agent page listing runs with their status, type, and who started them, next to the connected repository and schedule settings](https://mintcdn.com/planetscale-2/zfLczGsVZ6WcfFEo/images/insights-agent/agent-runs.png?fit=max\&auto=format\&n=zfLczGsVZ6WcfFEo\&q=85\&s=a29017af43328f0977daac25b15e6b9c)

| Status | Meaning |
| - | - |
| **Queued** | Waiting to start. See [Limits](#limits). |
| **Submitting** | Being handed to the agent. |
| **Running** | The agent is working on the run. |
| **Completed** | The run finished. It either opened a pull request or explained why no change was made. When it opened a pull request, the list shows the pull request's state instead, such as **PR open**. |
| **Skipped** | The scheduled run had nothing to work on. |
| **Failed** | The run couldn't finish. The run page shows the reason. |
| **Cancelled** | Someone cancelled the run. |

Select a run to see its timeline: the queries or recommendations it loaded, each stage as it happens (setting up the sandbox, collecting evidence, cloning the repository, analyzing the codebase, and opening the pull request), and the result.
Slow query runs also graph the query's p50 and p95 latency from the day before the run, and indicate when the pull request merged so you can compare before and after.

To stop a run that hasn't finished, click **Cancel agent run** on the run page.
A cancelled run can't be resumed, but you can start a new one.

## Review the pull request

Pull requests from Insights Agent explain the problem with links to the affected queries or recommendations in Insights, describe the fix, and link back to the run in PlanetScale.
Review them like any other pull request: run your CI, test the change, and merge or close it.

### Deploy requests on Vitess

On a Vitess database, a schema change in the pull request also gets a deploy request when the database has [safe migrations](https://planetscale.com/docs/vitess/schema-changes/safe-migrations.md) turned on and [Automatically copy migration data](https://planetscale.com/docs/vitess/schema-changes/branching.md#automatically-copy-migration-data) configured.
Insights Agent:

1. Creates a development branch named `insights-agent-<run ID>` from the run's branch.
2. Runs your repository's migration tool against that branch.
3. Opens a deploy request into the run's branch, linked from the pull request.

Merging the pull request doesn't deploy the schema change.
Deploy the deploy request as you normally would.
The development branch is deleted after the deploy request deploys.
If you close the pull request, Insights Agent closes the deploy request and deletes the development branch.

### Postgres and Neki

On Postgres and Neki databases, schema changes are included in the pull request as migrations that follow your project's conventions.
Apply them through your usual migration and deployment process.

## Limits

* **One repository per database.**
* **One active run per branch**, and up to three active runs per organization. Additional runs are queued.
* **Up to five queries or recommendations per run.** Related queries or recommendations can be combined into one run.
* **No custom instructions.** Insights Agent infers your conventions from your code. It doesn't read instruction files like `AGENTS.md`, and the prompt can't be edited. To control the prompt yourself, see [Insights Agent or your own agent](#insights-agent-or-your-own-agent).

## Billing

Each completed run is billed by usage, based on the tokens used to investigate and prepare the change.
The run page shows each run's cost.

* **Completed runs are billed**, including runs that finish without proposing a change.
* **Skipped, failed, and cancelled runs are free.**
* Charges appear on your monthly invoice under **Insights Agent**, with a count of completed runs.
* The development branch created for a [deploy request](#deploy-requests-on-vitess) is billed like any other development branch while it exists.

## Permissions

| Action | Who can do it |
| - | - |
| Turn Insights Agent on or off | Organization administrators |
| Connect a repository | Organization administrators |
| Disconnect a repository | Organization administrators and database administrators |
| Start runs and change schedules | Organization administrators, database administrators, and custom roles with the **Manage Insights Agent** permission |
| View runs | Anyone who can view Query Insights for the database |
| Start, cancel, or schedule runs with a service token | Token with `write_insights_agent` |
| List and view runs with a service token | Token with `read_insights_agent` |

## Security and data

* Runs happen in a sandbox that exists only for the duration of the run.
* Each run gets short-lived credentials scoped to cloning the connected repository and reading Insights data. They're revoked when the run ends.
* Insights Agent sends code from the connected repository, query patterns, and schema information to third-party LLM providers. This data isn't used to train models. See [How PlanetScale uses AI](https://planetscale.com/docs/how-we-use-ai.md).

## Insights Agent or your own agent

The [PlanetScale MCP server](https://planetscale.com/docs/mcp-server.md) and [PlanetScale CLI](https://planetscale.com/docs/cli/insights.md) give your own agents access to the same Insights data and schema recommendations. Our [self-improving database doc](https://planetscale.com/docs/self-improving-database.md) shows how to run agents in a schedule with your own prompts.

Use your own agent when you want to choose the model, write the prompt and house rules, benchmark changes before opening a pull request, or work across repositories that aren't connected to a database.

Use Insights Agent when you want the same loop without managing prompts, credentials, schedules, or deploy requests yourself.
Start a hosted run with `pscale insights agent runs create` or `planetscale_start_insights_agent_run` once a repository is connected.
Insights agent is a low-friction way to get better code and a faster database.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
