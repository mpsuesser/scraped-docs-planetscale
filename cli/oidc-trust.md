---
url: https://planetscale.com/docs/cli/oidc-trust
title: "Oidc Trust"
description: ""
access_date: 2026-11-12T21:52:41.301Z
current_date: 2026-11-12T21:52:41.301Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The oidc-trust command

Create, list, update, and delete OIDC trusts. An OIDC trust lets workflows from a trusted identity provider exchange an OIDC token for a short-lived service token. See [PlanetScale API with OpenID Connect](../api/oidc.md) for how the exchange works.

**Usage:**

```shellscript
pscale oidc-trust <SUB-COMMAND> <FLAG>
```

### Available sub-commands

| **Sub-command** | **Description** |
| --- | --- |
| `create` | Create an OIDC trust |
| `delete <TRUST_ID>` | Delete an OIDC trust |
| `list` | List the OIDC trusts in the organization |
| `providers list` | List the available OIDC trust providers |
| `providers show <NAME>` | Show an OIDC trust provider and the claims in its tokens |
| `show <TRUST_ID>` | Show an OIDC trust |
| `update <TRUST_ID>` | Update an OIDC trust |

### Sub-command flags

| **Flag** | **Description** | **Applicable sub-commands** |
| --- | --- | --- |
| `--name <NAME>` | The display name of the trust. Required for `create`. | `create`, `update` |
| `--provider <PROVIDER>` | The identity provider. Required. Run `pscale oidc-trust providers list` to see the options. | `create` |
| `--max-ttl <SECONDS>` | The maximum token lifetime in seconds. Required for `create`. | `create`, `update` |
| `--condition <CLAIM=VALUE>` | A claim the token must match. Repeatable. On `update`, replaces all conditions. | `create`, `update` |
| `--org-access <ACCESS>` | An organization access to grant. Repeatable. On `update`, replaces all organization and database accesses. | `create`, `update` |
| `--database-access <DATABASE:ACCESS>` | A database access to grant. Repeatable. On `update`, replaces all organization and database accesses. | `create`, `update` |
| `--all-databases-access <ACCESS>` | An access to grant on all databases. Repeatable. On `update`, replaces all all-databases accesses. | `create`, `update` |
| `--force` | Delete the trust without confirmation. Required to delete without a terminal prompt. | `delete` |

`update` changes only the fields you pass. To clear a list, pass its flag with an empty value, such as `--condition=`.

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for `oidc-trust` command |
| `--org <ORGANIZATION_NAME>` | The organization for the current user. Required. |

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

### Inspecting a provider

**Command:**

```shellscript
pscale oidc-trust providers show github_actions --org <ORGANIZATION_NAME>
```

This shows the provider’s issuer and the claims in its tokens, and marks which claims a trust must match. Use `providers list` to find provider names.

### Creating an OIDC trust

**Command:**

```shellscript
pscale oidc-trust create \
  --org <ORGANIZATION_NAME> \
  --name "Deploy from main" \
  --provider github_actions \
  --max-ttl 900 \
  --condition repository_id=<REPOSITORY_ID> \
  --condition repository_owner_id=<OWNER_ID> \
  --database-access <DATABASE_NAME>:read_branch
```

This creates a trust that grants `read_branch` on one database to workflows matching both conditions. Required claims for the provider must be set as conditions. The output includes the trust ID. Pass it as `oidc-trust` to `planetscale/auth-action`.

### Updating an OIDC trust

**Command:**

```shellscript
pscale oidc-trust update <TRUST_ID> --org <ORGANIZATION_NAME> --max-ttl 600
```

### Deleting an OIDC trust

**Command:**

```shellscript
pscale oidc-trust delete <TRUST_ID> --org <ORGANIZATION_NAME> --force
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
