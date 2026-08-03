---
url: https://planetscale.com/docs/cli/webhook
title: "Webhook"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The webhook command

This command allows you to create, list, update, test, and delete [webhooks](../api/webhooks.md) for your database.

**Usage:**

```shellscript
pscale webhook <SUB-COMMAND> <FLAG>
```

### Available sub-commands

| **Sub-command** | **Description** | **Product** |
| --- | --- | --- |
| `create <DATABASE_NAME>` | Create a new webhook for a database | Vitess, Postgres |
| `delete <DATABASE_NAME> <WEBHOOK_ID>` | Delete a webhook from a database | Vitess, Postgres |
| `list <DATABASE_NAME>` | List all webhooks for a database | Vitess, Postgres |
| `show <DATABASE_NAME> <WEBHOOK_ID>` | Show details for a specific webhook | Vitess, Postgres |
| `test <DATABASE_NAME> <WEBHOOK_ID>` | Send a test event to a webhook | Vitess, Postgres |
| `update <DATABASE_NAME> <WEBHOOK_ID>` | Update an existing webhook | Vitess, Postgres |

#### Sub-command flags

| **Sub-command flag** | **Description** | **Applicable sub-commands** |
| --- | --- | --- |
| `--events <EVENTS>` | Comma-separated list of events to trigger the webhook. See [webhook events](../api/webhook-events.md) for available events. | `create`, `update` |
| `--url <URL>` | The HTTPS URL where webhook events will be sent | `create`, `update` |
| `--enabled` | Enable or disable the webhook. Use `--enabled` or `--enabled=true` to enable, `--enabled=false` to disable. Webhooks are disabled by default when created. | `update` |

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for `webhook` command |
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

### List webhooks for a database

**Command:**

```shellscript
pscale webhook list <DATABASE_NAME> --org <ORGANIZATION_NAME>
```

This lists all webhooks configured for the specified database.

**Output:**

```shellscript
No webhooks exist in database <DATABASE_NAME>.
```

Or if webhooks exist:

```shellscript
ID             URL                                   EVENTS                          ENABLED   CREATED AT   UPDATED AT
-------------- ------------------------------------- ------------------------------- --------- ------------ ------------
 abc123xyz      https://example.com/webhook           branch.ready, branch.sleeping   Yes       2 days ago   1 day ago
```

### Create a webhook

**Command:**

```shellscript
pscale webhook create <DATABASE_NAME> --org <ORGANIZATION_NAME> \
  --events "branch.ready,branch.sleeping" \
  --url https://example.com/webhook
```

This creates a new webhook for the specified database with the selected events. The webhook will be disabled by default until you enable it with the `update` command.

**Output:**

```shellscript
ID             URL                                   SECRET                                                             EVENTS                          ENABLED   CREATED AT   UPDATED AT
-------------- ------------------------------------- ------------------------------------------------------------------ ------------------------------- --------- ------------ ------------
 abc123xyz      https://example.com/webhook           8e46bd50ca092655b1efdfca329f0d79eb976714030a8bfa031397eb0d1cb433   branch.ready, branch.sleeping   No        now          now
```

When you create a webhook, a secret is generated and displayed **only once** in the output. Store this secret securely as you’ll need it to [validate webhook signatures](../api/webhooks.md#validating-a-webhook-signature). You can also view the secret later from the database settings page in the dashboard.

### Show webhook details

**Command:**

```shellscript
pscale webhook show <DATABASE_NAME> <WEBHOOK_ID> --org <ORGANIZATION_NAME>
```

This displays detailed information about a specific webhook, including its ID, URL, secret, events, enabled status, and timestamps.

**Output:**

```shellscript
ID             URL                                   SECRET                                                             EVENTS                          ENABLED   CREATED AT       UPDATED AT
-------------- ------------------------------------- ------------------------------------------------------------------ ------------------------------- --------- ---------------- ----------------
 abc123xyz      https://example.com/webhook           b4c29e6ae54a6456496cec7dcbfad7ace6e973a694802de2978b4d6e001fca6e   branch.ready, branch.sleeping   No        25 seconds ago   25 seconds ago
```

The `show` command displays the webhook secret, which is useful if you need to retrieve it after creation. Store this secret securely as you’ll need it to [validate webhook signatures](../api/webhooks.md#validating-a-webhook-signature).

### Update a webhook

**Command:**

```shellscript
pscale webhook update <DATABASE_NAME> <WEBHOOK_ID> --org <ORGANIZATION_NAME> --enabled
```

This enables an existing webhook. To disable a webhook, use `--enabled=false`. You can also use this command to update other webhook settings like events and URL.

**Output:**

```shellscript
ID             URL                                   EVENTS                          ENABLED   CREATED AT     UPDATED AT
-------------- ------------------------------------- ------------------------------- --------- -------------- ------------
 abc123xyz      https://example.com/webhook           branch.ready, branch.sleeping   Yes       58 seconds ago now
```

### Test a webhook

**Command:**

```shellscript
pscale webhook test <DATABASE_NAME> <WEBHOOK_ID> --org <ORGANIZATION_NAME>
```

This sends a test event to the webhook URL to verify it’s configured correctly. You can only send one test event every 20 seconds per webhook.

**Output:**

```shellscript
Test event was successfully sent to webhook <WEBHOOK_ID>.
```

### Delete a webhook

**Command:**

```shellscript
pscale webhook delete <DATABASE_NAME> <WEBHOOK_ID> --org <ORGANIZATION_NAME>
```

This permanently deletes the webhook from the database.

**Output:**

```shellscript
Webhook <WEBHOOK_ID> was successfully deleted from <DATABASE_NAME>.
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
