# Azure IAM Demonstration (Authentication & Authorization)

## 1. IAM Concepts in Azure

### Authentication

Authentication is the process of verifying identity. It answers **"Who you are?"**

* Handled by **Microsoft Entra ID (Azure AD)**
* Verifies identity of users, applications, or Azure resources

Examples:

* User login to Azure Portal
* VM authenticating using Managed Identity

---

### Authorization

Authorization determines what permissions an authenticated identity has. It answers **"What are you allowed to do?"**

* Implemented using **Azure Role-Based Access Control (RBAC)**
* Roles define permissions

Examples:

* Owner
* Contributor
* Reader
* Storage Blob Data Reader

---

## 2. Users, Roles & Groups

### Users

* Human identities (developers, admins)
* Created in Microsoft Entra ID

### Groups

* Collection of users
* Role assigned to group instead of individuals

### Roles

* Define permissions on Azure resources
* Assigned at:

  * Subscription level
  * Resource group level
  * Resource level

---

## 3. Azure Resources Communicating with Each Other

Azure resources authenticate using:

### Service Principal

* Application identity
* Requires client secret or certificate
* Manual credential management

### Managed Identity (Recommended)

* Identity automatically managed by Azure
* No secrets or credentials
* Two types:

  * System-assigned
  * User-assigned

In this demo, we use **System-assigned Managed Identity**.

---

## 4. Demo Architecture

```
Virtual Machine (Managed Identity)
        |
        |  (Azure AD Token)
        v
Storage Account (Blob Container)
```

---

## 5. Demo: Create Resources (Azure Portal / UI Steps)

> Below steps explain how to perform the entire demo **using Azure Portal (UI)** without Azure CLI.

---

### Step 1: Create Resource Group (UI)

1. Login to **Azure Portal**
2. Search for **Resource groups**
3. Click **Create**
4. Select Subscription
5. Resource group name: `iam-rg`
6. Region: `East US`
7. Click **Review + Create → Create**

---

### Step 2: Create Storage Account (UI)

1. Search for **Storage accounts**
2. Click **Create**
3. Basics tab:

   * Subscription: Select your subscription
   * Resource group: `iam-rg`
   * Storage account name: `storageacc541` (must be unique)
   * Region: `East US`
   * Performance: Standard
   * Redundancy: LRS
4. Click **Review + Create → Create**

---

### Step 3: Create Blob Container (UI)

1. Open the **Storage Account**
2. Go to **Data storage → Containers**
3. Click **+ Container**
4. Name: `web`
5. Public access level: **Private (no anonymous access)**
6. Click **Create**

---

### Step 4: Upload index.html (UI)

1. Open the `web` container
2. Click **Upload**
3. Upload `index.html`
4. Click **Upload**

---

## 6. Create Virtual Machine with Managed Identity (UI)

### Step 5: Create VM

1. Search for **Virtual Machines**
2. Click **Create → Azure virtual machine**
3. Basics tab:

   * Resource group: `iam-rg`
   * VM name: `iam-vm`
   * Region: `East US`
   * Image: `Ubuntu Server 22.04 LTS`
   * Authentication: SSH public key
   * Username: `azureuser`
4. Click **Next** until **Review + Create**
5. Click **Create**

---

### Step 6: Enable System Assigned Managed Identity

1. Open the **Virtual Machine**
2. Go to **Identity** (left menu)
3. Under **System assigned**:

   * Status: **On**
4. Click **Save**

---

## 7. Assign Role to VM (Authorization via UI)

### Step 7: Role Assignment on Storage Account

1. Open the **Storage Account**
2. Go to **Access Control (IAM)**
3. Click **Add → Add role assignment**
4. Role: **Owner**
5. Assign access to: **Managed identity**
6. Click **Select members**
7. Choose:

   * Subscription
   * Resource type: Virtual Machine
   * Select VM: `iam-vm`
8. Click **Review + Assign**

---

## 8. Connect to VM & Access Blob

### Step 8: Connect to VM (UI)

Connect to VM from ubuntu terminal.

```bash
ssh -i <path-to-pem-file> azureuser@<public-ip>
```

---

## 9. Access Blob using Managed Identity

### Step 9: Install Tools

```bash
sudo apt update
sudo apt install -y curl jq
```

### Step 10: Fetch Access Token

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com%2F' -H Metadata:true | jq -r '.access_token')
```

### Step 11: Access Blob File

```bash
storage_account_name="storageacc541"
container_name="web"
blob_name="index.html"

curl "https://storageacc541.blob.core.windows.net/web/index.html" \
  -H "x-ms-version: 2017-11-09" \
  -H "Authorization: Bearer $access_token"
```

---

## 10. Final Outcome

* VM authenticated via **Managed Identity**
* Azure AD issued access token
* Storage Account validated RBAC
* Blob accessed securely without keys
