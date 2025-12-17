# Azure CLI on Ubuntu – VM and Storage Account Creation

## Step 1: Install Azure CLI

Run the below command to install Azure CLI:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Verify installation:

```bash
az version
```

---

## Step 2: Login to Azure

Login to Azure using browser authentication:

```bash
az login
```

After successful login, your subscription details will be shown.

If you have multiple subscriptions, set the required one:

```bash
az account list --output table
az account set --subscription <SUBSCRIPTION_ID>
```

---

## Step 3: Create a Resource Group

A resource group is a logical container to hold Azure resources.

```bash
az group create \
  --name azure-cli \
  --location southeastasia
```

---

## Step 4: Create an Ubuntu Virtual Machine using Azure CLI

```bash
az vm create \
  --resource-group azure-cli \
  --name myUbuntuVM \
  --image Ubuntu2204 \
  --admin-username azureuser \
  --size Standard_B2ats_v2 \
  --generate-ssh-keys
```

This command automatically creates:

* Virtual Network
* Subnet
* Network Security Group
* Public IP

Get VM public IP:

```bash
az vm show \
  --resource-group myResourceGroup \
  --name myUbuntuVM \
  --show-details \
  --query publicIps \
  --output tsv
```

Login to VM:

```bash
ssh azureuser@<PUBLIC_IP>
```

---

## Step 5: Create a Storage Account

> Storage account name must be **globally unique** and in lowercase.

```bash
az storage account create \
  --name mystorageacct123 \
  --resource-group myResourceGroup \
  --location eastus \
  --sku Standard_LRS \
  --kind StorageV2
```

Verify storage account:

```bash
az storage account show \
  --name mystorageacct123 \
  --resource-group myResourceGroup \
  --output table
```

---

## Step 6: Create a Blob Container

Get storage account key:

```bash
az storage account keys list \
  --account-name mystorageacct123 \
  --resource-group myResourceGroup \
  --output table
```

Create blob container:

```bash
az storage container create \
  --name mycontainer \
  --account-name mystorageacct123 \
  --account-key <STORAGE_ACCOUNT_KEY>
```

---

## Step 7: Upload File to Blob Storage

```bash
az storage blob upload \
  --account-name mystorageacct123 \
  --account-key <STORAGE_ACCOUNT_KEY> \
  --container-name mycontainer \
  --file sample.txt \
  --name sample.txt
```

---

## Cleanup (Important to avoid charges)

```bash
az group delete --name azure-cli --yes --no-wait
```
