# LAB 03: Manage Azure Resources by Using ARM Templates

## Lab Introduction
In this lab, I focused on automating resource deployments to ensure consistency and reduce administrative overhead. I explored Azure Resource Manager (ARM) templates and Bicep, learning how to deploy resources via the Portal, PowerShell, and CLI.

---

## Task 1: Create an Azure Resource Manager Template
The goal of this task was to deploy a resource manually and then "reverse-engineer" it into a reusable template. This is a common real-world workflow for digitizing existing infrastructure.

### Implementation Steps
1. **Resource Creation:** Deployed a Managed Disk named `az104-disk1` within a new resource group `az104-rg3`.
   - **Specs:** Standard HDD, 32 GiB, East US.
2. **Template Export:** Once the disk was provisioned, I navigated to the **Export template** blade.
3. **Analysis:** Reviewed the generated JSON structure, specifically identifying how the disk properties (size, SKU, location) are defined in the `resources` section.
4. **Artifacts:** Downloaded both the `template.json` and `parameters.json` files to my local environment for future customization.

---

## Evidence
> **[Paste your screenshot here]**
> *Capture the "Export template" screen in the Azure Portal, showing the JSON code for the az104-disk1 resource.*

---

## Professional Insight
- **IaC Foundations:** Exporting templates is the first step toward Infrastructure as Code. It allows us to move away from "Click-Ops" and toward version-controlled infrastructure that can be replicated across Dev, Test, and Prod environments.
- **Resource Consistency:** By using JSON templates, I can guarantee that every Managed Disk deployed for a specific project will have the exact same performance tier and encryption settings, eliminating human error during manual entry.
- **Blueprinting:** In a large-scale environment like MDA, these templates serve as the "blueprint" for complex systems, ensuring that disaster recovery environments can be stood up in minutes rather than hours.
