---
url: https://planetscale.com/docs/neki/guides/cloudflare-workers
title: "Cloudflare Workers"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

[Cloudflare Hyperdrive](https://developers.cloudflare.com/hyperdrive/) maintains a connection pool between Cloudflare Workers and Neki. The Worker connects to the Hyperdrive binding, and Hyperdrive connects to the Neki router.

Before you start, you need:

- A Neki database with a ready branch.
- An application [role](../connecting/roles.md) for that branch. Use `pg_read_all_data` for read traffic and add `pg_write_all_data` when the application writes rows. These inherited data roles do not grant `CREATE` or `ALTER`, so application credentials cannot change the schema.
- A separate migration role, if you run schema changes against the branch. DDL requires the `postgres` inherited role. Neki restricts execution of the cross-router DDL barrier function `__neki.wait_for_ddl` to its `neki_viewer` role, so add `neki_viewer`, which requires `pg_read_all_data`, to the same role. Keep the migration credentials out of the application’s runtime configuration.
- The **Primary** connection details from the database’s **Connect** page.

Use the complete generated username. It contains the branch routing information required by Neki.

You also need a Cloudflare account and a Workers project with Wrangler installed and authenticated.

## Create a Workers project

Create a TypeScript Worker and install `pg`:

```shellscript
npm create cloudflare@latest -- neki-worker
cd neki-worker
npm install 'pg@>8.16.3'
npm install --save-dev @types/pg
```

When prompted, choose a Worker-only application and do not deploy it yet.

## Create a Hyperdrive configuration

Cloudflare Hyperdrive requires a `pg` version newer than 8.16.3.

Cloudflare currently documents Hyperdrive compatibility through Postgres 17. Neki identifies as Postgres 18. Validate your required query paths before using this integration in production.

Download the ISRG Root X1 certificate in PEM format from [Let’s Encrypt](https://letsencrypt.org/certificates/), then upload the single certificate to Cloudflare:

```shellscript
npx wrangler cert upload certificate-authority \
  --ca-cert isrg-root-x1.pem \
  --name neki-isrg-root-x1
```

The command returns a CA certificate ID.

Copy the **Primary** connection URI from Neki’s **Connect** page and remove the `sslmode` and `sslrootcert` query parameters. Hyperdrive configures TLS to the Neki origin separately from the connection URI.

Create the Hyperdrive configuration with the CA certificate ID and `verify-full` SSL mode:

```shellscript
npx wrangler hyperdrive create neki-main \
  --connection-string='<NEKI_DATABASE_URL_WITHOUT_SSL_QUERY_PARAMETERS>' \
  --ca-certificate-id <CA_CERTIFICATE_ID> \
  --sslmode verify-full
```

`verify-full` verifies the certificate chain and checks that the Neki hostname matches the certificate. See Cloudflare’s [Hyperdrive TLS configuration](https://developers.cloudflare.com/hyperdrive/configuration/tls-ssl-certificates-for-hyperdrive/) for details. Wrangler verifies the database connection before creating the configuration.

The command returns a Hyperdrive ID. Add that ID to `wrangler.jsonc`. Use a recent `compatibility_date` rather than a hardcoded older date:

wrangler.jsonc

```jsonc
{
  "compatibility_date": "<RECENT_DATE>",
  "compatibility_flags": ["nodejs_compat"],
  "hyperdrive": [
    {
      "binding": "HYPERDRIVE",
      "id": "<HYPERDRIVE_ID>"
    }
  ]
}
```

## Query Neki from the Worker

Create a `pg` client from the Hyperdrive connection string:

src/index.ts

```typescript
import { Client } from 'pg'

interface Env {
  HYPERDRIVE: Hyperdrive
}

export default {
  async fetch(_request: Request, env: Env): Promise<Response> {
    const client = new Client({
      connectionString: env.HYPERDRIVE.connectionString,
    })

    await client.connect()

    const result = await client.query(
      'SELECT current_database() AS database, current_user AS role'
    )

    return Response.json(result.rows[0])
  },
} satisfies ExportedHandler<Env>
```

Create the client inside the request handler. Hyperdrive pools the underlying origin connections, and Workers automatically clean up the client connection when the request ends. You do not need to call `client.end()`. See Cloudflare’s [connection lifecycle](https://developers.cloudflare.com/hyperdrive/concepts/connection-lifecycle/) guidance.

Deploy the Worker:

```shellscript
npx wrangler deploy
```

Hyperdrive can cache eligible read queries. Its cache does not automatically invalidate after an application write. Disable caching for reads that require read-after-write consistency.

## Apply schema changes

Framework migration commands send DDL through a normal Neki connection. The router fans that DDL out to the managed shards, but it does not create a managed schema-change workflow. Use a [schema-change workflow](../schema-changes.md) when you want Neki to prepare and coordinate an Online DDL change.

Run migrations with the migration role described in the prerequisites. An application role that inherits only `pg_read_all_data` and `pg_write_all_data` cannot run `CREATE` or `ALTER`, so a migration that uses application credentials fails on its first DDL statement.

Apply migration DDL with `psql` and the migration role so the client prints PostgreSQL notices:

```shellscript
psql "$MIGRATION_DATABASE_URL" -f <MIGRATION_FILE>
```

The router that commits the DDL emits a `NOTICE` containing the barrier call for that transaction:

```text
NOTICE: DDL is committed and the change is visible on this router; other routers may not see it yet. To wait until it is visible on every router, use __neki.wait_for_ddl(42, 7)
```

The schema and cluster versions belong to that transaction, and the notice is the only place Neki reports them. Before you deploy application code that depends on the new schema, run the emitted call verbatim on a primary connection that uses the migration role:

```sql
SELECT __neki.wait_for_ddl(42, 7);
```

The call returns after every router has applied both versions. A successful migration only confirms that the DDL committed and is visible through the router that ran it; it does not replace this cross-router barrier.

Some framework migration commands discard notices. Generate migration SQL with the framework, but apply it with `psql` when the framework cannot preserve the notice. If the notice is lost, its version pair cannot be reconstructed.

Run schema migrations outside the Worker through a direct Neki connection. Hyperdrive’s transaction pooling does not preserve the per-session state or notices required by the DDL barrier workflow.

Hyperdrive does not currently provide a documented way to pass Neki’s replica startup option to its origin connections. Keep Hyperdrive traffic on the primary target.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
