---
url: https://planetscale.com/docs/how-we-use-ai
title: "How We Use Ai"
description: ""
access_date: 2026-11-17T17:39:52.141Z
current_date: 2026-11-17T17:39:52.141Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# How PlanetScale uses AI

> Which PlanetScale features use generative AI, and how we ensure customer data stays secure and private.

Available for Vitess and Postgres.

## Overview

PlanetScale uses generative AI to power [Insights](https://planetscale.com/docs/postgres/monitoring/query-insights.md) features. This overview explains those features and how PlanetScale ensures safety and privacy of customer data.

## Which features use generative AI

Features of [Insights](https://planetscale.com/docs/postgres/monitoring/query-insights.md) which use generative AI:

* Postgres [index recommendations](https://planetscale.com/docs/postgres/monitoring/schema-recommendations.md) use a large language model to interpret query patterns and identify potential indexes that could improve performance of those patterns. The prompt sent to the LLM includes one or more query patterns and one or more table schemas. Query patterns and table schemas include the names of tables and columns, but not any actual row data. This process runs automatically on each database each night.
* Query summaries for both [Postgres](https://planetscale.com/docs/postgres/monitoring/query-insights.md#query-deep-dive) and [Vitess](https://planetscale.com/docs/vitess/monitoring/query-insights.md#query-deep-dive) use a large language model to convert a query pattern to a one-sentence description. The prompt sent to the LLM contains a query pattern. This feature is invoked only when a user clicks on the "Summarize query" button on an Insights query-details page.
* [Insights Agent](#insights-agent) uses a large language model to write code changes that fix slow queries and apply schema recommendations, and opens them as pull requests in your connected repositories.

## Insights Agent

[Insights Agent](https://planetscale.com/docs/insights-agent.md) opens pull requests in your connected repositories that fix slow queries and apply [schema recommendations](https://planetscale.com/docs/postgres/monitoring/schema-recommendations.md). It runs on demand or on a schedule.

To generate its recommendations, Insights Agent uses:

* Code from the connected repository.
* Query and schema information for the database branch, such as query patterns, query statistics and sample executions, query tags, table schemas, and schema recommendations.

Insights Agent can't connect to your database or run queries, so it never reads table rows. This data is processed by PlanetScale and our AI providers, Anthropic and OpenAI, through Cloudflare.

The description of each pull request can include query patterns, table and column names, and performance numbers. If the repository is public, anyone who can see the pull request can see this information.

## Privacy and security

All AI use is covered by PlanetScale's existing [terms of service](https://planetscale.com/legal/agreement) and [subprocessor list](https://planetscale.com/legal/subprocessors). Our LLM providers are [Google Gemini](https://cloud.google.com/gemini/docs/discover/data-governance), [Anthropic](https://www.anthropic.com/legal/commercial-terms), and [OpenAI](https://developers.openai.com/api/docs/guides/your-data).

* Google Gemini encrypts all communication in transit, does not use prompts for training, and retains prompts only long enough to screen for abuse.
* Anthropic and OpenAI do not train on customer data. We send requests to them with our own provider keys through [Cloudflare AI Gateway](https://developers.cloudflare.com/ai-gateway/). Cloudflare does not train on customer data or retain it for other purposes.

## Opting out

We understand that not every customer wants to use AI. You can disable the LLM-based features for all the databases in your account or organization on the organization settings page.

Insights Agent has its own organization setting, separate from the LLM setting. It is on by default, and organization administrators can turn it on or off on the organization settings page. Insights Agent requires LLM usage, so turning off LLM usage also turns off Insights Agent.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
