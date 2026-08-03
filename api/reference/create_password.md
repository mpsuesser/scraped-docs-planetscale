---
url: https://planetscale.com/docs/api/reference/create_password
title: "Create_password"
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

databases

/

{database}

/

branches

/

{branch}

/

passwords

This endpoint creates a **Vitess/MySQL branch password**. Branch passwords are the credential system for PlanetScale **Vitess/MySQL** databases. If your database is **Postgres**, create [role credentials](create_role.md) instead — Postgres databases do not have branch passwords.

The `plain_text` password returned in the response is shown **only once** and cannot be retrieved later. Record it securely as soon as the password is created. If it is lost, you must [create a new password](create_password.md) — renewing only extends expiration and does not reissue the secret.

**Cleanup:** delete a password with [Delete a password](delete_password.md). Deleting a password immediately disconnects any clients using it.

#### AuthorizationsAuthorization

string

header

required

The access token received from the authorization server in the OAuth 2.0 flow.

#### Path Parametersorganization

string

required

The name of the organization the password belongs todatabase

string

required

The name of the database the password belongs tobranch

string

required

The name of the branch the password belongs to

#### Body

application/jsonname

string

Optional name of the passwordrole

enum<string>

The database role of the password (i.e. admin)

Available options:

`reader`,

`writer`,

`admin`,

`readwriter`replica

boolean

Whether the password is for a read replicattl

integer

Time to live (in seconds) for the password. The password will be invalid when TTL has passedcidrs

string\[\]

List of IP addresses or CIDR ranges that can use this passworddirect\_vtgate

boolean

Whether the password connects directly to a VTGate

#### Response

Returns the new credentialsid

string

required

The ID for the passwordname

string

required

The display name for the passwordrole

enum<string>

required

The role for the password

Available options:

`reader`,

`writer`,

`admin`,

`readwriter`cidrs

string\[\] | null

required

List of IP addresses or CIDR ranges that can use this passwordcreated\_at

string

required

When the password was createddeleted\_at

string | null

required

When the password was deletedexpires\_at

string | null

required

When the password will expirelast\_used\_at

string | null

required

When the password was last used to execute a queryexpired

boolean

required

True if the credentials are expireddirect\_vtgate

boolean

required

True if the credentials connect directly to a vtgate, bypassing load balancersdirect\_vtgate\_addresses

string\[\]

required

The list of hosts in each availability zone providing direct access to a vtgatettl\_seconds

integer | null

required

Time to live (in seconds) for the password. The password will be invalid when TTL has passedaccess\_host\_url

string

required

The host URL for the passwordaccess\_host\_regional\_url

string

required

The regional host URLaccess\_host\_regional\_urls

string\[\]

required

The read-only replica host URLsactor

object | null

required

Show child attributesregion

object

required

Show child attributesusername

string

required

The username for the passwordplain\_text

string | null

required

The plaintext password. Null except in the response from the create endpoint.replica

boolean

required

Whether or not the password is for a read replicarenewable

boolean

required

Whether or not the password can be reneweddatabase\_branch

object

required

Show child attributes
