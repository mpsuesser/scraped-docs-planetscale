---
url: https://planetscale.com/docs/vitess/schema-changes/safe-migrations
title: "Safe Migrations"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Safe migrations

> Safe migrations is an optional but highly recommended feature for branches in PlanetScale. With safe migrations enabled on a branch, you’ll gain zero-downtime schema migrations, schema reverts, and protection against accidental schema changes. The safe migrations setting is recommended for all production database branches to prevent downtime and unintentional data loss during schema migrations.

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

<PlatformAvailability current="vitess" />

## Zero-downtime schema migrations

Safe migrations enable the [PlanetScale workflow](../best-practices.md) on a given branch and allow your team to create deploy requests to merge schema changes into that branch. When changes are merged using deploy requests, a ghost table will be created with the desired schema changes. Your data will be continuously synchronized with that table until you decide to apply the changes.

## Schema revert

Safe migrations and deploy requests provide the option to quickly [revert schema changes](deploy-requests.md#revert-a-schema-change) if you discover that they are not compatible with your application. With schema revert enabled for a database, the old table is retained. You’re provided a 30-minute window where data will still be synchronized as writes occur on the new table. If you decide to revert your changes, the status of the two tables is flipped, bringing the former table back.

## Protection against accidental schema changes

To prevent accidental changes to the database schema, which may cause downtime, safe migrations enforce the use of [branching](branching.md) and [deploy requests](deploy-requests.md). This requires that changes be made safely and allows all team members to check and comment on schema changes before they are applied.

With safe migrations enabled, Data Definition Language (DDL) statements issued to branches with safe migrations enabled will automatically be rejected by PlanetScale. Any `CREATE`, `ALTER`, or `DELETE` commands, whether sent using the PlanetScale built-in console, terminal, or MySQL GUI, will fail when we receive them.

## Staging branches

You can use a development branch with safe migrations enabled to set up a workflow with a “staging” branch. First, make sure you have safe migration enabled for your main production branch. Then, create a “staging” branch with your main production branch as the base and turn on safe migrations. All new branches created for development can use this “staging” branch as the base branch.

You can then open a deploy request against either the main production or “staging” branch. Once it is deployed to “staging,” you can open a deploy request against the main production branch. The main production branch must be set as the default branch (found in your database's “Settings” page) to open a deploy request against it.

<Note>
  In this setup, the “staging” branch is still a development branch. Compared to your production branch, it will have reduced resources, similar to other development branches.
</Note>

<Frame>
  <img src="https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/safe-migrations/branches-with-staging-branch.jpg?fit=max&auto=format&n=GA0k5H-MolPvBjDk&q=85&s=f7ddf5ad9f99a86f2121909ef57e6783" alt="View of the Branches tab with main <- staging <- dev branches" width="2666" height="1068" data-path="images/assets/docs/concepts/safe-migrations/branches-with-staging-branch.jpg" />
</Frame>

## How to enable safe migrations

Safe migrations can be enabled using the PlanetScale dashboard or the pscale CLI.

### Using the PlanetScale dashboard

To enable safe migrations on a branch, select the branch you want to modify from the branch dropdown and click the **”cog”** in the upper right of the infrastructure card on the ”**Dashboard**” tab of the database.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/safe-migrations/production-branch-card-with-sm-disabled-2.png?fit=max&auto=format&n=GA0k5H-MolPvBjDk&q=85&s=e69aef44ce888729b22a81aa59cca2d2" alt="The production branch UI card." width="1096" height="801" data-path="images/assets/docs/concepts/safe-migrations/production-branch-card-with-sm-disabled-2.png" />
</Frame>

In the modal, toggle the option labeled **”Enable safe migrations”**, then click the **”Enable safe migrations”** button to save and close the modal.

The UI card will reflect the status of the safe migrations for that branch.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/safe-migrations/production-branch-card-with-sm-enabled-2.png?fit=max&auto=format&n=GA0k5H-MolPvBjDk&q=85&s=7cbcf6b5ea23eeceacc43d1443bf9553" alt="Branch UI card with safe migrations enabled." width="1074" height="792" data-path="images/assets/docs/concepts/safe-migrations/production-branch-card-with-sm-enabled-2.png" />
</Frame>

You can also access the same settings from the **”cog”** on a branch overview page (from the **”Branches”** tab, then select the branch you want to view or modify).

### Using the pscale CLI

To enable safe migrations on a branch using the pscale CLI, use the following command in your terminal:

```
pscale branch safe-migrations enable <DATABASE\_NAME> <BRANCH\_NAME>
```

## How to disable safe migrations

There are two ways to disable safe migrations: the PlanetScale dashboard and the CLI.

### Using the PlanetScale dashboard

To disable safe migrations, click the **”cog”** in the upper right of the infrastructure card on the ”**Dashboard**” tab of the database.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/safe-migrations/prod-card-cog-2.png?fit=max&auto=format&n=GA0k5H-MolPvBjDk&q=85&s=da093e115a9159f7d76cad61ca1ecc11" alt="Branch UI with enabled with cog highlighted." width="1074" height="792" data-path="images/assets/docs/concepts/safe-migrations/prod-card-cog-2.png" />
</Frame>

In the modal, toggle the option labeled **”Enable safe migrations,”** then click the **”Disable safe migrations”** button to save and close the modal.

You can also access the same settings from the **”cog”** on a branch overview page (from the **”Branches”** tab, then select the branch you want to view or modify).

### Using the pscale CLI

To disable safe migrations on a branch using the pscale CLI, use the following command in your terminal:

```
pscale branch safe-migrations disable <DATABASE\_NAME> <BRANCH\_NAME>
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
