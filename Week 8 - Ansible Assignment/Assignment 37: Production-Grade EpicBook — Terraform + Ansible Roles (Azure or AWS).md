# Assignment 37: **Production-Grade EpicBook — Terraform + Ansible Roles (Azure or AWS)**

## Objective

Stand up the **EpicBook** app on a cloud VM you provision with **Terraform** (Azure or AWS), and configure it with **Ansible roles**:

* **common**: baseline updates, packages, SSH hardening basics
* **nginx**: install, enable, serve from `/var/www/epicbook`
* **epicbook**: clone `theepicbook` repo and deploy content (build if needed), then reload Nginx

Repo: [https://github.com/pravinmishraaws/theepicbook](https://github.com/pravinmishraaws/theepicbook)

---

## What you’ll build

* Cloud VM (Ubuntu 22.04 preferred), **passwordless SSH**
* Security rules for **22 (SSH)** and **80 (HTTP)**
* Ansible **roles** with handlers, idempotency, and clean YAML

---

## Folder Layout (suggested)

```
epicbook-prod/
├─ terraform/                 # choose azure/ or aws/ below
│  └─ (azure|aws)/
├─ ansible/
│  ├─ inventory.ini
│  ├─ site.yml               # calls roles in order
│  ├─ group_vars/
│  │  └─ web.yml
│  └─ roles/
│     ├─ common/
│     │  └─ tasks/main.yml
│     ├─ nginx/
│     │  ├─ tasks/main.yml
│     │  └─ templates/epicbook.conf.j2
│     └─ epicbook/
│        └─ tasks/main.yml
└─ README.md
```

---

## Terraform (pick one)

### Option A — Azure

Provision 1 Ubuntu VM + NSG for 22/80 (use the same pattern from Assignment 36).
**Outputs required:** `public_ip`, `admin_user`.

### Option B — AWS

1 t3.micro in a public subnet; open 22, 80 in the SG.
**Outputs required:** `public_ip`, `admin_user` (e.g., `ubuntu` for Ubuntu AMIs).

> Keep SSH **key-based**. Test: `ssh <admin_user>@<public_ip> 'hostname'`.

---

## Ansible inventory

## site.yml (role orchestration)

`ansible/site.yml`

```yaml
---
- name: Prepare system (common)
  hosts: web
  become: true
  roles:
    - common

- name: Install and configure Nginx
  hosts: web
  become: true
  roles:
    - nginx

- name: Deploy EpicBook app
  hosts: web
  become: true
  roles:
    - epicbook
```

## Role: common

## Role: nginx

`ansible/roles/nginx/tasks/main.yml`

`ansible/roles/nginx/templates/epicbook.conf.j2`

## Role: epicbook

`ansible/roles/epicbook/tasks/main.yml`


## Verify

* Browser: `http://<public_ip>`

---

## Success Criteria

* VM reachable via SSH key; ports **22/80** open
* Roles run in order: **common → nginx → epicbook**
* Nginx serves EpicBook from `/var/www/epicbook`
* Idempotent re-runs (mostly OK/UNCHANGED)
* HTTP 200 and visible site content

---

## Submission Guidelines (Single Document)

**Name:** `Assignment37_EpicBook_Prod_<YourName>.pdf`

**Include screenshots:**

1. `terraform apply` success + `terraform output` (shows `public_ip`, `admin_user`)
2. Passwordless SSH check: `ssh <admin_user>@<public_ip> 'hostname'`
3. Ansible run (roles summarized) — final recap with `ok/changed/failed=0`
4. Nginx site file at `/etc/nginx/sites-available/epicbook` (snippet)
5. Browser showing `200 OK`

**Also include:**

* Role tree:

  ```
  ansible/roles/{common,nginx,epicbook}
  ```

**Reflection (comment on [**this Video**](https://youtube.com/live/XqqlV1wbVQ8?feature=share), 4–6 lines):**

* **One challenge you faced and how you fixed it**
* **Did you see any security issues? If yes, what are they and what’s your plan to fix them in production?**

