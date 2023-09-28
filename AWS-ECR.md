# AWS Elastic Container Registry

- Elastic : It is scalable and available in nature -- Increase the capacity 
- Container : A pkg which container the application code and it's dependencies to run the application.
- Registry : Container Registry -- (Similary to DockerHub, Quay.io or GCR) -- Used to store the docker images

Difference b/w ECR & Dockerhub
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/f783c500-8f5e-4b9f-9f30-0bcb0a151ce1)

- Dockerhub -- It is public and free to use, we have options like public repos and private repos
- Dockerhub -- User have to register with their own account
- ECR -- It supports private repos, bydefault it will create private repo
- ECR -- IAM USERS integrate with ECR
- ECR -- Have good integration with other AWS services like EKS

Create ECR
--
1) Search for ECR ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/6e04dfcf-ff71-43a1-9cc6-d885d8419563)
2) Image scan ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/2e859236-600d-4893-84db-87c4d3cbc8c2)

Push images to ECR
--
1) WE NEED AWS CLI installed in the system
2) Link ``` https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html ```
3) Click on Repo and click on view push commands
4) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/9a208e5d-e349-4229-8ac5-bc504bad57a7)
5) ECR login usig AWS CLI - Similar to docker login using CLI ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/7601f709-b1cf-4b76-b2ec-0bbc095bcc2a)
6) **NOTE : DOCKER SHUOLD BE INSTALLED BEFORE USING THIS**
7) and follow the commands to push
8) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/e64fbce8-d47a-4c78-9ceb-fb95d2d104cd)

# ----------------------------------------------------------
------------------------------------------------------------------------------
Problems with docker
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/889aaf9f-e9c6-41ed-99fd-3a2bf0494790)
- Auto Scaling
- Auto Healing

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/a83b5993-07f6-4138-984e-0b5d31464b7d)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/11b7298b-0d09-428a-8b79-7bbc6d2a9543)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/ebf9d565-ce10-4d92-8931-d050a8ca74fc)

ECS only for AWS 
- ECS does not have all the features of KUBERNTES
- Kubernetes have Custom Resource Definiations --  Extend the capability of kubernetes
- Kubernetes -- we have use ingress controller + we have lot of controllers we can add to it 

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/0867470d-dcb1-4b32-83eb-a8f0b2354dd9)

ECS advantages
--
- Server - EC2
- Serverless - Forgate

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/b62214ee-6605-46ff-9127-b7a9ba572317)

EKS
--
- Simple architecture

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/fc792766-544b-49ca-aa0c-83d42de50a85)

Structure of ECS
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/0911c321-0309-4e7b-98b5-b0f44f5f87c7)

1) We have to create a CLUSTER on aws
2) Fargate or EC2
3) Task defination (Like pod)
4) Task is one which is running the Container
5) Once the containers are running we can add the SERVICE
6) this SERVICE will add Load Balancer, Ingreee policies
7) It have IAM

REF LINK 1 : https://github.com/pavankumar0077/aws-devops-zero-to-hero/tree/main/day-20
REF LINK 2 : https://github.com/pavankumar0077/aws-devops-zero-to-hero/tree/main/day-21
REF LINK3 : https://mrdevops.hashnode.dev/compare-amazon-ecs-vs-eks

AWS ECS - Create container
--
Step 1:
1) Search for ECS -->
2) Here we are going with serverless fargate
3) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/ed1de977-ad2f-439c-9158-1e7e4a9d783c)
4) Create docker image and push to ECR

Create ECR and push image
--
```
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 794982227033.dkr.ecr.us-east-1.amazonaws.com
docker build -t 794982227033.dkr.ecr.us-east-1.amazonaws.com/demo-repo:latest .
sudo docker images
docker push 794982227033.dkr.ecr.us-east-1.amazonaws.com/demo-repo:latest
```
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/1eed5f2a-d2cc-414e-9101-9a694e191621)

Create Container ECS
--
Step 2 : Click on Task Defination
1) Task role -- container wants to talk to any other service like cloudwatch or s3 etc
2) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/d807c05f-4dce-42da-8a22-a2c356e96030)
3) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/9fa0d078-230e-4385-a31b-4b4d1beb5059)
4) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/49f60d62-47ac-4ea7-9d23-6b11d3a5faff)
5) Remaining details are same
6) Create it
7) Run the task defination : ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/21dab25a-4ab9-43b0-b7f6-06c4b34b19be)
8) If you need anything to change you can do it ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/f1a75155-5b54-47ea-be10-2c27d49d04e6)
9) Cluter : ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/f8f5fbb1-13f1-4857-a29b-1e854fdba6e8)
10) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/6def1ab9-a396-441a-aaf3-5b7b2cb8251a)
11) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/8639c403-7c48-4ca6-80f0-4302ae48150b)
12) Logs from cloudwatch : ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/80df21fa-4107-44e1-9f0b-3822a33628d0)


3) Task execution role -- Agent wants to talk with other services
4) Task execution -- integrate with cloudwatch 

