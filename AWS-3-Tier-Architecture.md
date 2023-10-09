# AWS 3-Tier Architecture

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/06992179-0936-4c06-9c42-90a98831d644)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/d43a3517-9fdf-4c08-98f6-1ab84f2a5768)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/af6e9568-1669-429d-8579-9d04fb06707a)


REF LINK : https://www.showwcase.com/show/35459/building-a-resilient-three-tier-architecture-on-aws-with-deploying-mern-stack-application

1) First we need to create VPC, Be'coz in organization there can be other projects as well
2) Ex: In one AWS account there can be mulitple projects, To keep your project secure from other projects
3) Better to keep a defined VPC with CIDR block range like 172.16.0.0/16
4) Now based on the application as we have 3 tier application, We have to create 3 subnets -- 3 private subnets
5) private subnets for frontend, backend and db
6) we use AWS managed DB RDS using it we can create mysql
7) In private subnet front-end we will use an auto scaling group and by using auto scaling group we will create APPLICATION in ec2 instances
8) The reasons for creating auto-scaling groups can create our application in different ec2 instances it can SCALE up SCALE Down and put these instances in differenet avaialblity zones as well.
9) This AutoScaling grups deploy our application in 2 differennt instances (we mentioned 2 in ASG) like one is us-east-1a and us-east-1b
10) similarily we have ASG for backend as well
11) We will create a Primary RDS and Secondary RDS, the reason is if something happens to this DB all our information should not be lost(secondary for backup)

User
--
1) When USER tries to send a request users requests would ideally go to ROUTE 53 FROM the ROUTE53 if we are using any CDN the request will do to CDN from there it will go to LOAD BALANCER/ eLASTIC LOAD BALANCER.
2) In generally there will be APPLICATION LOAD BALANCER or we can consider any ELASTIC LOAD BALANCER is available in any public PUBLIC SUBNET
3) From the ELB the request would go ONE OF THESE EC2 instances
4) From the EC2 instances it will go to APPLICAITON LOAD BALANCER the request will go to EC2 instances
5) From there the request will go to PRIMARY RDS FROM THERE response will come to BACKEND and from there to FRONT END
6) User will ger the response

Reasons to mention about 3-tier archi and when not to mention
--
- Now many of the organizations are moving to SERVERLESS architecutre or KUBERNETTES architecture
- If job description required containers then explain or use EKS Architecture
- If the JD requires knowledge of applications on EC2 instances then we can explain this 3 tier architecture model

REF LINE 1: https://aws.plainenglish.io/building-your-first-kubernetes-application-with-aws-eks-bc2f1e84118
REF LINK 2 : https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/deploy-a-sample-java-microservice-on-amazon-eks-and-expose-the-microservice-using-an-application-load-balancer.html
