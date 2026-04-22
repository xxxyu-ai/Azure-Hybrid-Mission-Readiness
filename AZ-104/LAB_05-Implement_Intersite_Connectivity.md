# LAB 05: Implement Intersite Connectivity

## Lab Introduction
In this lab, I explored advanced communication strategies between virtual networks. The focus was on implementing **Virtual Network Peering** to bridge segmented environments and configuring **User-Defined Routes (UDR)** to control traffic flow. This simulates a real-world enterprise environment where core services must securely interact with departmental resources.

---

## Task 1: Create a Core Services Virtual Machine and Virtual Network

In this task, I provisioned the central infrastructure component: a Windows Server VM acting as a core service provider. Unlike previous labs, I practiced "inline" resource creation by defining the virtual network directly during the VM setup process.

### Implementation Steps
1. **VM Configuration:**
   - **Name:** `CoreServicesVM`
   - **OS:** Windows Server 2025 Datacenter
   - **Size:** `Standard_D2s_v3`
   - **Security:** Set Public Inbound Ports to **None** to follow the principle of least privilege.
2. **Inline Networking:**
   - Created a new VNet named `CoreServicesVnet` during the VM creation wizard.
   - **Address Space:** `10.0.0.0/16`
   - **Subnet:** `CoreSubnet` (`10.0.0.0/24`)
3. **Optimized Deployment:** - Disabled Boot Diagnostics to accelerate the provisioning process for this lab environment.

---

## Evidence
> **[Paste screenshot here]**
> *Capture the 'Virtual machine' overview blade for CoreServicesVM, ensuring the Private IP address (10.0.0.4) and the associated VNet/Subnet are visible.*

---

## Professional Insight
- **Inline Resource Creation:** While creating a VNet during VM setup is fast for labs, in a production environment like MDA, I would typically pre-architect the network layers using Bicep/ARM to ensure strict adherence to naming conventions and IP schemas.
- **Security by Default:** By setting inbound ports to "None," I am ensuring the VM is not exposed to the public internet. Access will later be managed via internal networking or a Bastion host, aligning with Zero Trust security models.
- **Resource Group Management:** Placing all intersite connectivity resources into `az104-rg5` allows for clean lifecycle management and easy auditing of the connectivity project.

---

## Task 2: Create a Manufacturing Services Virtual Machine and Virtual Network

In this task, I provisioned a second isolated environment representing a different business unit (Manufacturing). By using a distinct Private IP address space, I established a scenario that requires explicit connectivity configuration (Peering) for inter-site communication.

---

### Implementation Steps
1. **VM Configuration:**
   - **Name:** `ManufacturingVM`
   - **OS:** Windows Server 2025 Datacenter
   - **Size:** `Standard_D2s_v3`
   - **Inbound Security:** Restricted all public inbound ports to ensure the internal network remains private.
2. **Networking Architecture:**
   - Designed a new VNet using a Class B private address space to avoid overlap with Core Services.
   - **VNet Name:** `ManufacturingVnet`
   - **Address Space:** `172.16.0.0/16`
   - **Subnet:** `ManufacturingSubnet` (`172.16.0.0/24`)
3. **Deployment Optimization:**
   - Disabled Boot Diagnostics to minimize resource overhead during the lab exercise.

---

## Evidence
> **[Paste screenshot here]**
> *Capture the 'Virtual machine' overview blade for ManufacturingVM, highlighting the Private IP address (172.16.0.4) and its association with ManufacturingVnet.*

---

## Professional Insight
- **IP Address Planning:** The choice of `172.16.0.0/16` is a deliberate architectural decision. In enterprise environments, ensuring that different departments use non-overlapping CIDR blocks is the first prerequisite for successful Virtual Network Peering or VPN integration.
- **Micro-segmentation:** By placing these VMs in entirely separate VNets, I have achieved the highest level of isolation. This prevents accidental data leakage between "Core IT" and "Manufacturing" until a secure, audited connection is established.
- **Resource Standardization:** Maintaining identical VM sizes (`Standard_D2s_v3`) and OS versions across departments simplifies patch management and ensures consistent performance during the connectivity testing phase.

---

## Task 3: Use Network Watcher to Test Connection (Baseline Test)

Before implementing any connectivity solutions, it is essential to establish a baseline. In this task, I used **Network Watcher** to diagnose the connectivity between `CoreServicesVM` and `ManufacturingVM`. As expected, the test confirmed that the two isolated networks could not communicate.

---

### Implementation Steps
1. **Tool Selection:** Accessed **Network Watcher** > **Connection troubleshoot**.
2. **Diagnostic Configuration:**
   - **Source:** `CoreServicesVM` (10.0.0.4)
   - **Destination:** `ManufacturingVM` (172.16.0.4)
   - **Target Port:** `3389` (RDP)
   - **Protocol:** TCP
3. **Execution:** Ran the diagnostic tests to evaluate path availability and security rules.
4. **Analysis of Results:** - **Status:** **Unreachable**
   - **Reason:** Since VNet Peering has not been established yet, there is no valid routing path between the `10.0.0.0/16` and `172.16.0.0/16` networks.

---

## Evidence
> **[Paste screenshot here]**
> *Capture the Connection Troubleshoot result screen showing the 'Unreachable' status and the red warning icons for the network path.*

---

## Professional Insight
- **Diagnostic-First Approach:** In a production environment like MDA Space, performing a baseline test before making changes is critical. It proves that a "failure" exists initially, allowing us to definitively verify that our subsequent fix (Task 4) was successful.
- **Network Watcher Utility:** This tool is more powerful than a simple `ping` command. It analyzes Network Security Groups (NSGs) and routing tables to identify exactly where a packet is being dropped.
- **RDP Port Testing:** Testing port `3389` is a standard way to check for basic connectivity between Windows nodes, even if the port is currently closed by an OS-level firewall; the network layer diagnostic will still show if the *path* itself is available.

---

## Task 4: Configure Virtual Network Peering

In this critical task, I established a bidirectional **Virtual Network Peering** between `CoreServicesVnet` and `ManufacturingVnet`. This allows resources in both networks to communicate privately using Azure's backbone network, bypassing the public internet and ensuring low-latency, high-bandwidth connectivity.

---

### Implementation Steps
1. **Peering Initiation:** Navigated to the `CoreServicesVnet` settings and initiated a new peering connection.
2. **Bidirectional Configuration:** Configured the peering links to ensure mutual communication:
   - **Link 1 (Core to Manufacturing):** Named `CoreServicesVnet-to-ManufacturingVnet`.
   - **Link 2 (Manufacturing to Core):** Named `ManufacturingVnet-to-CoreServicesVnet`.
3. **Traffic Settings:** - Enabled **Allow 'Virtual Network' to access 'Remote Virtual Network'** (Standard connectivity).
   - Enabled **Allow forwarded traffic**, preparing the network for transit scenarios where traffic might originate from outside the immediate peered network.
4. **Validation:**
   - Verified the **Peering status** in `CoreServicesVnet`.
   - Confirmed the reciprocal status in `ManufacturingVnet`. 
   - **Result:** Both links successfully reached the **Connected** state.

---

## Evidence
> **[Paste screenshot here]**
> *Capture the 'Peerings' blade from either VNet showing the link name and the 'Connected' Peering status. Seeing both link names in the portal is ideal.*

---

## Professional Insight
- **The Power of Peering:** VNet Peering is a non-transitive, high-performance connection. Unlike a VPN, it doesn't require an encryption gateway, meaning traffic stays within the Microsoft global network for maximum security and speed.
- **Reciprocal Links:** Azure simplifies the process by allowing us to create both sides of the peering link (Link 1 and Link 2) in a single operation. In a real-world multi-tenant scenario, you might only have permission for one side, requiring coordination with another admin.
- **Non-Overlapping Requirement:** The success of this task was dependent on the decisions made in Tasks 1 and 2 to use unique IP address spaces. If the CIDR blocks had overlapped, the peering would have failed—a common pitfall in enterprise IT planning.

---

## Task 5: Test Connectivity via Azure PowerShell (Post-Peering)

With VNet Peering established, I performed a verification test to confirm that the once-isolated networks can now communicate. This task utilized the **Run Command** feature, demonstrating how to execute diagnostics on a virtual machine without needing to establish a direct remote session.

---

### Implementation Steps
1. **Target Identification:** Recorded the private IP address of `CoreServicesVM` (`10.0.0.4`).
2. **Diagnostic Execution:** - Navigated to `ManufacturingVM` > **Operations** > **Run command**.
   - Selected **RunPowerShellScript** to execute a network probe from the manufacturing environment back to the core services.
3. **PowerShell Command:**
   - Executed the following cmdlet:
     `Test-NetConnection 10.0.0.4 -port 3389`
4. **Verification:**
   - Monitored the script execution until completion.
   - **Result:** `TcpTestSucceeded : True`
   - This confirmed that the network path across the peering link is fully operational for RDP traffic.

---

## Evidence
> **[Paste screenshot here]**
> *Capture the Run Command output window showing 'TcpTestSucceeded : True'. This is the definitive proof that the peering is working.*

---

## Professional Insight
- **Agent-based Diagnostics:** The "Run command" feature uses the Azure VM Agent. This is an invaluable tool for administrators to fix network or configuration issues when RDP access is broken or intentionally disabled for security.
- **Peering Validation:** Unlike Task 3 (which failed), this successful test proves that Azure's underlying fabric has updated the routing tables for both VNets, allowing packets to flow across the 10.x.x.x and 172.16.x.x boundaries.
- **Port-Specific Testing:** By testing port `3389`, I verified not just "connectivity" (ping), but specifically that the network is ready to handle management traffic, which is a key requirement for remote system administration.

---

## Task 6: Create a Custom User-Defined Route (UDR)

In this final task, I moved beyond standard routing by implementing a **User-Defined Route (UDR)**. This allows for precise control over traffic flow, specifically forcing traffic through a Virtual Network Appliance (NVA) for inspection or security filtering before it reaches the core services.

---

### Implementation Steps
1. **Network Segmentation:**
   - Created a new subnet named `perimeter` (`10.0.1.0/24`) within `CoreServicesVnet`. This serves as a "DMZ" or entry point for external traffic.
2. **Route Table Creation:**
   - Deployed a Route Table named `rt-CoreServices`.
   - Disabled **Gateway route propagation** to ensure total manual control over the routing logic.
3. **Custom Route Configuration:**
   - **Route Name:** `PerimetertoCore`
   - **Destination:** `10.0.0.0/16` (The entire Core Services VNet)
   - **Next Hop Type:** `Virtual appliance`
   - **Next Hop Address:** `10.0.1.7` (The designated internal IP for a future NVA/Firewall).
4. **Association:**
   - Linked the `rt-CoreServices` route table to the `perimeter` subnet. This ensures any packet entering the perimeter subnet and destined for the core network is redirected to the appliance first.

---

## Evidence
> **[Paste your screenshot here]**
> *Capture the 'Routes' blade of 'rt-CoreServices' showing the custom route, and the 'Subnets' blade showing the successful association with the 'perimeter' subnet.*

---

## Professional Insight
- **Traffic Interception:** By default, Azure routes traffic directly between subnets. Creating this UDR "breaks" that default behavior to enforce security. This is exactly how we ensure all incoming data is scrubbed by a firewall before touching sensitive systems.
- **NVA Readiness:** Even though the NVA (`10.0.1.7`) hasn't been fully configured yet, setting up the routing infrastructure first is a standard "Land and Expand" deployment strategy. 
- **User-Defined vs. System Routes:** This task demonstrates the power of UDRs to override System Routes. In the routing priority list, a User-Defined Route always takes precedence over the default system-generated paths, giving the architect full sovereignty over the network.
