---
url: https://planetscale.com/docs/terraform
title: "Terraform"
description: ""
access_date: 2026-09-10T14:43:51.520Z
current_date: 2026-09-10T14:43:51.520Z
---

Terraform is an open-source infrastructure-as-code tool that lets you define and manage cloud resources through declarative configuration files. With the [PlanetScale Terraform provider](https://github.com/planetscale/terraform-provider-planetscale), you can manage PlanetScale Vitess, Neki, and Postgres databases, branches, credentials, backups, backup policies, Neki routers and shards, dedicated PgBouncers, and cluster parameters alongside the rest of your infrastructure.

## Conceptual model

The PlanetScale Terraform provider is organized around a clear distinction between **Vitess**, **Neki**, and **Postgres** resources and is focused on long-lived infrastructure objects.

- There are separate resources for each **database kind** —Vitess, Neki, or Postgres.
- The provider maps cleanly onto PlanetScale’s public API while exposing Terraform-friendly fields and lifecycle behavior.

PlanetScale databases do not have a dedicated Terraform resource. Their lifecycle is managed implicitly through branch resources:

- Applying a `planetscale_vitess_branch`, `planetscale_neki_branch`, or `planetscale_postgres_branch` creates the parent database if it does not already exist.
- Destroying the last branch in a database also destroys the database. See [Deletion protection](#deletion-protection) for safeguards.

Creating a Neki branch also provisions a default [configuration profile](https://planetscale.com/docs/neki/cluster-configuration#configuration-profiles), [shard](https://planetscale.com/docs/neki/cluster-configuration#manage-shards), [router group](https://planetscale.com/docs/neki/cluster-configuration#configure-router-groups), [admin](https://planetscale.com/docs/neki/cluster-configuration#configure-the-admin-service), and [sidecar](https://planetscale.com/docs/neki/overview#what-runs-alongside-postgres). Import those objects if Terraform should manage them. Do not recreate the defaults with `planetscale_neki_configuration_profile` or `planetscale_neki_shard`. Creating a Vitess branch likewise provisions the branch’s default [keyspace](vitess/sharding/keyspaces.md); import it to manage its size or replicas.

### Credential models

**Vitess**, **Neki**, and **Postgres** use different terminology for database credentials:

| Database Type | Resource | How permissions work |
| --- | --- | --- |
| Vitess | `planetscale_vitess_branch_password` | Set the `role` attribute to `reader`, `writer`, `readwriter`, or `admin` |
| Neki | `planetscale_neki_role` | Use `inherited_roles` to inherit from [built-in Postgres roles](https://planetscale.com/docs/neki/connecting/roles) like `pg_read_all_data` or `pg_write_all_data`. |
| Postgres | `planetscale_postgres_branch_role` | Use `inherited_roles` to inherit from [built-in Postgres roles](postgres/connecting/roles.md) like `pg_read_all_data` or `pg_write_all_data`. |
| Postgres | `planetscale_postgres_redacted_branch_role` | Same as `planetscale_postgres_branch_role`, but the password is not stored in Terraform state. Use this when you manage passwords in a secret manager. |

## Quick start

This complete example creates a Postgres branch with application credentials. Vitess and Neki follow the same pattern with their own branch and credential resources.

```hcl
terraform {
  required_providers {
    planetscale = {
      source  = "planetscale/planetscale"
      version = "~> 1.3"
    }
  }
}

# Manage a branch (the database is created automatically if it does not exist)
resource "planetscale_postgres_branch" "main" {
  organization = "my-org"
  database     = "my-db"
  name         = "main"
}

# Create application credentials
resource "planetscale_postgres_branch_role" "app" {
  organization    = "my-org"
  database        = "my-db"
  branch          = planetscale_postgres_branch.main.id
  name            = "app"
  inherited_roles = ["pg_read_all_data", "pg_write_all_data"]
}

# Output connection details
output "connection_string" {
  sensitive = true
  value     = "postgres://${planetscale_postgres_branch_role.app.username}:${planetscale_postgres_branch_role.app.password}@${planetscale_postgres_branch_role.app.access_host_url}/${planetscale_postgres_branch_role.app.database_name}"
}
```

Run:

```shellscript
export PLANETSCALE_SERVICE_TOKEN_ID="your-token-id"
export PLANETSCALE_SERVICE_TOKEN="your-token"
terraform init
terraform apply
terraform output -raw connection_string
```

## Available resources

The provider offers resources for the most common automation scenarios:

**Vitess**

- `planetscale_vitess_backup_policy`
- `planetscale_vitess_branch`
- `planetscale_vitess_branch_backup`
- `planetscale_vitess_branch_password`
- `planetscale_vitess_keyspace`

**Neki**

- `planetscale_neki_admin`
- `planetscale_neki_backup_policy`
- `planetscale_neki_branch`
- `planetscale_neki_configuration_profile`
- `planetscale_neki_role`
- `planetscale_neki_router`
- `planetscale_neki_shard`
- `planetscale_neki_sidecar`

**Postgres**

- `planetscale_postgres_backup_policy`
- `planetscale_postgres_bouncer`
- `planetscale_postgres_branch`
- `planetscale_postgres_branch_backup`
- `planetscale_postgres_branch_role`
- `planetscale_postgres_read_only_replica`
- `planetscale_postgres_redacted_branch_role`

## Data sources

The provider also offers data sources for reading existing resources:

**Organization and databases**

- `planetscale_databases`
- `planetscale_database_postgres`
- `planetscale_database_vitess`
- `planetscale_organization`
- `planetscale_organizations`

**Vitess**

- `planetscale_vitess_backup_policies`
- `planetscale_vitess_backup_policy`
- `planetscale_vitess_branch`
- `planetscale_vitess_branch_backup`
- `planetscale_vitess_branch_backups`
- `planetscale_vitess_branch_password`
- `planetscale_vitess_branch_passwords`
- `planetscale_vitess_keyspace`
- `planetscale_vitess_keyspaces`

**Neki**

- `planetscale_neki_admin`
- `planetscale_neki_backup_policies`
- `planetscale_neki_backup_policy`
- `planetscale_neki_branch`
- `planetscale_neki_configuration_profile`
- `planetscale_neki_configuration_profiles`
- `planetscale_neki_role`
- `planetscale_neki_roles`
- `planetscale_neki_router`
- `planetscale_neki_routers`
- `planetscale_neki_shard`
- `planetscale_neki_shards`
- `planetscale_neki_sidecar`

**Postgres**

- `planetscale_postgres_backup_policies`
- `planetscale_postgres_backup_policy`
- `planetscale_postgres_bouncer`
- `planetscale_postgres_bouncers`
- `planetscale_postgres_branch`
- `planetscale_postgres_branch_backup`
- `planetscale_postgres_branch_backups`
- `planetscale_postgres_branch_role`
- `planetscale_postgres_branch_roles`
- `planetscale_postgres_read_only_replica`
- `planetscale_postgres_read_only_replicas`
- `planetscale_postgres_redacted_branch_role`

### Data source behavior

| Data Source | Required Inputs | Notes |
| --- | --- | --- |
| `planetscale_databases` | `organization` | Lists Vitess, Neki, and Postgres databases. Optional `q` parameter for filtering by name. |
| `planetscale_database_postgres` | `organization` | Returns Postgres database info. Does not accept a database name filter. |
| `planetscale_database_vitess` | `organization` | Returns Vitess database info. Does not accept a database name filter. |
| `planetscale_organization` | `organization` | Returns organization details. |
| `planetscale_organizations` | (none) | Lists all accessible organizations. |
| `planetscale_vitess_backup_policies` | `organization`, `database` | Lists Vitess backup policies for a database. |
| `planetscale_vitess_backup_policy` | `organization`, `database`, `id` | Fetches a specific Vitess backup policy by ID. |
| `planetscale_vitess_branch` | `organization`, `database` | Returns the default branch. Does not accept a branch name parameter. |
| `planetscale_vitess_branch_backups` | `organization`, `database`, `branch` | Lists Vitess backups for a branch. Supports filters like `state`, `from`, `to`, and `policy`. |
| `planetscale_vitess_branch_backup` | `organization`, `database`, `branch`, `id` | Fetches a specific Vitess backup by ID. |
| `planetscale_vitess_branch_passwords` | `organization`, `database`, `branch` | Lists all passwords on a branch. |
| `planetscale_vitess_branch_password` | `organization`, `database`, `branch`, `id` | Fetches a specific password by ID. |
| `planetscale_vitess_keyspace` | `organization`, `database`, `branch`, `name` | Fetches a specific keyspace by name. |
| `planetscale_vitess_keyspaces` | `organization`, `database`, `branch` | Lists keyspaces for a branch. |
| `planetscale_neki_admin` | `organization`, `database`, `branch` | Fetches the admin for a branch. |
| `planetscale_neki_backup_policies` | `organization`, `database` | Lists Neki backup policies for a database. |
| `planetscale_neki_backup_policy` | `organization`, `database`, `id` | Fetches a specific Neki backup policy by ID. |
| `planetscale_neki_branch` | `organization`, `database`, `id` | Fetches a specific branch by ID. Unlike the Vitess and Postgres branch data sources, this requires a branch ID. |
| `planetscale_neki_configuration_profile` | `organization`, `database`, `branch`, `name` | Fetches a specific configuration profile by name. |
| `planetscale_neki_configuration_profiles` | `organization`, `database`, `branch` | Lists configuration profiles for a branch. |
| `planetscale_neki_role` | `organization`, `database`, `branch`, `id` | Fetches a specific role by ID. Does not return the password. |
| `planetscale_neki_roles` | `organization`, `database`, `branch` | Lists roles on a branch. Optional `q` and `status` filters. |
| `planetscale_neki_router` | `organization`, `database`, `branch`, `name` | Fetches a specific router group by name. |
| `planetscale_neki_routers` | `organization`, `database`, `branch` | Lists router groups for a branch. |
| `planetscale_neki_shard` | `organization`, `database`, `branch`, `id` | Fetches a specific shard by ID. |
| `planetscale_neki_shards` | `organization`, `database`, `branch` | Lists shards for a branch. |
| `planetscale_neki_sidecar` | `organization`, `database`, `branch`, `configuration_profile` | Fetches the sidecar for a configuration profile. |
| `planetscale_postgres_backup_policies` | `organization`, `database` | Lists Postgres backup policies for a database. |
| `planetscale_postgres_backup_policy` | `organization`, `database`, `id` | Fetches a specific Postgres backup policy by ID. |
| `planetscale_postgres_bouncer` | `organization`, `database`, `branch`, `name` | Fetches a specific dedicated PgBouncer by name. |
| `planetscale_postgres_bouncers` | `organization`, `database`, `branch` | Lists dedicated PgBouncers for a branch. |
| `planetscale_postgres_branch` | `organization`, `database` | Returns the default branch. Does not accept a branch name parameter. |
| `planetscale_postgres_branch_backups` | `organization`, `database`, `branch` | Lists Postgres backups for a branch. Supports filters like `state`, `from`, `to`, and `policy`. |
| `planetscale_postgres_branch_backup` | `organization`, `database`, `branch`, `id` | Fetches a specific Postgres backup by ID. |
| `planetscale_postgres_branch_roles` | `organization`, `database`, `branch` | Lists all roles on a branch. |
| `planetscale_postgres_branch_role` | `organization`, `database`, `branch`, `id` | Fetches a specific role by ID. Useful for importing existing roles. |
| `planetscale_postgres_read_only_replica` | `organization`, `database`, `branch`, `name` | Fetches a specific read-only replica by name. |
| `planetscale_postgres_read_only_replicas` | `organization`, `database`, `branch` | Lists read-only replicas for a branch. |
| `planetscale_postgres_redacted_branch_role` | `organization`, `database`, `branch`, `id` | Fetches a specific role by ID without reading the password into state. |

Refer to the [Terraform Registry and provider documentation](https://registry.terraform.io/providers/planetscale/planetscale/latest) for the full, up-to-date list of available resources and data sources.

## Provider configuration

The provider supports authentication using [service tokens](api/reference/service-tokens.md).

Example configuration:

```hcl
terraform {
  required_providers {
    planetscale = {
      source  = "planetscale/planetscale"
      version = "~> 1.3"
    }
  }
}

provider "planetscale" {
  # Credentials are read from environment variables by default:
  #   PLANETSCALE_SERVICE_TOKEN_ID
  #   PLANETSCALE_SERVICE_TOKEN
  #
  # You can also configure them explicitly (not recommended for production):
  # service_token_id = "..."
  # service_token    = "..."
}
```

| Provider Attribute | Environment Variable | Description |
| --- | --- | --- |
| `service_token_id` | `PLANETSCALE_SERVICE_TOKEN_ID` | PlanetScale Service Token ID |
| `service_token` | `PLANETSCALE_SERVICE_TOKEN` | PlanetScale Service Token |
| `server_url` | `PLANETSCALE_SERVER_URL` | Optional server URL override |

## Example usage

### Vitess branch and password

```hcl
resource "planetscale_vitess_branch" "app" {
  organization = "my-org"
  database     = "my-vitess-db"
  name         = "app-main"

  # Optional configuration:
  # cluster_size                  = "PS_10"
  # region                        = "us-east-1"   # Region for the branch
  # parent_branch                 = "main"        # Fork from an existing branch
  # backup_id                     = "..."         # Restore from a specific backup
  # safe_migrations               = true
  # vtgate_size                   = "VTG_320"
  # vtgate_count                  = 2
  # vtgate_autoscaling            = true
  # vtgate_max_count              = 8
  # vtgate_target_cpu_utilization = 50
}

resource "planetscale_vitess_branch_password" "app_rw" {
  organization = "my-org"
  database     = "my-vitess-db"
  branch       = planetscale_vitess_branch.app.name
  name         = "app-rw"
  role         = "admin"  # Options: reader, writer, readwriter, admin

  # Optional configuration:
  # replica       = false              # Connect to read replica
  # direct_vtgate = false              # Direct VTGate connection
  # ttl           = 86400              # Credentials expire after N seconds
  # cidrs         = ["10.0.0.0/8"]     # IP allowlist
}
```

### Vitess keyspace

Creating a `planetscale_vitess_branch` already provisions the branch’s default keyspace. Use `planetscale_vitess_keyspace` for additional keyspaces, or import the default keyspace to manage its size or replicas. `cluster_size` and `extra_replicas` update in place. Changing `planetscale_vitess_branch.cluster_size` still replaces the branch.

```hcl
resource "planetscale_vitess_keyspace" "metrics" {
  organization   = planetscale_vitess_branch.app.organization
  database       = planetscale_vitess_branch.app.database
  branch         = planetscale_vitess_branch.app.name
  name           = "metrics"
  cluster_size   = "PS_10"
  extra_replicas = 0
}
```

### Neki branch and role

```hcl
resource "planetscale_neki_branch" "main" {
  organization = "my-org"
  database     = "my-neki-db"
  name         = "main"

  # Optional configuration:
  # region             = "us-east-1"                # Region for the branch
  # parent_branch      = "main"                     # Parent branch; development branches start empty
  # backup_id          = "..."                      # Restore from a specific backup
  # restore_point      = "2024-01-01T00:00:00Z"     # Point-in-time recovery
  # deletion_protected = true
}

resource "planetscale_neki_role" "app" {
  organization = "my-org"
  database     = "my-neki-db"
  branch       = planetscale_neki_branch.main.name
  name         = "app"

  # Grant permissions by inheriting from built-in Postgres roles
  inherited_roles = [
    "pg_read_all_data",
    "pg_write_all_data",
  ]

  # Optional configuration:
  # ttl              = 86400        # Credentials expire after N seconds
  # successor        = "other-role" # Role to reassign ownership to before dropping
  # with_replication = false
}
```

**Immutable attributes:** Changing `region`, `parent_branch`, `backup_id`, or `restore_point` on a Neki branch will **destroy and recreate** the branch. Set `deletion_protected = true` or use `lifecycle { prevent_destroy = true }` to protect production branches from accidental replacement.

### Neki routers and cluster configuration

Creating a Neki branch already provisions a default configuration profile, shard, router group, admin, and sidecar. Import those objects if Terraform should manage their size or parameters. Use `planetscale_neki_configuration_profile`, `planetscale_neki_shard`, and `planetscale_neki_router` to add more.

Clients connect through an additional router group by appending the group name to the username, e.g. `user|analytics`. See [Connect to Neki](https://planetscale.com/docs/neki/connecting).

```hcl
resource "planetscale_neki_router" "analytics" {
  organization = planetscale_neki_branch.main.organization
  database     = planetscale_neki_branch.main.database
  branch       = planetscale_neki_branch.main.name
  name         = "analytics"

  # Optional configuration:
  # router_size              = "NKR_1"
  # replicas_per_cell        = 1
  # autoscaling              = true
  # max_replicas_per_cell    = 4
  # target_cpu_utilization   = 50
}
```

See [Neki cluster configuration](https://planetscale.com/docs/neki/cluster-configuration) for configuration profiles, shards, admin, routers, and sidecars.

### Postgres branch and role

```hcl
resource "planetscale_postgres_branch" "main" {
  organization = "my-org"
  database     = "my-postgres-db"
  name         = "main"

  # Optional configuration:
  # cluster_size  = "PS_10_AWS_ARM"
  # region        = "us-east-1"                # Region for the branch
  # parent_branch = "main"                     # Fork from an existing branch
  # major_version = "18"                       # PostgreSQL major version
  # backup_id     = "..."                      # Restore from a specific backup
  # restore_point = "2024-01-01T00:00:00Z"     # Point-in-time recovery

  # parameters = {
  #   pgconf = {
  #     max_connections = 30
  #     shared_preload_libraries = "pg_cron"
  #     "cron.max_running_jobs" = "2"
  #   }
  # }
}

resource "planetscale_postgres_branch_role" "app_role" {
  organization = "my-org"
  database     = "my-postgres-db"
  branch       = planetscale_postgres_branch.main.id
  name         = "app_role"

  # Grant permissions by inheriting from built-in Postgres roles
  inherited_roles = [
    "pg_read_all_data",
    "pg_write_all_data",
  ]

  # Optional configuration:
  # ttl       = 86400        # Credentials expire after N seconds
  # successor = "other-role" # Role to reassign ownership to before dropping
}
```

**Immutable attributes:** Changing `region`, `parent_branch`, `major_version`, `backup_id`, or `restore_point` will **destroy and recreate** the branch. Use `lifecycle { prevent_destroy = true }` to protect production branches from accidental replacement.

### Backups and backup policies

Use backup policy resources to define automatic backup schedules. Use branch backup resources to create managed backups. Vitess, Neki, and Postgres support backup policies. Vitess and Postgres also support branch backup resources. Postgres branch backups also support the `emergency` option.

```hcl
resource "planetscale_postgres_backup_policy" "daily" {
  organization    = "my-org"
  database        = "my-postgres-db"
  name            = "daily-production"
  target          = "production"
  frequency_unit  = "day"
  frequency_value = 1
  retention_unit  = "day"
  retention_value = 7
  schedule_time   = "03:00"
}

resource "planetscale_postgres_branch_backup" "before_release" {
  organization    = "my-org"
  database        = "my-postgres-db"
  branch          = planetscale_postgres_branch.main.name
  name            = "before-release"
  retention_unit  = "day"
  retention_value = 14
}
```

For Vitess databases, use `planetscale_vitess_backup_policy` and `planetscale_vitess_branch_backup` with the same scheduling and retention fields. For Neki databases, use `planetscale_neki_backup_policy` with the same scheduling and retention fields.

### Dedicated PgBouncers (Postgres)

Use `planetscale_postgres_bouncer` to manage a dedicated PgBouncer for a Postgres branch. Clients connect through it by appending the bouncer name to the username, e.g. `postgres.abc123|my-bouncer`.

```hcl
resource "planetscale_postgres_bouncer" "main" {
  organization = planetscale_postgres_branch.main.organization
  database     = planetscale_postgres_branch.main.database
  branch       = planetscale_postgres_branch.main.name

  name              = "my-bouncer"
  target            = "primary"       # primary, replica, or replica_az_affinity
  bouncer_size      = "PGB_5"         # Defaults to PGB_5
  replicas_per_cell = 1               # Defaults to 1

  parameters = {
    pgbouncer = {
      default_pool_size = "30"
    }
  }
}
```

Changing `name` or `target` destroys and recreates the bouncer. Omitted `parameters` values are reset to their defaults.

### Postgres read-only replicas

Use `planetscale_postgres_read_only_replica` to add a read-only replica in the same region as the primary or in a different region. The replica can use a different cluster size than the primary.

```hcl
resource "planetscale_postgres_read_only_replica" "analytics" {
  organization = planetscale_postgres_branch.main.organization
  database     = planetscale_postgres_branch.main.database
  branch       = planetscale_postgres_branch.main.name
  name         = "analytics"
  region       = "us-west"

  # Optional configuration:
  # cluster_size = "PS_10_AWS_ARM"  # Defaults to the primary's cluster size
  # replicas     = 1                # Instances serving reads
}
```

### Postgres parameters and extensions

Postgres branches support a `parameters` map for cluster settings, PgBouncer settings, Patroni settings, and supported extension settings. Parameters are nested by namespace: `pgconf`, `pgbouncer`, and `patroni`.

```hcl
resource "planetscale_postgres_branch" "configured" {
  organization = "my-org"
  database     = "my-postgres-db"
  name         = "configured"

  parameters = {
    pgconf = {
      max_connections = 30
      shared_preload_libraries = "pg_cron"
      "cron.max_running_jobs" = "2"
    }
  }
}
```

Omitted values are reset to their defaults.

Some Postgres parameter and extension changes require a restart. See [Postgres parameters](postgres/cluster-configuration/parameters.md) and [Postgres extensions](postgres/extensions.md) for details.

## Retrieving connection details

After creating a role or password, use Terraform outputs to retrieve connection information:

### Vitess

```hcl
output "vitess_connection" {
  description = "Vitess connection details"
  sensitive   = true
  value = {
    host     = planetscale_vitess_branch_password.app_rw.access_host_url
    username = planetscale_vitess_branch_password.app_rw.username
    password = planetscale_vitess_branch_password.app_rw.plain_text
  }
}
```

### Neki

```hcl
output "neki_connection" {
  description = "Neki connection details"
  sensitive   = true
  value = {
    host     = planetscale_neki_role.app.access_host_url
    username = planetscale_neki_role.app.username
    password = planetscale_neki_role.app.password
    database = "postgres"
  }
}
```

Generated Neki connections use the `postgres` logical database by default. See [Connect to Neki](https://planetscale.com/docs/neki/connecting).

### Postgres

```hcl
output "postgres_connection" {
  description = "Postgres connection details"
  sensitive   = true
  value = {
    host     = planetscale_postgres_branch_role.app_role.access_host_url
    username = planetscale_postgres_branch_role.app_role.username
    password = planetscale_postgres_branch_role.app_role.password
    database = planetscale_postgres_branch_role.app_role.database_name
  }
}
```

The `password` (Neki and Postgres) and `plain_text` (Vitess) fields are only available after the initial `terraform apply`. They are marked as sensitive and stored in Terraform state. To manage a Postgres role without storing its password in state, use `planetscale_postgres_redacted_branch_role` instead.

## Practical examples

### Development branch workflow

Create a development branch forked from your main branch. Optionally specify a larger cluster size if you need more resources than the default. Neki development branches start empty and do not copy the parent branch’s schema or data; restore a [backup](https://planetscale.com/docs/neki/backups) when you need existing data.

```hcl
resource "planetscale_postgres_branch" "feature_auth" {
  organization  = "my-org"
  database      = "my-db"
  name          = "feature-auth"
  parent_branch = "main"
  # cluster_size = "PS_10_AWS_ARM"  # Optional: specify a larger cluster size
}
```

### Read-only role for reporting

Create a role with read-only access for analytics and reporting. Neki uses the same `inherited_roles` pattern with `planetscale_neki_role`:

```hcl
resource "planetscale_postgres_branch_role" "reporting" {
  organization    = "my-org"
  database        = "my-db"
  branch          = "main"
  name            = "reporting"
  inherited_roles = ["pg_read_all_data"]  # Read-only access
}
```

### Short-lived CI/CD credentials

Create credentials that automatically expire for CI/CD pipelines:

```hcl
resource "planetscale_postgres_branch_role" "ci_runner" {
  organization    = "my-org"
  database        = "my-db"
  branch          = "main"
  name            = "ci-runner"
  inherited_roles = ["pg_read_all_data", "pg_write_all_data"]
  ttl             = 3600  # Expires in 1 hour
}
```

### Postgres role without a password in state

Use `planetscale_postgres_redacted_branch_role` when Terraform should manage the role but the password should live in your secret manager instead of Terraform state. After creating the role, reset its password through the [reset role API](api/reference/reset_role.md) and store the new credential in your secret manager.

```hcl
resource "planetscale_postgres_redacted_branch_role" "app" {
  organization    = "my-org"
  database        = "my-db"
  branch          = planetscale_postgres_branch.main.id
  name            = "app"
  inherited_roles = ["pg_read_all_data", "pg_write_all_data"]
}
```

## Configuration reference

| Attribute | Valid Values |
| --- | --- |
| `cluster_size` | Use the [Clusters API](api/reference/list_cluster_size_skus.md#list-available-cluster-sizes) to get the list of available cluster sizes |
| `region` | See [available regions](plans/regions.md) |
| `major_version` (Postgres) | `17`, `18` |
| `parameters` (Postgres) | Map of string values nested under `pgconf`, `pgbouncer`, or `patroni` |
| `parameters` (Neki) | Map of string values nested by namespace on configuration profiles, admin, and sidecars. Enable extensions in `pgconf.session_preload_libraries`. See [Neki parameters](https://planetscale.com/docs/neki/cluster-configuration/parameters). |
| `bouncer_size` (dedicated PgBouncer) | `PGB_5`, `PGB_10`, `PGB_20`, `PGB_40`, `PGB_80`, `PGB_160` |
| `target` (dedicated PgBouncer) | `primary`, `replica`, `replica_az_affinity` |
| `router_size` (Neki) | Use `pscale branch router sizes` or [Neki cluster sizing](https://planetscale.com/docs/neki/cluster-configuration/cluster-sizing) |
| `admin_size` (Neki) | `NKA_0`, `NKA_1`, `NKA_2`, `NKA_5`, `NKA_20`, `NKA_40` |
| `retention_unit` | `hour`, `day`, `week`, `month`, `year` |
| `frequency_unit` | `hour`, `day`, `week`, `month` |
| `target` (backup policies) | `production`, `development` |
| `role` (Vitess) | `reader`, `writer`, `readwriter`, `admin` |
| `inherited_roles` (Neki and Postgres) | `pg_read_all_data`, `pg_write_all_data`, or any custom role |
| `deletion_protected` (Neki) | `true`, `false` |

## Deletion protection

Branch deletion is one of the most sensitive operations in infrastructure automation. To reduce risk, use Terraform’s `lifecycle` block to prevent accidental destruction of critical resources. Neki branches also support a native `deletion_protected` attribute:

```hcl
resource "planetscale_vitess_branch" "production" {
  organization = "my-org"
  database     = "my-vitess-db"
  name         = "main"

  lifecycle {
    prevent_destroy = true
  }
}

resource "planetscale_neki_branch" "production" {
  organization         = "my-org"
  database             = "my-neki-db"
  name                 = "main"
  deletion_protected   = true

  lifecycle {
    prevent_destroy = true
  }
}
```

You should enforce your own review and approval processes around Terraform `apply`, particularly for changes that delete or recreate branches.

## Upgrading from v0.x to v1.x

The original [PlanetScale Terraform provider](https://registry.terraform.io/providers/planetscale/planetscale/0.6.1) was not officially supported for production use and is no longer maintained.

Because the new v1 provider is a **breaking rewrite**, migration from v0.x to v1.x is a one-time, manual transition. Projects using the v0 provider will continue to work as normal, but this version will not receive further updates.

1. **Pin v0.x in existing workloads**
	- Ensure all existing Terraform projects using the PlanetScale provider are pinned to `~> 0.6.1` (or a specific v0.x version) to avoid unintentional upgrades.
2. **Create a new Terraform project for v1.x**
	- Create a separate directory with a new configuration using the v1 provider.
		- Use the v1 resource types for Vitess, Neki, and Postgres branches, roles, passwords, and other supported resources.
		- Run `terraform init` to download the v1 provider and initialize your working directory. This creates a new, independent state file.
3. **Import existing resources into v1.x state**
	- For each resource that you want Terraform to manage going forward, run `terraform import` for the corresponding v1 resource.
		- Expect import to use IDs that align with the v1 API design (for example, IDs rather than purely name-based identifiers).
4. **Cut over automation**
	- After resources are imported and `terraform plan` shows no unexpected changes, switch your automation (CI pipelines, etc.) to apply the v1 project.
5. **Sunset v0.x usage**
	- Once you have validated v1.x in production, retire the v0.x configurations and keep them only for historical reference, if needed.

## Importing existing resources

Resources are imported using JSON-encoded identifiers:

```shellscript
# Import a Neki branch
terraform import planetscale_neki_branch.main \
  '{"organization": "my-org", "database": "my-db", "id": "branch-id"}'

# Import a Neki role
terraform import planetscale_neki_role.app \
  '{"organization": "my-org", "database": "my-db", "branch": "main", "id": "role-id"}'

# Import a Neki backup policy
terraform import planetscale_neki_backup_policy.daily \
  '{"organization": "my-org", "database": "my-db", "id": "backup-policy-id"}'

# Import a Neki configuration profile
terraform import planetscale_neki_configuration_profile.default \
  '{"organization": "my-org", "database": "my-db", "branch": "main", "name": "default"}'

# Import a Neki router group
terraform import planetscale_neki_router.analytics \
  '{"organization": "my-org", "database": "my-db", "branch": "main", "name": "analytics"}'

# Import a Neki shard
terraform import planetscale_neki_shard.extra \
  '{"organization": "my-org", "database": "my-db", "branch": "main", "id": "shard-id"}'

# Import a Neki admin
terraform import planetscale_neki_admin.main \
  '{"organization": "my-org", "database": "my-db", "branch": "main"}'

# Import a Neki sidecar
terraform import planetscale_neki_sidecar.default \
  '{"organization": "my-org", "database": "my-db", "branch": "main", "configuration_profile": "default"}'

# Import a Postgres branch
terraform import planetscale_postgres_branch.main \
  '{"organization": "my-org", "database": "my-db", "id": "branch-id"}'

# Import a Postgres backup policy
terraform import planetscale_postgres_backup_policy.daily \
  '{"organization": "my-org", "database": "my-db", "id": "backup-policy-id"}'

# Import a Postgres branch backup
terraform import planetscale_postgres_branch_backup.before_release \
  '{"organization": "my-org", "database": "my-db", "branch": "main", "id": "backup-id"}'

# Import a Postgres branch role
terraform import planetscale_postgres_branch_role.app \
  '{"organization": "my-org", "database": "my-db", "branch": "main", "id": "role-id"}'

# Import a dedicated PgBouncer
terraform import planetscale_postgres_bouncer.app \
  '{"organization": "my-org", "database": "my-db", "branch": "main", "name": "bouncer-name"}'

# Import a Postgres read-only replica
terraform import planetscale_postgres_read_only_replica.analytics \
  '{"organization": "my-org", "database": "my-db", "branch": "main", "name": "analytics"}'

# Import a Vitess branch
terraform import planetscale_vitess_branch.main \
  '{"organization": "my-org", "database": "my-db", "id": "branch-id"}'

# Import a Vitess backup policy
terraform import planetscale_vitess_backup_policy.daily \
  '{"organization": "my-org", "database": "my-db", "id": "backup-policy-id"}'

# Import a Vitess branch backup
terraform import planetscale_vitess_branch_backup.before_release \
  '{"organization": "my-org", "database": "my-db", "branch": "main", "id": "backup-id"}'

# Import a Vitess branch password
terraform import planetscale_vitess_branch_password.app \
  '{"organization": "my-org", "database": "my-db", "branch": "main", "id": "password-id"}'

# Import a Vitess keyspace
terraform import planetscale_vitess_keyspace.metrics \
  '{"organization": "my-org", "database": "my-db", "branch": "main", "name": "metrics"}'
```

You can find resource IDs in the PlanetScale dashboard URL or via the API.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
