# AWS VPC [Security Group & NACL]

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/be0dcb0f-a8b6-474d-bc1c-73e290944ce2)

1) From the Load balancer the request will go to private subnet, their
WE CAN ADD SECURITY FOR PRIVATE SUBNET LAYER which is called NACL.
2) If we don't add the security over there then it we have add the security
3) EC2 instance level is called Security Group

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/34cd239e-8d97-4daa-90be-213d4cc5bbf0)

5) IN AWS SECURITY IS ALWAYS A SHARED RESPONSIBILITY that means AWS says
we will give security by VPC, Security Group, NACL, API along with that
we need help of devops or aws admins or systems enginner or network to be more secured.
6) NACL is called last point of security in AWS.
7) Before request reaching application we have NACL OR SG

Security Group
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/ebb646dd-26b6-42fa-a984-8eca49529b86)

1) It serves at the instance level
2) Bydefault aws gives the instance with vpc
3) In Security groups we have 2 things inbound traffic and outbound traffic
4) We user is trying to send request to the app'n present in EC2 then it is called
INBOUND Traffic
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/923c2ff4-5273-480e-b4cd-472d2bc85eaf)
6) The application responses to the user is called OUTBOUND traffic.
7) Ex: Amazon website, User trying to access amazon website and amazon website is tryping to access payment gateway
like amazon pay or razorpay. User --> Amazon site is inbound
Amazon webiste --> Amazonpay is outbound.
8) We have to manage inbound and outbound security for the ec2 instance.
9) AWS bydefault created security group for the instance and allows ALL THE PORT, Expect port 25
10) AWS bydefault deny inbound traffic

PORT 25
--
1) AWS does not allow outbound traffic for the PORT 25, Because it is mailing service
2) AWS by default allow port 25, because of any spaming activites etc

NACL (Network Access Control List)
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/df4ec36a-aef0-4e24-9d1f-2989dadcfc27)
1) Security group is applied for EC2 instance level, where applied at the subnet level
2) Ex: Devlopment team used EC2 instance with Jenkins server and for easy use they have allowed all the port instead of 8080
3) If devops engineers DENY TRAFFIC using NACL in the subnet level, even EC2 level is allowed then also appn will not get any traffic
4) If something is applied to subnet level bydefault it will be applied to all the instances with in the subnet
5) NACL will add additonal layer of security
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/7b0006c3-4205-4bce-b134-6b1c4d6f9267)
7) We can also use NACL for automation
8) Ex: If we have 10000 of EC2 instead of adding Security group, add NACL for the subnet then will be followed to all the EC2 instance present it is easy then using security groups.
9) NACL == Deny traffic + Allow Traffic
10) In security group we have only allow option

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/5c0e62fb-1a31-4c37-aef2-a76b8c3bfad3)
1) ... lines represents virtual private cloud
2) We have create virtual private cloud and provide IP ADDRESS RANGE, AWS bydefault will create Internet gateway, NACL, Route table
3) additonal we will create EC2 instance and attach a security group for this EC2 instance 

  
