# LAB 07: Manage Azure Storage

## Lab Introduction
In this lab, I focused on implementing secure and cost-optimized storage solutions. I configured Azure Storage accounts with a focus on geo-redundancy, network security through firewall rules, and automated cost management using lifecycle management policies.

---

## Task 1: Create and Configure a Storage Account

In this task, I provisioned a high-availability storage account and implemented strict network access controls. I also configured automation to optimize storage costs by transitioning infrequently accessed data to a lower-priced tier.

### Implementation Steps
1. **Storage Account Provisioning:**
   - **Resource Group:** `az104-rg7`
   - **Redundancy:** Configured **Geo-redundant storage (GRS)** with read access enabled, ensuring data survives even in the event of a regional disaster.
2. **Network Hardening:**
   - Initially, **disabled** public network access during creation to enforce a private-first security posture.
   - Later, restricted access to **selected networks** and whitelisted my specific **client IPv4 address**, ensuring only my administrative machine can interact with the storage.
3. **Redundancy Verification:**
   - Confirmed primary and secondary data center locations in the Redundancy blade, providing insight into the physical location of the data replicas.
4. **Lifecycle Management:**
   - Created an automation rule named `Movetocool`.
   - **Policy:** Configured base blobs to automatically move to **Cool storage** if they have not been modified for **30 days**, effectively reducing storage costs for aging data.

---

## Evidence
> **[Paste screenshot here]**
> *Capture the 'Networking' blade showing your whitelisted IP address, and the 'Lifecycle Management' rule summary showing the 'Movetocool' policy.*

---

## Professional Insight
- **Cost vs. Performance:** Using Lifecycle Management is a "Zero-Failure" strategy for budget management. It ensures that the organization only pays for high-performance (Hot) storage when data is active, without requiring manual administrator intervention.
- **Defense in Depth:** Disabling broad public access and only allowing specific IP addresses is a critical security layer. In the context of MDA Space, this prevents unauthorized external parties from even reaching the storage endpoint.
- **Geo-Redundancy (GRS):** By enabling GRS, data is replicated to a paired region hundreds of miles away. This is essential for business continuity and disaster recovery (BCDR) planning, protecting against localized outages or natural disasters.

---

## Task 2: Create and Configure Secure Blob Storage

In this task, I implemented granular security and governance for unstructured data. I configured a blob container with an immutability policy to prevent accidental or malicious deletion and practiced secure data sharing using Shared Access Signatures (SAS).

---

### Implementation Steps
1. **Container & Immutability Policy:**
   - Created a container named `data` with the Public access level set to **Private**.
   - Configured a **Time-based retention policy** under the Immutable blob storage settings, set for **180 days**. 
   - *Result:* This ensures that uploaded blobs cannot be deleted or overwritten by anyone (including administrators) for the specified duration, fulfilling strict data retention requirements.
2. **Secure Data Upload:**
   - Uploaded a sample file to a virtual folder named `securitytest`.
   - Set the Access tier to **Hot**, optimizing for immediate data retrieval.
3. **Access Verification (Private):**
   - Attempted to access the file directly via its URL in an InPrivate window.
   - **Result:** Access was denied with a `PublicAccessNotPermitted` error, confirming that the container’s private setting was effective.
4. **Limited Access via SAS (Shared Access Signature):**
   - Generated a **SAS token and URL** with a specific "Read" permission and a limited 24-hour validity window.
   - Verified that the file could be successfully viewed using the **Blob SAS URL**, demonstrating how to grant secure, temporary access without exposing account keys.

---

## Evidence
> **[Paste screenshot here]**
> *Capture the 'Access policy' blade showing the 180-day retention policy, and a screenshot of the browser successfully displaying the file content via the SAS URL.*

---

## Professional Insight
- **WORM (Write Once, Read Many) Compliance:** The time-based retention policy I applied is a core component of "WORM" storage. This is vital for legal and regulatory compliance, ensuring that mission-critical logs or telemetry data remain untouched for their required lifecycle.
- **SAS vs. Account Keys:** In professional environments, we never share the primary storage account keys. Using SAS tokens allows us to adhere to the **Principle of Least Privilege**, giving a user access only to a specific file, for a specific purpose, and for a specific time.
- **Virtual Folders:** Although Blob storage is a flat structure, using a prefix like `securitytest/` allows us to organize data logically. This is essential when managing millions of files, as it enables easier searching and organizational mapping.

---

## Task 3: Create and Configure Secure Azure File Storage

In this final task, I implemented Azure Files to simulate a cloud-based file share environment. I also enhanced the storage security posture by utilizing **Virtual Network Service Endpoints**, restricting access so that the storage account is only reachable from a designated internal network.

---

### Implementation Steps
1. **File Share Creation:**
   - Provisioned a file share named `share1` using the **Transaction optimized** tier.
   - Disabled Azure Backup for this lab to focus on the core connectivity and security settings.
2. **Data Management via Storage Browser:**
   - Used the **Storage Browser** (an integrated GUI tool) to manage the file share hierarchy.
   - Successfully uploaded a test file directly through the portal, demonstrating the ease of management for administrators.
3. **Network Hardening via Service Endpoints:**
   - Created a new virtual network `vnet1`.
   - Enabled the **Microsoft.Storage** Service Endpoint on the default subnet. This optimizes the routing of traffic to Azure Storage over the Azure backbone network.
4. **Restricting Access to VNet:**
   - Modified the storage account's Firewall settings to **Add existing virtual network (vnet1)**.
   - Removed my client's IPv4 address from the whitelist.
   - **Result:** After the policy took effect, attempting to access the storage via the Storage Browser resulted in an "Authorization failure" message. This proved that the storage was now successfully isolated to only accept traffic from within `vnet1`.

---

## Evidence
> **[Paste screenshot here]**
> *Capture the 'Storage browser' error message showing the authorization failure after removing your IP, and the 'Networking' blade showing 'vnet1' as the only allowed network.*

---

## Professional Insight
- **Azure Files vs. Blob Storage:** While Blob storage is for developers and applications (unstructured data), Azure Files provides a true SMB/NFS interface. This makes it the primary choice for migrating legacy on-premises file shares to the cloud without changing user workflows.
- **Service Endpoints Security:** By using Service Endpoints, we ensure that the storage account's traffic never traverses the public internet. This significantly reduces the "Attack Surface" of the storage account, making it invisible to scanners on the public web.
- **Access Control Testing:** Receiving the "Not authorized" error in the portal is actually a "Success" in security testing. It validates that the firewall is working correctly and that only resources located inside `vnet1` (like a future VM) would be able to read or write to the file share.
