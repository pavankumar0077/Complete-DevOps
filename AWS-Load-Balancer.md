# AWS LOAD BALANCERS

1) Application Load Balancer
2) Network Load Balancer
3) GateWay Load Balancer


Senario 1 :
- We have a game which was developed and played by 10-20 users intially, But after gaining
- popularity the game become famous and lot of people started play, not able to access application because of application is giving good user experience
- So we are deploying the application in multiple EC2 instances
- IN FRONT OF THIS 3 EC2 Instances we will place a LOAD BALANCER
- Now for the USER, instead of using EC2 instances use LOAD BALANCER ip to access the application
- NOW TO THE BALANCER is responsible to manage the request and distribute the traffic to the available instances
- Assume that all the instances have same spec like cpu ram and memory, the basic load balancing mechanism can be **ROUND RODIN**
- IF we have 100 requests the LOAD BALANCER will sends the traffic like 33, 33, 33 for the 3 instances
- The downtime is not there now, Highly available application

OSI layers Example :

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/b4f92508-243b-4f58-9e88-70dfe9618097)

Ex: - Lets say we have a user we want to access the LINKEDIN website, User requested a request and from his browser the request will be send to one of the LINKEDIN server
- Now the linkedin server will responsed as user requested for a page and now user can view the page in his browser
- Here from the USER TO SERVER our application is travelling with 7 different layers
- Differnet load balancers act on different layers
- - When user typed URL he uses http --> This is called application layer (LAYER 7) Here we are deciding what protocal we have to use to talk to the SERVER
  - Here our browser is creating HTTP request for us, FROM APPLICATION LAYER IT WILL go to
  - Presentation Layer (6) - Here the request that we are sending should this request should be SECURE Request or insecure request.
  - If you want to create a SECURE request then there should something like SSL, TSL
  - Then the request goes to SESSION LAYAER (5) -- Creating SESSION for our requests, basically deals with session objects
  - When server receives the requests, this server wants to understand few things about the SESSION that this user has INITIATED with the SERVER
  - In the session object we will get the details like what kind of Client as requested, when this request is created, all the information related to the session created between the client and the server
  - From session layer it goes to the TRANSPORT LAYER (4) - Here the request will be split into MULTIPLE PACKETS - This transport layer will make sure that this request is
  - transmitting from the CLIENT to the SERVER in SECURE way and in small small PACKETS
  - If we split this request small small packets then server and easily read the packets and again responsed in small packets that happens in the TRANSPORT LAYER
  - Now it will go to the NETWORK LAYER (3) The small packets in Transport Layer have to travel from user to server through multiple routers the NETWORK LAYER comes into picture here
  - Where our request goes multiple ROUTERS and finally the request will reach to the DATA CENTER
  - DATA LINK Layer (2)  -- Here we have all the switches our request will go from ONE of this ROUTERS, the final ROUTER which is before the SERVER
  - request will go to a switch and it will go to a DATA CENTER
  - From here it will comes to the PHYSICAL LAYER(1) - In this layer (we know all the cables that are connected to the servers)
  - Let say the server has to be connected to a cable and from the cable it has to be connected to a switch  and from the switch it has to be connected to the ROUTER
  - Finally from the cables it will go to the SERVER
 
## APPLICATION LOAD BALANCER

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/5a90e814-f85c-46cb-9351-4690f2137cba)

- ### Devops Engineers and Development Team people will take decision on which type of LOAD BALANCER should be implemented
- If you want perform TRAFFIC LOAD BALANCING on LAYER 7(applicaiton layer)then we should chose APPLICATION LOAD BALANCER
- If you want to perform TRAFFIC LOAD BALANCING on LAYER 4(transport layer) then we should chose NETWORK LOAD BALANCER
- - LAYER 7 He mostly we deal with http traffic in this layer (we have other protocal like sft and etc as well)
  - If we want to do LOAD BALACING at that HTTP LAYER, when a user initiate the request if you want to intercept at LAYER 7 decide up on LOAD BALANCING
  - Depending up on HOST or depending up on Path or Domain and etc
  - We can chose an APPLICAITON LOAD BALANCER
  - Ex: If we are access to the amazon.com/payment -- then it should goes to the particular service
  - if we are accessing to the amazon.com/transaction -- then it should goes to the transcation service
  - Based on endpoint it will send the request to particular service
  - RATION BASED Technique
  - SSL Off loading -- That means even if you send a plan request this load balancer can intiate a SECURE CONNECTION TO THE SERVERS
  - RE- Encrypt
  - Path Route
  - ALB is a costly load balancer -- it will many additonal and advances features
  - IT can INTERCEPT the HTTP Request it can decide the LOAD BALANCING TECHNIQUE
  - ALB little bit slow because it is intercepting and providing the additional capabilites it is costly
  - it is slow becuase the ALB is analysising your LAYER and then it is forwarding to the server there is A HOP in between or stoppage in between 
