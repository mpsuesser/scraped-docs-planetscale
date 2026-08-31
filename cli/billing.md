---
url: https://planetscale.com/docs/cli/billing
title: "Billing"
description: ""
access_date: 2026-08-31T07:29:59.083Z
current_date: 2026-08-31T07:29:59.083Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The billing command

This command manages the organization’s [payment method](../billing.md#payment-methods) and invoices. There is one current card. Updating it opens Stripe Checkout in a browser; the CLI never collects the card number.

**Usage:**

```shellscript
pscale billing <SUB-COMMAND> <FLAG>
```

`--org` is required.

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Product** | **Description** |
| --- | --- | --- | --- |
| `invoice list` | `--page <NUMBER>`, `--per-page <NUMBER>` | Postgres, Vitess | List invoices for an organization |
| `invoice show <INVOICE_ID>` |  | Postgres, Vitess | Show an invoice |
| `invoice line-items <INVOICE_ID>` | `--page <NUMBER>`, `--per-page <NUMBER>` | Postgres, Vitess | List line items for an invoice |
| `payment-method show` |  | Postgres, Vitess | Show the current card |
| `payment-method update` |  | Postgres, Vitess | Create a Stripe Checkout session, open it when possible, and wait until the card is verified and saved |
| `payment-method status <SETUP_ID>` |  | Postgres, Vitess | Show a Checkout setup started by `update`. Pass the setup id, not the card id from `show` |
| `payment-method delete` | `--force` | Postgres, Vitess | Delete the current card |

#### Sub-command flag descriptions

| **Sub-command flag** | **Description** | **Applicable sub-commands** |
| --- | --- | --- |
| `--force` | Delete the payment method without a confirmation prompt | `payment-method delete` |
| `--page <NUMBER>` | Page of results to fetch. Default is `1` for invoices. | `invoice list`, `invoice line-items` |
| `--per-page <NUMBER>` | Number of results per page. Default is `25`. | `invoice list`, `invoice line-items` |

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for `billing` command |
| `--org <ORGANIZATION_NAME>` | The organization for the current user |

`--org` is required.

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

Service tokens need `read_payment_method` to show the card and `write_payment_method` to update or delete it.

## Examples

### Show the current card

**Command:**

```shellscript
pscale billing payment-method show --org <ORGANIZATION_NAME>
```

### Update the card

**Command:**

```shellscript
pscale billing payment-method update --org <ORGANIZATION_NAME>
```

This creates a Checkout session and waits until you finish in the browser. Leave the command running. If it is interrupted, use `status` with the setup id it printed. Do not run `update` again while that setup is still pending.

Agents should pass `--format json`. Pending details are written to stderr; a final JSON object is written to stdout when Checkout completes.

### Check a setup after an interrupted update

**Command:**

```shellscript
pscale billing payment-method status <SETUP_ID> --org <ORGANIZATION_NAME>
```

`status` does not poll. If the setup is still `pending`, wait and run the same command again.

### Delete the current card

**Command:**

```shellscript
pscale billing payment-method delete --org <ORGANIZATION_NAME>
```

In JSON mode, pass `--force` after the user has approved the deletion.

### List invoices

Invoice list and line-item commands are paginated. Request the next page with `--page` when you need it; do not walk every page automatically.

```shellscript
pscale billing invoice list --org <ORGANIZATION_NAME>
pscale billing invoice list --org <ORGANIZATION_NAME> --page 2 --per-page 25
pscale billing invoice show <INVOICE_ID> --org <ORGANIZATION_NAME>
pscale billing invoice line-items <INVOICE_ID> --org <ORGANIZATION_NAME>
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
