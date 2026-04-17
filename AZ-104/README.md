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

### Evidence
> **[<img width="1048" height="644" alt="image" src="https://github.com/user-attachments/assets/28062a02-923c-44c7-8344-5167940ffb7e" />
]**
> *Capture the "All Users" blade in the Azure Portal showing both az104-user1 and the invited guest.*

---

### Professional Insight
- **Identity Governance:** Accurately populating user attributes (Job Title, Department) is essential for automating access control via Dynamic Groups and ensuring organized resource management.
- **Security & Collaboration:** Utilizing Guest invitations (B2B) allows for secure collaboration with external partners while maintaining strict organizational security policies and separate identity lifecycles.
