---
url: https://planetscale.com/docs/how-we-use-ai
title: "How We Use Ai"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# How PlanetScale uses AI

> Which PlanetScale features use generative AI, and how we ensure customer data stays secure and private.

**Platform availability:** Vitess and Postgres

## Overview

PlanetScale uses generative AI to power two [Insights](postgres/monitoring/query-insights.md) features. This overview explains those features and how PlanetScale ensures safety and privacy of customer data.

## Which features use generative AI

Two features of [Insights](postgres/monitoring/query-insights.md) use generative AI:

* Postgres [index recommendations](postgres/monitoring/schema-recommendations.md) use a large language model to interpret query patterns and identify potential indexes that could improve performance of those patterns. The prompt sent to the LLM includes one or more query patterns and one or more table schemas. Query patterns and table schemas include the names of tables and columns, but not any actual row data. This process runs automatically on each database each night.
* Query summaries for both [Postgres](postgres/monitoring/query-insights.md#query-deep-dive) and [Vitess](vitess/monitoring/query-insights.md#query-deep-dive) use a large language model to convert a query pattern to a one-sentence description. The prompt sent to the LLM contains a query pattern. This feature is invoked only when a user clicks on the "Summarize query" button on an Insights query-details page.

## Privacy and security

All AI use is covered by PlanetScale's existing [terms of service](https://planetscale.com/legal/agreement) and [subprocessor list](https://planetscale.com/legal/subprocessors). Our LLM provider, [Google Gemini](https://cloud.google.com/gemini/docs/discover/data-governance), encrypts all communication in transit, does not use prompts for training, and retains prompts only long enough to screen for abuse.

## Opting out

We understand that not every customer wants to use AI. You can disable the LLM-based features for all the databases in your account or organization on the organization settings page.
