---
url: https://planetscale.com/docs/api/reference/delete_role
title: "Delete_role"
description: ""
access_date: 2026-08-06T03:13:30.576Z
current_date: 2026-08-06T03:13:30.576Z
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

roles

/

{id}

This deletes a **Postgres role**. To delete a **Vitess/MySQL** branch password, use [Delete a password](delete_password.md) instead. Roles created with [Create role credentials](create_role.md) that own objects, have extra permissions, or created other roles require a successor — see [Deleting a role](../../postgres/connecting/roles.md#deleting-a-role).

#### AuthorizationsAuthorization

string

header

required

The access token received from the authorization server in the OAuth 2.0 flow.

#### Path Parametersorganization

string

required

Organization name slug from `list_organizations`. Example: `acme`.database

string

required

Database name slug from `list_databases`. Example: `app-db`.branch

string

required

Branch name from `list_branches`. Example: `main`.id

string

required

The ID of the role

#### Query Parameterssuccessor

string

The optional role to reassign ownership to before dropping. Accepts the role's ID, or its username with or without the branch ID suffix.

#### Response

Deletes the role credentials
