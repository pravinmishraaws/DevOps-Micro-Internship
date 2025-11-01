# Assignment 41: Automate the Book Review Application with Terraform, Ansible, and Azure DevOps

## Objective

Reproduce the end-to-end automation shown in the reference session, using two repositories and two Azure DevOps pipelines to deploy the Book Review App:

1. **Infra repository (Terraform):** provisions the infrastructure (resource group/VNet/2 VMs/MySQL).
2. **App repository (Book Review App + Ansible):** configures frontend and backend VMs and deploys the application.

You must follow the flow demonstrated in the video and implement the same handoff between infra and app pipelines.

**Reference video (must watch):**
[https://youtu.be/mmQ4PzZo_Xw](https://youtu.be/mmQ4PzZo_Xw)

**Application repo to deploy:**
[https://github.com/pravinmishraaws/book-review-app](https://github.com/pravinmishraaws/book-review-app)

---

## What You Will Build

**Repositories**

1. `book-review-infra`

   * Terraform code to provision:

     * 1 frontend VM (Ubuntu)
     * 1 backend VM (Ubuntu)
     * VNet / subnet
     * MySQL (Azure MySQL or VM-based; follow the video)
   
   * Terraform outputs:

     * `frontend_public_ip`
     * `backend_public_ip`
     * `mysql_fqdn` (or endpoint)

2. `book-review-app`

   * App source code (frontend + backend)
   * Ansible playbooks to:

     * configure common packages
     * configure backend (API, DB connection)
     * configure frontend (points to backend)
     * restart services / Nginx

**Pipelines**

1. **Infra pipeline (Azure Pipelines):**

   * Source: `book-review-infra` (in GitHub or Azure Repos)
   * Install Terraform
   * Authenticate using Azure Resource Manager service connection (SPN)
   * `terraform init/plan/apply`
   * Print/log Terraform outputs (frontend IP, backend IP, DB FQDN)
   * This is the “platform / infra” pipeline

2. **App pipeline (Azure Pipelines):**

   * Source: `book-review-app` repo
   * Install Ansible
   * Download SSH private key from Secure Files
   * Update Ansible inventory and vars manually with values from the infra pipeline
   * Run Ansible playbook to configure both VMs and deploy the app
   * This is the “application / Dev” pipeline

---

## Cloud Note

Primary flow in the video is on **Azure**.
For students comfortable with AWS, you may provision equivalent resources (VPC, 2 EC2 Ubuntu instances, RDS/MySQL) with Terraform and keep the same two-pipeline pattern.
However, Azure is recommended for this assignment to stay aligned with the video and service connection steps.

---

## Prerequisites

* Azure subscription and Azure DevOps organization/project
* Service Principal / App Registration (Client ID, Client Secret, Tenant ID, Subscription ID)
* Two repos available in Azure DevOps or GitHub:
  * `book-review-infra`
  * `book-review-app`
* SSH key pair available locally
* SSH private/public keys uploaded to Azure DevOps → Pipelines → Library → Secure files (you will download them in pipelines)
* Self-hosted agent running (recommended) OR working Microsoft-hosted agent with parallelism

---

## High-Level Steps

### Part 1: Infra Repository and Pipeline

1. Clone/import `book-review-infra` and place the Terraform code as shown in the video (modules for network, compute, database; separate env folders).
2. Create an **Azure Resource Manager** service connection using the SPN/App Registration (same as in the video).
3. Create an Azure Pipeline (YAML) in Azure DevOps for this repo:

   * Use self-hosted agent (preferred)
   * Install Terraform
   * Download SSH public key from Secure Files and place it where Terraform expects it
   * Run `terraform init`, `terraform plan`, and `terraform apply`
4. Verify in Azure Portal (or AWS console) that both VMs and MySQL got created.
5. Capture the Terraform outputs (frontend IP, backend IP, DB FQDN). These will be used in the application repo.

### Part 2: Manual Handoff

1. From the pipeline logs of the Infra pipeline, copy:
   * Frontend public IP
   * Backend public IP
   * MySQL FQDN / endpoint
2. Open the `book-review-app` repo
3. Update the following files (names may differ depending on your structure; follow the video):

   * `ansible/inventory.ini` → set frontend and backend hosts to the new IPs
   * `ansible/group_vars/backend.yml` (or similar) → set DB host / FQDN
   * `ansible/group_vars/frontend.yml` (or similar) → set backend API URL
4. Commit and push these changes to the app repo.

### Part 3: App Repository and Pipeline

1. In Azure DevOps, create a second pipeline, this time pointing to the **book-review-app** repo (video shows GitHub integration; follow that flow).
2. In the pipeline:

   * Install Ansible
   * Download SSH private key from Secure Files
   * Set correct permissions on the key
   * Run the main Ansible playbook that:

     * runs common role on both VMs
     * configures backend VM (app + DB connection)
     * configures frontend VM (Nginx + UI)
3. Run the pipeline and ensure all Ansible tasks succeed.

### Part 4: Validate CI/CD

1. Open browser with the **frontend VM public IP** and confirm the Book Review App loads.
2. Register a user and submit a review to confirm backend and DB are correctly wired.
3. Make a small change in the app repo (for example, in README or a UI text), push to `main`, and confirm that Azure DevOps triggers the app pipeline automatically.

---

## Success Criteria

| Area             | Expected Result                                              |
| ---------------- | ------------------------------------------------------------ |
| Infra pipeline   | Runs successfully and provisions 2 VMs + MySQL               |
| Outputs          | Frontend IP, Backend IP, MySQL FQDN visible in pipeline logs |
| SSH secure files | Uploaded and successfully downloaded in pipeline             |
| App pipeline     | Runs Ansible and configures both frontend and backend        |
| Application      | Book Review App accessible via frontend IP in browser        |
| CI/CD            | New commit to app repo triggers pipeline and redeploys       |

---

## Submission Guidelines

**File name:**
`Assignment42_BookReview_CICD_<YourName>.doc`

**Include screenshots of:**

1. Azure DevOps service connection (ARM) created
2. Infra pipeline run showing successful `terraform apply` and outputs
3. Azure Portal (or AWS console) showing both VMs and the database resource
4. App pipeline run showing Ansible tasks executed on both hosts
5. Browser showing working Book Review App (logged in or review added)

**Also include (text or screenshot):**

* The three values you carried over manually (frontend IP, backend IP, MySQL FQDN)
* The Ansible inventory snippet after you updated it

---

## Reflection (to be posted under the video)

Watch: [https://youtu.be/mmQ4PzZo_Xw](https://youtu.be/mmQ4PzZo_Xw)
Post 4–6 lines covering:

* What part took the most time: infra pipeline or app pipeline
* How you would automate passing Terraform outputs to Ansible in the next iteration

