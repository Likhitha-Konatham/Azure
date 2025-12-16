# Azure Networking – Advanced Concepts

## Azure Application Gateway
- Azure Application Gateway manages **web (HTTP/HTTPS) traffic**.
- It routes requests to the correct backend servers.
- Works at the **application level**.

### Key Features
- **Load Balancing:** Distributes traffic across multiple web servers.
- **SSL Termination:** Handles HTTPS encryption, reducing load on servers.
- **Path-based Routing:** Routes traffic based on URL paths.

---

## Web Application Firewall (WAF)
- WAF protects web applications from common web attacks.

### Why WAF is Important
- Improves application security
- Protects without changing application code

---

## Azure Load Balancer
- Azure Load Balancer distributes **network-level traffic**.
- Works at **Layer 4 (TCP/UDP)**.

### Key Features
- Distributes inbound traffic evenly
- Manages outbound traffic from VMs
- Works with availability sets for high availability

---

## Azure DNS
- Azure DNS converts **domain names into IP addresses**.
- Hosts DNS zones in Azure.

### Key Features
- Domain name hosting
- Integration with Azure services
- Fast global name resolution

---

## Azure Firewall
- Azure Firewall is a **managed network security service**.
- Protects Azure Virtual Network resources.

### Key Features
- Stateful traffic filtering
- Filters traffic using domain names (FQDNs)
- Blocks traffic from known malicious IPs

---

## Virtual Network Peering
- Connects two Azure Virtual Networks directly.
- Allows private communication between VNets.

### Key Features
- Works across regions (Global Peering)
- High performance using Azure backbone network
- No public internet exposure

---

## VNet Gateway
- VNet Gateway connects Azure networks with on-premises networks.

### Key Features
- **Site-to-Site VPN:** Connects office network to Azure
- **Point-to-Site VPN:** Secure access for remote users

---

## Azure VPN Gateway
- Provides secure encrypted connections between on-premises and Azure.

### Key Features
- Uses IPsec/IKE protocols
- Supports high availability configurations
- Supports BGP for dynamic routing

---

## Key Takeaways
- Application Gateway manages and secures web traffic
- WAF protects applications from web attacks
- Load Balancer handles network traffic
- Azure DNS manages domain resolution
- Azure Firewall secures networks
- VNet Peering connects VNets
- VPN Gateway enables hybrid connectivity

---

## Conclusion
Azure networking services help build **secure, scalable, and highly available** cloud architectures.  
