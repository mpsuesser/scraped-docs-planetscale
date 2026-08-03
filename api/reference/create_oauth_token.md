---
url: https://planetscale.com/docs/api/reference/create_oauth_token
title: "Create_oauth_token"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

POST

/

organizations

/

{organization}

/

oauth-applications

/

{id}

/

token

**This endpoint is deprecated.** Please use the new standard OAuth 2.0 token endpoint at `POST https://auth.planetscale.com/oauth/token` instead. See [Create OAuth token](oauth/create-token.md) for updated documentation.

#### AuthorizationsAuthorization

string

header

required

The access token received from the authorization server in the OAuth 2.0 flow.

#### Path Parametersorganization

string

required

The name of the organization the OAuth application belongs toid

string

required

The ID of the OAuth application

#### Body

application/jsonclient\_id

string

required

The OAuth application's client IDclient\_secret

string

required

The OAuth application's client secretgrant\_type

enum<string>

required

Whether an OAuth grant code or a refresh token is being exchanged for an OAuth token

Available options:

`authorization_code`,

`refresh_token`code

string

The OAuth grant code provided to your OAuth application's redirect URI. Required when grant\_type is authorization\_coderedirect\_uri

string

The OAuth application's redirect URI. Required when grant\_type is authorization\_coderefresh\_token

string

The refresh token from the original OAuth token grant. Required when grant\_type is refresh\_token

#### Response

Returns the created OAuth tokenid

string

required

The ID of the service tokenname

string | null

required

The name of the service tokendisplay\_name

string

required

The display name of the service token

The image source for the avatar of the service tokencreated\_at

string

required

When the service token was createdupdated\_at

string

required

When the service token was last updatedexpires\_at

string | null

required

When the service token will expirelast\_used\_at

string | null

required

When the service token was last usedactor\_id

string | null

required

The ID of the actor on whose behalf the service token was createdactor\_display\_name

string | null

required

The name of the actor on whose behalf the service token was createdactor\_type

string | null

required

The type of the actor on whose behalf the service token was createdtoken

string | null

The plaintext token. Available only after create.plain\_text\_refresh\_token

string | null

The plaintext refresh token. Available only after create.service\_token\_accesses

object\[\] | null

Show child attributesoauth\_accesses\_by\_resource

object | null

Show child attributes
