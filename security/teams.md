---
url: https://planetscale.com/docs/security/teams
title: "Teams"
description: ""
access_date: 2026-09-02T21:57:53.710Z
current_date: 2026-09-02T21:57:53.710Z
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

### Remove members and databases

To remove a member from a team, find their name in the member list and click “ **Remove** ”. At this time, you’ll also be able to delete any passwords this member has created to ensure you’ve completely revoked their access to the database.

To remove a database from a team, click the “ **x** ” next to the database name under “Administrator permissions”. This will remove database administrator access for all members of the team.

## Directory Sync with Teams

If you have [SSO with Directory Sync](sso.md#directory-sync) enabled, all Teams will be managed by your Directory Sync directory. You can add and remove database access to teams, but member management must be done through your directory.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
