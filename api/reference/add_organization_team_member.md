---
url: https://planetscale.com/docs/api/reference/add_organization_team_member
title: "Add_organization_team_member"
description: ""
access_date: 2026-09-18T08:03:39.049Z
current_date: 2026-09-18T08:03:39.049Z
---

POST

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

The slug of the team

#### Body

application/jsonuser\_id

string

required

The ID of the organization member to add to the team

#### Response

Returns the created team membershipid

string

required

The ID of the team membershipuser

object

required

Show child attributesactor

object

required

Show child attributescreated\_at

string

required

When the membership was createdupdated\_at

string

required

When the membership was last updatedpasswords

object\[\]

required

Show child attributes
