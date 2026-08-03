---
url: https://planetscale.com/docs/vitess/tutorials/connect-nextjs-app
title: "Connect Nextjs App"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Connect a Next.js application to PlanetScale

export const PlatformAvailability = ({current, vitess, postgres}) => {
  const docsHref = path => {
    if (!path) return path;
    const normalized = path.startsWith('/') ? path : `/${path}`;
    return normalized;
  };
  const labels = {
    vitess: 'Vitess',
    postgres: 'Postgres'
  };
  if (current === 'both') {
    return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
        <span data-engine="both" data-state="current" aria-current="true" className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
          Vitess and Postgres
        </span>
      </div>;
  }
  const hasVitess = current === 'vitess' || Boolean(vitess);
  const hasPostgres = current === 'postgres' || Boolean(postgres);
  const only = !(hasVitess && hasPostgres);
  const engines = [];
  if (current === 'vitess' || current === 'postgres') engines.push(current);
  if (hasVitess && current !== 'vitess') engines.push('vitess');
  if (hasPostgres && current !== 'postgres') engines.push('postgres');
  return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
      {engines.map(engine => {
    const isCurrent = current === engine;
    const href = docsHref(engine === 'vitess' ? vitess : postgres);
    const label = only ? `${labels[engine]} only` : labels[engine];
    const state = isCurrent || !href ? 'current' : 'link';
    if (isCurrent || !href) {
      return <span key={engine} data-engine={engine} data-state={state} aria-current={isCurrent ? 'true' : undefined} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
              {label}
            </span>;
    }
    return <a key={engine} href={href} data-engine={engine} data-state={state} title={`View ${labels[engine]} documentation`} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
            {label}
            <svg aria-hidden="true" width="12" height="12" viewBox="0 0 12 12" fill="none" className="shrink-0">
              <path d="M2.5 6h7M6.5 3l3 3-3 3" stroke="currentColor" strokeWidth="1.5" strokeLinecap="round" strokeLinejoin="round" />
            </svg>
          </a>;
  })}
    </div>;
};

<PlatformAvailability current="vitess" />

In this tutorial, you'll create a [Next.js](https://nextjs.org/) application that uses [Tailwind CSS](https://tailwindcss.com/) for styling and [Prisma](https://www.prisma.io/) to connect to a [PlanetScale](https://planetscale.com/docs/) database.

## Prerequisites

* [Node.js](https://nodejs.org/en/download/)
* A [PlanetScale account](https://auth.planetscale.com/sign-up)

## Set up the database

If this is your first time in the dashboard, you'll be prompted to go through a database creation walkthrough where you'll create a new database. Otherwise, click "**New database**" > "**Create new database**".

* **Name** — You can use any name with lowercase, alphanumeric characters, or underscores. We also permit dashes, but don't recommend them, as they may need to be escaped in some instances.
* **Plan type** — Select the [desired plan](../../planetscale-plans.md) for your database.
* **Region** — Choose the [region](https://planetscale.com/docs/vitess/regions#available-regions) closest to you or your application. It's important to note if you intend to make this branch a production branch, you will not be able to change the region later, so choose the region with this in mind.

Finally, click "**Create database**".

<Note>
  If you have an existing cloud-hosted database, you can also choose the "Import" option to import your database to PlanetScale using our Import tool. If you go this route, we recommend using our [Database Imports documentation](../imports/database-imports.md).
</Note>

A [production branch](../schema-changes/branching.md), `main`, is automatically created when you create your database. Production branches are highly available, protected database that you can connect your production application to. Once you are satisfied with your initial development, you may enable [safe migrations](../schema-changes/safe-migrations.md) to enable zero-downtime migrations and protect the branch from accidental data deletion.

## Set up the starter Next.js app

Now that you have your database, clone the [Next.js starter repository](https://github.com/planetscale/nextjs-starter), or grab your own project.

```sh theme={null}
git clone https://github.com/planetscale/nextjs-starter
```

Install the dependencies with:

```sh theme={null}
cd nextjs-starter
npm install
```

Create your `.env` file by renaming the `.env.example` file to `.env`:

```sh theme={null}
mv .env.example .env
```

## Generate a connection string

Next, you need to generate a database username and password so that you can use it to connect to your application.

In your PlanetScale dashboard, select your database, click "**Connect**", and select "**Prisma**" from the "**Connect with**" dropdown.

As long as you're an organization administrator, this will generate a username and password that has administrator privileges to the database.

Copy the `DATABASE_URL` string from the `.env` tab and paste it into your own `.env` file. The structure will look like this:

```sh theme={null}
DATABASE_URL='mysql://<USERNAME>:<PASSWORD>@<HOST>/<DATABASE_NAME>?sslaccept=strict'
```

For `DATABASE_NAME`, you can use your PlanetScale database name directly if you have a *single unsharded keyspace*. If you have a sharded keyspace, you'll need to use `@primary`. This will automatically direct incoming queries to the correct keyspace/shard. For more information, see the [Targeting the correct keyspace documentation](../sharding/targeting-correct-keyspace.md).

Your PlanetScale database should now be connected to your application.

## Add the schema and data

With the database connected, you're now ready to add a schema, and read/write data.

The sample repo we shared above includes a pre-built schema and some seed data built with Prisma. Push the database schema to your PlanetScale database with:

```sh theme={null}
npx prisma db push
```

Run the seed script. This will populate your database with `Product` and `Category` data:

```sh theme={null}
npm run seed
```

## Run the app

Run the app with following command:

```sh theme={null}
npm run dev
```

Open your browser at [localhost:3000](http://localhost:3000) to see the running application.

## Deploy your app

After you have your application running locally, you may want to deploy it to production. Your database branch (`main` by default) is already a production database branch. You should also enable [safe migrations](../schema-changes/safe-migrations.md), which protects your production branch from accidental schema changes. This can be done from the PlanetScale dashboard by clicking the same **"cog"** once the branch has been promoted to production.

In the modal that will appear, toggle the option labeled **"Enable safe migrations"**, then click the **"Enable safe migrations"**. This will close the modal and save the setting.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/connect-nextjs-app/prod-branch-options-modal.png?fit=max&auto=format&n=AJPY38bILe2zenXX&q=85&s=68804dcbb922b0c9653d0e19de7651c7" alt="Enable safe migrations" width="1368" height="1134" data-path="images/assets/docs/tutorials/connect-nextjs-app/prod-branch-options-modal.png" />
</Frame>

### Deploy to Vercel

If you'd like to deploy to Vercel, check out our [Deploy to Vercel documentation](deploy-to-vercel.md).

### Deploy to Netlify

If you'd like to deploy to Netlify, check out our [Deploy to Netlify documentation](deploy-to-netlify.md).

<Note>
  If you are deploying the `nextjs-starter` repo, the `Netlify.toml` file in this repository includes the configuration for you to customize the `PLANETSCALE_PRISMA_DATABASE_URL` property on the initial deploy.
</Note>

## What's next?

To learn more about PlanetScale, take a look at the following resources:

* [PlanetScale workflow](../best-practices.md) — Quick overview of the PlanetScale workflow: branching, non-blocking schema changes, deploy requests, and reverting a schema change.
* [PlanetScale branching](../schema-changes/branching.md) — Learn how to utilize branching to ship schema changes with no locking or downtime.
* [PlanetScale CLI](../../cli.md) — Power up your workflow with the PlanetScale CLI. Every single action you just performed in this quickstart (and much more) can also be done with the CLI.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
