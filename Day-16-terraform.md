# INFRASTRUCTURE AS CODE - TERRAFORM

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/94cad62b-759d-4021-8be3-495a40e0fe6c)

1) Dev is working for Flipkart application and they need compute resources like cpu,ram and etc they can be on-prime, or any cloud
2) Let's say they have 3000 applicatons, for this applications they need servers, now they can deploy on different differnet
clouds, or physical services. 
3) Finally decided to create all the servers in AWS

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/e4b8442f-8611-4216-99ea-8a05b9380df7)

PROBLEM 1
--
SENARIO 1
--
     
5) And as devops dev you have automated the entrire process of AWS, like by using AWS CLI or AWS cloud formation templates, AWS CDK
6) Finally we have automated the process using AWS Cloud formation templates and created the infra in aws.
7) If their is a required to created 10 ec2, 10 s3 buckets, using Cloud formation template it will created in very less time
and the scripts in CFT's are stable as well.
8) Now, suddent organization planned to move to another cloud, be'coz of costing issues and etc.
9) Now, if someone asks to create 10 vm's or ec2 IN AZURE then we can't do that as CFT is only specific to AWS all are wasted if we use another cloud.
10) Now we have to use Azure resource manager and automated the entire process to create 10 vm's and etc in Azure
11) Now, suddenly organization planned to move to on-prem like create data center and started using it then again all the efforts are wasted, ARM is specific to azure only
12) again the same,we use Heat template to automate the process.

PROBLEM 2
--
SENARIO 2
--

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/ed258a1b-08cf-4ea9-a1fc-cbc534a84f81)

# NOTE: Now organizations are planning to have HYBRID CLOUD model 
HYbrid is a way of using part of infra in AWS, and part of infra in Azure.
1) AWS is giving good support for Storage services.
2) For project management related then we are planned to use Azure devops
3) **NOW OUR ORGANIZATION IS PLANNED TO USE HYBRID CLOUD**
4) As a devops engineer, We have know both clouds, aws cft and azure resource manager. It may vere to org ot org


# NOTE: TO SOLVE THIS PROBLEM WE GOT TERRAFORM DEVELOPED BY HASHICORP
# TERRAFORM USED THE CONECPT OF API AS CODE.

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/3181178e-319e-4a0f-be84-c9c80b9fd269)

1) Now, if we can the TERRAFORM scripst, then it will take care of AWS, AZURE, AND GCP and etc clouds.
2) We just need to tell TERRAFORM that my provider is AWS or Azure or GCP.

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/55177b60-e98b-4044-8076-09c93f6dbf00)
  
4) Here also we need to modify the TERRAFORM scripts for different Clouds, but tarraform says that migration is very smoth
only minimun changes are required.
5) API as Code - Use this concept we can automate any provider using there API's

WHAT TERRAFORM DOES INTERNALLY
--
1) TErraFORM will talk to the API of aws, it talks with api of azure and same with gcp as well.
2) Once you write the terraform script files, terraform will convert them depending on the provider details.
3) In one of the files providers.tf we say that provider is AWS, So terrraform will convert that script in AWS Readable API,
api will excute the actions and gives back the result.

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/502c3a75-a008-49ce-a1d3-81ebbd6f2276)
NOTE: THE Programatic way of talking with application is called API, In general we using UI. But if you want to use the
Details and get some request and response to use in application dev we use API's.

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/520a0fb2-b2cb-4e30-8297-238bf624154e)

1) Terraform did the same, Terraform will actively looks for different clouds API, and it written their own modules
2) Let says if want to create EC2 instance in AWS, we directly calling AWS API, Terraform said that use EC2 modules and write some lines you required
3) Once user submits the request TERRAFORM will will take the request and it will convert your input into API CALL.
4) It will get the request back and it will sent to the user.So as a user we are not directly talking to the AWS API's 

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/d9472c48-069c-4061-8a31-320b7f6ee138)

# Infra as code using AWS CFT's AZURE resource manager., API as code terraform
