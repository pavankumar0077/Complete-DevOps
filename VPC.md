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
13) 
