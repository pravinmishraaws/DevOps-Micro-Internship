# Assignment 34: **Ad-Hoc Automation on Azure — 4 VMs, Inventory & Passwordless SSH**

## Objective

Provision a small Azure fleet with **Terraform (4 Linux VMs)**, set up **passwordless SSH**, create a **custom Ansible inventory**, and run **ad-hoc commands** (ping, uptime, package install, service restart) targeting **hosts and groups** with `--become` where needed.


---

## Real-World Scenario

Your team spins up dev sandboxes on Azure. You’re asked to bring up four VM, prove access is secure and consistent, and show that you can run fleet-wide actions in seconds—without writing a playbook.

---

## Prereqs

* Azure CLI authenticated, Terraform installed
* SSH key ready: `~/.ssh/id_ed25519` (or create one)
* Ubuntu LTS image preferred

---

## Deliverables (at the end)

* `terraform/` with working config, **outputs** listing VM IPs
* `inventory.ini` with groups
* Command history (or screenshots) proving ad-hoc tasks ran successfully

---

## Steps

### 1) Provision 4 Azure VMs (Terraform)

### 2) Configure passwordless SSH

### 3) Create a custom Ansible inventory

`inventory.ini` (map IPs to groups using the `roles` list you set in TF)

```ini
[web]
<ip_of_vm0>
<ip_of_vm1>

[app]
<ip_of_vm2>

[db]
<ip_of_vm3>

[all:vars]
ansible_user=azureuser
ansible_ssh_private_key_file=~/.ssh/id_ed25519
```

**Tip:** Paste the `terraform output public_ips` in order (index 0..3).
(If you want to automate, you can export a JSON output and generate this file—but manual is fine for this assignment.)

---

### 4) Run your first Ansible ad-hoc commands

- Test connectivity:
- Who am I on the remote hosts (no sudo):
- Check uptime (all hosts):
- Install a package (use `--become`):
- Start & enable a service (e.g., nginx on web only):
- Install nginx
  - Ensure nginx service is running
  - Target one group vs all:
    - df -h
    - free -m
  
---

## Final Output (What success looks like)

* Inventory groups (`web`, `app`, `db`) resolved correctly.
* `ping` returns **SUCCESS** for all 4 hosts.
* Demonstrated `--become` for package/service management.
* Screenshots of uptime, package install, and service status per group.

---

## Submission (single doc)

Name: `Assignment34_AdHoc_Azure_<YourName>.pdf`

1. Terraform apply success + outputs (IPs)
2. `inventory.ini` (redact your public IPs if you prefer)
3. Ping + uptime screenshots
4. `htop`/`nginx` install & service start screenshots
5. Short reflection (4–6 lines): when to use **ad-hoc** vs **playbook** on [**this Video**](https://youtube.com/live/XqqlV1wbVQ8?feature=share)
