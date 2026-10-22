---
url: https://planetscale.com/docs/security/teams
title: "Teams"
description: ""
access_date: 2026-10-22T18:21:49.469Z
current_date: 2026-10-22T18:21:49.469Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Teams

> PlanetScale allows you to create teams within organizations.

## Overview

This allows you to easily manage administrator access to one or multiple databases all in one spot.

## Create and manage Teams

You can manage teams straight from your PlanetScale dashboard by going to "**Settings**" > "**Teams**".

> [!NOTE]
>
> Only [Organization Administrators](https://planetscale.com/docs/security/access-control.md#organization-administrator) can create and
> manage Teams.

Once you add databases to a team, any members on that team will have [Database Administrator access](https://planetscale.com/docs/security/access-control.md#database-level-permissions) to those databases. Review our [Access control documentation](https://planetscale.com/docs/security/access-control.md) to understand the full scope of Database Administrator access.

### Create a team

**Step 1**

On your PlanetScale overview page, click "**Settings**".

**Step 2**

Click "**Teams**" in the left nav.

![Dashboard UI - Create a PlanetScale team](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/teams/create.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=807359c494a2da5af3c5df900cb25361)

**Step 3**

Give your team a name and description (*optional*).

**Step 4**

Click "**Create team**".

### Add members

**Step 1**

Click "**Add a member**".

**Step 2**

You'll see a list of your Organization members. Select the member(s) one at a time that you wish to add to the team.

### Add databases

**Step 1**

Click "**Add databases**".

**Step 2**

Select the databases you want this team to have [database administrator access](https://planetscale.com/docs/security/access-control.md#database-level-permissions) to.

Now, when you go to the Settings page for any databases you've added to a team, you'll also be able to view and revoke access straight from the database Administrators page.

![Dashboard UI - Database Administrators settings page](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/teams/settings.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=b8c11f131c83aec215dd8da1d5e84567)

### Remove members and databases

To remove a member from a team, find their name in the member list and click "**Remove**". At this time, you'll also be able to delete any passwords this member has created to ensure you've completely revoked their access to the database.

![Dashboard UI - Delete a member from a team](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/teams/member.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=a782f3091086aeed095c33ffc3b511e0)

To remove a database from a team, click the "**x**" next to the database name under "Administrator permissions". This will remove database administrator access for all members of the team.

![Dashboard UI - Delete a database from a team](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/teams/database.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=8976a32f9c1f8c2fe97d81e095813b39)

## Directory Sync with Teams

If you have [SSO with Directory Sync](https://planetscale.com/docs/security/sso.md#directory-sync) enabled, all Teams will be managed by your Directory Sync directory. You can add and remove database access to teams, but member management must be done through your directory.

![Dashboard UI - Directory-managed Teams page](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/sso/managed.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=f36262759a8dc656e592aa960eaaa77e)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
