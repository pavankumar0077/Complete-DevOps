# EC2 DEEP DIVE

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/054bc5eb-5634-4bd5-b782-53e86c241c9e)

### EC2 : Elastic Cloud Compute
Compute : To provide a compute instance which includes cpu ram and disk (Virtual Server)
Elastic : Scale up and scale down

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/cefd5e12-da68-4354-96ac-dc980dbd9fc9)

Why to use EC2
--
1) If someone say that we buy physical server and do logical partitions and create VM and share to team for developement
2) Problem here is we have to maintain it like security update, upgrade and etc


EC2  
--
1) If we use AWS EC2 instance then no need of maintaining, management,
2) cost is less (Pay as you go) if we shutdown no need to pay

Types of EC2
--
1) General
2) Compute Optimized
3) Memory
4) Storage
5) Accelarated

Regions
--
1) Multiple Data Center available all over the world
2) If data center is near then the latency is less if it far latency is more so select regions accordingly
3) **Availablity** -- We have region but why availablity So if the data center have some issue like natural natural disasters etc. In this case if we keep our applicaiton
4) in Differenet differnet available zones then their is not chance of downtime or application issues to the client.

In creation of EC2 
--
1) Key value -- Combination of public private keys, Using private key we can login into instance. Do not share the private key
2) login using public ip of the ec2 instance  ``` ssh -i pavan.pem ubuntu@public.ip ``` for ubuntu server
3) Error pavan.pem are too open --> ``` chmod 600 pavan.pem ``` Now we will able to access
