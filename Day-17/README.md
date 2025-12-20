# Three-Tier Microservices Application on AKS (Azure Kubernetes Service)

This repository demonstrates a **real-world Three-Tier Architecture** implemented using **microservices**, containerized with **Docker**, orchestrated using **Kubernetes**, packaged with **Helm**, and deployed on **Azure Kubernetes Service (AKS)**.

The implementation is based on the GitHub repository:
https://github.com/iam-veeramalla/three-tier-architecture-demo

---

## 1. What is Three-Tier Architecture?

Three-Tier Architecture separates an application into three logical layers:

### Presentation Tier (Frontend)
- User interface
- Handles HTTP requests from users
- Displays responses

### Application Tier (Business Logic)
- Core application logic
- Multiple backend microservices
- Communicates with data tier

### Data Tier (Database Layer)
- Persistent data storage
- Databases and caching systems

### Benefits
- Independent scaling
- Better security
- Loose coupling
- Easier maintenance

---

## 2. Architecture Overview

User
|
▼
Frontend (Web UI / NGINX)
|
▼
Backend Microservices (Kubernetes Pods)
|
▼
Databases (MongoDB / MySQL / Redis)

This is a **microservices-based implementation** of a three-tier architecture.

---

## 3. Microservices Implementation

The application is divided into **multiple independent microservices**, each responsible for a specific business function.

| Service | Technology |
|-------|------------|
| web | Angular + NGINX |
| cart | Node.js |
| user | Java (Spring Boot) |
| catalog | Python (Flask) |
| payment | Node.js |
| shipping | Go |
| ratings | PHP |
| dispatch | Python |
| databases | MongoDB, MySQL |
| cache / queue | Redis, RabbitMQ |

Each service:
- Has its **own source code**
- Has its **own Dockerfile**
- Runs as a **separate Kubernetes Pod**

---

## 4. Dockerization (Dockerfiles)

Each microservice is containerized using **Docker**.

### Why Docker?
- Consistent runtime environment
- Easy portability
- Faster deployments
- Cloud-native standard

### Example Dockerfile Flow
1. Choose base image
2. Copy application code
3. Install dependencies
4. Expose application port
5. Define entry point

Each microservice has its own `Dockerfile` to build an isolated container image.

---

## 5. Container Registry

After building Docker images, they are pushed to a container registry such as:
- Docker Hub
- Azure Container Registry (ACR)

AKS pulls images directly from the registry during deployment.

---

## 6. Kubernetes Manifests

Kubernetes manifests define **how applications run inside the cluster**.

### Manifests Included
- `Deployment` – runs pods
- `Service` – exposes pods internally or externally
- `ConfigMap` – application configuration
- `Secret` – sensitive values (passwords, keys)
- `Ingress` – external access (optional)

### Why Kubernetes?
- Automatic healing
- Load balancing
- Scaling
- High availability

Each microservice has its own Kubernetes manifests.

---

## 7. Helm Charts

To manage Kubernetes manifests efficiently, **Helm** is used.

### What is Helm?
- Package manager for Kubernetes
- Groups all manifests into reusable charts
- Simplifies deployments and upgrades

### Benefits of Helm
- Parameterized values (`values.yaml`)
- Easy rollbacks
- Versioned deployments
- Environment-based configuration

Each microservice can be deployed using Helm charts instead of raw YAML files.

---

## 8. Kubernetes Cluster on AKS

### What is AKS?

**Azure Kubernetes Service (AKS)** is a managed Kubernetes service where:
- Azure manages the **control plane**
- Users manage **worker nodes and applications**

---

## 9. AKS Architecture

### Control Plane (Azure Managed)
- API Server
- Scheduler
- Controller Manager
- etcd

> Control plane is **fully managed and free**

### Data Plane (User Managed)
- Node pools
- VM Scale Sets
- Worker nodes

Pods run on worker nodes.

---

## 10. Creating AKS Cluster

### Example AKS Creation

```bash
az aks create \
  --resource-group aks-rg \
  --name three-tier-aks \
  --node-count 2 \
  --enable-addons monitoring \
  --generate-ssh-keys
This creates:

Managed Kubernetes control plane

Node pool using VM Scale Sets

Azure Monitor integration

11. Connecting to AKS Cluster
'''bash
az aks get-credentials \
  --resource-group aks-rg \
  --name three-tier-aks
'''

Verify:
'''bash
kubectl get nodes
'''

12. Deploying the Application on AKS
Using Kubernetes Manifests
'''bash
kubectl apply -f k8s/
'''

Using Helm
'''bash
helm install three-tier-app ./helm-chart
'''
Verify:
'''bash
kubectl get pods
kubectl get services
'''
13. Load Balancing & Ingress
AKS provides easy integration with:

Azure Load Balancer

NGINX Ingress Controller

Application Gateway Ingress Controller

Frontend services can be exposed using:

Service type: LoadBalancer

Ingress

14. Scaling & High Availability
Pod Scaling
'''bash
kubectl scale deployment web --replicas=3
'''
Cluster Autoscaling
AKS supports auto-scaling

Automatically adds/removes nodes based on load

15. Maintenance Comparison
Feature	Self-Managed K8s	AKS
Control plane	Manual	Azure-managed
Upgrades	Manual	Automatic
Scaling	Manual	Built-in
Maintenance	High	Low

16. Security & Integrations in AKS
AKS integrates easily with:

Azure Active Directory (AAD)

Azure Key Vault (Secrets)

Azure Monitor & Log Analytics

CSI Drivers

Azure Load Balancer

Security and integrations are much easier compared to self-managed clusters.

17. When to Use AKS vs Self-Managed Kubernetes
AKS is Best For:
Freshers and beginners

Faster deployments

Less maintenance

Production workloads

Self-Managed K8s is Best For:
Advanced Kubernetes users

Full control over infrastructure

Custom networking and security

18. End-to-End DevOps Flow
Develop microservices

Write Dockerfiles

Build Docker images

Push images to registry

Write Kubernetes manifests

Package using Helm

Deploy to AKS

Scale, monitor, and upgrade

19. Conclusion
This project demonstrates a complete cloud-native DevOps workflow:

Microservices architecture

Docker containerization

Kubernetes orchestration

Helm-based deployments

AKS-managed Kubernetes

AKS simplifies Kubernetes operations and allows teams to focus on:

CI/CD

Application reliability

Scalability

Observability

