# AWS CLI

PROBLEM
--
1) If we want to create resources on AWS if it is 5 to 10 resources then we can easily create using web ui
2) But what if it is 500 resources that too in limited time


Solution
--
1) AWS CLI -- it will interact with AWS API's


![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/3fb151a9-be72-402d-8115-320bcbd559d6)

Tools to automate the process
--
1) AWS CLI
2) Terraform
3) Cloud Formation Template
4) AWS CDK
5) BOTO3 and etc

NOTE:THE ABOVE TOOLS TO AUTOMATE INFRA IN AWS 

### AWS CLI -- Is a python Utility, using this we can create resources in AWS

Ways to connnect aws
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/fc2e6553-f9af-4408-9727-f444b876a5db)

1) Web Ui
2) API -- though programming

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/a7118feb-2313-4a48-b581-d6eb62b2d17d)
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/24e4f6cf-a189-46fd-b361-1ee343a2eb78)

Why more tools for same use
--
1) We have AWS CLI, CFT AND TERRRAFORM
2) AWS CLI is used for quick command line like list of s3 or ec2
3) CFT and Terraform is used to create complex or infra which is in YAML files where aws cli will find bit difficult to write in command line

Install AWS CLI
--
1) python is pre-request
1) https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
2) after installation check for version for verification

Configure AWS CLI
--
1) User ``` aws configure ``` and set the details --> Get the access key and serect access from the aws web ui
2) Click on your profile --> securetiy credential (right top corner) and yu will find access key create it and stored it in safe place they are senstive
3) Only 2 access keys are allowed to created for the account
```
aws configure
AWS Access Key ID [****************VO7Y]: 
AWS Secret Access Key [****************D/Ry]: 
Default region name [us-east-1]: 
Default output format [json]:
```
COMMANDS
--
1) ``` https://docs.aws.amazon.com/cli/latest/reference/ ``` for all the docs like ec2, s3 and etc services


REF LINKS: for most commonly used cli commands
1) ``` https://gist.github.com/apolloclark/b3f60c1f68aa972d324b ```
2) ``` https://github.com/ravsau/AWS-CLI-Commands ``` 



