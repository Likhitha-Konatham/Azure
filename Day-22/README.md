# Azure Functions – Serverless Computing with Blob Trigger (Python)

This document explains the **complete concept of Azure Serverless using Azure Functions**, focusing on a **Python BlobTrigger function running on a Consumption Plan**.  
It covers **architecture, Azure CLI setup, Azure Portal (UI) demonstration, execution flow, and monitoring**.

---

## What is Serverless?

Serverless means:
- You **do not manage servers**
- Azure handles:
  - Infrastructure
  - Scaling
  - Availability
- You focus only on **code and business logic**
- You pay **only when code runs**

Azure Functions is Azure’s **serverless compute service**.

---

## What is Azure Functions?

Azure Functions allows you to run **event-driven code** without provisioning servers.

Key features:
- Event-based execution
- Automatic scaling
- Multiple runtime support (Python, Java, Node.js, .NET)
- Built-in integration with Azure services

---

## What is a Blob Trigger?

A **BlobTrigger** runs a function **automatically when a blob is created or modified** in an Azure Storage container.

Common use cases:
- Image processing
- File validation
- ETL pipelines
- Log processing
- Backup automation

---

## Architecture Overview

```text
User uploads file
        |
        v
Azure Blob Storage (Container)
        |
        v
BlobTrigger Function (Python)
        |
        v
Logs / Processing / Output

Prerequisites:
- Azure Subscription
- Azure CLI installed
- Python knowledge (basic)
- Azure Portal access

Login:
az login

Important Constraints:
- Function App name must be globally unique
- Storage Account name must be globally unique
- Storage Account is mandatory for:
- Function state
- Triggers
- Logs

Variables Used:

let "randomIdentifier=$RANDOM*$RANDOM"
location="eastus"
resourceGroup="azure-functions-rg-$randomIdentifier"
storage="storage$randomIdentifier"
functionApp="serverless-python-function-$randomIdentifier"
skuStorage="Standard_LRS"
functionsVersion="4"
pythonVersion="3.9"

Creating Resources using Azure CLI

1️. Create Resource Group

az group create \
  --name $resourceGroup \
  --location $location

2️. Create Storage Account

az storage account create \
  --name $storage \
  --location $location \
  --resource-group $resourceGroup \
  --sku $skuStorage

3️. Create Python Function App (Consumption Plan)

az functionapp create \
  --name $functionApp \
  --storage-account $storage \
  --consumption-plan-location $location \
  --resource-group $resourceGroup \
  --os-type Linux \
  --runtime python \
  --runtime-version $pythonVersion \
  --functions-version $functionsVersion

Azure Portal (UI) – BlobTrigger Setup

Step 1: Open Azure Portal
https://portal.azure.com

Step 2: Open Resource Group
Navigate to:
pgsql
Resource Groups → azure-functions-rg-<random>

Step 3: Open Function App
Click:
arduino
serverless-python-function-<random>

Step 4: Create BlobTrigger Function
- Go to Functions

- Click Create

Choose:
- Development: Develop in portal
- Template: Blob trigger
- Path:
pgsql
samples-workitems/{name}
- Storage account: Default
- Click Create

BlobTrigger Function Code (Python):
python code:

import logging

def main(myblob):
    logging.info(
        f"Blob Trigger executed\n"
        f"Blob Name: {myblob.name}\n"
        f"Size: {myblob.length} bytes"
    )
```

**Blob Container Setup**
- Open Storage Account
- Go to Containers

Create container:
samples-workitems

**Execution Flow (How It Works):**
- File uploaded to Blob container
- Azure Storage emits event
- BlobTrigger Function executes
- Function reads blob metadata/content
- Logs are written to Application Insights

Monitoring & Logs:

Function Monitoring
Function App → Functions → Monitor

Real-time Logs
Function App → Log Stream

Application Insights
Execution duration

Failures:
Performance metrics

Why Consumption Plan?
- No upfront cost
- Auto-scale
- Pay per execution
- Ideal for event-driven workloads

Cleanup (Important):
- To avoid unnecessary charges:

az group delete --name $resourceGroup --yes --no-wait

**Key Takeaways:**
- Azure Functions = Serverless compute
- BlobTrigger is fully event-driven
- Storage Account is mandatory
- Consumption plan is cost-effective
- No server management required
