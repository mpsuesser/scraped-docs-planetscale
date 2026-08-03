---
url: https://planetscale.com/docs/cli/role
title: "Role"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## Postgres

Create **role credentials** with [`pscale role`](role.md), then connect using a [connection string](../postgres/connecting/quickstart.md).

`pscale connect` is not supported.

## Vitess / MySQL

Create a **branch password** with [`pscale password`](password.md), then connect with a [connection string](../vitess/connecting/connection-strings.md) or [`pscale connect`](connect.md).

## The role command

Manage database roles for a Postgres database branch. This command is only supported for Postgres databases.

**Usage:**

```shellscript
pscale role [command]
```

### Available sub-commands

| **Sub-Command** | **Product** | **Description** |
| --- | --- | --- |
| `create` | Postgres | Create a new role for a Postgres database branch |
| `delete` | Postgres | Delete a role |
| `get` | Postgres | Retrieve information about a specific role |
| `list` | Postgres | List all roles for a Postgres database branch |
| `reassign` | Postgres | Reassign objects owned by a role to another role |
| `renew` | Postgres | Renew a role’s expiration |
| `reset` | Postgres | Reset a role’s password |
| `reset-default` | Postgres | Reset the credentials for the default `postgres` role |
| `update` | Postgres | Update a role’s name |

### Service token automation: role

Legend: ✅ supported · Postgres only. All sub-commands require `--org` or `PLANETSCALE_ORG`.

| Sub-command | Env-var auth | `--service-token` flag | Requires `--org` | Postgres / Vitess | `--format json` | API equivalent |
| --- | --- | --- | --- | --- | --- | --- |
| `list <database> <branch>` | ✅ | ✅ | ✅ | Postgres | ✅ | `pscale api organizations/<org>/databases/<database>/branches/<branch>/roles --format json` |
| `get <database> <branch> <id>` | ✅ | ✅ | ✅ | Postgres | ✅ | `pscale api organizations/<org>/databases/<database>/branches/<branch>/roles/<id> --format json` |
| `create` / `delete` / `update` / `renew` / `reset` | ✅ | ✅ | ✅ | Postgres | ✅ | `POST pscale api organizations/<org>/databases/<database>/branches/<branch>/roles --format json` · `DELETE pscale api organizations/<org>/databases/<database>/branches/<branch>/roles/<id> --format json` · `PATCH pscale api organizations/<org>/databases/<database>/branches/<branch>/roles/<id> --format json` · `POST pscale api organizations/<org>/databases/<database>/branches/<branch>/roles/<id>/renew --format json` · `POST pscale api organizations/<org>/databases/<database>/branches/<branch>/roles/<id>/reset --format json` |

```shellscript
export PLANETSCALE_ORG="<org>"
pscale role list <database> <branch> --format json
```

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for `role` command |
| `--org string` | The organization for the current user |

### Global flags

| **Command** | **Description** |
| --- | --- |
| `--api-token <TOKEN>` | The API token to use for authenticating against the PlanetScale API. |
| `--api-url <URL>` | The base URL for the PlanetScale API. Default is `https://api.planetscale.com/`. |
| `--config <CONFIG_FILE>` | Config file. Default is `$HOME/.config/planetscale/pscale.yml`. |
| `--debug` | Enable debug mode. |
| `-f`, `--format <FORMAT>` | Show output in a specific format. Possible values: `human` (default), `json`, `csv`. |
| `--no-color` | Disable color output. |
| `--service-token <TOKEN>` | The service token for authenticating. |
| `--service-token-id <TOKEN_ID>` | The service token ID for authenticating. |

## Examples

### The create sub-command

Create a new role for a Postgres database branch:

**Usage:**

```shellscript
pscale role create <database> <branch> <name> [flags]
```

**Available flags:**

- `--inherited-roles string` - Comma-separated list of role names to inherit privileges from. Common values are ‘pg\_read\_all\_data’ for read access, ‘pg\_write\_all\_data’ for write access, and ‘postgres’ for admin access.
- `--ttl duration` - TTL defines the time to live for the role. Durations such as “30m”, “24h”, or bare integers such as “3600” (seconds) are accepted. The default TTL is 0s, which means the role will never expire.

**Example:**

```shellscript
pscale role create my-database main api-user --inherited-roles pg_read_all_data --ttl 24h
```

### The delete sub-command

Delete a role:

**Usage:**

```shellscript
pscale role delete <database> <branch> <role-id> [flags]
```

**Available flags:**

- `--force` - Delete a role without confirmation
- `--successor string` - Role to transfer ownership to before deletion. Usually ‘postgres’.

**Aliases:** `delete`, `rm`

**Example:**

```shellscript
pscale role delete my-database main role-123 --successor postgres
```

### The get sub-command

Retrieve information about a specific role:

**Usage:**

```shellscript
pscale role get <database> <branch> <role-id> [flags]
```

**Example:**

```shellscript
pscale role get my-database main role-123
```

### The list sub-command

List all roles for a Postgres database branch:

**Usage:**

```shellscript
pscale role list <database> <branch> [flags]
```

**Available flags:**

- `-w`, `--web` - List roles in your web browser.

**Aliases:** `list`, `ls`

**Example:**

```shellscript
pscale role list my-database main
```

### The reassign sub-command

Reassign objects owned by one role to any other role:

Be careful with this command. Reassigning objects like databases, tables, or schemas will change who is able to write to them, alter them, or delete them.

**Usage:**

```shellscript
pscale role reassign <database> <branch> <role-id> --successor <role-id>
```

**Available flags:**

- `--force` - Force reset without confirmation

**Example:**

```shellscript
pscale role reassign my-database main role-123 --successor postgres
```

### The renew sub-command

Renew a role’s expiration:

**Usage:**

```shellscript
pscale role renew <database> <branch> <role-id> [flags]
```

**Example:**

```shellscript
pscale role renew my-database main role-123
```

### The reset sub-command

Reset the credentials for any API-created role:

Be careful with this command. If you are currently using the affected role’s credentials for connecting to your database, running this command will reset the password, and new connections using the old password will not work.

**Usage:**

```shellscript
pscale role reset <database> <branch> <role-id> [flags]
```

**Available flags:**

- `--force` - Force reset without confirmation

**Example:**

```shellscript
pscale role reset my-database main role-123
```

### The reset-default sub-command

Reset the credentials for the default `postgres` role:

Be careful with this command. If you are currently using the default `postgres` role credentials for connecting to your database, running this command will reset the password, and new connections using the old password will not work.

**Usage:**

```shellscript
pscale role reset-default <database> <branch> [flags]
```

**Available flags:**

- `--force` - Force reset without confirmation

**Example:**

```shellscript
pscale role reset-default my-database main
```

### The update sub-command

Update a role’s name:

**Usage:**

```shellscript
pscale role update <database> <branch> <role-id> [flags]
```

**Available flags:**

- `--name string` - New name for the role

**Example:**

```shellscript
pscale role update my-database main role-123 --name new-role-name
```

## Related documentation

## Managing Postgres roles

## Postgres roles API documentation

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
