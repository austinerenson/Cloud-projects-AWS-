# Build a VPC

This project focused on building and configuring a Virtual Private Cloud (VPC) 
on AWS to strengthen my networking skills.

## Project Walkthrough
<img width="507" height="421" alt="image" src="https://github.com/user-attachments/assets/927727bf-92b2-443f-ac42-7f4ac1b0b2d4" />


1. Create a VPC
   - Head to the VPC section in AWS and create a new VPC. Give it a name and set 
     a CIDR block. I used 10.0.0.0/16 which gives 65,536 possible addresses.
   - The formula to calculate addresses is 2^(32-n), where n is the prefix number.

2. Create Subnets
   - Create a subnet and link it to your VPC. Go into the subnet settings and 
     turn on auto-assign public IPv4 so that any instance launched gets a 
     public IP address automatically.

3. Set up an Internet Gateway
   - An internet gateway is what connects your VPC to the internet.
   - Create one, give it a name, and attach it to your VPC.

4. Create a second VPC using CloudShell
   - Run the following commands in CloudShell:
```bash
   aws ec2 create-vpc --cidr-block 10.0.0.0/24 --query Vpc.VpcId --output text
   aws ec2 create-tags --resources=VPC-ID --tags Key=Name,Value="Name-of-2nd-VPC"
   aws ec2 create-subnet --vpc-id VPC-ID --cidr-block 10.0.0.0/25
   aws ec2 create-internet-gateway
   aws ec2 attach-internet-gateway --vpc-id VPC-ID --internet-gateway-id IG-ID
```
   - Note: The prefix number in the subnet CIDR block must be larger than 
     the one used for the VPC CIDR block.
   - Once done, the internet gateway should appear in the Resource Map.

## VPC Traffic Flow & Security

1. Set up a Route Table
   - A route table is automatically created with your VPC, just rename it.
   - Add a destination of 0.0.0.0/0 and point it to your internet gateway.
   - Associate the route table with your Public subnet to make it publicly accessible.

2. Set up a Security Group
   - Create a security group and attach it to your VPC.
   - Add an inbound rule to allow HTTP traffic from anywhere.
   - Outbound traffic is allowed by default, so no changes needed there.

3. Set up a Network ACL
   - Create a Network ACL, name it, and associate it with your VPC.
   - Add inbound and outbound rules to allow all traffic.
   - Associate it with your public subnet.
   - Here is how traffic flows through the setup:
   
   `User → Internet Gateway → VPC → Route Table → Network ACL → 
   Public Subnet → Security Group → EC2 Instance → Data Sent`

4. Track VPC Resources Across Regions
   - Use CloudShell to spin up resources in different regions:
```bash
   # Create VPC in another region
   aws ec2 create-vpc --cidr-block 10.0.0.0/24 --query "Vpc.VpcId" --output text \
   --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=new-region-VPC}]' \
   --region REGION-CODE

   # Create internet gateway
   aws ec2 create-internet-gateway --query "InternetGateway.InternetGatewayId" \
   --output text \
   --tag-specifications 'ResourceType=internet-gateway,Tags=[{Key=Name,Value="new-region-IG"}]' \
   --region REGION-CODE

   # Create security group
   aws ec2 create-security-group --query "GroupId" --output text \
   --description "Security group for new region" \
   --group-name new-region-sg \
   --tag-specifications 'ResourceType=security-group,Tags=[{Key=Name,Value="new-region-sg"}]' \
   --region REGION-CODE
```
   - Use AWS Global View to see all your resources across regions in one place.
   - To clean up, delete resources with:
```bash
   aws ec2 delete-vpc --vpc-id VPC-ID
   aws ec2 delete-security-group --group-id SG-ID
   aws ec2 delete-internet-gateway --internet-gateway-id IG-ID
```
