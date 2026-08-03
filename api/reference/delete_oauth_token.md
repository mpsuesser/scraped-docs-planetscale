---
url: https://planetscale.com/docs/api/reference/delete_oauth_token
title: "Delete_oauth_token"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

DELETE

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

**This endpoint is deprecated.** Please use the new standard OAuth 2.0 revocation endpoint at `POST https://auth.planetscale.com/oauth/revoke` instead. See [Revoke OAuth token](oauth/revoke-token.md) for updated documentation.

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

Deletes an OAuth application's OAuth token
