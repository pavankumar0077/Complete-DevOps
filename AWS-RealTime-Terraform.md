#  TERRAFORM WITH AWS PROJECT | AWS REAL TIME PROJECT

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/99007b9b-407f-42be-824f-081d2488487e)

Install Terraform
--
- https://developer.hashicorp.com/terraform/downloads
- For Console we required PASSWORDS, for SDK, Programming Language we required  ACESS KEYS

Install AWS CLI
--
1) https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
2) ``` aws configure `` and setup it

DEMO
--
STEP 1: Create PROVIDER ``` https://registry.terraform.io/providers/hashicorp/aws/latest ``` Copy the code
```
terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
      version = "5.19.0"
    }
  }
}

provider "aws" {
  # Configuration options
}
```
REF LINK DOCS : https://registry.terraform.io/providers/hashicorp/aws/latest/docs
TO FIND ALL TERRAFROM COMMANDS : use ``` terraform ``` and click enter

Step 2 : ``` terrafrom inti ``` --> This command is used to initalize the terrafrom to work with terrafrom (MUST AND SHOULD WE HAVE TO DO THIS)
```
idrbt@idrbt:~/Desktop/DevOps/Terraform/Terraform_Demo$ terraform init

Initializing the backend...

Initializing provider plugins...
- Finding hashicorp/aws versions matching "5.19.0"...
- Installing hashicorp/aws v5.19.0...
- Installed hashicorp/aws v5.19.0 (signed by HashiCorp)

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
idrbt@idrbt:~/Desktop/DevOps/Terraform/Terraform_Demo$
```

Step 3 : Crate main.tf file 
- create VPC, REF LINK : https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/vpc
- create 2 subnets : https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/subnet
- create internet gateway : https://registry.terraform.io/providers/-/aws/latest/docs/resources/internet_gateway
- create route table : https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/route_table
- create route table association : https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/route_table_association

Step 4 :  After creating main.tf file now VALIDATE THE TERRAFORM
- Use ``` terraform validate ```
- ```
  idrbt@idrbt:~/Desktop/DevOps/Terraform/Terraform_Demo$ terraform validate
  Success! The configuration is valid.
```
```
Step 5 : Use ``` terraform plan ``` to check what all resources that will be created if we use the above files (Basically it is like dry run)
```
idrbt@idrbt:~/Desktop/DevOps/Terraform/Terraform_Demo$ terraform plan

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_internet_gateway.igw will be created
  + resource "aws_internet_gateway" "igw" {
      + arn      = (known after apply)
      + id       = (known after apply)
      + owner_id = (known after apply)
      + tags_all = (known after apply)
      + vpc_id   = (known after apply)
    }

  # aws_route_table.RT will be created
  + resource "aws_route_table" "RT" {
      + arn              = (known after apply)
      + id               = (known after apply)
      + owner_id         = (known after apply)
      + propagating_vgws = (known after apply)
      + route            = [
          + {
              + carrier_gateway_id         = ""
              + cidr_block                 = "0.0.0.0/0"
              + core_network_arn           = ""
              + destination_prefix_list_id = ""
              + egress_only_gateway_id     = ""
              + gateway_id                 = (known after apply)
              + ipv6_cidr_block            = ""
              + local_gateway_id           = ""
              + nat_gateway_id             = ""
              + network_interface_id       = ""
              + transit_gateway_id         = ""
              + vpc_endpoint_id            = ""
              + vpc_peering_connection_id  = ""
            },
        ]
      + tags_all         = (known after apply)
      + vpc_id           = (known after apply)
    }

  # aws_route_table_association.rta1 will be created
  + resource "aws_route_table_association" "rta1" {
      + id             = (known after apply)
      + route_table_id = (known after apply)
      + subnet_id      = (known after apply)
    }

  # aws_route_table_association.rta2 will be created
  + resource "aws_route_table_association" "rta2" {
      + id             = (known after apply)
      + route_table_id = (known after apply)
      + subnet_id      = (known after apply)
    }

  # aws_subnet.sub1 will be created
  + resource "aws_subnet" "sub1" {
      + arn                                            = (known after apply)
      + assign_ipv6_address_on_creation                = false
      + availability_zone                              = "us-east-1a"
      + availability_zone_id                           = (known after apply)
      + cidr_block                                     = "10.0.0.0/24"
      + enable_dns64                                   = false
      + enable_resource_name_dns_a_record_on_launch    = false
      + enable_resource_name_dns_aaaa_record_on_launch = false
      + id                                             = (known after apply)
      + ipv6_cidr_block_association_id                 = (known after apply)
      + ipv6_native                                    = false
      + map_public_ip_on_launch                        = true
      + owner_id                                       = (known after apply)
      + private_dns_hostname_type_on_launch            = (known after apply)
      + tags_all                                       = (known after apply)
      + vpc_id                                         = (known after apply)
    }

  # aws_subnet.sub2 will be created
  + resource "aws_subnet" "sub2" {
      + arn                                            = (known after apply)
      + assign_ipv6_address_on_creation                = false
      + availability_zone                              = "us-east-1b"
      + availability_zone_id                           = (known after apply)
      + cidr_block                                     = "10.0.1.0/24"
      + enable_dns64                                   = false
      + enable_resource_name_dns_a_record_on_launch    = false
      + enable_resource_name_dns_aaaa_record_on_launch = false
      + id                                             = (known after apply)
      + ipv6_cidr_block_association_id                 = (known after apply)
      + ipv6_native                                    = false
      + map_public_ip_on_launch                        = true
      + owner_id                                       = (known after apply)
      + private_dns_hostname_type_on_launch            = (known after apply)
      + tags_all                                       = (known after apply)
      + vpc_id                                         = (known after apply)
    }

  # aws_vpc.myvpc will be created
  + resource "aws_vpc" "myvpc" {
      + arn                                  = (known after apply)
      + cidr_block                           = "10.0.0.0/16"
      + default_network_acl_id               = (known after apply)
      + default_route_table_id               = (known after apply)
      + default_security_group_id            = (known after apply)
      + dhcp_options_id                      = (known after apply)
      + enable_dns_hostnames                 = (known after apply)
      + enable_dns_support                   = true
      + enable_network_address_usage_metrics = (known after apply)
      + id                                   = (known after apply)
      + instance_tenancy                     = "default"
      + ipv6_association_id                  = (known after apply)
      + ipv6_cidr_block                      = (known after apply)
      + ipv6_cidr_block_network_border_group = (known after apply)
      + main_route_table_id                  = (known after apply)
      + owner_id                             = (known after apply)
      + tags_all                             = (known after apply)
    }

Plan: 7 to add, 0 to change, 0 to destroy.

───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
idrbt@idrbt:~/Desktop/DevOps/Terraform/Terraform_Demo$ 
```
Step 6 : Use ``` terraform apply ``` command to create the resouces in AWS. It again show the plan and asks for confirmation type ``` yes ```
```
Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_vpc.myvpc: Creating...
aws_vpc.myvpc: Creation complete after 4s [id=vpc-069157067f20e5785]
aws_internet_gateway.igw: Creating...
aws_subnet.sub1: Creating...
aws_subnet.sub2: Creating...
aws_internet_gateway.igw: Creation complete after 2s [id=igw-0b4dfcc2d54f5b720]
aws_route_table.RT: Creating...
aws_route_table.RT: Creation complete after 3s [id=rtb-067b6dd536d521e72]
aws_subnet.sub1: Still creating... [10s elapsed]
aws_subnet.sub2: Still creating... [10s elapsed]
aws_subnet.sub1: Creation complete after 13s [id=subnet-0fb813aa106239e1c]
aws_route_table_association.rta1: Creating...
aws_subnet.sub2: Creation complete after 13s [id=subnet-03ea12d4e8b0dd23b]
aws_route_table_association.rta2: Creating...
aws_route_table_association.rta1: Creation complete after 1s [id=rtbassoc-00c1ff407a5429bf9]
aws_route_table_association.rta2: Creation complete after 1s [id=rtbassoc-0957f5c0be1fd32d5]

Apply complete! Resources: 7 added, 0 changed, 0 destroyed.
```

### NOTE : At this time it will also create the STATE FILE (SAVE IT IN THE REMOTE BACKENDS LIKE S3 BUCKETS)

Step 7 : Create security group 
REF LINK : https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/security_group
- Create S3 bucker : https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket
- Create ec2 instances : https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/instance
- Plan and apply again
- ``` terraform apply -auto-approve ``` Use this command to auto approve after using terraform apply command
- ``` terraform apply -lock=false ``` To not to lock the file

Step 8 :  Crate LOAD BALANCER 
REF LINK : https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/lb


