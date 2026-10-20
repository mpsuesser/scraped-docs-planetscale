---
url: https://planetscale.com/docs/agents/grok-bot
title: "Grok Bot"
description: ""
access_date: 2026-10-20T18:26:07.257Z
current_date: 2026-10-20T18:26:07.257Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Grok Bot

> Set up PlanetScale Bot to answer database questions, investigate performance, and follow webhook events.

PlanetScale Bot helps you get started with PlanetScale, understand database performance, and fix slow or failing queries using its [MCP server](../mcp-server.md) and installed skills.

> **[Add PlanetScale Bot](https://x.ai/bot/0hgwu8KbM0FIY-idg0x6L)**
>
> Open the PlanetScale Bot by PlanetScale template, then choose Add to Grok Bot.

> **[Connect the PlanetScale plugin](https://x.ai/bot/plugin/741)**
>
> Add PlanetScale to Grok Bot and authorize access to your databases.

## Before you begin

You need a PlanetScale account and access to the databases you want to investigate. If you are new to Grok Bot, start with these resources:

* [Download Grok Bot](https://x.ai/bot) and [follow the getting-started guide](https://docs.x.ai/grok-bot/get-started).
* [Set up Grok Bot on mobile](https://docs.x.ai/grok-bot/mobile).
* [Connect plugins](https://cursor.com/help/grok-bot/connect-plugins).

## Add PlanetScale Bot and connect it to PlanetScale

**1. Review and add the bot**

Open the [PlanetScale Bot template](https://x.ai/bot/0hgwu8KbM0FIY-idg0x6L). You should see PlanetScale Bot by PlanetScale.

![PlanetScale Bot template details in Grok Bot, showing Context, PlanetScale Webhook Triage, and the PlanetScale integration](https://mintcdn.com/planetscale-2/ogPtjo9qMB1g5dXJ/agents/images/grok-bot-template-light.png?fit=max\&auto=format\&n=ogPtjo9qMB1g5dXJ\&q=85\&s=72ee6c4537f6c21ff3710f258c696156)

**2. Let the agent set itself up**

PlanetScale Bot creates its memories, skills, and **PlanetScale Webhook Triage** routine, then prompts you to install the PlanetScale plugin. The routine starts paused.

![PlanetScale Bot setting itself up and creating the PlanetScale Webhook Triage routine](https://mintcdn.com/planetscale-2/ogPtjo9qMB1g5dXJ/agents/images/grok-bot-setup-light.png?fit=max\&auto=format\&n=ogPtjo9qMB1g5dXJ\&q=85\&s=905f2681d12d818da86d737df969f384)

**3. Install the PlanetScale plugin**

Accept the PlanetScale plugin installation prompt. If you don't see it, open the [PlanetScale plugin](https://x.ai/bot/plugin/741) and choose **Add**.

![PlanetScale Bot confirming setup is complete and showing the accepted PlanetScale plugin installation prompt](https://mintcdn.com/planetscale-2/ogPtjo9qMB1g5dXJ/agents/images/grok-bot-install-light.png?fit=max\&auto=format\&n=ogPtjo9qMB1g5dXJ\&q=85\&s=8462e22410850199d6b56c0be21e6d9c)

**4. Authorize PlanetScale**

Choose **Authorize** on the PlanetScale card and follow the PlanetScale authorization flow in your browser.

![PlanetScale installed with an Authorize button on its connection card](https://mintcdn.com/planetscale-2/ogPtjo9qMB1g5dXJ/agents/images/grok-bot-authorize-light.png?fit=max\&auto=format\&n=ogPtjo9qMB1g5dXJ\&q=85\&s=4c79c057156265b298964d6171ce3550)

**5. Choose a database**

Tell the bot which organization, database, and branch to use. Ask it to verify access with a read-only check before investigating.

For example:

```text theme={null}
Use PlanetScale organization <organization>, database <database>, branch <branch>.
Confirm you can access it, then explain which queries account for the most
execution time in Insights. Show your evidence and suggested next steps.
Do not change the database.
```

## Connect a codebase (optional)

With access to your repository, PlanetScale Bot can trace database issues back to your code and help fix them via [Cursor Cloud Agents](https://cursor.com/docs/cloud-agent). Tell the bot which repository to work on. If it is not already connected to Cursor, connect your source control account through [Cursor Integrations](https://cursor.com/dashboard/integrations). See [Work with Grok Bot](https://cursor.com/docs/grok-bot/work) for more.

## Setting up the webhook routine

Webhooks are optional. They let PlanetScale Bot investigate events as they arrive, check whether database changes completed, and flag what needs your attention. Without webhooks, the bot responds when you ask.

**1. Configure and enable the routine**

Ask the bot to configure **PlanetScale Webhook Triage** for your chosen organizations and databases, then turn it on. Open the routine to find its **Webhook URL** and **Authorization header**.

![PlanetScale Webhook Triage routine settings showing the active toggle and webhook endpoint, key, and header with values obscured](https://mintcdn.com/planetscale-2/0gogNjLFt8syD9n_/agents/images/grok-bot-webhook-settings-light.png?fit=max\&auto=format\&n=0gogNjLFt8syD9n_\&q=85\&s=9af534edae64e00d9f157d8d2933b91e)

**2. Add the webhook in PlanetScale**

In your database's **Settings → Webhooks → Add webhook**, paste the routine's Webhook URL into **URL** and the header value into **Authorization header**, including the scheme, such as `Bearer <token>`, without the `Authorization:` label.

![PlanetScale Add webhook form with URL, Authorization header, and event selection fields](https://mintcdn.com/planetscale-2/Qv8bmPHOBgofISPU/agents/images/grok-bot-add-webhook-light.png?fit=max\&auto=format\&n=Qv8bmPHOBgofISPU\&q=85\&s=60e2c37b6fe9aa35fabb6917aeebe60f)

**3. Choose events and test delivery**

Choose the [events you want the bot to follow](../api/webhook-events.md), save the webhook, and use **Test webhook**. Confirm delivery in PlanetScale and acknowledgment in Grok Bot.

Webhooks cover supported events; they don't notify the bot about every slow query or CPU spike.

See [Setting up webhooks](../api/webhooks.md) for more details.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
