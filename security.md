---
url: https://planetscale.com/docs/security
title: "Security"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

The security of our systems is of the utmost importance. We consistently aim to improve our security posture by building security into every layer of our products.

Below is a breakdown of common security and compliance requirements by PlanetScale plan:

|  | Base plan | Enterprise multi-tenant | Enterprise single-tenant | PlanetScale Managed |
| --- | --- | --- | --- | --- |
| [Encryption of data (at rest and in transit)](#encryption-of-data) |  |  |  |  |
| [SOC 1 Type 2 available](#soc-1-type-2-&-soc-2-type-2+-hipaa) |  |  |  |  |
| [SOC 2 Type 2+ HIPAA available](#soc-1-type-2-&-soc-2-type-2+-hipaa) |  |  |  |  |
| [IP restrictions](vitess/connecting/connection-strings.md#ip-restrictions) (Vitess only) |  |  |  |  |
| [Audit logs](security/audit-log.md) |  |  |  |  |
| [Security logs](security/security-log.md) |  |  |  |  |
| [Data Processing Addendum available](#data-processing-addendum) |  |  |  |  |
| [Private database connectivity](#private-database-connectivity) |  |  |  |  |
| [Single sign-on (SSO)](security/sso.md) | Available as add-on |  |  |  |
| [Business Associate Agreements available](#hipaa-and-business-associate-agreements) |  |  |  |  |
| Dedicated AWS/GCP account |  |  |  |  |
| [PCI compliant](#pci-compliance) |  |  |  |  |
| [Your own AWS/GCP account](#deploy-in-your-own-aws-or-gcp-account) |  |  |  |  |

## Available on all PlanetScale plans

### Private database connectivity

By default, all PlanetScale connections are encrypted and routed through the public Internet. Optionally, you can connect privately to databases through [AWS PrivateLink](vitess/connecting/private-connections.md) or [GCP Private Service Connect](vitess/connecting/private-connections-gcp.md).

### SOC 1 Type 2 & SOC 2 Type 2+ HIPAA

PlanetScale continuously monitors and reports primarily using System and Organization Controls (SOC) 1 & 2 Type 2 paired with the HIPAA Security Rule. To request access to our latest reports, please visit PlanetScale’s [Trust Center](https://trust.planetscale.com/).

### HIPAA and Business Associate Agreements

PlanetScale can enter into Business Associate Agreements (BAAs) with customers who meet qualifying criteria. To request a BAA:

1. Sign into the PlanetScale dashboard
2. Click “Settings” from your Organization page > “Legal” > “Create request”
3. Fill out the form
4. We will review your request and let you know if it’s approved
5. Once approved, you’ll receive a Docusign sent to the email you specified as the signer

The customer must determine whether they are a Covered Entity — or a Business Associate of a Covered Entity — as defined under HIPAA. If so, the customer may require a BAA with PlanetScale for the purposes of our relationship.

Responsibility around HIPAA compliance between PlanetScale and the customer is implemented using a shared responsibility model. While PlanetScale plans provide a secure and compliant infrastructure for the storage and processing of Protected Health Information (PHI), the customer is ultimately responsible for ensuring that the environment and applications that they build on top of PlanetScale are properly configured and secured according to HIPAA requirements.

The Department of Health and Human Services does not recognize any formal certification for HIPAA. PlanetScale systems, software, networks, and procedures are consistent with the controls outlined in the relevant rules.

### Data security

#### Encryption of data

PlanetScale databases and their client communications are AES encrypted throughout the PlanetScale platform, both in transit and at rest.

##### At rest

Data is encrypted at rest on the underlying storage media that serves database branches and also the underlying storage media that hosts your PlanetScale database backups. This helps mitigate the risk of unintentional or malicious access to user data on storage systems.

##### In transit

Data in transit to PlanetScale databases is encrypted and goes through three major paths:

- The [PlanetScale CLI](cli.md), leverages [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) when initiating a connection to PlanetScale’s API and Edge.
- PlanetScale [connection strings](vitess/connecting/connection-strings.md) require the successful establishment of a TLS session before any SQL commands can be issued.
- [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) is used to secure all data transmitted between PlanetScale and [clients using PlanetScale Connect](vitess/etl.md).

#### Additional data protection controls

Communications to the PlanetScale API and Dashboard are encrypted using TLS 1.3. Certificates are issued by established third-party certificate authorities.

### General Data Protection Regulation (GDPR)

PlanetScale offers database services in Amazon Web Services and Google Cloud Platform regions around the world. PlanetScale complies with the EU General Data Protection Regulation (GDPR) and other global privacy regulations, where applicable. Customers are responsible for their applications’ compliance with regulatory requirements, including as they relate to data subjects of their systems.

#### Data Processing Addendum

All PlanetScale plans are covered by our [Data Processing Addendum (DPA)](https://planetscale.com/legal/data-processing-addendum). Markups are accepted for addendums on all PlanetScale Enterprise plans. [Contact us](https://planetscale.com/contact) to talk more about PlanetScale Enterprise plans and changes to our DPA.

#### Data locality

The infrastructure supporting user databases, backups, etc., is in the provider (AWS or GCP) and region where the database is created. Any read-only replicas in other geographies will copy the data set to the selected regions.

The following are two examples of data locality in PlanetScale:

- If you create a database in a US-based region, all data, including customer data, is stored and processed in the US, except in cases where sub-processors are identified as having other locations.
- If you create a database in a Europe-based region, your data does not leave the region the database was created in, unless you create a read-only region in another region.

## Available on all Enterprise plans

### Additional audit logging features

In addition to the [audit log](security/audit-log.md) feature available to all PlanetScale plans, Enterprise plans can use our EventBridge configuration to send logs to your AWS account. Ask your PlanetScale account manager for more information on how to set it up.

If you have any questions or concerns related to the security and compliance of any PlanetScale Enterprise plans, please [contact us](https://planetscale.com/contact), and we will be happy to discuss them further.

## Available on Enterprise single-tenant plans

PlanetScale offers two single-tenant deployment options: Single-tenant and PlanetScale Managed for organizations that require a single-tenant environment. See the [section below](https://planetscale.com/docs#available-on-planetscale-managed) for more information on PlanetScale Managed-only security and compliance features.

[Contact us](https://planetscale.com/contact) if you are interested in exploring PlanetScale single-tenant deployment options for your organization.

### Dedicated AWS Organizations member account or GCP organization

PlanetScale single-tenant deployment options are deployed into a dedicated AWS Organizations member account or GCP organization owned by PlanetScale. If you want PlanetScale to deploy into your own AWS Organizations member account or GCP organization, owned by your organization, see PlanetScale Managed below.

## Available on PlanetScale Managed

PlanetScale Managed is a single-tenant deployment of PlanetScale within your Amazon Web Services (AWS) or Google Cloud Platform (GCP) account. In this configuration, you can use the same API, CLI, and web interface that PlanetScale offers, with the benefit of running entirely in your own AWS Organizations member account or GCP organization. You can learn more on the [PlanetScale Managed overview page](plans/managed.md).

### Deploy in your own AWS or GCP account

PlanetScale Managed is a packaged [data plane](https://en.wikipedia.org/wiki/Data_plane) that’s deployed to an AWS Organizations member account or GCP project that you own and we operate. Your database lives entirely inside a dedicated member account or project within your cloud organization. PlanetScale will not have access to other member accounts or projects nor your organization-level settings within the cloud service provider.

Read more on how PlanetScale Managed works inside either cloud provider:

- [PlanetScale Managed on Amazon Web Services](plans/managed/aws.md)
- [PlanetScale Managed on Google Cloud Platform](plans/managed/gcp.md)

### PCI compliance

PlanetScale Managed, when deployed with the appropriate controls enabled via our Shared Responsibility Matrix, has been issued an Attestation of Compliance (AoC) and Report on Compliance (RoC), certifying our compliance with the PCI DSS 4.0 as a Level 1 Service Provider. This enables PlanetScale Managed to be used via a shared responsibility model across merchants, acquirers, issuers, and other roles in storing and processing cardholder data.

### Human access flow

PlanetScale Managed supports customer counter-approval for access to Managed environment(s) by PlanetScale employees via an integration in the web application. Talk to your Customer Engineer about enabling this feature.

### Other PlanetScale Managed security features

PlanetScale Managed on AWS also supports:

- [Fully private network isolation](plans/managed/aws.md#fully-private-network-isolation)
- [Third-account customer-controlled public key infrastructure (PKI)](plans/managed/aws.md#third-account-customer-controlled-public-key-infrastructure)

## Corporate security

### Background checks

Background checks are performed on new team members during onboarding (within 30 days of their start date) as permitted by local law.

### Security training

All team members complete security awareness training covering company security policies and procedures during onboarding (within 30 days of their hire date). Trainings are required annually after that for all employees.

The training material is designed to assist the employee in identifying and responding to social engineering and other cybersecurity risks they may encounter as part of their role at PlanetScale.

## Security operations

### Endpoints

All company-provided devices are managed with [Kandji](https://kandji.io/). Device configuration is based on the Center for Internet Security (CIS) level 1 benchmark and is continuously enforced. Mobile device management deploys and enables relevant services to ensure corporate endpoints are appropriately monitored.

### Extended Detection and Response (XDR)

PlanetScale administrative endpoints, such as devices used by employees, are monitored via endpoint detection and response systems.

### Detection and response

PlanetScale employs measures for both corporate endpoints and cloud instances to collect, analyze, and store events, connections, and other potentially relevant metadata in real-time. Automated systems match events against internal and known bad patterns and intelligence streams.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
