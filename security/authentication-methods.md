---
url: https://planetscale.com/docs/security/authentication-methods
title: "Authentication Methods"
description: ""
access_date: 2026-11-09T19:25:59.204Z
current_date: 2026-11-09T19:25:59.204Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Authentication methods

> There are three ways to authenticate with PlanetScale: _email address and password_, _single sign-on_, and _OAuth via GitHub_.

## Overview

Let's break down how each of these work.

## Email address and password

This is the only authentication mechanism where PlanetScale maintains user credentials.

Additionally, users can opt to configure [two-factor authentication (2FA)](https://planetscale.com/docs/security/multi-factor-authentication.md). This option requires **something you know** *(i.e. your password)* and **something you have** *(i.e. recovery codes)*.

## Single sign-on

Users can authenticate with their chosen corporate identity provider *(i.e. Okta)* instead of maintaining passwords with PlanetScale.

Once [SSO](https://planetscale.com/docs/security/sso.md) is enabled for an `organization`, all members are redirected through that identity provider's authentication flow. Moving forward, they must pass through SSO to access their PlanetScale account.

## OAuth via GitHub

Users can authenticate with PlanetScale using their GitHub account.

> [!WARNING]
>
> PlanetScale doesn't maintain the passwords for these accounts. Losing access to your GitHub account prevents accessing
> your PlanetScale account.

> [!NOTE]
>
> Enabling SSO removes OAuth access for all members of your *organization*, meaning they will no longer be able
> to sign in with their GitHub credentials.

### Switch to email and password

You can stop signing in with GitHub and sign in with your email address and a password instead.

**Step 1**

Go to your [PlanetScale account settings](https://app.planetscale.com/settings/account) page.

**Step 2**

In the **Sign in with GitHub** section, click **"Disconnect GitHub"**.

**Step 3**

Click **"Confirm on GitHub"** and approve the sign-in on GitHub.

**Step 4**

Enter and confirm a new password, then click **"Disconnect GitHub"**.

Signing in with GitHub stops working for your account, and you are signed out of PlanetScale in your other browsers. Your account then works like any other email and password account, including [multi-factor authentication](https://planetscale.com/docs/security/multi-factor-authentication.md).

> [!NOTE]
>
> Accounts that belong to an organization using SSO can't disconnect GitHub.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
