---
url: https://planetscale.com/docs/neki/guides/django
title: "Django"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

Django connects to Neki through its Postgres database backend.

Before you start, you need:

- A Neki database with a ready branch.
- An application [role](../connecting/roles.md) for that branch. Use `pg_read_all_data` for read traffic and add `pg_write_all_data` when the application writes rows. These inherited data roles do not grant `CREATE` or `ALTER`, so application credentials cannot change the schema.
- A separate migration role, if you run schema changes against the branch. DDL requires the `postgres` inherited role. Neki restricts execution of the cross-router DDL barrier function `__neki.wait_for_ddl` to its `neki_viewer` role, so add `neki_viewer`, which requires `pg_read_all_data`, to the same role. Keep the migration credentials out of the application’s runtime configuration.
- The **Primary** connection details from the database’s **Connect** page.

Use the complete generated username. It contains the branch routing information required by Neki.

## Install the Postgres driver

Install Psycopg. This example also uses `python-dotenv` for local environment variables:

```shellscript
pip install "psycopg[binary]" python-dotenv
```

## Add the connection details

Create a `.env` file with the values from the **Connect** page. Set `DB_SSLROOTCERT` to the path of the system CA bundle:

.env

```shellscript
DB_HOST=<HOST>
DB_PORT=5432
DB_NAME=postgres
DB_USER=<USERNAME>
DB_PASSWORD=<PASSWORD>
DB_SSLROOTCERT=<SYSTEM_CA_PATH>
```

## Configure Django

Load the environment file and configure the Postgres backend in `settings.py`:

settings.py

```python
import os

from dotenv import load_dotenv

load_dotenv()

DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        "HOST": os.environ["DB_HOST"],
        "PORT": os.environ.get("DB_PORT", "5432"),
        "NAME": os.environ.get("DB_NAME", "postgres"),
        "USER": os.environ["DB_USER"],
        "PASSWORD": os.environ["DB_PASSWORD"],
        "OPTIONS": {
            "sslmode": "verify-full",
            "sslrootcert": os.environ["DB_SSLROOTCERT"],
        },
    }
}
```

Confirm that Django can connect:

```shellscript
python manage.py check --database default
```

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

To send a separate Django connection to replicas, add the Neki startup option described in [Primary and replica routing](../connecting.md#primary-and-replica-routing) to its `OPTIONS`.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
