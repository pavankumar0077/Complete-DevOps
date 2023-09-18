# AWS ROUTE 53

ROUTE 53
--
1) AWS provides DNS (Domain name system) as a service with Route 53
2) DNS will map or resolves with the application ip address
3) In real world DNS will resolves with load balancer ip address only

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/a4c4b14e-7340-471f-a6f2-9e6a13e505f2)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/8cbb3fcf-09cb-48b2-b0bb-e0afe24cda10)
1) We can't share load balancer ip because
2) We can't remember ip's and when we restart the application if we haven't given fixed ip then ip will changed and user can not use the application
3) WE NEED TO USE NAMES instead of IP's

DNS
--
1) It will keep a lot of records
2) RECRODS Matches with the IP ADDRESS
3) AWS will

OWN APPLICATION AVAILABLE TO OUT SIDE WORLD
--
1) Gobaby or any domain seller buy a domain name
2) Hosting solution

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/3665ed6d-d25a-41d9-b315-7c5abf6f5e8b)

To solve this issue AWS provides Route 53
-- 
1)  When user hits the URL it will come internet gateway --> Route 53 --> Load balancer --> Application
2)  Route 53 will check for the DNS RECORDS IT WILL RESOLVE THE DNS WITH IP ADDRESS OF THE LOAD BALANCER --> APPLICAITON

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/95b4533f-a02c-4e87-978d-9c3250b2ef71)

Configure route 53 (DOMAIN REGISRATION, HOSTED ZONES, HEALTH CHECKS)
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/5ecb87fc-40a4-4fd4-ab66-0b9fbe4e7537)

1) **DOMAIN REGSITRATION**
2) AWS SELLS THE DOMAIN NAMES OR IF YOU ALREADY HAVE DOMAIN THEN YOU CAN ASLO CONFIGURE IT
3) IF we Buy from aws or outside domain we need to CREATE HOSTED ZONES
4) IN **HOSTED ZONES** WE BASICALLY CREATE THE DNS RECORDS --> DNS MAPPING
5) FROM THE DNS RECORDS IF IP OR NAME IS RESOLVED THEN IT WILL SHOW THE APPLICATION
6) DOMAIN REGISTRATION IS NOT AS PAY AS YOU GO, WE HAVE TO PAY UP FRONT.
7) ROUTE 53 ALSO PERFORMS **HEALTH CHECKS** ON THE WEB APPLICATIONS



