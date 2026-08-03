---
url: https://planetscale.com/docs/plans/managed/aws/reverse-privatelink
title: "Reverse Privatelink"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

Often, one of the first tasks for a new Managed deployment is to import data from an existing database housed in a separate AWS Organizations member account.

This guide explains how to set up AWS PrivateLink components that enable cross-account communication to facilitate this process. In PrivateLink parlance, the source database is known as the “producer”, and PlanetScale is the “consumer”.

While there are a number of different ways to set up and configure PrivateLink components, in this guide we’ll be using the AWS CLI tool.

## How PlanetScale Managed and AWS PrivateLink work

Broadly speaking, there are three major components to this PrivateLink setup:

- A [VPC Endpoint Service](https://docs.aws.amazon.com/vpc/latest/privatelink/endpoint-service-overview.html) conceptually lives in the producer’s VPC and AWS account. Once configured, it allows authorized principals (e.g. AWS accounts, IAM users) to establish a connection to an existing VPC Endpoint Service ID.
- A [VPC Endpoint Interface](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html) exists on the consumer side, and once configured with an endpoint service address exposes an internal IP address and port which consumers may use for cross-account communication.
- A [Network Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/introduction.html) (NLB) is created on the producer side, and by managing the IP target group on this NLB you can choose which internal services to expose via the PrivateLink Endpoint Service.

## Starting state

Below is a simplified diagram of our initial state. We’ve got two separate accounts, each with their own VPCs and availability zones. We’re using AWS [AZ IDs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#az-ids) as well as of AZ names to ensure that we’re always referring to the correct AZ, as AZ names are not consistent across AWS accounts. On the producer side, we have a network subnet inside each AZ.

![starting state](https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/managed/aws/privatelink/starting-state.png?w=2500&fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=9ced727a7239e9c8f75783746f328f8a)

starting state

## Create and configure the NLB

First, create the Network Load Balancer in the producer AWS account. You want the NLB to be available in each of the three AZs that the two accounts share. This is done by adding the ids of the three subnets associated with those AZs using the `--subnets` option.

```shellscript
aws elbv2 create-load-balancer \
  --name pl-nlb \
  --scheme internal \
  --type network \
  --subnets subnet-cafed00d subnet-c0ffee subnet-cafef00d
  --security-groups sg-f00
```

If you want to attach a security group to your NLB, you must do so at creation time. This allows you to restrict the inbound protocol and port range to the appropriate port (e.g. `TCP:3306` for MySQL or `TCP:5432` for Postgres). This command will return an ARN that uniquely identifies the newly created NLB. You’ll need this later.

### Create an IP target group

Run the following to create an empty IP target group on the appropriate port (e.g. `TCP:3306` for MySQL, `TCP:5432` for Postgres) in the producer VPC:

```shellscript
aws elbv2 create-target-group \
  --name pl-nlb-target-group \
  --protocol TCP \
  --port 3306 \
  --vpc-id vpc-f00 \
  --target-type ip
```

This will return an ARN that uniquely identifies the target group.

### Register a target

This step adds the IP address of the existing Primary RDS instance to the target group. You want to make sure that only the RDS writer is registered as a target.

```shellscript
aws elbv2 register-targets \
  --target-group-arn <arn>
  --targets Id=10.0.0.1
```

### Create a listener

Finally, associate the target group NLB by creating a listener.

```shellscript
aws elbv2 create-listener \
   --load-balancer-arn <arn>\
   --protocol TCP \
   --port 3306 \
   --default-actions Type=forward,TargetGroupArn=<arn>
```

Now that you’ve created the NLB and configured it, you can add the NLB to the diagram. Although on the diagram it looks like there are three separate NLBs, it’s meant to represent a single NLB object that has network interfaces in three different subnets.

![create and configure nlb](https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/managed/aws/privatelink/create-and-configure-nlb.png?w=2500&fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=a37bfa2cdefa37293d90239bd79c6dd9)

create and configure nlb

## Create and configure the PrivateLink VPC Endpoint Service

Now, let’s configure the endpoint service. You’ll need the ARN of your load balancer for this.

```shellscript
aws ec2 create-vpc-endpoint-service-configuration \
  --network-load-balancer-arn <arn> \
  --no-acceptance-required
```

This will return a VPC service id.

## Allow inbound traffic from VPC’s subnet

After setting up the NLB and the VPC Endpoint Service, you need to ensure that the security group attached to the NLB permits inbound traffic from the subnet(s) of the consumer VPC where the interface VPC endpoint resides.

### Configure service permissions

You only want to allow incoming connections on this endpoint service from the PlanetScale Managed AWS Organizations member account. This step requires the account number.

```shellscript
aws ec2 modify-vpc-endpoint-service-permissions \
  --service-id vpce-svc-f00 \
  --add-allowed-principals '["arn:aws:iam::123456789012:root"]'
```

![create and configure vpce svc](https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/managed/aws/privatelink/create-and-configure-vpce-svc.png?w=2500&fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=feead52b45f085880bb172f82f922469)

create and configure vpce svc

## Wrapping Up

Once you’ve communicated your newly created VPC Service Endpoint ID to your Solutions Engineer, the PlanetScale Engineering team can then complete the rest of the process. This involves creating, configuring, and testing the PrivateLink VPC Interface Endpoint. The diagram below illustrates the completed system.

![wrapping up](https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/managed/aws/privatelink/wrapping-up.png?w=2500&fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=739cbbd655957959fb862d47f1566def)

wrapping up

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
