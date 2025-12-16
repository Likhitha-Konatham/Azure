# Azure Virtual Machines, Virtualization & Jenkins Deployment

## Virtualization

Virtualization allows one physical server to run multiple virtual computers, each with its own OS and applications.

* Traditional servers run a single OS and apps directly.
* This can waste resources and makes scaling harder.
* Virtualization creates a layer between hardware and OS, allowing multiple virtual machines (VMs) on one server.

### Components

* **Hypervisor:** Manages resources and creates VMs.

  * Type 1: Runs directly on hardware (bare-metal).
  * Type 2: Runs on top of an OS (hosted).
    
* **Virtual Machines (VMs):** Independent computers with virtual CPU, memory, storage, and networking.

### Key Concepts

* **Server Virtualization:** One server runs multiple VMs.
* **Resource Pooling:** CPU, memory, storage shared among VMs.
* **Isolation:** VMs are independent; issues in one don’t affect others.
* **Snapshots & Cloning:** Save a VM state or duplicate VMs quickly.

### Benefits

* **Server Consolidation:** Run multiple VMs on one server to save costs.
* **Flexibility & Scalability:** Easily create, modify, or scale VMs.
* **Disaster Recovery:** Restore VMs quickly from snapshots/backups.
* **Resource Optimization:** Dynamically allocate resources based on demand.
* **Testing & Development:** Sandbox environments without affecting production.

---

## Azure Virtual Machine Types

Azure provides different VM types for specific workloads:

### General Purpose VMs

* Balanced CPU and memory.
* Good for development, testing, small to medium apps.
* Example: Standard_D2s_v3

### Compute Optimized VMs

* High CPU-to-memory ratio for compute-intensive tasks.
* Example: Standard_F2s_v2

### Memory Optimized VMs

* High memory-to-CPU ratio for memory-heavy apps.
* Example: Standard_E16s_v3

### Storage Optimized VMs

* High disk throughput and I/O for storage-heavy workloads.
* Example: Standard_L8s_v2

### GPU VMs

* Equipped with graphics processors for GPU workloads.
* Example: Standard_NC6s_v3

### High-Performance Compute VMs

* For demanding parallel processing and HPC tasks.
* Example: Standard_H16r

### Burstable VMs

* Baseline CPU performance with occasional bursts.
* Example: B1s

---

## Jenkins Installation Steps on Azure VM

1. **Create an Azure VM**

   * Choose Linux OS (Ubuntu recommended), VM size, resource group, and region.

2. **Open necessary ports**

   * SSH port: 22
   * Jenkins port: 8080

3. **Connect to VM via SSH**

   ```bash
   ssh -i <path-to-pem-file> <username>@<VM-Public-IP>
   ```

4. **Update system packages and Install Java**

   ```bash
   sudo apt update
   sudo apt install openjdk-17-jre
   java -version  # Verify Java installation
   ```

5. **Add Jenkins repository and install Jenkins**

   ```bash
   curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
     /usr/share/keyrings/jenkins-keyring.asc > /dev/null
   echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
     https://pkg.jenkins.io/debian binary/ | sudo tee \
     /etc/apt/sources.list.d/jenkins.list > /dev/null
   sudo apt-get update
   sudo apt-get install jenkins
   ```

6. **Start Jenkins service**

   ```bash
   sudo systemctl start jenkins
   ```

7. **Access Jenkins via browser**

http://<VM-Public-IP-ADDRESS>:8080

8. **Complete initial setup**
   - Unlock Jenkins using the initial admin password.
   - Install suggested plugins.
   - Create admin user.

---

## Conclusion
Following these steps, Jenkins will be installed and accessible on your Azure VM, ready for automation tasks.
