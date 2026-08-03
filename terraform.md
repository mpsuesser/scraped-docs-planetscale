---
url: https://planetscale.com/docs/terraform
title: "Terraform"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale Terraform provider

> A resource model for Vitess and Postgres databases, designed for Terraform and OpenTofu.

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

<PlatformAvailability current="both" />

Terraform is an open-source infrastructure-as-code tool that lets you define and manage cloud resources through declarative configuration files. With the [PlanetScale Terraform provider](https://github.com/planetscale/terraform-provider-planetscale), you can manage PlanetScale databases, branches, credentials, backups, backup policies, dedicated PgBouncers, and Postgres parameters and extensions alongside the rest of your infrastructure.

## Conceptual model

The PlanetScale Terraform provider is organized around a clear distinction between **Vitess** and **Postgres** resources and is focused on long-lived infrastructure objects.

* There are separate resources for each **database kind**—Vitess or Postgres.
* The provider maps cleanly onto PlanetScale's public API while exposing Terraform-friendly fields and lifecycle behavior.

PlanetScale databases do not have a dedicated Terraform resource. Their lifecycle is managed implicitly through branch resources:

* Applying a `planetscale_postgres_branch` or `planetscale_vitess_branch` creates the parent database if it does not already exist.
* Destroying the last branch in a database also destroys the database. See [Deletion protection](#deletion-protection) for safeguards.

### Credential models

**Vitess** and **Postgres** use different terminology for database credentials:

| Database Type | Resource                                    | How permissions work                                                                                                                                   |
| ------------- | ------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Vitess        | `planetscale_vitess_branch_password`        | Set the `role` attribute to `reader`, `writer`, `readwriter`, or `admin`                                                                               |
| Postgres      | `planetscale_postgres_branch_role`          | Use `inherited_roles` to inherit from [built-in Postgres roles](postgres/connecting/roles.md) like `pg_read_all_data` or `pg_write_all_data`.            |
| Postgres      | `planetscale_postgres_redacted_branch_role` | Same as `planetscale_postgres_branch_role`, but the password is not stored in Terraform state. Use this when you manage passwords in a secret manager. |

## Quick start

This complete example creates a Postgres branch with application credentials:

```hcl theme={null}
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

```bash theme={null}
export PLANETSCALE_SERVICE_TOKEN_ID="your-token-id"
export PLANETSCALE_SERVICE_TOKEN="your-token"
terraform init
terraform apply
terraform output -raw connection_string
```

## Available resources

The provider offers resources for the most common automation scenarios:

* `planetscale_postgres_branch`
* `planetscale_postgres_branch_backup`
* `planetscale_postgres_branch_role`
* `planetscale_postgres_redacted_branch_role`
* `planetscale_postgres_backup_policy`
* `planetscale_postgres_bouncer`
* `planetscale_vitess_branch`
* `planetscale_vitess_branch_backup`
* `planetscale_vitess_branch_password`
* `planetscale_vitess_backup_policy`

## Data sources

The provider also offers data sources for reading existing resources:

* `planetscale_databases`
* `planetscale_database_postgres`
* `planetscale_database_vitess`
* `planetscale_organization`
* `planetscale_organizations`
* `planetscale_postgres_backup_policies`
* `planetscale_postgres_backup_policy`
* `planetscale_postgres_bouncer`
* `planetscale_postgres_bouncers`
* `planetscale_postgres_branch`
* `planetscale_postgres_branch_backup`
* `planetscale_postgres_branch_backups`
* `planetscale_postgres_branch_role`
* `planetscale_postgres_branch_roles`
* `planetscale_postgres_redacted_branch_role`
* `planetscale_vitess_backup_policies`
* `planetscale_vitess_backup_policy`
* `planetscale_vitess_branch`
* `planetscale_vitess_branch_backup`
* `planetscale_vitess_branch_backups`
* `planetscale_vitess_branch_password`
* `planetscale_vitess_branch_passwords`

### Data source behavior

| Data Source                                 | Required Inputs                              | Notes                                                                                           |
| ------------------------------------------- | -------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| `planetscale_databases`                     | `organization`                               | Lists all databases. Optional `q` parameter for filtering by name.                              |
| `planetscale_database_postgres`             | `organization`                               | Returns Postgres database info. Does not accept a database name filter.                         |
| `planetscale_database_vitess`               | `organization`                               | Returns Vitess database info. Does not accept a database name filter.                           |
| `planetscale_postgres_backup_policies`      | `organization`, `database`                   | Lists Postgres backup policies for a database.                                                  |
| `planetscale_postgres_backup_policy`        | `organization`, `database`, `id`             | Fetches a specific Postgres backup policy by ID.                                                |
| `planetscale_postgres_bouncer`              | `organization`, `database`, `branch`, `name` | Fetches a specific dedicated PgBouncer by name.                                                 |
| `planetscale_postgres_bouncers`             | `organization`, `database`, `branch`         | Lists dedicated PgBouncers for a branch.                                                        |
| `planetscale_postgres_branch`               | `organization`, `database`                   | Returns the default branch. Does not accept a branch name parameter.                            |
| `planetscale_postgres_branch_backups`       | `organization`, `database`, `branch`         | Lists Postgres backups for a branch. Supports filters like `state`, `from`, `to`, and `policy`. |
| `planetscale_postgres_branch_backup`        | `organization`, `database`, `branch`, `id`   | Fetches a specific Postgres backup by ID.                                                       |
| `planetscale_postgres_branch_roles`         | `organization`, `database`, `branch`         | Lists all roles on a branch.                                                                    |
| `planetscale_postgres_branch_role`          | `organization`, `database`, `branch`, `id`   | Fetches a specific role by ID. Useful for importing existing roles.                             |
| `planetscale_postgres_redacted_branch_role` | `organization`, `database`, `branch`, `id`   | Fetches a specific role by ID without reading the password into state.                          |
| `planetscale_vitess_backup_policies`        | `organization`, `database`                   | Lists Vitess backup policies for a database.                                                    |
| `planetscale_vitess_backup_policy`          | `organization`, `database`, `id`             | Fetches a specific Vitess backup policy by ID.                                                  |
| `planetscale_vitess_branch`                 | `organization`, `database`                   | Returns the default branch. Does not accept a branch name parameter.                            |
| `planetscale_vitess_branch_backups`         | `organization`, `database`, `branch`         | Lists Vitess backups for a branch. Supports filters like `state`, `from`, `to`, and `policy`.   |
| `planetscale_vitess_branch_backup`          | `organization`, `database`, `branch`, `id`   | Fetches a specific Vitess backup by ID.                                                         |
| `planetscale_vitess_branch_passwords`       | `organization`, `database`, `branch`         | Lists all passwords on a branch.                                                                |
| `planetscale_vitess_branch_password`        | `organization`, `database`, `branch`, `id`   | Fetches a specific password by ID.                                                              |
| `planetscale_organization`                  | `organization`                               | Returns organization details.                                                                   |
| `planetscale_organizations`                 | (none)                                       | Lists all accessible organizations.                                                             |

Refer to the [Terraform Registry and provider documentation](https://registry.terraform.io/providers/planetscale/planetscale/latest) for the full, up-to-date list of available resources and data sources.

## Provider configuration

The provider supports authentication using [service tokens](api/reference/service-tokens.md).

Example configuration:

```hcl theme={null}
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

| Provider Attribute | Environment Variable           | Description                  |
| ------------------ | ------------------------------ | ---------------------------- |
| `service_token_id` | `PLANETSCALE_SERVICE_TOKEN_ID` | PlanetScale Service Token ID |
| `service_token`    | `PLANETSCALE_SERVICE_TOKEN`    | PlanetScale Service Token    |
| `server_url`       | `PLANETSCALE_SERVER_URL`       | Optional server URL override |

## Example usage

### Vitess branch and password

```hcl theme={null}
resource "planetscale_vitess_branch" "app" {
  organization = "my-org"
  database     = "my-vitess-db"
  name         = "app-main"

  # Optional configuration:
  # cluster_size  = "PS_10"
  # region        = "us-east-1"   # Region for the branch
  # parent_branch = "main"        # Fork from an existing branch
  # backup_id     = "..."         # Restore from a specific backup
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

### Postgres branch and role

```hcl theme={null}
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
  # restore_point = "2024-01-01T00:00:00Z"     # Point-in-time recovery (Postgres only)

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

<Warning>
  **Immutable attributes:** Changing `region`, `parent_branch`, `major_version`, `backup_id`, or `restore_point` will **destroy and recreate** the branch. Use `lifecycle { prevent_destroy = true }` to protect production branches from accidental replacement.
</Warning>

### Backups and backup policies

Use backup policy resources to define automatic backup schedules. Use branch backup resources to create managed backups. Both Vitess and Postgres support backup policies and branch backups. Postgres branch backups also support the `emergency` option.

```hcl theme={null}
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

For Vitess databases, use `planetscale_vitess_backup_policy` and `planetscale_vitess_branch_backup` with the same scheduling and retention fields.

### Dedicated PgBouncers (Postgres)

Use `planetscale_postgres_bouncer` to manage a dedicated PgBouncer for a Postgres branch. Clients connect through it by appending the bouncer name to the username, e.g. `postgres.abc123|my-bouncer`.

```hcl theme={null}
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

<Warning>
  Changing `name` or `target` destroys and recreates the bouncer. Omitted `parameters` values are reset to their defaults.
</Warning>

### Postgres parameters and extensions

Postgres branches support a `parameters` map for cluster settings, PgBouncer settings, Patroni settings, and supported extension settings. Parameters are nested by namespace: `pgconf`, `pgbouncer`, and `patroni`.

```hcl theme={null}
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

<Warning>
  Omitted values are reset to their defaults.
</Warning>

<Warning>
  Some Postgres parameter and extension changes require a restart. See [Postgres parameters](postgres/cluster-configuration/parameters.md) and [Postgres extensions](postgres/extensions.md) for details.
</Warning>

## Retrieving connection details

After creating a role or password, use Terraform outputs to retrieve connection information:

### Postgres

```hcl theme={null}
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

### Vitess

```hcl theme={null}
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

<Note>
  The `password` (Postgres) and `plain_text` (Vitess) fields are only available after the initial `terraform apply`. They are marked as sensitive and stored in Terraform state. To manage a Postgres role without storing its password in state, use `planetscale_postgres_redacted_branch_role` instead.
</Note>

## Practical examples

### Development branch workflow

Create a development branch forked from your main branch. Optionally specify a larger cluster size if you need more resources than the default:

```hcl theme={null}
resource "planetscale_postgres_branch" "feature_auth" {
  organization  = "my-org"
  database      = "my-db"
  name          = "feature-auth"
  parent_branch = "main"
  # cluster_size = "PS_10_AWS_ARM"  # Optional: specify a larger cluster size
}
```

### Read-only role for reporting

Create a role with read-only access for analytics and reporting:

```hcl theme={null}
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

```hcl theme={null}
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

```hcl theme={null}
resource "planetscale_postgres_redacted_branch_role" "app" {
  organization    = "my-org"
  database        = "my-db"
  branch          = planetscale_postgres_branch.main.id
  name            = "app"
  inherited_roles = ["pg_read_all_data", "pg_write_all_data"]
}
```

## Configuration reference

| Attribute                            | Valid Values                                                                                                                          |
| ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------- |
| `cluster_size`                       | Use the [Clusters API](api/reference/list_cluster_size_skus.md#list-available-cluster-sizes) to get the list of available cluster sizes |
| `region`                             | See [available regions](https://planetscale.com/docs/vitess/regions)                                                                                              |
| `major_version` (Postgres)           | `17`, `18`                                                                                                                            |
| `parameters` (Postgres)              | Map of string values nested under `pgconf`, `pgbouncer`, or `patroni`                                                                 |
| `bouncer_size` (dedicated PgBouncer) | `PGB_5`, `PGB_10`, `PGB_20`, `PGB_40`, `PGB_80`, `PGB_160`                                                                            |
| `target` (dedicated PgBouncer)       | `primary`, `replica`, `replica_az_affinity`                                                                                           |
| `retention_unit`                     | `hour`, `day`, `week`, `month`, `year`                                                                                                |
| `frequency_unit`                     | `hour`, `day`, `week`, `month`                                                                                                        |
| `target` (backup policies)           | `production`, `development`                                                                                                           |
| `role` (Vitess)                      | `reader`, `writer`, `readwriter`, `admin`                                                                                             |
| `inherited_roles` (Postgres)         | `pg_read_all_data`, `pg_write_all_data`, or any custom role                                                                           |

## Deletion protection

Branch deletion is one of the most sensitive operations in infrastructure automation. To reduce risk, use Terraform's `lifecycle` block to prevent accidental destruction of critical resources:

```hcl theme={null}
resource "planetscale_vitess_branch" "production" {
  organization = "my-org"
  database     = "my-vitess-db"
  name         = "main"

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
   * Ensure all existing Terraform projects using the PlanetScale provider are pinned to `~> 0.6.1` (or a specific v0.x version) to avoid unintentional upgrades.
2. **Create a new Terraform project for v1.x**
   * Create a separate directory with a new configuration using the v1 provider.
   * Use the v1 resource types for Vitess/Postgres branches, roles, passwords, and other supported resources.
   * Run `terraform init` to download the v1 provider and initialize your working directory. This creates a new, independent state file.
3. **Import existing resources into v1.x state**
   * For each resource that you want Terraform to manage going forward, run `terraform import` for the corresponding v1 resource.
   * Expect import to use IDs that align with the v1 API design (for example, IDs rather than purely name-based identifiers).
4. **Cut over automation**
   * After resources are imported and `terraform plan` shows no unexpected changes, switch your automation (CI pipelines, etc.) to apply the v1 project.
5. **Sunset v0.x usage**
   * Once you have validated v1.x in production, retire the v0.x configurations and keep them only for historical reference, if needed.

## Importing existing resources

Resources are imported using JSON-encoded identifiers:

```bash theme={null}
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
```

You can find resource IDs in the PlanetScale dashboard URL or via the API.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
