---
url: https://planetscale.com/docs/postgres/integrations/github-actions
title: "Github Actions"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale GitHub Actions

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

<PlatformAvailability current="postgres" vitess="/vitess/integrations/github-actions" />

With GitHub Actions, you can automate branch creation and role credentials within your CI workflow.

<Columns cols={2}>
  <Card title="Getting Started" href="#getting-started" icon="rocket-launch" horizontal />

  <Card title="Authentication" href="#authentication" icon="key" horizontal />

  <Card title="Convert GitHub branch name to PlanetScale branch name" href="#convert-github-branch-name-to-planetscale-branch-name" icon="code-branch" horizontal />

  <Card title="Create a PlanetScale branch" href="#create-a-planetscale-branch" icon="code-branch" horizontal />

  <Card title="Create a role for a branch" href="#create-a-role-for-a-branch" icon="key" horizontal />

  <Card title="Delete a role" href="#delete-a-role" icon="key" horizontal />

  <Card title="Delete a PlanetScale branch" href="#delete-a-planetscale-branch" icon="code-branch" horizontal />
</Columns>

## Getting started

The best way to use PlanetScale within GitHub Actions is via the `pscale` CLI.

Use [`planetscale/setup-pscale-action`](https://github.com/planetscale/setup-pscale-action) to make pscale available within your GitHub Actions.

```yaml theme={null}
- name: Setup pscale
  uses: planetscale/setup-pscale-action@v1
```

The action works with Linux, Windows, and Mac runners. Once installed it will be added to your tool cache for subsequent runs.

## Authentication

Authentication for pscale is via service token environment variables.

You will need to [create a service token](../../cli/service-token.md). Make sure to give your service token the proper permissions to the database you'll be using in your workflow.

Add your `PLANETSCALE_SERVICE_TOKEN_ID` and `PLANETSCALE_SERVICE_TOKEN` to your [Actions secrets](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions#creating-secrets-for-a-repository).

In your Actions workflow, you will need to make the secrets available as environment variables.

```yaml theme={null}
- name: Run pscale command
  env:
    PLANETSCALE_SERVICE_TOKEN_ID: ${{ secrets.PLANETSCALE_SERVICE_TOKEN_ID }}
    PLANETSCALE_SERVICE_TOKEN: ${{ secrets.PLANETSCALE_SERVICE_TOKEN }}
  run: pscale database list
```

## Examples

The following examples show how to accomplish common Actions workflows with PlanetScale. In each example, notice that we use secrets for the service token, database and organization names.
You will need to set them in your GitHub repository to target your own database.

```
PLANETSCALE_ORG_NAME
PLANETSCALE_DATABASE_NAME
PLANETSCALE_SERVICE_TOKEN_ID
PLANETSCALE_SERVICE_TOKEN
```

### Convert GitHub branch name to PlanetScale branch name

PlanetScale branch names must be lowercase, alphanumeric characters and hyphens are allowed.

Since git branch names allow more possibilities, you can use the following code to transform a git branch name into an acceptable PlanetScale branch name.

```yaml theme={null}
- name: Rename branch name
  run: |
    branch_name=$(printf '%s' "$HEAD_REF" | tr -cd '[:alnum:]-' | tr '[:upper:]' '[:lower:]')
    echo "PSCALE_BRANCH_NAME=$branch_name" >> "$GITHUB_ENV"
  env:
    HEAD_REF: ${{ github.head_ref }}
```

This makes `${{ env.PSCALE_BRANCH_NAME }}` available for use in the rest of the workflow. This is useful to run in any scenario where you are creating
a PlanetScale branch to correspond with a git branch.

### Create a PlanetScale branch

You can use `pscale branch create` to create a branch that matches your GitHub branch name.

```yaml theme={null}
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

This is useful when running in CI, as the workflow may run multiple times and you'll want the branch ready before creating roles or running migrations.

<Note>
  New branches created this way start empty (no schema or data). To create a branch that includes schema and data, restore from a [backup](../backups.md) with `--restore`. See [Branching](../branching.md).
</Note>

### Create a role for a branch

You can use `pscale role create` to generate [credentials](../connecting/roles.md) for your database branch.

```yaml expandable theme={null}
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

<Note>
  `pscale role create` accepts a `--ttl` flag which lets you limit how long the role is valid. In CI, a short TTL (such as `1h`) is recommended so credentials expire automatically if cleanup does not run.
</Note>

### Delete a role

When your workflow finishes, you can delete the temporary role with `pscale role delete`.

```yaml theme={null}
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

```yaml theme={null}
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

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
