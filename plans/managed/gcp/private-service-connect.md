---
url: https://planetscale.com/docs/plans/managed/gcp/private-service-connect
title: "Private Service Connect"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Overview

The following guide describes how PlanetScale Managed with GCP Private Service Connect works and how to set it up.

If you are on the Base plan and would like to set up GCP Private Service Connect endpoint, see our [Private connections documentation](../../../vitess/connecting/private-connections-gcp.md).

## How PlanetScale Managed and GCP Private Service Connect work

Private Service Connect (PSC) allows a service producer (PlanetScale) offer services to a service consumer without the consumer being a member of the service producer’s organization.

The service producer is the Google Cloud project controlled by PlanetScale, and the service consumer is the project(s) where your applications operate. Your applications connect to a private IP you allocate in your project, which is routed to your PlanetScale databases in the project that PlanetScale controls.

GCP PSC requires multiple components:

- A Private Service Connect [Service Attachment](https://cloud.google.com/vpc/docs/private-service-connect#service-attachments) (also known as a Published Service) deployed in the project that PlanetScale controls on your behalf.
- A Private Service Connect [Endpoint](https://cloud.google.com/vpc/docs/private-service-connect#endpoints) deployed in the project(s) that your applications operate in.

Once all components are operating correctly, the applications in the project with the endpoint configured will connect to the service attachment using private IP addresses instead of the publicly accessible endpoint.

## Step 1: Initiating the setup process

If you would like to initiate the process, please contact your Solutions Engineer and let them know the Google Cloud project ID(s) in which you intend to create Private Service Connect endpoints. If you need to add additional projects to the allowlist, please get in touch with your Solutions Engineer.

Google Cloud project IDs cannot be changed after initial setup. Please be sure to choose an ID that you will continue to use.

Once they receive your project IDs and forward them to the team responsible for provisioning your deployment, the team will provide them (and ultimately you) with the Private Service Connect Service Attachment URI, which will be in the form `projects/PROJECT/regions/REGION/serviceAttachments/SERVICE_NAME`.

If you use VPC Service Controls in your VPC, you must ensure that the policy allows access to the PlanetScale-controlled project.

Your Solutions Engineer will provide you the following information when the setup is complete:

- `Target Service` (example: `projects/PROJECT/regions/REGION/serviceAttachments/SERVICE_NAME`)

You will use these values when configuring the Private Service Connect in your application projects.

If you have databases in multiple regions, each region will have a unique `Target Service`, and you will need to configure consumer endpoints for each region.

## Step 2: Establishing Private Service Connect

Only proceed to the next steps once a PlanetScale Solutions Engineer has provided the `Target Service`.

Refer to Google Cloud’s [Access published services through endpoints](https://cloud.google.com/vpc/docs/configure-private-service-connect-services) document for more information on connecting to services via Private Service Connect. This document covers additional details not covered here, including the IAM roles required to perform the configuration process.

### Using the GCP console

The following steps are an example of establishing a Private Service Connect endpoint in the [GCP Console](https://console.cloud.google.com/).

## Step 3: Verifying Connectivity

## DNS

Private Service Connect services created after **May 8, 2024** automatically create private Cloud DNS records in the project where the PSC consumer endpoints are created.

PSC services published before **May 8, 2024** may need to create a private Cloud DNS zone and configure records pointing to the PSC endpoint IP’s manually if you wish to use DNS names to connect to your PlanetScale databases.

Google maintains additional documentation covering DNS and Private Service Connect here:

- [Automatic DNS configuration for Service Consumers](https://cloud.google.com/vpc/docs/dns-vpc-hosted-services#auto-dns-consumer)
- [Other ways to configure DNS for Service Consumers](https://cloud.google.com/vpc/docs/configure-private-service-connect-services#other-dns)

Private Service Connect endpoints automatically create a private DNS records in the project where the PSC consumer endpoints are created that resolve to the endpoint’s reserved IP.

The domain name used varies by region. You can view the domain name by clicking on `Network Services > Cloud DNS`. If Google was able to set up automatic DNS, you will see a new private DNS zone labeled by `DNS Name`:

![cloud dns zone list](https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/managed/gcp/private-service-connect/cloud_dns.png?w=2500&fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=d64a57ebcb14557e2736dc1bc9147c04)

cloud dns zone list

Your consumer endpoints will be available via DNS records visible only within your VPC using the format:

- `<Endpoint-Name>.<Domain-Name>`

If your endpoint was created with automatic DNS or your created your own DNS records manually, you can verify resolution with `dig`. In this example, the endpoint was created with the name `edge` and the service’s domain name was `izkpm55j334u-uscentral1.private-connect.psdb.cloud`:

```shellscript
$ dig +short edge.izkpm55j334u-uscentral1.private-connect.psdb.cloud
10.128.0.14
```

## Test connectivity

Run `curl https://<Endpoint-Name>.<Domain-Name>` to verify your connectivity. A successful response will yield `Welcome to PlanetScale`.

```shellscript
curl https://edge.izkpm55j334u-uscentral1.private-connect.psdb.cloud
Welcome to PlanetScale.
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
