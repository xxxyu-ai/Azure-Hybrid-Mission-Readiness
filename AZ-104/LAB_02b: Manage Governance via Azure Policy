# LAB 02: Manage Governance via Azure Policy

## Lab Introduction
This lab focuses on implementing organizational governance. I learned how to enforce operational standards using Azure Policy, utilize resource tagging for cost and project reporting, and protect critical resources using Resource Locks.

---

## Task 1: Create and Assign Tags via the Azure Portal
In this task, I implemented a tagging strategy at the Resource Group level. Tags are essential metadata that align Azure resources with business structures, such as Cost Centers or Projects.

### Implementation Steps
1. **Resource Group Creation:** Created a new resource group named `az104-rg2` in the `East US` region.
2. **Tag Assignment:** During the creation process, I assigned the following tag:
   - **Name:** `Cost Center`
   - **Value:** `000`
3. **Purpose:** This tag allows for granular cost tracking and ensures that all resources within this group can be billed to the correct department.

---

## Evidence
> **[Paste screenshot here]**
> *Capture the "Tags" tab during the resource group creation or the "Overview" blade of az104-rg2 showing the assigned tag.*

---

## Professional Insight
- **Governance Framework:** Following the Microsoft Cloud Adoption Framework (CAF), tagging is the first line of defense in resource management. Without consistent tags, cost management and auditing become impossible as the cloud footprint grows.
- **Lifecycle Management:** Using tags like `Cost Center` or `ProjectID` enables automated reporting and ensures that the finance department has clear visibility into cloud spending.
- **Standardization:** While manual tagging was performed here, the next steps (Azure Policy) will demonstrate how to automate this to eliminate human error.

---

## Task 2: Enforce Tagging via an Azure Policy
In this task, I implemented automated governance by assigning an Azure Policy. This ensures that any new resources created within a specific scope must comply with organizational tagging standards, or the deployment will be blocked.

### Implementation Steps
1. **Policy Selection:** Identified the built-in policy definition: `Require a tag and its value on resources`.
2. **Scope Assignment:** Assigned the policy to the `az104-rg2` Resource Group.
3. **Parameter Configuration:**
   - **Tag Name:** `Cost Center`
   - **Tag Value:** `000`
4. **Validation Test:** Attempted to create a **Storage Account** within `az104-rg2` without providing the required tags.
5. **Result:** The deployment failed during the "Validation" phase, as expected.

---

### Evidence
> **[Paste screenshot here]**
> *Capture the "Validation failed" message in the Storage Account creation blade, highlighting the "Raw Error" or the policy name that disallowed the deployment.*

---

### Professional Insight
- **Policy Enforcement:** Azure Policy is a powerful tool for maintaining compliance at scale. Unlike RBAC, which controls *who* can do something, Policy controls *what* can be done and *how* resources must be configured.
- **Proactive Governance:** By blocking non-compliant resources at the time of creation (Deny effect), I can prevent "Resource Drift" and ensure that the cloud environment remains organized without manual cleanup.
- **Validation in the CI/CD Pipeline:** Understanding how policy evaluation works is critical for troubleshooting deployment failures. In a real-world scenario, this prevents unallocated costs from being incurred by untagged resources.

---

## Task 3: Apply Tagging via an Azure Policy (Remediation)
In this task, I implemented a proactive governance strategy using the "Modify" effect. Instead of blocking deployments, this policy ensures that any resource created within the scope automatically inherits specific tags from its parent Resource Group.

### Implementation Steps
1. **Policy Selection:** Assigned the built-in policy: `Inherit a tag from the resource group if missing`.
2. **Configuration:**
   - **Scope:** `az104-rg2` Resource Group.
   - **Tag Name:** `Cost Center`
3. **Remediation & Managed Identity:** - Enabled a **Remediation Task** to handle non-compliant resources.
   - Azure automatically created a **Managed Identity** to grant the policy the necessary permissions to modify resource attributes.
4. **Validation Test:** Created a new **Storage Account** without manual tags.
5. **Result:** The deployment passed validation. Upon provisioning, the `Cost Center: 000` tag was automatically applied to the storage account.

---

### Evidence
> **[Paste screenshot here]**
> *Capture the "Tags" blade of the newly created Storage Account, showing the automatically inherited 'Cost Center: 000' tag.*

---

### Professional Insight
- **Automation vs. Friction:** While "Deny" policies (from Task 2) are great for enforcement, they can slow down development teams. "Modify" policies with remediation provide a seamless experience by automatically aligning resources with governance standards.
- **Managed Identity for Policy:** Understanding that policies with `Modify` or `DeployIfNotExists` effects require a Managed Identity is crucial. This identity provides the policy engine with the specific RBAC permissions needed to update resource tags.
- **Scalability:** Inheritance-based tagging ensures that even if a developer forgets to add metadata, the organization maintains its ability to track costs and ownership across thousands of resources.

---

## Task 4: Configure and Test Resource Locks
In this final task, I implemented Resource Locks to protect critical infrastructure. Locks ensure that even users with "Owner" or "Contributor" permissions cannot accidentally delete or modify essential resources.

### Implementation Steps
1. **Configuration:** Navigated to the `az104-rg2` Resource Group and accessed the **Locks** settings.
2. **Lock Creation:** Created a new lock with the following parameters:
   - **Lock name:** `rg-lock`
   - **Lock type:** `Delete` (Prevents deletion while allowing modifications).
3. **Verification Test:** Attempted to delete the entire Resource Group `az104-rg2`.
4. **Result:** The deletion request was denied by Azure. A notification confirmed that the resource is locked and cannot be deleted until the lock is removed.

---

### Evidence
> **[Paste screenshot here]**
> *Capture the notification error message that appeared when you attempted to delete the resource group, or the Locks blade showing 'rg-lock'.*

---

### Professional Insight
- **Safety Net for High-Privilege Users:** RBAC controls permissions, but Resource Locks control the resource itself. Even an "Owner" is blocked by a Delete lock, providing a critical safety net against accidental commands in the portal or CLI.
- **Lock Scoping & Inheritance:** Applying a lock at the Resource Group level ensures that all child resources (like the Storage Account created in Task 3) are also protected from deletion.
- **Operational Discipline:** In mission-critical environments (such as aerospace or defense infrastructure), "CanNotDelete" locks are standard practice for production environments to prevent catastrophic downtime.
