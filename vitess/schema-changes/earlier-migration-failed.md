---
url: https://planetscale.com/docs/vitess/schema-changes/earlier-migration-failed
title: "Earlier Migration Failed"
description: ""
access_date: 2026-10-09T14:37:23.319Z
current_date: 2026-10-09T14:37:23.319Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Earlier migration failed

> A later table in a deploy request stops when an earlier migration in that deploy has failed.

**Platform availability:** Vitess only

PlanetScale runs the migrations in one deploy request in order. If an earlier migration fails or is cancelled, every later migration in that deploy stops:

```text theme={null}
migration bbbbbbbb_bbbb_bbbb_bbbb_bbbbbbbbbbbb cannot run because prior migration aaaaaaaa_aaaa_aaaa_aaaa_aaaaaaaaaaaa in same context has failed/was cancelled
```

This table did not fail on its own. The migration named after `prior migration` is the one that failed. Later tables stay blocked until that earlier migration succeeds.

## How to fix it

Find the first failed table in the deploy request and fix that error. Then retry the deploy.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
