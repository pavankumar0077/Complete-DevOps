## Questions or Doubts

```

1. How control plane, core dns and metric server is getting https - who will install certificates for it lets say for example i have installed on ec2 instance self hosted kubernetes cluster so how this is come automatically
2. When you bootstrap the cluster (e.g., using kubeadm), it automatically generates the required certificates using OpenSSL or cfssl.Got it then what about EKS, AKS or cloud managed kubernets cluster, how these clusters get https
3. If i install the kubernetes cluster using kubeadm in ec2 instance then i will get the SSL certs automatically ?
4. So service discovery is completely on using Kind: Service right is there anything else
5. So then what is the difference there in Kind: Service and Service Mesh: Istio.Both are doing the same job right one is only cluster -- node level and Istio is completely all the clusters and nodes right ?
6. mTLS between services what does it mean in detailed deep dive
7. Lets say for example if i am not using any of the service mesh then the communcation will be TLS ? if yes how TLS will be implementation and who will be implementing this ?
8. Let's say for example if i install minikube then i think the kube proxy will work with which CNI ? Bydefault kubernetes comes with any CNI ? like calico
9. So cloud managed kubernetes like EKS and AKS will be CNI installed like calico or clilium ? by default or we need to install it
10. So one doubt i have let's say for example in one pod can we run multiple applications my name is running on port 80 and other application is running on 3306 for example this case will work ? i can access it using pod id with port 80 and 3306.2nd case lets say for example if we have application 1 and application 2 both are in one pod with same port 80 then what happens and 3rd question let's say for example if pod restarts then pod ip will be changed right ? then what should i do ?
11. I need a deep dive info about surge max: 1 and 10 sec waiting Can you give me notes on this
12. If i delete RS then deployment will create again ?
13. No tell me one thing i want to update the version 1.7 to 1.8 at this time we have RS with 1.7 and 10 sec's for each pod to upgrade the version to 1.8 so at this time does RS will deleted and created again ? with 1.8 versions
14. maxunaviable:0 what does this means ?
15. Can you please help me with why we have use TCP protocal here and why can we use other protocols as well. What is the need of it
16. Yes diagram will help me to understand better
17. One doubt i have if we select kind:service and service as a NodePort, let say for example if we have 2 nodes and my application pod is there in node1 and not in node2 then if i go ahead with node1 ip address with port 80 i am able to access my application and if i go with node2 ip addresss with port 80 i am also able to access my application may i know how this is working ?
18. eksctl is only for EKS in aws, it is same as Kubectl ?
19. eksctl kubectlSo my doubt is if we are using opensource kubernetes then kubectl only manages both like cluster provisioning and mangaement and resource management inside cluster ?

```

# Kubernetes Deep Dive: Real-World Scenarios and Architecture

## 1. SSL/TLS Certificates in Kubernetes Control Plane

### Self-Hosted Kubernetes (kubeadm on EC2)

**Real-World Scenario**: You're setting up a production Kubernetes cluster on AWS EC2 instances for a fintech company.

When you run `kubeadm init`, here's what happens automatically:

```bash
# kubeadm generates these certificates automatically:
/etc/kubernetes/pki/
├── ca.crt                    # Cluster CA certificate
├── ca.key                    # Cluster CA private key
├── apiserver.crt             # API server certificate
├── apiserver.key             # API server private key
├── apiserver-kubelet-client.crt
├── apiserver-kubelet-client.key
├── front-proxy-ca.crt
├── front-proxy-ca.key
├── front-proxy-client.crt
├── front-proxy-client.key
├── etcd/
│   ├── ca.crt               # etcd CA
│   ├── ca.key
│   ├── server.crt           # etcd server cert
│   └── server.key
└── sa.key                   # Service account signing key
```

**Certificate Generation Process**:
1. kubeadm creates a self-signed CA
2. Uses this CA to sign all component certificates
3. Certificates include SANs (Subject Alternative Names) for cluster IP, node IPs, and DNS names

### Cloud Managed Kubernetes (EKS, AKS, GKE)

**EKS Example**:
```bash
# AWS manages the control plane certificates
# You only see the client certificates in your kubeconfig
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTi... # AWS-managed CA
    server: https://A1B2C3D4E5F6.gr7.us-west-2.eks.amazonaws.com
```

**How it works**:
- AWS Certificate Manager or internal AWS CA signs control plane certificates
- AWS handles certificate rotation automatically
- You get a kubeconfig with client certificates for authentication

## 2. Service Discovery Deep Dive

### Beyond Kind: Service

**Real-World Banking Scenario**: A microservices architecture with payment processing, user authentication, and notification services.

```yaml
# Traditional Service Discovery
apiVersion: v1
kind: Service
metadata:
  name: payment-service
spec:
  selector:
    app: payment
  ports:
  - port: 80
    targetPort: 8080
```

**Service Discovery Methods**:

1. **DNS-based** (Primary method):
```bash
# From any pod, you can access:
payment-service.default.svc.cluster.local
# or simply:
payment-service
```

2. **Environment Variables**:
```bash
# Kubernetes injects these automatically
PAYMENT_SERVICE_SERVICE_HOST=10.96.45.123
PAYMENT_SERVICE_SERVICE_PORT=80
```

3. **Service Mesh Discovery** (Advanced):
```yaml
# Istio Service Entry for external service
apiVersion: networking.istio.io/v1beta1
kind: ServiceEntry
metadata:
  name: external-payment-gateway
spec:
  hosts:
  - payment-gateway.external.com
  ports:
  - number: 443
    name: https
    protocol: HTTPS
```

### Service vs Service Mesh Comparison

| Aspect | Kubernetes Service | Service Mesh (Istio) |
|--------|-------------------|---------------------|
| Scope | Single cluster | Multi-cluster, multi-cloud |
| Traffic Management | Basic load balancing | Advanced routing, retries, circuit breakers |
| Security | Basic TLS termination | mTLS, advanced policies |
| Observability | Basic metrics | Distributed tracing, detailed metrics |

## 3. mTLS (Mutual TLS) Deep Dive

### What is mTLS?

**Traditional TLS**: Only server presents certificate
**mTLS**: Both client and server present certificates

### Real-World E-commerce Scenario

**Without mTLS**:
```
User Service → Payment Service
[Client] -----> [Server with TLS cert]
         HTTPS
```

**With mTLS (Istio)**:
```
User Service → Payment Service
[Client cert] ↔ [Server cert]
    Both authenticate each other
```

### Implementation Example:

```yaml
# Istio PeerAuthentication
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
  name: payment-service-mtls
spec:
  selector:
    matchLabels:
      app: payment
  mtls:
    mode: STRICT  # Requires mTLS for all traffic
```

**Certificate Flow**:
1. Istio's Citadel (now Istiod) acts as CA
2. Each service gets a unique certificate
3. Certificates rotate automatically (default: 24 hours)
4. Service-to-service communication encrypted and authenticated

## 4. TLS Without Service Mesh

### Manual TLS Implementation

**Real-World DevOps Scenario**: Legacy application migration where you can't use service mesh immediately.

```yaml
# Application with TLS termination at pod level
apiVersion: apps/v1
kind: Deployment
metadata:
  name: secure-api
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: api
        image: my-secure-api:v1.0
        ports:
        - containerPort: 8443  # HTTPS port
        volumeMounts:
        - name: tls-certs
          mountPath: /etc/ssl/certs
      volumes:
      - name: tls-certs
        secret:
          secretName: api-tls-secret
```

**Certificate Management Options**:

1. **cert-manager** (Recommended):
```yaml
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: api-tls
spec:
  secretName: api-tls-secret
  issuerRef:
    name: letsencrypt-prod
    kind: ClusterIssuer
  dnsNames:
  - api.mycompany.com
```

2. **Manual certificate injection**:
```bash
# Create TLS secret
kubectl create secret tls api-tls-secret \
  --cert=path/to/tls.crt \
  --key=path/to/tls.key
```

## 5. CNI (Container Network Interface) Deep Dive

### Minikube Default CNI

Minikube uses different CNI plugins based on the driver:

```bash
# Docker driver (default)
minikube start --driver=docker  # Uses docker bridge networking

# With specific CNI
minikube start --cni=calico
minikube start --cni=cilium
```

### Cloud-Managed Kubernetes CNI

**EKS**:
- Default: AWS VPC CNI
- Supports: Calico, Cilium, Weave Net as add-ons

```bash
# Install Calico on EKS
kubectl apply -f https://raw.githubusercontent.com/aws/amazon-vpc-cni-k8s/release-1.12/config/master/calico-operator.yaml
```

**AKS**:
- Default: Azure CNI or Kubenet
- Advanced: Cilium with eBPF

```bash
# Create AKS with Cilium
az aks create --network-plugin azure --network-dataplane cilium
```

## 6. Pod Multi-Container Scenarios

### Case 1: Multiple Applications, Different Ports ✅

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-app-pod
spec:
  containers:
  - name: web-server
    image: nginx
    ports:
    - containerPort: 80
  - name: database
    image: mysql:5.7
    ports:
    - containerPort: 3306
    env:
    - name: MYSQL_ROOT_PASSWORD
      value: "password"
```

**Access Pattern**:
```bash
# Both applications accessible via pod IP
curl http://10.244.1.5:80     # Nginx
mysql -h 10.244.1.5 -P 3306   # MySQL
```

### Case 2: Same Port Conflict ❌

```yaml
# This will cause port conflict
spec:
  containers:
  - name: app1
    image: nginx    # Wants port 80
    ports:
    - containerPort: 80
  - name: app2
    image: httpd    # Also wants port 80
    ports:
    - containerPort: 80
```

**Solution**: Use different ports or separate pods.

### Case 3: Pod IP Changes on Restart ✅

**Problem**: Pod IPs are ephemeral

**Solution**: Use Services

```yaml
apiVersion: v1
kind: Service
metadata:
  name: stable-endpoint
spec:
  selector:
    app: my-app
  ports:
  - port: 80
    targetPort: 80
```

## 7. Rolling Update Deep Dive

### Deployment Strategy Parameters

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  replicas: 10
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1          # Allow 1 extra pod (total 11 during update)
      maxUnavailable: 0    # Keep all original pods running
```

### Real-World Update Scenario

**Initial State**: 10 pods running v1.7

**Update Process to v1.8**:

1. **Step 1**: Create 1 new pod with v1.8 (total: 11 pods)
2. **Step 2**: Wait for new pod to be ready
3. **Step 3**: Terminate 1 old pod with v1.7 (total: 10 pods)
4. **Step 4**: Repeat until all pods are v1.8

### maxUnavailable: 0 Explanation

```yaml
maxUnavailable: 0  # Zero-downtime deployment
# Ensures minimum replica count is maintained
# Critical for production services
```

**Real-World Banking Example**:
- Payment processing service cannot afford downtime
- maxUnavailable: 0 ensures always 10 pods serving traffic
- maxSurge: 1 allows temporary scale-up for smooth transition

## 8. Service Types and NodePort Behavior

### NodePort Traffic Flow Mystery Solved

**Scenario**: 2-node cluster, app pod only on node1

```
Node1 (10.0.1.100): [App Pod] ← Pod actually running here
Node2 (10.0.1.101): [No App Pod] ← But NodePort works here too!
```

**Traffic Flow Explanation**:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
  - port: 80
    targetPort: 8080
    nodePort: 30080
```

**When accessing Node2:30080**:

1. **Request hits Node2:30080**
2. **kube-proxy** on Node2 receives the request
3. **kube-proxy** has service endpoints information
4. **Forwards traffic** to Node1 where pod actually exists
5. **Response** flows back through Node2 to client

### Behind the Scenes: kube-proxy Rules

```bash
# iptables rules created by kube-proxy
-A KUBE-NODEPORTS -p tcp --dport 30080 -j KUBE-SVC-XXXXXXXX
-A KUBE-SVC-XXXXXXXX -j KUBE-SEP-YYYYYYYY  # Forward to actual pod
```

## 9. kubectl vs eksctl Comparison

### Tool Responsibilities

| Tool | Purpose | Scope |
|------|---------|-------|
| **kubectl** | Resource management | Inside cluster |
| **eksctl** | Cluster provisioning | AWS EKS cluster creation |

### Real-World DevOps Workflow

**Infrastructure Setup**:
```bash
# 1. Create EKS cluster (eksctl)
eksctl create cluster --name prod-cluster --region us-west-2

# 2. Deploy applications (kubectl)
kubectl apply -f deployment.yaml
kubectl get pods
kubectl logs my-app-12345
```

### Open Source Kubernetes

**For self-managed clusters**:

```bash
# Cluster bootstrap (kubeadm)
kubeadm init

# Resource management (kubectl)
kubectl apply -f app.yaml
kubectl scale deployment my-app --replicas=5
```

## 10. Protocol Selection in Services

### Why TCP Protocol?

**Real-World Load Balancer Scenario**:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: database-service
spec:
  selector:
    app: postgres
  ports:
  - name: postgres
    port: 5432
    targetPort: 5432
    protocol: TCP  # Database needs reliable connection
```

### Protocol Options

1. **TCP** (Most common):
   - Reliable, connection-oriented
   - Used for databases, APIs, web traffic
   - Supports connection pooling

2. **UDP**:
   - Fast, connectionless
   - Used for DNS, video streaming, gaming

```yaml
# DNS service example
apiVersion: v1
kind: Service
metadata:
  name: dns-service
spec:
  ports:
  - name: dns-tcp
    port: 53
    protocol: TCP
  - name: dns-udp
    port: 53
    protocol: UDP  # DNS queries often use UDP for speed
```

3. **SCTP** (Stream Control Transmission Protocol):
   - Used in telecommunications
   - Multi-streaming, multi-homing

## Architecture Diagrams Summary

### Complete Kubernetes Network Flow

```
[Internet] 
    ↓
[Load Balancer] 
    ↓
[Ingress Controller] 
    ↓
[Service] (ClusterIP/NodePort/LoadBalancer)
    ↓
[kube-proxy] (iptables/IPVS rules)
    ↓
[Pod] (via CNI network)
    ↓
[Container] (application)
```

### Certificate Flow in EKS

```
[AWS Certificate Manager] 
    ↓
[EKS Control Plane] (API Server, etcd, scheduler)
    ↓
[Worker Nodes] (kubelet with client certs)
    ↓
[Pods] (service account tokens)
```

This comprehensive guide covers enterprise-level Kubernetes concepts with real-world scenarios. Each section builds upon practical experience from production deployments.