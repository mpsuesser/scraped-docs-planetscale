---
url: https://planetscale.com/docs/postgres/cluster-configuration/maintenance-windows
title: "Maintenance Windows"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Maintenance windows

> Maintenance windows have been temporarily disabled for PlanetScale Postgres databases.

This page covers Postgres.

PlanetScale Postgres databases previously supported weekly maintenance windows, during which Postgres image upgrades would occur. These required connections to be terminated and re-established on upgraded nodes.

To ensure better connection stability for customers, we've moved to optional updates and have temporarily disabled maintenance windows for Postgres databases.

You can now [manually update your cluster](https://planetscale.com/docs/postgres/cluster-configuration/updates.md) to access new extensions and other software updates.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
