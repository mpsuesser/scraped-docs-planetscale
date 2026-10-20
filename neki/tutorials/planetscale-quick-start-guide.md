---
url: https://planetscale.com/docs/neki/tutorials/planetscale-quick-start-guide
title: "Planetscale Quick Start Guide"
description: ""
access_date: 2026-10-20T18:01:41.586Z
current_date: 2026-10-20T18:01:41.586Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Neki quickstart

> Create a single-shard Neki database, connect, and insert data

## Getting started

### Prerequisites

1. You will need [a PlanetScale account](https://auth.planetscale.com/sign-up).
2. An administrator of your PlanetScale organization must [join the Neki Platform Preview](../../neki.md#availability-and-access).

### Create a Neki database

**Dashboard**

**Step 1**

Click "**New database**" > "**Create new database**" on your organization's
overview page.

**Step 2**

Name your database.

**Step 3**

Select a region. For the lowest latency, select a region near you or your
application's hosting location.

**Step 4**

Select **Neki** as the database engine.

![Create database form with the Neki engine selected](https://mintcdn.com/planetscale-2/6i1lJ6cNfB-VSsaH/neki/tutorials/select-neki-engine.png?fit=max\&auto=format\&n=6i1lJ6cNfB-VSsaH\&q=85\&s=41490be17a8a313a2bb81d9075e4ff7e)

**Step 5**

Select the desired cluster configuration, storage option, and cluster size
for your database.

**Step 6**

If prompted, add a valid credit or debit card.

**Step 7**

Click "**Create database**" to deploy your database.

**CLI**

Install and authenticate the [PlanetScale CLI](../../cli/planetscale-environment-setup.md).

**Step 1**

List organizations, then switch to the one enrolled in the Platform
Preview:

```bash theme={null}
pscale org list
pscale org switch <ORGANIZATION_NAME>
```

**Step 2**

List regions and pick a slug close to your application:

```bash theme={null}
pscale region list
```

**Step 3**

Create an HA Neki database. `--replicas 2` requests two replicas in
addition to the primary, for three Postgres nodes on the initial shard.
`PS_10` is a valid starting cluster size.

```bash theme={null}
pscale database create <DATABASE_NAME> \
  --engine neki \
  --region <REGION_SLUG> \
  --cluster-size PS_10 \
  --replicas 2 \
  --wait
```

Provisioning can take a few minutes. `--wait` keeps the command open
until the database is ready.

> [!NOTE]
>
> Database names can contain up to 63 lowercase letters, numbers,
> dashes, or underscores. A name cannot start or end with a dash or
> underscore.

### Add a schema to your database

Use the dashboard's web console to create some example tables.
To use another SQL client, first see [Connect to your
database](#connect-to-your-database).

**Step 1**

[Enable web console access for production
branches](../web-console.md#enable-for-production-branches).

**Step 2**

Select **Console** and connect to the `main` branch. The console connects to
the **Primary** server and the `postgres` database by default. See [Web
console](../web-console.md) for connection controls and supported commands.

![Web console connected to the main branch](https://mintcdn.com/planetscale-2/gWONWhlM_S1jInv7/neki/tutorials/web-console-main.png?fit=max\&auto=format\&n=gWONWhlM_S1jInv7\&q=85\&s=9c900b11594e861e011a75c953a4c041)

**Step 3**

Create some example tables by running the following
commands in the web console:

```sql theme={null}
CREATE TABLE categories (
  id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name varchar(255) NOT NULL
);
```

```sql theme={null}
CREATE TABLE products (
  id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name varchar(255) NOT NULL,
  image_url varchar(255),
  category_id bigint
);
```

```sql theme={null}
CREATE INDEX category_id_idx ON products (category_id);
```

**Step 4**

You can confirm that the tables have been added by running:

```sql theme={null}
SELECT table_name
FROM information_schema.tables
WHERE table_schema = 'public'
  AND table_name IN ('categories', 'products')
ORDER BY table_name;
```

![Web console showing the categories and products tables](https://mintcdn.com/planetscale-2/gWONWhlM_S1jInv7/neki/tutorials/web-console-tables.png?fit=max\&auto=format\&n=gWONWhlM_S1jInv7\&q=85\&s=46ae8b2fd775222c13f6798b5cbb0990)

### Insert data into your database

Run the following SQL to add some rows to the tables:

```sql theme={null}
INSERT INTO categories (name)
VALUES ('Office supplies');
```

```sql theme={null}
INSERT INTO products (name, image_url, category_id)
VALUES ('Ballpoint pen', 'https://example.com/500x500', 1);
```

You can confirm the data has been added with:

```sql theme={null}
SELECT * FROM products;
```

```sql theme={null}
SELECT * FROM categories;
```

![Web console showing SELECT results for the products and categories tables](https://mintcdn.com/planetscale-2/gWONWhlM_S1jInv7/neki/tutorials/web-console-results.png?fit=max\&auto=format\&n=gWONWhlM_S1jInv7\&q=85\&s=76fdf615a7caa99aef5e67f185381814)

You can view the schema of your database by navigating to the "**Branches**"
tab and selecting the branch you want to view. You may need to click
"**Refresh schema**". For now, select `main`, and it will display the names of
the two tables you just created. Click on the name of each table to see further
schema details.

![Branches page with the products table expanded to show its schema](https://mintcdn.com/planetscale-2/6i1lJ6cNfB-VSsaH/neki/tutorials/branch-schema.png?fit=max\&auto=format\&n=6i1lJ6cNfB-VSsaH\&q=85\&s=03b874caa3898600faef8101cae0835c)

### Connect to your database

Connect from outside the PlanetScale dashboard by creating a new role in the **Settings** -> **Roles** page, or open `psql` through the PlanetScale CLI.
On the dashboard page, you can also click **Connect** to create a new role.

**Dashboard**

**Step 1**

With the default branch selected, click **Connect** from your database dashboard.

**Step 2**

Click **Create new role**. A role determines which operations the connection can
perform. See [Roles and credentials](../connecting/roles.md) for more details.

**Step 3**

Copy the generated `psql` command and enter it into your terminal.

![Connect page showing generated role credentials and the psql command](https://mintcdn.com/planetscale-2/6i1lJ6cNfB-VSsaH/neki/tutorials/connect-credentials.png?fit=max\&auto=format\&n=6i1lJ6cNfB-VSsaH\&q=85\&s=e984936fa0ec61f9763422ea93367fde)

**CLI**

Open a `psql` session through the PlanetScale CLI. `pscale shell` creates
temporary credentials, so you do not need a saved role for this step.

```bash theme={null}
pscale shell <DATABASE_NAME> main
```

For a persistent role that your application can reuse, create one from the
dashboard **Connect** tab or with `pscale role`. See [Roles and
credentials](../connecting/roles.md).

When the `postgres` prompt appears, query the data you added:

```sql theme={null}
SELECT c.name AS category, p.name AS product
FROM categories AS c
JOIN products AS p ON p.category_id = c.id;
```

### What's next?

Open the **Connect** page and select a framework or language to configure your
application.

See [Connect to Neki](../connecting.md) for connection parameters, replica
routing, and router groups.

A new Neki database starts on one shard.
Continue with the [Sharding quickstart](sharding-quick-start-guide.md) to see how sharding works in Neki.

When you want to continue developing your database, create a [development
branch](../branching.md) from `main` so you can work independently of the
production branch. See [Development environments](../development-environments.md)
to initialize an empty branch for schema work and tests.

### Clean up

Delete your database from **Settings** > **Delete database**, or use the CLI:

```bash theme={null}
pscale database delete <DATABASE_NAME>
```

Deleting the database permanently removes all of its branches and data.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
