---
url: https://planetscale.com/docs/vitess/tutorials/github-actions
title: "Github Actions"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
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

<PlatformAvailability current="vitess" postgres="/postgres/integrations/github-actions" />

See our [tech talk on Databases + CI/CD](https://planetscale.com/blog/databases-ci-cd-pipeline) to see pscale + GitHub Actions
used in a real application.

With GitHub Actions, you can automate the creation of branches and deploy requests all within your CI workflow.

<Columns cols={2}>
  <Card title="Getting Started" href="#getting-started" icon="rocket-launch" horizontal />

  <Card title="Authentication" href="#authentication" icon="key" horizontal />

  <Card title="Convert GitHub branch name to PlanetScale branch name" href="#convert-github-branch-name-to-planetscale-branch-name" icon="code-branch" horizontal />

  <Card title="Create a PlanetScale branch" href="#create-a-planetscale-branch" icon="code-branch" horizontal />

  <Card title="Create a password for a branch" href="#create-a-password-for-a-branch" icon="key" horizontal />

  <Card title="Open a deploy request" href="#open-a-deploy-request" icon="code-branch" horizontal />

  <Card title="Get deploy request by branch name" href="#get-deploy-request-by-branch-name" icon="code-branch" horizontal />

  <Card title="Get deploy request diff and comment on pull request" href="#get-deploy-request-diff-and-comment-on-pull-request" icon="code-branch" horizontal />

  <Card title="Check for dropped columns" href="#check-for-dropped-columns" icon="code-branch" horizontal />

  <Card title="Submit a deploy request by branch name" href="#submit-a-deploy-request-by-branch-name" icon="code-branch" horizontal />
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

This is useful when running in CI, as the workflow may run multiple times and you'll want the branch ready if you are running schema migrations immediately after creating the branch.

### Create a password for a branch

You can use `pscale password create` to generate credentials for your database branch.

```yaml expandable theme={null}
- name: Generate password for branch
  env:
    PLANETSCALE_SERVICE_TOKEN_ID: ${{ secrets.PLANETSCALE_SERVICE_TOKEN_ID }}
    PLANETSCALE_SERVICE_TOKEN: ${{ secrets.PLANETSCALE_SERVICE_TOKEN }}
  run: |
    response=$(pscale password create ${{ secrets.PLANETSCALE_DATABASE_NAME }} ${{ env.PSCALE_BRANCH_NAME }} "" -f json --org ${{ secrets.PLANETSCALE_ORG_NAME }})

    id=$(echo "$response" | jq -r '.id')
    host=$(echo "$response" | jq -r '.access_host_url')
    username=$(echo "$response" | jq -r '.username')
    password=$(echo "$response" | jq -r '.plain_text')
    ssl_mode="verify_identity"  # Assuming a default value for ssl_mode
    ssl_ca="/etc/ssl/certs/ca-certificates.crt"  # Assuming a default value for ssl_ca

    # Set the password ID, allows us to later delete it if wanted.
    echo "PASSWORD_ID=$id" >> $GITHUB_ENV

    # Create the DATABASE_URL
    database_url="mysql://$username:$password@$host/${{ secrets.PLANETSCALE_DATABASE_NAME }}?sslmode=$ssl_mode&sslca=$ssl_ca"
    echo "DATABASE_URL=$database_url" >> $GITHUB_ENV
    echo "::add-mask::$DATABASE_URL"
- name: Use the DATABASE_URL in a subsequent step
  run: |
    echo "Using DATABASE_URL: $DATABASE_URL"
```

This example shows creating the password and getting back a response in json. The json is then parsed to create a `DATABASE_URL` which can be used in later steps.

<Note>
  `pscale password create` can also accept a `ttl` flag which lets you limit the number of minutes the password is valid for.
</Note>

### Open a deploy request

You can use `pscale deploy-request create` to open a new deploy request from GitHub Actions.
This can be useful after running migrations against a branch.

```yaml theme={null}
- name: Open DR if migrations
  env:
    PLANETSCALE_SERVICE_TOKEN_ID: ${{ secrets.PLANETSCALE_SERVICE_TOKEN_ID }}
    PLANETSCALE_SERVICE_TOKEN: ${{ secrets.PLANETSCALE_SERVICE_TOKEN }}
  run: pscale deploy-request create ${{ secrets.PLANETSCALE_DATABASE_NAME }} ${{ env.PSCALE_BRANCH_NAME }}
```

### Get deploy request by branch name

You can use `pscale deploy-requests show` to grab the latest deploy request by branch name.

This can be useful when deploying your application. You can first check if there are any deploy requests open for the branch being deployed. If there are, you can
trigger the deploy request to run before you deploy your application.

```yaml theme={null}
- name: Get Deploy Requests
  env:
    PLANETSCALE_SERVICE_TOKEN_ID: ${{ secrets.PLANETSCALE_SERVICE_TOKEN_ID }}
    PLANETSCALE_SERVICE_TOKEN: ${{ secrets.PLANETSCALE_SERVICE_TOKEN }}
  run: |
    deploy_request_number=$(pscale deploy-request show ${{ secrets.PLANETSCALE_DATABASE_NAME }} ${{ env.PSCALE_BRANCH_NAME }} --org ${{ secrets.PLANETSCALE_ORG_NAME }} -f json | jq -r '.number')
    echo "DEPLOY_REQUEST_NUMBER=$deploy_request_number" >> $GITHUB_ENV
```

This example also makes the deploy request number available as an `env` var so that it can be used in later steps.

### Get deploy request diff and comment on pull request

We can use `pscale deploy-request diff` to see the full schema diff of a deploy request.

This example is useful when combined with opening a deploy request for a git branch. You can then automatically comment the diff back to the GitHub pull request.

```yaml theme={null}
- name: Comment on PR
  env:
    PLANETSCALE_SERVICE_TOKEN_ID: ${{ secrets.PLANETSCALE_SERVICE_TOKEN_ID }}
    PLANETSCALE_SERVICE_TOKEN: ${{ secrets.PLANETSCALE_SERVICE_TOKEN }}
  run: |
    echo "Deploy request opened: https://app.planetscale.com/${{ secrets.PLANETSCALE_ORG_NAME }}/${{ secrets.PLANETSCALE_DATABASE_NAME }}/deploy-requests/${{ env.DEPLOY_REQUEST_NUMBER }}" >> migration-message.txt
    echo "" >> migration-message.txt
    echo "\`\`\`diff" >> migration-message.txt
    pscale deploy-request diff ${{ secrets.PLANETSCALE_DATABASE_NAME }} ${{ env.DEPLOY_REQUEST_NUMBER }} --org ${{ secrets.PLANETSCALE_ORG_NAME }} -f json | jq -r '.[].raw' >> migration-message.txt
    echo "\`\`\`" >> migration-message.txt
- name: Comment PR - db migrated
  uses: thollander/actions-comment-pull-request@v2
  with:
    filePath: migration-message.txt
```

This writes the diff to the `migration-message.txt` file. And then creates a comment on the pull request that triggered the workflow.

### Check for dropped columns

PlanetScale sets a `can_drop_data` boolean for any schema change that drop a column or table. We can make use of this to emit a warning into our pull requests.

In this example, we first wait for the deployment check to be `ready`. During this time, PlanetScale is examining the schema change, verifying that it is safe and
generating the DDL statements to make the change. Once it's done, we then use this information to put a comment on the deploy request with tips on how to deploy it safely.

```yaml expandable theme={null}
- name: Check deployment state
    id: check-state
    env:
      PLANETSCALE_SERVICE_TOKEN_ID: ${{ secrets.PLANETSCALE_SERVICE_TOKEN_ID }}
      PLANETSCALE_SERVICE_TOKEN: ${{ secrets.PLANETSCALE_SERVICE_TOKEN }}
    run: |
      for i in {1..10}; do
        deployment_state=$(pscale deploy-request show ${{ secrets.PLANETSCALE_ORG_NAME }} ${{ env.PSCALE_BRANCH_NAME }} --org ${{ secrets.PLANETSCALE_ORG_NAME }} --format json | jq -r '.deployment_state')
        echo "Deployment State: $deployment_state"

        if [ "$deployment_state" = "ready" ]; then
          echo "Deployment state is ready. Continuing."
          break
        fi

        echo "Deployment state is not ready. Waiting 2 seconds before checking again."
        sleep 2
      done
  - name: Comment PR - db migrated
    if: ${{ env.DR_OPENED }}
    env:
      PLANETSCALE_SERVICE_TOKEN_ID: ${{ secrets.PLANETSCALE_SERVICE_TOKEN_ID }}
      PLANETSCALE_SERVICE_TOKEN: ${{ secrets.PLANETSCALE_SERVICE_TOKEN }}
    run: |
      deploy_data=$(pscale api organizations/${{ secrets.PLANETSCALE_ORG_NAME }}/databases/${{ secrets.PLANETSCALE_DATABASE_NAME }}/deploy-requests/${{ env.DEPLOY_REQUEST_NUMBER }}/deployment --org planetscale)
      can_drop_data=$(echo "$deploy_data" | jq -r '.deploy_operations[] | select(.can_drop_data == true) | .can_drop_data')

      echo "Deploy request opened: https://app.planetscale.com/${{ secrets.PLANETSCALE_ORG_NAME }}/${{ secrets.PLANETSCALE_DATABASE_NAME }}/deploy-requests/${{ env.DEPLOY_REQUEST_NUMBER }}" >> migration-message.txt
      echo "" >> migration-message.txt

      if [ "$can_drop_data" = "true" ]; then
        echo ":rotating_light: You are dropping a column. Before running the migration make sure to do the following:" >> migration-message.txt
        echo "" >> migration-message.txt

        echo "1. [ ] Deploy app changes to ensure the column is no longer being used." >> migration-message.txt
        echo "2. [ ] Once you've verified it's no used, run the deploy request." >> migration-message.txt
        echo "" >> migration-message.txt
      else
        echo "When adding to the schema, the Deploy Request must be run **before** the code is deployed." >> migration-message.txt
        echo "Please ensure your schema changes are compatible with the application code currently running in production." >> migration-message.txt
        echo "" >> migration-message.txt

        echo "1. [ ] Successfully run the Deploy Request" >> migration-message.txt
        echo "2. [ ] Deploy this PR" >> migration-message.txt
        echo "" >> migration-message.txt
      fi

      echo "\`\`\`diff" >> migration-message.txt
      pscale deploy-request diff ${{ secrets.PLANETSCALE_ORG_NAME }} ${{ env.DEPLOY_REQUEST_NUMBER }} -f json | jq -r '.[].raw' >> migration-message.txt
      echo "\`\`\`" >> migration-message.txt
  - name: Comment PR - db migrated
    uses: thollander/actions-comment-pull-request@v2
    if: ${{ env.DR_OPENED }}
    with:
      filePath: migration-message.txt
```

### Submit a deploy request by branch name

To trigger a deploy, we can use `pscale deploy-request deploy`. This command will accept either the deploy request number, or the name of the branch that the deploy request was created from.

When using with GitHub Actions, it's often easier to use the branch name.

The `--wait` flag will let the command run until the deployment is complete. This is important if you want your schema change to run before the next step in your workflow.

```yaml theme={null}
- name: Deploy schema migrations
  env:
    PLANETSCALE_SERVICE_TOKEN_ID: ${{ secrets.PLANETSCALE_SERVICE_TOKEN_ID }}
    PLANETSCALE_SERVICE_TOKEN: ${{ secrets.PLANETSCALE_SERVICE_TOKEN }}
  run: |
    pscale deploy-request deploy ${{ secrets.PLANETSCALE_DATABASE_NAME }} ${{ env.PSCALE_BRANCH_NAME }} --org ${{ secrets.PLANETSCALE_ORG_NAME }} --wait
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
