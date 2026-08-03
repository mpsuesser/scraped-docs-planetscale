---
url: https://planetscale.com/docs/security/teams
title: "Teams"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Overview

This allows you to easily manage administrator access to one or multiple databases all in one spot.

## Create and manage Teams

You can manage teams straight from your PlanetScale dashboard by going to “ **Settings** ” > “ **Teams** ”.

Only [Organization Administrators](access-control.md#organization-administrator) can create and manage Teams.

Once you add databases to a team, any members on that team will have [Database Administrator access](access-control.md#database-level-permissions) to those databases. Review our [Access control documentation](access-control.md) to understand the full scope of Database Administrator access.

### Create a team

### Add members

### Add databases

Now, when you go to the Settings page for any databases you’ve added to a team, you’ll also be able to view and revoke access straight from the database Administrators page.

![Dashboard UI - Database Administrators settings page](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/teams/settings.png?w=2500&fit=max&auto=format&n=GA0k5H-MolPvBjDk&q=85&s=0c3679aaffe66316146dc7a6effea7a3)

Dashboard UI - Database Administrators settings page

### Remove members and databases

To remove a member from a team, find their name in the member list and click “ **Remove** ”. At this time, you’ll also be able to delete any passwords this member has created to ensure you’ve completely revoked their access to the database.

![Dashboard UI - Delete a member from a team](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/teams/member.png?w=2500&fit=max&auto=format&n=GA0k5H-MolPvBjDk&q=85&s=4e8cf1533862c8a6244c38ef466ff440)

Dashboard UI - Delete a member from a team

To remove a database from a team, click the “ **x** ” next to the database name under “Administrator permissions”. This will remove database administrator access for all members of the team.

![Dashboard UI - Delete a database from a team](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/teams/database.png?w=2500&fit=max&auto=format&n=GA0k5H-MolPvBjDk&q=85&s=a9753bd84b37d9afdad19cca7d163f74)

Dashboard UI - Delete a database from a team

## Directory Sync with Teams

If you have [SSO with Directory Sync](sso.md#directory-sync) enabled, all Teams will be managed by your Directory Sync directory. You can add and remove database access to teams, but member management must be done through your directory.

![Dashboard UI - Directory-managed Teams page](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/sso/managed.png?w=2500&fit=max&auto=format&n=GA0k5H-MolPvBjDk&q=85&s=8c0b03673194997294972778ea13b2c2)

Dashboard UI - Directory-managed Teams page

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
