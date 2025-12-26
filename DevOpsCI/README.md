# Azure DevOps CI Pipelines for Example Voting App

This document explains how to:

- Sign in to Azure DevOps  
- Create an Azure DevOps project  
- Import the `example-voting-app` GitHub repository  
- Create three CI pipelines for the **result**, **vote**, and **worker** services  
- Configure path-based triggers  
- Create an Azure VM  
- Configure a self-hosted Azure DevOps agent  
- Install Docker on the VM  
- Run pipelines successfully using the self-hosted agent  

**Repository used:**  
https://github.com/dockersamples/example-voting-app

---

## 1. Prerequisites

Before you begin, ensure you have:

- An Azure account  
- A GitHub account  
- Access to Azure DevOps  
- Basic understanding of Docker and CI/CD  

---

## 2. Sign in to Azure DevOps

1. Navigate to https://dev.azure.com/
2. Sign in using your Microsoft account.

### Create an Organization (If Required)

1. Click **Create new organization**
2. Choose a name and region
3. Click **Continue**

You should now see the Azure DevOps dashboard.

---

## 3. Create a New Project

1. Click **New Project**
2. Project name: `azurecicd`
3. Visibility: Private (recommended)
4. Click **Create**

---

## 4. Import the GitHub Repository

1. From the left menu, select **Repos**
2. Click **Files**
3. Click **Import**
4. Under **Clone URL**, paste:

https://github.com/dockersamples/example-voting-app

markdown
Copy code

5. Click **Import**
6. Wait for the import to complete

You should now see the following folders:

result/
vote/
worker/

yaml
Copy code

---

## 5. Create CI Pipelines

We will create three separate CI pipelines, one for each service:

- `result`
- `vote`
- `worker`

Each pipeline will:
- Use Azure DevOps YAML
- Use path-based triggers
- Build and push Docker images

---

## 5.1 Pipeline for `result`

### 5.1.1 Start Pipeline Creation

1. Go to **Pipelines → New Pipeline**
2. Select **Azure Repos Git**
3. Select the imported repository

---

### 5.1.2 Define YAML Pipeline

```yaml
trigger:
  paths:
    include:
      - result/*

pool:
  name: azureagent

variables:
  dockerRegistryServiceConnection: '<your-service-connection-id>'
  imageRepository: 'resultapp'
  containerRegistry: '<your-acr-name>.azurecr.io'
  tag: '$(Build.BuildId)'

stages:
- stage: Build
  displayName: Build Image
  jobs:
  - job: Build
    steps:
    - task: Docker@2
      displayName: Build Docker image
      inputs:
        containerRegistry: '$(dockerRegistryServiceConnection)'
        repository: '$(imageRepository)'
        command: build
        Dockerfile: 'result/Dockerfile'
        tags: '$(tag)'

- stage: Push
  displayName: Push Image
  jobs:
  - job: Push
    steps:
    - task: Docker@2
      displayName: Push Docker image
      inputs:
        containerRegistry: '$(dockerRegistryServiceConnection)'
        repository: '$(imageRepository)'
        command: push
        tags: '$(tag)'
```
Click Save and run.

## 5.2 Pipelines for vote and worker

Repeat the same steps for:

- vote → azure-pipelines-vote.yml
- worker → azure-pipelines-worker.yml
- Update: trigger.paths.include, Dockerfile path, imageRepository name

## 6. Create Azure VM (Self-Hosted Agent)

Free Azure DevOps organizations do not have Microsoft-hosted parallelism enabled by default.
To overcome this, we use a self-hosted agent.

## 6.1 Create VM in Azure

Go to Azure Portal
- Create a Linux VM (Ubuntu recommended)
- Username: azureagent
- Authentication: SSH key
- Allow port 22 (SSH)

## 6.2 Login to the VM
```bash
ssh azureuser@<VM_PUBLIC_IP>
```

## 7. Create Agent Pool in Azure DevOps

Azure DevOps → Organization Settings
- Select Agent pools
- Click Add pool
- Pool type: Self-hosted
- Pool name: azureagent
- Click Create

## 8. Add Agent to the VM
## 8.1 Server URL
Use the following format:

```arduino
https://dev.azure.com/janaharitha081
```

## 8.2 Create Personal Access Token (PAT)

Azure DevOps → User settings
- Select Personal access tokens
- Click New Token
- Scope: Agent Pools (Read & Manage)
- Copy the token

## 8.3 Download and Configure Agent

```bash
mkdir agent && cd agent
wget https://vstsagentpackage.azureedge.net/agent/4.266.2/vsts-agent-linux-x64-4.266.2.tar.gz
tar zxvf vsts-agent-linux-x64-4.266.2.tar.gz
./config.sh
Provide:

Server URL

PAT token

Agent pool name: azureagent

Agent name: your choice
```

## 9. Install Docker on the VM

```bash
sudo apt update
sudo apt install -y docker.io
```

Add user to Docker group:

```bash
sudo usermod -aG docker azureuser
newgrp docker
```
Verify Docker:

```bash
docker ps
```

## 10. Start the Agent

```bash
cd ~/agent
./run.sh
```
Expected output:

```rust
Listening for Jobs
```

## 11. Verify Agent Status

Azure DevOps → Organization Settings
- Go to Agent pools → azureagent
- Confirm agent status is Online

## 12. Run the Pipeline

Go to Pipelines
- Click Run pipeline
- Pipeline executes successfully and pushes images to Azure Container Registry.

## 13. Validate Path-Based Triggering

Test Scenario:
- Make a code change inside the result/ folder
- Commit and push the changes

Expected behavior:
- result pipeline runs
- vote pipeline does not run
- worker pipeline does not run

## 14. Conclusion

- GitHub repository successfully imported into Azure Repos
- CI pipelines created per microservice
- Path-based triggers implemented correctly
- Self-hosted Azure DevOps agent configured on Azure VM
- Docker installed and permissions configured
- Pipelines executed successfully using the self-hosted agent

✅ Final Outcome
GitHub CI pipelines successfully migrated to Azure DevOps CI using a self-hosted agent.
