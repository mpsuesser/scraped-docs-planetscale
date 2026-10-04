---
url: https://planetscale.com/docs/api/reference/delete_password
title: "Delete_password"
description: ""
access_date: 2026-10-04T19:56:22.595Z
current_date: 2026-10-04T19:56:22.595Z
---

DELETE

/

organizations

/

{organization}

/

databases

/

{database}

/

branches

/

{branch}

/

passwords

/

{id}

This deletes a **Vitess/MySQL** branch password created with [Create a password](create_password.md), and immediately disconnects any clients using it. To delete a **Postgres** role, use [Delete a role](delete_role.md) instead.

#### AuthorizationsAuthorization

string

header

required

The access token received from the authorization server in the OAuth 2.0 flow.

FlowAuthorization Code

Authorization URL

https://app.planetscale.com/oauth/authorize

Token URL

https://auth.planetscale.com/oauth/token

#### Path Parametersorganization

string

required

The name of the organization the password belongs todatabase

string

required

The name of the database the password belongs tobranch

string

required

The name of the branch the password belongs toid

string

required

The ID of the password

#### Response

Deletes the password
