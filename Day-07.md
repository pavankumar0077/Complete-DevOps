Shell Scripting Basic Project
--

* Managebality ( Reducing the managing over head )
-- Problem -- Team to take care of servers, patches and update and lot of other stuff

* Cost ( Cost effective )
-- Pay as you use

DEVOPS ENGINEER OR AWS ADMIN
-- Should make cost effecitiviness
-- Tracking the resouce usage

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/0666856d-a14e-44cf-9320-8da359433a55)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/17e00c07-5f1c-4c1f-be1d-fb8dae2000ec)

```
#!/bin/bash

##################################
# Author: Pavan
# Date: 16/01/2023

# Version: v1
# This script will report the AWS resource usage
##################################

# AWS S3
# AWS EC2
# AWS Lambda
# AWS IAM Users

# list s3 buckets
aws s3 ls

# list EC2 Instance
aws ec2 describe-instances

# list of EC2 Instances
aws lambda list-functions

# list IAM Users
aws iam list-users
```

IF we want to read in better way use this:
----------------------------------------------
./aws_resource_tracker.sh | more | pipe and more


To get specific info about the instance
---------------------------------------------
aws ec2 describe-instances | jq '.Reservations[].Instances[].InstanceId'
====
-- Here  jq is a command-line utility that can slice, filter, and transform the components of a JSON file.
-- [] is used be'coz of it is list









