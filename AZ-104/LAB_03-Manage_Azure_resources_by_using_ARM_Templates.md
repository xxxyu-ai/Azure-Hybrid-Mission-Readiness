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
> **[Paste screenshot here]**
> *Capture the "Export template" screen in the Azure Portal, showing the JSON code for the az104-disk1 resource.*

---

## Professional Insight
- **IaC Foundations:** Exporting templates is the first step toward Infrastructure as Code. It allows us to move away from "Click-Ops" and toward version-controlled infrastructure that can be replicated across Dev, Test, and Prod environments.
- **Resource Consistency:** By using JSON templates, I can guarantee that every Managed Disk deployed for a specific project will have the exact same performance tier and encryption settings, eliminating human error during manual entry.
- **Blueprinting:** In a large-scale environment like MDA, these templates serve as the "blueprint" for complex systems, ensuring that disaster recovery environments can be stood up in minutes rather than hours.

---

## Task 2: Edit and Redeploy an ARM Template
In this task, I practiced the iterative nature of IaC by modifying an existing ARM template to deploy a second resource. This demonstrates how templates can be used to scale infrastructure rapidly and accurately.

### Implementation Steps
1. **Custom Deployment:** Used the "Deploy a custom template" service in the Azure Portal.
2. **Template Modification:** - Uploaded the previously exported `template.json`.
   - Edited the JSON variables to generalize the naming convention (changing specific resource names to a generic `disk_name` parameter).
   - Set the new disk name to `az104-disk2`.
3. **Parameter Alignment:** Updated `parameters.json` to match the structural changes made in the main template.
4. **Deployment:** Executed the deployment into the existing `az104-rg3` resource group.
5. **Verification:** Confirmed the successful creation of `az104-disk2` alongside the original disk.

---

### Evidence
> **[Paste screenshot here]**
> *Capture the "Resource Group > Deployments" blade showing both successful deployment history items, or the "Overview" blade showing both az104-disk1 and az104-disk2.*

---

### Professional Insight
- **Parameterization:** By changing hard-coded names to parameters like `disk_name`, I transformed a static script into a dynamic tool. This is a critical skill for creating production-ready templates that work across different environments.
- **Deployment History & Audit:** Reviewing the "Deployments" blade in the Resource Group provides a clear audit trail. In a professional setting, this allows team members to see exactly what was deployed, when, and with what parameters.
- **Efficiency:** The time required to deploy the second disk via a template was significantly less than the manual creation of the first disk, showcasing the reduction in administrative overhead.

---

## Task 3: Deploy a Template via Azure Cloud Shell (PowerShell)
In this task, I transitioned from the graphical interface to a command-line environment. I utilized Azure Cloud Shell and PowerShell to execute an ARM template deployment, demonstrating the ability to manage Azure resources through scripting and terminal-based tools.

### Implementation Steps
1. **Cloud Shell Configuration:**
   - Initialized Azure Cloud Shell with the **PowerShell** environment.
   - Configured the persistent storage account and file share (`fs-cloudshell`) required for the session.
2. **File Management:**
   - Uploaded the `template.json` and `parameters.json` files directly into the Cloud Shell storage.
   - Used the built-in code editor to modify the disk name to `az104-disk3`.
3. **Command-Line Deployment:**
   - Executed the deployment using the following cmdlet:
     `New-AzResourceGroupDeployment -ResourceGroupName az104-rg3 -TemplateFile template.json -TemplateParameterFile parameters.json`
4. **Verification:**
   - Verified the deployment status was `Succeeded`.
   - Used the command `Get-AzDisk` to confirm the presence of all three disks within the resource group.

---

### Evidence
> **[Paste screenshot here]**
> *Capture the Cloud Shell terminal window showing the successful execution of the 'New-AzResourceGroupDeployment' command and the subsequent 'Get-AzDisk' output list.*

---

### Professional Insight
- **Terminal-Based Efficiency:** Managing resources via Cloud Shell is significantly faster for repetitive tasks. It eliminates the need to navigate through multiple portal blades and allows for rapid, scriptable adjustments.
- **Universal Access:** Since Cloud Shell is browser-accessible and pre-configured with Azure modules, it provides a consistent management environment from any machine without needing local installations.
- **Hybrid Readiness:** Mastering PowerShell in Azure is directly applicable to managing hybrid environments (on-premises Windows Servers + Azure), aligning with my AZ-800/801 expertise.

---

## Task 4: Deploy a Template via Azure Cloud Shell (Azure CLI/Bash)
In this task, I explored the cross-platform capabilities of Azure by switching to the **Bash** environment. I utilized the Azure CLI to perform a template deployment, demonstrating versatility in using different command-line tools for infrastructure management.

### Implementation Steps
1. **Environment Switch:** Switched the Azure Cloud Shell environment from PowerShell to **Bash**.
2. **File Verification:** Confirmed that the `template.json` and `parameters.json` files were persisted in the Cloud Shell storage using the `ls` command.
3. **Template Editing:**
   - Opened the built-in editor and updated the disk name to `az104-disk4`.
4. **CLI-Based Deployment:**
   - Executed the deployment using the Azure CLI command:
     `az deployment group create --resource-group az104-rg3 --template-file template.json --parameters parameters.json`
5. **Verification:**
   - Monitored the JSON output to ensure the `provisioningState` was `Succeeded`.
   - Used the CLI command `az disk list --resource-group az104-rg3 --output table` to view the updated list of disks in a structured table format.

---

### Evidence
> **[Paste screenshot here]**
> *Capture the Bash terminal showing the 'az deployment group create' command output and the final 'az disk list' table displaying disk1 through disk4.*

---

### Professional Insight
- **CLI Versatility:** Azure CLI is idempotent and works identically across Windows, macOS, and Linux. Mastering `az` commands is essential for scripting in DevOps pipelines and managing multi-cloud environments.
- **Output Formatting:** The `--output table` flag is a powerful tool for administrators to quickly visualize resource status in a human-readable format, which is critical during real-time troubleshooting.
- **State Management:** By deploying multiple disks to the same resource group via different shells, I’ve demonstrated how Azure Resource Manager (ARM) acts as a centralized API that handles requests consistently, regardless of the tool used.

---

## Task 5: Deploy a Resource by Using Azure Bicep
In this final task, I utilized **Azure Bicep**, a domain-specific language (DSL) that uses declarative syntax to deploy Azure resources. Bicep offers a more concise and readable alternative to traditional JSON ARM templates, representing the next generation of Infrastructure as Code for Azure.

### Implementation Steps
1. **Environment Preparation:** Uploaded the `azuredeploydisk.bicep` file to the Azure Cloud Shell (Bash).
2. **Bicep Authoring:**
   - Used the Cloud Shell editor to modify the Bicep parameters:
     - **Disk Name:** `az104-disk5`
     - **Size:** `32 GiB`
     - **SKU:** `StandardSSD_LRS` (Standard SSD)
3. **Deployment:**
   - Executed the deployment using the same Azure CLI command used for JSON:
     `az deployment group create --resource-group az104-rg3 --template-file azuredeploydisk.bicep`
4. **Verification:**
   - Verified the creation of the fifth disk using `az disk list --output table`.
   - Confirmed that all 5 disks (disk1 to disk5) now exist within the `az104-rg3` resource group.

---

### Evidence
> **[Paste screenshot here]**
> *Capture the final 'az disk list' output showing all 5 disks. Highlighting az104-disk5 with its 'StandardSSD_LRS' SKU is key.*

---

### Professional Insight
- **Bicep vs. JSON:** Bicep is significantly easier to read and maintain. Its syntax is cleaner, reducing the risk of syntax errors compared to the verbose nesting found in JSON ARM templates.
- **Transpilation:** Behind the scenes, Azure Bicep transpiles into standard ARM template JSON. This means I can leverage the simplicity of Bicep while maintaining full compatibility with the Azure Resource Manager engine.
- **Advanced Automation:** Mastering Bicep is a prerequisite for sophisticated cloud architecture. It allows for modularity and better integration with version control systems, which is vital for maintaining "Zero-Failure" precision in infrastructure deployments.
