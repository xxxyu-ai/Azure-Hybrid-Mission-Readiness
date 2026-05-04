# LAB 09a: Implement Web Apps

## Task 1: Create and Configure an Azure Web App

In this task, I provisioned an Azure Web App to host a PHP-based application. By transitioning from an on-premises environment to a PaaS (Platform as a Service) solution, I have eliminated the overhead of managing underlying hardware and operating system maintenance.

---

### Implementation Steps
1. **Resource Group Creation:** Created `az104-rg9` to host the web application and its associated service plan.
2. **Web App Configuration:**
   - **Publish:** Selected **Code**.
   - **Runtime Stack:** Selected **PHP 8.2** to maintain compatibility with existing legacy code.
   - **Operating System:** Selected **Linux** for better performance and cost-efficiency.
3. **App Service Plan (ASP) Setup:**
   - **Region:** `East US`.
   - **Pricing Plan:** Selected **Premium V3 (P1V3)**. 
   - *Note:* This tier was specifically chosen to enable advanced features like deployment slots and autoscaling required in later tasks.
4. **Verification:** Successfully validated and deployed the resource, confirming the **Running** status from the Overview blade.

---

<img width="1404" height="811" alt="lab9a task1" src="https://github.com/user-attachments/assets/d6f8ea74-5ab4-463a-9c4e-f4efc9af3dc8" />

---

## Professional Insight
- **The Shift to PaaS:** Moving to Azure App Service allows the organization to focus strictly on application code rather than server patching. This "Zero-Failure" infrastructure management by Microsoft ensures higher availability for the company's websites.
- **Scaling Potential:** By starting with a Premium V3 plan, we are positioned to handle traffic spikes through horizontal scaling, which would have required significant lead time and capital expenditure in an on-premises data center.
- **Security & Maintenance:** Azure automatically handles the updates for the PHP runtime and the underlying Linux OS, significantly reducing the security attack surface compared to managing raw Windows Servers.

---

## Task 2: Create and Configure a Deployment Slot

In this task, I implemented **Deployment Slots**, which are live apps with their own hostnames. This allows for testing in a staged environment before swapping to production, ensuring a "Zero-Downtime" deployment workflow.

### Implementation Steps
1. **Slot Addition:** Navigated to the **Deployment slots** blade of the web app.
2. **Configuration:** Added a new slot named `staging`.
3. **Cloning:** Selected the option to **Clone settings** from the production web app to ensure environment parity.
4. **Status Check:** Confirmed both `production` and `staging` slots are in a **Running** state.

---

<img width="1389" height="834" alt="lab9a task2" src="https://github.com/user-attachments/assets/54166548-b2c3-4149-8bb1-9700bfe77e7e" />

---

## Task 3: Configure Web App Deployment Settings

I configured Continuous Deployment (CD) by integrating the Web App with a GitHub repository. This automates the delivery of code updates directly to the staging environment.

### Implementation Steps
1. **Source Control Integration:** Used the **Deployment Center** to link the App Service with the `php-docs-hello-world` GitHub repository.
2. **Deployment Execution:** Monitored the **Logs** until the build and deployment were successfully completed.
3. **Verification:** Accessed the staging slot URL to confirm the "Hello World" message is active.

---

<img width="1322" height="675" alt="lab9a task3" src="https://github.com/user-attachments/assets/00b2bc7a-e2b5-41a3-85e6-00b8bc0b2fc7" />

---

## Task 4: Swap Deployment Slots

In this task, I executed a **Slot Swap** to promote the verified "Hello World" application from the staging environment to the production environment. This process redirects production traffic to the new version of the app without any downtime.

---

### Implementation Steps
1. **Initiate Swap:**
   - Navigated to the **Deployment slots** blade of the web app.
   - Selected **Swap**, ensuring the Source was `staging` and the Target was `production`.
2. **Swap Execution:** - Reviewed the configuration changes and initiated the process. 
   - *Note:* Azure automatically warms up the staging instance before the swap to ensure there is no performance drop when it takes live traffic.
3. **Verification:**
   - Accessed the **Default domain** URL of the production web app.
   - **Result:** Confirmed that the production site now displays the "Hello World!" page, which was previously only available on the staging URL.

---

<img width="1319" height="780" alt="lab9a task4" src="https://github.com/user-attachments/assets/8f0916da-1614-4351-8097-bd64591d8a33" />

<img width="1123" height="378" alt="lab9a task4-1" src="https://github.com/user-attachments/assets/f177be76-25e3-4ccc-a24d-b10eb2d62a4e" />

---

## Professional Insight
- **Zero-Downtime Deployment:** Unlike traditional methods, where you might stop a service to overwrite files, the Swap operation swaps the Virtual IP (VIP) addresses or routing rules. The application remains "Always On."
- **Rollback Strategy:** If a bug had been discovered immediately after the swap, I could perform the swap again to return the previous production code (now in staging) back to the live URL. This provides a "Zero-Failure" safety net.
- **Warm-up Phase:** During the swap, Azure ensures the target slot is fully initialized. This is critical for PHP or .NET applications that require a "cold start" period to load libraries into memory.

---

## Task 5: Configure and Test Autoscaling of the Azure Web App

In this final task, I configured **Automatic Scaling** to ensure the application remains performant under varying traffic loads. I then utilized **Azure Load Testing** to simulate user traffic and verify that the App Service Plan responds effectively to increased demand.

---

### Implementation Steps
1. **Autoscale Configuration:**
   - Navigated to the **Scale out (App Service plan)** settings for the production slot.
   - Selected **Automatic** scaling mode.
   - Set the **Maximum burst** to **2**.
   - *Logic:* This allows Azure to automatically manage instance counts within defined limits, providing a balance between availability and cost control.
2. **Load Test Infrastructure:**
   - Deployed an **Azure Load Testing** resource via the "Diagnose and solve problems" blade.
   - Configured a new load test targeting the production **Default domain URL**.
3. **Execution and Monitoring:**
   - Initiated the load test with simulated virtual users.
   - Monitored real-time metrics including **Response time (ms)** and **Requests/sec**.
   - Observed how the App Service handles concurrent connections before stopping the test to prevent unnecessary resource consumption.

---

<img width="1334" height="784" alt="lab9a task5" src="https://github.com/user-attachments/assets/e5828951-6188-4d49-864a-40f3336c2eae" />

---

## Professional Insight
- **Automatic vs. Rules-Based Scaling:** While Rules-Based scaling (configured in Lab 08 for VMs) offers granular control, the **Automatic** mode in App Service is often preferred for Web Apps as it leverages built-in intelligence to handle rapid traffic spikes more smoothly.
- **Load Testing as Validation:** In a "Zero-Failure" environment like MDA, we never assume an autoscale rule works. We validate it. Azure Load Testing allows us to find the "breaking point" of our application architecture before our customers do.
- **Cost Guardrails:** Even in an automated environment, setting a **Maximum burst** limit is a critical financial safety measure. It prevents an unexpected bill if the application experiences a massive, sustained spike in traffic or a DDoS attempt.
