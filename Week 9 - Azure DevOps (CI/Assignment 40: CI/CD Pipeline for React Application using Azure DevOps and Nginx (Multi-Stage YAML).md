# Assignment 40: **CI/CD Pipeline for React Application using Azure DevOps and Nginx (Multi-Stage YAML)**

## Objective

Build and deploy a real **React application** using a **multi-stage Azure DevOps pipeline** that covers the complete CI/CD lifecycle — from build and test to deployment on a Linux web server.

You will import the React app, configure the pipeline, and deploy the compiled build to your Ubuntu VM running Nginx via an SSH Service Connection.

**Reference repository:** [https://github.com/pravinmishraaws/my-react-app](https://github.com/pravinmishraaws/my-react-app)

---

## What You Will Build

* Azure DevOps pipeline with **Build → Test → Publish → Deploy** stages
* Automated deployment of React app to an **Ubuntu VM (AWS or Azure)**
* Nginx serving the React build from `/var/www/html`
* End-to-end CI/CD triggered automatically on commits to `main`

---

## Learning Outcomes

* Set up a **multi-stage pipeline** in Azure DevOps for a React project
* Understand why you deploy **artifacts (/build)** instead of raw source code
* Learn the difference between **Microsoft-hosted** and **self-hosted** agents
* Use **SSH tasks** for secure deployment to a web server
* Enable **true CI/CD** where commits to `main` trigger automatic deployment

---

## Prerequisites

Before starting this assignment, ensure you have completed:

* Assignment 38: Self-Hosted Agent setup (optional but recommended)
* Assignment 39: Static Website Deployment via SSH
* Azure DevOps Organization and Project
* New Ubuntu VM provisioned using **Terraform** (on AWS or Azure)
* Nginx installed and serving `/var/www/html` (using **Ansible**)
* SSH Service Connection configured with username and password

---

## Instructions

Follow the high-level workflow below.
Use the reference materials and video guidance to explore the detailed steps.

### Step 1 — Import the React App

Import the React repository into your Azure Repos:
[https://github.com/pravinmishraaws/my-react-app](https://github.com/pravinmishraaws/my-react-app)

**Reference Video:** [Automating React Application Deployment with Azure Pipelines](https://youtu.be/btrZ3X3NlSA)

Ensure the `package.json` and `src/` files are visible in your project.

---

### Step 2 — Prepare the Target VM

* New Ubuntu VM provisioned using **Terraform** (on AWS or Azure)
* Nginx installed and serving `/var/www/html` (using **Ansible**)
* SSH Service Connection configured with username and password

---

### Step 3 — Create or Update the SSH Service Connection

If not already configured, create or update your **SSH Service Connection** pointing to the VM.
Validate that the connection works successfully.

---

### Step 4 — Author a Multi-Stage Pipeline (YAML)

Create a new Azure Pipeline using YAML.
Define the following stages:

#### Stage 1 — Build

* Install Node.js
* Install dependencies (`npm install`)
* Build the React app (`npm run build`)

#### Stage 2 — Test

* Run unit tests (`npm test -- --watchAll=false`)
* Fail the stage if tests fail

#### Stage 3 — Publish

* Publish the `build/` directory as an **artifact** for later deployment

#### Stage 4 — Deploy

* Use an **SSH task** to copy the artifact (`/build` folder) to `/var/www/html` on the VM
* Optionally clear old content before copying
* Restart Nginx after deployment

**Example Sections (simplified):**

```yaml
trigger:
  branches:
    include:
      - main

stages:
  - stage: Build
    jobs:
      - job: build
        pool: SelfHostedPool
        steps:
          - checkout: self
          - task: NodeTool@0
            inputs:
              versionSpec: '18.x'
          - script: |
              npm install
              npm run build
            displayName: 'Build React App'
          - publish: build
            artifact: react_build

  - stage: Deploy
    dependsOn: Build
    jobs:
      - job: deploy
        pool: SelfHostedPool
        steps:
          - download: current
            artifact: react_build
          - task: CopyFilesOverSSH@0
            inputs:
              sshEndpoint: 'ubuntu-nginx-ssh'
              sourceFolder: '$(Pipeline.Workspace)/react_build'
              targetFolder: '/var/www/html'
          - task: SSH@0
            inputs:
              sshEndpoint: 'ubuntu-nginx-ssh'
              script: 'sudo systemctl restart nginx'
```

---

### Step 5 — Run and Verify

1. Commit changes to `main`.
2. Observe that the pipeline triggers automatically.
3. Validate that all stages complete successfully.
4. Access your React app at `http://<public_ip>` in a browser.
5. Confirm that the Nginx server serves the React application.

---

## Success Criteria

| Area            | Expected Result                         |
| --------------- | --------------------------------------- |
| Pipeline stages | Build, Test, Publish, Deploy            |
| Artifact        | Created and visible in pipeline summary |
| SSH deployment  | Files copied to `/var/www/html`         |
| Web app         | Accessible on port 80                   |
| Automation      | Commit to `main` triggers full pipeline |

---

## Submission Guidelines

**File name:**
`Assignment40_ReactApp_CICD_<YourName>.doc`

**Include screenshots of:**

1. Azure Repos showing imported React project
2. Azure Pipeline YAML definition (key sections visible)
3. Pipeline run summary showing all stages succeeded
4. Nginx folder content (`/var/www/html`) post-deployment
5. Browser showing the running React app

**Optional (Advanced):**
Add a manual approval gate between the `Test` and `Deploy` stages for controlled releases.

---

## Reflection (Comment on Video)

Under the reference video, write 4–6 lines describing:

* One insight you gained about artifact-based deployments
* How this pipeline could be extended for multi-environment deployments (Dev → Test → Prod)
