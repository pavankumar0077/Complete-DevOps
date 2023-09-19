# VPC with Public-private Subnet in Production

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/73b25239-ad27-4ef4-949d-71e95bb64dfe)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/b7998944-8549-4afc-9b49-d1a942294801)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/0a540456-64e5-4bf6-82a9-d2c46a5c8a76)

Why we need NAT gateway
--
1) The application that needs to access the internet for any info like json or api info's
2) It is better if we mask the application ip address when sending a request HERE NAT GATEWAY WILL BE USED.
3) Ex: EC2 instance trying to access the info from the internet now NAT GATEWAY will change the IP ADDRESS to public subnet of NAT and it sends the request
So that your ip address or other data will be hidden
4) If the application or anything that you want to access on the internet is hacked. BUT THEY don't know the ip address of the application

Auto Scaling Group
--
1) Ex: You want to deploy your application in 2 availability zones, Instead of creating EC2 instances 2 times
2) WE CAN SAY AUTO SCALING GROUP, CREATE MINIMUM OF 2 REPLICAS INCASE MY APPLICATION RECEIVES MORE REQUESTS 2 SERVERS ARE NOT ENOUGH TO LOAD TO ACCEPT THE
INCOMING TRAFFIC. ASG will Immediately take a decision to SCALE UP TO 4 servers or 5 .. and more.

Load Balancer
--
1) It will balance the load, Let say we have 100 requests, we have 2 servers SO LOAD BALANCER WILL SENDS THE REQUESTS like 50 requests to one and 50 to other server
2) Apart from this we can also do path based routing,  host based routing

Bastion Host or Jump Server
--
1) The EC2 instances that we have created are in private subnet, they don't have public ip or we can not SSH into this instances directly
2) We want to keep them secured, we dont create any public ips, but we will create a BASTION HOST
3) Though that BASTION HOST or Jump Server we will connect to the ec2 instances
4) Their will proper loggin machanism if we use BASTION HOST, We can configure rules and lot of other things.

# ===========================================================================

NOTE: DOC REF : ``` https://docs.aws.amazon.com/vpc/latest/userguide/vpc-example-private-subnets-nat.html ```
Create VPC
--
1) Select VPC and more if we select VPC only then we have to create lot of configurations like ipv4 v6 subnets and etc,Better to CREATE with VPC and MORE
2) Check preview
3) select NAT gatways as 1 per AZ
4) vpc ENDPOINT AS NONE

ELASTION IP ADDRESS
--
1) IT is nothing but IP address which will remain same even if you GO DOWN THEN the INSTANCE
2) It is like STATIC IP ADDRESS

Create EC2 with AUTO SCALING GROUP
--
CREATION OF TEMPLATE
--
1) Select EC2 instance --> from the left panel select Auto scaling groups --> create auto scaling group
2) It can not be created directly we have to use Template as a reference -->
3) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/3fa8a33f-bdad-4d7d-a02e-e0d7e3ad7d9d)
4) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/e190b6c6-b04a-4ae7-8b8a-978be398cb37)
5) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/9b2735c9-c1ba-421d-8d53-179527aaac64)
6) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/4e5b6bb3-c128-4652-85cd-97918c344a25)
7) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/6ec23a32-d6fc-4772-af78-2908e29a00e6)
8) Rest of the settings are same, The port will be as per application best practise is only allow required ports.

CREATE AUTO SCALING GROUP
--
NOTE: ALL the options are same only mentioned should changed
1) select any name
2) select create luanch template
3) select vpc that you have created
4) select availablity zones as per diagram app should be in private subnet so select 2 private subnets as per diagram
5) Group size as per we need 2 increate to 2 and max to 4 as required based on the requirement

Create BASTION Host
--
1) Now 2 instances are created by they dont have public ip addresses
2) We have to create BASTION HOST IT works as mediatory from private instances and outiside to access the instances
3) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/015d1cab-497d-4a4c-a76e-838725893f67)
4) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/01c5c511-3a56-4de5-a655-8ff07f72ef1b)
5) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/cbad9dba-d654-493b-82bd-b0e1d41fbc7a)
6) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/4b0e0eb6-2417-49ca-9d4b-f4a5bfc5516a)
