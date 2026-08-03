---
url: https://planetscale.com/docs/cli/org
title: "Org"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The org command

This command allows you to list, show, and switch [organizations](../security/access-control.md#organization-member).

**Usage:**

```shellscript
pscale org <SUB-COMMAND> <FLAG>
```

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Product** | **Description** |
| --- | --- | --- | --- |
| `list` |  | Postgres, Vitess | List all currently active organizations with timestamps |
| `show` |  | Postgres, Vitess | Display the currently active organization |
| `switch <ORGANIZATION_NAME>` | `--save-config <PATH>` | Postgres, Vitess | Switch the currently active organization |

### Service token automation: org

Legend: ✅ supported · 🚫 unavailable with a service token · 👤 interactive login only.

| Sub-command | Env-var auth | `--service-token` flag | Requires `--org` | Postgres / Vitess | `--format json` | API equivalent |
| --- | --- | --- | --- | --- | --- | --- |
| `org list` | ✅ | ✅ | No | Both | ✅ | `pscale api organizations --format json` |
| `org show` | 🚫 | 🚫 | No | Both | ✅ | N/A |
| `org switch <org>` | ✅ | ✅ | No | Both | ✅ | N/A |

`org show` fails with `not authenticated yet. Please run 'pscale auth login'` under a valid service token. That is the **no-current-org** state. Do **not** run `pscale auth login`. Use `org list --format json`, then set `PLANETSCALE_ORG` or pass `--org` on resource commands.

#### Sub-command flag descriptions

Some of the sub-commands have additional flags unique to the sub-command. This section covers what each of those does. See the above table for which context.

| **Sub-command flag** | **Description** | **Applicable sub-commands** |
| --- | --- | --- |
| `--save-config <PATH>` | Path to store the organization. By default, the configuration is automatically deduced based on where `pscale` is executed. | `switch` |

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for `org` command |

### Global flags

| **Command** | **Description** |
| --- | --- |
| `--api-token <TOKEN>` | The API token to use for authenticating against the PlanetScale API. |
| `--api-url <URL>` | The base URL for the PlanetScale API. Default is `https://api.planetscale.com/`. |
| `--config <CONFIG_FILE>` | Config file. Default is `$HOME/.config/planetscale/pscale.yml`. Local override inside a Git repository is `$CWD/.pscale.yml` in the project’s root. |
| `--debug` | Enable debug mode. |
| `-f`, `--format <FORMAT>` | Show output in a specific format. Possible values: `human` (default), `json`, `csv`. |
| `--no-color` | Disable color output. |
| `--service-token <TOKEN>` | The service token for authenticating. |
| `--service-token-id <TOKEN_ID>` | The service token ID for authenticating. |

## Examples

### The org command with switch sub-command

**Command:**

```shellscript
pscale org switch <ORGANIZATION_NAME>
```

**Output:**

Successfully switched to organization `<ORGANIZATION_NAME>` (using file: `/Users/name/.config/planetscale/pscale.yml`)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
