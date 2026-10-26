---
url: https://planetscale.com/docs/vitess/tutorials/automatic-rails-migrations
title: "Automatic Rails Migrations"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Automatic Rails migrations

This page covers Vitess.

Postgres version: [Postgres](https://planetscale.com/docs/postgres/tutorials/planetscale-postgres-rails.md)

> [!TIP]
>
> If you are using PlanetScale with a Rails application, go to your database's Settings page in the web app and enable
> "Automatically copy migration data." Select "Rails/Phoenix" as the migration framework. When enabled, this setting
> updates the *schema\_migrations* table each time you branch with the latest migration. If disabled, running
> *rake db:migrate* will try to run all migrations every time, instead of only the latest one.

## Introduction

In this tutorial, you're going to learn how Rails migrations work with the PlanetScale branching and deployment workflows.

> [!NOTE]
>
> Migration tracking works with any migration tool, not just Rails. For other frameworks, specify the migration table name on your database's Settings page.

## Prerequisites

Follow the [Connect a Rails app](https://planetscale.com/docs/vitess/tutorials/connect-rails-app.md) tutorial first. By the end, you will have:

* Installed the [PlanetScale CLI](https://github.com/planetscale/cli), Ruby, and the Rails gem
* Created a PlanetScale database named `blog`
* Started a new Rails app named `blog` with a migration creating a `Users` table
* Run the first Rails migration

### A quick introduction to Rails migrations

Rails tracks an application's migrations in an internal table called `schema_migrations`. At a high level, running `rake db:migrate` does the following:

* Rails looks at all of the migration files in your `db/migrate` directory.
* Rails queries the `schema_migrations` table to see which migrations have and haven't been run.
* Any migration that doesn’t appear in the `schema_migrations` table is considered pending and is executed by this task.

> [!TIP]
>
> When you merge a deploy request in PlanetScale, the *schema\_migrations* table in *main* is
> automatically updated with the migration data from your branch.

## Execute a Rails migration on PlanetScale

Rails migrations follow the PlanetScale [non-blocking schema change](https://planetscale.com/docs/vitess/schema-changes.md) workflow. First, the migration is applied to a *development* branch, and then the development branch is merged into the `main` production branch with [safe migrations](https://planetscale.com/docs/vitess/schema-changes/safe-migrations.md) enabled.

Let's add another table to your existing `blog` schema:

**Step 1**

Create an `add-posts-table` development branch from `main` in your database *blog*:

```bash theme={null}
pscale branch create blog add-posts-table
```

When the branch is ready, you can verify that the `schema_migrations` table is up-to-date with `main` by checking for the timestamp of your `Create Users` migration file. Your migration will have a different timestamp than the one shown here.

Check the timestamp in your codebase:

```bash theme={null}
ls db/migrate
20211014210422_create_users.rb
```

Connect to the new branch:

```bash theme={null}
pscale shell blog add-posts-table
```

Query the migration table:

```sql theme={null}
blog/add-posts-table> select * from schema_migrations;
+----------------+
| version        |
+----------------+
| 20211014210422 |
+----------------+
```

**Step 2**

Connect your development environment to the new branch:

One way to do this is to create a new password for the `add-posts-table` branch and update `config/database.yml` with the new username, password, and host. Another is to use `pscale connect` to establish a secure connection on a local port. Since the `add-posts-table` branch won't be needed after the migration, let's use the `pscale connect` proxy.

In a separate terminal, establish the connection:

```bash theme={null}
pscale connect blog add-posts-table --port 3309
```

Then, update `config/database.yml` to connect through the proxy:

```yaml theme={null}
development:
  <<: *default
  adapter: trilogy
  database: blog
  host: 127.0.0.1
  port: 3309
```

**Step 3**

Create the second Rails migration and call it `CreatePosts`:

```bash theme={null}
rails generate migration CreatePosts
```

Find the new migration file in `db/migrate` and add a few details for the new Posts table:

```ruby theme={null}
class CreatePosts < ActiveRecord::Migration[7.0]
  def change
    create_table :posts do |t|
      t.string :title
      t.text :content
      t.bool :published
      t.references :user
      t.timestamps
    end
  end
end
```

**Step 4**

Run the CreatePosts migration:

```bash theme={null}
rake db:migrate
```

This command runs the new migration against your `add-posts-table` *development* branch.

At this point, Rails creates the `posts` table and inserts another `timestamp` into the `schema_migrations` table on your development branch.

You can verify the change in `schema_migrations` yourself:

```sql theme={null}
blog/add-posts-table> select * from schema_migrations;
+----------------+
| version        |
+----------------+
| 20211014210422 |
| 20220224221753 |
+----------------+
```

**Step 5**

Open a deploy request for your `add-posts-table` branch, and deploy your changes to `main`.

You can complete the deploy request either in the web app or with the `pscale deploy-request` command.

```bash theme={null}
pscale deploy-request create blog add-posts-table
```

```bash theme={null}
pscale deploy-request deploy blog 1
```

To create the deploy request, PlanetScale looks at the differences between the schemas of `main` and `add-posts-table` and plans a `create table` statement to add the new table to `main`. When you deploy, PlanetScale runs that ` create table` statement and copies the second row from `schema_migrations` in `add-posts-table` to the `schema_migrations` table in `main`.\`

**Step 6**

Verify the changes in your `main` branch:

In a `pscale` shell for `main` you can verify that the changes from `add-posts-table` were deployed successfully.

```bash theme={null}
pscale shell blog main
```

```sql theme={null}
blog/|⚠ main ⚠|> show tables;
+----------------------+
| Tables_in_blog       |
+----------------------+
| posts                |
| schema_migrations    |
| users                |
+----------------------+

blog/|⚠ main ⚠|> select * from schema_migrations;
+----------------+
| version        |
+----------------+
| 20220223232425 |
| 20220224221753 |
+----------------+
```

## Summary

In this tutorial, we learned how to use the PlanetScale deployment process with the Rails migration workflow.

## What's next?

Learn more about how PlanetScale allows you to make [schema changes](https://planetscale.com/docs/vitess/schema-changes.md) to your production databases without downtime or locking tables.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
