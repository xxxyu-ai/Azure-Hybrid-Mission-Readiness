# LAB 06: Implement Network Traffic Management

## Lab Introduction
In this lab, I focused on high availability and traffic distribution strategies. I implemented an **Azure Load Balancer** for Layer 4 (TCP/UDP) traffic management and an **Azure Application Gateway** for Layer 7 (HTTP/HTTPS) routing. These tools are essential for ensuring that public-facing applications remain resilient and performant under heavy load.

---

## Task 1: Provision Infrastructure via ARM Template

In this task, I deployed the foundational infrastructure required for load balancing tests. To ensure consistency and speed, I used an ARM template to provision a virtual network with multiple subnets and three separate virtual machines.

### Implementation Steps
1. **Template Deployment:** Utilized the **Custom Deployment** feature to upload the pre-configured `az104-06-vms-template.json` and `az104-06-vms-parameters.json`.
2. **Resource Configuration:**
   - **Resource Group:** `az104-rg6`
   - **Infrastructure Components:**
     - **VNet:** 1 Virtual Network.
     - **Subnets:** 3 dedicated subnets (one for each VM).
     - **Virtual Machines:** 3 VMs (`vm0`, `vm1`, `vm2`) to serve as backend pool members.
3. **Validation:** Confirmed that all resources passed validation and were successfully deployed within the same region (East US).

---

## Evidence
> **[Paste screenshot here]**
> *Capture the 'Resource Group' view for az104-rg6 showing the three virtual machines (vm0, vm1, vm2) and the virtual network successfully deployed.*

---

## Professional Insight
- **Infrastructure as Code (IaC) Efficiency:** Deploying three VMs and a multi-subnet VNet manually would be prone to human error. Using a template ensures that all backend nodes have identical configurations, which is a prerequisite for effective load balancing.
- **Subnet Segmentation:** By placing each VM in its own subnet, we simulate a robust architecture where different functional tiers can be isolated, while still being part of the same backend pool for the load balancer.
- **Operational Precision:** In a "Zero-Failure" environment, being able to rapidly tear down and rebuild this standardized infrastructure via templates is key to disaster recovery and consistent testing.

- ---

## Task 2: Configure an Azure Load Balancer (Layer 4)

In this task, I implemented an Azure Load Balancer to distribute incoming public traffic across two backend virtual machines. This setup ensures high availability and balances the workload at the transport layer (TCP/UDP).

---

### Implementation Steps
1. **Load Balancer Creation:**
   - **Name:** `az104-lb`
   - **SKU:** Standard (Required for high availability and static IP).
   - **Type:** Public (To accept internet-facing traffic).
2. **Frontend Configuration:**
   - Created a Frontend IP named `az104-fe`.
   - Provisioned a new Static Public IP address `az104-lbpip`.
3. **Backend Pool Management:**
   - Created a backend pool `az104-be`.
   - Added `vm0` and `vm1` to the pool using their Network Interface Cards (NICs).
4. **Health Probe & Load Balancing Rule:**
   - **Health Probe:** Configured `az104-hp` on TCP Port 80 to monitor the "pulse" of the backend servers.
   - **Load Balancing Rule:** Created `az104-lbrule` to forward traffic from port 80 (Frontend) to port 80 (Backend).
5. **Verification:**
   - Accessed the Load Balancer's public IP via a web browser.
   - Verified that traffic was successfully distributed, showing responses from both `vm0` and `vm1` upon refreshing.

---

## Evidence
> **[Paste screenshot here]**
> *Capture the "Load balancing rules" blade showing az104-lbrule, and a screenshot of the browser displaying 'Hello World' from either vm0 or vm1.*

---

## Professional Insight
- **L4 Load Balancing Logic:** By operating at Layer 4, the Load Balancer makes routing decisions based on IP addresses and ports. This is highly efficient for general traffic distribution where inspecting the content of the HTTP request is not required.
- **The Role of Health Probes:** The Load Balancer doesn't blindly send traffic; it constantly checks the health of `vm0` and `vm1`. If a server stops responding on Port 80, the probe detects the failure and the Load Balancer automatically stops sending traffic to that node, ensuring "Zero-Failure" service continuity.
- **Session Persistence:** During the task, I set Session Persistence to "None." This ensures that each new request can go to any available server, which is ideal for testing the distribution. In production, we might use "Client IP" to keep a user on the same server for the duration of their session.

- ---

## Task 3: Configure an Azure Application Gateway (Layer 7)

In this final task, I implemented an Azure Application Gateway to enable advanced HTTP load balancing. By configuring path-based routing rules, I demonstrated how to direct specific types of traffic (images vs. videos) to dedicated backend server pools, optimizing resource utilization and security.

---

### Implementation Steps
1. **Dedicated Subnet Provisioning:**
   - Created `subnet-appgw` (`10.60.3.224/27`) within `az104-06-vnet1`. Application Gateways require a dedicated subnet to manage their specialized infrastructure.
2. **Gateway Deployment:**
   - **Name:** `az104-appgw` (Tier: Standard V2).
   - **Frontend:** Assigned a new Public Static IP `az104-gwpip`.
3. **Multi-Backend Pool Configuration:**
   - **Default Pool:** `az104-appgwbe` (includes both vm1 and vm2).
   - **Image Pool:** `az104-imagebe` (specifically targeted `vm1`).
   - **Video Pool:** `az104-videobe` (specifically targeted `vm2`).
4. **Path-Based Routing Rules:**
   - Configured a listener on Port 80.
   - Implemented a routing rule `az104-gwrule` with path-based targets:
     - Traffic to `/image/*` → Redirected to `az104-imagebe`.
     - Traffic to `/video/*` → Redirected to `az104-videobe`.
5. **Verification:**
   - Monitored **Backend health** until both nodes displayed a "Healthy" status.
   - Tested URL paths in a browser:
     - `http://<IP>/image/` → Confirmed response from **vm1**.
     - `http://<IP>/video/` → Confirmed response from **vm2**.

---

## Evidence
> **[Paste screenshot here]**
> *Capture the 'Backend health' screen showing Healthy status for all pools, and the 'Configuration' blade showing the Path-based routing rules.*

---

## Professional Insight
- **L7 Logic & Path-Based Routing:** Unlike a standard Load Balancer, the Application Gateway inspects the URL. This allows for microservices-style architectures where different servers handle different content types, improving scalability.
- **WAF Readiness:** Although I deployed the Standard V2 tier, the same architecture can be upgraded to the WAF (Web Application Firewall) tier. This would provide centralized protection against common web vulnerabilities like SQL injection and cross-site scripting—a must-have for MDA's public-facing assets.
- **SSL Offloading:** In a production environment, Application Gateway can handle SSL termination. This relieves backend VMs from the CPU-intensive task of encrypting/decrypting traffic, allowing them to focus entirely on serving application data.
