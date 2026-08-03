---
url: https://planetscale.com/docs/vitess/tutorials/connect-nodejs-app
title: "Connect Nodejs App"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Connect a Node.js application to PlanetScale

> In this tutorial, you'll create a simple Node.js and Express.js application and connect it to a PlanetScale database.

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

<PlatformAvailability current="vitess" postgres="/postgres/tutorials/planetscale-postgres-node" />

<Tip>
  Already have a Node.js application and just want to connect to PlanetScale? Check out the [Node.js quick connect repo](https://github.com/planetscale/connection-examples/tree/main/nodejs).
</Tip>

## Prerequisites

<Card title="Node.js" icon="node" horizontal href="https://nodejs.org/en/download/" />

## Set up the database

First, create a new database with the following command:

```bash theme={null}
pscale database create <DATABASE_NAME>
```

Next, let's add some data to the database. You'll create a new table called `users` and add one record to it.

To do this, use the PlanetScale CLI shell to open a MySQL shell where you can manipulate your database. You may need to [install the MySQL command line client](../../cli/planetscale-environment-setup.md) if you haven't already.

```bash theme={null}
pscale shell <DATABASE_NAME> <BRANCH_NAME>
```

<Note>
  A branch, `main`, was automatically created when you created your database, so you can use that for `BRANCH_NAME`.
</Note>

Create the `users` table:

```sql theme={null}
CREATE TABLE `users` (
  `id` int NOT NULL AUTO_INCREMENT PRIMARY KEY,
  `email` varchar(255) NOT NULL,
  `first_name` varchar(255),
  `last_name` varchar(255)
);
```

Then, add a record to it with:

```sql theme={null}
INSERT INTO `users` (email, first_name, last_name)
VALUES  ('jp@example.com', 'Jean', 'Pixy');
```

You can verify it was added with:

```sql theme={null}
select * from users;
```

```
+----+----------------+------------+-----------+
| id | email          | first_name | last_name |
+----+----------------+------------+-----------+
|  1 | jp@example.com | Jean       | Pixy      |
+----+----------------+------------+-----------+
```

Next, you'll set up the Express starter application.

## Set up the starter Node.js app

Clone the starter repository:

```bash theme={null}
git clone https://github.com/planetscale/express-example.git
```

Enter into the folder and install the dependencies with:

```bash theme={null}
cd express-example
npm install
```

Now that your application is set up and the database is ready to be used, let's connect them.

## Connect to PlanetScale with Express.js

There are **two ways to connect** to PlanetScale:

* With an auto-generated username and password
* Using the PlanetScale proxy with the CLI

Both options are covered below.

### Option 1: Connect with username and password (Recommended)

These instructions show you how to generate a set of credentials with [the PlanetScale CLI](../../cli/planetscale-environment-setup.md).

You can also get these exact values to copy/paste from your [PlanetScale dashboard](https://app.planetscale.com). In the dashboard, click on the database > "**Connect**" > "**Connect with**" language dropdown > "**Node.js**". If the password is blurred, click "**New password**". Skip to step 3 once you have these credentials.

1. Authenticate the CLI with the following command:

   ```bash theme={null}
   pscale auth login
   ```

2. Using the PlanetScale CLI, create a new username and password for the branch of your database:

   ```bash theme={null}
   pscale password create <DATABASE_NAME> <BRANCH_NAME> <PASSWORD_NAME>
   ```

   <Note>
     The `PASSWORD_NAME` value represents the name of the username and password being generated. You can have multiple credentials for a branch, so this gives you a way to categorize them. To manage your passwords in the dashboard, go to your database dashboard page, click "Settings", and then click "Passwords".
   </Note>

   Take note of the values returned to you, as you won't be able to see this password again.

   ```
   Password production-password was successfully created.
   Please save the values below as they will not be shown again

     NAME                  USERNAME       ACCESS HOST URL                     ROLE               PASSWORD
    --------------------- -------------- ----------------------------------- ------------------ -------------------------------------------------------
     production-password   xxxxxxxxxxxxx   xxxxxx.us-east-2.psdb.cloud   Can Read & Write   pscale_pw_xxxxxxx
   ```

3. Next, create your `.env` file by renaming the `.env.example` file to `.env`:

   ```bash theme={null}
   mv .env.example .env
   ```

4. Use the values from the CLI output in step 1 to construct your connection string that will be used to connect your Node app to your PlanetScale database. Create your connection string in the following format:

   ```
   mysql://<USERNAME>:<PLAIN_TEXT_PASSWORD>@<ACCESS_HOST_URL>/<DATABASE_NAME>?ssl={"rejectUnauthorized":true}
   ```

5. In the `.env` file, fill in the `DATABASE_URL` variable with the value you constructed above. It should look something like this:

   ```bash theme={null}
   DATABASE_URL=mysql://xxxxxxxxxxxxx:pscale_pw_xxxxxxx@xxxxxx.us-east-2.psdb.cloud/express_database?ssl={"rejectUnauthorized":true}
   ```

6. Finally, run your Express application with:

   ```bash theme={null}
   node app.js
   ```

Navigate to [http://localhost:3000](http://localhost:3000) and you'll see the data from your `users` table!

### Option 2: Using the PlanetScale proxy with the CLI

Use the following command to create a connection to your database and start the application:

```bash theme={null}
pscale connect <DATABASE_NAME> <BRANCH_NAME> --execute 'node app.js'
```

<Note>
  Running `pscale connect` with the execute flag will pass a `DATABASE_URL` to the Node application, enabling it to connect to PlanetScale. Don't forget to look in `app.js` to see how the DATABASE\_URL is used.
</Note>

Navigate to [http://localhost:3000](http://localhost:3000) and you'll see the data from your `users` table!

## What's next?

Learn more about how PlanetScale allows you to make [non-blocking schema changes](../schema-changes.md) to your database tables without locking or causing downtime for production databases. If you're interested in learning how to secure your application when connecting to PlanetScale,
please read [Connecting to PlanetScale securely](../connecting/secure-connections.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
