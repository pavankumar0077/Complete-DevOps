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
- EKS is a managed controlled plane
- If we request for a Kubernetes Cluster in AWS, EKS will give Highly available Kubernetes cluster with respect to the control plane componets
- That is everything about control plane is taken care by AWS Platform
- AWS gives us a best and highly available cluster only. We don't see any down's
- It also allow us to attach WORKER NODES (Data plane) with the Control plane in easy way
- When we request for a Kubernetes cluster AWS will install entire CONTROL PLAN FOR US and it will create the MASTER NODES (We will not know there is master nodes are)
- We not need to bother about MASTER NODES and When we attach the WORKER NODES, AWS EKS will give us 2 options
- 1. Is we can use EC2 instances (SERVER) (we can create instances by our self)
- 2. We can also go with FORGATE (SERVERLESS)
