---
url: https://planetscale.com/docs/neki/connecting
title: "Connecting"
description: ""
access_date: 2026-10-20T18:26:07.257Z
current_date: 2026-10-20T18:26:07.257Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Connect to Neki

> Postgres clients, roles, router groups, and replica routing

Neki uses the Postgres wire protocol, so you can connect with `psql` or any
standard Postgres driver. Your applications connect to a Neki router instead
of connecting to each shard's primary or replicas. The router plans each statement and sends it to
the required Postgres shards.

Create a role for each application or permission boundary. The same role can
be reused by that application's connection pool and other connections that
need the same permissions.

## Get connection details

**Dashboard**

**Step 1**

Open the database in the PlanetScale dashboard and click **Connect**.

**Step 2**

Optionally, select the branch you want to connect to.

**Step 3**

Select an existing role, or create a [user-defined role](connecting/roles.md) with the permissions your application needs.

**Step 4**

Under **Connection target**, choose a router group. The `default`
router group is selected by default.

**Step 5**

To send reads to replicas, select **Replica** or **Read-only
replica** under **Query target**. This option is separate from the
router-group target.

**Step 6**

Select your framework, language, or **Postgres CLI**, then copy the generated connection details.

The generated `psql` command has this form:

```bash theme={null}
psql 'host=<HOST> port=5432 user=<USERNAME> password=<PASSWORD> dbname=postgres sslnegotiation=direct sslmode=verify-full sslrootcert=system'
```

Replace the placeholders with the values from the dashboard. Generated connections use the `postgres` logical database within a cluster.

**CLI**

Install and authenticate the [PlanetScale CLI](../cli/planetscale-environment-setup.md) first.

**Step 1**

Open a `psql` session. `pscale shell` creates temporary credentials, so you do not need a saved role for this step.

```bash theme={null}
pscale shell <DATABASE> <BRANCH> --org <ORGANIZATION>
```

**Step 2**

To target a router group other than the default, add `--router`.

```bash theme={null}
pscale shell <DATABASE> <BRANCH> \
  --org <ORGANIZATION> \
  --router <ROUTER_GROUP>
```

For a persistent role that your application can reuse, create one with `pscale role`. See [Roles and credentials](connecting/roles.md).

## Connection parameters

| Parameter | Value |
| :- | :- |
| Host | The hostname shown on the **Connect** page |
| Port | `5432` |
| Username | The complete generated username, including any suffix for a non-default router group |
| Password | The password shown when the role is created or reset |
| Database | logical database, `postgres` by default |
| TLS | Required; use the verification settings generated for your client |

All connections to Neki databases use port `5432`.

## Secure connections

Neki requires TLS. Your client should verify both the certificate chain and the
server hostname instead of only encrypting the connection.

The generated `psql` command uses `sslmode=verify-full` and the system CA store.
Other drivers use different names for the same settings. Select your framework
or language on the **Connect** page to get the correct TLS configuration for
that client.

## Private connections

Keep traffic between your cloud network and Neki off the public internet with
[AWS PrivateLink](connecting/private-connections/aws-privatelink.md) or
[GCP Private Service Connect](connecting/private-connections/gcp-private-service-connect.md).
Private connections use the same roles, router groups, and required TLS
settings as public connections.

## Primary and replica routing

By default, Neki sends work to shard primaries. Use that for writes and for reads that need the latest committed data.

Set `__neki.target` to send reads to replicas.

Selecting **Replica** under **Query target** on the **Connect** page adds this
setting to the generated connection options. It does not change the username.
To send reads to [read-only replicas](read-only-replicas.md), select
**Read-only replica** instead.

```sql theme={null}
SET __neki.target = 'replica';
```

Or set it when the connection starts. For `psql`, use `PGOPTIONS`:

```bash theme={null}
PGOPTIONS='-c __neki.target=REPLICA' \
  psql 'host=<HOST> port=5432 user=<USERNAME> password=<PASSWORD> dbname=postgres sslnegotiation=direct sslmode=verify-full sslrootcert=system'
```

For a connection URI, add it as the Postgres `options` parameter:

```text theme={null}
postgresql://<USERNAME>:<PASSWORD>@<HOST>:5432/postgres?sslmode=verify-full&sslrootcert=system&options=-c%20__neki.target%3DREPLICA
```

For `pscale shell`, add `--replica`. That sets `__neki.target=REPLICA` for the session.

See [Choosing where reads run](shard-targeting-fanout.md#choosing-where-reads-run) for Neki's replica selection settings and lag behavior.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
