# Azure DevOps CI + GitOps CD using Argo CD

## Project Description

This project demonstrates a **complete CI/CD implementation using GitOps** for the **Example Voting App** microservices application.

The solution uses:

* **Azure DevOps Pipelines** for Continuous Integration (CI)
* **Azure Container Registry (ACR)** to store Docker images
* **Azure Kubernetes Service (AKS)** to run containers
* **Argo CD** for GitOps-based Continuous Deployment (CD)

CI pipelines are already implemented for:

* vote
* worker
* result

This document explains:

* Create Azure resources using Azure Portal (UI)
* Configure AKS
* Install and configure Argo CD
* Create Kubernetes manifests
* Create ACR ImagePullSecret
* Update Azure DevOps pipelines with an Update stage
* Automatically deploy to AKS using GitOps

---

## End-to-End Architecture Flow

Developer commits code
↓
Azure DevOps CI Pipeline (Build Docker Image)
↓
Push Image to Azure Container Registry
↓
Update Kubernetes Manifest (image tag)
↓
Git Repository (GitOps source of truth)
↓
Argo CD detects change
↓
Argo CD syncs to AKS
↓
Application deployed to Kubernetes

---

## Application Source Repository

[https://github.com/dockersamples/example-voting-app](https://github.com/dockersamples/example-voting-app)

---

## Prerequisites

Before starting, ensure you have:

* Azure Subscription
* Azure DevOps Organization
* Azure DevOps Project
* Azure DevOps Agent (self-hosted or Microsoft-hosted)
* Azure CLI installed
* kubectl installed
* Docker installed
* Basic understanding of Kubernetes & CI/CD

---

# PART 1: Azure Infrastructure Setup (Azure Portal – UI)

---

## Step 1: Create Resource Group

1. Login to **Azure Portal**
2. Search for **Resource Groups**
3. Click **Create**
4. Enter the following details:

   * Subscription: Your subscription
   * Resource Group Name: `azurecicd`
   * Region: `East US`
5. Click **Review + Create**
6. Click **Create**

---

## Step 2: Create Azure Container Registry (ACR)

1. Azure Portal → Search for **Container Registries**
2. Click **Create**
3. Fill in details:

   * Subscription: Your subscription
   * Resource Group: `azurecicd`
   * Registry Name: `likhithaazurecicd`
   * Location: East US
   * SKU: Basic
4. Click **Review + Create**
5. Click **Create**

---

## Step 3: Create Azure Kubernetes Service (AKS)

1. Azure Portal → Search for **Kubernetes services**
2. Click **Create**
3. Basics tab:

   * Resource Group: `azurecicd`
   * Cluster Name: `azurecicdcluster`
   * Region: East US
   * Kubernetes version: Default
   * Node count: 2
4. Authentication:

   * Select **System-assigned managed identity**
5. Networking:

   * Leave default values
6. Monitoring:

   * Enable monitoring
7. Click **Review + Create**
8. Click **Create**

---

## 🔗 Step 4: Attach ACR to AKS

1. Open **AKS cluster**
2. Go to **Integrations**
3. Attach ACR:

   * Select `likhithaazurecicd`
4. Save changes

This allows AKS to pull Docker images from ACR.

---

## Step 5: Connect to AKS Cluster (CLI)

```bash
az login
az aks get-credentials \
  --resource-group azurecicd \
  --name azurek8scicd
```

Verify connection:

```bash
kubectl get nodes
```

---

# PART 2: Create ACR ImagePullSecret

## Create ACR ImagePullSecret

```bash
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```

### Add ImagePullSecret to Deployment YAML

```yaml
imagePullSecrets:
- name: acr-secret
```

---

# PART 3: Install & Configure Argo CD

## Step 6: Install Argo CD

```bash
kubectl create namespace argocd
kubectl apply -n argocd \
-f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Verify installation:

```bash
kubectl get pods -n argocd
```

---

## Step 7: Access Argo CD UI

```bash
kubectl port-forward svc/argocd-server -n argocd 8084:443
```

Open browser:

[https://localhost:8084](https://localhost:8084)

---

## Step 8: Get Argo CD Admin Password

```bash
kubectl get secret argocd-initial-admin-secret \
-n argocd \
-o jsonpath="{.data.password}" | base64 -d
```

Username: `admin`

---

## Step 9: Generate Azure DevOps PAT Token

Azure DevOps → User Settings → Personal Access Tokens

Permissions:

* Code → Read & Write

---

## Step 10: Connect Azure DevOps Repo to Argo CD

Repository URL:

```
https://dev.azure.com/<ORG>/voting-app/_git/voting-app
```

Username: any value
Password: Azure DevOps PAT

---

## Step 11: Create Argo CD Application

* Application Name: azurecicd
* Project: default
* Repository URL: Azure DevOps repo
* Path: k8s-specifications
* Cluster URL: [https://kubernetes.default.svc](https://kubernetes.default.svc)
* Namespace: default
* Sync Policy: Automatic

---

# PART 4: GitOps Implementation

## Kubernetes Manifest Folder Structure

```
k8s-specifications/
├── vote-deployment.yaml
├── worker-deployment.yaml
├── result-deployment.yaml
```

---

## updateK8sManifests.sh Script

```bash
#!/bin/bash
set -x
REPO_URL="https://<ACCESS-TOKEN>@dev.azure.com/<ORG>/voting-app/_git/voting-app"

git clone "$REPO_URL" /tmp/temp_repo
cd /tmp/temp_repo

sed -i "s|image:.*|image: <ACR-REGISTRY>/$2:$3|g" \
k8s-specifications/$1-deployment.yaml

git add .
git commit -m "Update Kubernetes manifest"
git push

rm -rf /tmp/temp_repo
```

---

# PART 5: Azure DevOps Pipeline with Update Stage

```yaml
# Docker
# Build and push an image to Azure Container Registry
# https://docs.microsoft.com/azure/devops/pipelines/languages/docker

trigger:
 paths:
   include:
     - vote/*

resources:
- repo: self

variables:
  # Container registry service connection established during pipeline creation
  dockerRegistryServiceConnection: '6d98ab55-5471-477e-b47c-a3c92999a578'
  imageRepository: 'votingapp'
  containerRegistry: 'likhithaazurecicd.azurecr.io'
  dockerfilePath: '$(Build.SourcesDirectory)/result/Dockerfile'
  tag: '$(Build.BuildId)'

pool:
 name: 'azureagent'


stages:
- stage: Build
  displayName: Build 
  jobs:
  - job: Build
    displayName: Build
    steps:
    - task: Docker@2
      displayName: Build an image
      inputs:
        containerRegistry: '$(dockerRegistryServiceConnection)'
        repository: '$(imageRepository)'
        command: 'build'
        Dockerfile: 'vote/Dockerfile'
        tags: '$(tag)'
- stage: Push
  displayName: Push 
  jobs:
  - job: Push
    displayName: Push
    steps:
    - task: Docker@2
      displayName: Build an image
      inputs:
        containerRegistry: '$(dockerRegistryServiceConnection)'
        repository: '$(imageRepository)'
        command: 'push'
        tags: '$(tag)'
- stage: Update
  displayName: Update 
  jobs:
  - job: Update
    displayName: Update
    steps:
    - task: ShellScript@2
      inputs:
        scriptPath: 'scripts/updateK8sManifests.sh'
        args: 'vote $(imageRepository) $(tag)'
```

Repeat same logic for **worker** and **result** pipelines, Click on save and run. Verify deployments.

---

## Verification Commands

```bash
kubectl get pods
kubectl get svc
```

---

## Final Result

* CI builds Docker images
* Images pushed to ACR
* Manifests updated automatically
* Argo CD syncs changes
* AKS deploys latest version
