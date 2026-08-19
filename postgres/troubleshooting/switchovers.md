---
url: https://planetscale.com/docs/postgres/troubleshooting/switchovers
title: "Switchovers"
description: ""
access_date: 2026-08-19T20:12:19.089Z
current_date: 2026-08-19T20:12:19.089Z
---

## Overview

A switchover moves the primary of a Postgres branch to one of its replicas on demand. The current primary steps down and a replica is promoted in its place, using the same battle-tested mechanism PlanetScale uses for [automatic failovers](../operations-philosophy.md). Writes are briefly interrupted (on the order of seconds) while the switch completes.

Switchovers are useful when you want to:

- Verify that your application tolerates a primary change before you depend on it during an unplanned failover
- Move the primary ahead of planned work, instead of waiting for routine maintenance to switch over the primary for you

A branch without replicas has nothing to promote. Requesting a switchover on a single-node branch **restarts the instance in place** instead, and the branch is unreachable while it comes back. Check the `method` field on the switchover (`switchover` or `restart`) to see which one a branch got.

## Start a switchover

A branch accepts one switchover at a time. You can start one from the dashboard, the CLI, or the API.

### Dashboard

On the branch page, open the cluster actions menu (the kebab icon next to the cluster summary) and select **Switch over to replica**. On a single-node branch the action reads **Restart cluster** instead.

### CLI

```shellscript
pscale branch switchover $DATABASE $BRANCH
```

### API

```shellscript
curl -X POST \
  "https://api.planetscale.com/v1/organizations/$ORG/databases/$DATABASE/branches/$BRANCH/switchovers" \
  -H "Authorization: $API_TOKEN"
```

See the [create switchover API reference](../../api/reference/create_switchover.md) for details.

## Choose the replica to promote

By default, an eligible replica is selected automatically. To pick one yourself, first find replica names with `pscale branch infra $DATABASE $BRANCH` or on the branch page in the dashboard, then pass the exact replica `name` as the candidate:

```shellscript
pscale branch switchover $DATABASE $BRANCH --candidate $REPLICA_NAME
```

```shellscript
curl -X POST \
  "https://api.planetscale.com/v1/organizations/$ORG/databases/$DATABASE/branches/$BRANCH/switchovers" \
  -H "Authorization: $API_TOKEN" \
  -d '{"candidate": "$REPLICA_NAME"}'
```

A candidate can only be specified for a branch with replicas.

## Track the outcome

A switchover moves through `pending`, `running`, and a terminal state of `succeeded` or `failed`. List or fetch switchovers via the API:

```shellscript
curl "https://api.planetscale.com/v1/organizations/$ORG/databases/$DATABASE/branches/$BRANCH/switchovers" \
  -H "Authorization: $API_TOKEN"
```

A switchover that ends in the `failed` state has an **unconfirmed outcome**: the primary may still have moved, and nothing is rolled back. Check which instance is currently primary (with `pscale branch infra` or the dashboard) before retrying.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
