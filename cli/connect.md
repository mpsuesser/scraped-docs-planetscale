---
url: https://planetscale.com/docs/cli/connect
title: "Connect"
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

## The connect command

This command creates a secure connection to a database branch for a local client. It is supported for **Vitess/MySQL databases only**; to connect to a Postgres database, use a [connection string](../postgres/connecting/quickstart.md) with a [role](../postgres/connecting/roles.md).

**Usage:**

```shellscript
pscale connect <DATABASE_NAME> <BRANCH_NAME> <FLAG>
```

If there is only one branch available on the database, you can leave off `<BRANCH_NAME>`.

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `--execute <COMMAND>` | Run the specified command after successfully connecting to the database. |
| `--execute-env-url <ENV_VAR_NAME>` | Environment variable name that contains the exposed Database URL. Default is `DATABASE_URL`. |
| `--execute-protocol <PROTOCOL>` | Protocol for the exposed URL (by default `DATABASE_URL`) value in execute. Default is `mysql2`. |
| `-h`, `--help` | Help with the `connect` command. |
| `--host <HOST>` | Local host to bind and listen for connections. Default is `127.0.0.1`. |
| `--org <ORG_NAME>` | The organization of the database you want to connect to. |
| `--port <PORT>` | Local port to bind and listen for connections. Default is `3306`. |
| `--remote-addr <ADDRESS>` | PlanetScale Database remote network address. By default, the remote address is automatically populated from the PlanetScale API. |
| `--role <ROLE>` | Define the access level [with a role](../vitess/security/password-roles.md) |

Available roles for the `--role` flag are:

- `reader`
- `writer`
- `readwriter`
- `admin`

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

### The connect command with --execute flag

**Command:**

```shellscript
pscale connect <DATABASE_NAME> <BRANCH_NAME> --execute 'node app.js'
```

**Output:**

This command connects to the specified PlanetScale branch and runs the `node app.js` command. Since no `--execute-env-url` flag was passed, it uses the default `DATABASE_URL` environment variable. You can find a full example of this in our [Node quickstart](../vitess/tutorials/connect-nodejs-app.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
