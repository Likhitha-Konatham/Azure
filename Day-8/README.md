# Azure Networking

## 1. What is the difference between NSG and ASG?

### NSG (Network Security Group)

* NSG is used to **allow or deny traffic** to Azure resources.
* It works using **rules** based on:

  * Source IP
  * Destination IP
  * Port
  * Protocol
* NSGs can be attached to **subnets** or **network interfaces (NICs)**.

### ASG (Application Security Group)

* ASG is used to **group virtual machines logically**.
* It does **not control traffic by itself**.
* ASGs are used **inside NSG rules** instead of IP addresses.

### Simple difference:

* **NSG** = Security rules (allow / deny traffic)
* **ASG** = Grouping of VMs to make NSG rules easier to manage

Example:

* Web servers → one ASG
* App servers → another ASG
* NSG rules can allow traffic **from Web ASG to App ASG**

---

## 2. How can you block access to your VM from a subnet?

* By default, Azure allows traffic **between subnets in the same VNet**.
* This is because of a default NSG rule called:

  ```
  AllowVnetInBound
  ```
* This rule has **priority 65000**.

### To block traffic:

1. Create a **new NSG rule**
2. Set action to **Deny**
3. Give it a **priority number lower than 65000** (for example: 1000)
4. Specify the **source subnet** you want to block

Lower priority number = higher importance

---

## 3. Are Azure NSGs stateful or stateless?

**Azure NSGs are stateful**.

### What does stateful mean?

* If you allow **inbound traffic**, the response is automatically allowed
* You do NOT need to create a separate outbound rule

### Example:

* You allow inbound traffic on **port 80**
* User sends a request to your VM
* VM sends response back automatically

No outbound rule is required for the response.

---

## 4. What is the difference between Azure Firewall and NSG?

### Azure Firewall

* Fully managed **network security service**
* Used to control **inbound and outbound traffic**
* Supports:

  * Application rules (FQDN-based)
  * Network rules
  * Threat intelligence
* Used for **enterprise-level security**

### NSG

* Basic security filtering
* Works at **subnet or VM level**
* Uses IP, port, and protocol
* Lightweight and simple

### Simple difference:

* **NSG** → basic traffic control
* **Azure Firewall** → advanced, centralized security

---

## 5. What are the advantages of Resource Groups in Azure?

Resource Groups help you **manage Azure resources easily**.

### Advantages:

* **Logical organization** – group related resources together
* **Lifecycle management** – create, update, or delete all resources together
* **RBAC** – control who can access resources
* **Tagging** – add environment, owner, or project info
* **Cost management** – track costs per resource group
* **ARM templates** – deploy resources together
* **Resource locks** – prevent accidental deletion

---

## 6. What is the difference between Azure User Data and Custom Data?

### Custom Data

* Available **only during first VM boot**
* Used for basic startup scripts
* Data is **not accessible after VM creation**

### User Data

* Newer and improved feature
* Data is stored in Azure
* Can be **viewed and updated anytime**
* Persists even after VM restarts

### Simple difference:

* **Custom Data** → one-time use
* **User Data** → persistent and manageable

---

## 7. What is the difference between Azure Application Gateway and Azure Load Balancer?

### Azure Application Gateway

* Works at **Layer 7 (Application layer)**
* Understands HTTP/HTTPS traffic
* Features:

  * SSL termination
  * Path-based routing
  * Web Application Firewall (WAF)
* Best for **web applications**

### Azure Load Balancer

* Works at **Layer 4 (Transport layer)**
* Routes traffic based on IP and port
* Supports TCP/UDP traffic
* Very fast and simple

### difference:

* **Application Gateway** → smart, web-aware routing
* **Load Balancer** → fast, basic traffic distribution

---

## 8. Traffic flow to an application in an ideal Azure networking setup

### Step-by-step flow:

1. **User Access**

   * User accesses the application using a URL
   * DNS resolves the URL to a public IP

2. **Internet Entry**

   * Traffic enters Azure through:

     * Azure Front Door OR
     * Application Gateway OR
     * Azure Load Balancer

3. **Load Balancing & Security**

   * SSL termination may happen
   * Traffic is distributed across backend servers

4. **VNet Routing**

   * Traffic reaches the **web subnet** inside the VNet

5. **NSG Rules**

   * NSG checks inbound rules
   * Only allowed traffic is permitted

6. **Application Processing**

   * Web servers process the request
   * Response is sent back to the user

---

## 9. What is Azure Bastion and when is it used?

Azure Bastion provides **secure remote access** to VMs.

### Purpose of Azure Bastion:

* Access VMs using **RDP or SSH**
* No public IP required on VM
* Access through **Azure Portal**

### Key benefits:

* No exposure to public internet
* Reduced attack surface
* Uses Azure authentication (RBAC & MFA)
* Secure and audited access

### When to use Bastion:

* Production environments
* High-security workloads
* When public IPs are not allowed

---

## Summary

* NSG = traffic rules
* ASG = VM grouping
* NSGs are stateful
* Bastion = secure VM access
* Application Gateway = Layer 7
* Load Balancer = Layer 4
* Bicep/ARM deploy everything through ARM
