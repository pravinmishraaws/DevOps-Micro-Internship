# Assignment 28: Authenticate Terraform to Azure using **Service Principal + Client Secret**

## Objective

Configure Terraform to authenticate to Azure **without** your personal login by creating and using a **Service Principal (SP)** with a **client secret**. You will:

* Create a Service Principal with least-privilege RBAC
* Export credentials as environment variables for Terraform
* Verify end-to-end by provisioning a small Azure resource (RG) using the SP
* Learn secure handling and rotation practices

## What you will do

* Create an SP scoped to your subscription (role: **Contributor** by default; adjust if needed)
* Store **Client ID, Client Secret, Tenant ID, Subscription ID**
* Set **ARM_*** environment variables Terraform uses
* Run `terraform init/plan/apply` **without** Azure CLI login
* Tear down and clean up

## Prerequisites

* Active Azure subscription (same as previous assignments)
* Azure CLI installed and able to `az login` (only to create the SP)
* Terraform installed (v1.5+ recommended)
* macOS/Linux shell or Windows PowerShell

## Estimated time

**40–70 minutes**

---

## Instructions (Step-by-Step)

### 1) Identify your subscription

```bash
az login
az account show --query id -o tsv
# copy SUBSCRIPTION_ID
```

### 2) Create Service Principal (SP) with RBAC

> You must be Owner or User Access Administrator on the scope to create RBAC assignments.

**Option A – Scoped to entire subscription (simple for labs)**

```bash
SUBSCRIPTION_ID="<your-subscription-id>"

az ad sp create-for-rbac \
  --name "sp-terraform-epicbook" \
  --role "Contributor" \
  --scopes "/subscriptions/$SUBSCRIPTION_ID" \
  --years 1 \
  --query "{appId:appId,password:password,tenant:tenant}" -o json
```

**Output fields you need**

* `appId` → **Client ID**
* `password` → **Client Secret**
* `tenant` → **Tenant ID**

> Tip: You can restrict further (e.g., scope to a Resource Group) once you’re comfortable.

### 3) Export credentials for Terraform

Terraform’s AzureRM provider reads these environment variables:

* `ARM_CLIENT_ID`
* `ARM_CLIENT_SECRET`
* `ARM_TENANT_ID`
* `ARM_SUBSCRIPTION_ID`

**macOS/Linux (temporary for current shell)**

```bash
export ARM_CLIENT_ID="<appId>"
export ARM_CLIENT_SECRET="<password>"
export ARM_TENANT_ID="<tenant>"
export ARM_SUBSCRIPTION_ID="$SUBSCRIPTION_ID"
```

**Windows PowerShell (temporary for current session)**

```powershell
$env:ARM_CLIENT_ID="<appId>"
$env:ARM_CLIENT_SECRET="<password>"
$env:ARM_TENANT_ID="<tenant>"
$env:ARM_SUBSCRIPTION_ID="<subscriptionId>"
```

> To make them persistent locally (optional for labs):
> macOS/Linux → add the `export` lines to `~/.bashrc` or `~/.zshrc`
> Windows → `setx` (opens a new session to take effect)

### 4) Log out Azure CLI (to prove Terraform uses SP)

```bash
az logout
```

### 5) Create a minimal Terraform config to test

**`main.tf`**

```hcl
provider "azurerm" {
  features {}
  # Do NOT put subscription id.
}

resource "azurerm_resource_group" "rg" {
  name     = "rg-tf-sp-demo"
  location = "East US"
}

output "rg_name" {
  value = azurerm_resource_group.rg.name
}
```

Run:

```bash
terraform init
terraform plan
terraform apply -auto-approve
```

You should see the RG created **without** any `az login` in this shell.

### 6) Rotate / show / delete secret (reference)

* **Rotate secret** (creates a new password):

  ```bash
  az ad sp credential reset --name "<appId>" --years 1
  ```
* **Delete SP** (cleanup when done with labs):

  ```bash
  az ad sp delete --id "<appId>"
  ```

---

## Deliverables (What to Submit)

Upload **one Google document(Refer assignment submtion instruction google sheets)** containing:

1. The Terraform **plan** snippet (showing provider init + RG create)
2. Screenshot of the **Resource Group** in Azure Portal
3. Terminal proof that `az logout` was run before `terraform apply`
4. A short note (3–6 lines): where you stored secrets and how you would rotate them in production

---

## Security & Best Practices (read & apply)

* **Least privilege:** Use the narrowest scope possible (e.g., RG instead of Subscription) and minimal roles.
* **Never commit secrets:** Do not commit env vars or lock files containing secrets to Git.
* **Secret storage:** In CI/CD, store secrets in **GitHub Actions Secrets**, **Azure Key Vault**, etc.
* **Rotation:** Rotate client secrets regularly and on personnel changes.
* **Prefer federated identity (advanced):** For production CI/CD, use **OIDC (workload identity)** to avoid long-lived client secrets.

---

## Troubleshooting

**`Error: AuthorizationFailed`**

* SP doesn’t have required role or scope is wrong. Re-run create-for-rbac with correct `--scopes`.

**`Error acquiring the state lock` or provider init issues**

* Re-run `terraform init -upgrade`. Ensure network/proxy allows provider downloads.

**`Authentication failed`**

* Check `ARM_*` variables are set in the **same** shell where you run Terraform.
* Secret may be expired—reset with `az ad sp credential reset`.

**`Provider version conflicts`**

* Delete `.terraform` folder and re-run `terraform init`.
* Pin a compatible `azurerm` version in `required_providers`.

---

## Cleanup (Important)

Destroy resources and (optionally) the SP:

```bash
terraform destroy -auto-approve
az ad sp delete --id "<appId>"
```
