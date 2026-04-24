# LAB 10: Implement Data Protection

## Task 1: Use a Template to Provision an Infrastructure

In this task, I practiced Infrastructure as Code (IaC) by deploying a virtual machine and network using an Azure Resource Manager (ARM) template. This established a standardized environment to test backup and recovery scenarios, ensuring that the infrastructure is consistent and repeatable.

---

### Implementation Steps
1. **Template Preparation:**
   - Accessed the **Deploy a custom template** blade in the Azure portal.
   - Loaded the specialized JSON template file (`az104-10-vms-edge-template.json`) and the associated parameters file.
2. **Infrastructure Configuration:**
   - **Resource Group:** `az104-rg-region1`
   - **Region:** `East US`
   - **Credentials:** Configured `localadmin` with a secure password.
3. **Deployment Execution:**
   - Initiated the custom deployment to provision the virtual network and the virtual machine.
4. **Verification:**
   - Confirmed the successful creation of the virtual machine, which will serve as the primary target for the subsequent backup and replication tasks.

---

## Evidence
> **[Paste your screenshot here]**
> *Capture the 'Template deployment' success page or the 'Overview' blade of the newly created virtual machine in the az104-rg-region1 resource group.*

---

## Professional Insight
- **The Value of IaC:** Manually creating VMs for testing is prone to human error. Using ARM templates ensures a "Zero-Failure" setup where the networking, disk, and VM SKUs exactly match the required testing specifications every time.
- **Hybrid Infrastructure Alignment (AZ-800/801):** In a hybrid environment, using templates allows for parity between on-premises virtualized environments and Azure. This consistency is vital when planning for cross-site recovery and data protection.
- **Parameter Separation:** By separating the `parameters.json` from the main template, I demonstrated the ability to deploy the same infrastructure to different regions or environments (Dev/Test/Prod) just by swapping the parameter file.

---

## Task 2: Create and Configure a Recovery Services Vault

In this task, I established a **Recovery Services Vault (RSV)**, which serves as the centralized storage and management interface for backup and disaster recovery data. This vault is a critical component for ensuring data durability and meeting compliance requirements.

---

### Implementation Steps
1. **Vault Provisioning:**
   - **Name:** `az104-rsv-region1`
   - **Resource Group:** `az104-rg-region1`
   - **Region:** `East US` (Aligned with the source VM for optimal performance and regional compliance).
2. **Storage Replication Settings:**
   - Verified the **Storage replication type** is set to **Geo-redundant (GRS)**.
   - *Logic:* GRS replicates data to a secondary paired region (West US), ensuring availability even if the primary East US region experiences a complete outage.
3. **Security Configuration (Soft Delete):**
   - Confirmed that **Soft Delete** is enabled with a **14-day retention period**.
   - *Logic:* This prevents accidental or malicious deletion of backup data, allowing for recovery even after a delete command is issued.
4. **Cross Region Restore (CRR):** Verified the availability of CRR, enabling the restoration of data in the paired region for advanced disaster recovery scenarios.

---

## Evidence
> **[Paste your screenshot here]**
> *Capture the 'Properties' blade of the Recovery Services Vault, highlighting the 'Backup Configuration' (Geo-redundant) and 'Security Settings' (Soft Delete enabled).*

---

## Professional Insight
- **Regional Alignment:** In Azure, you generally back up resources to a vault in the same region to minimize data transfer latency and costs. However, the *storage* of that vault should be Geo-redundant for true Disaster Recovery.
- **Protection Against Ransomware:** The **Soft Delete** feature is a vital defense against ransomware. Even if an attacker gains access to the portal and attempts to wipe your backups, the 14-day grace period provides a "Zero-Failure" safety net for the organization.
- **Storage Tiering:** RSVs automatically manage the underlying storage. For an administrator with AZ-800/801 experience, this is a significant relief compared to managing physical tape libraries or off-site disk rotations.

---

## Task 3: Configure Azure Virtual Machine-Level Backup

In this task, I implemented a virtual machine-level backup by defining a custom backup policy. This ensures that the infrastructure adheres to organizational data retention standards and provides a reliable recovery point in the event of data corruption or loss.

---

### Implementation Steps
1. **Backup Goal Definition:**
   - **Workload Location:** Azure
   - **Resource Type:** Virtual Machine
2. **Backup Policy Creation:**
   - **Policy Name:** `az104-backup`
   - **Frequency:** Daily at 12:00 AM.
   - **Instant Recovery:** Set to retain snapshots for **2 days**.
   - **Policy Subtype:** Standard (adequate for general-purpose VM protection).
3. **Target Selection:**
   - Assigned the `az104-10-vm0` virtual machine to the newly created policy.
4. **On-Demand Execution:**
   - Initiated an immediate backup via **Backup now** to create the initial recovery point without waiting for the scheduled midnight window.
5. **Validation:**
   - Monitored the **Backup Pre-Check** to ensure the VM is in a healthy state for data capture.

---

## Evidence
> **[Paste screenshot here]**
> *Capture the 'Backup Items' blade showing 'az104-10-vm0' with the 'Last Backup Status' as 'Warning (Initial backup pending)' or 'In progress'.*

---

## Professional Insight
- **Standard vs. Enhanced Policy:** While we used **Standard** for this lab, the **Enhanced** policy subtype is critical for high-end workloads, supporting multiple backups per day and increased disk limits.
- **Snapshot Retention:** The "Instant Recovery" feature stores snapshots locally on the disk for a few days, allowing for near-instant restoration without moving data from the vault. This is a "Zero-Failure" strategy for rapid recovery from accidental OS changes.
- **Application Consistency:** Azure Backup works with the VM agent to perform "Application-Consistent" backups. For an administrator with AZ-800/801 experience, this is the cloud equivalent of VSS (Volume Shadow Copy Service) integration on Windows Server.

---

## Task 4: Monitor Azure Backup

In this task, I implemented a centralized monitoring solution for backup operations. By configuring **Diagnostic Settings** to export vault logs and metrics to a dedicated Storage Account, I ensured that all backup and recovery events are archived for long-term auditing and operational analysis.

---

### Implementation Steps
1. **Monitoring Storage Provisioning:**
   - Created a globally unique **Storage Account** in the `East US` region to act as the repository for diagnostic data.
2. **Diagnostic Settings Configuration:**
   - Navigated to the **Monitoring > Diagnostic Settings** blade of the Recovery Services Vault.
   - Enabled log collection for critical categories:
     - **Azure Backup Reporting/Job/Alert Data**
     - **Azure Site Recovery Jobs/Events**
   - Configured the destination to **Archive to a storage account**, selecting the newly created storage resource.
3. **Job Verification:**
   - Monitored the **Backup jobs** blade to track the real-time status of the initial backup for `az104-10-vm0`.
   - Reviewed the job details to confirm data transfer size and operation status.

---

## Evidence
> **[Paste your screenshot here]**
> *Capture the 'Diagnostic settings' blade showing the selected log categories and the target storage account, as well as the 'Backup jobs' list showing the progress of your VM backup.*

---

## Professional Insight
- **The "Audit Trail" Requirement:** In high-compliance environments, keeping backup logs is mandatory. Archiving to a Storage Account provides a cost-effective way to store years of "Zero-Failure" evidence that backups were performed as scheduled.
- **Proactive Alerting:** By funneling "Alert Data" to a diagnostic setting, we can integrate with **Azure Monitor** to send automated emails or SMS if a backup fails. This shifts the administrator's role from "Manual Checking" to "Exception Management."
- **Hybrid Integration (AZ-800/801):** For those managing hybrid infrastructures, these logs can be consolidated with on-premises Event Viewer data using the **Log Analytics Agent**, providing a "Single Pane of Glass" view for all data protection tasks across the enterprise.

---

## Task 5: Enable Virtual Machine Replication (Disaster Recovery)

In this final task, I implemented **Azure Site Recovery (ASR)** to provide regional-level disaster recovery for the `az104-10-vm0` virtual machine. By replicating the VM from **East US** to **West US**, I ensured that the application can be failed over to a secondary region in the event of a catastrophic regional outage.

---

### Implementation Steps
1. **Secondary Vault Provisioning:**
   - Created a new Recovery Services Vault (`az104-rsv-region2`) in the **West US** region.
   - *Logic:* The DR vault must be located in the target region to manage the failover resources effectively.
2. **Disaster Recovery Configuration:**
   - Navigated to the **Disaster recovery** blade of the primary VM.
   - Set the **Target region** to **West US**.
3. **Advanced Automation:**
   - Configured an **Automation Account** to handle the operational tasks during replication and failover.
   - Azure automatically created the necessary target networking (VNet) and storage resources in the secondary region.
4. **Replication Initiation:**
   - Enabled replication and monitored the **Initial Synchronization**.
5. **Verification:**
   - Confirmed the status of the VM in the `az104-rsv-region2` vault under **Replicated items**.
   - **Result:** Verified the health is **Healthy** and the status transitioned from Synchronizing to **Protected**.

---

## Evidence
> **[Paste screenshot here]**
> *Capture the 'Replicated items' blade in the West US vault, showing the VM status as 'Protected'.*

---

## Professional Insight
- **RTO/RPO Management:** ASR provides near-zero data loss (Low RPO) and rapid recovery time (Low RTO). For a professional holding AZ-801, this represents the cloud-native evolution of **Hyper-V Replica** or **Storage Replica** across sites.
- **Automation is Key:** The use of an Automation Account ensures that the complex orchestration of spinning up a VM in a new region (attaching disks, IP mapping) is handled without human error during the high-pressure environment of a real disaster.
- **Regional Pairs:** East US and West US are "Azure Paired Regions." Using these specific pairs is a best practice, as Azure ensures that only one region in a pair is updated at a time, further reducing the risk of simultaneous downtime.
