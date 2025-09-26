# Assignment 30: **Team-Ready State—Remote Backends & Locking (Azure + AWS)**

## Objective

Configure **remote Terraform state** in **both Azure and AWS** and **prove** state **locking** prevents concurrent changes. You will:

* Set up a remote backend on **Azure** (Azure Storage)
* Set up a remote backend on **AWS** (S3 + DynamoDB for locking)
* Validate **locking** by deliberately attempting concurrent operations
* Document your findings like you would for a team runbook

> No code is provided. Your job is to read the **Terraform Registry** and cloud docs, then implement minimal configs that store state remotely and demonstrate locking.

---

## Real-World Scenario

Your team of 10 engineers is moving off local state to avoid “it works on my machine.” You must:

1. Stand up **team backends** (Azure + AWS) for different projects.
2. **Enforce locking** so two engineers can’t apply at the same time.
3. Provide a **short runbook** so anyone can onboard and use the backends safely.

You’ll use a **tiny, low-cost resource** per cloud (e.g., an Azure Resource Group; an AWS S3 bucket with basic settings) to keep the demo cheap and fast.

---

## Learning Goals

By the end, you should be able to:

1. Explain **why** remote state is required in teams (sharing, source of truth, drift prevention).
2. Configure **Terraform remote backends** for **Azure** and **AWS** (backend blocks + `init` with backend config).
3. Enable and **prove state locking** (Azure backend’s native locking; AWS S3 with **DynamoDB** lock table).
4. Run a **concurrency test** (two terminals or two shells) and interpret the behavior.

---

## Scope & Constraints

* Keep each cloud in a **separate root folder** (e.g., `azure/`, `aws/`).
* Minimal resources; focus is the **backend** and **locking**, not resource complexity.
* You **must** read these Registry pages (titles; you record exact URLs in submission):

  * **Backend: azurerm** (Terraform backend for Azure Storage)
  * **Backend: s3** (Terraform backend for AWS S3, and the `dynamodb_table` lock requirement)
  * **Provider: azurerm** and **Provider: aws**
  * One tiny resource per cloud (e.g., `azurerm_resource_group`, `aws_s3_bucket`)

---

## Very High-Level Steps (you fill in details)

### Part A — Azure Remote Backend (Azure Storage)

1. **Provision backend infra** (portal or CLI):

   * Resource Group (backend RG)
   * Storage Account (choose a globally unique name)
   * **Blob Container** (e.g., `tfstate`)
2. **Registry reading:** Find **Backend: azurerm** and note required fields (resource group, account, container, key; and auth options—CLI/env).
3. In a new folder `azure/`, write a tiny Terraform config that creates **one** innocuous resource (e.g., a Resource Group).
4. Configure the **azurerm backend** for this folder (you may pass sensitive bits via `-backend-config` during `init`).
5. `terraform init` → migrate (or create) state to Azure.
6. **Locking test (Azure):**

   * Open **two terminals** in the same `azure/` folder.
   * In Terminal A, run a long-ish operation (e.g., add a tag/change that takes a moment; start `terraform apply` and pause at prompt).
   * In Terminal B, run another `terraform apply` (or `plan` if your setup locks on plan) and **observe** the lock behavior/error.
   * Capture behavior and exact message.

### Part B — AWS Remote Backend (S3 + DynamoDB)

1. **Provision backend infra**:

   * S3 bucket for state (unique name; versioning recommended)
   * **DynamoDB table** for locking (partition key usually `LockID`, string)
2. **Registry reading:** Find **Backend: s3** and the section on **DynamoDB table for state locking**. Note required attributes.
3. In a new folder `aws/`, write a tiny Terraform config that creates **one** innocuous resource (e.g., a small S3 bucket).
4. Configure the **s3 backend** to point to your bucket and **lock table**.
5. `terraform init` → migrate (or create) state to S3.
6. **Locking test (AWS):**

   * Open **two terminals** in `aws/`.
   * Start `terraform apply` in Terminal A and keep it interactive/running.
   * Try `terraform apply` or `plan` in Terminal B and **observe** the DynamoDB lock behavior.
   * Capture the message: it should indicate the state is locked.

> Tip: If your operation is “too fast”, add a small manual pause (e.g., wait at the apply approval prompt) to give yourself time to attempt the second command.

---

## What “Done” Looks Like (Final Output)

* **Azure**: Remote backend in Azure Storage; `terraform plan` and `apply` succeed; a **locking attempt** from a second terminal is blocked (or clearly queued/fails with lock message).
* **AWS**: Remote backend in S3 + DynamoDB; `terraform plan/apply` succeed; a **locking attempt** from a second terminal is blocked with a DynamoDB lock message.
* **No local `terraform.tfstate`** in the working dirs; state stored remotely.
* A short **runbook** that a teammate could follow to replicate.

---

## Submission Guidelines (Single google doc)

Name: `Assignment30_RemoteState_Locking_<YourName>.pdf`

1. **Screenshots**

   * Azure Storage **container** showing the created state object (`*.tfstate`).
   * AWS S3 state object path and **DynamoDB lock table** view (table with at least one lock item created during your test).
   * Terminal screenshots of the **locking test** for each cloud:

     * Terminal A running `apply` (or holding at prompt)
     * Terminal B failing/queuing with a clear **lock** message

2. **Runbook (max 1 page)**

   * Step-by-step bullets: how to init, plan/apply, how locking behaves, and how to recover a **stale lock** (Azure note vs AWS DynamoDB manual delete guidance).
   * Include **cleanup** steps.

3. **Reflection (5–8 lines)**

   * What surprised you about backends vs providers?
   * How would you structure backend keys for **dev/test/prod**?
   * One pitfall you’d warn teammates about.

---

## Evaluation Criteria

* **Correct backend setup** in both clouds (state files stored remotely).
* **Locking demonstrated** with clear evidence from both clouds.
* **Clarity of runbook** (a teammate could use it tomorrow).
* **Proper references** to the Registry and documented auth choices.

This mirrors what real teams do: set up remote state, enforce locking, document the process, and make it reproducible for everyone.
