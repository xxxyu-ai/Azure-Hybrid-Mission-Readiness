# AZ-104: Microsoft Azure Administrator

## Lab 01: Manage Microsoft Entra ID Identities

### Task 1: Create and Configure User Accounts
In this task, I implemented the foundation of identity management by creating internal user accounts and inviting external guest users.

#### 1. Internal User Creation
- **User Principal Name:** `az104-user1`
- **Job Title:** `IT Lab Administrator`
- **Department:** `IT`
- **Usage Location:** `United States`

#### 2. External User Invitation
- Executed B2B collaboration by inviting an external email address as a Guest User.

---

<img width="1252" height="813" alt="lab1 t1" src="https://github.com/user-attachments/assets/717ba730-86f4-46c8-b252-b2bce388df31" />

---

### Professional Insight
- **Identity Governance:** Accurately populating user attributes (Job Title, Department) is essential for automating access control via Dynamic Groups and ensuring organized resource management.
- **Security & Collaboration:** Utilizing Guest invitations (B2B) allows for secure collaboration with external partners while maintaining strict organizational security policies and separate identity lifecycles.

---

### Task 2: Create Groups and Add Members
In this task, I implemented group-based management to streamline access control. I configured a security group and assigned both internal and guest members to it.

#### 1. Security Group Configuration
- **Group Type:** `Security`
- **Group Name:** `IT Lab Administrators`
- **Membership Type:** `Assigned` (Static assignment)
- **Description:** Administrators responsible for managing the IT lab environment.

#### 2. Ownership & Membership Assignment
- **Owner:** Assigned my account as the group owner to manage membership and lifecycle.
- **Members:** Added `az104-user1` and the `Guest User` to the group.

---

<img width="1740" height="878" alt="lab1 t2" src="https://github.com/user-attachments/assets/d5e6642e-d874-4862-a5dd-625ea8a85e30" />

---

### Professional Insight
- **Efficiency through Group Management:** Managing permissions at the group level, rather than per individual user, significantly reduces administrative overhead and minimizes the risk of inconsistent access rights.
- **Static vs. Dynamic Membership:** While this task used `Assigned` (static) membership, in a large-scale production environment (with Entra ID P1/P2 licenses), I would implement `Dynamic Groups` based on attributes like "Department" to automate the onboarding/offboarding process.
- **Operational Discipline:** Assigning an explicit "Owner" ensures accountability for the group's lifecycle, which is a critical practice for maintaining directory hygiene.
