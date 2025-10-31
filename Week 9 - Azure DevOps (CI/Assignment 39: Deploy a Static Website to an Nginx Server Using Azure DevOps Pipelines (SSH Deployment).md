# Assignment 39: **Deploy a Website to an Nginx Server Using Azure DevOps Pipelines**

## Objective

Implement a complete **CI/CD pipeline** in Azure DevOps that deploys a website from **Azure Repos** to an **Ubuntu VM** running **Nginx**.
This exercise demonstrates end-to-end automation—from source control to live deployment—using Azure DevOps YAML pipelines.

**Reference video:** [Deploy Static Website to Azure VM using Azure DevOps (YouTube)](https://youtu.be/0GjRkyqTcTs)

---

## What You Will Build

* Azure Repos project containing the **[Azure-Static-Website](https://github.com/pravinmishraaws/Azure-Static-Website)** codebase
* Azure DevOps pipeline triggered on commits to `main`
* Secure **SSH Service Connection** (password-based) to your Ubuntu VM
* Deployed and accessible site served over HTTP (port 80)
* Pipeline logs verifying successful connection, file transfer, and deployment

---

## Instructions

Follow the high-level steps below. Use the reference video for detailed commands and examples.

### Step 1 — Import the Repository

Import the following repository into your Azure Repos:
[https://github.com/pravinmishraaws/Azure-Static-Website/tree/main](https://github.com/pravinmishraaws/Azure-Static-Website/tree/main)

Confirm that the code and `index.html` are visible under your project.

### Step 2 — Prepare the Target VM

Use Terraform to create Linux VM running in **any cloud** (AWS or Azure).
Use Ansible to ensure Nginx is installed and serving content from `/var/www/html`.
Keep port 80 open and verify that you can SSH into the instance using username and password.

### Step 3 — Create an SSH Service Connection

In Azure DevOps → Project Settings → Service Connections, create a new **SSH** connection:

* Name: `ubuntu-nginx-ssh`
* Host: Public IP of your VM
* Port: 22
* Username and Password (as configured on the VM)

Test the connection and ensure it validates successfully.

### Step 4 — Author the YAML Pipeline

Create a new pipeline in Azure DevOps using the YAML editor.
Select the imported repo and define a pipeline with the following components:

* **Trigger:** on `main` branch commits
* **Variables:** VM IP, deployment path (`/var/www/html`)
* **Pool:** use the self-hosted agent from Assignment 38 (preferred) or Microsoft hosted agent if available
* **Steps:**

  * Checkout code
  * Copy files to the target VM using the SSH task
  * Verify deployment via remote command (`ls /var/www/html`)

### Step 5 — Handle Common Issues

If pipeline fails due to parallelism limits or agent pool selection, fix by:

* Requesting parallelism in Organization Settings → Billing → Parallel Jobs
* Ensuring correct agent pool is referenced in the YAML (`pool: SelfHostedPool`)

### Step 6 — Verify Deployment

Access `http://<public_ip>` in your browser and confirm the static website is served successfully.
Validate that the files are present on the server in `/var/www/html`.

---

## Success Criteria

| Area               | Expected Result                      |
| ------------------ | ------------------------------------ |
| Repository         | Code imported to Azure Repos         |
| Service Connection | SSH connection validated             |
| Pipeline           | YAML pipeline runs successfully      |
| Deployment         | Static site accessible on port 80    |
| Logs               | SSH copy and remote commands succeed |

---

## Submission Guidelines

**File name:**
`Assignment39_StaticWebsite_CICD_<YourName>.doc`

**Include screenshots of:**

1. Repository imported into Azure Repos
2. SSH Service Connection configuration page
3. Pipeline YAML definition (open in editor)
4. Successful pipeline run (log summary)
5. Browser showing the deployed website

**Optional (Advanced):**
Add a post-deployment verification step (e.g., `curl http://localhost`) to confirm success within the pipeline.

---

## Reflection (Comment on Video)

Under the reference video, write 4–6 lines about:

* One issue you encountered with the pipeline or service connection and how you resolved it
* How this workflow compares to GitHub Actions or other CI/CD tools you’ve used
