---
url: https://planetscale.com/docs/neki/guides/rails
title: "Rails"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

Rails connects to Neki through Active Record’s Postgres adapter.

Before you start, you need:

- A Neki database with a ready branch.
- An application [role](../connecting/roles.md) for that branch. Use `pg_read_all_data` for read traffic and add `pg_write_all_data` when the application writes rows. These inherited data roles do not grant `CREATE` or `ALTER`, so application credentials cannot change the schema.
- A separate migration role, if you run schema changes against the branch. DDL requires the `postgres` inherited role. Neki restricts execution of the cross-router DDL barrier function `__neki.wait_for_ddl` to its `neki_viewer` role, so add `neki_viewer`, which requires `pg_read_all_data`, to the same role. Keep the migration credentials out of the application’s runtime configuration.
- The **Primary** connection details from the database’s **Connect** page.

Use the complete generated username. It contains the branch routing information required by Neki.

## Install the Postgres adapter

Add `pg` to the application’s Gemfile:

Gemfile

```ruby
gem "pg"
```

Install the dependency:

```shellscript
bundle install
```

## Store the connection details

Open the encrypted credentials for the target environment:

```shellscript
bin/rails credentials:edit --environment production
```

Add the values from the **Connect** page. Set `sslrootcert` to the path of the system CA bundle. Common paths include `/etc/ssl/certs/ca-certificates.crt` on Debian and Ubuntu, `/etc/pki/tls/certs/ca-bundle.crt` on RHEL, and `/etc/ssl/cert.pem` on macOS:

config/credentials/production.yml.enc

```yaml
planetscale:
  host: <HOST>
  port: 5432
  database: postgres
  username: <USERNAME>
  password: <PASSWORD>
  sslrootcert: <SYSTEM_CA_PATH>
```

Environment-specific credentials replace the global credentials for that environment. Keep `secret_key_base` in the production credentials, or set `SECRET_KEY_BASE` where the application runs, so Rails can start in production. For local development, use `bin/rails credentials:edit --environment development` and a matching `development` entry in `config/database.yml`.

## Configure Active Record

Add the production connection to `config/database.yml`:

config/database.yml

```yaml
production:
  primary: &primary
    <<: *default
    adapter: postgresql
    host: <%= Rails.application.credentials.planetscale.fetch(:host) %>
    port: <%= Rails.application.credentials.planetscale.fetch(:port) %>
    database: <%= Rails.application.credentials.planetscale.fetch(:database) %>
    username: <%= Rails.application.credentials.planetscale.fetch(:username) %>
    password: <%= Rails.application.credentials.planetscale.fetch(:password) %>
    sslmode: verify-full
    sslrootcert: <%= Rails.application.credentials.planetscale.fetch(:sslrootcert) %>
    pool: <%= ENV.fetch("RAILS_MAX_THREADS", 5) %>
  primary_replica:
    <<: *primary
    options: "-c __neki.target=REPLICA"
    replica: true
```

Confirm the connection:

```shellscript
RAILS_ENV=production bin/rails runner \
  'puts ActiveRecord::Base.connection.select_value("SELECT current_database()")'
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

## Route Active Record reads to replicas

The `primary_replica` entry connects to the same Neki endpoint and database, but the libpq `options` parameter sets the target when the connection starts. `replica: true` prevents Rails database tasks, including migrations, from running through the reader.

Map Active Record’s writing and reading roles in `ApplicationRecord`:

app/models/application\_record.rb

```ruby
class ApplicationRecord < ActiveRecord::Base
  self.abstract_class = true

  connects_to database: { writing: :primary, reading: :primary_replica }
end
```

Confirm that the reading role uses replicas:

```shellscript
RAILS_ENV=production bin/rails runner \
  'ActiveRecord::Base.connected_to(role: :reading) { puts ActiveRecord::Base.connection.select_value("SHOW __neki.target") }'
```

The command prints `REPLICA`. Replica reads can return stale data, so keep reads that require the latest committed data on the writing role. See [Primary and replica routing](../connecting.md#primary-and-replica-routing).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
