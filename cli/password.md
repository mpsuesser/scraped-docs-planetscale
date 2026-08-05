---
url: https://planetscale.com/docs/cli/password
title: "Password"
description: ""
access_date: 2026-08-05T19:12:39.041Z
current_date: 2026-08-05T19:12:39.041Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## Postgres

Create **role credentials** with [`pscale role`](role.md), then connect using a [connection string](../postgres/connecting/quickstart.md).

`pscale connect` is not supported.

## Vitess / MySQL

Create a **branch password** with [`pscale password`](password.md), then connect with a [connection string](../vitess/connecting/connection-strings.md) or [`pscale connect`](connect.md).

## The password command

Manage database passwords for a Vitess database branch. This command is only supported for Vitess databases. To manage a role for Postgres, see the [role command](role.md).

**Usage:**

```shellscript
pscale password <SUB-COMMAND> <FLAG>
```

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Product** | **Description** |
| --- | --- | --- | --- |
| `create <DATABASE_NAME> <BRANCH_NAME> <PASSWORD_NAME>` | `--ttl`, `--role`, `--replica`, `--read-only-region` | Vitess | Create new credentials to access a branch’s data |
| `delete <DATABASE_NAME> <BRANCH_NAME> <PASSWORD_ID>` | `--force` | Vitess | Delete the specified branch credentials |
| `list <DATABASE_NAME> <BRANCH_NAME>` | `--web` | Vitess | List all credentials of a database |

### Service token automation: password

Legend: ✅ supported · Vitess only. All sub-commands require `--org` or `PLANETSCALE_ORG`.

| Sub-command | Env-var auth | `--service-token` flag | Requires `--org` | Postgres / Vitess | `--format json` | API equivalent |
| --- | --- | --- | --- | --- | --- | --- |
| `list <database> <branch>` | ✅ | ✅ | ✅ | Vitess | ✅ | `pscale api organizations/<org>/databases/<database>/branches/<branch>/passwords --format json` |
| `create` | ✅ | ✅ | ✅ | Vitess | ✅ | `POST pscale api organizations/<org>/databases/<database>/branches/<branch>/passwords --format json` |
| `delete` | ✅ | ✅ | ✅ | Vitess | ✅ | `DELETE pscale api organizations/<org>/databases/<database>/branches/<branch>/passwords/<id> --format json` |

```shellscript
export PLANETSCALE_ORG="<org>"
pscale password list <database> <branch> --format json
```

The value `<PASSWORD_ID>` represents the ID number of the set of credentials. To find all available credentials and their IDs, run `pscale list <DATABASE_NAME> <BRANCH_NAME>`.

#### Sub-command flag descriptions

Some of the sub-commands have additional flags unique to the sub-command. This section covers what each of those does. See the above table for which context.

| **Sub-command flag** | **Description** | **Applicable sub-commands** |
| --- | --- | --- |
| `--ttl` | TTL defines the time to live for the password. Durations such as `30m` or `24h`, or bare seconds like `3600`, are accepted. Default is `0` (never expires). | `create` |
| `--role <ROLE>` | Add a [role to a password](../vitess/security/password-roles.md). Defaults to `reader` for `--replica` and `--read-only-region`, otherwise `admin`. | `create` |
| `--replica` | Route reads to the branch’s primary-region replicas and all [read-only regions](../vitess/scaling/read-only-regions.md) (nearest replica). Cannot be combined with `--read-only-region`. | `create` |
| `--read-only-region <REGION>` | Scope the password to a Vitess [read-only region](../vitess/scaling/read-only-regions.md). Accepts a region slug, display name, or id. List regions with `pscale keyspace read-only-regions`. Cannot be combined with `--replica`. | `create` |
| `--force` | Delete a password without confirmation. | `delete` |
| `--web` | Perform the action in your web browser | `list` |

Available roles for the `--role` flag are:

- `reader`
- `writer`
- `readwriter`
- `admin`

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for `password` command |
| `--org <ORGANIZATION_NAME>` | The organization for the current user |

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

### Create a read-only region password

Create credentials that always connect to a specific Vitess read-only region:

```shellscript
pscale password create <DATABASE_NAME> <BRANCH_NAME> <PASSWORD_NAME> --read-only-region eu-west
```

List configured regions for a keyspace first:

```shellscript
pscale keyspace read-only-regions <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>
```

### The delete sub-command

Delete a password from a database branch:

**Command:**

```shellscript
pscale password delete <DATABASE_NAME> <BRANCH_NAME> <PASSWORD_ID>
```

**Output:**

```shellscript
? Please type <DATABASE_NAME>/<BRANCH_NAME>/<PASSWORD_ID> to confirm:
Password <PASSWORD_ID> was successfully deleted from <BRANCH_NAME>.
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
