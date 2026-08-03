---
url: https://planetscale.com/docs/api/reference/get_oauth_token
title: "Get_oauth_token"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

GET

/

organizations

/

{organization}

/

oauth-applications

/

{application\_id}

/

tokens

/

{token\_id}

**This endpoint is deprecated.** Please use the new standard OAuth 2.0 token info endpoint at `GET https://auth.planetscale.com/oauth/token/info` instead. See [Get OAuth token info](oauth/token-info.md) for updated documentation.

#### AuthorizationsAuthorization

string

header

required

The access token received from the authorization server in the OAuth 2.0 flow.

#### Path Parametersorganization

string

required

The name of the organization the OAuth application belongs toapplication\_id

string

required

The ID of the OAuth applicationtoken\_id

string

required

The ID of the OAuth application token

#### Response

Returns an OAuth token that was issued on behalf of the OAuth applicationid

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
