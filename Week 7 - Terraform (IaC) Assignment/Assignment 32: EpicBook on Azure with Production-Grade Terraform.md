# Assignment 32: **EpicBook on Azure/AWS with Production-Grade Terraform**

## Objective

Build the **entire EpicBook stack on Azure or AWS (you decide)** using **production-grade Terraform patterns**:

* **Dynamic variables** (locals + maps; env-aware naming & settings)
* **Reusable modules** (network, database, compute/app, optionally nginx)
* **Workspaces** (`dev`, `prod`) from a **single codebase**
* **Remote backend** (state stored in Azure Storage *or* S3)
* **State file locking** (Azure Blob lease *or* AWS DynamoDB)
* **Two parallel environments** deployed from your machine: **dev** and **prod**

> Application reference: same as your earlier manual assignment (EpicBook on Azure VM + Azure Database for MySQL – Flexible Server with **private** VNet integration).

---

## Real-World Scenario

Your team is standardizing on **Terraform for repeatable environments**. Security requires **private DB**, **restricted NSGs**, and **remote, locked Terraform state** so ten engineers can work safely. You’ll structure code so **dev** and **prod** are identical but isolated, differing only by variables and workspaces.

---

## What You Must Build (Minimum)

1. **Networking (Module)**

   * VNet `10.0.0.0/16`
   * Subnets:

     * `public-subnet` `10.0.1.0/24` (VM)
     * `mysql-subnet` `10.0.2.0/24` (Flexible Server with subnet **delegation**)
   * NSGs:

     * Public: allow **22** from *your* IP, allow **80** from Internet, deny all else
     * Private: allow **3306** only from `10.0.1.0/24`
2. **Database (Module)**

   * **Azure Database for MySQL – Flexible Server**, **Private access (VNet integration)** in `mysql-subnet`
   * DB `epicbook`, admin creds via variables (never hardcode)
   * Ensure **Private DNS** is created/linked
3. **Compute/App (Module)**

   * **Ubuntu 22.04 LTS** VM (B1s) in `public-subnet` with Public IP
   * Provision **Node.js, npm, git, mysql-client, Nginx**
   * Deploy **EpicBook** (frontend build + backend service)
   * Nginx serves SPA and reverse-proxies `/api` to backend (localhost port)
4. **Terraform Platform Features**

   * **Dynamic variables**: drive names, tags, regions, sizes by env (maps/locals)
   * **Modules**: at least **network**, **database**, **compute** (separate subfolders); root calls them
   * **Workspaces**: `dev` & `prod` using the **same root**; no duplicate roots
   * **Remote backend** + **locking**:

     * **Option A (Azure recommended):** Backend **azurerm** → Storage Account + Blob Container; locking via blob lease (built-in)
     * **Option B (AWS):** Backend **s3** → S3 bucket + **DynamoDB** table for locking
   * **Apply twice**: deploy **dev** and **prod** independently; switching workspaces must **not** replace the other env

---

## Learning Goals

* Model a real stack as **modules** and **reuse** them across environments.
* Use **workspaces** to isolate state and deploy from a single codebase.
* Configure **remote state** & **locking** correctly; **prove** lock behavior.
* Manage **dynamic names/config** per environment without copy-pasting code.
* Validate a full **VM + Nginx + private DB** application path.

---

## Very High-Level Build Steps (you fill in details from the Registry)

1. **Plan your naming & inputs**

   * Decide naming convention including env (e.g., `${terraform.workspace}-epicbook-...`).
   * Decide locations per env (e.g., dev = East US, prod = West Europe).
2. **Backend setup**

   * Create backend resources (either Azure Storage or S3 + DynamoDB).
   * Configure `backend "azurerm"` **or** `backend "s3"` in `terraform {}` and run `terraform init -reconfigure`.
   * Ensure your identity has **Blob Data Contributor** (Azure) or S3 + DynamoDB permissions (AWS).
3. **Modules**

   * `modules/network`: VNet, subnets, delegations, NSGs (inputs: cidrs, names, tags)
   * `modules/database`: MySQL Flexible Server (private), DB name, admin (sensitive vars), DNS link
   * `modules/compute`: VM, extensions/provisioners or cloud-init to install Nginx/Node, pull repo, build frontend, run backend, Nginx config
4. **Root module**

   * Provider `azurerm` with `features {}` (and optional provider aliasing if needed)
   * Locals/maps to vary **location**, **name_prefix**, **allowed_ip**, **vm_size** by env
   * Call modules; pass env-aware inputs
   * Outputs: VM public IP, site URL, DB host FQDN
5. **Workspaces & deploy**

   * `terraform workspace new dev` → `init/plan/apply`
   * Publish happens via provisioners or cloud-init; confirm site loads
   * `terraform workspace new prod` → `init/plan/apply`
   * Switching between `dev` and `prod` must show **no cross-changes**
6. **Prove locking**

   * Open two terminals in the root:

     * Terminal A: run `terraform apply` and keep it open at the approval prompt
     * Terminal B: run `terraform plan` or `apply` → capture the **lock error/message**
7. **End-to-end test**

   * Open `http://<vm_public_ip>` → SPA loads
   * Confirm `/api` routes succeed (health/product list, etc.)
   * DB reachable only from VM (no public DB access), NSGs enforced

---

## What to Submit (Single Google doc)

**Name:** `Assignment32_EpicBook_Terraform_Capstone_<YourName>.pdf`

1. **Backend & Locking Evidence**

   * Screenshot of the **state object** in Azure Blob **or** S3, and (if AWS) the **DynamoDB** lock table
   * Terminal screenshots showing the **locking test** (Terminal A + Terminal B output)
2. **Infra Evidence**

   * Azure Portal: VNet & subnets view, NSGs with rules, MySQL Flexible Server showing **Private access / VNet**
   * VM Overview page + Public IP
3. **App Evidence**

   * Browser screenshot of the EpicBook SPA
   * `curl -i http://127.0.0.1:<backend-port>/api/health` (from VM) or any equivalent API call
4. **Reflection (5–10 lines)**

   * Biggest Terraform concept you consolidated (modules/workspaces/backends)
   * One improvement you’d make for production

