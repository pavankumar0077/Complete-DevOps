# AWS LAMBDA

What problem Lambda is solving in AWS
--
- Compute -- Belong to Compute family like EC2 
- Serverless

Lamba
--
- It solve the problem of instances, For example if we have a python based application on this conpute that belongs to lambda.
- The difference between EC2 and lambda functions follow the SERVERLESS architecture.
- That means if we are spinning up a LAMBDA function, Now AWS automatically take care of SERVER depending on the application we are creating
- Like python or nodejs. AWS will automatically create conpute for us depending up on the requirements.
- One the entrire application is done. AWS WILL TIER DOWN the compute. Scale up and scale down is takind care by AWS
- In EC2 we have do manually
- Lamda function it will automatically increase the compute and tier down when application running is done.

Example
--
- Where is a food delivery platform. When a user creates a request (checkout --> payment done) --> Food order is placed.
- When user send request at that time, AWS create infra to run that request or application (payments) -- now the user job is done after that payment
- Once it done aws wil tier down the infra that is create to ru the payment services or application

AWS lambda compare with EC2
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/aeb2e856-c6d5-435e-8ced-f378e842bf3c)
- If we create a EC2 instance we will get any IP address
- If terms of Lambda function we don't need anything like IP address.
- Even we not even that where is instance is created and where it is hosted
- for EC2 we are the owner we can do lot of things

### NOTE: Decision of use server or serverless approach is taken care by DEVELOPMENT TEAMS or architecture, Depends on the applicaiton.

Cost Estimation or Opatimzation
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/193999af-f9d3-4916-8354-cf91b38ee55a)
- Look at all the AWS resources that are there on the platform
- Look for any steal resources, we will ether delete them or send some notification
- Ex: Dev1 created EBS volume before 1 month, and no one is using it delete or notification should be send to dev1
- To Govern the resoures (Monitoring and reporting) using Lambda functions we can see all the aws resources and we can reporing back saying that "Hey i wrote a script as a Devops engineer
and what i noticed is someone has created EC2 instance 1 year ago and instance is running with static IP ADDRESS, that it we are using ELASTIC IP and you will be get costing for Elastic Ip and
EC2 instance and recommed you to delete it this information is shared by the script that we have wrote as a DEVOPS ENGINEER.
- For this script we have run 10 python scripts or 10 lambda functions and this activity every day 10 am. You will definitely perffer lambda functions over ec2 instance
- If we create EC2 instance at 10am and script will run for few minutes after that we have delete the instance.
- If we go with serverless architecture we just have to say to CLOUDWATCH every day 10 am trigger this Lambda fucntion. (We have to use CLOUD WATCH be'coz these lambda functions are event driven)
- It has to driven by an EVENT. we configure a CRONJOB and we will trigger the lambda function

