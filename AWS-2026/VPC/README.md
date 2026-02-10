# AWS Virtual Private Cloud (VPC)

## Overview

An AWS Virtual Private Cloud (VPC) is a logically isolated section of the AWS Cloud where you can launch AWS resources in a virtual network that you define. A VPC gives you control over your virtual networking environment, including selection of IP address ranges, subnets, route tables, network gateways, and security settings.

## Core Components

- **Subnets:** Segments of a VPC IP address range where you place groups of resources. Subnets can be public (with internet access) or private.
- **Route Tables:** Rules that determine how traffic is directed within the VPC and to the internet or other networks.
- **Internet Gateway (IGW):** Enables communication between instances in your VPC and the internet.
- **NAT Gateway / NAT Instance:** Allows private subnet instances to initiate outbound IPv4 traffic to the internet while preventing inbound connections.
- **Security Groups:** Instance-level virtual firewalls that control inbound and outbound traffic.
- **Network ACLs (NACLs):** Optional subnet-level stateless firewalls.
- **VPC Peering / Transit Gateway:** Connect VPCs across accounts or regions for private communication.

## Best Practices

- Use separate subnets for public and private resources.
- Keep security groups as restrictive as possible; use least privilege principles.
- Use multiple Availability Zones (AZs) for high availability.
- Use NAT Gateways (managed) instead of NAT instances for simplicity and scalability.
- Tag resources consistently for cost allocation and management.

## Common Use Cases

- Hosting web applications with public-facing load balancers and private application/data tiers.
- Connecting on-premises networks to AWS via AWS Site-to-Site VPN or AWS Direct Connect.
- Isolating environments (dev/stage/prod) using separate VPCs or accounts with peering/Transit Gateway.

## Security Considerations

- Restrict SSH/RDP access via security groups and use bastion hosts or Session Manager.
- Use VPC Flow Logs to capture information about the IP traffic going to and from network interfaces in your VPC.
- Apply Network ACLs for stateless filtering where needed, but rely on security groups for most controls.

## Example (Quick CLI snippets)

Create a VPC with the AWS CLI (example):

```bash
aws ec2 create-vpc --cidr-block 10.0.0.0/16
```

Create a public subnet:

```bash
aws ec2 create-subnet --vpc-id <vpc-id> --cidr-block 10.0.1.0/24 --availability-zone us-east-1a
```

Attach an Internet Gateway:

```bash
aws ec2 create-internet-gateway
aws ec2 attach-internet-gateway --vpc-id <vpc-id> --internet-gateway-id <igw-id>
```

## Resources & References

- AWS VPC documentation: https://docs.aws.amazon.com/vpc/
- AWS Well-Architected Framework: networking best practices

---

This README provides a concise overview and quick-start notes for working with AWS VPCs. Let me know if you want a deeper tutorial (diagram, Terraform/AWS CloudFormation examples, or a security checklist).
