---
url: https://planetscale.com/docs/vitess/tutorials/planetscale-serverless-driver
title: "Planetscale Serverless Driver"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale serverless driver for JavaScript

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

## Why use the PlanetScale serverless driver

Before learning how to use the PlanetScale serverless driver for JavaScript, it’s worth understanding why you should use this over other MySQL packages available in the directory. Some serverless and edge function hosts do not permit arbitrary outbound TCP connections, which is how many MySQL clients operate.

Using the PlanetScale serverless driver for JavaScript provides a means of accessing your database and executing queries over an HTTP connection, which is generally not blocked by cloud providers. If you encounter issues using MySQL packages with PlanetScale, use the serverless driver instead.

<Note>
  Be sure to check out our [F1 Championship Stats demo application](https://github.com/planetscale/f1-championship-stats) to find examples for use with Cloudflare Workers, Vercel Edge Functions, and Netlify Edge Functions.
</Note>

## Add and use the PlanetScale serverless driver for JavaScript to your project

To install the package in your project, run the following install command:

```bash theme={null}
npm install @planetscale/database
```

### Connect to the database

The first step to using the PlanetScale serverless driver for JavaScript is to connect to your database.

You can get your connection string in the PlanetScale dashboard by clicking on your database, clicking "**Connect**", and selecting `database-js` from the "Select your language or framework" section.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/planetscale-serverless-driver/connect-serverless-credentials-database-js.png?fit=max&auto=format&n=AJPY38bILe2zenXX&q=85&s=5c2e3614d9bbca1970191da46306d547" alt="Database-js selection priority" width="3512" height="1632" data-path="images/assets/docs/tutorials/planetscale-serverless-driver/connect-serverless-credentials-database-js.png" />
</Frame>

Scroll down to the env variables. You'll need this to connect to your database.

Use the `connect` function to create the connection and return it to an object.

```js theme={null}
const config = {
  host: 'aws.connect.psdb.cloud',
  username: '<PS_USERNAME>',
  password: '<PS_PASSWORD>'
}
const conn = await connect(config)
```

### Executing queries

To execute a query, use the `execute` function of the connection object, with the query passed as the first parameter.

```js theme={null}
const results = await conn.execute('SELECT * FROM hotels')
```

Here is the content of the `results` object from the `SELECT` statement:

```js expandable theme={null}
{
  headers: [ 'id', 'name', 'address', 'stars' ],
  types: {
    id: 'UINT32',
    name: 'VARCHAR',
    address: 'VARCHAR',
    stars: 'FLOAT32'
  },
  rows: [
    {
      id: 1,
      name: 'Four Seasons Resort Jackson hole',
      address: '7680 Granite Loop Rd, Teton Village, WY 83025',
      stars: 4.7
    },
    {
      id: 2,
      name: 'The Galt House',
      address: '140 N Fourth St, Louisville, KY 40202',
      stars: 4
    },
    // ...results removed for brevity
  ],
  rowsAffected: null,
  insertId: null,
  error: null,
  size: 5,
  statement: 'SELECT * FROM hotels',
  time: 136
}
```

For parameterized queries, there are two ways in which to pass data to the query. The first is by the order in which they appear in the query. The first step is to add a `?` in the specific locations you want the parameters passed into.

```js theme={null}
const query = 'INSERT INTO hotels (`name`, `address`, `stars`) VALUES (?, ?, ?)'
```

Then you can pass your parameters as an array of values. The driver package will replace the `?` entries in the query with the values passed in the array, in the order in which they were placed.

```js theme={null}
const params = ['The Galt House', '140 N Fourth St, Louisville, KY 40202', 4.2]
const results = await conn.execute(query, params)
```

Here is the content of the `results` object for the `INSERT` statement:

```js theme={null}
{
  headers: [],
  types: {},
  rows: [],
  rowsAffected: 1,
  insertId: '6',
  error: null,
  size: 0,
  statement: "INSERT INTO hotels (`name`, `address`, `stars`) VALUES ('Montage Kapalua Bay 2', '1 Bay Dr, Lahaina, HI 96761', 4)",
  time: 102
}
```

Alternately, you can name your parameters using the `:param_name` format.

```js theme={null}
const query = 'INSERT INTO hotels (`name`, `address`, `stars`) VALUES (:name, :address, :stars)'
const params = {
  name: 'The Galt House',
  address: '140 N Fourth St, Louisville, KY 40202',
  stars: 4.2
}
const results = await conn.execute(query, params)
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
