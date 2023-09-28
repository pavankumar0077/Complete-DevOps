# VIRTUAL PRIVATE CLOUD

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/108bd383-f963-4c4a-94aa-2e0a076594ee)
1) If we see the above picture we have normal houses and secure houses
2) If thief enter one house in normal houses he can enter other houses as well
3) Let say if the same houses have security so they the thief can not enter into the houses

# Problems before VPC
--
1) We have lot of Regions in AWS, Like some of the companies have their own data centers and some companies can't afford it
2) AWS took this opportunity and created DATA CENTERS Across the world.
3) Like some companies requested for 10 EC2 in mumbai region, Other company also requested for the same and other company also same.
4) AWS created all the instances in one phsyical server for above 3 companies.
5) If hacker hacked 1 instance he can also hack the other instances of other companies as well becuase all the instances are present in one physical server

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/b96badfc-0d6c-401b-a1c0-ddc492a29b16)

After VPC
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/868a33dc-b471-4ee2-a1f9-31b00ab83ca9)

1) So AWS solved the problem using Secured network called VPC
2) Now AWS given a documentation to build secure network, Devops enginners will created secure network
3) So VPC size can be defined with IP ADDRESS RANGE
4) Devops engineers will split the ip to all the projects available
5) Their will a GATEWAY to enter into a Network Devops engineers will create it
6) Public subnet is the one user can access inside VPC, It connects to internet using INTERNET GATEWAY
7) the application access will be done using router route table to load balance and to application or vice versa
8) Route table defines how should the application goes to the load balancer
9) Internet gateway --> Public subnet --> Request goes to Load balancer (assigned with public subnet) --> target group of the application. Now if the request has to go
to the subnet, Load balancer does not know how to go, So for this subnet we will create a Route table. So route table will define and tell to load balancer go in this so
that you will reach me.
11) Now the instance will not allow directly it will access from which IP you are comming from it will confirm and allow the applicatiion to get access.
12) Finally --> If someone from the internet is trying to access tha application in the private subnet. First of the the request has to go through internet gatewway -->
public subnet in the vpc (commmon subnet across vpc) --> Load Balancer (It send the request to the private subnet and the applicaiton) --> For load balancer subnet is present
but path is given in route table --> Once the request reached in the subnet still we have SECURITY GROUP --> Once the security group allows the ip request will be sent
to application.

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/26cb7ea4-7ecf-4d64-bc8f-af89ab10bc71)

1) Someone in the internete tries to access one application with ip 172.17.0.4/24 (In general he will not use private ip address he uses the load balancer ip)
2) To reach So we know Devops engineer created a VPC. In VPC we have internet gateway. The entire VPC has a ip address range. Devops engineer created subnets based on the projects. For each project we have some ip range
3) Internet --> VPC internet gateay --> Public subnet (Which can be accessed to public outside the vpc) --> Load balancer (Elastic load balancer) (Target group - access instance to target group)(private subnet - and at the same time the subnet should have the route table) --> To send request to private subnet there should be a proper route --> route will be decided by route table --> security group to accept or reject the request
4) Internet gateway --> Public subnet --> Load balancer --> Route tables (routers) --> security groups
5) NACL -- automation to security groups
6) If the application wants to access the internet to download some pkg's. So we don't want to expose private ip to internet (it is bad practise) for this we have do
masking of ip address --> It is called NAT GATEWAY
7) NAT gateway --> will change the ip address like public ip of the subnet or router ip to access the internet from the private subnet application instance
8) If it is using load balancer to access then it is called SNAT, if it is using router we call it as NAT gateway
9) NAT gateway will be created in public subnet 
10) VPC flow logs will log the every traffic (some features are paid)



