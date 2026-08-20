---
url: https://planetscale.com/docs/cli/org
title: "Org"
description: ""
access_date: 2026-08-20T18:00:05.296Z
current_date: 2026-08-20T18:00:05.296Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The org command

This command allows you to list, show, and switch [organizations](../security/access-control.md#organization-member), and to manage organization members.

**Usage:**

```shellscript
pscale org <SUB-COMMAND> <FLAG>
```

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Product** | **Description** |
| --- | --- | --- | --- |
| `list` |  | Postgres, Vitess | List all currently active organizations with timestamps |
| `member list` | `--query <QUERY>`, `--page <NUMBER>`, `--per-page <NUMBER>` | Postgres, Vitess | List the members of an organization |
| `member show <EMAIL\|USER_ID>` |  | Postgres, Vitess | Show an organization member |
| `member update <EMAIL\|USER_ID>` | `--role <ROLE>` \* | Postgres, Vitess | Change another member’s organization role |
| `member remove <EMAIL\|USER_ID>` | `--force`, `--delete-passwords`, `--delete-service-tokens` | Postgres, Vitess | Remove a member from an organization |
| `show` |  | Postgres, Vitess | Display the currently active organization |
| `switch <ORGANIZATION_NAME>` | `--save-config <PATH>` | Postgres, Vitess | Switch the currently active organization |

> \* *Flag is required*

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
| `--query <QUERY>` | Filter members by a name or email prefix. | `member list` |
| `--page <NUMBER>` | Page of results to fetch. | `member list` |
| `--per-page <NUMBER>` | Number of results per page. Default is `100`. | `member list` |
| `--role <ROLE>` | Organization role to assign: `admin`, `member`, or `analyst`. See [access control](../security/access-control.md). | `member update` |
| `--force` | Remove the member without confirmation. | `member remove` |
| `--delete-passwords` | Also delete passwords created by the member. Cannot be used when removing yourself. | `member remove` |
| `--delete-service-tokens` | Also delete service tokens created by the member. Cannot be used when removing yourself. | `member remove` |

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

### The org command with member sub-commands

**Command:**

```shellscript
pscale org member list --org <ORGANIZATION_NAME>
pscale org member show <EMAIL> --org <ORGANIZATION_NAME>
pscale org member update <EMAIL> --role member --org <ORGANIZATION_NAME>
pscale org member remove <EMAIL> --org <ORGANIZATION_NAME>
```

Members can be identified by email or by the `USER_ID` shown by `org member list`, which is paginated at 100 members per page. Changing another member’s role or removing them requires the organization admin role, and you cannot change your own role or remove the last admin. You can remove yourself without being an admin.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
