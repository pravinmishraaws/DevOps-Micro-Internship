# Assignment 24: Deploy a React Application on an Azure Virtual Machine

## Objective

Deploy a production-ready **[React application](https://github.com/pravinmishraaws/my-react-app)** from GitHub to an **Ubuntu Azure VM**, build it, and serve it via **Nginx**.

> Note: You deployed this same app in the AWS section—follow the same logic and the app’s README.

## What you will do

* Create a **Resource Group**
* Provision an **Ubuntu 20.04 LTS** Virtual Machine (B1s recommended)
* Open **HTTP (80)** and **SSH (22)**
* SSH into the VM and install **Node.js + npm**
* Clone, **build** the React app, and configure **Nginx** to serve `/build`
* Verify the app at the VM’s **public IP**

## Prerequisites

* Azure Free/active subscription (created in Assignment 23)
* Local terminal with SSH (macOS/Linux) or PuTTY/Windows Terminal
* Basic Git knowledge

## Estimated time

45–75 minutes

---

## Instructions (Step-by-Step) ([Watch video here](https://www.youtube.com/live/1XtZPrydvbw?t=2881s))

### 1) Create a Resource Group

* Name: `react-app-rg`
* Region: your nearest (e.g., `West Europe`)

**Portal:** *Home → Resource groups → Create*
**CLI (optional):**

```bash
az group create --name react-app-rg --location westeurope
```

### 2) Provision an Ubuntu VM

* Image: **Ubuntu 20.04 LTS**
* Size: **B1s** (demo friendly)
* Username: `azureuser` (or your choice)
* Inbound ports: **SSH (22)** and **HTTP (80)**
* Put it in **react-app-rg**

### 3) Connect to the VM

Find the VM **Public IP** in the portal, then:

```bash
ssh azureuser@<VM_PUBLIC_IP>
```

### 4) Install Node.js & npm

Update packages:

```bash
sudo apt update && sudo apt upgrade -y
```

**Option A (simple, Ubuntu repo – may be older):**

```bash
sudo apt install -y nodejs npm
node -v
npm -v
```

**Option B (recommended, newer LTS via NodeSource – optional):**

```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs
node -v && npm -v
```

### 5) Clone & Build the React App

```bash
cd ~
git clone https://github.com/pravinmishraaws/my-react-app.git
cd my-react-app
npm install
npm run build
```

You should now have a `~/my-react-app/build/` directory with static files.

### 6) Install & Configure Nginx (static hosting)

Install Nginx:

```bash
sudo apt install -y nginx
sudo systemctl enable nginx
sudo systemctl start nginx
sudo systemctl status nginx --no-pager
```

Edit default server block:

```bash
sudo nano /etc/nginx/sites-available/default
```

Inside the existing `server { ... }` block, ensure you have:

```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    server_name _;

    location / {
        root /home/azureuser/my-react-app/build;
        index index.html index.htm;
        try_files $uri /index.html;
    }
}
```

> If your Linux username isn’t `azureuser`, update the path accordingly.

Test and restart:

```bash
sudo nginx -t
sudo systemctl restart nginx
```

### 7) Test the deployment

Open your browser:

```
http://<VM_PUBLIC_IP>
```

You should see the React app homepage. Navigate between routes to confirm client-side routing works.

---

## Tools & Services Required

* Azure Virtual Machine (Ubuntu 20.04 LTS)
* Git and Nginx
* Node.js and npm
* React app: `github.com/pravinmishraaws/my-react-app`

---

## Deliverables (What to Submit)

Upload **one document** (PDF/Markdown/Word) that includes:

1. **Public IP** of your VM
2. **Screenshot** of the React app running in your browser (from the VM public IP)
3. **Screenshot** of the Azure VM **Overview** blade (showing VM name, resource group, region)
4. **Brief note (3–5 lines)**: what you learned + any issue you faced and fixed

> Mask any personal data if shown.

---

## Troubleshooting (Read before asking for help)

**Port open but site not loading**

* Confirm NSG inbound rule for **HTTP 80**. In portal: *VM → Networking → Inbound port rules*.
* VM firewall: Ubuntu typically allows 80 by default (UFW is usually disabled). If using UFW:

  ```bash
  sudo ufw allow 80/tcp
  sudo ufw status
  ```

**Nginx welcome page instead of app**

* Your `root` path might be wrong. Confirm the path exists:

  ```bash
  ls -la /home/azureuser/my-react-app/build
  ```
* Ensure the config was edited in `/etc/nginx/sites-available/default`, then:

  ```bash
  sudo nginx -t && sudo systemctl restart nginx
  ```

**Blank page / 404 on client routes**

* Ensure this line is present: `try_files $uri /index.html;` (enables SPA routing).

**Build failed**

* Re-run: `npm install` then `npm run build`
* Check Node/npm versions. If Ubuntu repo is too old, use NodeSource (Step 4 Option B).

**SSH issues**

* Use the correct username (e.g., `azureuser`).
* Reboot VM from portal if hung: *VM → Restart*.

---

## Cost & Cleanup (Important)

* This demo can run on **B1s** for a few cents/hour.
* When done, **stop charges** by deleting the **Resource Group**:

```bash
az group delete --name react-app-rg --yes --no-wait
```

(or Portal → *Resource group → Delete*). This removes the VM, IP, disk, NIC—everything.
