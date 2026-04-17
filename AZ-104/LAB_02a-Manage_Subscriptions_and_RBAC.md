# LAB 02a: Manage Subscriptions and RBAC

## Lab Introduction
In this lab, I implemented Role-Based Access Control (RBAC) and Management Groups to govern Azure resources efficiently. I focused on structuring the environment for centralized management and delegating precise permissions following the principle of least privilege.

---

## Task 1: Implement Management Groups
Management Groups provide a level of scope above subscriptions. I created a management group to logically organize subscriptions and prepare for centralized RBAC and Policy inheritance.

### Implementation Steps
1. **Directory-level Access:** Verified "Access management for Azure resources" in Microsoft Entra ID properties to ensure the ability to manage the root hierarchy.
2. **Creation of Management Group:** - **Management Group ID:** `az104-mg1`
   - **Display Name:** `az104-mg1`

---

## Evidence
> **[Paste screenshot here]**
> *Capture the "Management groups" blade showing the newly created 'az104-mg1' under the Tenant Root Group.*

---

## Professional Insight
- **Hierarchical Governance:** Management groups are critical for large organizations (like MDA) that manage multiple subscriptions. By applying RBAC at the Management Group level, I can ensure that "Help Desk" staff have consistent support-request permissions across the entire organization without manual configuration on each subscription.
- **Inheritance & Compliance:** Utilizing the Root Management Group allows for "Global Policies" to be enforced directory-wide, ensuring that every new subscription automatically complies with corporate security standards.

- ---

## Task 2: Review and Assign a Built-in Azure Role
In this task, I assigned a built-in RBAC role at the Management Group scope. This demonstrates how to delegate specific administrative permissions to a group without granting full control over the subscription.

### Implementation Steps
1. **Target Scope:** Selected the `az104-mg1` management group to ensure role inheritance across all child subscriptions.
2. **Role Selection:** Selected the **Virtual Machine Contributor** role. 
   - *Key Characteristic:* Allows managing VMs but prevents access to the OS, virtual networks, or storage accounts.
3. **Assignment:** Assigned the role to the **`helpdesk`** security group.
4. **Verification:** Confirmed the assignment via the **Access control (IAM) > Role assignments** tab.

---

### Evidence
> **[Paste screenshot here]**
> *Capture the "Role assignments" tab showing the 'helpdesk' group listed with the 'Virtual Machine Contributor' role.*

---

### Professional Insight
- **Least Privilege Access:** By assigning "Virtual Machine Contributor" instead of "Contributor," I ensured the Help Desk has exactly the permissions needed for VM maintenance without compromising the security of the underlying network or storage infrastructure.
- **Group-Based RBAC:** As a best practice, I assigned the role to a **Group** rather than individual users. This simplifies administration; as staff join or leave the Help Desk, I only need to manage their group membership rather than updating multiple role assignments.
- **Scope Management:** Assigning roles at the **Management Group level** ensures that these permissions are automatically applied to any new subscriptions added to the group in the future, maintaining a consistent security posture.

- ---

## Task 3: Create a Custom RBAC Role
In this task, I designed and deployed a Custom RBAC Role. Custom roles are essential when built-in roles grant more permissions than required, allowing for a stricter implementation of the "Principle of Least Privilege."

### Implementation Steps
1. **Baseline Selection:** Cloned the built-in **Support Request Contributor** role as a starting point.
2. **Permission Customization (NotActions):**
   - **Requirement:** Prevent the Help Desk from registering Azure Resource Providers.
   - **Action:** Added `Microsoft.Support/register/action` to the **Exclude permissions (NotActions)** section. 
3. **Configuration:**
   - **Custom Role Name:** `Custom Support Request`
   - **Assignable Scopes:** Ensured the role is available at the `az104-mg1` Management Group scope.
4. **JSON Review:** Verified the underlying JSON structure to ensure `Actions`, `NotActions`, and `AssignableScopes` were correctly defined.

---

### Evidence
> **[Paste screenshot here]**
> *Capture the JSON view or the Basics tab of your "Custom Support Request" role, specifically showing the "NotActions" section.*

---

### Professional Insight
- **Precision Security:** Built-in roles are often too broad. By creating a custom role with `NotActions`, I can surgically remove high-privilege operations (like registering resource providers) while still allowing the user to perform their core job functions.
- **Scalability via Assignable Scopes:** By defining the `AssignableScopes` at the Management Group level, this custom role becomes available for use across all current and future subscriptions within that hierarchy, ensuring consistent permission modeling.
- **Resource Providers Control:** In a governed environment, allowing any user to register resource providers can lead to unexpected service availability or cost. Restricting this via a custom role is a proactive governance measure.

- ---

## Task 4: Monitor Role Assignments with the Activity Log
In this final task, I utilized the Azure Activity Log to audit the changes made during the lab. Monitoring administrative actions is a key component of security compliance and operational transparency.

### Implementation Steps
1. **Accessing Logs:** Navigated to the **Activity log** blade within the `az104-mg1` Management Group scope.
2. **Reviewing Events:** Analyzed the log entries to identify "Create role assignment" and "Create or update custom role definition" events.
3. **Filtering:** Verified that administrative operations can be filtered by time, user, and operation type for rapid incident response.

---

### Evidence
> **[Paste screenshot here]**
> *Capture the Activity Log screen showing the history of your role assignments or custom role creation.*

---

### Professional Insight
- **Auditability & Compliance:** The Activity Log provides an immutable record of "Who did what, and when." In a production environment, this is essential for troubleshooting unauthorized changes and meeting regulatory compliance requirements.
- **Proactive Monitoring:** While I reviewed the logs manually here, in a real-world enterprise scenario (like at MDA), I would recommend streaming these logs to a **Log Analytics Workspace** or **Microsoft Sentinel** to set up automated alerts for high-privilege role changes.
- **Scoped Visibility:** By viewing the logs at the Management Group level, I can gain a consolidated view of administrative actions across all child subscriptions, which is much more efficient than checking each subscription individually.
