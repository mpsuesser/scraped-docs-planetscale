---
url: https://planetscale.com/docs/vitess/tutorials/planetscale-serverless-driver-node-example
title: "Planetscale Serverless Driver Node Example"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Node.js example using the PlanetScale serverless driver

> This guide will cover how to use the provided Node.js sample application using the PlanetScale serverless driver for JavaScript.

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

## Overview

<Note>
  This guide will be using VS Code as the IDE, but you may use your preferred IDE.
</Note>

## Use the sample repository

We offer a sample repository that can be used as an educational resource. It is an Express API that can be run locally with sample `SELECT`, `INSERT`, `UPDATE`, and `DELETE` statements mapped to the proper API endpoints.

To follow along, you’ll need the following:

* A PlanetScale account, as well as knowing how to create a database.
* The PlanetScale CLI is installed on your computer, which will be used to seed data.

Start by creating a database in PlanetScale by clicking **"New database"** > **"Create new database"**.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/planetscale-serverless-driver-node-example/how-to-create-a-new-database-2.png?fit=max&auto=format&n=AJPY38bILe2zenXX&q=85&s=beb148475e5dfe5db5f24934b11ad022" alt="How to create a new database. priority" width="1604" height="825" data-path="images/assets/docs/tutorials/planetscale-serverless-driver-node-example/how-to-create-a-new-database-2.png" />
</Frame>

Name the database `travel_db`. Click **"Create database"**. Wait for the database to finish initializing before moving on.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/planetscale-serverless-driver-node-example/initializing.png?fit=max&auto=format&n=AJPY38bILe2zenXX&q=85&s=5fc78b39b71f92b2fe184b2478fb5a84" alt="The travel_db initializing." width="3490" height="1144" data-path="images/assets/docs/tutorials/planetscale-serverless-driver-node-example/initializing.png" />
</Frame>

Generate a set of credentials by clicking the **"Connect"** button.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/planetscale-serverless-driver-node-example/the-connect-button-in-the-planetscale-dashboard.png?fit=max&auto=format&n=AJPY38bILe2zenXX&q=85&s=2fd16015214563dbbc2b6b06a1c2c402" alt="The Connect button in the PlanetScale dashboard." width="3494" height="1078" data-path="images/assets/docs/tutorials/planetscale-serverless-driver-node-example/the-connect-button-in-the-planetscale-dashboard.png" />
</Frame>

Copy your password credentials first:

<Frame>
  <img src="https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/planetscale-serverless-driver-node-example/the-serverlessjs-connect-modal.png?fit=max&auto=format&n=AJPY38bILe2zenXX&q=85&s=f73a60ddc00f0cb91e3cae3555e92e05" alt="The password details." width="3498" height="1118" data-path="images/assets/docs/tutorials/planetscale-serverless-driver-node-example/the-serverlessjs-connect-modal.png" />
</Frame>

Scroll down and select **"database-js"** from the "Select your language or framework" options. Copy the text from the **".env"** section, as we'll be putting this in the project after it's pulled down from GitHub.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/planetscale-serverless-driver-node-example/the-serverlessjs-connect-modal-env.png?fit=max&auto=format&n=AJPY38bILe2zenXX&q=85&s=a0eedb7b2b936dbf75a9d8bca97148b1" alt="The password env details." width="3496" height="618" data-path="images/assets/docs/tutorials/planetscale-serverless-driver-node-example/the-serverlessjs-connect-modal-env.png" />
</Frame>

On your workstation, open a terminal and clone the repository to your computer by running the following command:

```bash theme={null}
git clone https://github.com/planetscale/database-js-starter
```

Navigate to the `scripts` folder and run the `seed_database.sh` script to populate a small database simulating a travel agency.

```bash theme={null}
cd database-js-starter/scripts
./seed-database.sh
```

<Note>
  If you are using Windows, run this command through the [Windows Subsystem for Linux (WSL)](https://docs.microsoft.com/en-us/windows/wsl/)
</Note>

Create a new file named `.env` in the root of the project and paste in the sample provided from PlanetScale that you copied earlier.

To run the project, run the following commands from the root of the project.

```bash theme={null}
npm install
npm start
```

If the project is running properly, you should receive a message stating that the API is running.

The `tests.http` file is designed to work with the [VS Code Rest Client plugin](https://marketplace.visualstudio.com/items?itemName=humao.rest-client), but can be used as a reference when testing with the tool of your choosing. If you are using the plugin, you may click the **"Send request"** button that appears above each request to see the API in action.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/planetscale-serverless-driver-node-example/an-example-of-a-post-request-to-the-sample-project.png?fit=max&auto=format&n=AJPY38bILe2zenXX&q=85&s=d1eeca2575f13571f643ef7af27003e8" alt="An example of a POST request to the sample project." width="860" height="310" data-path="images/assets/docs/tutorials/planetscale-serverless-driver-node-example/an-example-of-a-post-request-to-the-sample-project.png" />
</Frame>

If you check the terminal where the API was started, the response from the `execute` function is logged out for review.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
