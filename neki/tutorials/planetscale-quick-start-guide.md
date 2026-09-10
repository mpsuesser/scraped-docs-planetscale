---
url: https://planetscale.com/docs/neki/tutorials/planetscale-quick-start-guide
title: "Planetscale Quick Start Guide"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

## Getting started

### Prerequisites

1. You will need [a PlanetScale account](https://auth.planetscale.com/sign-up).
2. An administrator of your PlanetScale organization must [join the Neki Platform Preview](../../neki.md#availability-and-access).

### Create a Neki database

#### Dashboard

#### CLI

Install and authenticate the [PlanetScale CLI](../../cli/planetscale-environment-setup.md).

### Add a schema to your database

Use the dashboard’s web console to create some example tables. To use another SQL client, first see [Connect to your database](#connect-to-your-database).

### Insert data into your database

Run the following SQL to add some rows to the tables:

```sql
INSERT INTO categories (name)
VALUES ('Office supplies');
```

```sql
INSERT INTO products (name, image_url, category_id)
VALUES ('Ballpoint pen', 'https://example.com/500x500', 1);
```

You can confirm the data has been added with:

```sql
SELECT * FROM products;
```

```sql
SELECT * FROM categories;
```

![Web console showing SELECT results for the products and categories tables](https://mintcdn.com/planetscale-2/gWONWhlM_S1jInv7/neki/tutorials/web-console-results.png?w=2500&fit=max&auto=format&n=gWONWhlM_S1jInv7&q=85&s=0b3787db26925fb8204ebc3f7ec9affc)

Web console showing SELECT results for the products and categories tables

![Web console showing SELECT results for the products and categories tables](https://mintcdn.com/planetscale-2/gWONWhlM_S1jInv7/neki/tutorials/web-console-results-darkmode.png?w=2500&fit=max&auto=format&n=gWONWhlM_S1jInv7&q=85&s=a9e6de1b8af71534a42e7bd994c91fe7)

Web console showing SELECT results for the products and categories tables

You can view the schema of your database by navigating to the “ **Branches** ” tab and selecting the branch you want to view. You may need to click “ **Refresh schema** ”. For now, select `main`, and it will display the names of the two tables you just created. Click on the name of each table to see further schema details.

![Branches page with the products table expanded to show its schema](https://mintcdn.com/planetscale-2/6i1lJ6cNfB-VSsaH/neki/tutorials/branch-schema.png?w=2500&fit=max&auto=format&n=6i1lJ6cNfB-VSsaH&q=85&s=7ff2fe9670cb7248b7b83b609eb69525)

Branches page with the products table expanded to show its schema

![Branches page with the products table expanded to show its schema](https://mintcdn.com/planetscale-2/6i1lJ6cNfB-VSsaH/neki/tutorials/branch-schema-darkmode.png?w=2500&fit=max&auto=format&n=6i1lJ6cNfB-VSsaH&q=85&s=85712ea53bcbe0f4d3ec1754614f2449)

Branches page with the products table expanded to show its schema

### Connect to your database

Connect from outside the PlanetScale dashboard by creating a new role in the **Settings** -> **Roles** page, or open `psql` through the PlanetScale CLI. On the dashboard page, you can also click **Connect** to create a new role.

#### Dashboard

#### CLI

Open a `psql` session through the PlanetScale CLI. `pscale shell` creates temporary credentials, so you do not need a saved role for this step.

```shellscript
pscale shell <DATABASE_NAME> main
```

For a persistent role that your application can reuse, create one from the dashboard **Connect** tab or with `pscale role`. See [Roles and credentials](../connecting/roles.md).

When the `postgres` prompt appears, query the data you added:

```sql
SELECT c.name AS category, p.name AS product
FROM categories AS c
JOIN products AS p ON p.category_id = c.id;
```

### What’s next?

Open the **Connect** page and select a framework or language to configure your application.

See [Connect to Neki](../connecting.md) for connection parameters, replica routing, and router groups.

A new Neki database starts on one shard. Continue with the [Sharding quickstart](sharding-quick-start-guide.md) to see how sharding works in Neki.

When you want to continue developing your database, create a [development branch](../branching.md) from `main` so you can work independently of the production branch. See [Development environments](../development-environments.md) to initialize an empty branch for schema work and tests.

### Clean up

Delete your database from **Settings** > **Delete database**, or use the CLI:

```shellscript
pscale database delete <DATABASE_NAME>
```

Deleting the database permanently removes all of its branches and data.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
