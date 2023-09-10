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



===========================================================================
===========================================================================
===========================================================================

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/eec41fea-6e53-4ed0-b561-6426734a177b)

1) **Manage any Infra** --  If we need any new CLOUD Provider, The hashicrop people will write the code to interact to the API. We just need to give the details of theat particular Cloud provider in terraform script.
2) **Track your infrastructure** -- In Terraform we don't need to login to the cloud and see what are created. We can keep track of infra using terraform state file.
3) **Automate changes** -- Like if you want to update and like increase the instances then we don't need to login in AWS and do the stuff, Instead use terrafrom script and update it and we can even **collaborate** with others using github don't upload state.tf exact that we can upload other files so other can also update it based on requirement.
4) **Standardize configurations** -- There is standard we are maintaining with tf files, like in differnet clouds we use the scripts in different so here we are following the standardized way.

Life cycle of Terraform
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/42a08c72-fac8-42fd-b5ed-e6944bbe12d1)

1) Write the configuarion files -- If you don't know how to write then search in google like ```hashicrop terrafrom aws``` and you will get the docs by using this link ``` https://registry.terraform.io/providers/hashicorp/aws/latest/docs ```
2) Once we write the terraform configuration files, terraform takes the request create an api request and send to target cloud provider.
3) Let's say you wrote something wrong, then terrafrom supports DRY-RUN feature
4) DRY-RUN is a way using which we can acutally see what are the things that going to be happen when we execute
5) Terraform plan -- will check the mistakes and show up
6) Terrform apply -- to apply the config files and create the infra.

Install Terraform
--
``` https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli ```
or Google it

NOTE : Always install the latest version to get support of all clouds providers.

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/b81acc54-3e5e-4fa2-8992-51e789a789c6)

1) Terraform init -- will intialize the terraform
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/94587575-6519-4290-841a-20b4b7d5c816)

Basic main.tf file
--
```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.16"
    }
  }

  required_version = ">= 1.2.0"
}

provider "aws" {
  region  = "us-west-2"
}

resource "aws_instance" "app_server" {
  ami           = "ami-830c94e3"
  instance_type = "t2.micro"

  tags = {
    Name = "Terraform_Demo"
  }
}
```
1) terrform block
2) require_providers -- Here it can single providers or multi providers like AWS, AZURE the version we can get from docs
``` https://registry.terraform.io/providers/hashicorp/aws/latest/docs ```
3) if we add AZURE in feature in the tf file then we need to re-initilize the terraform using ``` terraform init ```
4) required_version == used to support terraform
5) privider_region -- If we don't mention this then also it will work, BUt but default terraform will take the default region that is us-east, ANd this block not mandatory
6) till this line all the abvoe code will be same and static
7) resources -- What type of resources you want to create like ec2, s3 and etc, we and create one more at a time.Org's always use input,output files are variable like name, version and etc to update.

To to authenticate AWS and make terrform works with cli
--
1) Install AWS CLI
2) ``` aws configure ```
3) Next we need to go to aws ui and on right side profile click on credentails and find access and copy and paste and it will configured.
4) NOW TERRAFORM WILL WORK, We have executed that script, Terraform init, plan , and apply
5) After creating we are getting only instance ip which is not enough, we need more details like ip, private ip and etc
6) For that we need to create **outputs.tf** file. provide all the details what all we needed.
7) we have variables.tf and calling them in main.ft file
8) **terraform.tf.state** is imp file and it should not be given to anyone, It is used for tracking the infra
9) If we change something in main.tf file then it will tracked using **terrafrom.tfstate** file All the info about services.

Problem
--
1) terraform state file can not be uploaded to github
2) And we all have different state files, so we can't merge it

SOLUTION
--
1) Put the state file in one centralized location.
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/fddebc6e-849f-4e94-b3bb-5a7be202d5ec)

# NOTE: If we are owner of the file and if we update it the TERRAFORM will be corrupted, terraform will not understand.

REMOTE BACK-END
--
1) AWS S3 BUCKETS
2) AZURE STORAGE CONTAINERS
3) First we need to create the BACK-END and DYNAMO DB

```
terraform {
  backend "s3" {
    bucket         = "THE_NAME_OF_THE_STATE_BUCKET"
    key            = "some_environment/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    kms_key_id     = "THE_ID_OF_THE_KMS_KEY"
    dynamodb_table = "THE_ID_OF_THE_DYNAMODB_TABLE"
  }
}
```
```
resource "aws_s3_bucket" "terraform_state" {
  # With account id, this S3 bucket names can be *globally* unique.
  bucket = "${local.account_id}-terraform-states"

  # Enable versioning so we can see the full revision history of our
  # state files
  versioning {
    enabled = true
  }

  # Enable server-side encryption by default
  server_side_encryption_configuration {
    rule {
      apply_server_side_encryption_by_default {
        sse_algorithm = "AES256"
      }
    }
  }
}

# ------------------------------------------------------------------------------
# CREATE THE DYNAMODB TABLE
# ------------------------------------------------------------------------------

resource "aws_dynamodb_table" "terraform_lock" {
  name         = "terraform-lock"
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "LockID"

  attribute {
    name = "LockID"
    type = "S"
  }
}
```



SENARIO 1 -- FOR SOME REASON ONLY DEVOPS ENGINEERS CAN LOGIN INTO AWS
--     
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/96ae0dc0-2801-4f5e-b4d5-b0ebb74259c6)

1) If someone wants to create the resources they should use the jenkinsfile pipeline which will watch for the terraform resources in github it will pull the files from github and it will create infra in aws.
2) The user will get the information about the instance using output.ft file.
3) Here the state file should be stored in S3 bucket
4) There are 2 dev's who are trying to parallel execute the terraform scripts and there is a conflicting info to AWS. like update ec2 with 2 cpus and other is saying update with 4 cpus. both are running parrel.
5) To solve this problem we are using DYNAMO DB along with the REMOTE BACK-END (S3)
6) Here DYNAMO DB is used to locking the terraform state file. Once it is locked and terraform will say other user is performing some task plase wait. Once it is unlocked he is execute the scripts. TO GET THE LOCKING MACHANISM 
7) Now parrel exectiion is not allowed.


TERRAFORM MODULES
--
1) Existing Modules
2) Write your own Modules

Problems with Terraform
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/1e0f3bd9-013e-496d-be6a-2cb829d8205d)


















