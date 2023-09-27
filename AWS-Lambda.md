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

Serverless Uses
--
- Cost Optimization *
- Security/Compliance *

 How lamba is used in security ?
 --
 ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/0c48b13c-aa53-4648-8a8f-91925d9754a6)
 - Let's say your organization decided that nobody will create a EBS volume of type GP2. (GP2 has security issues)
 - If one dev by mistake created EBS volume with GP2
 - We can write a Lambda functin saying that this lambda should run everyday at 10 am to verify there are any GP2 based volumes in AWS account
 - IF there are any. We can trigger a notification using SNS. to the dev
 - Or some dev created S3 with public -- at this time also we can use lambda function to check and run SNS

Create a Lambda function
--
1) Search for lambda functions
2) Create with none, blueprint or other options select as per requriement
3) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/e95bfd6c-a1fa-4dff-8671-20f1cb4e4277)
4) Use IAM user, for this demo i am using none
5) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/a80c26f6-2c32-46bd-acbf-5668e3007e4a)
6) Rest all are default values --> create function
7) create a test event and click on test to  check whether it is working or not.
8) function name should be same : lambda_handler is like main function like java main. We cal also edit the name of the function in config
9) we can add files or folder like requirements.txt
10) We can write the code in local as well and upload the zip file
11) Like we use env variables we can also use that in lambda functions as well.
12) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/e76e8991-ebbc-42cf-9699-90707a3844fa)
13) Bydefault when we created lambda functions. a role will be created. If you dont want to use that role. During the creation of function assign your existing role to it
14) We can change in Change default exection role -- and select role accordingly

