---
url: https://planetscale.com/docs/cli/size
title: "Size"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale CLI commands: size

**Platform availability:** 

Lists the sizes for various components within PlanetScale.

## Usage

```bash theme={null}
pscale size [command]
```

## Available commands

| **Command** | **Product**            | **Description**                          |
| :---------- | :--------------------- | :--------------------------------------- |
| `cluster`   | Postgres, Vitess, Neki | List the sizes for PlanetScale databases |

## Flags

| **Flag**       | **Description**                       |
| :------------- | :------------------------------------ |
| `-h`, `--help` | Help for size                         |
| `--org string` | The organization for the current user |

### Global flags

| **Command**                 | **Description**                                                                                                    |
| :-------------------------- | :----------------------------------------------------------------------------------------------------------------- |
| `--api-token string`        | The API token to use for authenticating against the PlanetScale API                                                |
| `--api-url string`          | The base URL for the PlanetScale API. (default "\<[https://api.planetscale.com/>](https://api.planetscale.com/>)") |
| `--config string`           | Config file (default is `$HOME/.config/planetscale/pscale.yml`)                                                    |
| `--debug`                   | Enable debug mode                                                                                                  |
| `-f`, `--format string`     | Show output in a specific format. Possible values: \[human, json, csv] (default "human")                           |
| `--no-color`                | Disable color output                                                                                               |
| `--service-token string`    | The service token for authenticating                                                                               |
| `--service-token-id string` | The service token ID for authenticating                                                                            |

## The `cluster` sub-command

List the sizes for PlanetScale databases.

**Usage:**

```bash theme={null}
pscale size cluster [command]
```

**Alias:** `clusters`

### Available sub-commands

| **Command** | **Product**            | **Description**                                              |
| :---------- | :--------------------- | :----------------------------------------------------------- |
| `list`      | Postgres, Vitess, Neki | List the sizes that are available for a PlanetScale database |

## The `list` sub-command

List the sizes that are available for a PlanetScale database. Use `--engine` to specify the database engine type.

**Usage:**

```bash theme={null}
pscale size cluster list [flags]
```

**Alias:** `ls`

### Available flags

| **Flag**          | **Description**                                                                                                     |
| :---------------- | :------------------------------------------------------------------------------------------------------------------ |
| `--engine string` | Filter cluster sizes by database engine. Supported values: `mysql`, `postgresql`, `neki`. Omit to list all engines. |
| `-h`, `--help`    | Help for list                                                                                                       |
| `--metal`         | View cluster sizes and rates for clusters with metal storage                                                        |
| `--region string` | View cluster sizes and rates for a specific region                                                                  |

## Examples

### List all available cluster sizes

```bash theme={null}
pscale size cluster list
```

Omitting `--engine` lists Vitess, Neki, and Postgres sizes.

### List Vitess cluster sizes explicitly

```bash theme={null}
pscale size cluster list --engine mysql
```

### List PostgreSQL cluster sizes

```bash theme={null}
pscale size cluster list --engine postgresql
```

### List Neki cluster sizes

```bash theme={null}
pscale size cluster list --engine neki
```

Neki admin and router SKUs are listed separately with `pscale branch admin sizes` and `pscale branch router sizes`. See [Neki cluster sizing](../neki/cluster-configuration/cluster-sizing.md).

### List cluster sizes for a specific organization

```bash theme={null}
pscale size cluster list --org <ORG_NAME>
```

### List cluster sizes for a specific region

```bash theme={null}
pscale size cluster list --region <REGION_NAME>
```

### List PostgreSQL cluster sizes for a specific region

```bash theme={null}
pscale size cluster list --engine postgresql --region us-east
```

### List Metal cluster sizes

```bash theme={null}
pscale size cluster list --metal
```

For Postgres or Neki Metal clusters, use:

```bash theme={null}
pscale size cluster list --engine postgresql --metal
pscale size cluster list --engine neki --metal
```

For more information about PlanetScale cluster sizes and pricing, see:

* [PlanetScale Vitess plans documentation](../planetscale-plans.md)
* [Neki pricing](../neki/pricing.md)
* [PlanetScale Postgres pricing](../postgres/pricing.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
