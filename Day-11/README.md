# Azure Resource Manager (ARM) Templates

## What Are ARM Templates?

Azure Resource Manager (ARM) Templates are **declarative JSON files** that describe your Azure infrastructure. They allow you to define **what resources** (like VMs, storage accounts, networks) you want to create, and Azure deploys them automatically.

* They follow the **Infrastructure as Code (IaC)** approach.
* JSON format means all resources are described in code instead of manual steps.
* ARM templates are **native to Azure** and can be deployed using **Azure CLI, PowerShell, Azure Portal, or CI/CD tools**.

---

## Why Use ARM Templates?

ARM templates help you:

* **Automate deployments** — no need to create resources manually every time.
* **Repeat deployments reliably** — templates are idempotent (running many times has the same result).
* **Version control infrastructure** — keep infrastructure and application code together.
* **Deploy complex architectures** in a single deployment.

---

## Structure of an ARM Template

An ARM template typically includes:

* **$schema** — Defines the template’s schema version.
* **contentVersion** — Version of the template.
* **parameters** — Input values you can pass at deployment time.
* **variables** — Intermediate values used inside the template.
* **resources** — The actual Azure resources to create.
* **outputs** — Return values after deployment.

---

## Azure Storage Account using ARM Template

## Prerequisites

* Azure account
* Azure CLI installed
* Logged in to Azure

```bash
az login
```

---

## Step 1: Create a Resource Group

```bash
az group create --name storageRG --location eastus
```

---

## Step 2: Create the ARM Template

Create a file named `storage-template.json` with the following content:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "name": "storageacct752",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2023-01-01",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      }
    }
  ],
  "outputs": {}
}
```

> Note: Storage account name must be globally unique and lowercase.

---

## Step 3: Deploy the ARM Template

Navigate to the folder where the JSON file is saved and run:

```bash
az deployment group create \
  --resource-group storageRG \
  --template-file storage-template.json
```

---

## Step 4: Verify the Storage Account

```bash
az storage account list --resource-group storageRG --output table
```

Or for a specific account:

```bash
az storage account show --name storageacct752 --resource-group storageRG
```

---

## Step 5: Output Storage Account Name

Add this to the ARM template outputs to return the storage account name:

```json
"outputs": {
  "storageAccountName": {
    "type": "string",
    "value": "storageacct752"
  }
}
```

---

## Step 6: Cleanup Resources

```bash
az group delete --name storageRG --yes --no-wait
```

---

## Summary

1. Login to Azure
2. Create Resource Group
3. Create ARM template JSON
4. Deploy template

