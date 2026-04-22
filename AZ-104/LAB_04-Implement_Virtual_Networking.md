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

## Evidence
> **[Paste your screenshot here]**
> *Capture the "Subnets" blade of CoreServicesVnet showing the SharedServicesSubnet and DatabaseSubnet with their respective address ranges.*

---

## Professional Insight
- **Address Space Planning:** Using a `/16` for core services provides over 65,000 IP addresses, ensuring the infrastructure won't hit a "networking wall" as the organization scales.
- **Subnet Isolation:** By dividing services into subnets from the start, we prepare the environment for granular security controls (NSGs), ensuring that database traffic can be strictly isolated from shared services.
- **Reserved IPs:** In Azure, 5 IP addresses per subnet are reserved for management. I accounted for this in the planning to ensure sufficient capacity for host resources.
- **Avoiding Overlap:** As a best practice, I ensured these ranges do not overlap with other corporate networks, a critical requirement for future VNet Peering or VPN connections.
