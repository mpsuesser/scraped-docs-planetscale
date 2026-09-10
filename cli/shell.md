---
url: https://planetscale.com/docs/cli/shell
title: "Shell"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The shell command

This command opens a secure shell instance to your database so that you can manipulate it from the command line.

For Vitess databases, it uses the MySQL command-line client (`mysql`). For Postgres and Neki databases, it uses the PostgreSQL command-line client (`psql`). The appropriate client [must be installed](planetscale-environment-setup.md) prior to use.

If your Managed cluster has public connectivity disabled, you must connect over PrivateLink. Set up the VPC endpoint using the service name we provide, make sure your DNS resolves your PlanetScale hostnames to that PrivateLink endpoint, and then run `pscale shell mydatabase mybranch`.

If you use a VPN such as Tailscale, do not override `psdb.cloud` DNS in your VPN configuration. Split‑DNS misconfiguration can point hostnames at the wrong place and break shell connectivity.

**Usage:**

```shellscript
pscale shell <DATABASE_NAME> [BRANCH_NAME] [DB_NAME] <FLAG>
```

By default, if no branch names are given and there is only one branch, it automatically opens a shell to that branch. If there are multiple branches for the given database, you’ll be prompted to choose one.

### Available flags

| **Flag** | **Description** |  |
| --- | --- | --- |
| `-h`, `--help` | Help for shell command |  |
| `--local-addr <ADDRESS>` | Local address where the Vitess proxy binds and listens. By default it binds to `127.0.0.1` with a random port. Not supported for Postgres or Neki. |  |
| `--org <ORGANIZATION_NAME>` | The organization for the current user |  |
| `--remote-addr <ADDRESS>` | PlanetScale Database remote network address. By default the remote address is populated automatically from the PlanetScale API |  |
| `--replica` | Connect to a replica. The CLI adds the \` | replica `username suffix for Vitess and Postgres. For Neki, it instead sets` PGOPTIONS `to` -c \_\_neki.target=REPLICA\`. |
| `--role <ROLE>` | Role defines the access level, allowed values are: reader, writer, readwriter, admin. Defaults to ‘reader’ for replica passwords, otherwise defaults to ‘admin’ |  |
| `--router <ROUTER>` | Connect through the named router group. Neki only. |  |
| `--db-name <NAME>` | PostgreSQL database name to connect to (default `postgres`). Also accepted as a third positional argument. Postgres and Neki only. |  |

Available roles for the `--role` flag are:

- `reader` - Read-only access
- `writer` - Write-only access
- `readwriter` - Read and write access
- `admin` - Full administrative access

For replica connections (`--replica` flag), the default role is `reader`. For regular connections, the default role is `admin`.

### Global flags

| **Command** | **Description** |
| --- | --- |
| `--api-token <TOKEN>` | The API token to use for authenticating against the PlanetScale API |
| `--api-url <URL>` | The base URL for the PlanetScale API (default `https://api.planetscale.com/`) |
| `--config <CONFIG_FILE>` | Config file (default is `$HOME/.config/planetscale/pscale.yml`) |
| `--debug` | Enable debug mode |
| `-f`, `--format <FORMAT>` | Show output in a specific format. Possible values: `human` (default), `json`, `csv` |
| `--no-color` | Disable color output |
| `--service-token <TOKEN>` | Service Token for authenticating |
| `--service-token-id <TOKEN_ID>` | The Service Token ID for authenticating |

## Examples

### Basic shell usage

**Open a shell to a database (auto-selects branch if only one exists):**

```shellscript
pscale shell mydatabase
```

**Open a shell to a specific branch:**

```shellscript
pscale shell mydatabase mybranch
```

**Open a Postgres or Neki shell to a specific database name:**

```shellscript
pscale shell <DATABASE_NAME> <BRANCH_NAME> --db-name <DB_NAME>
pscale shell <DATABASE_NAME> <BRANCH_NAME> <DB_NAME>
```

Once the shell is opened, you can run SQL as expected.

**Example MySQL session:**

```shellscript
DATABASE_NAME/BRANCH_NAME >
DATABASE_NAME/BRANCH_NAME > show tables;
+---------------+
| Tables_in_db |
+---------------+
| users       |
+---------------+
DATABASE_NAME/BRANCH_NAME > exit;
```

**Example Postgres session:**

```shellscript
psql-17 (17.5 (Homebrew))
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_128_GCM_SHA256, compression: off, ALPN: postgresql)
Type "help" for help.

pg/|⚠ main ⚠|> \dt
                   List of relations
 Schema |    Name     | Type  |          Owner
--------+-------------+-------+-------------------------
 public | users | table | pscale_api_2e2o0t28kd0v
(1 row)

pg/|⚠ main ⚠|> \q
```

Type `exit` (MySQL) or `\q` (Postgres and Neki) to exit the shell.

### Using replica connections

**Connect to a read-only replica:**

```shellscript
pscale shell mydatabase mybranch --replica
```

Replica connections route reads to replicas and default to the `reader` role.

For Neki, `--replica` sets the session’s Neki target to `REPLICA`. You can combine it with `--router` to select both a router group and a replica target.

### Connect through a Neki router group

```shellscript
pscale shell mydatabase mybranch --router analytics
pscale shell mydatabase mybranch --router analytics --replica
```

### Using specific roles

**Connect with a specific role:**

```shellscript
pscale shell mydatabase mybranch --role reader
```

### Import an existing.sql file using the shell command

**Command:**

The following example assumes you have already ran the `pscale shell` command and you have the shell open to run a MySQL command.

To import an existing `.sql` file you may have available you would want to use the MySQL `source` command and provide it the path to your file:

```shellscript
DATABASE_NAME/BRANCH_NAME > source <YOUR_DUMP_FILE>.sql;
```

**Output:**

Your file should be imported as expected.

When importing `.sql` dump files there are a few caveats to be aware of as sometimes the `.sql` file may have everything wrapped in a `START TRANSACTION;` / `COMMIT;` transaction which will result in the import timing out if it takes more than 20 seconds to complete due to our 20 second transaction timeout limit so you will want to make sure those are removed prior to beginning an import of the file.

Additionally, if your current schema requires our [Vitess foreign key constraints](../vitess/foreign-key-constraints.md) support you may need to ensure it has been enabled within your database Settings area first before proceeding with your import.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
