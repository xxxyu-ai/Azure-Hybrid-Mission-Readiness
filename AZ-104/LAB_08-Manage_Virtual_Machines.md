# LAB 08: Manage Virtual Machines

## Lab Introduction
In this lab, I focused on deploying and scaling Azure Virtual Machines (VMs). I explored high-availability configurations using Availability Zones and compared manual scaling of single VMs against the automated capabilities of Virtual Machine Scale Sets (VMSS).

---

## Task 1: Deploy Zone-Resilient Azure VMs

In this task, I implemented a high-availability architecture by deploying two Windows Server VMs across different physical data centers (Availability Zones) within the East US region. This configuration is the industry standard for achieving a 99.99% uptime SLA.

### Implementation Steps
1. **Multi-Zone Configuration:**
   - **Resource Group:** `az104-rg8`
   - **Availability Strategy:** Selected **Zone 1 and Zone 2** to ensure physical separation of the workloads.
2. **VM Provisioning:**
   - **VM Names:** `az104-vm1` (Zone 1) and `az104-vm2` (Zone 2).
   - **OS:** Windows Server 2025 Datacenter.
   - **Size:** `Standard_D2s_v3`.
3. **Storage & Networking Hardening:**
   - **Disks:** Utilized **Premium SSD** for optimal performance and enabled "Delete with VM" to ensure clean resource lifecycle management.
   - **Networking:** Set Public Inbound Ports to **None** to prevent unauthorized external access.
   - **Lifecycle Management:** Configured the environment to automatically delete the Public IP and NIC when the VM is removed, preventing "orphan resources" and unnecessary costs.
4. **Monitoring:** Disabled Boot Diagnostics to accelerate the deployment phase for this lab.

---

## Evidence
> **[Paste screenshot here]**
> *Capture the 'Virtual machines' list showing both az104-vm1 and az104-vm2. Ensure the 'Location' column displays 'East US (Zone 1)' and 'East US (Zone 2)' respectively.*

---

## Professional Insight
- **The 99.99% SLA Logic:** Simply having two VMs isn't enough for the highest SLA; they must be in different **Availability Zones**. By placing them in separate zones, I've ensured that even if an entire data center experiences a power or cooling failure, the application remains operational in the other zone.
- **Resource Independence:** During deployment, Azure treats the NIC, Disk, and Public IP as separate entities. This modularity allows for advanced scenarios, such as attaching the same OS disk to a different VM instance during a recovery operation.
- **Security-First Deployment:** By disabling public inbound ports from the start, I am practicing **Defense in Depth**. In a production environment, management of these servers would be handled via Azure Bastion or a VPN, rather than exposing RDP directly to the internet.

---

## Task 2: Manage Compute and Storage Scaling for Virtual Machines

In this task, I practiced vertical scaling (scaling up/down) by resizing an existing VM and managing its storage components. I demonstrated the ability to adjust compute resources based on demand and upgrade disk performance tiers without data loss.

---

### Implementation Steps
1. **Compute Resizing (Vertical Scaling):**
   - Navigated to `az104-vm1` and adjusted the size to `Standard_D2ds_v4`.
   - **Result:** The VM was successfully resized. This process involves a brief restart as Azure moves the VM to a physical host with the required hardware specifications.
2. **Data Disk Management:**
   - Created and attached a new 32 GiB **Standard HDD** named `vm1-disk1`.
   - Practiced the **Detach** operation, which removes the disk from the VM while preserving the data in Azure Storage.
3. **Storage Tier Upgrade:**
   - Accessed the standalone `vm1-disk1` resource and upgraded its performance tier from **Standard HDD** to **Standard SSD**.
   - Re-attached the upgraded disk to `az104-vm1`.
4. **Verification:**
   - Confirmed that `az104-vm1` now operates with the new SKU and the higher-performance SSD data disk.

---

## Evidence
> **[Paste screenshot here]**
> *Capture the 'Size' blade of az104-vm1 showing the current SKU as D2ds_v4, and the 'Disks' blade showing vm1-disk1 with the 'Standard SSD' SKU.*

---

## Professional Insight
- **Vertical Scaling Considerations:** While resizing a VM is a powerful way to handle increased load, it requires a reboot. In a "Zero-Failure" environment, I would perform this during a maintenance window or ensure that the secondary node (`az104-vm2`) is handling traffic during the transition.
- **Storage Flexibility:** The ability to detach, upgrade, and re-attach disks is a key advantage of cloud infrastructure. It allows us to start with low-cost storage (HDD) and move to high-performance storage (SSD) only when the application's I/O requirements increase.
- **Resource Decoupling:** This task reinforced the concept that Managed Disks are independent Azure resources. They have their own lifecycle and can be moved between VMs, providing significant flexibility for disaster recovery and workload migration.

---

## Task 3: Create and Configure Azure Virtual Machine Scale Sets (VMSS)

In this task, I implemented a Virtual Machine Scale Set (VMSS) to provide high availability and automated management for a fleet of virtual machines. By distributing instances across all three availability zones and integrating an Azure Load Balancer, I established a resilient architecture capable of handling horizontal scaling.

---

### Implementation Steps
1. **VMSS Foundation:**
   - **Name:** `vmss1`
   - **Availability Zones:** Selected **Zones 1, 2, and 3** for maximum regional resiliency.
   - **Orchestration Mode:** Uniform (optimized for large-scale, identical VM instances).
2. **Network Infrastructure:**
   - Created a custom VNet `vmss-vnet` with an address space of `10.82.0.0/20`.
   - Configured a dedicated subnet `subnet0` (`10.82.0.0/24`).
3. **Security Configuration:**
   - Deployed a Network Security Group (NSG) named `vmss1-nsg`.
   - Added an inbound rule `allow-http` (Priority 1010) to permit traffic on **Port 80**.
   - Enabled **Public IP addresses** for the scale set instances to facilitate direct testing if needed.
4. **Integrated Load Balancing:**
   - Configured an Azure Load Balancer named `vmss-lb` during the VMSS creation process. This automatically manages the distribution of traffic to all instances within the scale set.
5. **Optimization:**
   - Disabled Boot Diagnostics to streamline the deployment of multiple instances.

---

## Evidence
> **[Paste screenshot here]**
> *Capture the 'Instances' blade of vmss1 showing the initial virtual machine instances being provisioned across different availability zones.*

---

## Professional Insight
- **Automation vs. Manual Overhead:** Unlike Task 1, where I manually created individual VMs, VMSS allows me to define a "template" once. Azure then handles the deployment, networking, and load balancer integration for every instance in the set, significantly reducing administrative effort.
- **Zonal Redundancy:** By spanning all three zones (1, 2, and 3), this architecture ensures that the service remains available even if two out of three data centers in the region experience a complete outage.
- **Load Balancer Synergy:** Creating the load balancer (`vmss-lb`) as part of the VMSS workflow ensures that as the scale set grows or shrinks (Scale Out/In), the load balancer is automatically updated to include or remove those instances from its backend pool.

---

## Task 4: Scale Azure Virtual Machine Scale Sets (Autoscaling)

In this final core task, I implemented dynamic horizontal scaling by configuring custom autoscale rules based on performance metrics. This ensures that the infrastructure remains responsive during high demand while optimizing costs during periods of low activity.

---

### Implementation Steps
1. **Autoscale Strategy:**
   - Switched from Manual scale to **Custom autoscale**.
   - Configured the scaling mode to **Scale based on a metric** (Percentage CPU).
2. **Scale Out Rule (Expansion):**
   - **Trigger:** Average CPU usage > **70%** for **10 minutes**.
   - **Action:** Increase instance count by **50%**.
   - **Cool down:** 5 minutes (to prevent rapid "flapping" of instances).
3. **Scale In Rule (Contraction):**
   - **Trigger:** Average CPU usage < **30%** for **10 minutes**.
   - **Action:** Decrease instance count by **20%**.
4. **Instance Limits (The Guardrails):**
   - **Minimum:** 2 (Ensures high availability even at low load).
   - **Maximum:** 10 (Prevents uncontrolled costs during an attack or spike).
   - **Default:** 2.
5. **Validation:**
   - Navigated to the **Instances** blade to observe the current state of the fleet under the new management policy.

---

## Evidence
> **[Paste screenshot here]**
> *Capture the 'Scaling' configuration page showing both the Scale out and Scale in rules, and the Instance limits section.*

---

## Professional Insight
- **Predictive vs. Reactive Scaling:** These rules are reactive—they respond to changes that have already occurred. In a mission-critical environment, I would combine these with **Scheduled Scaling** (e.g., increasing capacity 30 minutes before a planned launch event) for a proactive defense.
- **Percentage-Based Scaling:** Using "Increase percent by" rather than a fixed number (like +1) is a more sophisticated approach. As the fleet grows, the scaling speed increases proportionally, allowing the infrastructure to respond more aggressively to massive traffic spikes.
- **The Importance of Cool Down:** The 5-minute cool-down period is essential. It allows the new VM instances time to boot up and start taking load before the scaling engine evaluates the metrics again, preventing the system from over-provisioning resources.

---

## Task 5: Create a Virtual Machine using Azure PowerShell (Option 1)

In this optional but essential task, I moved from GUI-based management to Infrastructure as Code (IaC) principles using Azure PowerShell. I practiced provisioning, monitoring, and managing the lifecycle of a VM through the Cloud Shell environment.

---

### Implementation Steps
1. **Environment Setup:** Launched **Azure Cloud Shell** and ensured the session was set to **PowerShell** mode.
2. **Command-Line Provisioning:**
   - Executed the `New-AzVm` cmdlet with specific parameters:
     - **Name:** `myPSVM`
     - **Location:** `East US`
     - **Zone:** `1` (Ensuring zonal placement via CLI)
     - **Image:** `Win2019Datacenter`
3. **Status Verification:**
   - Used `Get-AzVM -ResourceGroupName 'az104-rg8' -Status` to confirm the VM was successfully provisioned and in the **Running** state.
4. **Lifecycle Management (Cost Optimization):**
   - Executed `Stop-AzVM` to shut down the instance.
   - Verified the status changed to **Deallocated**, confirming that compute charges have ceased and public IP resources are released.

---

## Evidence
> **[Paste screenshot here]**
> *Capture the Cloud Shell window showing the successful output of the Get-AzVM command with the status 'VM running' and then 'VM deallocated'.*

---

## Professional Insight
- **The Power of Scripting:** While the portal is great for learning, PowerShell allows for repeatable, error-free deployments. Mastering `New-AzVM` is the first step toward building automated scaling scripts and disaster recovery playbooks.
- **"Stopped" vs. "Deallocated":** This task highlighted a crucial billing concept. If you shut down a VM from *inside* the Guest OS, Azure may still charge for the compute power. By using `Stop-AzVM` (or the Portal's Stop button), the VM is **Deallocated**, ensuring the CPU/RAM resources are returned to the Azure pool and billing is paused.
- **Resource Group Context:** By targeting `az104-rg8`, this PowerShell-created VM exists alongside the Portal-created VMs, demonstrating that different management tools interact seamlessly with the same Azure Resource Manager (ARM) backend.

---

## Task 6: Create a Virtual Machine using the Azure CLI (Option 2)

In this final task, I used the **Azure CLI** in a Bash environment to provision a Linux virtual machine. This completed my mastery of Azure's primary management interfaces, demonstrating proficiency in command-line automation for multi-OS environments.

---

### Implementation Steps
1. **Environment Setup:** Switched the Azure Cloud Shell to **Bash** mode.
2. **Linux Provisioning via CLI:**
   - Executed the `az vm create` command to deploy an Ubuntu 22.04 LTS instance.
   - **Parameters used:**
     - `--image Canonical:0001-com-ubuntu-server-jammy:22_04-lts:latest`
     - `--generate-ssh-keys`: Automatically handled security by creating SSH key pairs for secure Linux access.
3. **Detail Verification:**
   - Used `az vm show` with the `--output table` flag to display resource details in a human-readable format.
   - **Result:** Confirmed `powerState` was `VM running`.
4. **Deallocation for Cost Control:**
   - Executed `az vm deallocate` to shut down the VM and release compute resources.
   - Verified the final state as `VM deallocated`.

---

## Evidence
> **[Paste screenshot here]**
> *Capture the Bash terminal showing the 'az vm show' output table with the 'VM deallocated' powerState.*

---

## Professional Insight
- **SSH Key Automation:** One of the best features of `az vm create` is `--generate-ssh-keys`. In a manual Linux setup, you would have to generate keys and upload them. The CLI handles this in one step, embodying the "Zero-Failure" efficiency required for rapid infrastructure scaling.
- **Output Flexibility:** The CLI’s `--output table` or `--output json` options are critical for automation. In a production script, I would use JSON output and `jq` to extract specific data, such as private IP addresses, for follow-up configuration tasks.
- **The "Deallocated" Standard:** Just like in PowerShell, using `az vm deallocate` is the professional way to stop a VM. It ensures that the VM is not just "off," but that its resources are truly freed up in the Azure fabric, stopping the billing clock for compute.
