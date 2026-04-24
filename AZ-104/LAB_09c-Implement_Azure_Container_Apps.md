# LAB 09c: Implement Azure Container Apps

## Task 1: Create and Configure an Azure Container App and Environment

In this task, I deployed an **Azure Container App (ACA)**. While ACI is designed for simple, isolated containers, Container Apps provides a managed environment based on Kubernetes (AKS) without the operational complexity of managing a cluster. This allows for advanced features like auto-scaling to zero and built-in HTTP ingress.

---

### Implementation Steps
1. **Container Apps Environment:**
   - Created a new environment named `my-environment`.
   - *Note:* The Environment acts as a secure boundary for one or more container apps, sharing the same virtual network and logging configuration.
2. **Container App Provisioning:**
   - **Resource Group:** `az104-rg9`
   - **App Name:** `my-app`
   - **Region:** `East US`
3. **Container Configuration:**
   - **Image Source:** Quickstart image.
   - **Image:** Simple hello world container.
4. **Deployment:** Validated and initiated the creation of the serverless container infrastructure.

---

## Evidence
> **[Paste screenshot here]**
> *Capture the 'Overview' blade of your Container App. Ensure the 'Environment' name and 'Provisioning state: Succeeded' are visible.*

---

## Professional Insight
- **The "Serverless Kubernetes" Advantage:** Azure Container Apps abstracts away the Kubernetes Control Plane. As a system administrator, I can leverage KEDA (Kubernetes Event-driven Autoscaling) features through a simple UI or CLI without having to learn complex YAML manifest management.
- **Microservices Ready:** Unlike ACI, Container Apps are designed for microservices. They support **Dapr** (Distributed Application Runtime) and **Envoy** for traffic splitting, which is essential for "Zero-Failure" blue/green deployments.
- **Logical Grouping:** By using a Container Apps Environment, we consolidate monitoring and networking. For an organization moving away from on-premises VMs, this provides a structured yet flexible cloud landing zone for all containerized workloads.

---

## Task 2: Test and Verify Deployment of the Azure Container App

In this final task, I verified the accessibility of the newly deployed Azure Container App. Unlike ACI, which provides a basic FQDN, Container Apps offers a more robust application URL with built-in HTTPS, confirming the deployment is ready for live web traffic.

---

### Implementation Steps
1. **Application Access:**
   - Navigated to the **Overview** blade of the `my-app` container app.
   - Located and clicked the **Application URL** link (the unique DNS name assigned by Azure).
2. **Connectivity Verification:**
   - **Result:** Successfully reached the landing page displaying the message: **"Your Azure Container Apps app is live"**.
   - This confirms that the Ingress controller is correctly routing traffic to the container and the application is responding on Port 80.

---

## Evidence
> **[Paste screenshot here]**
> *Capture the browser window showing the "Your Azure Container Apps app is live" message with the unique Application URL visible in the address bar.*

---

## Professional Insight
- **Built-in HTTPS:** Notice that the Application URL automatically uses HTTPS. Azure Container Apps manages the SSL/TLS certificates for the default domain, a significant "Zero-Failure" advantage over managing custom certificates on hybrid Windows/Linux VMs.
- **Ingress Abstraction:** In a standard Kubernetes environment (AZ-800/801 hybrid scenarios), setting up an Ingress Controller and Load Balancer requires significant configuration. ACA handles this as a native service, allowing for rapid deployment.
- **Scalability Testing:** While not explicitly tested in this task, this URL is now backed by a scaling engine that can scale the container instances to zero when no traffic is detected, providing extreme cost efficiency for the organization.
