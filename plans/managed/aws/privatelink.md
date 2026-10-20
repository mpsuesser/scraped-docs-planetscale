---
url: https://planetscale.com/docs/plans/managed/aws/privatelink
title: "Privatelink"
description: ""
access_date: 2026-10-20T18:01:41.586Z
current_date: 2026-10-20T18:01:41.586Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Set up AWS PrivateLink with PlanetScale Managed

> PlanetScale Managed can connect you to your databases via [AWS PrivateLink](https://aws.amazon.com/privatelink/). The following guide describes how PlanetScale Managed with AWS PrivateLink works and how to set it up.

Vitess and Postgres

## Overview

If you are on the Base plan and would like to set up AWS PrivateLink, see our [Private connections documentation](../../../vitess/connecting/private-connections.md).

## How PlanetScale Managed and AWS PrivateLink work

AWS PrivateLink requires two components:

* A [VPC endpoint service](https://docs.aws.amazon.com/vpc/latest/privatelink/endpoint-service-overview.html) deployed in the AWS Organizations member account that PlanetScale controls.
* A [VPC endpoint interface](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html), sometimes referred to as a "VPC endpoint" in AWS, deployed in the account that your applications operate in.

Once both components are operating correctly, the EC2 instances in the VPC that the VPC endpoint has been assigned to will leverage [Private DNS](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html#vpce-private-dns) to connect to your VPC endpoint instead of the publicly accessible endpoint.

The connection strings that PlanetScale provides will operate successfully inside and outside your VPC, creating PrivateLink connections inside of your VPC and regular connections outside of your VPC.

## Step 1: Initiating the setup process

There is no fully automated way to establish a PrivateLink connection. If you would like to initiate the process, please get in touch with your Solutions Engineer and let them know the [AWS Account ID](https://docs.aws.amazon.com/IAM/latest/UserGuide/console_account-alias.html) that you intend to create the [VPC endpoint](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints.html) in.

Once they receive your AWS Account ID and forward it to the team responsible for provisioning your deployment, the team will provide the Solutions Engineer (and ultimately you) with the Service Name of the [VPC endpoint service](https://docs.aws.amazon.com/vpc/latest/privatelink/endpoint-service-overview.html) that will be responsible for accepting your connection.

> [!NOTE]
>
> It is important to keep the service name in your records. It is the only piece of information you need to input when creating your VPC endpoint.

## Step 2: Establishing a VPC endpoint connection

> [!WARNING]
>
> Only proceed to the next steps once a PlanetScale Solutions Engineer has provided the service name and confirmed cross-account authentication has been configured.

The following steps are an example of establishing a VPC endpoint connection in the AWS Console. In this example, the customer has requested that their deployment be in the `eu-west-1` region.

When you go through the steps, make sure that you have selected the region that matches the region that your PlanetScale Managed cluster deployment has been provisioned into.

**Step 1**

Navigate to the Endpoints section on the Virtual Private Cloud page and select "**Create Endpoint**."

![nav\_to\_splash](https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/managed/aws/privatelink/nav_to_splash.png?fit=max\&auto=format\&n=UzFO5Pe10M0-W-uW\&q=85\&s=f47c7cb8652a121d20c31645fa65d932)

**Step 2**

Select the "**Find service by name**" selector, input the provided Service Name, and select the "**Verify**" button.

![verified](https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/managed/aws/privatelink/verified.png?fit=max\&auto=format\&n=UzFO5Pe10M0-W-uW\&q=85\&s=469e7a63f0d2cf0d2a858c3535c6a07a)

**Step 3**

Select the VPC in the drop-down where you wish to provision this VPC endpoint and the relevant subnets inside your VPC.

![cyo\_vpc](https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/managed/aws/privatelink/cyo_vpc.png?fit=max\&auto=format\&n=UzFO5Pe10M0-W-uW\&q=85\&s=66a5cd7586905f3f8a102116147a12f7)

**Step 4**

Select the "**Enable DNS Name**" checkbox. Take note of the value of your "**Private DNS Name**" field. That is how we will verify that the connection is operating successfully.

![enable\_dns\_name](https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/managed/aws/privatelink/enable_dns_name.png?fit=max\&auto=format\&n=UzFO5Pe10M0-W-uW\&q=85\&s=cf722a66139f93210f5ccbfa4e6a6a98)

**Step 5**

Select the relevant Security Groups you want your VPC endpoint to adhere to.

![select\_sgs](https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/managed/aws/privatelink/select_sgs.png?fit=max\&auto=format\&n=UzFO5Pe10M0-W-uW\&q=85\&s=f90b6530c68f3c41f6133cc3caa14ead)

**Step 6**

Add as many tags as your heart desires (up to 50) and select "**Create endpoint**."

![click\_it](https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/managed/aws/privatelink/click_it.png?fit=max\&auto=format\&n=UzFO5Pe10M0-W-uW\&q=85\&s=e0cbec31162fc455f92fba292c7f3fa1)

**Step 7**

The "Creating" spinner will spin momentarily and then deliver you the news of the endpoint creation. You should see a VPC endpoint in the `pending` state if it was successful. If the creation failed, record the reason and consult your Solutions Engineer.

![pending](https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/managed/aws/privatelink/pending.png?fit=max\&auto=format\&n=UzFO5Pe10M0-W-uW\&q=85\&s=a8754d136979d8347c35682445a313aa)

**Step 8**

After 2-10 minutes (make sure to refresh), your VPC endpoint will report an `available` state.

![available](https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/managed/aws/privatelink/available.png?fit=max\&auto=format\&n=UzFO5Pe10M0-W-uW\&q=85\&s=ff418c62e571302611620ed4638aead6)

## Step 3: Verifying a VPC endpoint connection

PlanetScale publishes a [wildcard DNS record](https://en.wikipedia.org/wiki/Wildcard_DNS_record) for your private region. AWS PrivateLink will override the DNS record in your VPC to point to your VPC endpoint instead of the publicly published record.

To verify that the DNS override is working correctly, issue the following `dig` command using the value of your "Private DNS Name" instead of the value in the example:

```shell theme={null}
dig +short wildcard.frzzbztuqm3h-euwest1-1.psdb.cloud
172.31.16.197
172.31.13.7
```

If your `dig` command returns a set of static IP addresses, your VPC Endpoint connection is operating successfully. If it returns a `CNAME` to an ELB record (for example, something like `something.elb.region.amazoneaws.com`), your connection is not operating successfully, and you should consult your Solutions Engineer.

Once you've verified that your connection is operating successfully, you will need to verify that you can reach a database you've provisioned:

**Step 1**

Create a connection string for your PlanetScale database using the "**Connect**" button in the dashboard or the [pscale CLI](../../../cli.md).

**Step 2**

From an EC2 instance running in your VPC, connect using the appropriate client for your database engine:

**MySQL**

Ensure the `mysql-client` package is installed, then paste your MySQL CLI command:

```shell theme={null}
mysql -h <HOST_NAME> -u <USERNAME> -p --ssl-mode=VERIFY_IDENTITY --ssl-ca=/etc/pki/tls/certs/ca-bundle.crt
Enter password:
...

mysql>
```

If you receive the `mysql>` prompt, your connection is operating successfully.

> [!NOTE]
>
> The correct path for the CA root configuration for the `--ssl-ca` flag depends on your operating system. See the [CA root configuration documentation](../../../vitess/connecting/secure-connections.md#ca-root-configuration) for the correct path.

**Postgres**

Ensure `psql` (version 17+ recommended for `sslnegotiation=direct` support) is installed, then connect:

```shell theme={null}
psql 'host=<HOST_NAME> \
      port=5432 \
      user=<USERNAME> \
      password=<PASSWORD> \
      dbname=<DATABASE> \
      sslnegotiation=direct \
      sslmode=verify-full \
      sslrootcert=system'
```

If you reach the `postgres=>` prompt, your connection is operating successfully.

> [!NOTE]
>
> If `sslrootcert=system` doesn't work in your environment, replace it with the path to your system's root CA certificate. See the [Postgres connecting documentation](../../../postgres/connecting/quickstart.md#secure-connections) for more details.

If your client connects successfully, you have confirmed that your connections to PlanetScale will be established through AWS PrivateLink. If you cannot connect, please consult your Solutions Engineer.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
