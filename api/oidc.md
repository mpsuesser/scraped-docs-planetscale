---
url: https://planetscale.com/docs/api/oidc
title: "Oidc"
description: ""
access_date: 2026-11-12T21:52:41.301Z
current_date: 2026-11-12T21:52:41.301Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale API with OpenID Connect

> Authenticate to the PlanetScale API with OpenID Connect

## Overview

With OpenID Connect (OIDC), trusted issuers can create signed, short-lived JSON Web Tokens (JWTs) with a verified set of claims to define the context of a workflow (such as a GitHub repository ID and the ref it runs on).
Organization admins can configure a trust policy with the expected set of claims and permissions to grant workflows.
Workflows can then exchange that OIDC token with the PlanetScale API for a short-lived service token, replacing the need for long-lived credentials.

## Create an OIDC trust using the PlanetScale dashboard

To create an OIDC trust using the dashboard, log into your organization, go to the [**"Settings"** > **"OpenID Connect"**](https://app.planetscale.com/~/settings/oidc) page, and click the **"New policy"** button.

Choose a policy name, an identity provider, a service token lifetime, the required claims, and the permissions to grant to the short-lived service tokens minted for this policy.

Additional claims are recommended to scope a policy to a specific workflow, environment, or branch depending on the scenario.

## Create an OIDC trust using the PlanetScale CLI

You can also manage OIDC trusts with the [`pscale oidc-trust`](../cli/oidc-trust.md) commands. List the available providers and the claims in their tokens:

```bash theme={null}
pscale oidc-trust providers list --org <ORGANIZATION_NAME>
pscale oidc-trust providers show github_actions --org <ORGANIZATION_NAME>
```

Then create the trust:

```bash theme={null}
pscale oidc-trust create \
  --org <ORGANIZATION_NAME> \
  --name "Deploy from main" \
  --provider github_actions \
  --max-ttl 900 \
  --condition repository_id=<REPOSITORY_ID> \
  --condition repository_owner_id=<OWNER_ID> \
  --database-access <DATABASE_NAME>:read_branch
```

The output includes the trust ID to pass to `planetscale/auth-action`.

## Providers

PlanetScale supports configuring OIDC with [GitHub Actions](https://docs.github.com/en/actions/concepts/security/openid-connect) and [Depot CI](https://depot.dev/docs/ci/oidc). Refer to their respective documentation for information on the claims they support.

## Exchanging an OIDC token

A workflow in GitHub Actions or Depot CI with the `id-token: write` permission can request an OIDC token.
Use the `planetscale/auth-action` action in a Depot CI or GitHub Actions workflow job with the `id-token: write` permission to request
an OIDC token from the provider and exchange it for a service token:

```yaml theme={null}
name: CI

on:
  push:
    branches: [ main ]

jobs:
  main:
    runs-on: ubuntu-latest
    permissions:
      id-token: write

    steps:
    - uses: planetscale/auth-action@v1
      id: auth
      with:
        organization: "${{ vars.PLANETSCALE_ORGANIZATION }}"
        oidc-trust: ${{ vars.PLANETSCALE_OIDC_TRUST_ID }}

    - name: Read organization
      env:
        ORGANIZATION: ${{ vars.PLANETSCALE_ORGANIZATION }}
        SERVICE_TOKEN_ID: ${{ steps.auth.outputs.service-token-id }}
        SERVICE_TOKEN: ${{ steps.auth.outputs.service-token }}
      run: |
        curl --fail-with-body --silent --show-error \
          --header "Authorization: $SERVICE_TOKEN_ID:$SERVICE_TOKEN" \
          --header "Accept: application/json" \
          "https://api.planetscale.com/v1/organizations/$ORGANIZATION" \
          | jq '{name, created_at}'
```

The source of [`planetscale/auth-action`](https://github.com/planetscale/auth-action) is a useful reference if you want to implement the exchange in your own tooling.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
