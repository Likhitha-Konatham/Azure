# Azure DevOps CI Pipelines for Example Voting App

This document explains how to:

* Sign in to Azure DevOps
* Create an Azure DevOps project
* Import the **example-voting-app** GitHub repository
* Create three CI pipelines for the `result`, `vote`, and `worker` folders
* Use built-in templates and configure **path filters** for each pipeline

Repository used for this project:
[https://github.com/dockersamples/example-voting-app](https://github.com/dockersamples/example-voting-app)

---

## 1. Prerequisites

Before you begin:

* An **Azure account**
* A **GitHub account**
* Access to **Azure DevOps**

---

## 2. Sign in to Azure DevOps

1. Navigate to **[https://dev.azure.com/](https://dev.azure.com/)**
2. Sign in with your Microsoft account.
3. If you don’t have an Azure DevOps organization:

   * Click **Create new organization**
   * Choose a name and region
   * Click **Continue**

You should now see the Azure DevOps dashboard.

---

## 3. Create a New Project

1. Click **New Project**
2. Name: `azurecicd`
3. Visibility: **Private** (preferred) or **Public**
4. Click **Create**

---

## 4. Import the GitHub Repository

1. From the left menu, select **Repos**
2. Click **Files**
3. In the top bar, click **Import**
4. Under **Clone URL**, paste:

   ```
   https://github.com/dockersamples/example-voting-app
   ```
5. Click **Import**
6. Wait for the repository import to complete

You should now see all folders (`result`, `vote`, `worker`, etc.) in Azure Repos.

---

## 5. Create CI Pipelines

In this section, we will create **three separate pipelines**, one each for:

* `result`
* `vote`
* `worker`

Each pipeline will:

* Use **Azure DevOps built-in templates**
* Use **path-based triggers** so it runs only when its folder changes

---

### 5.1 Pipeline: result

#### 5.1.1 Start Pipeline Creation

1. Go to **Pipelines → New Pipeline**
2. Select **Azure Repos Git** as the source
3. Select your imported repository: **example-voting-app**

---

#### 5.1.2 Choose Configuration

1. Choose **Starter pipeline** or **Use a template**
2. Optionally select **Docker – Build and push an image** if building containers
3. Azure DevOps opens the YAML editor

---

#### 5.1.3 Define Trigger, Build, and Push Stages

Replace the YAML content with the following:

```yaml
trigger:
  paths:
    include:
      - result/*

pool:
  name: 'azureagent'

stages:
- stage: Build
  displayName: Build Image
  jobs:
  - job: Build
    displayName: Build
    steps:
    - task: Docker@2
      displayName: Build the image to container registry
      inputs:
        containerRegistry: '$(dockerRegistryServiceConnection)'
        repository: '$(imageRepository)'
        command: 'build'
        Dockerfile: 'result/Dockerfile'
        tags: '$(tag)'
    
- stage: Push
  displayName: Push Image
  jobs:
  - job: Push
    displayName: Push 
    steps:
    - task: Docker@2
      displayName: push the image to container registry
      inputs:
        containerRegistry: '$(dockerRegistryServiceConnection)'
        repository: '$(imageRepository)'
        command: 'build'
        Dockerfile: 'result/Dockerfile'
        tags: '$(tag)'
```

4. Click **Save and run**

---

#### 5.1.4 Create Pipelines for vote and worker

Repeat the same steps for the remaining services:

* `vote` → `azure-pipelines-vote.yml`
* `worker` → `azure-pipelines-worker.yml`

---

## 6. Validate the Migration

### Test Scenario

1. Make a code change inside the `result/` folder
2. Commit and push the change
3. Observe pipeline behavior:

* `result` pipeline runs
* `vote` pipeline does not run
* `worker` pipeline does not run

This confirms correct **path-based triggering** after migration.

---

## 7. Conclusion

GitHub CI pipelines migrated to Azure DevOps CI
Repository successfully imported into Azure Repos
Separate CI pipelines created per service
Path filters implemented correctly

**This completes the migration from GitHub CI pipelines to Azure DevOps CI pipelines.**
