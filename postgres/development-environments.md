---
url: https://planetscale.com/docs/postgres/development-environments
title: "Development Environments"
description: ""
access_date: 2026-10-22T18:21:49.469Z
current_date: 2026-10-22T18:21:49.469Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Development Environments

> When using development environments, it's important to have a way to work with your database and test schema changes in isolation from production. We have several recommendations for how to accomplish this when using PlanetScale Postgres.

> [!NOTE]
>
> Schema and code changes must be deployed in the correct sequence. When adding a new column to a table along with a new application feature, you likely want to execute the schema change before deploying the corresponding code changes. In other cases, such as removing a feature and with it a table in your database, you would make your code changes first and then drop the table second.
>
> We recommend reviewing [this blog post](https://planetscale.com/blog/safely-making-database-schema-changes) for more information about deploying database schema changes alongside application changes.

## Use local Postgres

Since PlanetScale uses standard PostgreSQL, one option is to use locally-running Postgres servers for all of your development work.

In this case, you would create a new local database for each branch a developer is working on. After creating the database, run your database migration scripts to populate it with your project's schema. Optionally, you could have a script to populate your tables with dummy data to improve local testing. When you are ready to commit your code changes, execute the necessary schema changes on your production branch, being [careful about ordering](https://planetscale.com/blog/safely-making-database-schema-changes).

While this is a convenient option in some cases, local databases can make it difficult for multiple developers to collaborate on shared branches, as schema changes must be manually synchronized across each developer's local environment.

## Use PlanetScale branches

PlanetScale Postgres supports [branching](https://planetscale.com/docs/postgres/branching.md). This allows you to spin up lightweight Postgres databases separate from production that can be used for development and testing. Creating a new branch produces a completely empty Postgres database. No schema or data is copied to the branch, unless restoring from a backup (more on this later).

Branches can be used similarly to local Postgres instances. The advantage is, since they are hosted by PlanetScale, they can be easily shared amongst development teams.

If using branches for your development environments, you would [create new database branch](https://planetscale.com/docs/postgres/branching.md#create-a-branch) for each code branch a developer/team is working on. You would then run your schema migrations and any data loading scripts you need to initialize the branch. When you are ready to commit your code changes, execute the necessary schema changes on your production branch, and then [delete the branch](https://planetscale.com/docs/postgres/branching.md#delete-a-branch).

## Use PlanetScale branches + backups

PlanetScale gives you the ability to create new branches that are [initialized from a recent backup](https://planetscale.com/docs/postgres/branching.md#from-a-backup). This can be used instead of empty branches to create development environments that are pre-populated with schema and data.

There are some downsides to this approach, however. This approach copies production data to development branches, which may pose security risks if your database contains sensitive customer information.

Another consideration is cost. Each branch is its own, isolated database and uses its own storage separate from production. You will be charged for the storage consumed by both your production branches and your development branches.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
