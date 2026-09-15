---
url: https://planetscale.com/docs/agents/grok-bot
title: "Grok Bot"
description: ""
access_date: 2026-09-15T19:21:39.360Z
current_date: 2026-09-15T19:21:39.360Z
---

PlanetScale Bot helps you get started with PlanetScale, understand database performance, and fix slow or failing queries using its [MCP server](../mcp-server.md) and installed skills.

## Add PlanetScale Bot

Open the PlanetScale Bot by PlanetScale template, then choose Add to Grok Bot.

## Connect the PlanetScale plugin

Add PlanetScale to Grok Bot and authorize access to your databases.

## Before you begin

You need a PlanetScale account and access to the databases you want to investigate. If you are new to Grok Bot, start with these resources:

- [Download Grok Bot](https://x.ai/bot) and [follow the getting-started guide](https://docs.x.ai/grok-bot/get-started).
- [Set up Grok Bot on mobile](https://docs.x.ai/grok-bot/mobile).
- [Connect plugins](https://cursor.com/help/grok-bot/connect-plugins).

## Add PlanetScale Bot and connect it to PlanetScale

For example:

```text
Use PlanetScale organization <organization>, database <database>, branch <branch>.
Confirm you can access it, then explain which queries account for the most
execution time in Insights. Show your evidence and suggested next steps.
Do not change the database.
```

## Connect a codebase (optional)

With access to your repository, PlanetScale Bot can trace database issues back to your code and help fix them via [Cursor Cloud Agents](https://cursor.com/docs/cloud-agent). Tell the bot which repository to work on. If it is not already connected to Cursor, connect your source control account through [Cursor Integrations](https://cursor.com/dashboard/integrations). See [Work with Grok Bot](https://cursor.com/docs/grok-bot/work) for more.

## Setting up the webhook routine

Webhooks are optional. They let PlanetScale Bot investigate events as they arrive, check whether database changes completed, and flag what needs your attention. Without webhooks, the bot responds when you ask.

See [Setting up webhooks](../api/webhooks.md) for more details.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
