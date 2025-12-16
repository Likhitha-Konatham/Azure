# Azure Secure Network Architecture with Bastion & Firewall

## Architecture Components

### 1. Virtual Network (VNet)

* Created a **custom Virtual Network** with a proper CIDR block.
* Ensures isolation and secure communication between Azure resources.
* Proper IP address planning helps avoid conflicts and supports scalability.

**Example:**

* VNet Address Space: `10.0.0.0/16`

---

### 2. Subnets

Subnets divide the VNet into smaller, manageable network segments.

**Application Subnet**

* Hosts application Virtual Machines.
* CIDR: `10.0.1.0/24`

**Bastion Subnet**

* Dedicated subnet for Azure Bastion.
* Must be named **AzureBastionSubnet**.
* CIDR: `10.0.2.0/27`

**Database Subnet (Optional)**

* Used to isolate database workloads for better security.

---

### 3. Virtual Machines

* Application servers running the workload.
* VM sizing chosen based on performance needs.
* Deployed without public IP for better security.

---

### 4. Azure Bastion

* Provides **secure SSH/RDP access** directly from the Azure Portal.
* No public IP required on Virtual Machines.
* Eliminates the need for jump servers.
* Browser-based connectivity.

---

### 5. Network Security Groups (NSGs)

* Acts as a firewall at subnet or NIC level.
* Controls inbound and outbound traffic.

**Configured Rules:**

* Allow HTTP (Port 80)
* Allow HTTPS (Port 443)
* Deny all other traffic by default

---

### 6. Azure Firewall

* Centralized network security service.
* Provides application and network filtering rules.
* Integrates with threat intelligence feeds for enhanced protection.

---

## Prerequisites

Before starting this project, ensure you have:

* **Azure Account:** Active Azure subscription

---

## Step-by-Step Implementation

This project can be implemented using:

* Azure Portal (UI) 
* Azure CLI 

---

## Implementation Using Azure Portal (UI)

### Step 1: Create Resource Group

* Resource Group Name: `respurce-group`
* Region: East US

---

### Step 2: Create Virtual Network

* VNet Name: `vnet-app`
* Address Space: `10.0.0.0/16`

**Subnets Created:**

* `subnet-app` → `10.0.1.0/24`
* `AzureBastionSubnet` → `10.0.2.0/27`

---

### Step 3: Create Network Security Group

* NSG Name: `nsg-app`

**Inbound Rules:**

* Allow HTTP (80)
* Allow HTTPS (443)

**Association:**

* NSG associated with `subnet-app`

---

### Step 4: Create Virtual Machine

* VM Name: `vm-app-server`
* OS: Ubuntu Server 22.04 LTS
* Size: Standard_B2s
* Public IP: None
* Authentication: SSH key or Password

---

### Step 5: Create Azure Bastion

* Bastion Name: `bastion-app`
* Subnet: AzureBastionSubnet
* Public IP: Standard, Static
* Secure browser-based access enabled

---

### Step 6: Connect to VM Using Bastion

* Navigate to VM → Connect → Bastion
* Authenticate using SSH key or password
* Access VM securely via browser

---

### Step 7: Deploy Application (NGINX)

Run the following commands on the VM:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
sudo systemctl status nginx
echo "<h1>Hello from Azure VM!</h1>" | sudo tee /var/www/html/index.html
sudo systemctl restart nginx
```

NGINX is used as a **sample web server** to validate connectivity and application deployment.

---

## Learning Outcome

* Secure network design in Azure
* Importance of subnet isolation and CIDR planning
* How Azure Bastion improves VM security
* Role of NSGs in traffic control
* Deploying and accessing applications securely

---

## Conclusion

This project demonstrates how to build a **secure, scalable, and production-ready Azure network architecture**.
Using Azure Bastion and NSGs ensures strong security while maintaining ease of access for administrators.
