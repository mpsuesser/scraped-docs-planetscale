---
url: https://planetscale.com/docs/vitess/schema-changes/earlier-migration-failed
title: "Earlier Migration Failed"
description: ""
access_date: 2026-11-09T19:12:16.262Z
current_date: 2026-11-09T19:12:16.262Z
---

PlanetScale runs the migrations in one deploy request in order. If an earlier migration fails or is cancelled, every later migration in that deploy stops:

```text
migration bbbbbbbb_bbbb_bbbb_bbbb_bbbbbbbbbbbb cannot run because prior migration aaaaaaaa_aaaa_aaaa_aaaa_aaaaaaaaaaaa in same context has failed/was cancelled
```

This table did not fail on its own. The migration named after `prior migration` is the one that failed. Later tables stay blocked until that earlier migration succeeds.

## How to fix it

Find the first failed table in the deploy request and fix that error. Then [retry the failed tables](deploy-requests.md#retry-a-partially-failed-deploy). Retry recopies failed tables in migration order. Tables that already finished stay as they are.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
