---
url: https://planetscale.com/docs/cli/shell
title: "Shell"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale CLI commands: shell

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

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you've installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The `shell` command

This command opens a secure shell instance to your database so that you can manipulate it from the command line.

For MySQL databases, it uses the MySQL command-line client (`mysql`). For Postgres databases, it uses the Postgres command-line client (`psql`). The appropriate client [must be installed](planetscale-environment-setup.md) prior to use.

<Note>
  If your Managed cluster has public connectivity disabled, you must connect over PrivateLink. Set up the VPC endpoint using the service name we provide, make sure your DNS resolves your PlanetScale hostnames to that PrivateLink endpoint, and then run `pscale shell mydatabase mybranch`.

  If you use a VPN such as Tailscale, do not override `psdb.cloud` DNS in your VPN configuration. Split‑DNS misconfiguration can point hostnames at the wrong place and break shell connectivity.
</Note>

**Usage:**

```bash theme={null}
pscale shell <DATABASE_NAME> <BRANCH_NAME> <FLAG>
```

By default, if no branch names are given and there is only one branch, it automatically opens a shell to that branch. If there are multiple branches for the given database, you'll be prompted to choose one.

### Available flags

| **Flag**                    | **Description**                                                                                                                                                 |
| :-------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `-h`, `--help`              | Help for shell command                                                                                                                                          |
| `--local-addr <ADDRESS>`    | Local address to bind and listen for connections. By default the proxy binds to `127.0.0.1` with a random port.                                                 |
| `--org <ORGANIZATION_NAME>` | The organization for the current user                                                                                                                           |
| `--remote-addr <ADDRESS>`   | PlanetScale Database remote network address. By default the remote address is populated automatically from the PlanetScale API                                  |
| `--replica`                 | When enabled, the password will route all reads to the branch's primary replicas and all read-only regions                                                      |
| `--role <ROLE>`             | Role defines the access level, allowed values are: reader, writer, readwriter, admin. Defaults to 'reader' for replica passwords, otherwise defaults to 'admin' |

Available roles for the `--role` flag are:

* `reader` - Read-only access
* `writer` - Write-only access
* `readwriter` - Read and write access
* `admin` - Full administrative access

For replica connections (`--replica` flag), the default role is `reader`. For regular connections, the default role is `admin`.

### Global flags

| **Command**                     | **Description**                                                                     |
| :------------------------------ | :---------------------------------------------------------------------------------- |
| `--api-token <TOKEN>`           | The API token to use for authenticating against the PlanetScale API                 |
| `--api-url <URL>`               | The base URL for the PlanetScale API (default `https://api.planetscale.com/`)       |
| `--config <CONFIG_FILE>`        | Config file (default is `$HOME/.config/planetscale/pscale.yml`)                     |
| `--debug`                       | Enable debug mode                                                                   |
| `-f`, `--format <FORMAT>`       | Show output in a specific format. Possible values: `human` (default), `json`, `csv` |
| `--no-color`                    | Disable color output                                                                |
| `--service-token <TOKEN>`       | Service Token for authenticating                                                    |
| `--service-token-id <TOKEN_ID>` | The Service Token ID for authenticating                                             |

## Examples

### Basic shell usage

**Open a shell to a database (auto-selects branch if only one exists):**

```bash theme={null}
pscale shell mydatabase
```

**Open a shell to a specific branch:**

```bash theme={null}
pscale shell mydatabase mybranch
```

Once the shell is opened, you can run SQL as expected.

**Example MySQL session:**

```bash theme={null}
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

```bash theme={null}
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

Type `exit` (MySQL) or `\q` (Postgres) to exit the shell.

### Using replica connections

**Connect to a read-only replica:**

```bash theme={null}
pscale shell mydatabase mybranch --replica
```

Replica connections route all reads to the branch's primary replicas, and defaults to `reader` role.

### Using specific roles

**Connect with a specific role:**

```bash theme={null}
pscale shell mydatabase mybranch --role reader
```

### Import an existing .sql file using the `shell` command

**Command:**

The following example assumes you have already ran the `pscale shell` command and you have the shell open to run a MySQL command.

To import an existing `.sql` file you may have available you would want to use the MySQL `source` command and provide it the path to your file:

```bash theme={null}
DATABASE_NAME/BRANCH_NAME > source <YOUR_DUMP_FILE>.sql;
```

**Output:**

Your file should be imported as expected.

<Note>
  When importing `.sql` dump files there are a few caveats to be aware of as sometimes the `.sql` file may have everything wrapped in a `START TRANSACTION;` / `COMMIT;` transaction which will result in the import timing out if it takes more than 20 seconds to complete due to our 20 second transaction timeout limit so you will want to make sure those are removed prior to beginning an import of the file.

  Additionally, if your current schema requires our [Vitess foreign key constraints](../vitess/foreign-key-constraints.md) support you may need to ensure it has been enabled within your database Settings area first before proceeding with your import.
</Note>

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
