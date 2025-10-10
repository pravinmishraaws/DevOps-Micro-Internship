# Assignment 36: **Deploy Mini Finance Project using Terraform and Ansible**

## Objective

Provision a secure Azure VM with **Terraform** and use an **Ansible multi-playbook** to:

* Install & configure **Nginx**
* **Clone** a static mini-finance site from GitHub
* **Deploy** it to Nginx’s web root
* **Gracefully reload** Nginx after deployment

---

## Real-World Scenario

You need a fast, repeatable path to spin up a public demo of a static site. Infra via Terraform, config & deploy via Ansible—clean separation of concerns.

---

## What you’ll build

* Azure **Resource Group, VNet, Subnet, NSG**, and **Ubuntu VM**
* NSG rules for **SSH (22)** and **HTTP (80)**
* Passwordless SSH with your public key
* Ansible multi-play flow: **install → deploy → verify**

---

## Folder Layout (suggested)

```
mini-finance/
├─ terraform/
│  ├─ providers.tf
│  ├─ main.tf
│  └─ variables.tf   (optional)
├─ ansible/
│  ├─ inventory.ini
│  └─ site.yml       # multi-play
└─ README.md
```

---

## Terraform (Azure VM + NSG for 22/80)

## Ansible (multi-playbook: install → deploy → verify)

---

## Submission Guidelines (Single Document)

**Name:** `Assignment36_MiniFinance_<YourName>.pdf`

**Include screenshots:**

1. `terraform apply` success (end of apply)
2. `terraform output` showing `public_ip`
3. NSG rules proving inbound **22** and **80** (Terraform code or Azure Portal)
4. Passwordless SSH check: `ssh <admin_user>@<public_ip> "hostname"`
5. `ansible-playbook -i inventory.ini site.yml (HTTP 200 + assertion OK) 
8. Browser hitting `http://<public_ip>`

**Also include:**

* Your project tree from repo root:

  ```
  mini-finance/
  ├─ terraform/
  ├─ ansible/
  └─ README.md
  ```
* `ansible/inventory.ini` (you may mask the last octet of the IP)

**Add a short reflection (4–6 lines) as a comment on [this video](https://youtube.com/live/XqqlV1wbVQ8?feature=share):**

* One challenge you faced and how you fixed it
* One real-world example where you can use this learning

