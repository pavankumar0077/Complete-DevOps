# Advanced Kubernetes Scenarios & Best Practices

## Real-World Production Scenarios

### Scenario 1: E-commerce Platform Migration

**Context**: Migrating a monolithic e-commerce application to Kubernetes microservices

**Architecture**:
```
┌─────────────────────────────────────────────────┐
│                 AWS EKS Cluster                 │
├─────────────────────────────────────────────────┤
│                                                 │
│  ┌─────────────────┐    ┌─────────────────┐    │
│  │   Frontend      │    │   API Gateway   │    │
│  │   (React/Next)  │    │   (Kong/Nginx)  │    │
│  │   Port: 3000    │    │   Port: 8080    │    │
│  └─────────────────┘    └─────────────────┘    │
│           │                       │            │
│           └───────────────────────┘            │
│                       │                        │
│  ┌─────────────────┐  │  ┌─────────────────┐  │
│  │  User Service   │  │  │ Payment Service │  │
│  │  Port: 8001     │  │  │ Port: 8002      │  │
│  └─────────────────┘  │  └─────────────────┘  │
│           │            │           │           │
│  ┌─────────────────┐  │  ┌─────────────────┐  │
│  │ Inventory Svc   │  │  │ Notification    │  │
│  │ Port: 8003      │  │  │ Service: 8004   │  │
│  └─────────────────┘  │  └─────────────────┘  │
│                       │                        │
│  ┌─────────────────────────────────────────┐  │
│  │          Shared Database Layer          │  │
│  │  ┌─────────────┐  ┌─────────────────┐  │  │
│  │  │   Redis     │  │   PostgreSQL    │  │  │
│  │  │   Cache     │  │   Database      │  │  │
│  │  └─────────────┘  └─────────────────┘  │  │
│  └─────────────────────────────────────────┘  │
└─────────────────────────────────────────────────┘
```

### Scenario 2: Financial Services with Strict Security

**Requirements**:
- mTLS between all services
- Network policies for microsegmentation
- Certificate rotation every 12 hours
- Compliance auditing

**Implementation**:

```yaml
# Istio Security Configuration
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
  name: strict-mtls
  namespace: finance
spec:
  mtls:
    mode: STRICT

---
apiVersion: security.istio.io/v1beta1
kind: AuthorizationPolicy
metadata:
  name: payment-policy
  namespace: finance
spec:
  selector:
    matchLabels:
      app: payment-service
  rules:
  - from:
    - source:
        principals: ["cluster.local/ns/finance/sa/user-service"]
  - to:
    - operation:
        methods: ["POST", "GET"]
        paths: ["/api/payment/*"]
```

## Performance Tuning & Optimization

### Resource Management Best Practices

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: high-performance-api
spec:
  replicas: 5
  template:
    spec:
      containers:
      - name: api
        image: my-api:v2.0
        resources:
          requests:
            memory: "256Mi"    # Guaranteed memory
            cpu: "250m"        # 25% of CPU core
          limits:
            memory: "512Mi"    # Max memory before OOM kill
            cpu: "500m"        # Max CPU before throttling
        
        # Probes for reliability
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
          timeoutSeconds: 5
          failureThreshold: 3
        
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 5
          timeoutSeconds: 3
          failureThreshold: 2
        
        # Graceful shutdown
        lifecycle:
          preStop:
            exec:
              command: ["/bin/sh", "-c", "sleep 15"]
        
        terminationGracePeriodSeconds: 30
```

### HPA (Horizontal Pod Autoscaler) Configuration

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: api-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: high-performance-api
  minReplicas: 3
  maxReplicas: 50
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
  - type: Pods
    pods:
      metric:
        name: http_requests_per_second
      target:
        type: AverageValue
        averageValue: "100"
  
  behavior:
    scaleUp:
      stabilizationWindowSeconds: 300
      policies:
      - type: Percent
        value: 100
        periodSeconds: 15
    scaleDown:
      stabilizationWindowSeconds: 300
      policies:
      - type: Percent
        value: 10
        periodSeconds: 60
```

## Network Security Deep Dive

### Network Policies for Microsegmentation

```yaml
# Deny all traffic by default
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-all
  namespace: production
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress

---
# Allow specific service-to-service communication
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: user-service-policy
  namespace: production
spec:
  podSelector:
    matchLabels:
      app: user-service
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: frontend
    - podSelector:
        matchLabels:
          app: api-gateway
    ports:
    - protocol: TCP
      port: 8080
  egress:
  - to:
    - podSelector:
        matchLabels:
          app: database
    ports:
    - protocol: TCP
      port: 5432
  - to: []  # Allow DNS resolution
    ports:
    - protocol: UDP
      port: 53
```

## Certificate Management in Production

### cert-manager with Let's Encrypt

```yaml
# ClusterIssuer for Let's Encrypt
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: devops@company.com
    privateKeySecretRef:
      name: letsencrypt-prod
    solvers:
    - http01:
        ingress:
          class: nginx
    - dns01:
        route53:
          region: us-west-2
          accessKeyID: AKIA...
          secretAccessKeySecretRef:
            name: route53-credentials
            key: secret-access-key

---
# Certificate for application
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: api-tls-cert
  namespace: production
spec:
  secretName: api-tls-secret
  issuerRef:
    name: letsencrypt-prod
    kind: ClusterIssuer
  dnsNames:
  - api.mycompany.com
  - api-staging.mycompany.com
```

### Internal PKI with cert-manager

```yaml
# Self-signed issuer for internal CA
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: selfsigned-issuer
spec:
  selfSigned: {}

---
# Root CA Certificate
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: my-selfsigned-ca
  namespace: cert-manager
spec:
  isCA: true
  commonName: my-selfsigned-ca
  secretName: root-secret
  privateKey:
    algorithm: ECDSA
    size: 256
  issuerRef:
    name: selfsigned-issuer
    kind: ClusterIssuer

---
# CA Issuer using the root certificate
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: my-ca-issuer
spec:
  ca:
    secretName: root-secret
```

## Advanced Deployment Strategies

### Blue-Green Deployment

```yaml
# Blue deployment (current production)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-blue
  labels:
    version: blue
spec:
  replicas: 10
  selector:
    matchLabels:
      app: myapp
      version: blue
  template:
    metadata:
      labels:
        app: myapp
        version: blue
    spec:
      containers:
      - name: app
        image: myapp:v1.0
        ports:
        - containerPort: 8080

---
# Green deployment (new version)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-green
  labels:
    version: green
spec:
  replicas: 10
  selector:
    matchLabels:
      app: myapp
      version: green
  template:
    metadata:
      labels:
        app: myapp
        version: green
    spec:
      containers:
      - name: app
        image: myapp:v2.0
        ports:
        - containerPort: 8080

---
# Service that can switch between blue and green
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
    version: blue  # Switch to 'green' for deployment
  ports:
  - port: 80
    targetPort: 8080
```

### Canary Deployment with Istio

```yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: myapp-vs
spec:
  http:
  - match:
    - headers:
        canary:
          exact: "true"
    route:
    - destination:
        host: myapp-service
        subset: v2
  - route:
    - destination:
        host: myapp-service
        subset: v1
      weight: 90
    - destination:
        host: myapp-service
        subset: v2
      weight: 10  # 10% traffic to new version

---
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: myapp-dr
spec:
  host: myapp-service
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
```

## Monitoring and Observability

### Comprehensive Monitoring Stack

```yaml
# Prometheus ServiceMonitor
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: myapp-metrics
spec:
  selector:
    matchLabels:
      app: myapp
  endpoints:
  - port: metrics
    interval: 30s
    path: /metrics

---
# Grafana Dashboard ConfigMap
apiVersion: v1
kind: ConfigMap
metadata:
  name: myapp-dashboard
  labels:
    grafana_dashboard: "1"
data:
  myapp.json: |
    {
      "dashboard": {
        "title": "MyApp Metrics",
        "panels": [
          {
            "title": "Request Rate",
            "type": "graph",
            "targets": [
              {
                "expr": "rate(http_requests_total[5m])"
              }
            ]
          }
        ]
      }
    }
```

## Disaster Recovery and Backup

### Velero Backup Configuration

```yaml
# Backup all namespaces daily
apiVersion: velero.io/v1
kind: Schedule
metadata:
  name: daily-backup
spec:
  schedule: "0 2 * * *"  # Daily at 2 AM
  template:
    includedNamespaces:
    - production
    - staging
    storageLocation: aws-s3
    volumeSnapshotLocations:
    - aws-ebs
    ttl: 720h0m0s  # Retain for 30 days

---
# Application-specific backup
apiVersion: velero.io/v1
kind: Backup
metadata:
  name: myapp-backup
spec:
  includedNamespaces:
  - production
  labelSelector:
    matchLabels:
      app: myapp
  snapshotVolumes: true
  storageLocation: aws-s3
```

## Cost Optimization Strategies

### Resource Optimization

```yaml
# Vertical Pod Autoscaler
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: myapp-vpa
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  updatePolicy:
    updateMode: "Auto"
  resourcePolicy:
    containerPolicies:
    - containerName: app
      maxAllowed:
        cpu: "2"
        memory: "4Gi"
      minAllowed:
        cpu: "100m"
        memory: "128Mi"

---
# Pod Disruption Budget for cost optimization
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: myapp-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: myapp
```

### Node Affinity for Cost Optimization

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: batch-job
spec:
  replicas: 5
  template:
    spec:
      affinity:
        nodeAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 100
            preference:
              matchExpressions:
              - key: node.kubernetes.io/instance-type
                operator: In
                values:
                - t3.large    # Prefer cheaper instances
                - t3.medium
          - weight: 50
            preference:
              matchExpressions:
              - key: karpenter.sh/capacity-type
                operator: In
                values:
                - spot        # Prefer spot instances
      tolerations:
      - key: node.kubernetes.io/spot
        operator: Exists
        effect: NoSchedule
      containers:
      - name: batch-worker
        image: batch-worker:v1.0
        resources:
          requests:
            memory: "512Mi"
            cpu: "200m"
          limits:
            memory: "1Gi"
            cpu: "400m"
```

## Security Best Practices

### Pod Security Standards

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: secure-namespace
  labels:
    pod-security.kubernetes.io/enforce: restricted
    pod-security.kubernetes.io/audit: restricted
    pod-security.kubernetes.io/warn: restricted

---
# Secure pod configuration
apiVersion: apps/v1
kind: Deployment
metadata:
  name: secure-app
  namespace: secure-namespace
spec:
  template:
    spec:
      serviceAccountName: secure-app-sa
      securityContext:
        runAsNonRoot: true
        runAsUser: 1001
        runAsGroup: 3000
        fsGroup: 2000
        seccompProfile:
          type: RuntimeDefault
      containers:
      - name: app
        image: myapp:secure
        securityContext:
          allowPrivilegeEscalation: false
          readOnlyRootFilesystem: true
          capabilities:
            drop:
            - ALL
        volumeMounts:
        - name: tmp
          mountPath: /tmp
        - name: var-run
          mountPath: /var/run
      volumes:
      - name: tmp
        emptyDir: {}
      - name: var-run
        emptyDir: {}
```

### RBAC Configuration

```yaml
# Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: secure-app-sa
  namespace: secure-namespace

---
# Role with minimal permissions
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: secure-namespace
  name: secure-app-role
rules:
- apiGroups: [""]
  resources: ["configmaps", "secrets"]
  verbs: ["get", "list"]
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]

---
# RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: secure-app-binding
  namespace: secure-namespace
subjects:
- kind: ServiceAccount
  name: secure-app-sa
  namespace: secure-namespace
roleRef:
  kind: Role
  name: secure-app-role
  apiGroup: rbac.authorization.k8s.io
```

## Troubleshooting Guide

### Common Issues and Solutions

#### Issue 1: Pods Stuck in Pending State

**Diagnosis Commands**:
```bash
kubectl describe pod <pod-name>
kubectl get events --sort-by=.metadata.creationTimestamp
kubectl top nodes
```

**Common Causes**:
1. **Insufficient Resources**: Node doesn't have enough CPU/memory
2. **Node Affinity**: Pod can't be scheduled on available nodes
3. **Taints and Tolerations**: Pod doesn't tolerate node taints
4. **PVC Issues**: Persistent Volume Claims not bound

#### Issue 2: Service Not Accessible

**Diagnosis Commands**:
```bash
kubectl get svc <service-name>
kubectl get endpoints <service-name>
kubectl describe svc <service-name>
```

**Common Causes**:
1. **Selector Mismatch**: Service selector doesn't match pod labels
2. **Port Configuration**: targetPort doesn't match container port
3. **Network Policies**: Traffic blocked by network policies

#### Issue 3: High Memory Usage / OOM Kills

**Diagnosis Commands**:
```bash
kubectl top pods --sort-by=memory
kubectl describe pod <pod-name> | grep -A 5 "Last State"
```

**Solutions**:
1. **Increase Memory Limits**: Based on actual usage patterns
2. **Memory Profiling**: Use profiling tools to identify memory leaks
3. **Horizontal Scaling**: Scale out instead of scaling up

## Production Checklist

### Pre-Deployment Checklist

- [ ] **Security**:
  - [ ] Pod Security Standards configured
  - [ ] RBAC policies in place
  - [ ] Network policies defined
  - [ ] Image vulnerability scanning passed
  - [ ] Secrets properly managed

- [ ] **Reliability**:
  - [ ] Health checks configured
  - [ ] Resource requests/limits set
  - [ ] Pod Disruption Budgets defined
  - [ ] Multi-zone deployment
  - [ ] Backup strategy implemented

- [ ] **Performance**:
  - [ ] HPA configured
  - [ ] Resource optimization done
  - [ ] Load testing completed
  - [ ] Monitoring and alerting setup

- [ ] **Observability**:
  - [ ] Metrics collection enabled
  - [ ] Logging configured
  - [ ] Distributed tracing setup
  - [ ] Dashboards created

### Post-Deployment Monitoring

```yaml
# SLO-based alerting
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: slo-alerts
spec:
  groups:
  - name: slo.rules
    rules:
    - alert: HighErrorRate
      expr: |
        (
          rate(http_requests_total{status=~"5.."}[5m]) /
          rate(http_requests_total[5m])
        ) > 0.01
      for: 5m
      labels:
        severity: warning
      annotations:
        summary: "High error rate detected"
        description: "Error rate is {{ $value | humanizePercentage }}"

    - alert: HighLatency
      expr: |
        histogram_quantile(0.95,
          rate(http_request_duration_seconds_bucket[5m])
        ) > 0.5
      for: 10m
      labels:
        severity: critical
      annotations:
        summary: "High latency detected"
        description: "95th percentile latency is {{ $value }}s"
```

