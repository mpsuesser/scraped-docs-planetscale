---
url: https://planetscale.com/docs/api/reference/remove_organization_team_member
title: "Remove_organization_team_member"
description: ""
access_date: 2026-09-18T17:45:47.196Z
current_date: 2026-09-18T17:45:47.196Z
---

DELETE

/

organizations

/

{organization}

/

teams

/

{team}

/

members

/

{id}

#### AuthorizationsAuthorization

string

header

required

The access token received from the authorization server in the OAuth 2.0 flow.

#### Path Parametersorganization

string

required

The name of the organizationteam

string

required

The slug of the teamid

string

required

The ID of the team membership or the ID of the member to remove

#### Query Parametersdelete\_passwords

boolean

Whether to delete the member's passwords created through this team

#### Response

Member removed successfully. Note: SSO-managed teams cannot have members removed.
