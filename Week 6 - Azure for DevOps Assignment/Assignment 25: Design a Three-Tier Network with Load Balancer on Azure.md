## Assignment 25: Design a Three-Tier Network with Load Balancer on Azure

**Assignment Objective**
You will design and deploy a **three-tier network architecture** on Azure using VNets, subnets, VMs, and a Load Balancer.

By completing this assignment, you will:

* Understand Azure Virtual Networks and subnets
* Practice creating network architectures
* Deploy a VM inside a subnet
* Install NGINX and serve content
* Configure a Public Load Balancer with backend pool, health probes, and load balancing rules

---

## Prerequisites

Before starting this assignment, make sure you have:

* An active **Azure Free Account**
* Completed the lecture on **Azure Networking**
* Basic familiarity with Azure VM creation (Watched the live session)

**IP Planning Sheet:**
Use this sheet for CIDR and subnet calculations:
🔗 [IP Calculation Sheet](https://docs.google.com/spreadsheets/d/10aph-_Dd3IfDGn1pK24bjLE6AnY_frwR2GbGjHL04Vk/edit?gid=1623340646#gid=1623340646)

---

## Architecture Overview

You will build this structure:

<img width="1810" height="803" alt="Screenshot 2025-09-19 at 07 52 31" src="https://github.com/user-attachments/assets/2607f40c-6ef0-4be0-bec9-02428cadebe0" />

* **VNet CIDR**: 10.0.0.0/16 (example from sheet)
* **Web Subnet**: 10.0.1.0/24
* **App Subnet**: 10.0.2.0/25
* **DB Subnet**: 10.0.3.0/26

---

## Tasks [Watch step by step guide here](https://youtu.be/1XtZPrydvbw?t=8259)

### **Step 1: Create a Resource Group**

* Name: `vnet-demo-rg`
* Region: `Central India` (or your nearest)

---

### **Step 2: Create a Virtual Network**

* Name: `eb-demo-vnet`
* CIDR: `10.0.0.0/16`
* Enable **Azure Bastion** (optional, but recommended for private VM access later)

Create the following subnets inside the VNet:

| Subnet Name | Address Range |
| ----------- | ------------- |
| web-subnet  | 10.0.1.0/24   |
| app-subnet  | 10.0.2.0/25   |
| db-subnet   | 10.0.3.0/26   |

> Note: Bastion will auto-create its own subnet (`AzureBastionSubnet`)

---

### **Step 3: Create a VM in the Web Subnet**

* Name: `web-nginx`
* Image: `Ubuntu 22.04 LTS`
* Authentication: username/password
* Subnet: `web-subnet`
* Assign a **Public IP**
* Allow inbound ports: SSH (22) and HTTP (80)

---

### **Step 4: Install and Configure NGINX**

SSH into the VM using its Public IP:

```bash
ssh <username>@<public-ip>
sudo apt update
sudo apt install -y nginx
sudo systemctl enable nginx
sudo systemctl start nginx
```

Verify by visiting:
`http://<VM Public IP>` → You should see the NGINX default page.

---

### **Step 5: Create a Public Load Balancer**

* Name: `web-public-elb`
* Type: `Public`
* SKU: `Standard`
* Region: same as VNet
* Frontend: Create new Public IP (name: `web-elb-ip`)
* Backend pool:

  * Name: `web-backend-pool`
  * Add your `web-nginx` VM (by IP or NIC)
* Health probe:

  * Name: `web-health-probe`
  * Protocol: TCP
  * Port: 80
  * Interval: 5 seconds
* Load balancing rule:

  * Name: `web-http-rule`
  * Frontend: `web-elb-ip`
  * Backend: `web-backend-pool`
  * Protocol: TCP
  * Port: 80 → Backend port: 80

---

### **Step 6: Test the Architecture**

* Open your browser and go to:
  `http://<LoadBalancer Public IP>`
* You should see the same NGINX default page

---

### **Step 7: Clean Up Resources**

To avoid charges, delete everything:

1. Go to your Resource Group `vnet-demo-rg`
2. Click **Delete resource group**
3. Type the name to confirm

---

## Submission Instructions

* Submit the following:

  1. Screenshot of the **subnet configuration screen** (showing 3 subnets + bastion)
  2. Screenshot of the **Load Balancer frontend IP config**
  3. Screenshot of your browser showing the **NGINX welcome page via Load Balancer public IP**

---

## Expected Output

* VNet with **web, app, db subnets**
* Working VM running **NGINX** in web subnet
* Public **Load Balancer** distributing traffic to your web VM
* Website accessible at:

  ```
  http://<load-balancer-public-ip>
  ```

---

## 💡 Tips

* If the Load Balancer shows a private IP, you may have accidentally selected **Internal** type. Delete and recreate it as **Public**.
* Always add your VM to the backend pool **after** the VM is created.
* Health probe must match your VM’s running port (80).
