🔥 Real-World Scenario: Multi-Service Enterprise Application on EKS with ALB Ingress

Imagine you are working for Wayne Enterprises 🦇.
They host multiple microservices inside EKS (Elastic Kubernetes Service):

App1 → /game (a public game portal like 2048)

App2 → /track-joker (tracking system for Gotham security)

App3 → /monitor-batcave (internal monitoring dashboards)

App4 → /order-batsuit (e-commerce-like ordering portal)

If we expose each service with its own LoadBalancer service type, AWS will create 4 separate ELBs (costly 💸, inefficient, and hard to manage).
Instead, we use ALB Ingress Controller to manage everything under one single Application Load Balancer with path-based routing.

🏗️ Architecture Flow with Ingress

User hits DNS (e.g. batcave.com)
→ ALB receives request.

ALB Path Routing Rules

/game → Service game-svc → Pods running game

/track-joker → Service tracker-svc → Pods running tracker

/monitor-batcave → Service monitor-svc → Pods running monitoring tools

/order-batsuit → Service batsuit-svc → Pods running e-commerce portal

ALB Ingress Controller Pod

Watches Kubernetes API for Ingress resources

Configures AWS ALB dynamically with routes

IAM Role for ServiceAccount (IRSA)

Allows ingress-controller pod to create/manage AWS ALB resources

``` mermaid
flowchart TB
    subgraph AWS["AWS Cloud"]
        ALB["Application Load Balancer"]
    end

    subgraph EKS["EKS Cluster"]
        subgraph IngressController["ALB Ingress Controller (Pod)"]
        end

        subgraph Game["Game App"]
            SVC1["Service: game-svc (NodePort/ClusterIP)"]
            P1["Pod: Game-1"]
            P2["Pod: Game-2"]
        end

        subgraph Tracker["Tracker App"]
            SVC2["Service: tracker-svc"]
            T1["Pod: Tracker-1"]
            T2["Pod: Tracker-2"]
        end

        subgraph Monitor["Monitoring App"]
            SVC3["Service: monitor-svc"]
            M1["Pod: Monitor-1"]
            M2["Pod: Monitor-2"]
        end

        subgraph Batsuit["Batsuit App"]
            SVC4["Service: batsuit-svc"]
            B1["Pod: Batsuit-1"]
            B2["Pod: Batsuit-2"]
        end
    end

    User["User Browser"]

    User -->|"https://batcave.com/game"| ALB
    User -->|"https://batcave.com/track-joker"| ALB
    User -->|"https://batcave.com/monitor-batcave"| ALB
    User -->|"https://batcave.com/order-batsuit"| ALB

    ALB -->|"Path: /game"| SVC1
    ALB -->|"Path: /track-joker"| SVC2
    ALB -->|"Path: /monitor-batcave"| SVC3
    ALB -->|"Path: /order-batsuit"| SVC4

    SVC1 --> P1 & P2
    SVC2 --> T1 & T2
    SVC3 --> M1 & M2
    SVC4 --> B1 & B2
```
``` mermaid

flowchart TB
    User["Users / Clients"] -->|HTTPS:443| ALB["AWS Application Load Balancer (Single)"]

    subgraph EKS["Amazon EKS Cluster"]
      Controller["AWS Load Balancer Controller (IRSA)"]
      subgraph NS1["ns: gotham-apps"]
        S1["Service game-svc (ClusterIP/NodePort)"]
        S2["Service tracker-svc"]
        S3["Service monitor-svc"]
        S4["Service batsuit-svc"]

        P1["Pods: game xN"]
        P2["Pods: tracker xN"]
        P3["Pods: monitor xN"]
        P4["Pods: batsuit xN"]
      end
    end

    ALB -->|"Path /game"| S1
    ALB -->|"Path /track-joker"| S2
    ALB -->|"Path /monitor-batcave"| S3
    ALB -->|"Path /order-batsuit"| S4

    S1 --> P1
    S2 --> P2
    S3 --> P3
    S4 --> P4

    Controller <-->|Watches Ingress| EKS
    Controller -->|Creates/Manages| ALB
```

``` mermaid
flowchart LR
    ALB1["ALB (target-type: instance)"] -->|"NodePort"| Node["Worker Node"]
    Node --> Pods1["Pods"]

    ALB2["ALB (target-type: ip)"] -->|"Target: Pod IPs"| Pods2["Pods (VPC-routable)"]
```
### instance: simpler networking; traffic: ALB → node:nodePort → pod

### ip: fewer hops; ALB targets pod IPs (requires CNI support & SG/POD rules)

## COMPLETE EXAMPLE
```
https://chatgpt.com/share/68ada36c-cca4-800c-88a2-3cfea4daf95e

```
