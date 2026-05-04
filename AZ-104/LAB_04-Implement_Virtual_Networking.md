# LAB 04: Implement Virtual Networking

## Lab Introduction
In this lab, I established the networking foundation for a growing global organization. I focused on designing scalable virtual networks (VNets), implementing subnetting strategies, and securing traffic using Network Security Groups (NSGs) and Application Security Groups (ASGs).

---

## Task 1: Create a Virtual Network with Subnets via the Azure Portal
In this task, I architected the `CoreServicesVnet` to accommodate significant future growth. This involved designing a large IP address space and segmenting it into functional subnets.

### Implementation Steps
1. **VNet Creation:** Defined the `CoreServicesVnet` in a new resource group `az104-rg4`.
2. **Address Space Design:** Configured a large `/16` network: `10.20.0.0/16`.
3. **Subnet Segmentation:** Created two dedicated subnets for organizational structure:
   - **SharedServicesSubnet:** `10.20.10.0/24` (For shared infrastructure).
   - **DatabaseSubnet:** `10.20.20.0/24` (For data tier isolation).
4. **Validation:** Confirmed that the VNet was provisioned correctly with no overlapping address spaces.
5. **Template Export:** Exported the ARM template for use in automating the next VNet deployment.

---

<img width="1381" height="788" alt="lab4 task1" src="https://github.com/user-attachments/assets/e1bf4c25-c282-452d-880e-567bec0830fa" />

---

## Professional Insight
- **Address Space Planning:** Using a `/16` for core services provides over 65,000 IP addresses, ensuring the infrastructure won't hit a "networking wall" as the organization scales.
- **Subnet Isolation:** By dividing services into subnets from the start, we prepare the environment for granular security controls (NSGs), ensuring that database traffic can be strictly isolated from shared services.
- **Reserved IPs:** In Azure, 5 IP addresses per subnet are reserved for management. I accounted for this in the planning to ensure sufficient capacity for host resources.
- **Avoiding Overlap:** As a best practice, I ensured these ranges do not overlap with other corporate networks, a critical requirement for future VNet Peering or VPN connections.

---

## Task 2: Create a Virtual Network and Subnets via ARM Template

In this task, I practiced the "reusability" aspect of Infrastructure as Code. By taking an exported template from a production-like environment (CoreServicesVnet) and performing a bulk-replace of key identifiers, I rapidly deployed a separate networking stack for manufacturing operations.

---

### Implementation Steps
1. **Template Customization:**
   - Used a text editor to perform a global search-and-replace on the exported `template.json`.
   - **VNet Identity:** Replaced `CoreServicesVnet` with `ManufacturingVnet`.
   - **IP Space:** Shifted the address space from `10.20.0.0/16` to `10.30.0.0/16`.
2. **Subnet Re-segmentation:**
   - Updated subnet names to `SensorSubnet1` and `SensorSubnet2`.
   - Assigned new CIDR blocks: `10.30.20.0/24` and `10.30.21.0/24`.
3. **Parameter Synchronization:** Updated the `parameters.json` file to align with the new VNet naming convention.
4. **Automated Deployment:** Deployed the modified template using the **Custom Deployment** feature in the Azure Portal.
5. **Verification:** Confirmed that the `ManufacturingVnet` was successfully isolated in `az104-rg4` with the intended IP configuration.

---

<img width="1534" height="853" alt="lab4 task2" src="https://github.com/user-attachments/assets/833bfcf5-13ab-47d4-9f5c-ec2d111af44c" />

---

## Professional Insight
- **The "Clone" Workflow:** This task simulates a high-pressure real-world scenario where an administrator must replicate a complex environment in a different region or for a different department.
- **CIDR Strategy:** By incrementing the second octet (10.**20**.x.x to 10.**30**.x.x), I maintained a clean, non-overlapping IP schema, which is a prerequisite for seamless VNet Peering in the future.
- **Efficiency:** Leveraging existing JSON artifacts reduces manual entry errors and ensures that organizational standards (like subnet naming conventions) are preserved across the enterprise.

---

## Task 3: Configure Communication between an ASG and an NSG

In this task, I implemented advanced network security controls by combining **Application Security Groups (ASG)** and **Network Security Groups (NSG)**. This approach allows for "Identity-based" security rules rather than relying solely on static IP addresses, which is essential for dynamic cloud environments.

---

### Implementation Steps
1. **ASG Creation:**
   - Created an Application Security Group named `asg-web`. 
   - *Purpose:* This acts as a logical container for web servers, allowing me to apply rules to multiple VMs simultaneously.
2. **NSG Deployment & Association:**
   - Deployed a Network Security Group named `myNSGSecure`.
   - Associated this NSG with the `SharedServicesSubnet` within `CoreServicesVnet`. This ensures all resources in that subnet are governed by the same security baseline.
3. **Inbound Rule Configuration (Allow ASG):**
   - Created a high-priority rule (`Priority: 100`) named `AllowASG`.
   - **Logic:** Allowed TCP traffic on ports `80` (HTTP) and `443` (HTTPS) specifically from the `asg-web` source.
4. **Outbound Rule Configuration (Deny Internet):**
   - Created a rule named `DenyInternetOutbound` with `Priority: 4096`.
   - **Logic:** Set the Destination to the `Internet` Service Tag and the Action to `Deny`. This overrides the default Azure rule that allows outbound internet access.

---

<img width="1390" height="804" alt="lab4 task3" src="https://github.com/user-attachments/assets/a263020d-46b0-49ab-ab9f-eabcb00e20e7" />

---

## Professional Insight
- **Identity-based Security:** By using an ASG as a source in the NSG rule, I've simplified management. If new web servers are added to the environment, I simply associate them with the `asg-web` group, and they automatically inherit the correct firewall rules without needing an IP change.
- **Micro-segmentation:** Associating the NSG at the subnet level provides a strong security boundary. The `DenyInternetOutbound` rule is a critical "Zero Trust" practice, preventing potential malware from communicating with external Command & Control (C2) servers.
- **Service Tags:** Using the `Internet` service tag instead of specific IP ranges demonstrates a modern approach to cloud security, allowing Azure to manage the vast and changing list of public IP addresses on my behalf.

---

## Task 4: Configure Public and Private Azure DNS Zones

In this final task of Lab 04, I implemented name resolution services using Azure DNS. I configured both a Public DNS zone for internet-facing resolution and a Private DNS zone for secure, internal resolution within the virtual network.

---

### Implementation Steps

#### 1. Public DNS Zone Configuration
- **Creation:** Deployed a public DNS zone for `contoso.com`.
- **Record Management:** Added an **A record** named `www` pointing to a placeholder IP address (`10.1.1.4`).
- **Validation:** - Identified the four authoritative Name Servers (NS) assigned by Azure.
  - Performed an `nslookup` via the command line to verify that `www.contoso.com` correctly resolves to the assigned IP using Azure's name servers.

#### 2. Private DNS Zone Configuration
- **Creation:** Deployed a Private DNS zone named `private.contoso.com`.
- **VNet Linking:** Established a **Virtual Network Link** named `manufacturing-link` to the `ManufacturingVnet`. This step is critical as it enables resources within that specific VNet to resolve names defined in this private zone.
- **Internal Records:** Added a record for `sensorvm` (`10.1.1.4`) to simulate internal name resolution for manufacturing devices.

---

<img width="1321" height="750" alt="lab4 task4" src="https://github.com/user-attachments/assets/656817ec-3150-406b-9762-e84a3681d535" />

---

## Professional Insight
- **Hybrid DNS Strategy:** Understanding the distinction between public and private zones is vital for security. Private DNS ensures that internal server names (like database or sensor endpoints) are never exposed to the public internet, reducing the attack surface.
- **VNet Integration:** The 'Virtual Network Link' is the bridge that makes Private DNS functional. Without this link, even if a record exists, the VMs in the network would be unable to find the private zone.
- **Global Availability:** Azure DNS leverages a global network of name servers, ensuring high availability and low-latency name resolution for public-facing applications.
