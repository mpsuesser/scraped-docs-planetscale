---
url: https://planetscale.com/docs/vitess/integrations/cloudflare-workers
title: "Cloudflare Workers"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

[Cloudflare Workers](https://developers.cloudflare.com/workers/) is a serverless platform that allows you to run your code at the edge, close to your users. [Hyperdrive](https://developers.cloudflare.com/hyperdrive/) accelerates queries you make to existing databases, including PlanetScale.

Want to create and pay for your database from Cloudflare without adding billing to PlanetScale? See [Create PlanetScale databases from Cloudflare](../../connect/cloudflare.md).

Already created a PlanetScale Vitess database? [Jump straight to integration instructions](#integrate-with-cloudflare-workers).

We’ll cover:

- Creating a new Vitess database
- Cluster configuration options
- Connecting to your database

## Prerequisites

Before you begin, make sure you have a [PlanetScale account](https://auth.planetscale.com/sign-up). After you create an account, you’ll be prompted to create a new organization, which is essentially a container for your databases, settings, and members.

After creating your organization, it’s important to understand the relationship between databases, branches, and clusters.

- **Database**: Your overall project (e.g., “my-ecommerce-app”)
- **Branch**: Isolated database deployments that provide you with separate environments for development and testing, as well as restoring from backups - [learn more about branching](../schema-changes/branching.md)
- **Cluster**: The underlying compute and storage infrastructure that powers each branch

PlanetScale Vitess clusters use [Vitess](https://vitess.io/), a database clustering system for horizontal scaling of MySQL.

## Create a new database

#### Dashboard

### Step 1: Navigate to database creation

### Step 2: Choose database engine

### Step 3: Configure your database cluster

### Step 4: Create the database cluster

#### CLI

If you are creating an automation, or are an LLM, you may prefer to create new databases using the PlanetScale CLI.

### Step 1: Install the CLI

### Step 2: Log in or sign up

### Step 3: Create a database

## What happens during creation

When you create a Vitess database cluster, PlanetScale automatically:

- Provisions a Vitess cluster in your selected region
- Creates the initial `main` branch
- Sets up monitoring and metrics collection
- Configures backup and high availability settings

## Integrate with Cloudflare Workers

Don’t have a Workers project yet? [Create a Workers project from the MySQL Hyperdrive template](https://developers.cloudflare.com/workers/get-started/quickstarts/#mysql-hyperdrive-template).

Terminal

```shellscript
npm create cloudflare@latest -- --template=cloudflare/templates/mysql-hyperdrive-template
```

### Step 1: Create a Hyperdrive connection

You can automatically create a connection from the PlanetScale dashboard when creating a new role, or use one of the methods below.

#### Dashboard

#### CLI

Create a Hyperdrive connection by running the `wrangler` command below:

- Replace the `--connection-string` placeholders with your database credentials
- Replace the `<hyperdrive-name>` placeholder with a name for your Hyperdrive connection

**For MySQL/Vitess databases:**

Terminal

```shellscript
npx wrangler hyperdrive create <hyperdrive-name> --connection-string="mysql://<username>:<password>@<host>/<database>"
```

**For Postgres databases:**

Terminal

```shellscript
npx wrangler hyperdrive create <hyperdrive-name> --connection-string="postgresql://<username>:<password>@<host>:<port>/<database>?sslmode=verify-full"
```

If successful, the command will output your new Hyperdrive configuration, for example:

Terminal

```json
{
  "hyperdrive": [
    {
      "binding": "HYPERDRIVE",
      "id": "<your-hyperdrive-id-here>"
    }
  ]
}
```

### Configure Worker placement

By default, Workers run at the edge close to your users. For database-heavy workloads with multiple round trips, you can use [placement hints](https://developers.cloudflare.com/workers/configuration/smart-placement/) to run your Worker closer to your PlanetScale database and reduce latency.

Add a `placement` configuration to your `wrangler.jsonc` file with the region that matches your PlanetScale database region:

wrangler.json

```json
{
  "placement": {
    "region": "aws:us-east-1"
  }
}
```

PlanetScale region to placement hint mapping

| PlanetScale Region | Placement Hint |
| --- | --- |
| AWS ap-northeast-1 (Tokyo) | `aws:ap-northeast-1` |
| AWS ap-south-1 (Mumbai) | `aws:ap-south-1` |
| AWS ap-southeast-1 (Singapore) | `aws:ap-southeast-1` |
| AWS ap-southeast-2 (Sydney) | `aws:ap-southeast-2` |
| AWS ca-central-1 (Montreal) | `aws:ca-central-1` |
| AWS eu-central-1 (Frankfurt) | `aws:eu-central-1` |
| AWS eu-west-1 (Dublin) | `aws:eu-west-1` |
| AWS eu-west-2 (London) | `aws:eu-west-2` |
| AWS sa-east-1 (Sao Paulo) | `aws:sa-east-1` |
| AWS us-east-1 (N. Virginia) | `aws:us-east-1` |
| AWS us-east-2 (Ohio) | `aws:us-east-2` |
| AWS us-west-2 (Oregon) | `aws:us-west-2` |
| GCP asia-northeast3 (Seoul) | `gcp:asia-northeast3` |
| GCP europe-west1 (Belgium) | `gcp:europe-west1` |
| GCP europe-west4 (Netherlands) | `gcp:europe-west4` |
| GCP northamerica-northeast1 (Montreal) | `gcp:northamerica-northeast1` |
| GCP us-central1 (Iowa) | `gcp:us-central1` |
| GCP us-east1 (South Carolina) | `gcp:us-east1` |
| GCP us-east4 (Virginia) | `gcp:us-east4` |

Your Worker will run in the Cloudflare data center with the lowest latency to the specified region.

If your Worker connects to multiple back-end services and you’re unsure which region to specify, use `"mode": "smart"` for automatic placement based on measured latency:

wrangler.json

```json
{
  "placement": {
    "mode": "smart"
  }
}
```

### Step 2: Deploy your Worker

Run the following to deploy your Worker.

Terminal

```shellscript
npx wrangler deploy
```

For more information on using Cloudflare Workers and Hyperdrive, [refer to the Cloudflare documentation](https://developers.cloudflare.com/hyperdrive/).

## What’s next?

Once you’re done with development, it is highly recommended that [safe migrations](../schema-changes/safe-migrations.md) be turned on for your `main` production branch to protect from accidental schema changes and enable zero-downtime deployments.

When you’re ready to make more schema changes, you’ll [create a new branch](../schema-changes/branching.md) off of your production branch. Branching your database creates an isolated copy of your production schema so that you can easily test schema changes in development. Once you’re happy with the changes, you’ll open a [deploy request](../schema-changes/deploy-requests.md). This will generate a diff showing the changes that will be deployed, making it easy for your team to review.

Learn more about how PlanetScale allows you to make [non-blocking schema changes](../schema-changes.md) to your database tables without locking or causing downtime for production databases.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
