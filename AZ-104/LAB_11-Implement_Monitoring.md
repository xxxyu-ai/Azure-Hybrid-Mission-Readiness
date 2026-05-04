# LAB 11: Implement Monitoring

## Task 1: Use a Template to Provision an Infrastructure

In this task, I deployed the necessary infrastructure for monitoring testing using an ARM template. Additionally, I enabled **VM Insights**, which integrates the Log Analytics agent and Dependency agent to provide deep visibility into the virtual machine's performance and processes.

---

### Implementation Steps
1. **Infrastructure Deployment:**
   - Deployed a Virtual Network and Virtual Machine (`az104-rg11`) using the `az104-11-vm-template.json` file.
   - Standardized the environment with a dedicated `localadmin` account for monitoring consistency.
2. **Azure Monitor Configuration:**
   - Navigated to **Monitor > VM Insights**.
   - Selected **Configure Insights** and enabled the feature for the newly deployed VM.
   - *Logic:* Enabling Insights automates the installation of the Azure Monitor Agent (AMA), allowing for the collection of detailed performance data and network connection maps.
3. **Verification:**
   - Confirmed the deployment of the VM and verified that the monitoring agent installation was initiated.

---

<img width="1670" height="770" alt="lab11 task1" src="https://github.com/user-attachments/assets/051fb768-a98c-4443-8908-250c16fbfe16" />

<img width="1385" height="754" alt="lab11 task1-1" src="https://github.com/user-attachments/assets/a69ff3d8-2f7a-43fa-9bfc-ab7afd3fe745" />

---

## Professional Insight
- **VM Insights & Observability:** While standard Azure metrics provide basic CPU/Disk usage, **VM Insights** delivers the "OS-level" telemetry required for deep troubleshooting. For an IT Pro with AZ-800/801 experience, this is the equivalent of combining Performance Monitor (PerfMon) with Network Monitor, but centralized in the cloud.
- **Infrastructure as Code (IaC):** Using ARM templates ensures that the monitoring environment is identical across different test runs, reducing "noise" in the data caused by manual configuration differences.
- **The Agent Transition:** Modern Azure Monitoring relies on the **Azure Monitor Agent (AMA)**. Ensuring this is correctly provisioned during the "Enable Insights" phase is a key step in building a reliable "Zero-Failure" monitoring pipeline.

---

## Task 2: Create an Alert Rule

In this task, I configured a proactive monitoring alert to detect the deletion of any virtual machine within the subscription. By leveraging **Activity Log signals**, Azure Monitor can trigger notifications the moment a critical infrastructure change occurs, ensuring high visibility and accountability.

---

### Implementation Steps
1. **Scope Definition:**
   - Set the alert scope to the entire **Subscription** level.
   - *Strategy:* Applying the rule at the subscription level ensures that even newly created VMs are automatically covered by this security policy without manual intervention.
2. **Signal Selection:**
   - Selected the **Delete Virtual Machine (Virtual Machines)** signal from the Activity Log.
   - *Insight:* This is an administrative signal that tracks the specific action of resource removal, rather than just performance metrics like CPU usage.
3. **Alert Logic Configuration:**
   - **Event Level:** All selected (Critical, Error, Warning, Informational).
   - **Status:** All selected (Started, Succeeded, Failed).
   - This ensures that even a *failed* attempt to delete a VM is logged and alerted, providing a complete audit trail of unauthorized actions.

---

<img width="1397" height="814" alt="lab11 task2" src="https://github.com/user-attachments/assets/b3c09850-1177-46e0-b952-bb57bcf8d789" />

---

## Professional Insight
- **Activity Log Alerts vs. Metric Alerts:** Unlike metric alerts (e.g., CPU > 80%), **Activity Log alerts** are essential for compliance and security auditing. They tell us "who did what" and "to what resource," which is vital for a "Zero-Failure" infrastructure strategy.
- **Resource Governance:** For an administrator managing hybrid workloads (AZ-800/801), this mirrors the auditing of Active Directory or Group Policy changes. In Azure, these signals are integrated natively, allowing for immediate response to configuration drift.
- **Scope Granularity:** While we used a broad subscription scope here, in a production environment, you might narrow this to a specific **Resource Group** containing mission-critical production servers to reduce "alert fatigue."

---

## Task 3: Configure Action Group Notifications

In this task, I established an **Action Group** to define the notification strategy for critical alerts. By linking the "VM Deletion" alert to an automated email notification, I ensured that the operations team is immediately informed of infrastructure changes, facilitating a rapid response.

---

### Implementation Steps
1. **Action Group Provisioning:**
   - **Name:** `Alert the operations team.`
   - **Display Name:** `AlertOpsTeam`
   - **Resource Group:** `az104-rg11`
2. **Notification Configuration:**
   - **Type:** Email/SMS message/Push/Voice.
   - **Protocol:** Selected **Email** and provided a dedicated administrative address.
   - **Verification:** Confirmed the "Join" notification email from Azure, validating the communication path between Azure Monitor and the external recipient.
3. **Alert Rule Finalization:**
   - Linked the new action group to the alert rule.
   - **Rule Name:** `VM was deleted`
   - **Description:** Provided a clear, actionable description for the operations team.
4. **Validation:** Successfully created and deployed the integrated monitoring and notification rule.

---

<img width="1374" height="773" alt="lab11 task3" src="https://github.com/user-attachments/assets/1743f02c-8be9-41ba-986c-bab0dbe71f55" />

---

## Professional Insight
- **The "Notification Fatigue" Balance:** While email is used in this lab, Azure Action Groups support advanced integrations like **Webhooks**, **Logic Apps**, and **Azure Functions**. For a "Zero-Failure" environment, these can trigger automated recovery scripts or post-incident reports in Microsoft Teams/Slack.
- **Display Names (AlertOpsTeam):** The 12-character display name is what appears in SMS or mobile push notifications. Choosing a concise, recognizable name is crucial for quick triage when an engineer receives an alert on the go.
- **Hybrid Operations (AZ-800/801):** In a hybrid setup, these action groups can be used to notify on-premises monitoring systems through IT Service Management (ITSM) connectors, bridging the gap between cloud events and local operations teams.

---

## Task 4: Trigger an Alert and Confirm it is Working

In this task, I performed a "Live Test" of the monitoring infrastructure by intentionally deleting the `az104-vm0` virtual machine. This verified that the end-to-end alerting pipeline—from activity detection to action group execution—is functioning correctly.

---

### Implementation Steps
1. **Triggering the Event:**
   - Initiated a **Force Delete** on the `az104-vm0` virtual machine.
   - Monitored the Azure Portal notifications to ensure the deletion operation was successfully recorded in the Activity Log.
2. **Notification Verification:**
   - **Result:** Received an automated alert email from `azure-noreply@microsoft.com` titled: *"Important notice: Azure Monitor alert VM was deleted was activated..."*.
   - This confirmed that the **Action Group** successfully processed the signal and dispatched the notification to the designated recipient.
3. **Alert Dashboard Review:**
   - Navigated to **Monitor > Alerts**.
   - Observed three verbose alert entries triggered by the deletion event (covering different stages of the deletion process).
   - Inspected the **Alert details** to review the specific timestamp, resource ID, and logic that triggered the rule.

---

<img width="1343" height="828" alt="lab11 task4" src="https://github.com/user-attachments/assets/b4af227c-931b-4cbd-bd0e-df3a85316b4b" />

---

## Professional Insight
- **The Lifecycle of an Alert:** An alert typically goes through three states: *New, Acknowledged, and Closed*. In a professional setting, an administrator would "Acknowledge" the alert to let the team know the issue is being investigated, preventing duplicate efforts.
- **Verbose Alerting:** Azure generated three alerts for a single deletion. This provides a granular view (e.g., *Started, Succeeded*), which is essential for determining exactly when a failure occurred during an automated process.
- **"Zero-Failure" Verification:** By confirming the receipt of the email, I verified that there are no "silent failures" in the monitoring chain. In organizations like MDA, a monitoring system that fails to alert is as dangerous as the system failure itself.

---

## Task 5: Configure an Alert Processing Rule (Maintenance Suppression)

In this task, I implemented an **Alert Processing Rule** designed to suppress notifications during a defined maintenance window. This ensures that the operations team is not overwhelmed by expected alerts during planned service activities, maintaining the integrity of the monitoring system.

---

### Implementation Steps
1. **Rule Definition:**
   - **Scope:** Applied at the **Subscription** level to cover all resources.
   - **Action:** Selected **Suppress notifications**.
2. **Scheduling (Overnight Maintenance):**
   - Configured a specific time window: **10:00 PM (Today) to 7:00 AM (Tomorrow)**.
   - Set the time zone to match the local operations center.
   - *Logic:* By scheduling suppression, we ensure that monitoring resumes automatically at the start of the next business day without manual intervention.
3. **Deployment Details:**
   - **Resource Group:** `az104-rg11`
   - **Rule Name:** `Planned Maintenance`
   - **Description:** Clearly labeled as "Suppress notifications during planned maintenance" for audit clarity.
4. **Validation:** Successfully deployed the rule to the alerting pipeline.

---

<img width="1378" height="792" alt="lab11 task5" src="https://github.com/user-attachments/assets/c6057300-7827-45e9-8be5-143c326ac466" />

---

## Professional Insight
- **Reducing "Alert Fatigue":** Frequent, expected notifications lead to engineers ignoring their inbox. Suppressing them during maintenance ensures that when an alert *does* fire, it is treated with the necessary urgency.
- **Precision Governance:** Alert processing rules don't stop the alert from being *recorded* in the portal; they only stop the *notification* (Email/SMS). This allows us to review what happened during maintenance later without being disturbed in the middle of the night.
- **Hybrid Scenario (AZ-800/801):** In a hybrid environment where Azure Monitor manages on-premises servers via Azure Arc, these rules are vital. They allow us to sync cloud suppression with local physical maintenance windows, providing a unified operational rhythm.

---

## Task 6: Use Azure Monitor Log Queries (KQL)

In this final task, I utilized **Log Analytics** to perform advanced data analysis using **Kusto Query Language (KQL)**. By querying telemetry captured from the virtual machine, I demonstrated the ability to transform raw log data into visual performance insights and operational health reports.

---

### Implementation Steps
1. **Query Environment Setup:**
   - Navigated to **Monitor > Logs** and set the scope to the subscription level.
   - Accessed the preconfigured **Virtual machines** query library.
2. **Heartbeat Analysis:**
   - Executed the **Count heartbeats** query.
   - *Result:* Verified the connectivity and "up-time" of the virtual machine by counting the heartbeat signals sent by the Azure Monitor Agent.
3. **Advanced Performance Visualization:**
   - Switched to **KQL Mode** and executed a custom time-chart query:
     ```kusto
     InsightsMetrics
     | where TimeGenerated > ago(1h)
     | where Name == "UtilizationPercentage"
     | summarize avg(Val) by bin(TimeGenerated, 5m), Computer
     | render timechart
     ```
   - *Result:* Generated a visual **Timechart** showing the average CPU utilization percentage in 5-minute intervals.
4. **Knowledge Expansion:**
   - Explored other built-in queries to understand the schema of tables like `Heartbeat` and `InsightsMetrics`.

---

<img width="1836" height="855" alt="lab11 task6" src="https://github.com/user-attachments/assets/3b337dc1-b5e5-423c-bed5-1af094482f1e" />

---

## Professional Insight
- **KQL: The Language of the Cloud:** Mastering KQL is essential for any modern Azure Administrator. It is used not only in Azure Monitor but also in **Microsoft Sentinel** (SIEM) and **Microsoft Defender for Cloud**, making it a versatile skill for both operations and security.
- **Proactive Scaling & Alerting:** The query I ran (UtilizationPercentage) is exactly the type of logic used to trigger **Autoscale** rules or custom performance alerts. If a query can visualize a problem, it can be used to automate a solution.
- **Hybrid Visibility (AZ-800/801):** For those managing hybrid environments, KQL allows you to run a single query that aggregates data from both on-premises servers (via Azure Arc) and Azure native VMs, providing a unified "Zero-Failure" view of the entire estate.
