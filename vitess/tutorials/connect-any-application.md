---
url: https://planetscale.com/docs/vitess/tutorials/connect-any-application
title: "Connect Any Application"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

## Introduction

In this tutorial, you’ll learn how to connect any application to your PlanetScale database.

If you’re just getting started and still need to set up a database, we recommend starting with the [PlanetScale quick start guide](planetscale-quick-start-guide.md) first. We also have language/framework-specific guides under “ **Integration guides** ” if you prefer a more detailed walk-through.

PlanetScale uses [database branches](../schema-changes/branching.md) to create a development-friendly workflow. Your database is initially created with a default branch, `main`, which is meant to serve as a production database branch. Production branches are highly available databases intended for production traffic. They are automatically provided with an additional replica to resist outages, enabling zero-downtime failovers.

You can connect to a *production* or *development* database branch. We recommend creating and connecting to a *development* branch while in *development*, as it allows you to make schema changes without affecting production. Your production application, however, should be connected to your production database branch. Check out our [Branching guide](../schema-changes/branching.md) for more information about the branching workflow.

By default, production branches have safe migrations turned off. This means that any schema changes you make will be applied immediately. Once you are ready to go to production, we recommend turning on safe migrations if you want to make non-blocking schema changes. Check out our [Safe migrations documentation](../schema-changes/safe-migrations.md) for more information.

There are two ways to connect your app to PlanetScale. Both are covered below.

## Option 1: Connect with username and password (Recommended)

This section will show you how to create a username and password for your branch and use those credentials to connect to your database. This is the recommended way to connect.

There are two ways to generate a new username and password for your branch:

- In the PlanetScale dashboard
- With the PlanetScale CLI

### Generate credentials in the PlanetScale dashboard

### Generate credentials in the PlanetScale CLI

If you prefer working from the CLI, you can quickly spin up new credentials there. Make sure you have the [CLI set up](../../cli/planetscale-environment-setup.md) first.

## Option 2: Connect using the PlanetScale proxy

Another way to connect your application to your PlanetScale database *during development* is using the PlanetScale proxy. You won’t have to fiddle with configuring any credential details, as that’s handled by PlanetScale. It’s as simple as a single CLI command.

You’ll use the CLI to establish a secure connection to PlanetScale. It will listen on a local port that your application can connect to. The main benefit of this method is you won’t have to generate and remember multiple passwords every time you’re creating or switching to a new branch.

Your application should now be connected to the specified PlanetScale database branch!

## What’s next?

Once your application is connected to a development database branch, you can make schema changes in an isolated development environment without worrying about affecting production. Additionally, we recommend that [safe migrations](../schema-changes/safe-migrations.md) be enabled on your production database branch, which allows you to make [non-blocking schema changes](../schema-changes.md) without locking or causing downtime.

### PlanetScale workflow

Here’s the general workflow that you’ll go through to get schema changes from development to production:

Note: you must already have [a production branch](../schema-changes/branching.md#promote-a-branch-to-production) in place to create a deploy request.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
