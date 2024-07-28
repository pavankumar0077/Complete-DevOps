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
- We need to  the pem key file content to bostion host
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

NACLS & SG
--
### NACL's (Network Access Control List) - STATELESS
### SECURITY GROUPS - STATEFUL
![image](https://github.com/user-attachments/assets/1c280ca6-682c-4db6-9659-a760247a6108)
- If there is an incomming request, In the inbound rules of NACL, in the inbound rules it will check for the particular traffic is allowed or not, If it is allowd it opens the door but still we have security group here, In SG as will we have rule to accept or reject the traffic that is comming in, even if the NACL allows it, it doesn't matter that the SG will allow it.
- There should an Inbound rule for the traffic in the SG also. If SG is allowed the traffic it basically reach the EC2 INSTANCE.
- IF SG is allowed something then by default the OUTBOUND is also allowd so same traffic can go out of the Security group without any issues.
- When it comes to NACL while the traffic is leaving the network there will be outbound rules and in the outbound rules it will check this particular traffic is allowed out of the network or not, So if it not allowed it will block it if not it will allow it.

```
Certainly! I'll explain the difference between Network Access Control Lists (NACLs) and Security Groups, focusing on their stateless and stateful nature respectively. I'll use a step-by-step explanation with a real-time example and a text-based representation to help visualize the concept.
Let's start with definitions:

Stateless (NACLs): Does not keep track of the connection state. Each incoming and outgoing packet is evaluated independently.
Stateful (Security Groups): Keeps track of the connection state. If an outgoing request is allowed, the incoming response is automatically allowed, regardless of inbound rules.

Real-time Example Scenario:
Let's consider a web server in a VPC that needs to respond to HTTP requests and also needs to download updates.
Text-based Diagram:
Internet
    |
    v
[NACL] --> [Security Group] --> [Web Server]
Step-by-step Explanation:

Incoming HTTP Request:

NACL (Stateless):
Inbound Rule: Allow TCP port 80 from any source
Outbound Rule: Allow TCP ports 1024-65535 to any destination
Security Group (Stateful):
Inbound Rule: Allow TCP port 80 from any source
Process:
a) The incoming HTTP request on port 80 arrives.
b) NACL checks inbound rule - Allowed
c) Security Group checks inbound rule - Allowed
d) Web server receives the request and processes it

Outgoing HTTP Response:

NACL (Stateless):
Outbound Rule: Allow TCP ports 1024-65535 to any destination
Inbound Rule: Allow TCP port 80 from any source
Security Group (Stateful):
(No explicit outbound rule needed)
Process:
a) Web server sends response (from a random high port, e.g., 52000)
b) Security Group automatically allows the response (stateful)
c) NACL checks outbound rule - Allowed
d) Response reaches the internet

Outgoing Update Request:

NACL (Stateless):
Outbound Rule: Allow TCP port 443 to any destination
Inbound Rule: Allow TCP ports 1024-65535 from any source
Security Group (Stateful):
Outbound Rule: Allow all traffic
Process:
a) Web server initiates HTTPS connection to update server on port 443
b) Security Group allows outbound (rule exists)
c) NACL checks outbound rule - Allowed
d) Request reaches the internet

Incoming Update Response:

NACL (Stateless):
Inbound Rule: Allow TCP ports 1024-65535 from any source
Outbound Rule: Allow TCP port 443 to any destination
Security Group (Stateful):
(No explicit inbound rule needed)
Process:
a) Update server sends response (to the high port used in the request)
b) NACL checks inbound rule - Allowed
c) Security Group automatically allows the response (stateful)
d) Web server receives the update
Key Differences Illustrated:

NACL (Stateless):

Requires explicit rules for both directions (inbound and outbound)
Evaluates each packet independently
Needs careful configuration to allow return traffic


Security Group (Stateful):

Only requires rules for initiated connections
Automatically allows return traffic
Simpler to configure and manage



This stateful nature of Security Groups makes them easier to manage, as you don't need to create explicit rules for return traffic. On the other hand, NACLs provide an additional layer of security by allowing more granular control over traffic flow, at the cost of more complex configuration.
Would you like me to elaborate on any part of this explanation?
```
```
Stateful vs. Stateless:

Stateful (Security Groups):

"Remembers" connections
If you allow traffic in one direction, it automatically allows the response
Like a good host at a party who remembers guests


Stateless (NACLs):

Doesn't "remember" connections
Checks every packet individually, both incoming and outgoing
Like a strict bouncer who checks everyone's ID, every time they enter or leave



Let's use a simple analogy:
Imagine a house party (your network):
Security Group (Stateful):

You're the host (Security Group)
When you invite a friend (allow incoming traffic), you automatically let them in and out
You don't need to give separate permission for them to leave (send a response)

NACL (Stateless):

You have a bouncer (NACL) at the door
The bouncer checks everyone's ID when they enter AND when they leave
Even if you (the host) invited someone, the bouncer still checks their ID every time

Now, let's apply this to a real scenario:
Scenario: A web server responding to a request

Security Group (Stateful):

You allow incoming traffic on port 80 (HTTP)
When a request comes in, it's allowed
The response is automatically allowed out, without needing an explicit rule


NACL (Stateless):

You need a rule to allow incoming traffic on port 80
You also need a separate rule to allow outgoing traffic for the response
Each packet is checked against these rules independently



Why the difference?

Security Groups are stateful because it's simpler and more convenient for most use cases. You don't have to worry about return traffic.
NACLs are stateless to provide an additional layer of security and more granular control, if needed.

Key Takeaway:

Security Groups (Stateful): Allow a connection once, and all related traffic is automatically permitted.
NACLs (Stateless): Every single packet must be explicitly allowed by a rule, in both directions.

This design allows Security Groups to be user-friendly for most scenarios, while NACLs provide an extra layer of control for more complex security requirements.
```

NACL
--
![image](https://github.com/user-attachments/assets/c01b1218-e909-40b4-9c16-2e5a64f1d8c3)

- NACL are like a firewall which control traffic from and to SUBNETS
One NACL per subnet new subnets are assigned the Default NACL ( Allow all the traffic ), BETTER TO CREATE OWN NACL
- You define NACL Rules:
- Rules have a number (I-32766), higher precedence with a lower number
- First rule match will drive the decision
- Example: if you define # 100 ALLOW 10.0.0.10/32 and #200 DENY 10.00 10
address will be allowed because 100 has a higher precedence over 200
- The last rule is an asterisk (*) and denies a request in case of no rule m
- AWS recommends adding rules by increment of 100
- Newly created NACLs will deny everything
- NACL are a great way of blocking a specific IP address at the subnet level.
- BLOCKING A SPECIFIC IP ADDRESS, WHY WE NEED TO USE NACL ONLY WHY NOT SECURITY GROUP, IP ADDRESS SHOULD BLOCK AT THE SUBNET LEVEL NOT AT THE EC2 INSTANCE LEVEL, THERE CAN BE 1000 EC2 INSTANCES IN YOUR PARTICULAR SUBNET, IS IT POSSIBLE TO GO TO EACH EC2 INSTANCE SG AND DENY IT, WE WILL WRITE A NACL RULE LIKE THIS IP ADDRESS IS NOT ALLOWD TO MY SUBNET.

 Default NACL
 --
 ![image](https://github.com/user-attachments/assets/338ff748-a3b2-4b09-95af-37c6f1c8d5fa)

Ephemeral Ports
--
![image](https://github.com/user-attachments/assets/922b1911-08d3-470a-b1d1-96722cca17b0)

- For any two endpoints to establish a connection, they must use ports
- Clients connect to a defined port, and expect a response on an ephemeral port
- Different Operating Systems use different port ranges, examples:
- IANA & MS Windows 10 → 49152 - 65535
- Many Linux Kernels → 32768 - 60999
- Client and Server should have a ports to connection that is EPHEMERAL PORT (client side - open port to listen server)

NACL with Ephemeral Ports
--
![image](https://github.com/user-attachments/assets/610f073e-6000-4859-a304-b9a0d396a1bf)
- The NACL associated with the public subnet must have outbound TCP rule on port 3306 to the DATABASE Subnet
- In the DataBase tier it should allow inboud TCP on port 3306 for the particular DB instance DATABSE should have Inboud rule and Web Tier should have OUtbound rule
- Now DB will sends some reponse back to the client which is WEB-TIER, DB- NACL associated with Private Subnet will have Outbound TCP on port number 1024 to 65535 any of the Ephemeral Ports. Becuase it send on any of Ephemeral Ports the response.
- The web tier should have to inbound of any of Ephermerl port from 1024 to 65535, If not response will not be received by the web teir.

Create NACL Rules for Each Target subnets CIDR
--
![image](https://github.com/user-attachments/assets/80998616-9845-4174-90a3-f4c450cffb68)
- Why here NACL is associated with TWO SUBNETS, rule is 1 NACL PER SUBNET, but 1 NACL CAN HAVE MULTIPLE SUBNETS

 Security Groups Vs NALCs
- 
![image](https://github.com/user-attachments/assets/6bde904f-2c34-47c0-9006-37337675954d)

NACL DEMO
--
- ![image](https://github.com/user-attachments/assets/8d77e5fa-04a6-4a7e-95ac-772a51b415bf)
- ![image](https://github.com/user-attachments/assets/5ea7f920-ded4-45f1-993b-73f22a2119ef)
- ![image](https://github.com/user-attachments/assets/84c1d468-6e31-4dd4-8736-cf94c62c5472)
- WE CAN OBSERVE ONE THING BY DEFAULT IT IS NOT ALLOWING ANY TRAFFIC AND BY DEFAULT NOT  ALLOW ANY TRAFFIC TO GO OUTSIDE.
- Now, If we are connecting to any ec2 instance which is in public, in the above example we have BASTION host ec2 instance, It will not connect.
- Perviously why is working it at that time our public instance is connected to BY default NACL which allows all the traffic in and out, But now we have created our own NACL which will not allows, we need to write for it. Even security group is allowing offer ssh 22 port but nsl is not allowing
- ![image](https://github.com/user-attachments/assets/c5ded84b-17c5-4784-9b1b-0de81b3f7141)
- WE NEED TO WRITE INBOUND RULE  AS WELL AS OUTBOUND, GENERALLY WE FORGET THIS LIKE IN SG WE WILL ALLOW AND THEIR IS NOT NEED TO WRITE OUTBOUND RULE IN SG, BUT IN NACL WE NEED TO WRITE THE OUTBOUND RULE AS WELL.
- Allow any traffic out of the MY SUBNET
- ![image](https://github.com/user-attachments/assets/d16822df-23c6-4683-83e6-947946b4d43c)

