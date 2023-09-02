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

Install AWS CLI
--
1) https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
2) use ``` aws configure``` to configure Access Key ID, and Secret Access Key.
3) AWS CLI Documentation ``` https://docs.aws.amazon.com/cli/latest/ ``` Used for any aws service like s3,
ec2 and etc commands and usage.

```

#!/bin/bash

###############################
# Author: Pavan Kumar Dasari
# Date: 2nd-Sept-2023
#
# Version: v1
# This script will report the AWS resource usage
###############################

set -x

# AWS S3
# AWS EC2
# AWS Lambda
# AWS IAM Users

# List s3 buckets
echo "List of s3 buckets"
aws s3 ls > ResourceUsage.txt

# List EC2 Instances

echo "List of list of EC2"
aws ec2 describe-instances | jq '.Reservations[].Instances[].InstanceId' >> ResourceUsage.txt

# List lambda

echo "List of Lambda functions"
aws lambda list-functions >> ResourceUsage.txt

# List IAM users

echo "List of iam users"
aws iam list-users >> ResourceUsage.txt

```

To run the script
--
```./aws_resource_tracker.sh | more```
Here more is used to read the file in better way

**set -x IS DEBUG in the script -- Gives us all the details like it is show the command which is printing, echo statements**

Here we are getting output in json and it is displaying everything, So if we need specfic thing then use 
```aws ec2 describe-instances | jq '.Reservations[].Instances[].InstanceId'``` Is is used to show the specific tag info

Saving the results in file
--
```> ResourceUsage.txt after every command in the script will save the result in the text file```
```>> Used to append and singl e > used to indicate save the result in a txt file.```



jq - json parser
yq - yaml parser


IF we want to read in better way use this:
----------------------------------------------
./aws_resource_tracker.sh | more | pipe and more


To get specific info about the instance
---------------------------------------------
aws ec2 describe-instances | jq '.Reservations[].Instances[].InstanceId'
====
-- Here  jq is a command-line utility that can slice, filter, and transform the components of a JSON file.
-- [] is used be'coz of it is list









