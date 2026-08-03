---
url: https://planetscale.com/docs/connect/stripe-projects
title: "Stripe Projects"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

Resources are provisioned in your PlanetScale account with normal access and dashboards. Your billing page in the PlanetScale dashboard will indicate that you are connected to the Stripe app and payments are made through Stripe.

[Stripe Projects](https://docs.stripe.com/projects) is currently in **developer preview**. You can request access to try it out [here](https://projects.dev/).

## Before you begin

Before you can use PlanetScale with the Stripe CLI, you’ll need:

- [Access](https://projects.dev/) to the developer preview
- A Stripe account
- A PlanetScale account (or Stripe Projects can create one for you)
- The [Stripe CLI](https://docs.stripe.com/stripe-cli) installed and up to date
- The Projects plugin installed:

```shellscript
stripe plugin install projects
```

## Quickstart

Initialize Stripe Projects in your app directory, add a PlanetScale database, and sync credentials to your local environment:

```shellscript
# Enter into your app directory
cd my-app

# Initialize a new Stripe project
stripe projects init

# Add a PlanetScale Postgres database
stripe projects add planetscale/postgresql

# Or add a PlanetScale MySQL database
stripe projects add planetscale/mysql

# Sync credentials to your local .env file
stripe projects env --sync
```

After syncing, your `.env` file will be updated with your PlanetScale database connection string.

## Add a database

When you run `stripe projects add`, you can sign in to your existing PlanetScale account or let Stripe Projects create a new one. You’ll be prompted to configure:

- Database name
- Cluster size
- Region
- Number of replicas

Once confirmed, your database is provisioned and credentials are returned in an agent-readable format.

## Manage credentials

List your project’s environment variables (values are not shown):

```shellscript
stripe projects env list
```

Sync credentials to your local `.env` file:

```shellscript
stripe projects env --sync
```

Rotate credentials for your PlanetScale database:

```shellscript
stripe projects rotate planetscale/postgresql
stripe projects rotate planetscale/mysql
```

## Check project status

Review your project’s linked providers, provisioned services, and health:

```shellscript
stripe projects status
```

## Open your PlanetScale dashboard

```shellscript
stripe projects open planetscale
```

## Remove a database

This is a destructive operation. Running this command will **delete your PlanetScale database**.

```shellscript
stripe projects remove planetscale/postgresql
stripe projects remove planetscale/mysql
```

## Non-interactive and agent use

All commands support flags for use in CI/CD pipelines, scripts, and AI agents:

| Flag | Description |
| --- | --- |
| `--json` | Return output as structured JSON |
| `--no-interactive` | Disable prompts; commands fail if input is missing |
| `--auto-confirm` | Accept confirmation prompts automatically |
| `--quiet` | Suppress non-essential output |

Example — add a database with no prompts:

```shellscript
stripe projects add planetscale/postgresql \
  --no-interactive \
  --json
```

## Further reading

For full documentation on Stripe Projects, including billing, LLM context generation, the complete command reference, and regional availability, see the [Stripe Projects documentation](https://docs.stripe.com/stripe-projects). You can also [view PlanetScale in the Stripe Marketplace](https://marketplace.stripe.com/apps/planetscale) to get started.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
