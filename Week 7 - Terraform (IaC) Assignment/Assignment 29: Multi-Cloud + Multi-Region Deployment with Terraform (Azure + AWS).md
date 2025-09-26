# Assignment 29: **Multi-Cloud + Multi-Region Deployment with Terraform (Azure + AWS)**

## Objective

Build confidence using the **Terraform Registry** to author configurations from scratch. You will deploy a **minimal, low-cost, multi-cloud, multi-region footprint** using only resources you can find and understand from the registry docs.

You’ll provision **storage foundations** in **two clouds** and **two regions each**:

* **AWS:** S3 buckets in two regions
* **Azure:** Storage accounts (plus resource groups) in two regions

> No code is provided. Your task is to read the Terraform Registry pages and write the configuration yourself.

---

## Real-World Scenario

Your company needs a **resilient, cloud-agnostic “asset landing zone.”** Product teams will later push build artifacts and static assets to whichever region/cloud is closest. Today’s goal is to **establish the foundations**, not the data flows.

**Requirements**

* **AWS:** Create one S3 bucket in **us-east-1** and one in **eu-central-1**.
* **Azure:** Create one Storage Account in **East US** and one in **West Europe**, each inside its own Resource Group.
* Apply a **clear naming convention** that embeds the environment and region (e.g., `company-dev-assets-use1`, `company-devassetsweu`).
* Tag all resources with at least: `project=multicloud-foundation`, `owner=<your-name>`, `env=dev`.
* Enable basic durability settings you can locate in the registry (e.g., bucket versioning on S3, standard redundancy on Azure).

> Keep everything in the **free/lowest-cost tier** wherever possible.

---

## Learning Objectives

By the end you should be able to:

1. **Navigate the Terraform Registry** to find providers and resources (arguments, attributes, examples).
2. Declare **providers and regions** (AzureRM & AWS) and understand that each provider needs its own region/location.
3. Author **basic resource blocks** (no modules, no variables required).
4. Apply **idempotency**: run `plan`/`apply` multiple times with no unintended changes.
5. Use **meaningful names & tags** and understand global-uniqueness constraints (e.g., S3 bucket names).
6. **Isolate state per project folder** (local state is fine for this assignment).

---


## Very High-Level Steps (you fill the details)

1. **Plan your names** (write them down before you start). Ensure S3 names are globally unique.
2. **AWS (two folders)**

   * Find and read: **AWS provider**, **`aws_s3_bucket`**, and (optional) **`aws_s3_bucket_versioning`** pages on the Registry.
   * In each region folder, define the provider **with a region** and create exactly **one bucket** with tags (and versioning if you choose).
3. **Azure (two folders)**

   * Find and read: **AzureRM provider**, **`azurerm_resource_group`**, **`azurerm_storage_account`**.
   * In each region folder: define the provider **with a location**, create a **resource group**, then a **storage account** with tags.
4. **Apply safely**

   * In each folder: `terraform init`, then `terraform plan`, then `terraform apply`.
   * Re-run `plan` to confirm **no drift**.
5. **Document** what you used from the Registry (links/titles), any arguments that were confusing, and how you resolved them.
6. **Clean up** when done (destroy each folder’s resources) unless you need them for review.

---

## Final Output (What success looks like)

* **AWS Console:** Two S3 buckets present (us-east-1 and eu-central-1), named and tagged as required; versioning enabled if you opted in.
* **Azure Portal:** Two resource groups and two storage accounts present (East US, West Europe) with correct names/tags.
* **Terraform:** A clean `plan` (no changes) when re-run in each folder.

---

## Submission Guidelines (Single Google Doc)

Include the following, in order:

1. **Screenshots (must show regions & names):**

   * AWS S3 buckets list (both regions)
   * One S3 bucket detail page (showing versioning state if enabled)
   * Azure Resource Groups (the two RGs)
   * Each Storage Account overview (name, region)
2. **Reflection (5–10 lines):**
   * What you learnt? 

