# Azure Networking – Virtual Network, Subnets, NSG, ASG, Public & Private IP

## Virtual Network (VNet)
- A **VNet** is like a private network in the cloud where your Azure resources can communicate securely.
- It is similar to a local area network (LAN) in on-premise infrastructure.
- Each VNet is isolated and has its own **IP address range**.
- VNets can span multiple **subnets**.

---

## Subnets
- Subnets divide a VNet into smaller segments.
- Each subnet has its own IP range.
- Benefits:
  - Organize resources
  - Apply security rules
  - Control traffic flow

---

### CIDR Blocks
- **CIDR (Classless Inter-Domain Routing)** is a method used to define IP address ranges.
- It is written in the format: `IP-address/prefix`
  - Example: `10.0.0.0/24`
- The prefix number decides how many IP addresses are available.
  - Smaller prefix = more IPs
  - Larger prefix = fewer IPs

**CIDR in Azure:**
- VNets use CIDR blocks to define their address space.
- Subnets are created using smaller CIDR ranges within the VNet.

**Example:**
- VNet: `10.0.0.0/16`
- Subnet 1: `10.0.1.0/24`
- Subnet 2: `10.0.2.0/24`

---

## Key Points to Remember
- Subnets divide a VNet into smaller networks.
- CIDR blocks define the IP range for VNets and subnets.
- Proper CIDR planning avoids IP conflicts and improves scalability.

---

## Routes and Route Tables

### Routes
- A **route** defines how network traffic is sent from one place to another.
- It tells Azure **where to send the traffic** and **what path to follow**.
- Each route contains:
  - **Address prefix** (destination IP range)

---

### Route Tables
- A **Route Table** is a collection of routes.
- Route tables are associated with **subnets**.
- When traffic leaves a subnet, Azure checks the route table to decide where the traffic should go.

**Why route tables are used:**
- Control traffic flow
- Force traffic through firewalls
- Customize routing instead of using default routes

---

## Network Security Group (NSG)
- An **NSG** acts like a firewall for your Azure resources.
- You can allow or deny **inbound** and **outbound** traffic.
- NSGs can be applied to:
  - Individual VMs
  - Subnets
- Example:
  - Allow SSH (port 22)
  - Allow HTTP/HTTPS (ports 80/443)
  - Deny all other traffic

---

## What NSGs Control
NSGs decide traffic based on:
- Source IP address
- Destination IP address
- Port number
- Protocol (TCP/UDP)

---

## NSG Rules
- NSG rules define whether traffic is **allowed** or **denied**.
- Each rule has:
  - Priority (lower number = higher priority)
  - Source and destination
  - Port and protocol
  - Action (Allow or Deny)

---

## Application Security Groups (ASGs)

- **Application Security Groups (ASGs)** are used to group Virtual Machines based on their application role.
- Instead of using IP addresses in security rules, ASGs allow using **logical group names**.

---

## Why ASGs are Used
- Makes network security rules **easy to manage**
- Avoids hardcoding IP addresses
- Automatically updates rules when VMs are added or removed

---

## How ASGs Work
- VMs are added to an ASG based on the application they run.
- NSG rules can reference ASGs instead of IP ranges.
- Traffic is allowed or denied **between application groups**.

---

## Example
- Create ASG for **Web Servers**
- Create ASG for **Database Servers**
- NSG rule:
  - Allow traffic from **Web-ASG** to **DB-ASG** on port **3306**

**What this means:**
- Only web servers can talk to database servers
- No need to manage individual VM IPs

---

## Public and Private IP
- **Public IP:** Accessible from the internet.
  - Used to connect to VMs from your computer.
- **Private IP:** Accessible only inside the VNet.
  - Used for internal communication between resources.
- Each VM can have both public and private IPs.

---

## Azure Load Balancer
- Distributes incoming network traffic across multiple VMs.
- Ensures high availability and reliability.
- Supports **internal** (private) and **external** (public) load balancing.

---

## Key Points to Remember
- VNets are the backbone of Azure networking.
- Subnets help organize and secure resources.
- NSGs control access at subnet or VM level.
- Public IPs expose resources to the internet; private IPs are for internal use.
- Load balancers ensure traffic is distributed efficiently.

---

## Conclusion
Azure Networking allows resources to communicate securely and efficiently.
