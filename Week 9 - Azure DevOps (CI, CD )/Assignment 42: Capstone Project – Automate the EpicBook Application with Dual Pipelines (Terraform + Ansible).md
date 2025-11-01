# Assignment 42: **Capstone Project – Automate the EpicBook Application with Dual Pipelines (Terraform + Ansible)**

## Objective

In this capstone project, you will design and implement a **complete DevOps automation workflow** for the **EpicBook** application using **Azure DevOps Pipelines**, **Terraform**, and **Ansible**.

You will separate responsibilities across two repositories and two pipelines — one for infrastructure provisioning and one for application deployment — similar to how large teams work in production environments.

**Reference video:** [Capstone – EpicBook Automation using Azure DevOps (YouTube)](https://youtu.be/mmQ4PzZo_Xw)

---

## What You Will Build

### Repositories

1. **Infra Repository (Terraform)**

   * Provisions Azure resources:

     * Resource Group
     * Virtual Network and Subnets
     * Ubuntu VMs for Frontend and Backend
     * MySQL Database (PaaS or VM-based)

2. **App Repository (Ansible + Application Code)**

   * Configures the provisioned VMs
   * Installs Nginx and deploys the EpicBook application
   * Configures the backend connection to MySQL

### Pipelines

1. **Infra Pipeline (Azure Pipelines – Terraform)**

   * Install Terraform
   * Authenticate using **Azure Resource Manager Service Connection (SPN)**
   * Execute: `terraform init`, `plan`, `apply`
   * Produce outputs:

     * `app_public_ip`
     * `mysql_fqdn`

2. **App Pipeline (Azure Pipelines – Ansible)**

   * Install Ansible
   * Download SSH keys from **Secure Files**
   * Update inventory and variable files using Terraform outputs
   * Configure backend and frontend servers
   * Deploy the EpicBook application and verify through Nginx

---

## Learning Outcomes

* Apply a **two-repository model** (Infrastructure vs. Application) used in enterprise DevOps workflows
* Create and use **Azure Resource Manager (SPN)** service connections for Terraform pipelines
* Securely manage and access **SSH keys** using Azure DevOps Secure Files
* Trigger builds from **GitHub repositories** into Azure DevOps pipelines
* Handle manual **handoff of Terraform outputs** into the Ansible inventory, and understand how this can later be automated

---

## Prerequisites

* Azure subscription and active Azure DevOps organization/project
* App Registration in Azure AD (Client ID, Client Secret, Tenant ID, Subscription ID)
* Two repositories in Azure Repos or GitHub:

  1. `infra-epicbook` – Terraform code
  2. `theepicbook` – application + Ansible code
* SSH key pair for VM access (stored as Secure Files in Azure DevOps)

---

## Instructions

Follow the high-level workflow below. Use the reference video for detailed configuration and commands.

### Step 1 — Prepare Repositories

* **Infra repo:** Initialize Terraform configuration for network, compute, and database.
  Include outputs for VM IPs and database FQDN.
* **App repo:** Add Ansible playbooks and roles for common setup, Nginx, and EpicBook deployment.
  Create an inventory template that will later be updated with IPs from Terraform outputs.

---

### Step 2 — Create Azure Service Connection

Create an **Azure Resource Manager (SPN)** service connection in Azure DevOps using:

* Tenant ID
* Subscription ID
* Client ID
* Client Secret

This will allow Terraform to authenticate and provision Azure resources.

---

### Step 3 — Create Infra Pipeline

In Azure DevOps, create a YAML pipeline in the Infra repository with stages for:

1. Installing Terraform
2. Initializing backend and providers
3. Running `terraform plan` and `terraform apply`
4. Publishing key outputs (Application IP, MySQL FQDN)

Validate that resources are provisioned successfully in Azure.

---

### Step 4 — Create App Pipeline

In the App repository, define another Azure DevOps pipeline that:

1. Installs Ansible
2. Downloads SSH private key from Secure Files
3. Updates Ansible inventory and variable files with Terraform outputs (manual copy for now)
4. Configures backend (MySQL connection, application setup) and frontend (Nginx, app deployment)
5. Verifies Nginx is serving the EpicBook site

---

### Step 5 — Verify End-to-End Workflow

1. Confirm infrastructure creation in Azure (VMs, network, MySQL).
2. Confirm successful configuration and deployment via Ansible.
3. Access the EpicBook app via the frontend public IP in your browser.
4. Validate backend connectivity and database response.

---

## Success Criteria

| Area                   | Expected Result                                          |
| ---------------------- | -------------------------------------------------------- |
| Terraform pipeline     | Successfully creates infrastructure and outputs IPs/FQDN |
| ARM Service Connection | Authenticated and working                                |
| SSH keys               | Stored and accessed securely via Secure Files            |
| Ansible pipeline       | Executes playbooks successfully                          |
| Application            | EpicBook accessible from browser (port 80)               |

---

## Submission Guidelines

**File name:**
`Assignment41_EpicBook_Capstone_<YourName>.doc`

**Include screenshots of:**

1. Infra pipeline run showing `terraform apply` completion and outputs
2. Azure portal confirming provisioned resources
3. App pipeline run summary
4. Ansible playbook output showing successful configuration
5. Browser displaying the running EpicBook application

**Optional (Advanced):**
Automate the flow of Terraform outputs into Ansible inventory using pipeline variables or artifact sharing.

---

## Reflection (Comment on Video)

Under the reference video, write 5–6 lines discussing:

* How separating Infra and App pipelines improves scalability and team collaboration
* One improvement you would make to automate the handoff between Terraform and Ansible
