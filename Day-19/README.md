# Monitoring in Azure Platform (AKS – Kubernetes Monitoring)

This document explains **monitoring on the Azure platform**, specifically for **Kubernetes clusters (AKS)**.

It covers:
- What monitoring is
- What DevOps engineers monitor
- How to create a monitoring system on Azure
- **6 levels of Kubernetes monitoring in AKS**

---

## 1. What is Monitoring?

**Monitoring** means continuously watching systems, applications, and infrastructure to:
- Check whether they are working correctly
- Detect issues early
- Reduce downtime
- Improve performance and reliability

In simple words:  
**Monitoring tells us what is happening inside our system in real time.**

---

## 2. What Do DevOps Engineers Monitor?

DevOps engineers monitor:
- Servers and virtual machines
- Kubernetes clusters
- Applications and APIs
- Network traffic
- Logs and errors
- Performance and availability
- Security and resource usage

The goal is to **find problems before users notice them**.

---

## 3. Monitoring on Azure Platform

Azure provides built-in monitoring services such as:
- **Azure Monitor**
- **Azure Log Analytics**
- **Application Insights**
- **Network Watcher**

For Kubernetes (AKS), Azure follows a **multi-level monitoring approach**.

---

## 4. Kubernetes Monitoring in AKS – 6 Levels

In AKS, Kubernetes components can be monitored at **6 different levels**.

---

## Level 1: Network Components Monitoring

### What is monitored?
- Virtual Network (VNet)
- Subnets
- Network traffic
- Connectivity issues
- NSG flow logs

### Tool Used:
**Azure Network Watcher**

### Why it is important?
- Helps troubleshoot network issues
- Detects packet drops and latency
- Ensures pods and services can communicate properly

---

## Level 2: Node / Node Pools Monitoring

### What is monitored?
- Worker nodes
- CPU usage
- Memory usage
- Disk utilization
- Node health

### Tool Used:
**Prometheus**

### Why it is important?
- Nodes run all Kubernetes workloads
- If nodes fail, applications go down
- Helps in capacity planning and scaling

---

## Level 3: Control Plane Components Monitoring

### What is monitored?
- API Server
- Scheduler
- Controller Manager
- etcd (indirectly)

### Tool Used:
**Azure Monitor**

### Why it is important?
- Control plane manages the entire cluster
- Azure manages control plane in AKS
- Azure Monitor gives visibility into control plane health

You **do not manage control plane VMs** in AKS, but you **monitor them**.

---

## Level 4: Pods and Containers Monitoring

### What is monitored?
- Pod health
- Container CPU and memory
- Restarts and crashes
- Resource limits and requests

### Tool Used:
**Prometheus**

### Why it is important?
- Pods run microservices
- Helps identify failing containers
- Useful for auto-scaling and performance tuning

---

## Level 5: Application Performance Monitoring (APM)

### What is monitored?
- Application response time
- API latency
- Errors and exceptions
- Dependency failures

### Tool Used:
**Azure Monitor → Application Insights**

### Why it is important?
- Focuses on **application behavior**
- Helps developers and DevOps teams understand real user experience
- Useful for debugging production issues

---

## Level 6: Remaining Azure Components Monitoring

### What is monitored?
- Virtual Machines
- Serverless components (Azure Functions)
- Databases
- Load balancers
- Storage accounts

### Tool Used:
**Azure Monitor**

### Why it is important?
- Applications depend on multiple Azure services
- Centralized monitoring helps manage everything from one place

---

## 5. Summary of Monitoring Tools

| Monitoring Level | Component | Tool Used |
|-----------------|----------|----------|
| Network | VNet, traffic | Azure Network Watcher |
| Nodes | Worker nodes | Prometheus |
| Control Plane | AKS control plane | Azure Monitor |
| Pods / Containers | Kubernetes workloads | Prometheus |
| APM | Application metrics | Azure Monitor (Application Insights) |
| Other Azure Services | VMs, Functions, DBs | Azure Monitor |

---

## 6. How to Create a Monitoring System on Azure (High Level Steps)

1. Enable **Azure Monitor** on AKS
2. Configure **Log Analytics Workspace**
3. Install **Prometheus** for metrics collection
4. Use **Grafana** for dashboards (optional)
5. Enable **Application Insights** for applications
6. Set alerts and notifications
7. Monitor continuously and optimize

---

## 7. Why Monitoring is Important in AKS?

- Detect failures early
- Improve system reliability
- Enable auto-scaling decisions
- Reduce downtime
- Improve user experience
- Support DevOps and SRE practices

---

## 8. Final Notes

- Monitoring is **mandatory** for production systems
- AKS uses **Azure-native + open-source tools**
- Prometheus + Azure Monitor is a common combination
- Always monitor **infrastructure + application + network**

---

## Conclusion

Monitoring in Azure AKS is done at **multiple levels**, using the right tool for each component.  
This layered approach helps DevOps engineers maintain **high availability, performance, and security** of Kubernetes workloads on Azure.

