# AWS VPC
- VPC = Virtual Private Cloud
- You can have multiple VPCs in an AWS region (max. 5 per region - soft limit)
- Max. CIDR per VPC is 5, for each CIDR:
- Min. size is /28 (16 IP addresses)
- Max. size is /16 (65536 IP addresses)
- Because VPC is private, only the Private Pv4 ranges are allowed:
- 10.0.0.0 - 10.255.255.255 (10.0.0.0/8)
- 172.16.0.0 - 172.31.255.255 (172.16.0.0/12)
- 192.168.0.0 - 192.168.255.255 (192.168.0.0/16)
 Your VPC CIDR should NOT overlap with your other networks (e.g., corporate Like ON-PREM or Data Center)

Vpc - Subnet
--
- AWS reserves 5 IP addresses (first 4 & last 1) in each subnet
- These 5 IP addresses are not available for use and can't be assigned to an
EC2 instance
- Example: if CIDR block 10.0.0.0/84, then reserved IP addresses are:
- 10.0.0.0 - Network Address
- 10.0.0. 1 - reserved by AWS for the VPC router
- 10.0.0.2 - reserved by AWS for mapping to Amazon-provided DNS
- 10.0.0.3 - reserved by AWS for future use
- 10.0.0.255 - Network Broadcast Address. AWS does not support broadcast in a VPC,
therefore the address is reserved
- Exam Tip, if you need 29 IP addresses for EC2 instances:
- You can't choose a subnet of size /27 (32 IP addresses, 32 - 5 = 27 < 29)
- You need to choose a subnet of size /26 (64 IP addresses, 64 - 5 = 59 > 29)

How to Calculate the subnect CIDR rangte
--
``` https://www.ipaddressguide.com/cidr ``` 
- Use the above website to CIDR range
- ![image](https://github.com/user-attachments/assets/cf59ae90-31f6-4670-b50a-e15e3f30b2a6)
- The above IMAGE IS FOR VPC - CIDR /16
- When we are creating subnet we can choose the CIDR range
- ![image](https://github.com/user-attachments/assets/2663b828-4048-405d-8cf6-cbe802916c6a)
- ![image](https://github.com/user-attachments/assets/52b3ef04-0f9e-4ce2-b832-50f1c6360586)
- The above is for public subnet
- Now for private subnet we need to select other octat, as 255 is the last form 0 - 255, we have to take before number
- ![image](https://github.com/user-attachments/assets/a9c8c80a-facd-440a-8377-2f70192b8aef)


IGW 
--
- Allows resources (e.g., EC2 instances) in a VPC connect to the Internet
- It scales horizontally and is highly available and redundant
- Must be created separately from a VPC
- One VPC can only be attached to one IGW and vice versa
- **Internet Gateways on their own do not allow Internet access...**
- **Route tables must also be edited!**
- ![image](https://github.com/user-attachments/assets/75d20198-c385-4701-8fe2-d93320019ced)

# Bastion Host

![image](https://github.com/user-attachments/assets/2ccb4e28-3be1-45d2-a461-3703c7e45651)
- Private instance doesn't have direct communication with the internet,
- We use public instance for patching or updating
- Both should be in same VPC
- Should allow required ports
- We can use a Bastion Host to SSt
our private EC2 instances
- The bastion is in the public subnet
then connected to all other privat
- Bastion Host security group must
inbound from the internet on po
restricted CIDR, for example the
CIDR of your corporation
- Security Group of the EC2 Instances must
allow the Security Group of the Bastion
Host, or the private IP of the Bastion host

- ![image](https://github.com/user-attachments/assets/3d6afd93-83c1-4f94-a8c0-a289ed912f51)
- ![image](https://github.com/user-attachments/assets/8ee7ab7f-f93a-451d-8328-2bf0a152fc8e)
- We need to copy the pem key file content to bostion host
- ![image](https://github.com/user-attachments/assets/1e886848-9475-40f9-ab2f-d2fb932fe6f9)
- ![image](https://github.com/user-attachments/assets/b247cb7b-5114-47c4-a126-0db61d36f1b7)
- We have sucessfully logined into private EC2 INSTANCE
- ![image](https://github.com/user-attachments/assets/9ce8caf6-6550-4741-bd5f-7490d2cef500)
- Even i have given anywhere it is be able to connect be'coz it is not connected to IGW
- I have given source as PublicSG be'coz it allows 22 port

NAT Gateway
--
![image](https://github.com/user-attachments/assets/d84a0c5e-4a0c-429b-8b0e-4ddf9a2b52b0)

- AWS-managed NAT, higher bandwidth, high availability, no administration
- Pay per hour for usage and bandwidth
- NATGW is created in a specific Availability Zone, uses an Elastic IP
- Can't be used by EC2 instance in the same subnet (only from other
subnets)
- Requires an IGW (Private Subnet => NATGW => IGW)
- 5 Gbps of bandwidth with automatic scaling up to 45 Gbps
- No Security Groups to manage / required
- Private EC2 instance in private subnet -- It not accept any incomming connections, IT NEEDS TO COMMUNICATE WITH INTERNET FOR UPDATES AND PATCHING
- WE HAVE BASTION HOST WHY CAN'T WE USE IT, WHY WE NEED TO USE NAT GATWAT, Using Bostion host we can COMMUNICATE WITH ONE TIME WITH ONE INSTANCE.
- WHEN WE ARE USING NAT GATEWAY IT WILL NOT ALLOW INCOMING TAFFIC IT ONLY ALLOWS OR USE OUTSIDE TRAFFIC
- WORKS WITH SPECIFIC AZ only.

Demo
--
- Crate a NAT GATEWAY, It should be PUBLIC subnet -- To get the INTERNET.
- ![image](https://github.com/user-attachments/assets/66e8b377-ab04-4b2c-bf2f-34a962fed3f4)
- It should have FIXED IP ADDRESS, ALLOCATE ELASTIC IP AS WELL.
- As of now we have private subnet to local - that means it can we connect to only in the VPC CIDR all the instance we can communicate with it.
- ![image](https://github.com/user-attachments/assets/53768530-a00f-44cc-9744-ec485b99cf85)
- ![image](https://github.com/user-attachments/assets/1836fb3d-71d3-411e-8aa2-2ae928b7f4b1)
- Edit the routes and add nat gatewway.
- NOW ANY IP WHICH WANTS TO COMMUNICATE WITH THE OUTSIDE WORLD, IT WILL GO TO THE NAT GATEWAY WITH THE PARTICULAR ROUTE THAT WE HAVE ADDED 
- Without NAT GATEWAY NO internet into PRIVATE INSTANCE.
- ![image](https://github.com/user-attachments/assets/e2f16b3c-1730-4c35-9676-8d13c172a86a)
- After adding the ROUTE RULE ADDING NAG GETEWAY WE WILL GET THE INTERNET.
- Now we got the internet
- ![image](https://github.com/user-attachments/assets/ac21432e-eec0-4173-83de-92222184a12e)
- PRIVATE INSTANCE - NAT - IGW - PUBLIC INTENET

![image](https://github.com/user-attachments/assets/0700eb09-4d3d-4d91-ba19-e2316dc4070f)

NAT INSTANCE
--
![image](https://github.com/user-attachments/assets/133d14e1-cc66-446f-b210-18d821bc13c8)
- It is nothing but a EC2 instance



- 
