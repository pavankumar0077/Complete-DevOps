# AWS ELASTIC KUBERNETES SERVICE

3 MASTER NODES  3 WORKER NODES (RECOMMENDED) 
- 3 IN CONTROL PLANE
- 3 IN DATA PLANE

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/474262cb-2048-4832-98f1-7027cb49f626)

Tools for K8's
--
kubeadm , Kops 

KOPS CAN CREATE KUBERNETES CLUSTER - BUT IF GET ANY ISSUES LIKE Master node went down, some other services 

EKS
--

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/4f4be2f9-d66a-4134-9209-7fab4e851308)

- EKS is a managed controlled plane
- If we request for a Kubernetes Cluster in AWS, EKS will give Highly available Kubernetes cluster with respect to the control plane componets
- That is everything about control plane is taken care by AWS Platform
- AWS gives us a best and highly available cluster only. We don't see any down's
- It also allow us to attach WORKER NODES (Data plane) with the Control plane in easy way
- When we request for a Kubernetes cluster AWS will install entire CONTROL PLAN FOR US and it will create the MASTER NODES (We will not know there is master nodes are)
- We not need to bother about MASTER NODES and When we attach the WORKER NODES, AWS EKS will give us 2 options
- 1. Is we can use EC2 instances (SERVER) (we can create instances by our self)
- 2. We can also go with FORGATE (SERVERLESS) -- aws serverless compute just like LAMBDA functions -- FROGATES are defined to run containers compute
  3. FORGATE will take care for worker nods, EKS will take care for Mater nodes
  4. With these now we can build ROBUST HIGHLY SCABLE KUBERNTES CLUSTER
- if we use SERVER LIKE ec2 instance then we have take cate of it to can high availablity -- configure with auto-scaling -- monitoring and other stuff has to TAKEN CARE BY US

3 Ways of Installing Kubernets
--
- 1. With Virtual Machines and we can set up TOOLS LIKE KUBEADM, KOPS TO INSTALL KUBERNETS CLUSTER, without EKS 
- 2. We CAN USE EKS where AWS will do most of the work and make it highly avaialble
- 3. Install KUBERNTES CLUSTER ON Servers


Creating the EKS cluster and deploying the application
--
- 3 Master nodes, 2 worker nodes
- Application in working node w2
- Service yaml for applcaiton
- Cluster, nodeport and LoadBalancer type of services
- LOAD BALANCER TO ACCESS THE APPLICATION TO THE END-USER (ELASTIC IP)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/a4e00b66-2d9a-4433-8dd0-458f23695966)

Using INGRESS RESOURCE
--

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/85d2c076-6506-41f0-950a-c6ea486b5429)

- Ingress will allow the User to access the applicaiton that is inside the EKS cluster
- Ingress basically ROUTE THE TRAFFIC INSIDE THE CLUSTER
- In ingress resource the devops engineer will write like this ALLOW THE USER --> To access example.com --> example.com -- access the service -- from service to POD
- We will deploy the Ingress resource using KUBECTL
- Now the application is in PRIVATE SUBNET, User can access only PUBLIC SUBNET
- FRom the PUBLIC SUBNET IF WE TAKE THE LOADBALANCER TO ACCESS THE INGRESS RESOURCES THEN USER CAN ACCESS THE APPLCIATON
- iN KUBERNTES we will concept of INGRESS CONTROLLER Typically all the LOAD BALANCERS supports the INGRESS CONTROLLER like NGINX, F5 it will available like Helm charts or downloadables and WE CAN DEPLOY ON KUBERNTES CLUSTER
- As soon as we create the INGRESS CNTROLLER -- IT will watch for the INGRESS RESOURCE and it will create the ALB Controller
- ALB Controller watch for the INGRESS RESOURCES whenever the INGRESS RESOURCE IS CREATED THE ALB CONTROLLER WILL CREATE A ALB envornment or it will create APPLICATION LOAD BALANCER and Using this Application load balancer user can
talk to the applicaiton load balancer from the application load balancer request will go to the APPLICATON.
- This process is same for EVERY INGRESS CONTROLLER


- EX: Let says we are using NGINX INGRESS CONTROLLER --> WE will deploy inside the kuberntes cluster and whenever the Nginx ingress controller will find the ingress resource this nginx ingress controller either create a nginx load balancer
- Or if the load balancer is already there it will configure the load balancer for the RULES MENTIONED IN THE INGRESS RESOURCE. Like (Example.com --> Service --> pods) these rules should be configured in the INGRESS RESOURCE. INgress.yaml have these things (example.com --> service --> pod) there has to be a load balancer to performs this actions that load balancer will be NIGINX LOAD BALANCER OR APPLICATION LB OR F5 LB Depending up on the ingress controller that we are using
- WHAT HAPPENDS IF WE CREATE NGINX INGRESS CONTROLLER, ALB INGRESS CONTROLLER AND EVERYTHING IN THE SAME CLUSTER THEN WE CAN DEFINE IN THE INGRESS ITSELF LIKE WHO SHOULD ACCESS THIS INGRESS RESOURCES WE can Ingress class here we can define who access to access the infress resource.

Finally Flow
--
- Devops engineer along with the POD, SERVICE, CREATE INGRESS FOR EVERY RESOURCES OR EVERY POD THAT NEEDS ACCESS FROM THE EXTERNAL WORLD
- There will be one INGRESS Controller that will watch for all the ingress resources and it will configure the LOAD BALANCER
- USER -- will take to the LOAD BALANCER -- FROM THE LOAD BALANCER WHICH IS IN THE PUBLIC SUBNET REQUEST WILL COME TO THE POD THROUGHT SERVICE TO THE POD


# ---------------------------------
DEMO
# ---------------------------------

FOLLOW THIS LINK : 
https://github.com/pavankumar0077/aws-devops-zero-to-hero/tree/main/day-22

Create EKS cluster
--
- Recommened way to CREATE CLUSTER IS USING EKCTL CLI
- COMMANDS ``` https://github.com/pavankumar0077/aws-devops-zero-to-hero/blob/main/day-22/installing-eks.md ```
- Use this command to create cluster ``` eksctl create cluster --name demo-cluster --region us-east-1 --fargate ``` while creating if there are any errors. Please delete the CFT CloudFormation Stack and re-try
- It create everything that we required like it will create public private subnet for us IN VPC. In the private subnet we place our applications -- All this things are taken care by ekstcl utility
- OPEN ID CONNECTOR PROVIDER : For the cluster what we have created we can provide IDENTITY PROVIDERS LIKE Okta, KeyClock, LDAP
- We can use IAM, But if you want to use any othe identity provider we can INTEGRATE with OpenID connect.
- FROGATE PROFILE -- IS ATTACHED TO default Frogate profile that means we can deploy in default and kube-system namespaces only. If we want add then we can use Add Fargete profile.
- Instead of always going to WEB UI and verifing the resources, Use we can use kubectl command line
- Use this command to get the kubectl ``` aws eks update-kubeconfig --name demo-cluster --region us-east-1 ```
-  ``` aws eks list-clusters ``` to list the clusters
-  Crating FORGATE PROFILE here we are attaching the namespace game-2048
```
eksctl create fargateprofile \
    --cluster demo-cluster \
    --region us-east-1 \
    --name alb-sample-app \
    --namespace game-2048
```
- ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/6a57a30e-2f5a-415f-99b3-48ef2cf39460)
- We can create instance in both namespace of game-2048 namespace. If your using EC2 then no need to do this step
- ``` kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml ``` THIS COMMADND WILL CREATE POD, DEPLOYMENT, SERVICE, INGRESS (Route the traffic inside the cluster)
- Now we have to create INGRESS CONTROLLER TO ACCESS THE APPLICATION. THAT ACCESS ``` ingress-2048 ``` And it will CREATE LOAD BALANCER AND CONFIGURE --> Inside the ALB target group and ports and etc it will create for us.
- Before ingress controller we need to configure IAM OICD
- Now Go to ALB CONNECTOR ``` https://github.com/pavankumar0077/aws-devops-zero-to-hero/blob/main/day-22/alb-controller-add-on.md ```
- ALB CONTROLLER --> pOD --> ACCESS TO AWS SERVICES SUCH AS ALB
- Create IAM POLICY ``` curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json ```
- Create I AM POLICY ``` aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json
  ```
  - ATTCH ROLE TO SERVICE ACCOUNT OF THE POD
  ```
  eksctl create iamserviceaccount \
  --cluster=demo-cluster \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::794982227033:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
  ```h
  - NOW WE WILL USE THE SAME SERIVICE ACCOUNT AND ACCESS THE APPLICATION
  - CREATING ALB CONTROLLER -- WE ARE USING HELPCHART ``` helm repo add eks https://aws.github.io/eks-charts ```
  ```
  helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system \
  --set clusterName=demo-cluster \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=us-east-1 \
  --set vpcId=vpc-099982b8d4a5a1094 
  ```
- ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/aac06c01-4ca8-40d6-ba4c-6a91b21b668f)
- ``` kubectl get deployment -n kube-system aws-load-balancer-controller ``` check for controller is running or not
- ``` kubectl get pods -n kube-system ``` -- This will create it kube-system namespace
- ``` kubectl edit deployment -n kube-system aws-load-balancer-controller ``` if we get any error then we have to lookk here in the status or descibre option to find the error.
- ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/65d5a12b-378e-45e5-8d8e-6bcfe1c66d22)
- ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/650a1a3b-6d25-4429-8514-835dde512109)
- This Load Balancer Controller had created --> How --> We submitted a ingress resources
- ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/58ba1297-a9c7-4c61-857d-2a4034e2cf16)
- ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/6651705d-c6a3-4a13-af74-acd3ca5ff0de)
- FINALLY APPLICATION WILL BE ACCESSED
- ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/a2a4cf75-7112-45a2-9e04-79bc4da70150)

