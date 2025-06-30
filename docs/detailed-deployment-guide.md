## 1 Create VPC
Create the base layer to the project.

*In the top right corner of the AWS console, select Europe (London) eu-west-2. All resources in this step will be created here*

1. Create the VPC:
- Navigate to VPC (you can search for "VPC" in the search bar).
- In the left navigation pane, click on Your VPCs
- Click the Create VPC button.
- For Resources to create, select VPC only.
   - Name tag: Enter my-vpc-region1.
   - IPv4 CIDR block: Enter 10.16.0.0/16.
   - IPv6 CIDR block: Leave as No IPv6 CIDR block.
   - Tenancy: Leave as Default.
   - Click *Create VPC*.
  - Verification: You should see your new VPC listed. Note down its VPC ID (e.g., vpc-xxxxxxxxxxxxxxxxx).

 2. Create an Internet Gateway (IGW):
  - In the left navigation pane, click on Internet Gateways.
  - Click *Create internet gateway*.
   - Name tag: Enter my-igw-region1.
   - Click *Create internet gateway*.
  - Verification: The IGW will be in a detached state.

 3. Attach the Internet Gateway to your VPC:
  - Select the newly created my-igw-region1 Internet Gateway.
   - Click *Actions*, then Attach to VPC.
   - Select my-vpc-region1 from the dropdown list.
   - Click *Attach internet gateway*.
  - Verification: The IGW state should change to attached.

 4. Create Public Subnets (using /20):
  - In the left navigation pane, click on Subnets.
  - Click *Create subnet*.
  - VPC ID: Select my-vpc-region1.
  - For each public subnet:
   - Subnet name: my-public-subnet-az1-region1 (for AZ1)
   - Availability Zone: Select the first AZ (e.g., eu-west-2a).
   - IPv4 CIDR block: Enter 10.16.0.0/20.
   - Click *Add new subnet*.
   - Subnet name: my-public-subnet-az2-region1 (for AZ2)
   - Availability Zone: Select the second AZ (e.g., eu-west-2b).
   - IPv4 CIDR block: Enter 10.16.16.0/20.
   - Click *Create subnet*.
   - Verification: You should see your two public subnets listed.

 5. Create Private Subnets (using /20).
  - Still on the Subnets page, click *Create subnet* again.
  - VPC ID: Select my-vpc-region1.
  - For each private subnet:
   - Subnet name: my-private-subnet-az1-region1 (for AZ1)
   - Availability Zone: Select the same first AZ (e.g., eu-west-2a).
  - IPv4 CIDR block: Enter 10.16.32.0/20.
  - Click *Add new subnet*.
   - Subnet name: my-private-subnet-az2-region1 (for AZ2)
   - Availability Zone: Select the same second AZ (e.g., eu-west-2b).
   - IPv4 CIDR block: Enter 10.16.48.0/20.
   - Click *Create subnet*.
   - Verification: You should now have four subnets (2 public, 2 private) listed for my-vpc-region1
 6. Configure Auto-Assign Public IPv4 Addresses for Public Subnets:
  - In the Subnets list, select my-public-subnet-az1-region1.
   - Click *Actions*, then Modify auto-assign IP settings.
   - Check the box next to Enable auto-assign public IPv4 address
   - Click *Save*.
  - In the Subnets list, select my-public-subnet-az2-region2.
   - Click *Actions*, then Modify auto-assign IP settings.
   - Check the box next to Enable auto-assign public IPv4 address
   - Click *Save*.
  - Verification: This setting is crucial so that EC2 instances launched into these public subnets automatically receive a public IP address, allowing direct internet access (and SSH access for testing).

 7. Create a Public Route Table and Associate it with Public Subnets:
  - In the left navigation pane, click on *Route Tables*.
  - Click *Create route table*.
   - Name tag: my-public-rt-region1.
   - VPC: Select my-vpc-region1.
   - Click *Create route table*.
  - Add Route to IGW.
   - Select my-public-rt-region1.
   - Click the *Routes* tab, then *Edit routes*.
   - Click *Add route*.
   - Destination: 0.0.0.0/0 (This means all internet traffic).
   - Target: Select Internet Gateway and then choose my-igw-region1.
   - Click *Save changes*.
  - Associate with Public Subnets:
   - Still with my-public-rt-region1 selected, click the *Subnet associations* tab, then *Edit subnet associations*.
   - Check the boxes next to my-public-subnet-az1-region1 and my-public-subnet-az2-region1.
   - Click *Save associations*.
  - Verification: Confirm the 0.0.0.0/0 route to the IGW and that both public subnets are associated.
 8. Create Private Route Tables 1 (one per AZ for redundancy/isolation):
   - In the left navigation pane, click on *Route Tables*.
   - Click *Create route table*.
   - Name tag: my-private-rt-az1-region1.
   - VPC: Select my-vpc-region1.
   - Click **Create route table.
   - Associate with Private Subnet 1:
   - Select my-private-rt-az1-region1.
   - Click the *Subnet associations tab*, then *Edit subnet associations*.
   - Check the box next to my-private-subnet-az1-region1.
   - Click *Save associations*.
  - Create Private Route Table 2
   - Click *Create route table*.
  - Name tag: my-private-rt-az1-region1.
  - VPC: Select my-vpc-region1.
  - Click **Create route table.
  - Associate with Private Subnet 2:
  - Select my-private-rt-az2-region1.
  - Click the *Subnet associations tab*, then *Edit subnet associations*.
  - Check the box next to my-private-subnet-az2-region1.
  - Click *Save associations*.
  - Verification: You should now have one public route table associated with both public subnets, and two private route tables, each associated with one private subnet. (Note: These private route tables don't have internet routes yet; that will come in Step 2 with the NAT Gateway).
 9. Create region 2
   *In the top right corner of the console, crucially, select your secondary region US East (N. Virginia) us-east-1. All resources in this step will be created here*.
  - Create the VPC:
  - Navigate to VPC.
  - In the left navigation pane, click on Your VPCs.
  - Click the *Create VPC button*.
  - For Resources to create, select VPC only.
   - Name tag: Enter US-VPC.
   - IPv4 CIDR block: Enter 10.32.0.0/16.
   - IPv6 CIDR block: Leave as No IPv6 CIDR block.
   - Tenancy: Leave as Default.
   - Click *Create VPC*
  - Verification: You should see your new VPC listed. Note down its VPC ID.

 10. Create an Internet Gateway (IGW):
   - In the left navigation pane, click on *Internet Gateways*.
   - Click *Create internet gateway*.
    - Name tag: Enter US-VPC-IGW.
    - Click *Create internet gateway*.
    - Verification: The IGW will be in a detached state.
    - Attach the Internet Gateway to your VPC:
    - Select the newly created US-VPC-IGW Internet Gateway.
    - Click *Actions*, then Attach to VPC.
    - Select US-VPC from the dropdown list.
    - Click *Attach internet gateway*.
   -  Verification: The IGW state should change to attached.

 11. Create Public Subnets (using /20 with distinct ranges):
   - In the left navigation pane, *click on Subnets*.
   - Click *Create subnet*.
    - VPC ID: Select US-VPC.
   - For each public subnet:
    - Subnet name: US-Public-Subnet-AZ1 (for AZ1)
    - Availability Zone: Select us-east-1a.
    - IPv4 CIDR block: Enter 10.32.0.0/20.
    - Click *Add new subnet*.
    - Subnet name: US-Public-Subnet-AZ2 (for AZ2)
    - Availability Zone: Select the second AZ us-east-1b.
    - IPv4 CIDR block: Enter 10.32.32.0/20.
    - Click *Create subnet*.
   - Verification: You should see your two public subnets listed with the correct CIDRs.

 12. Create Private Subnets (using /20 with distinct ranges):
   - Still on the Subnets page, click *Create subnet* again.
    - VPC ID: Select US-VPC.
   - Private subnet 1:
    - Subnet name: US-Private-Subnet-AZ1 (for AZ1).
    - Availability Zone: Select the same first AZ us-east-1a.
    - IPv4 CIDR block: Enter 10.32.16.0/20.
    - Click *Add new subnet*.
    - Subnet name: US-Private-Subnet-AZ2 (for AZ2).
    - Availability Zone: Select the same second AZ us-east-1b.
    - IPv4 CIDR block: Enter 10.32.48.0/20.
    - Click *Create subnet*.
   - Verification: You should now have four subnets (2 public, 2 private) listed for US-VPC with their respective distinct CIDRs.

 13. Configure Auto-Assign Public IPv4 Addresses for Public Subnets:
   - In the Subnets list, select US-Public-Subnet-AZ1.
   - Click *Actions*, then Modify auto-assign IP settings.
    - Check the box next to Enable auto-assign public IPv4 address.
    - Click *Save*.
   - In the Subnets list, select US-Public-Subnet-AZ2.
  - Click *Actions*, then Modify auto-assign IP settings.
   - Check the box next to Enable auto-assign public IPv4 address.
   - Click *Save*.
   - Verification: This setting is crucial so that EC2 instances launched into these public subnets automatically receive a public IP address, allowing direct internet access.

 14. Create a Public Route Table and Associate it with Public Subnets:
   - In the left navigation pane, click on *Route Tables*.
   - Click Create *route table*.
    - Name tag: US-Public-RT.
    - VPC: Select US-VPC.
    - Click Create *route table*.
   - Add Route to IGW:
    - Select US-Public-RT.
    - Click the *Routes* tab, then *Edit routes*.
    - Click *Add route*.
    - Destination: 0.0.0.0/0.
    - Target: Select Internet Gateway and then choose US-VPC-IGW.
    - Click *Save changes*.
    - Associate with Public Subnets:
    - Still with US-Public-RT selected, click the *Subnet associations* tab, then *Edit subnet associations*.
    - Check the boxes next to US-Public-Subnet-AZ1 and US-Public-Subnet-AZ2.
    - Click *Save associations*.
   - Verification: Confirm the 0.0.0.0/0 route to the IGW and that both public subnets are associated.

 15. Create Private Route Tables (one per AZ):
   - In the left navigation pane, click on *Route Tables*.
   - Click *Create route table*.
    - Name tag: US-Private-RT-AZ1.
    - VPC: Select US-VPC.
    - Click *Create route table*.
    - Associate with Private Subnet 1:
    - Select US-Private-RT-AZ1.
    - Click the *Subnet associations* tab, then *Edit subnet associations*.
    - Check the box next to US-Private-Subnet-AZ1.
    - Click *Save associations*.

## 2 Create NAT Gateways
Allow internet access for you private resources
		
 1. Allocate Elastic IP (EIP) for NAT Gateway in AZ1:
  - Ensure you are in Region 1 eu-west-2.
  - Navigate to VPC > Elastic IPs (under Virtual Private Cloud in the left pane).
  - Click *Allocate Elastic IP address*.
   - Network border group: Choose the default for your region.
   - Click *Allocate*.
   - Name tag: Add EU-NAT-EIP-AZ1. Note down the Elastic IP address.
 2. Allocate Elastic IP (EIP) for NAT Gateway in AZ2:
  - Ensure you are in Region 1 eu-west-2.
  - Navigate to VPC > Elastic IPs (under Virtual Private Cloud in the left pane).
  - Click *Allocate Elastic IP address*.
  - Network border group: Choose the default for your region.
  - Click *Allocate*.
   - Name tag: Add EU-NAT-EIP-AZ2. Note down the Elastic IP address.

 3. Create NAT Gateway
  - Navigate to VPC > NAT Gateways.
  - Click *Create NAT gateway*.
   - Name: EU-NAT-GW-AZ1.
   - Subnet: Select EU-Public-Subnet-AZ1 (10.16.0.0/20). Crucial: *NAT Gateway must be in a public subnet*.
   - Elastic IP allocation ID: Choose the Elastic IP you allocated for AZ1 (EU-NAT-EIP-AZ1).
   - Click Create NAT gateway.
  - Verification: The NAT Gateway status will transition to Pending and then Available. Wait for it to be Available before proceeding.
  - Create NAT Gateway in Public Subnet AZ2:
   *Repeat the above step for AZ2*
   - Name: EU-NAT-GW-AZ2.
   - Subnet: Select EU-Public-Subnet-AZ2 (10.16.32.0/20).
   - Elastic IP allocation ID: Choose the Elastic IP you allocated for AZ2 (EU-NAT-EIP-AZ2).
   - Click *Create NAT gateway*.
  - Verification: Wait for it to be Available.

 4. Update Private Route Tables to Route via NAT Gateways:
  - Navigate to VPC > Route Tables.
  - For EU-Private-RT-AZ1.
   - Select EU-Private-RT-AZ1.
   - Click the *Routes* tab, then *Edit routes*.
   - Click *Add route*.
   - Destination: 0.0.0.0/0 (for all internet traffic).
   - Target: Select NAT Gateway and choose EU-NAT-GW-AZ1.
   - Click *Save changes*.
  - For EU-Private-RT-AZ2:
  - Select EU-Private-RT-AZ2.
   - Click the *Routes* tab, then *Edit routes*.
   - Click *Add route*.
   - Destination: 0.0.0.0/0.
   - Target: Select NAT Gateway and choose EU-NAT-GW-AZ2.
   - Click *Save changes*.
  - Verification: Each private route table should now have a default route pointing to the NAT Gateway in its respective Availability Zone.

 5. Allocate Elastic IPs for region 2:
  - For Region 1 US, US-VPC:
  - Allocate Elastic IP (EIP) for NAT Gateway in AZ1:
    *Ensure you are in Region 2 us-east-1*.
  - Navigate to VPC > Elastic IPs.
  - Click *Allocate Elastic IP address*.
   - Network border group: Choose the default for your region.
   - Click *Allocate*.
   - Name tag: Add EU-NAT-EIP-AZ1. Note down the Elastic IP address.
  - Repeat for AZ2: Allocate another Elastic IP and name it EU-NAT-EIP-AZ2.

 6. Create NAT Gateway
  - Navigate to VPC > NAT Gateways.
   - Click *Create NAT gateway*.
   - Name: EU-NAT-GW-AZ1.
   - Subnet: Select US-Public-Subnet-AZ1 (10.32.0.0/20). *Crucial: NAT Gateway must be in a public subnet*.
   - Elastic IP allocation ID: Choose the Elastic IP you allocated for AZ1 (EU-NAT-EIP-AZ1).
   - Click *Create NAT gateway*.
  - Verification: The NAT Gateway status will transition to Pending and then Available. Wait for it to be Available before proceeding
  - Create NAT Gateway in Public Subnet AZ2:
  - Repeat the above step for AZ2.
   - Name: US-NAT-GW-AZ2.
   - Subnet: Select EU-Public-Subnet-AZ2 (10.32.32.0/20).
   - Elastic IP allocation ID: Choose the Elastic IP you allocated for AZ2 (US-NAT-EIP-AZ2)
   - Click *Create NAT gateway*.
   - Verification: Wait for it to be Available.

 7. Update Private Route Tables to Route via NAT Gateways:
  - Navigate to VPC > Route Tables.
  - For EU-Private-RT-AZ1:
  - Select US-Private-RT-AZ1.
  - Click the *Routes* tab, then *Edit routes*.
  - Click *Add route*.
   - Destination: 0.0.0.0/0 (for all internet traffic).
   - Target: Select NAT Gateway and choose EU-NAT-GW-AZ1.
   - Click *Save changes*.
  - For EU-Private-RT-AZ2:
  - Select US-Private-RT-AZ2.
  - Click the *Routes* tab, then *Edit routes*.
   - Click *Add route*.
   - Destination: 0.0.0.0/0.
   - Target: Select NAT Gateway and choose EU-NAT-GW-AZ2.
   - Click *Save changes*.
  - Verification: Each private route table should now have a default route pointing to the NAT Gateway in its respective Availability Zone.

 8. Create security groups for region 1
  - For Region 1 EU-VPC:
  - Create "Allow SSH (My IP)" Security Group (for administrative access to EC2 instances):
  - Ensure you are in Region 1 eu-west-2.
  - Navigate to VPC > Security Groups.
  - Click *Create security group*.
   - Security group name: SSH-Access-SG.
   - Description: Allow SSH access from my IP.
   - VPC: Select EU-VPC.
  - Inbound rules:
   - Click *Add rule*.
  - Outbound rules: 
  - Leave as default (All traffic, All ports, 0.0.0.0/0).
    - Click *Create security group*.
  - Create "EC2 Test Instance" Security Group (for general purpose EC2s and testing inter-VPC traffic):
  - Click *Create security group again*.
   - Security group name: EC2-Test-SG.	
   - Description: Security group for general EC2 instances for network testing.
   - VPC: Select EU-VPC.
  - Inbound rules:
   - Click *Add rule*. Type: All ICMP - IPv4. Source: Select Custom and enter the CIDR blocks of both your VPCs (e.g., 10.16.0.0/16, 10.32.0.0/16). This will allow EC2s in either VPC to ping each other once the Transit Gateway is up.
  - Outbound rules: Leave as default (All traffic, All ports, 0.0.0.0/0). (This allows outbound internet access via NAT Gateway for private instances, and direct for public ones).
   - Click *Create security group*.

 9. Create security groups for region 2.
  - For Region 1 us-east-1 US-VPC:
  - Create "Allow SSH (My IP)" Security Group (for administrative access to EC2 instances):
  - Ensure you are in Region 1 us-east-1.
  - Navigate to VPC > Security Groups.
  - Click *Create security group*.
   - Security group name: SSH-Access-SG.
   - Description: Allow SSH access from my IP.
   - VPC: Select EU-VPC.
  - Inbound rules:
   - Click *Add rule*.						.
    Outbound rules: Leave as default (All traffic, All ports, 0.0.0.0/0).
   - Click *Create security group*.
  - Create "EC2 Test Instance" Security Group (for general purpose EC2s and testing inter-VPC traffic):
  - Click *Create security group* again.
   - Security group name: EC2-Test-SG.
   - Description: Security group for general EC2 instances for network testing.
   - VPC: Select EU-VPC.
  - Inbound rules:
   - Click Add rule. Type: All ICMP - IPv4. Source: Select Custom and enter the CIDR blocks of both your VPCs (e.g., 10.16.0.0/16, 10.32.0.0/16). This will allow EC2s in either VPC to ping each other once the Transit Gateway is up.
  - Outbound rules: Leave as default (All traffic, All ports, 0.0.0.0/0). (This allows outbound internet access via NAT Gateway for private instances, and direct for public ones).
   - Click *Create security group*.

## 3 Create The Transit Gateway
Create the central routing hub that will enable communication between the two VPCs.

 1. Create Transit Gateway in Region 1 eu-west-2:
  - Ensure you are in Region 1 eu-west-2.
  - Navigate to VPC > Transit Gateways.
  - Click *Create Transit Gateway*.
   - Name tag: Enter EU-Region-TGW.
   - Description: Transit Gateway for EU-VPC.
   - Leave other options as default (e.g., Auto accept shared attachments, Default route table association, Default route table propagation).
   - Click *Create Transit Gateway*.
  - Verification: The TGW status will be Pending then Available. Note down its Transit Gateway ID.

 2. Create Transit Gateway in Region 2 (us-east-1):
  - Switch to Region 2 (e.g., us-east-1).
  - Navigate to VPC > Transit Gateways.
  - Click *Create Transit Gateway*.
   - Name tag: Enter US-Region-TGW.
   - Description: Transit Gateway for US-VPC.
   - Leave other options as default.
   - Click *Create Transit Gateway*.
  - Verification: The TGW status will be Pending then Available. Note down its Transit Gateway ID.

 3. Create Transit Gateway Peering Attachment (from Region 1 to Region 2):
  - Ensure you are in Region 1 eu-west-2.
  - Navigate to VPC > Transit Gateway Attachments.
  - Click *Create Transit Gateway Attachment*.
   - Attachment type: Select Peering Connection.
   - Transit Gateway ID: Select EU-Region-TGW.
   - Peer region: Select Region 2 (e.g., us-east-1).
   - Peer Transit Gateway ID: Enter the Transit Gateway ID of US-Region-TGW that you noted from Step 4.2.
   - Peer account ID: Enter your AWS Account ID (the same account you are currently using).
   - Tags (optional): Add Name: EU-US-TGW-Peering.
   - Click *Create Transit Gateway Attachment*.
  - Verification: The status will be Pending Acceptance.

 4. Accept Transit Gateway Peering Attachment (in Region 2):
  - Switch to Region 2 us-east-1.
  - Navigate to VPC > Transit Gateway Attachments.
   - Select the Pending Acceptance peering attachment (it will have a name like tgw-attach-xxxxxxxxxxxxx).
   - Click *Actions*, then Accept Transit Gateway attachment.
  - Verification: The status will change to Accepting and then Available.

 5. Create VPC Attachment for EU-VPC (in Region 1):
  - Ensure you are in Region 1 eu-west-2.
  - Navigate to VPC > Transit Gateway Attachments.
  - Click *Create Transit Gateway Attachment*.
   - Attachment type: Select VPC.
   - Transit Gateway ID: Select EU-Region-TGW.
   - VPC ID: Select EU-VPC.
   - Subnet IDs: Select your two private subnets
   - Options (optional): You can keep DNS support and IPv4 DNS support enabled.
   - Click *Create Transit Gateway Attachment*.
  - Verification: Status will be Pending then Available. Note down its Transit Gateway Attachment ID.

 6. Create VPC Attachment for US-VPC (in Region 2):
  - Switch to Region 2 us-east-1.
  - Navigate to VPC > Transit Gateway Attachments.
  - Click *Create Transit Gateway Attachment*.
   - Attachment type: Select VPC.
   - Transit Gateway ID: Select US-Region-TGW.
   - VPC ID: Select US-VPC.
   - Subnet IDs: Select your two private subnets
   - Click *Create Transit Gateway Attachment*.
   - Verification: Status will be Pending then Available. Note down its Transit Gateway Attachment ID.
   
 7. Update EU-VPC Private Route Tables to route to US-VPC via TGW:
  - Ensure you are in Region 1 eu-west-2.
  - Navigate to VPC > Route Tables.
  - For EU-Private-RT-AZ1:
  - Select it, go to Routes tab, click *Edit routes*.
  - Click *Add route*.
   - Destination: 10.32.0.0/16 (the CIDR of US-VPC).
   - Target: Select Transit Gateway and choose EU-Region-TGW the transit gateway attachment you just made.
   - Click *Save changes*.
  - For EU-Private-RT-AZ2:
  - Select it, go to Routes tab, click *Edit routes*.
  - Click *Add route*.
   - Destination: 10.32.0.0/16 (the CIDR of US-VPC).
   - Target: Select Transit Gateway and choose EU-Region-TGW the transit gateway attachment you just made.
   - Click *Save changes*.
  - Verification: Both private route tables in EU-VPC should now have a route to 10.32.0.0/16 pointing to the Transit Gateway.

 8. Update US-VPC Private Route Tables to route to EU-VPC via TGW:
  - Switch to Region 2 us-east-1.
  - Navigate to VPC > Route Tables.
  - For US-Private-RT-AZ1:
  - Select it, go to Routes tab, click *Edit routes*.
  - Click *Add route*.
   - Destination: 10.16.0.0/16 (the CIDR of EU-VPC).
   - Target: Select Transit Gateway and choose US-Region-TGW (your TGW in this region).
   - Click *Save changes*.

 9.  US-Private-RT-AZ2:
  - Switch to Region 2 us-east-1.
  - Navigate to VPC > Route Tables.
  - For US-Private-RT-AZ2:
  - Select it, go to Routes tab, click *Edit routes*.
  - Click *Add route*.
   - Destination: 10.16.0.0/16 (the CIDR of EU-VPC).
   - Target: Select Transit Gateway and choose US-Region-TGW (your TGW in this region).
   - Click *Save changes*.
  - Verification: Both private route tables in US-VPC should now have a route to 10.16.0.0/16 pointing to the Transit Gateway.

 10. Verify Transit Gateway Route Tables & Add Static Routes Default Behavior:
   *AWS Transit Gateways, by default, will propagate routes from attached VPCs and peering connections to their default route table*.
   - In Region 1 eu-west-2, go to VPC > Transit Gateway Route Tables. Select the default route table for EU-Region-TGW. Check the Routes tab and Associations tab. You should see propagated routes for 10.16.0.0/16 (from EU-VPC attachment) and 10.32.0.0/16 (from the peering attachment).
   - Select *routes*.
   - Select *create static route*.
    - US route 10.32.0.0/16 on the peering attachment of the transit gateway
   - In Region 2 us-east-1 go to VPC > Transit Gateway Route Tables. Select the default route table for US-Region-TGW. Check the Routes tab and Associations tab. You should see propagated routes for 10.32.0.0/16 (from US-VPC attachment) and 10.16.0.0/16 (from the peering attachment).
   - Select *routes*.
   - Select *create static route*.
    - EU route 10.16.0.0/16 on the peering attachment of the transit gateway.

## 4 Deploy EC2

 1. Create an instance profile that allows SSM.
  - Navigate to the IAM console. You can search this in the search box in the top left of the screen.
  - Select *roles* from the left hand pane.
  - Click *Create Role*.
    - AWS service.
    - Select EC2.
    - Select EC2 Role for AWS Systems Manager.
    - Click *next*.
    - Click *next*
    - Role name: "allow-ssm"
    - Click *Create role*
 2. Launch EC2 Instances in EU-VPC Private Subnet:
   *Ensure you are in Region 1 eu-west-2*
  - Navigate to EC2 > Instances.
  - Click *Launch instances*.
   - Name: EU-Private-Test-Instance-AZ1.
   - Application and OS Images Amazon Linux 2023 AMI.
   - Instance type: Select t2.micro or t3.micro (free-tier eligible).
   - Key pair (login): Proceed without a key pair (Not recommended).
  - Network settings:
   - VPC: Select EU-VPC.
   - Subnet: Select EU-Private-Subnet-AZ1.
   - Auto-assign Public IP: Ensure this is Disabled.
   - Firewall (security groups): Select existing security group.
   - Choose SSH-Access-SG AND EC2-Test-SG.
   - Configure storage: Leave default.
  - Advanced details: 
   - IAM Instance Profile: allow-ssm
  - User data: 
    !#/bin/bash
    yum update -y
   - Click *Launch instance*.
  - Verification: Instance state should be Pending then Running. Note its Private IPv4 address. You will not see a Public IP as it's in a private subnet.
   *Ensure you are in Region 1 eu-west-2*.
  - Launch instanace 2.
  - Navigate to EC2 > Instances.
  - Click *Launch instances*.
   - Name: EU-Private-Test-Instance-AZ2
   - Application and OS Images Amazon Linux 2023 AMI.
   - Instance type: Select t2.micro or t3.micro (free-tier eligible).
   - Key pair (login): Proceed without a key pair (Not recommended).
  - Network settings:
   - VPC: Select EU-VPC.
   - Subnet: Select EU-Private-Subnet-AZ2.
   - Auto-assign Public IP: Ensure this is Disabled.
   - Firewall (security groups): Select existing security group.
   - Choose SSH-Access-SG AND EC2-Test-SG.
   - Configure storage: Leave default.
  - Advanced details: 
   - IAM Instance Profile: allow-ssm
  - User data: 
    !#/bin/bash
    yum update -y
   - Click *Launch instance*.
  - Verification: Instance state should be Pending then Running. Note its Private IPv4 address. You will not see a Public IP as it's in a private subnet.

 3. Launch EC2 Instance in US-VPC Private Subnet:
  - Switch to Region 2 us-east-1.
  - Navigate to EC2 > Instances.
  - Click *Launch instances*.
   - Name: US-Private-Test-Instance-AZ1.
   - Application and OS Images Amazon Linux 2023 AMI.
   - Instance type: Select t2.micro or t3.micro (free-tier eligible).
   - Key pair (login): Proceed without a key pair (Not recommended).
  - Network settings:
   - VPC: Select US-VPC.
   - Subnet: Select US-Private-Subnet-AZ1.
   - Auto-assign Public IP: Ensure this is Disabled.
   - Firewall (security groups): Select existing security group.
   - Choose SSH-Access-SG AND EC2-Test-SG.
   - Configure storage: Leave default.
  - Advanced details: 
   - IAM Instance Profile: allow-ssm
  - User data: 
    !#/bin/bash
    yum update -y
   - Click *Launch instance*.
  - Verification: Instance state should be Pending then Running. Note its Private IPv4 address. You will not see a Public IP as it's in a private subnet.
  - Launch instance 2.
   *Ensure you are in Region 1 us-east-1*.
  - Navigate to EC2 > Instances.
  - Click *Launch instances*.
   - Name: US-Private-Test-Instance.-AZ2
   - Application and OS Images Amazon Linux 2023 AMI.
   - Instance type: Select t2.micro or t3.micro (free-tier eligible).
   - Key pair (login): Proceed without a key pair (Not recommended).
  - Network settings:
   - VPC: Select US-VPC.
   - Subnet: Select US-Private-Subnet-AZ2.
   - Auto-assign Public IP: Ensure this is Disabled.
   - Firewall (security groups): Select existing security group.
   - Choose SSH-Access-SG AND EC2-Test-SG.
   - Configure storage: Leave default.
  - Advanced details: 
   - IAM Instance Profile: allow-ssm
  - User data: 
    !#/bin/bash
    yum update -y
   - Click *Launch instance*.
  - Verification: Instance state should be Pending then Running. Note its Private IPv4 address. You will not see a Public IP as it's in a private subnet.
 
## 5 Configure VPC Flow Logs
 - For EU-VPC:
 - Navigate to VPC > VPC Flow Logs.
 - Click *Create flow log*.
  - Filter: Select VPC. Resource: Select EU-VPC.
  - Max aggregation interval: 1 minute.
  - Destination: Send to CloudWatch Logs.
  - Log group: Enter a new name, e.g., /aws/vpc/flowlogs/eu-vpc.
  - IAM role: Create a new IAM role or choose an existing one with permissions to publish to CloudWatch Logs (AWS will often suggest one with the necessary permissions if you create a new one here).
  - Click *Create flow log*.
 - Repeat for US-VPC in Region 2.
 - For US-VPC:
 - Navigate to VPC > VPC Flow Logs.
 - Click *Create flow log*.
  - Filter: Select VPC. Resource: Select US-VPC.
  - Max aggregation interval: 1 minute.
  - Destination: Send to CloudWatch Logs.
  - Log group: Enter a new name, /aws/vpc/flowlogs/us-vpc.
  - IAM role: Select the IAM role created in for EU-VPC.
  - Click *Create flow log*.

## 6 Testing

 1. Test Outbound Internet Access from Private EC2 Instances (via NAT Gateway) & Inter-AZ connectivity:
  - Connect to EU-Private-Test-Instance:
  - Navigate to EC2 > Instances, select EU-Private-Test-Instance-AZ1
  - Click *Connect*
  - Select Session Manager
  - Click *Connect*.
  -	Once connected to the instance's shell, run a command to test internet connectivity:
   - ping google.com
   - curl ifconfig.me (This should show the public IP of your NAT Gateway).
   - sudo yum update -y (This will attempt to download updates).
   - Ping *replace with private IP EU-Private-Test-Instance-AZ2* (If successful you should see 'reply')
 - Expected Result: All commands should succeed, demonstrating outbound internet access.
 - Connect to EU-Private-Test-Instance-AZ2:
  - Navigate to EC2 > Instances, select EU-Private-Test-Instance-AZ2
  - Click *Connect*
  - Select Session Manager
  - Click *Connect*.
  -	Once connected to the instance's shell, run a command to test internet connectivity:
   - ping google.com
   - curl ifconfig.me (This should show the public IP of your NAT Gateway).
   - sudo yum update -y (This will attempt to download updates).
   - Ping *replace with private IP of EU-Private-Test-Instance-AZ1* (If successful you should see 'reply')
 2. Connect to US-Private-Test-Instance:
  *Repeat the process for the US-Private-Test-Instance in Region 2*.
  - Expected Result: Same as above, outbound internet access should work via its respective NAT Gateway.
  - Connect to US-Private-Test-Instance:
  - Navigate to EC2 > Instances, select US-Private-Test-Instance-AZ1
  - Click *Connect*
  - Select Session Manager
  - Click *Connect*.
  - Once connected to the instance's shell, run a command to test internet connectivity:
   - ping google.com
   - curl ifconfig.me (This should show the public IP of your NAT Gateway).
   - sudo yum update -y (This will attempt to download updates).
   - Ping *replace with private IP of US-Private-Test-Instance-AZ2* (If successful you should see 'reply')
  - Expected Result: All commands should succeed, demonstrating outbound internet access.
  - Connect to US-Private-Test-Instance-AZ2:
  - Navigate to EC2 > Instances, select US-Private-Test-Instance-AZ2
  - Click *Connect*
  - Select Session Manager
  - Click *Connect*.
  - Once connected to the instance's shell, run a command to test internet connectivity:
   - ping google.com
   - curl ifconfig.me (This should show the public IP of your NAT Gateway).
   - sudo yum update -y (This will attempt to download updates).
   - Ping *replace with private IP of US-Private-Test-Instance-AZ1* (If successful you should see 'reply')

 3. Test Inter-VPC/Inter-Region Connectivity (via Transit Gateway):
  - This is the primary test for the Transit Gateway setup.
  - From EU-Private-Test-Instance-AZ1 (in Region 1) ping US-Private-Test-Instance-AZ1 (in Region 2):
  - Connect to EU-Private-Test-Instance using Session Manager.
  - Run the ping command, replacing *private IP of US-Test-Instance-AZ1* with the private IP of your US-Private-Test-Instance:
   - ping *private IP of US-Test-Instance-AZ1* 
  - Expected Result: Pings should be successful (you might see some packet loss initially, but it should stabilize). This confirms the Transit Gateway peering and routing are working.
  - Run the ping again from EU-Test-Instance-AZ2 to US-Test-Instance-AZ2
   - ping *private IP of US-Test-Instance-AZ2* 
  - Expected Result: Pings should be successful (you might see some packet loss initially, but it should stabilize). This confirms the Transit Gateway peering and routing are working. 
  - From US-Private-Test-Instance (in Region 2) ping EU-Private-Test-Instance (in Region 1):
  - Run the ping command, replacing *private IP of EU-Test-Instance-AZ1* with the private IP of your US-Private-Test-Instance:
   - ping *private IP of EU-Test-Instance-AZ1* 
  - Expected Result: Pings should be successful (you might see some packet loss initially, but it should stabilize). This confirms the Transit Gateway peering and routing are working.
  - Run the ping again from US-Test-Instance-AZ2 to EU-Test-Instance-AZ2
   - ping *private IP of EU-Test-Instance-AZ2* 

 4. Troubleshooting Tips
  - If pings fail, double-check:
   - Security Group EC2-Test-SG inbound rules (All ICMP from both VPC CIDRs).
   - VPC Private Route Tables (routes to opposite VPC CIDR point to TGW).
   - Transit Gateway Route Tables (associations, static routes and propagations are correct).
   - Transit Gateway Peering Attachment status (Available).
  - Check VPC flow logs.
   - CloudWatch > Log Groups > select the log group of one of the VPCs.
   - Check logs for traffic information.
  - Ensure NAT gateways are attached to both the public subnets *NOT PRIVATE* (Step 2).
  - Ensure that you have routes pointing from your private subnets to the NAT Gateway (Step 2).

## 7 Clean-up
Tidying up the console to ensure no charges

   *For Region 1 (eu-west-2)*
 1. Terminate EC2 Instances:
  - Navigate to EC2 > Instances.
  - Select EU-Private-Test-Instance.
  - Click *Instance state* > Terminate instance. Confirm termination.
  - Repeat for other instances launched. 

 2. Delete NAT Gateways:
  - Navigate to VPC > NAT Gateways.
  - Select EU-NAT-GW-AZ1 and EU-NAT-GW-AZ2.
  - Click *Actions* > Delete NAT Gateway. Confirm deletion.

 3. Release Elastic IPs:
  - Navigate to VPC > Elastic IPs.
  - Select EU-NAT-EIP-AZ1 and EU-NAT-EIP-AZ2.
  - Click *Actions* > Release Elastic IP addresses. Confirm release.

 4. Delete Transit Gateway Attachments (VPC Attachment):
  - Navigate to VPC > Transit Gateway Attachments.
  - Select the attachment for EU-VPC (its name usually indicates its attached VPC ID).
  - Click *Actions* > Delete Transit Gateway Attachment. Confirm deletion.

 5. Delete Transit Gateway Peering Connection (initiating side):
  - Navigate to VPC > Transit Gateway Attachments.
  - Select the EU-US-TGW-Peering attachment.
  - Click *Actions* > Delete Transit Gateway Attachment. Confirm deletion.

 6. Delete Transit Gateway:
  - Navigate to VPC > Transit Gateways.
  - Select EU-Region-TGW.
  - Click *Actions* > Delete Transit Gateway. Confirm deletion by typing delete in the confirmation box.

 7. Delete Private Route Tables:
  - Navigate to VPC > Route Tables.
  - Select EU-Private-RT-AZ1 and EU-Private-RT-AZ2.
  - Click *Actions* > Delete route table. Confirm deletion.

 8. Delete Public Route Table:
  - Select EU-Public-RT.
  - Click Actions > Delete route table. Confirm deletion.

 9. Delete Internet Gateway:
  - Navigate to VPC > Internet Gateways.
  - Select EU-VPC-IGW.
  - Click Actions > Detach from VPC. Confirm detachment.
  - Then, with it still selected, click Actions > Delete internet gateway. Confirm deletion.
  
 10. Delete Subnets:
  - Navigate to VPC > Subnets.
  - Select EU-Public-Subnet-AZ1, EU-Private-Subnet-AZ1, EU-Public-Subnet-AZ2, EU-Private-Subnet-AZ2.
  - Click Actions > Delete subnet. Confirm deletion.
  
 11. Delete Security Groups:
  - Navigate to VPC > Security Groups.
  - Select SSH-Access-SG and EC2-Test-SG.
  - Click Actions > Delete security groups. Confirm deletion. (Ensure no instances are using them before deleting).

 12. Delete VPC:
  - Navigate to VPC > Your VPCs.
  - Select EU-VPC.
  - Click Actions > Delete VPC. Confirm deletion by typing delete in the confirmation box.

    *For Region 2 (us-west-2)*
 1. Terminate EC2 Instances:
  - Navigate to EC2 > Instances.
  - Select US-Private-Test-Instance-AZ1.
  - Click *Instance state* > Terminate instance. Confirm termination.
  - Repeat for other instances launched.

 2. Delete NAT Gateways:
  - Navigate to VPC > NAT Gateways.
  - Select US-NAT-GW-AZ1 and US-NAT-GW-AZ2.
  - Click *Actions* > Delete NAT Gateway. Confirm deletion.

 3. Release Elastic IPs:
  - Navigate to VPC > Elastic IPs.
  - Select US-NAT-EIP-AZ1 and US-NAT-EIP-AZ2.
  - Click *Actions* > Release Elastic IP addresses. Confirm release.

 4. Delete Transit Gateway Attachments (VPC Attachment):
  - Navigate to VPC > Transit Gateway Attachments.
  - Select the attachment for US-VPC (its name usually indicates its attached VPC ID).
  - Click *Actions* > Delete Transit Gateway Attachment. Confirm deletion.

 5. Delete Transit Gateway Peering Connection (initiating side):
  - Navigate to VPC > Transit Gateway Attachments.
  - Select the US-TGW-Peering attachment.
  - Click *Actions* > Delete Transit Gateway Attachment. Confirm deletion.
  
 6. Delete Transit Gateway:
  - Navigate to VPC > Transit Gateways.
  - Select US-Region-TGW.
  - Click *Actions* > Delete Transit Gateway. Confirm deletion by typing delete in the confirmation box.

 7. Delete Private Route Tables:
  - Navigate to VPC > Route Tables.
  - Select US-Private-RT-AZ1 and US-Private-RT-AZ2.
  - Click *Actions* > Delete route table. Confirm deletion.

 8. Delete Public Route Table:
  - Select US-Public-RT.
  - Click Actions > Delete route table. Confirm deletion.

 9. Delete Internet Gateway:
  - Navigate to VPC > Internet Gateways.
  - Select US-VPC-IGW.
  - Click Actions > Detach from VPC. Confirm detachment.
  - Then, with it still selected, click Actions > Delete internet gateway. Confirm deletion.

 10. Delete Subnets:
  - Navigate to VPC > Subnets.
  - Select US-Public-Subnet-AZ1, US-Private-Subnet-AZ1, US-Public-Subnet-AZ2, US-Private-Subnet-AZ2.
  - Click Actions > Delete subnet. Confirm deletion.
  
 11. Delete Security Groups:
  - Navigate to VPC > Security Groups.
  - Select SSH-Access-SG and EC2-Test-SG.
  - Click Actions > Delete security groups. Confirm deletion. (Ensure no instances are using them before deleting).

 12. Delete VPC:
  - Navigate to VPC > Your VPCs.
  - Select EU-VPC.
  - Click Actions > Delete VPC. Confirm deletion by typing delete in the confirmation box.
