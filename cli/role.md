---
url: https://planetscale.com/docs/cli/role
title: "Role"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale CLI commands: role

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

<PlatformAvailability current="postgres" />

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you've installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

<Columns cols={2}>
  <Card title="Postgres">
    Create **role credentials** with [`pscale role`](role.md), then connect using a [connection string](../postgres/connecting/quickstart.md).

    `pscale connect` is not supported.
  </Card>

  <Card title="Vitess / MySQL">
    Create a **branch password** with [`pscale password`](password.md), then connect with a [connection string](../vitess/connecting/connection-strings.md) or [`pscale connect`](connect.md).
  </Card>
</Columns>

## The `role` command

Manage database roles for a Postgres database branch. This command is only supported for Postgres databases.

**Usage:**

```bash theme={null}
pscale role [command]
```

### Available sub-commands

| **Sub-Command** | **Product** | **Description**                                       |
| :-------------- | :---------- | :---------------------------------------------------- |
| `create`        | Postgres    | Create a new role for a Postgres database branch      |
| `delete`        | Postgres    | Delete a role                                         |
| `get`           | Postgres    | Retrieve information about a specific role            |
| `list`          | Postgres    | List all roles for a Postgres database branch         |
| `reassign`      | Postgres    | Reassign objects owned by a role to another role      |
| `renew`         | Postgres    | Renew a role's expiration                             |
| `reset`         | Postgres    | Reset a role's password                               |
| `reset-default` | Postgres    | Reset the credentials for the default `postgres` role |
| `update`        | Postgres    | Update a role's name                                  |

### Service token automation: `role`

Legend: ✅ supported · Postgres only. All sub-commands require `--org` or `PLANETSCALE_ORG`.

| Sub-command                                        | Env-var auth | `--service-token` flag | Requires `--org` | Postgres / Vitess | `--format json` | API equivalent                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| :------------------------------------------------- | :----------: | :--------------------: | :--------------: | :---------------- | :-------------: | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `list <database> <branch>`                         |       ✅      |            ✅           |         ✅        | Postgres          |        ✅        | `pscale api organizations/<org>/databases/<database>/branches/<branch>/roles --format json`                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `get <database> <branch> <id>`                     |       ✅      |            ✅           |         ✅        | Postgres          |        ✅        | `pscale api organizations/<org>/databases/<database>/branches/<branch>/roles/<id> --format json`                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `create` / `delete` / `update` / `renew` / `reset` |       ✅      |            ✅           |         ✅        | Postgres          |        ✅        | `POST pscale api organizations/<org>/databases/<database>/branches/<branch>/roles --format json` · `DELETE pscale api organizations/<org>/databases/<database>/branches/<branch>/roles/<id> --format json` · `PATCH pscale api organizations/<org>/databases/<database>/branches/<branch>/roles/<id> --format json` · `POST pscale api organizations/<org>/databases/<database>/branches/<branch>/roles/<id>/renew --format json` · `POST pscale api organizations/<org>/databases/<database>/branches/<branch>/roles/<id>/reset --format json` |

```bash theme={null}
export PLANETSCALE_ORG="<org>"
pscale role list <database> <branch> --format json
```

Setup and commands to avoid: [CLI overview](../cli.md) · [Service tokens](../api/service-tokens.md#service-token-automation)

### Available flags

| **Flag**       | **Description**                       |
| :------------- | :------------------------------------ |
| `-h`, `--help` | View help for `role` command          |
| `--org string` | The organization for the current user |

### Global flags

| **Command**                     | **Description**                                                                      |
| :------------------------------ | :----------------------------------------------------------------------------------- |
| `--api-token <TOKEN>`           | The API token to use for authenticating against the PlanetScale API.                 |
| `--api-url <URL>`               | The base URL for the PlanetScale API. Default is `https://api.planetscale.com/`.     |
| `--config <CONFIG_FILE>`        | Config file. Default is `$HOME/.config/planetscale/pscale.yml`.                      |
| `--debug`                       | Enable debug mode.                                                                   |
| `-f`, `--format <FORMAT>`       | Show output in a specific format. Possible values: `human` (default), `json`, `csv`. |
| `--no-color`                    | Disable color output.                                                                |
| `--service-token <TOKEN>`       | The service token for authenticating.                                                |
| `--service-token-id <TOKEN_ID>` | The service token ID for authenticating.                                             |

## Examples

### The `create` sub-command

Create a new role for a Postgres database branch:

**Usage:**

```bash theme={null}
pscale role create <database> <branch> <name> [flags]
```

**Available flags:**

* `--inherited-roles string` - Comma-separated list of role names to inherit privileges from. Common values are 'pg\_read\_all\_data' for read access, 'pg\_write\_all\_data' for write access, and 'postgres' for admin access.
* `--ttl duration` - TTL defines the time to live for the role. Durations such as "30m", "24h", or bare integers such as "3600" (seconds) are accepted. The default TTL is 0s, which means the role will never expire.

**Example:**

```bash theme={null}
pscale role create my-database main api-user --inherited-roles pg_read_all_data --ttl 24h
```

### The `delete` sub-command

Delete a role:

**Usage:**

```bash theme={null}
pscale role delete <database> <branch> <role-id> [flags]
```

**Available flags:**

* `--force` - Delete a role without confirmation
* `--successor string` - Role to transfer ownership to before deletion. Usually 'postgres'.

**Aliases:** `delete`, `rm`

**Example:**

```bash theme={null}
pscale role delete my-database main role-123 --successor postgres
```

### The `get` sub-command

Retrieve information about a specific role:

**Usage:**

```bash theme={null}
pscale role get <database> <branch> <role-id> [flags]
```

**Example:**

```bash theme={null}
pscale role get my-database main role-123
```

### The `list` sub-command

List all roles for a Postgres database branch:

**Usage:**

```bash theme={null}
pscale role list <database> <branch> [flags]
```

**Available flags:**

* `-w`, `--web` - List roles in your web browser.

**Aliases:** `list`, `ls`

**Example:**

```bash theme={null}
pscale role list my-database main
```

### The `reassign` sub-command

Reassign objects owned by one role to any other role:

<Warning>
  Be careful with this command. Reassigning objects like databases, tables, or schemas will change who is able to write to them, alter them, or delete them.
</Warning>

**Usage:**

```bash theme={null}
pscale role reassign <database> <branch> <role-id> --successor <role-id>
```

**Available flags:**

* `--force` - Force reset without confirmation

**Example:**

```bash theme={null}
pscale role reassign my-database main role-123 --successor postgres
```

### The `renew` sub-command

Renew a role's expiration:

**Usage:**

```bash theme={null}
pscale role renew <database> <branch> <role-id> [flags]
```

**Example:**

```bash theme={null}
pscale role renew my-database main role-123
```

### The `reset` sub-command

Reset the credentials for any API-created role:

<Warning>
  Be careful with this command. If you are currently using the affected role's credentials for connecting to your database, running this command will reset the password, and new connections using the old password will not work.
</Warning>

**Usage:**

```bash theme={null}
pscale role reset <database> <branch> <role-id> [flags]
```

**Available flags:**

* `--force` - Force reset without confirmation

**Example:**

```bash theme={null}
pscale role reset my-database main role-123
```

### The `reset-default` sub-command

Reset the credentials for the default `postgres` role:

<Warning>
  Be careful with this command. If you are currently using the default `postgres` role credentials for connecting to your database, running this command will reset the password, and new connections using the old password will not work.
</Warning>

**Usage:**

```bash theme={null}
pscale role reset-default <database> <branch> [flags]
```

**Available flags:**

* `--force` - Force reset without confirmation

**Example:**

```bash theme={null}
pscale role reset-default my-database main
```

### The `update` sub-command

Update a role's name:

**Usage:**

```bash theme={null}
pscale role update <database> <branch> <role-id> [flags]
```

**Available flags:**

* `--name string` - New name for the role

**Example:**

```bash theme={null}
pscale role update my-database main role-123 --name new-role-name
```

## Related documentation

<CardGroup>
  <Card title="Managing Postgres roles" href="/docs/postgres/connecting/roles" icon="angles-right" horizontal />

  <Card title="Postgres roles API documentation" href="/docs/api/reference/list_roles" icon="angles-right" horizontal />
</CardGroup>

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
