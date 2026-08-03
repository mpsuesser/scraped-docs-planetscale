---
url: https://planetscale.com/docs/api/reference/oauth/revoke-token
title: "Revoke Token"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Endpoint

```text
POST https://auth.planetscale.com/oauth/revoke
```

This is the standard OAuth 2.0 token revocation endpoint. Once revoked, a token can no longer be used to access the API.

## Request Body

The request body should be sent as `application/x-www-form-urlencoded`.string

required

The access token or refresh token to revokestring

required

Your OAuth application’s client IDstring

required

Your OAuth application’s client secret

## Response

### Success Response (200 OK)

Returns an empty response with status 200 when the token is successfully revoked.

```json
{}
```

## Example

```shellscript
curl -X POST https://auth.planetscale.com/oauth/revoke \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "token=YOUR_ACCESS_TOKEN" \
  -d "client_id=YOUR_CLIENT_ID" \
  -d "client_secret=YOUR_CLIENT_SECRET"
```

## Error Responses

### 401 Unauthorized

Invalid client credentials (client\_id or client\_secret is incorrect).

## Notes

- Revoking an access token does not automatically revoke its associated refresh token
- You can revoke either access tokens or refresh tokens using this endpoint
- Once revoked, the token cannot be used again and cannot be un-revoked
