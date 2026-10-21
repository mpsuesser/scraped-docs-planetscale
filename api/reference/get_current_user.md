---
url: https://planetscale.com/docs/api/reference/get_current_user
title: "Get_current_user"
description: ""
access_date: 2026-10-21T09:50:28.718Z
current_date: 2026-10-21T09:50:28.718Z
---

GET

/

user

#### AuthorizationsAuthorization

string

header

required

The access token received from the authorization server in the OAuth 2.0 flow.

#### Response

Returns the user associated with this service tokenid

string

required

The ID of the userdisplay\_name

string

required

The display name of the username

string | null

required

The name of the useremail

string

required

The email of the user

The URL source of the user's avatarcreated\_at

string

required

When the user was createdupdated\_at

string

required

When the user was last updatedtwo\_factor\_auth\_configured

boolean

required

Whether or not the user has configured two factor authenticationdefault\_organization

object | null

Show child attributessso

boolean | null

Whether or not the user is managed by SSO.managed

boolean | null

Whether or not the user is managed by an authentication provider.directory\_managed

boolean | null

Whether or not the user is managed by a SSO directory.email\_verified

boolean | null

Whether or not the user is verified by email.
