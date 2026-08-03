---
url: https://planetscale.com/docs/postgres/imports/import-ips
title: "Import Ips"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

If you have IP restrictions on your source database, you’ll need to allowlist the PlanetScale IP addresses so we can connect during the import process. The IPs you need depend on the region of your PlanetScale database.

## Retrieve IP addresses via API

You can get the list of IP addresses for your database’s region by visiting the following URL in your browser while logged into PlanetScale:

```text
https://api.planetscale.com/v1/organizations/<ORG>/databases/<DATABASE>
```

Replace `<ORG>` with your organization name and `<DATABASE>` with your database name.

The response includes a `region` object with a `public_ip_addresses` array containing all the IPs you need to allowlist:

```json
{
  "id": "rr0qdj0nin8q",
  "type": "Database",
  "region": {
    "id": "ri0pbcmdkjsh",
    "type": "Region",
    "provider": "AWS",
    "enabled": true,
    "public_ip_addresses": [
      "18.117.23.127",
      "3.131.243.164",
      "3.132.168.252",
      "3.131.252.213",
      "3.132.182.173",
      "3.15.49.114",
      "3.209.149.66",
      "3.215.97.46",
      "34.193.111.15"
    ],
    "display_name": "AWS us-east-2",
    "location": "Ohio",
    "slug": "aws-us-east-2"
  }
}
```

Add each IP address from the `public_ip_addresses` array to your source database’s allowlist before starting the import.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
