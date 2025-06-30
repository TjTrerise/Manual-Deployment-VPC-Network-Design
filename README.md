# Manual Deployment of AWS Multi-Region VPC Network Architecture with Transit Gateway.


## Project Overview

This project involves designing and manually building a robust, multi-region cloud network architecture on AWS. It leverages core networking components like custom VPCs, public/private subnets, NAT Gateways, EC2 and security groups, with a focus on establishing secure and scalable inter-region connectivity using AWS Transit Gateway.


## Features

- Manual construction of a multi-region cloud network architecture on AWS.
- Leveraging custom core networking components like custom VPCs, public/private subnets, NAT Gateways, EC2 and security groups.
- Focus on establishing a secure and scalable inter-region connectivity using AWS Transit Gateway.

## Technologies Used

* EC2
* Linux
* Session Manager
* Transit Gateway
* VPC

## Getting Started

To get started with deploying this AWS Multi-Region VPC Network Architecture, please ensure you meet the following prerequisites.

### Prerequisites 
 
* Fundamental knowledge of networking concepts such as:
 - IPv4.
 - Subnets.
 - Routing.
* An active AWS account with appropriate permissions to create VPCs, subnets, gateways, EC2 instances and security groups.
* Access to the AWS console

## Deployment Steps


### Step 1: VPC Creation in 2 Regions

- Select the first region you wish to deploy the VPC In.
- Select create VPC.
- IPv4 CIDR 10.16.0.0/16.
- Create an internet gateway and attach it to the VPC.
- Create public subnets.
- Subnet A 10.16.0.0/20 AZ A.
- Subnet B 10.32.0/20 AZ B.
- Create private subnets.
- Subnet A 10.16.0.0/20 AZ A.
- Subnet B 10.32.0.0/20 AZ B.
- Configure auto-assign public IPs for public subnets.
- Create a public route table and associate with public subnets.
- Create private route table for private subnet A.
- Create private route table for private subnet B.
  *Repeat entire step but in a new region*


### Step 2: Create NAT Gateways and Security Groups

- Allocate 2 elastic IPs in both region (Total of 4)
- Create 4 NAT gateways, 1 in each public subnet of each region.
- Allocate elastic IPs to all NAT gateways.
- Attach NAT gateways to each public subnet.
- Add route on all private route tables for 0.0.0.0/0 to corresponding NAT gateways.
- Create a security group in each region that allows all ICMP traffic from the CIDR blocks of the created VPCs

### Step 3: Create The Transit Gateway 

 - Create a Transit Gateway in both regions.
 - Create a Transit Gateway peering attachment in region 1 and enter region 2's transit gateway ID.
 - Accept the peering attachment in region 2.
 - Create a Tranist Gateway VPC attachment in region 1 and region 2.
 - Update your private route tables in region 1 to point to the transit gateway attachment you just created.
 - Update your private route tables in region 2 to point to the transit gateway attachment you just created.
 - Add a static route for Transit Gateway EU pointing to the peering connection 10.32.0.0/16 and 10.16.0.0/16
 - Add a static route for Transit Gateway US pointing to the peering connection 10.16.0.0/16 and 10.32.0.0/16

 ### Step 4: Deploy EC2 Instances

 - Create an instance profile that allows session manager (SSM).
 - Create 4 instances, two in each public subnets in each region.

### Step 5: Configure VPC Flow Logs
 
 - Enable VPC flow logs on both VPCs.

### Step 6: Test Connectivity

 - Test connectivity externally.
 - Test connectivity between VPCs.

### Step 7: Clean-Up 

 - Delete all the resources used to prevent incurring any charges.

## Usage

Once the AWS Multi-Region VPC Network Architecture with Transit Gateway is deployed, it provides a robust and scalable foundation for your cloud applications. This is how you can leverage this:

- Application deployment: You can deploy your applications and services within the private subnets in either region, ensuring they are not directly exposed to the internet while still having controlled outbound internet access via the NAT Gateways.
- Secure Inter-Region Communication: The Transit Gateway peering connection enables seamless and secure communication between resources deployed in different regions. This is ideal for distributed applications, disaster recovery, or sharing data across regions.
- High Availability: With resources deployed across multiple Availability Zones and regions, the architecture inherently supports high availability across services.
- Network Centralization: The Transit Gateway acts as a central hub for network connectivity, simplifying routing and management of inter-VPC and inter-region traffic.
- Monitoring and Troubleshooting: VPC Flow Logs are enabled to provide detailed insights into network traffic, aiding in monitoring, security analysis, and troubleshooting connectivity issues within and between your VPCs.

## Visuals Of Finished Project

![Architecture Design](/docs/architecture-diagram.png)


## Lessons Learned & Challenges Overcome

- NAT Gateways: During this project I made the mistake of attaching the NAT gateways to the private subnets instead of the public subnets. I quickly realised that the reason that I couldn't reach session manager was due to the instances being locked away without any access. NAT is required for devices without public IPs to access the internet, making that fundamental mistake was great for solidfying the topology of NAT.
- Static routes for Transit Gateway: Transit Gateway usally propagates the routes. In this project, even though the VPC are attached to their respective Transit Gateway the Gateways themselves need to know how to reach the CIDR blocks in the other region. During my testing I was able to ping out, ping my instances in same region but not cross region. Once I entered the static routes I was able to successfully communicate between regions. 


## Future Enhancements

- IaC: Automate the building process, allowing repeatable and version controlled deployments. 
- Network Firewalls: Can implement AWS Network Firewall or a third party firewall to provide extra granularity when controlling traffic between regions.
- Private Link: Reduce reliance on NAT Gateways for secure private connectivity to AWS services. 


