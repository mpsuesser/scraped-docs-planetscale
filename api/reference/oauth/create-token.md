---
url: https://planetscale.com/docs/api/reference/oauth/create-token
title: "Create Token"
description: ""
access_date: 2026-10-20T18:26:07.257Z
current_date: 2026-10-20T18:26:07.257Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Create or refresh OAuth token

> Exchange an authorization code for an access token or refresh an expired token

## Endpoint

```
POST https://auth.planetscale.com/oauth/token
```

This is the standard OAuth 2.0 token endpoint for creating and refreshing access tokens.

## Request Body

The request body should be sent as `application/x-www-form-urlencoded`.

### For authorization code exchange

**grant\_type** `string` *required*

Must be `authorization_code`

**code** `string` *required*

The authorization code received from the authorization flow

**redirect\_uri** `string` *required*

The redirect URI used in the authorization request

**client\_id** `string` *required*

Your OAuth application's client ID

**client\_secret** `string` *required*

Your OAuth application's client secret

### For token refresh

**grant\_type** `string` *required*

Must be `refresh_token`

**refresh\_token** `string` *required*

The refresh token from a previous token response

**client\_id** `string` *required*

Your OAuth application's client ID

**client\_secret** `string` *required*

Your OAuth application's client secret

## Response

### Success Response (200 OK)

```json theme={null}
{
  "access_token": "<PLANETSCALE_OAUTH_ACCESS_TOKEN>",
  "token_type": "Bearer",
  "expires_in": 2592000,
  "refresh_token": "<PLANETSCALE_OAUTH_REFRESH_TOKEN>",
  "scope": "read_user read_databases"
}
```

**access\_token** `string`

The OAuth access token to use for API requests

**token\_type** `string`

Will always be "Bearer"

**expires\_in** `integer`

Number of seconds until the access token expires

**refresh\_token** `string`

Token to use for refreshing the access token when it expires

**scope** `string`

Space-separated list of scopes granted to this token

## Example

```bash theme={null}
# Exchange authorization code for access token
curl -X POST https://auth.planetscale.com/oauth/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=authorization_code" \
  -d "code=YOUR_AUTHORIZATION_CODE" \
  -d "redirect_uri=https://your-app.com/callback" \
  -d "client_id=YOUR_CLIENT_ID" \
  -d "client_secret=YOUR_CLIENT_SECRET"

# Refresh an access token
curl -X POST https://auth.planetscale.com/oauth/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=refresh_token" \
  -d "refresh_token=YOUR_REFRESH_TOKEN" \
  -d "client_id=YOUR_CLIENT_ID" \
  -d "client_secret=YOUR_CLIENT_SECRET"
```

## Error Responses

### 400 Bad Request

Invalid request parameters (e.g., missing required fields, invalid grant\_type).

### 401 Unauthorized

Invalid client credentials (client\_id or client\_secret is incorrect).

### 400 Invalid Grant

The authorization code or refresh token is invalid, expired, or already used.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
