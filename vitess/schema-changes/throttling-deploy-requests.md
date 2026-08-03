---
url: https://planetscale.com/docs/vitess/schema-changes/throttling-deploy-requests
title: "Throttling Deploy Requests"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Overview

Deploy requests, though non-blocking, do consume some resources as the online schema change process occurs. Normally, you don’t need to get involved, as the [Vitess tablet throttler](https://vitess.io/docs/reference/features/tablet-throttler/) automatically identifies when replication lag is high on your database and [slows down migration progress](https://vitess.io/docs/user-guides/schema-changes/audit-and-control/#controlling-throttling).

For long-running schema changes that take several minutes or hours, you may wish to increase throttling on your deploy request to mitigate load on your database.

![Deploy request throttling settings page](https://mintcdn.com/planetscale-2/xsX1e-5IXCYXbX59/vitess/schema-changes/throttling-deploy-requests/deploy-request-throttling.png?w=2500&fit=max&auto=format&n=xsX1e-5IXCYXbX59&q=85&s=4cc8e069cb718c731a235d14f6bf0352)

Deploy request throttling settings page

## How it works

The throttle setting allows you to adjust the underlying Vitess tablet throttler for deploy requests. The throttle setting is a value between 0 and 95, where 0 means throttling is effectively disabled, and 95 is nearly fully throttled, which will drastically slow down migrations. 95 is currently the max throttle setting available.

For example, if you set the throttle value to 25, deploy requests will run at about 75% of their normal speed. The lower the number, the less throttling.

## Managing throttler settings

There are three ways to adjust the throttler for deploy requests:

- At the database level
- At the keyspace level
- At the deploy request level

You must be an [Organization Administrator](../../security/access-control.md#organization-administrator) or [Database Administrator](../../security/access-control.md#database-administrator) for the database that you’re configuring in order to change these settings at the database level.

However, any organization member who has permission to deploy a deploy request is able to adjust the configuration at the deploy request level.

### Managing throttler settings at the database or keyspace level

For the first two options, first select your database, click “Settings” in the left nav, and scroll down to “Advanced settings”. Here, you’ll see the options to configure deploy request throttling.

You can adjust the slider or manually type in the value you’d like to set throttling to.

If you’d like to set throttling at the [keyspace](../sharding/keyspaces.md) level so that each keyspace is throttled differently, check “Set throttling per keyspace”. This will give you the option to individually adjust the sliders for each keyspace.

Click “Save throttling settings”. Going forward, these throttler settings will apply to every deploy request as configured.

### Managing throttler settings per deploy request

You also have the option to manually configure throttler settings on each deploy request.

Once you click “Deploy changes” on a deploy request, click the arrow next to “throttling” to show the settings to adjust the throttle value for that deploy request. From here, you’ll see the same options to adjust the value for each keyspace or for all keyspaces.

Changes to throttler configuration on the deploy request page will only apply to that individual deploy request and will not affect the database level throttler configurations.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
