---
url: https://planetscale.com/docs/vitess/tutorials/automatic-rails-migrations
title: "Automatic Rails Migrations"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

If you are using PlanetScale with a Rails application, go to your database’s Settings page in the web app and enable “Automatically copy migration data.” Select “Rails/Phoenix” as the migration framework. When enabled, this setting updates the *schema\_migrations* table each time you branch with the latest migration. If disabled, running *rake db:migrate* will try to run all migrations every time, instead of only the latest one.

## Introduction

In this tutorial, you’re going to learn how Rails migrations work with the PlanetScale branching and deployment workflows.

Migration tracking works with any migration tool, not just Rails. For other frameworks, specify the migration table name on your database’s Settings page.

## Prerequisites

Follow the [Connect a Rails app](connect-rails-app.md) tutorial first. By the end, you will have:

- Installed the [PlanetScale CLI](https://github.com/planetscale/cli), Ruby, and the Rails gem
- Created a PlanetScale database named `blog`
- Started a new Rails app named `blog` with a migration creating a `Users` table
- Run the first Rails migration

### A quick introduction to Rails migrations

Rails tracks an application’s migrations in an internal table called `schema_migrations`. At a high level, running `rake db:migrate` does the following:

- Rails looks at all of the migration files in your `db/migrate` directory.
- Rails queries the `schema_migrations` table to see which migrations have and haven’t been run.
- Any migration that doesn’t appear in the `schema_migrations` table is considered pending and is executed by this task.

When you merge a deploy request in PlanetScale, the *schema\_migrations* table in *main* is automatically updated with the migration data from your branch.

## Execute a Rails migration on PlanetScale

Rails migrations follow the PlanetScale [non-blocking schema change](../schema-changes.md) workflow. First, the migration is applied to a *development* branch, and then the development branch is merged into the `main` production branch with [safe migrations](../schema-changes/safe-migrations.md) enabled.

Let’s add another table to your existing `blog` schema:

## Summary

In this tutorial, we learned how to use the PlanetScale deployment process with the Rails migration workflow.

## What’s next?

Learn more about how PlanetScale allows you to make [schema changes](../schema-changes.md) to your production databases without downtime or locking tables.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
