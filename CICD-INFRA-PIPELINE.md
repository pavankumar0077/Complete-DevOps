# CREATE INFRA USING CICD (JENINS) AND IAC (TERRAFORM)

```
Environment Setup:

First, ensure you have the following installed on your Jenkins server:

Jenkins (latest LTS version)
Terraform (latest version)
AWS CLI
jq (for JSON parsing)

Install these additional Jenkins plugins:

Slack Notification Plugin
Email Extension Plugin
AnsiColor Plugin (for colored console output)
Workspace Cleanup Plugin


Jenkins Configuration:

a. Configure Slack in Jenkins:

Go to "Manage Jenkins" > "Configure System"
Find the Slack section
Add your Slack workspace and Bot User OAuth Access Token
Test the connection

b. Configure Email in Jenkins (alternative to Slack):

Go to "Manage Jenkins" > "Configure System"
Set up the E-mail Notification section with your SMTP server details

c. Add the following credentials in Jenkins:

AWS credentials (use IAM role if possible, otherwise access key and secret)
Git credentials (if using a private repository)
Terraform Cloud token (if using Terraform Cloud for state management)

Repository Structure:

.
├── environments
│   ├── dev
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   └── prod
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
├── modules
│   ├── ec2
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   └── s3
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
├── scripts
│   └── notify.sh
├── Jenkinsfile
└── README.md

Terraform Code:
environments/dev/main.tf

provider "aws" {
  region = var.aws_region
}

module "ec2_instance" {
  source        = "../../modules/ec2"
  instance_type = var.instance_type
  ami_id        = var.ami_id
  environment   = "dev"
}

module "s3_bucket" {
  source      = "../../modules/s3"
  bucket_name = var.bucket_name
  environment = "dev"
}

terraform {
  backend "s3" {
    bucket = "my-terraform-state-bucket"
    key    = "dev/terraform.tfstate"
    region = "us-west-2"
  }
}

modules/ec2/main.tf
resource "aws_instance" "example" {
  ami           = var.ami_id
  instance_type = var.instance_type
  tags = {
    Name        = "ExampleInstance-${var.environment}"
    Environment = var.environment
  }
}

modules/s3/main.tf
resource "aws_s3_bucket" "example" {
  bucket = "${var.bucket_name}-${var.environment}"
  acl    = "public-read"

  website {
    index_document = "index.html"
    error_document = "error.html"
  }

  tags = {
    Name        = "ExampleBucket-${var.environment}"
    Environment = var.environment
  }
}

resource "aws_s3_bucket_public_access_block" "example" {
  bucket = aws_s3_bucket.example.id

  block_public_acls       = false
  block_public_policy     = false
  ignore_public_acls      = false
  restrict_public_buckets = false
}

```
# JENKINSFILE 

```
pipeline {
    agent any

    parameters {
        choice(name: 'ENVIRONMENT', choices: ['dev', 'prod'], description: 'Select the environment')
        string(name: 'SLACK_CHANNEL', defaultValue: '#devops-notifications', description: 'Slack channel for notifications')
    }

    environment {
        AWS_ACCESS_KEY_ID     = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
        TF_IN_AUTOMATION      = '1'
        TERRAFORM_VERSION     = '1.0.0'
    }

    options {
        ansiColor('xterm')
        timestamps()
        timeout(time: 1, unit: 'HOURS')
        disableConcurrentBuilds()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Terraform Setup') {
            steps {
                sh "tfenv install ${TERRAFORM_VERSION}"
                sh "tfenv use ${TERRAFORM_VERSION}"
            }
        }

        stage('Terraform Init') {
            steps {
                dir("environments/${params.ENVIRONMENT}") {
                    sh 'terraform init -input=false'
                }
            }
        }

        stage('Terraform Validate') {
            steps {
                dir("environments/${params.ENVIRONMENT}") {
                    sh 'terraform validate'
                }
            }
        }

        stage('Terraform Plan') {
            steps {
                dir("environments/${params.ENVIRONMENT}") {
                    sh 'terraform plan -out=tfplan -input=false'
                    sh 'terraform show -json tfplan > tfplan.json'
                    stash name: 'tfplan', includes: 'tfplan'
                    stash name: 'tfplan-json', includes: 'tfplan.json'
                }
            }
        }

        stage('Approval') {
            when {
                expression { params.ENVIRONMENT == 'prod' }
            }
            steps {
                script {
                    def plan = readFile 'environments/prod/tfplan.json'
                    def changes = evaluatePlanChanges(plan)
                    if (changes > 0) {
                        slackSend(channel: params.SLACK_CHANNEL, color: 'warning', message: "Production infrastructure changes detected. Please review and approve: ${env.BUILD_URL}")
                        input message: 'Do you want to apply this plan?', ok: 'Apply'
                    } else {
                        echo "No changes detected in production plan. Proceeding without approval."
                    }
                }
            }
        }

        stage('Terraform Apply') {
            steps {
                dir("environments/${params.ENVIRONMENT}") {
                    unstash 'tfplan'
                    sh 'terraform apply -input=false -auto-approve tfplan'
                }
            }
        }

        stage('Capture Outputs') {
            steps {
                dir("environments/${params.ENVIRONMENT}") {
                    script {
                        def ec2_public_ip = sh(script: 'terraform output -raw ec2_public_ip', returnStdout: true).trim()
                        def s3_bucket_name = sh(script: 'terraform output -raw s3_bucket_name', returnStdout: true).trim()
                        
                        writeFile file: 'outputs.json', text: """{
                            "ec2_public_ip": "${ec2_public_ip}",
                            "s3_bucket_name": "${s3_bucket_name}"
                        }"""
                        
                        archiveArtifacts artifacts: 'outputs.json', allowEmptyArchive: false
                    }
                }
            }
        }
    }

    post {
        success {
            script {
                def outputs = readJSON file: "environments/${params.ENVIRONMENT}/outputs.json"
                def message = "Infrastructure created successfully in ${params.ENVIRONMENT} environment:\n" +
                              "EC2 Public IP: ${outputs.ec2_public_ip}\n" +
                              "S3 Bucket Name: ${outputs.s3_bucket_name}\n" +
                              "Build URL: ${env.BUILD_URL}"
                slackSend(channel: params.SLACK_CHANNEL, color: 'good', message: message)
                // Uncomment the following line if you want to send an email as well
                // emailext body: message, subject: "Infrastructure Creation Successful - ${params.ENVIRONMENT}", to: 'team@example.com'
            }
        }
        failure {
            slackSend(channel: params.SLACK_CHANNEL, color: 'danger', message: "Infrastructure creation failed in ${params.ENVIRONMENT} environment. Check the build logs: ${env.BUILD_URL}")
            // Uncomment the following line if you want to send an email as well
            // emailext body: "Infrastructure creation failed. Check the build logs: ${env.BUILD_URL}", subject: "Infrastructure Creation Failed - ${params.ENVIRONMENT}", to: 'team@example.com'
        }
        always {
            cleanWs()
        }
    }
}

def evaluatePlanChanges(plan) {
    def planJson = readJSON text: plan
    def changes = planJson.resource_changes.sum { it.change.actions.size() }
    return changes
}
```
```
Notification Script (optional for more complex notifications):
scripts/notify.sh:
#!/bin/bash

# This script can be used for more complex notification logic
# You can call this script from the Jenkinsfile if needed

SLACK_WEBHOOK_URL="$1"
MESSAGE="$2"

curl -X POST -H 'Content-type: application/json' --data "{\"text\":\"$MESSAGE\"}" "$SLACK_WEBHOOK_URL"

```
```
Explanation of the enhanced pipeline:

Environment-specific configurations: We've organized the Terraform code into environment-specific directories (dev and prod) for better management.
Modular Terraform structure: The EC2 and S3 resources are defined as reusable modules.
Terraform state management: We're using an S3 backend for storing Terraform state, which is crucial for team collaboration and state locking.
Jenkins parameters: The pipeline allows selecting the environment (dev or prod) and specifying a Slack channel for notifications.
Version pinning: We're using a specific Terraform version to ensure consistency.
Security measures:

Using Jenkins credentials for AWS access
Timeout to prevent indefinitely running jobs
Disabling concurrent builds to prevent conflicts


Validation steps: We've added a Terraform validate stage to catch configuration errors early.
Plan analysis: The pipeline analyzes the Terraform plan to determine if there are any changes.
Approval process: For production environments, the pipeline requires manual approval if changes are detected.
Notifications: We're using Slack for notifications, with an option to easily add email notifications.
Output management: The pipeline captures and archives the Terraform outputs, making them available for future steps or external processes.
Cleanup: The workspace is cleaned after each run to prevent issues with subsequent builds.

To set up this pipeline:

Ensure all necessary software is installed on the Jenkins server.
Install and configure the required Jenkins plugins.
Set up the necessary credentials in Jenkins.
Create the repository structure and add the Terraform code.
Create the Jenkinsfile in your repository.
Create a new Jenkins pipeline job, pointing it to your repository and Jenkinsfile.

This pipeline provides a robust, production-ready solution for managing infrastructure as code. It includes proper separation of environments,
modular structure, security considerations, approval processes, and comprehensive notifications. The pipeline is designed to be easily extensible
for more complex infrastructure requirements and can be adapted to different notification systems or additional stages as needed.
```
