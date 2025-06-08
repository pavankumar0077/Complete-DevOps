VPC Flow Logs Network Monitoring Solution - Current Implementation
==================================================================

Overview
--------

This document provides a complete step-by-step implementation guide for setting up AWS VPC Flow Logs with CloudWatch monitoring and S3 analytics. The solution helps detect malicious network activities and provides compliance monitoring capabilities.

Architecture Overview
---------------------
![image](https://github.com/user-attachments/assets/60025d1b-0937-463a-88a7-847e979f168e)


Prerequisites
-------------

*   AWS Account with appropriate permissions
    
*   AWS CLI configured (optional)
    
*   Basic understanding of AWS VPC, EC2, and CloudWatch services
    
*   Valid email address for SNS notifications
    

Implementation Steps
--------------------

### Step 1: Create VPC and Network Infrastructure

#### 1.1 Create VPC

1.  Navigate to **VPC Console** in AWS Management Console
    
2.  Ensure you're in **N.Virginia (us-east-1)** region
    
3.  Click **"Your VPCs"** → **"Create VPC"**
    

**Configuration:**

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   Resources to create: VPC only  Name tag: Lab-VPC-Flow-Logs  IPv4 CIDR Block: 10.0.0.0/24  IPv6 CIDR Block: No IPv6 CIDR Block  Tenancy: Default   `

1.  Click **"Create VPC"**
    

#### 1.2 Create Subnet

1.  Navigate to **"Subnets"** → **"Create subnet"**
    

**Configuration:**

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   VPC ID: Lab-VPC-Flow-Logs  Subnet name: Lab-VPC-Flow-Log-Subnet  Availability Zone: us-east-1a  IPv4 CIDR block: 10.0.0.0/26   `

1.  Click **"Create subnet"**
    

#### 1.3 Create Internet Gateway

1.  Navigate to **"Internet Gateways"** → **"Create internet gateway"**
    

**Configuration:**

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   Name tag: Lab-VPC-Flow-Log-IG   `

1.  Click **"Create internet gateway"**
    
2.  Select the gateway → **Actions** → **"Attach to VPC"**
    
3.  Select **Lab-VPC-Flow-Logs** → **"Attach internet gateway"**
    

#### 1.4 Configure Route Table

1.  Navigate to **"Route Tables"**
    
2.  Select the main route table for **Lab-VPC-Flow-Logs**
    
3.  Click **"Routes"** tab → **"Edit routes"**
    
4.  Click **"Add route"**
    

**Route Configuration:**

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   Destination: 0.0.0.0/0  Target: Lab-VPC-Flow-Log-IG (Internet Gateway)   `

1.  Click **"Save changes"**
    

### Step 2: Configure VPC Flow Logs

#### 2.1 Create CloudWatch Log Group

1.  Navigate to **CloudWatch Console**
    
2.  Click **"Logs"** → **"Create log group"**
    

**Configuration:**

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   Log group name: Lab-VPC-Flow-Log-Group  Retention setting: Never expire   `

1.  Click **"Create"**
    

#### 2.2 Create IAM Role for Flow Logs

1.  Navigate to **IAM Console** → **"Roles"** → **"Create role"**
    
2.  Select **"Custom trust policy"**
    

**Trust Policy:**

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   {    "Version": "2012-10-17",    "Statement": [      {        "Effect": "Allow",        "Principal": {          "Service": "vpc-flow-logs.amazonaws.com"        },        "Action": "sts:AssumeRole"      }    ]  }   `

1.  Click **"Next"** → **"Create policy"**
    

**Policy Document:**

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   {    "Version": "2012-10-17",    "Statement": [      {        "Effect": "Allow",        "Action": [          "logs:CreateLogGroup",          "logs:CreateLogStream",          "logs:PutLogEvents",          "logs:DescribeLogGroups",          "logs:DescribeLogStreams"        ],        "Resource": "*"      }    ]  }   `

1.  Save policy as **"flowlogsPolicy"**
    
2.  Attach policy to role and name it **"flowlogsRole"**
    

#### 2.3 Create S3 Bucket for Flow Logs

1.  Navigate to **S3 Console** → **"Create bucket"**
    

**Configuration:**

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   Bucket name: lab-vpc-flow-log-bucket-[unique-suffix]  AWS Region: US East (N. Virginia) us-east-1   `

1.  Keep default settings and click **"Create bucket"**
    
2.  Copy the bucket ARN from Properties tab
    

#### 2.4 Create VPC Flow Log to CloudWatch

1.  Navigate to **VPC Console** → **"Your VPCs"**
    
2.  Select **Lab-VPC-Flow-Logs** → **"Flow Logs"** tab
    
3.  Click **"Create flow log"**
    

**Configuration:**

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   Name: VPC-Flow-Log-Cloudwatch  Filter: All  Maximum aggregation interval: 1 minute  Destination: Send to CloudWatch Logs  Destination log group: Lab-VPC-Flow-Log-Group  IAM role: flowlogsRole   `

1.  Click **"Create flow log"**
    

#### 2.5 Create VPC Flow Log to S3

1.  Click **"Create flow log"** again
    

**Configuration:**

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   Name: Lab-VPC-Flow-Log-S3  Filter: All  Maximum aggregation interval: 1 minute  Destination: Send to Amazon S3 bucket  S3 bucket ARN: [Your bucket ARN from step 2.3]   `

1.  Click **"Create flow log"**
    

### Step 3: Launch EC2 Instance for Testing

#### 3.1 Create Security Group

1.  Navigate to **EC2 Console** → **"Security Groups"**
    
2.  Click **"Create security group"**
    

**Configuration:**

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   Security group name: Lab-SSH  Description: Lab-SSH  VPC: Lab-VPC-Flow-Logs  Inbound Rules:  Type: SSH  Protocol: TCP  Port Range: 22  Source: 0.0.0.0/0   `

1.  Click **"Create security group"**
    

#### 3.2 Launch EC2 Instance

1.  Navigate to **"Instances"** → **"Launch Instances"**
    

**Configuration:**

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   AMI: Amazon Linux 2 AMI  Instance type: t2.micro  Key pair: No key pair (use EC2 Instance Connect)  Network: Lab-VPC-Flow-Logs  Subnet: Lab-VPC-Flow-Log-Subnet  Auto-assign Public IP: Enable  Security group: Lab-SSH   `

1.  Click **"Launch Instance"**
    

### Step 4: Configure CloudWatch Monitoring and Alerts

#### 4.1 Test Initial Traffic

1.  Connect to EC2 instance using **EC2 Instance Connect**
    
2.  Run command: ping google.com
    
3.  Note the IP address and stop ping with Ctrl+C
    
4.  Check CloudWatch Logs for ACCEPT entries
    

#### 4.2 Create Network ACL Deny Rule

1.  Navigate to **VPC Console** → **"Network ACLs"**
    
2.  Select NACL for **Lab-VPC-Flow-Log-Subnet**
    
3.  Go to **"Outbound rules"** → **"Edit outbound rules"**
    
4.  Click **"Add new rule"**
    

**Rule Configuration:**

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   Rule number: 90  Type: All ICMP - IPv4  Protocol: ICMP  Port range: All  Destination: 0.0.0.0/0  Allow/Deny: Deny   `

1.  Click **"Save changes"**
    

#### 4.3 Create CloudWatch Metric Filter

1.  Navigate to **CloudWatch Console** → **"Log groups"**
    
2.  Select **Lab-VPC-Flow-Log-Group** → **"Metric filters"**
    
3.  Click **"Create metric filter"**
    

**Filter Configuration:**

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   Filter pattern: [version, account, eni, source, destination, srcport, destport, protocol="1", packets, bytes, starttime, endtime, action="REJECT", flowlogstatus]  Test data:  2 609242387670 eni-01a7afa7d0e999a73 10.0.0.49 172.217.9.206 0 0 1 33 2772 1621577928 1621577987 REJECT OK  2 609242387670 eni-01a7afa7d0e999a73 18.206.107.25 10.0.0.49 19653 22 6 70 7047 1621576794 1621576852 ACCEPT OK  2 609242387670 eni-01a7afa7d0e999a73 10.0.0.49 18.206.107.25 22 19653 6 57 9761 1621576794 1621576852 ACCEPT OK  2 609242387670 eni-01a7afa7d0e999a73 10.0.0.49 172.217.12.208 0 0 1 33 2775 1621577938 1621577998 REJECT OK  Filter name: Lab-VPC-Flow-Log-Ping-Deny  Metric namespace: Lab-VPC-Flow-Log-Ping  Metric name: Ping-Deny  Metric value: 1   `

1.  Click **"Create metric filter"**
    

#### 4.4 Create CloudWatch Alarm

1.  Select the metric filter → **"Create alarm"**
    

**Alarm Configuration:**

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   Metric name: Ping-Deny  Statistic: Sum  Period: 1 minute  Threshold type: Static  Condition: Greater/Equal  Threshold value: 1  Alarm state trigger: In alarm  SNS topic: Create new topic  Topic name: CloudWatch_Alarms_Ping_Deny  Email endpoint: [Your email address]  Alarm name: CloudWatch-Alarm-Ping-Deny   `

1.  Click **"Create alarm"**
    
2.  Confirm SNS subscription in your email
    

#### 4.5 Test Alarm Trigger

1.  Connect to EC2 instance
    
2.  Run ping google.com (should fail due to NACL rule)
    
3.  Wait for CloudWatch alarm to trigger
    
4.  Check email for alarm notification
    

### Step 5: Set Up Athena Analytics

#### 5.1 Prepare S3 Bucket

1.  Navigate to **S3 Console**
    
2.  Go to your flow logs bucket
    
3.  Create folder: **"AthenaQueryResults"**
    

#### 5.2 Configure Athena

1.  Navigate to **Athena Console**
    
2.  Set query result location to: s3://your-bucket-name/AthenaQueryResults/
    
3.  Create database:
    

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   CREATE DATABASE lab;   `

#### 5.3 Create Athena Table

**Replace the S3 location and account ID with your values:**

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML``   CREATE EXTERNAL TABLE IF NOT EXISTS lab.vpc_flow_log (    version int,    account string,    eni string,    source string,    destination string,    srcport int,    destport int,    protocol int,    packets int,    bytes bigint,    starttime int,    endtime int,    action string,    flowlogstatus string  )  PARTITIONED BY (    `year` string,    `month` string,    `day` string)  ROW FORMAT DELIMITED  FIELDS TERMINATED BY ' '  LOCATION 's3://your-bucket-name/AWSLogs/YOUR-ACCOUNT-ID/vpcflowlogs/us-east-1/'  TBLPROPERTIES ("skip.header.line.count"="1");   ``

#### 5.4 Add Partitions

**Update the date and S3 path:**

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   ALTER TABLE lab.vpc_flow_log  ADD PARTITION (year='2024', month='12', day='07')  location 's3://your-bucket-name/AWSLogs/YOUR-ACCOUNT-ID/vpcflowlogs/us-east-1/2024/12/07';   `

#### 5.5 Load Partitions

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   MSCK REPAIR TABLE lab.vpc_flow_log;   `

#### 5.6 Analyze Traffic Patterns

**Query ICMP Traffic (replace IP with your EC2 instance private IP):**

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   SELECT CONCAT(year, '-', month, '-', day) date,          eni, source, destination, starttime, endtime, action, protocol  FROM lab.vpc_flow_log  WHERE source = '10.0.0.49' AND protocol = 1  ORDER BY starttime;   `

**Query Rejected Traffic:**

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   SELECT CONCAT(year, '-', month, '-', day) date,          eni, source, destination, starttime, endtime, action, protocol  FROM lab.vpc_flow_log  WHERE action = 'REJECT'  ORDER BY starttime;   `

Testing and Validation
----------------------

### Verify Flow Logs Collection

1.  Check CloudWatch Logs for flow log entries
    
2.  Verify S3 bucket contains flow log files
    
3.  Confirm Athena can query the data
    

### Test Security Monitoring

1.  Generate rejected traffic (ping after NACL rule)
    
2.  Verify CloudWatch alarm triggers
    
3.  Confirm SNS email notification received
    

### Validate Analytics

1.  Run Athena queries successfully
    
2.  Analyze traffic patterns
    
3.  Identify ACCEPT vs REJECT traffic
    

Troubleshooting
---------------

### Common Issues

**Flow Logs Not Appearing:**

*   Check IAM role permissions
    
*   Verify VPC Flow Log configuration
    
*   Ensure sufficient time has passed (can take 5-15 minutes)
    

**CloudWatch Alarm Not Triggering:**

*   Verify metric filter pattern
    
*   Check log group has data
    
*   Confirm alarm threshold settings
    

**Athena Query Errors:**

*   Verify S3 bucket permissions
    
*   Check table location path
    
*   Ensure partitions are correctly added
    

**SNS Notifications Not Received:**

*   Confirm email subscription
    
*   Check spam folder
    
*   Verify SNS topic permissions
    

Cost Considerations
-------------------

### Estimated Monthly Costs:

*   **VPC Flow Logs**: $50-100 (depending on traffic volume)
    
*   **CloudWatch Logs**: $30-60 (for ingestion and storage)
    
*   **S3 Storage**: $10-20 (for flow log files)
    
*   **Athena Queries**: $5-15 (per TB scanned)
    
*   **SNS Notifications**: $1-5 (for email notifications)
    

**Total Estimated Cost: $95-200/month**

Security Notes
--------------

⚠️ **Important Security Considerations:**

1.  **SSH Access**: Current setup allows SSH from anywhere (0.0.0.0/0) - restrict to your IP range in production
    
2.  **Data Encryption**: Flow logs are stored unencrypted - consider enabling S3 encryption
    
3.  **IAM Permissions**: Review and tighten IAM roles following least privilege principle
    
4.  **Network ACLs**: The ICMP deny rule is for testing - review production network access rules
    

Cleanup Instructions
--------------------

To avoid ongoing charges:

1.  **Delete EC2 Instance**
    
2.  **Delete VPC Flow Logs**
    
3.  **Delete CloudWatch Log Group**
    
4.  **Delete S3 Bucket and contents**
    
5.  **Delete SNS Topic**
    
6.  **Delete CloudWatch Alarms**
    
7.  **Delete IAM Role and Policy**
    
8.  **Delete VPC and associated resources**
    

Next Steps
----------

This implementation provides a foundation for network monitoring. Consider the following enhancements:

*   Implement real-time processing with Kinesis
    
*   Add automated threat response with Lambda
    
*   Integrate with AWS GuardDuty for advanced threat detection
    
*   Create custom dashboards with QuickSight
    
*   Implement cost optimization strategies
