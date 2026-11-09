---
url: https://planetscale.com/docs/security/multi-factor-authentication
title: "Multi Factor Authentication"
description: ""
access_date: 2026-11-09T23:28:57.282Z
current_date: 2026-11-09T23:28:57.282Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Multi-factor authentication

> Multi-factor authentication (MFA) provides better safety for your databases and prevents unauthorized access to your user account. 

## Overview

MFA strengthens security by requiring two or more methods *(i.e. authentication factors)* to verify your identity.

PlanetScale allows users logging in with an email address and password to set MFA as a requirement for logging into the user account.

> [!NOTE]
>
> If you're authenticating via GitHub OAuth or [SSO](https://planetscale.com/docs/security/sso.md), MFA settings will not be displayed. To use MFA with an account that signs in with GitHub, first [switch to email and password](https://planetscale.com/docs/security/authentication-methods.md#switch-to-email-and-password).

### Authentication providers

PlanetScale supports login with a unique *time-based one-time password (TOTP)* that is generated for your user account by using TOTP apps such as [1Password](https://support.1password.com/one-time-passwords/), [Authy](https://help.twilio.com/articles/19753413823643), or [LastPass Authenticator](https://lastpass.com/auth/).

You can also add a passkey or a hardware security key. A passkey is saved in a password manager or on your device. A hardware security key is a small USB or NFC device, like a YubiKey, that you plug in or tap when you sign in.

## Enable MFA

You can enable MFA for your user account under your PlanetScale account settings.

**Step 1**

Go to your [PlanetScale account settings](https://app.planetscale.com/account) page.

**Step 2**

Find the **Security** row and click the **"Setup multi-factor authentication"** button.

![Click the "Setup MFA" button priority](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/mfa/setup.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=759dc07f699bb12c7115b2b24ee035d6)

This will bring up a pop-up modal with a *QR code* and some `recovery codes` that you will need to copy.

![Pop-up modal with QR code and recovery codes priority](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/mfa/recovery-codes.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=3f7c5e1ab150e3b48f164a04bfc6c237)

> [!WARNING]
>
> Recovery codes are the only account recovery method accepted when MFA is enabled. If you lose both your TOTP app and the recovery codes, there is no way to regain access to your account.

**Step 3**

Scan the QR code with your preferred TOTP app. If you can't scan it, enter the secret shown under the code. Then enter the code from the app.

**Step 4**

Click **Confirm code**.

**Step 5**

Once the generated code is validated, click the **Copy** button in the `recovery codes` section.

![Copy the recovery codes](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/mfa/copy.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=2a28f8791b68f85570d38818c99256b0)

> [!TIP]
>
> Save the recovery codes somewhere secure before you continue. You can generate a new set later from your account settings. Generating new codes replaces the old ones, and the new codes are shown only once.

**Step 6**

Click **Done** to close the pop-up modal.

## Passkeys and security keys

A passkey is saved on your device or in a password manager, such as iCloud Keychain, 1Password, or Google Password Manager. A hardware security key is a small USB or NFC device, like a YubiKey, that you plug in or tap when you sign in.

GitHub and [SSO](https://planetscale.com/docs/security/sso.md) accounts do not get this option. They keep using their identity provider.

### Add a key

**Step 1**

Go to your [PlanetScale account settings](https://app.planetscale.com/account) page.

**Step 2**

Under **Security keys**, click **Add key**.

**Step 3**

Click **Continue** and follow the browser prompt. Use a passkey, or insert and touch a hardware security key.

**Step 4**

Name the key so you can recognize it later. The name is filled in for you, and you can change it.

**Step 5**

If you already have a second factor, enter an authenticator or recovery code. Then click **Add key**. If this passkey is your first second factor, save the recovery codes shown after it is added.

You can rename a key later from the same list. Removing a key asks for an authenticator or recovery code. Removing your only key turns multi-factor authentication off.

### Sign in with a passkey

On the sign-in page, choose **Sign in with a passkey**, next to the forgot password link. This signs you in without a password or an authenticator code.

The passkey has to verify that it is you, for example with Face ID, Touch ID, a device PIN, or your password manager. That verification counts as both your password and your second factor. A hardware security key that only asks for a touch cannot skip the password.

### Sign in with a key after your password

If you enter your email and password instead, the next page asks for a second factor. Click **Use a security key instead** and follow the browser prompt. A touch-only security key works here. If a passkey is your only factor, you can also enter a recovery code.

## Login with two-factor authentication

Once you've enabled MFA in your PlanetScale user account, the next time you login, you'll be prompted to enter your two-factor authentication (2FA) code.

* Use the **OTP code** generated by your preferred TOTP app to login to your PlanetScale account.
* Or click **Use a security key instead** if you have added a passkey or security key.

## Recovery code login

The `recovery codes` shown during MFA setup are **the only way regain access to your account** in the event that you lose the device that generates your authentication codes. PlanetScale will not accept any other method of authentication or identification.

You can use one of the `recovery codes` in the place of a TOTP token on the second screen during login.

To replace your codes, open [account settings](https://app.planetscale.com/account), click **Recovery codes**, and enter a code from your authenticator app or one of your current recovery codes. The new codes replace the old ones and are shown only once.

## Disable MFA

> [!WARNING]
>
> We strongly recommend that you do not disable MFA to avoid unauthorized access to your user account.

> [!NOTE]
>
> * Any devices setup with the QR code for your account will no longer be able to produce valid OTP tokens.
> * Any recovery codes that were generated when MFA was enabled will no longer be valid.

You can disable MFA for your user account under your PlanetScale account settings.

**Step 1**

Go to your [PlanetScale account settings](https://app.planetscale.com/account) page.

**Step 2**

Click the **Disable** button next to *"Multi-factor authentication enabled"* in the **Security** row.

![Click the "Disable" button](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/mfa/disable.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=7e3ac87c9a3b1a1fd18d7419f757f0c5)

**Step 3**

Enter an **OTP code** or one of the `recovery codes` generated by your preferred TOTP app to confirm.

![Disable MFA pop-up modal](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/mfa/modal.png?fit=max\&auto=format\&n=GA0k5H-MolPvBjDk\&q=85\&s=c8315d37e641c8aa2b4200c14169cdb1)

**Step 4**

Click the **Disable** button to close the pop-up modal.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
