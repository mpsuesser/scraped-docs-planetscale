---
url: https://planetscale.com/docs/postgres/connecting/neon-serverless-driver
title: "Neon Serverless Driver"
description: ""
access_date: 2026-10-20T18:01:41.586Z
current_date: 2026-10-20T18:01:41.586Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Using the Neon serverless driver

The [Neon serverless driver](https://neon.com/docs/serverless/serverless-driver) (`@neondatabase/serverless`) runs Postgres queries from JavaScript and TypeScript over HTTP or WebSockets instead of TCP. PlanetScale supports this driver on platforms where standard TCP drivers and connection pooling are not viable.

For full API details, see [Neon's serverless driver documentation](https://neon.com/docs/serverless/serverless-driver) and the [GitHub repository](https://github.com/neondatabase/serverless).

> [!NOTE]
>
> Driver v1.0.0 and later require Node.js 19 or higher.

## When to use this driver

Use the Neon serverless driver when your deployment platform cannot maintain TCP connections or a connection pool between requests.

| Platform | Recommended approach |
| - | - |
| [Netlify Functions](https://docs.netlify.com/functions/overview/) | Neon serverless driver (this page) |
| [Deno Deploy](https://deno.com/deploy/docs) | Neon serverless driver (this page) |
| [Cloudflare Workers](https://developers.cloudflare.com/workers/) (without Hyperdrive) | Neon serverless driver (this page) |
| [Vercel](https://vercel.com/docs/functions) | [`pg`](https://node-postgres.com/) with [`@vercel/functions`](https://vercel.com/guides/connection-pooling-with-functions) and [PgBouncer on port 6432](pgbouncer.md) |
| [Cloudflare Workers](https://developers.cloudflare.com/workers/) + Hyperdrive | [`pg` with Hyperdrive](../tutorials/planetscale-postgres-cloudflare-workers.md) |
| AWS Lambda, Railway, Render, VPS, Docker | [`pg`](../tutorials/planetscale-postgres-node.md) with [PgBouncer on port 6432](pgbouncer.md) |

### Vercel

[Vercel Fluid compute](https://vercel.com/docs/functions/fluid-compute) reuses warm function instances, making TCP connection pooling safe. For Vercel deployments, connect with [`pg`](https://node-postgres.com/) through [PgBouncer](pgbouncer.md) on port **6432** and pool connections with [`attachDatabasePool`](https://vercel.com/guides/connection-pooling-with-functions) from `@vercel/functions`.

The Neon serverless driver remains an option on Vercel when you cannot use connection pooling (for example, classic serverless without Fluid). See [Neon's Vercel connection guide](https://neon.com/docs/guides/vercel-connection-methods) for a comparison of TCP, HTTP, and WebSocket latency on that platform.

### Cloudflare Workers

For Cloudflare Workers, [Hyperdrive with `pg`](../tutorials/planetscale-postgres-cloudflare-workers.md) is the recommended approach. It provides connection pooling and lower latency for warm connections. Use the Neon serverless driver only when Hyperdrive is not available for your setup.

### Deno Deploy

Install the driver from JSR for Deno projects:

```bash theme={null}
deno add jsr:@neon/serverless
```

See [Neon's Deno Deploy guide](https://neon.com/docs/guides/deno) for deployment details.

## HTTP vs WebSocket modes

The Neon serverless driver supports two connection modes:

* **HTTP mode** — Uses the `neon` function to execute queries over HTTP. Faster for single queries and non-interactive transactions. No connection state is maintained between requests.
* **WebSocket mode** — Uses the `Pool` object to establish a WebSocket connection. Required for session support, interactive transactions, or `node-postgres` compatibility.

PlanetScale supports both modes. Choose based on your use case:

| Use case | Recommended mode |
| - | - |
| Single queries | HTTP |
| Non-interactive transactions (batch of queries) | HTTP |
| Interactive transactions | WebSocket |
| Session-based features | WebSocket |
| `node-postgres` compatibility | WebSocket |

## Setting up credentials

Both modes use the same credentials setup.

**Step 1**

Install the driver via npm:

```bash theme={null}
npm install @neondatabase/serverless
```

**Step 2**

You'll need to create a [Postgres role](roles.md) to use with the driver.
Once you have these credentials, place them in environment variables:

```bash theme={null}
DATABASE_HOST=XXXX.pg.psdb.cloud
DATABASE_PORT=5432
DATABASE_NAME=XXXX
DATABASE_USERNAME=XXXX
DATABASE_PASSWORD=pscale_pw_XXXX
```

These can all be added to a unified Postgres connection URL for use by the driver:

```bash theme={null}
DATABASE_URL="postgresql://$DATABASE_USERNAME:$DATABASE_PASSWORD@$DATABASE_HOST:$DATABASE_PORT/$DATABASE_NAME"
```

## Using HTTP mode

HTTP mode is the simplest way to execute queries. You must configure the `fetchEndpoint` to use PlanetScale's SQL endpoint.

```ts theme={null}
import { neon, neonConfig } from "@neondatabase/serverless";

// This MUST be set for PlanetScale Postgres connections
neonConfig.fetchEndpoint = (host) => `https://${host}/sql`;

const sql = neon(process.env.DATABASE_URL!);

const posts = await sql`SELECT * FROM posts WHERE id = ${postId}`;
```

The `neon` function returns a tagged template literal that automatically handles parameterized queries, protecting against SQL injection. For manually parameterized queries, use `sql.query()`:

```ts theme={null}
const posts = await sql.query("SELECT * FROM posts WHERE id = $1", [postId]);
```

See [Neon's HTTP mode documentation](https://neon.com/docs/serverless/serverless-driver#use-the-driver-over-http) for `arrayMode`, `fullResults`, `fetchOptions`, and other configuration options.

### Non-interactive transactions

HTTP mode supports non-interactive transactions where you send a batch of queries to be executed together. Use the `transaction` function:

```ts theme={null}
import { neon, neonConfig } from "@neondatabase/serverless";

// This MUST be set for PlanetScale Postgres connections
neonConfig.fetchEndpoint = (host) => `https://${host}/sql`;

const sql = neon(process.env.DATABASE_URL!);

const [posts, tags] = await sql.transaction(
  [
    sql`SELECT * FROM posts ORDER BY posted_at DESC LIMIT ${limit}`,
    sql`SELECT * FROM tags`,
  ],
  { isolationLevel: "ReadCommitted", readOnly: true }
);
```

## Using WebSocket mode

WebSocket mode provides a full `Pool` interface compatible with the `pg` library. See [Neon's WebSocket documentation](https://neon.com/docs/serverless/serverless-driver#use-the-driver-over-websockets) for the full API reference. This mode requires additional configuration for PlanetScale connections.

> [!WARNING]
>
> In serverless and edge environments, WebSocket connections cannot outlive a single request. Create, use, and close `Pool` or `Client` objects within the same request handler. Do not create pools outside a handler or reuse them across handlers.

**Step 1**

When connecting, you must set the following configuration options:

```ts theme={null}
neonConfig.pipelineConnect = false;
neonConfig.wsProxy = (host, port) => `${host}/v2?address=${host}:${port}`;
```

**Step 2**

Here's a complete example:

```ts theme={null}
import ws from "ws";
import { Pool, neonConfig } from "@neondatabase/serverless";

neonConfig.webSocketConstructor = ws;
// These MUST be set for PlanetScale Postgres connections
neonConfig.pipelineConnect = false;
neonConfig.wsProxy = (host, port) => `${host}/v2?address=${host}:${port}`;

const pool = new Pool({ connectionString: process.env.DATABASE_URL });

const posts = await pool.query("SELECT * FROM posts WHERE id = $1", [postId]);

pool.end();
```

> [!NOTE]
>
> In browser or edge environments that have a native `WebSocket` global, you don't need to import `ws` or set `neonConfig.webSocketConstructor`.

## Security

PlanetScale requires `SCRAM-SHA-256` for all authentication to Postgres servers.
We maintain this strict requirement for security purposes.

For WebSocket connections, you must set `neonConfig.pipelineConnect = false;`.
This adds a bit of additional latency, but is necessary to connect using `SCRAM-SHA-256`.
When this is `"password"` (the default value) it requires using cleartext password authentication, reducing connection security.

HTTP mode connections handle authentication automatically and don't require this configuration.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
