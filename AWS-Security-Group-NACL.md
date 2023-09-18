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


  
