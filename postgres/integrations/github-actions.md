---
url: https://planetscale.com/docs/postgres/integrations/github-actions
title: "Github Actions"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

With GitHub Actions, you can automate branch creation and role credentials within your CI workflow.

## Getting Started

## Authentication

## Convert GitHub branch name to PlanetScale branch name

## Create a PlanetScale branch

## Create a role for a branch

## Delete a role

## Delete a PlanetScale branch

## Getting started

The best way to use PlanetScale within GitHub Actions is via the `pscale` CLI.

Use [`planetscale/setup-pscale-action`](https://github.com/planetscale/setup-pscale-action) to make pscale available within your GitHub Actions.

```yaml
- name: Setup pscale
  uses: planetscale/setup-pscale-action@v1
```

The action works with Linux, Windows, and Mac runners. Once installed it will be added to your tool cache for subsequent runs.

## Authentication

Authentication for pscale is via service token environment variables.

You will need to [create a service token](../../cli/service-token.md). Make sure to give your service token the proper permissions to the database you’ll be using in your workflow.

Add your `PLANETSCALE_SERVICE_TOKEN_ID` and `PLANETSCALE_SERVICE_TOKEN` to your [Actions secrets](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions#creating-secrets-for-a-repository).

In your Actions workflow, you will need to make the secrets available as environment variables.

```yaml
- name: Run pscale command
  env:
    PLANETSCALE_SERVICE_TOKEN_ID: ${{ secrets.PLANETSCALE_SERVICE_TOKEN_ID }}
    PLANETSCALE_SERVICE_TOKEN: ${{ secrets.PLANETSCALE_SERVICE_TOKEN }}
  run: pscale database list
```

## Examples

The following examples show how to accomplish common Actions workflows with PlanetScale. In each example, notice that we use secrets for the service token, database and organization names. You will need to set them in your GitHub repository to target your own database.

```text
PLANETSCALE_ORG_NAME
PLANETSCALE_DATABASE_NAME
PLANETSCALE_SERVICE_TOKEN_ID
PLANETSCALE_SERVICE_TOKEN
```

### Convert GitHub branch name to PlanetScale branch name

PlanetScale branch names must be lowercase, alphanumeric characters and hyphens are allowed.

Since git branch names allow more possibilities, you can use the following code to transform a git branch name into an acceptable PlanetScale branch name.

```yaml
- name: Rename branch name
  run: |
    branch_name=$(printf '%s' "$HEAD_REF" | tr -cd '[:alnum:]-' | tr '[:upper:]' '[:lower:]')
    echo "PSCALE_BRANCH_NAME=$branch_name" >> "$GITHUB_ENV"
  env:
    HEAD_REF: ${{ github.head_ref }}
```

This makes `${{ env.PSCALE_BRANCH_NAME }}` available for use in the rest of the workflow. This is useful to run in any scenario where you are creating a PlanetScale branch to correspond with a git branch.

### Create a PlanetScale branch

You can use `pscale branch create` to create a branch that matches your GitHub branch name.

```yaml
- name: Create branch
  env:
    PLANETSCALE_SERVICE_TOKEN_ID: ${{ secrets.PLANETSCALE_SERVICE_TOKEN_ID }}
    PLANETSCALE_SERVICE_TOKEN: ${{ secrets.PLANETSCALE_SERVICE_TOKEN }}
  run: |
    set +e
    pscale branch show ${{ secrets.PLANETSCALE_DATABASE_NAME }} ${{ env.PSCALE_BRANCH_NAME }} --org ${{ secrets.PLANETSCALE_ORG_NAME }}
    exit_code=$?
    set -e

    if [ $exit_code -eq 0 ]; then
      echo "Branch exists. Skipping branch creation."
    else
      echo "Branch does not exist. Creating."
      pscale branch create ${{ secrets.PLANETSCALE_DATABASE_NAME }} ${{ env.PSCALE_BRANCH_NAME }} --wait --org ${{ secrets.PLANETSCALE_ORG_NAME }}
    fi
```

Notice that we first check if the branch exists. If it does, we do nothing. Otherwise we create it and pass the `--wait` flag.

This is useful when running in CI, as the workflow may run multiple times and you’ll want the branch ready before creating roles or running migrations.

New branches created this way start empty (no schema or data). To create a branch that includes schema and data, restore from a [backup](../backups.md) with `--restore`. See [Branching](../branching.md).

### Create a role for a branch

You can use `pscale role create` to generate [credentials](../connecting/roles.md) for your database branch.

```yaml
- name: Generate role for branch
  env:
    PLANETSCALE_SERVICE_TOKEN_ID: ${{ secrets.PLANETSCALE_SERVICE_TOKEN_ID }}
    PLANETSCALE_SERVICE_TOKEN: ${{ secrets.PLANETSCALE_SERVICE_TOKEN }}
  run: |
    response=$(pscale role create ${{ secrets.PLANETSCALE_DATABASE_NAME }} ${{ env.PSCALE_BRANCH_NAME }} "github-actions" \
      --inherited-roles postgres \
      --ttl 1h \
      -f json \
      --org ${{ secrets.PLANETSCALE_ORG_NAME }})

    id=$(echo "$response" | jq -r '.id')
    host=$(echo "$response" | jq -r '.access_host_url')
    username=$(echo "$response" | jq -r '.username')
    password=$(echo "$response" | jq -r '.password')
    database_name=$(echo "$response" | jq -r '.database_name')

    # Set the role ID, allows us to later delete it if wanted.
    echo "ROLE_ID=$id" >> $GITHUB_ENV

    # Create the DATABASE_URL
    database_url="postgresql://$username:$password@$host:5432/$database_name?sslmode=verify-full"
    echo "DATABASE_URL=$database_url" >> $GITHUB_ENV
    echo "::add-mask::$database_url"
- name: Use the DATABASE_URL in a subsequent step
  run: |
    echo "Using DATABASE_URL: $DATABASE_URL"
```

This example shows creating the role and getting back a response in JSON. The JSON is then parsed to create a `DATABASE_URL` which can be used in later steps.

`pscale role create` accepts a `--ttl` flag which lets you limit how long the role is valid. In CI, a short TTL (such as `1h`) is recommended so credentials expire automatically if cleanup does not run.

### Delete a role

When your workflow finishes, you can delete the temporary role with `pscale role delete`.

```yaml
- name: Delete role
  if: always()
  env:
    PLANETSCALE_SERVICE_TOKEN_ID: ${{ secrets.PLANETSCALE_SERVICE_TOKEN_ID }}
    PLANETSCALE_SERVICE_TOKEN: ${{ secrets.PLANETSCALE_SERVICE_TOKEN }}
  run: |
    pscale role delete ${{ secrets.PLANETSCALE_DATABASE_NAME }} ${{ env.PSCALE_BRANCH_NAME }} ${{ env.ROLE_ID }} \
      --force \
      --successor postgres \
      --org ${{ secrets.PLANETSCALE_ORG_NAME }}
```

`--force` skips the interactive confirmation prompt. `--successor postgres` reassigns any objects owned by the role before deletion.

### Delete a PlanetScale branch

You can clean up ephemeral CI branches with `pscale branch delete`.

```yaml
- name: Delete branch
  if: always()
  env:
    PLANETSCALE_SERVICE_TOKEN_ID: ${{ secrets.PLANETSCALE_SERVICE_TOKEN_ID }}
    PLANETSCALE_SERVICE_TOKEN: ${{ secrets.PLANETSCALE_SERVICE_TOKEN }}
  run: |
    pscale branch delete ${{ secrets.PLANETSCALE_DATABASE_NAME }} ${{ env.PSCALE_BRANCH_NAME }} \
      --force \
      --org ${{ secrets.PLANETSCALE_ORG_NAME }}
```

`--force` skips the interactive confirmation prompt so the command can run non-interactively in CI.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
