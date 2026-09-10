---
url: https://planetscale.com/docs/cli/org
title: "Org"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The org command

This command allows you to list, show, switch, and update [organizations](../security/access-control.md#organization-member), and to manage organization members, teams, and [SSO](../security/sso.md).

**Usage:**

```shellscript
pscale org <SUB-COMMAND> <FLAG>
```

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Product** | **Description** |
| --- | --- | --- | --- |
| `list` |  | Postgres, Vitess, Neki | List all currently active organizations with timestamps |
| `member list` | `--query <QUERY>`, `--page <NUMBER>`, `--per-page <NUMBER>` | Postgres, Vitess, Neki | List the members of an organization |
| `member show <EMAIL\|USER_ID>` |  | Postgres, Vitess, Neki | Show an organization member |
| `member update <EMAIL\|USER_ID>` | `--role <ROLE>` \* | Postgres, Vitess, Neki | Change another member’s organization role |
| `member remove <EMAIL\|USER_ID>` | `--force`, `--delete-passwords`, `--delete-service-tokens` | Postgres, Vitess, Neki | Remove a member from an organization |
| `show` |  | Postgres, Vitess, Neki | Display the currently active organization |
| `sso show` |  | Postgres, Vitess, Neki | Show organization SSO status |
| `sso enable` |  | Postgres, Vitess, Neki | Enable organization SSO |
| `sso disable` | `--force` | Postgres, Vitess, Neki | Disable organization SSO |
| `sso configure` |  | Postgres, Vitess, Neki | Open the identity provider setup portal |
| `sso directory enable` |  | Postgres, Vitess, Neki | Open the directory sync setup portal |
| `sso directory disable` | `--force` | Postgres, Vitess, Neki | Disable directory sync |
| `sso domain list` |  | Postgres, Vitess, Neki | List SSO email domains |
| `sso domain show <DOMAIN_ID>` |  | Postgres, Vitess, Neki | Show an SSO email domain |
| `sso domain verify` | `--wait`, `--wait-timeout <DURATION>` | Postgres, Vitess, Neki | Open the domain verification portal |
| `sso domain delete <DOMAIN_ID>` | `--force` | Postgres, Vitess, Neki | Delete an SSO email domain |
| `switch <ORGANIZATION_NAME>` | `--save-config <PATH>` | Postgres, Vitess, Neki | Switch the currently active organization |
| `team create` | `--name <NAME>` \*, `--description <TEXT>` | Postgres, Vitess, Neki | Create a team |
| `team delete <TEAM>` | `--force` | Postgres, Vitess, Neki | Delete a team |
| `team list` | `--query <QUERY>`, `--page <NUMBER>`, `--per-page <NUMBER>` | Postgres, Vitess, Neki | List teams in an organization |
| `team member add <TEAM> <EMAIL\|USER_ID>` |  | Postgres, Vitess, Neki | Add an organization member to a team |
| `team member list <TEAM>` | `--page <NUMBER>`, `--per-page <NUMBER>` | Postgres, Vitess, Neki | List members of a team |
| `team member remove <TEAM> <EMAIL\|USER_ID>` | `--force`, `--delete-passwords` | Postgres, Vitess, Neki | Remove a member from a team |
| `team show <TEAM>` |  | Postgres, Vitess, Neki | Show a team |
| `team update <TEAM>` | `--name <NAME>`, `--description <TEXT>` | Postgres, Vitess, Neki | Update a team’s name or description |
| `update` | `--billing-email <EMAIL>`, `--idp-managed-roles`, `--idp-sso-managed-roles`, `--spend-alert`, `--spend-alert-amount <AMOUNT>` | Postgres, Vitess, Neki | Update organization settings |

> \* *Flag is required*

### Service token automation: org

Legend: ✅ supported · 🚫 unavailable with a service token · 👤 interactive login only.

| Sub-command | Env-var auth | `--service-token` flag | Requires `--org` | Postgres / Vitess | `--format json` | API equivalent |
| --- | --- | --- | --- | --- | --- | --- |
| `org list` | ✅ | ✅ | No | Both | ✅ | `pscale api organizations --format json` |
| `org show` | 🚫 | 🚫 | No | Both | ✅ | N/A |
| `org switch <org>` | ✅ | ✅ | No | Both | ✅ | N/A |
| `org update` | ✅ | ✅ | Yes | Both | ✅ | `pscale api --method PATCH organizations/<org> --format json` |
| `org sso show` | ✅ | ✅ | Yes | Both | ✅ | `pscale api organizations/<org>/sso --format json` |
| `org sso enable` | ✅ | ✅ | Yes | Both | ✅ | `pscale api --method POST organizations/<org>/sso --format json` |
| `org sso configure` | ✅ | ✅ | Yes | Both | ✅ | `pscale api --method POST organizations/<org>/sso/configure --format json` |
| `org sso domain list` | ✅ | ✅ | Yes | Both | ✅ | `pscale api organizations/<org>/sso/domains --format json` |
| `org sso domain show <id>` | ✅ | ✅ | Yes | Both | ✅ | `pscale api organizations/<org>/sso/domains/<id> --format json` |
| `org sso domain verify` | ✅ | ✅ | Yes | Both | ✅ | `pscale api --method POST organizations/<org>/sso/domains --format json` |
| `org sso domain delete <id>` | ✅ | ✅ | Yes | Both | ✅ | `pscale api --method DELETE organizations/<org>/sso/domains/<id> --format json` |
| `org sso directory enable` | ✅ | ✅ | Yes | Both | ✅ | `pscale api --method POST organizations/<org>/sso/directory --format json` |
| `org sso directory disable` | ✅ | ✅ | Yes | Both | ✅ | `pscale api --method DELETE organizations/<org>/sso/directory --format json` |
| `org sso disable` | ✅ | ✅ | Yes | Both | ✅ | `pscale api --method DELETE organizations/<org>/sso --format json` |

`org show` fails with `not authenticated yet. Please run 'pscale auth login'` under a valid service token. That is the **no-current-org** state. Do **not** run `pscale auth login`. Use `org list --format json`, then set `PLANETSCALE_ORG` or pass `--org` on resource commands.

SSO commands require `manage_sso` on the service token. `org update` requires `write_organization`.

#### Sub-command flag descriptions

Some of the sub-commands have additional flags unique to the sub-command. This section covers what each of those does. See the above table for which context.

| **Sub-command flag** | **Description** | **Applicable sub-commands** |
| --- | --- | --- |
| `--save-config <PATH>` | Path to store the organization. By default, the configuration is automatically deduced based on where `pscale` is executed. | `switch` |
| `--query <QUERY>` | Filter members by a name or email prefix, or filter teams by name. | `member list`, `team list` |
| `--page <NUMBER>` | Page of results to fetch. | `member list`, `team list`, `team member list` |
| `--per-page <NUMBER>` | Number of results per page. Default is `100`. | `member list`, `team list`, `team member list` |
| `--role <ROLE>` | Organization role to assign: `admin`, `member`, or `analyst`. See [access control](../security/access-control.md). | `member update` |
| `--force` | Skip confirmation for destructive actions. | `member remove`, `team delete`, `team member remove`, `sso disable`, `sso directory disable`, `sso domain delete` |
| `--delete-passwords` | Also delete passwords created by the member, or passwords created through the team. Cannot be used when removing yourself from the organization. | `member remove`, `team member remove` |
| `--delete-service-tokens` | Also delete service tokens created by the member. Cannot be used when removing yourself. | `member remove` |
| `--name <NAME>` | Team name. Required on create. | `team create`, `team update` |
| `--description <TEXT>` | Team description. | `team create`, `team update` |
| `--billing-email <EMAIL>` | Billing email for the organization. | `update` |
| `--idp-managed-roles` | Whether the identity provider manages organization roles through directory sync. Boolean; set explicitly with `=true` or `=false`. Enabling this turns off `--idp-sso-managed-roles`. | `update` |
| `--idp-sso-managed-roles` | Whether the identity provider manages organization roles through SSO. Boolean; set explicitly with `=true` or `=false`. Enabling this turns off `--idp-managed-roles`. | `update` |
| `--spend-alert` | Enable or disable billing spend alerts. Boolean; set explicitly with `=true` or `=false`. | `update` |
| `--spend-alert-amount <AMOUNT>` | Monthly spend amount that triggers spend alerts. Required when enabling spend alerts. | `update` |
| `--wait` | After opening the domain verification portal, wait until a domain is verified or failed. | `sso domain verify` |
| `--wait-timeout <DURATION>` | Maximum time to wait with `--wait`. Default is `10m`. | `sso domain verify` |

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

### Update organization settings

`--org` is required. Only flags you pass are sent to the API.

```shellscript
pscale org update --org <ORGANIZATION_NAME> --billing-email billing@example.com
pscale org update --org <ORGANIZATION_NAME> --idp-managed-roles=false
pscale org update --org <ORGANIZATION_NAME> --idp-sso-managed-roles=true
pscale org update --org <ORGANIZATION_NAME> --spend-alert=true --spend-alert-amount 2500
pscale org update --org <ORGANIZATION_NAME> --spend-alert=false
```

`--spend-alert-amount` cannot be combined with `--spend-alert=false`. You cannot enable both `--idp-managed-roles` and `--idp-sso-managed-roles`. Enabling one turns the other off.

### Manage organization SSO

`--org` is required. Organization administrators can enable SSO from the CLI. Service tokens need `manage_sso` access. See [Single sign-on](../security/sso.md) for what enabling SSO does to membership.

`sso domain verify` returns a portal URL and tries to open it. Domain records appear after you finish verification in the portal. With `--wait`, the command prints the URL, then waits until a new domain is verified or failed.

```shellscript
pscale org sso show --org <ORGANIZATION_NAME>
pscale org sso enable --org <ORGANIZATION_NAME>
pscale org sso domain verify --org <ORGANIZATION_NAME>
pscale org sso domain verify --org <ORGANIZATION_NAME> --wait
pscale org sso domain list --org <ORGANIZATION_NAME>
pscale org sso domain show <DOMAIN_ID> --org <ORGANIZATION_NAME>
pscale org sso configure --org <ORGANIZATION_NAME>
pscale org sso directory enable --org <ORGANIZATION_NAME>
pscale org sso disable --org <ORGANIZATION_NAME>
```

In JSON mode, `disable`, `directory disable`, and `domain delete` require `--force`.

### Manage organization teams

Teams can be identified by ID, name, or slug. `--org` is required. List and member-list commands are paginated; pass `--page` for the next page instead of walking every page automatically.

```shellscript
pscale org team list --org <ORGANIZATION_NAME>
pscale org team show <TEAM> --org <ORGANIZATION_NAME>
pscale org team create --name <NAME> --description <TEXT> --org <ORGANIZATION_NAME>
pscale org team update <TEAM> --name <NAME> --org <ORGANIZATION_NAME>
pscale org team delete <TEAM> --org <ORGANIZATION_NAME>
pscale org team member list <TEAM> --org <ORGANIZATION_NAME>
pscale org team member add <TEAM> <EMAIL> --org <ORGANIZATION_NAME>
pscale org team member remove <TEAM> <EMAIL> --org <ORGANIZATION_NAME>
```

SSO or directory-managed teams cannot be updated, deleted, or have members added or removed through the API.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
