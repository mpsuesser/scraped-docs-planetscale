---
url: https://planetscale.com/docs/api/reference/oauth/create-token
title: "Create Token"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Endpoint

```text
POST https://auth.planetscale.com/oauth/token
```

This is the standard OAuth 2.0 token endpoint for creating and refreshing access tokens.

## Request Body

The request body should be sent as `application/x-www-form-urlencoded`.

### For authorization code exchangestring

required

Must be `authorization_code`string

required

The authorization code received from the authorization flowstring

required

The redirect URI used in the authorization requeststring

required

Your OAuth application’s client IDstring

required

Your OAuth application’s client secret

### For token refreshstring

required

Must be `refresh_token`string

required

The refresh token from a previous token responsestring

required

Your OAuth application’s client IDstring

required

Your OAuth application’s client secret

## Response

### Success Response (200 OK)

```json
{
  "access_token": "<PLANETSCALE_OAUTH_ACCESS_TOKEN>",
  "token_type": "Bearer",
  "expires_in": 2592000,
  "refresh_token": "<PLANETSCALE_OAUTH_REFRESH_TOKEN>",
  "scope": "read_user read_databases"
}
```string

The OAuth access token to use for API requestsstring

Will always be “Bearer”integer

Number of seconds until the access token expiresstring

Token to use for refreshing the access token when it expiresstring

Space-separated list of scopes granted to this token

## Example

```shellscript
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
