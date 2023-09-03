# AWS SERVICES FOR DEVOPS

1) EC2
2) VPC (Private (secure)) -- security group, inboud - out roles
3) EBS ( Volumes) -- attach a volume to EC2
4) EFS
5) S3 (Storage) -- Encrypted bydefault
6) IAM (1 & AM )(IMP)
7) Cloud Watch (Monitoring) - have log for everything in the AWS
8) Lambda (sending mail notification, Severless computing)
9) Cloud build services (AWS Code Pipeline) (AWS CodeBuild) (AWS CodeDeploy) --
9.1) Code Pipeline -- is like jenkins pipeline
9.2) CodeBuild Service -- Compile the code, run some test, provides pkg and etc
9.3) CodeDeploy -- Deploying our application onto EC2 instances or on-prem
10) AWS Configuration Service -- Configuration that we have created
11) Billing & Coasting
12) AWS KMS (key management service (Secrets, certificates to managed. Sensitive information stored here)
13) CloudTrail (Enable operation and risk aduiting) -- Stores the info of API logs (30 days ago) api activites and perserves logs for 30 days like that.
14) AWS EKS (ELastic Kubernetes Service) (imp)
15) Forgate ( Without kubernetes) ECS (Container orchestration solution)
16) ELK (Elasticsearch, Logstash, and Kibana)
17) Route 53

Difference between Lambda and EC2
--
```
1) AWS lambda functions they can be created and run automatically ( Used for short actions)
2) eX: When dev created unencrypted EBS volume you can run a lambda function which will encrypted the
EBS Volume and function get executed and stoppted automatically.
3) EC2 have server inbuild
```

Difference between EKS vs ECS
--
```
1) Both of them are container orchestration, But ECS is a AWS proprietary solution AWS has the build the solution
by their own.   
2) EKS is the managed kubernets service. Providing kubernets as a managed service
```
