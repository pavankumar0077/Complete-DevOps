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

 
