# Assignment 26: Deploy **EpicBook** on Azure VM + **Azure Database for MySQL**

## Objective

Deploy the **[EpicBook](https://github.com/pravinmishraaws/theepicbook)** web application on Azure using:

* **Ubuntu VM** (frontend + backend on the same VM, Nginx as web/reverse proxy)
* **Azure Database for MySQL – Flexible Server** (private VNet integration in a **private subnet**)

You will design a secure network (public & private subnets), apply NSGs, provision the VM, configure Nginx + Node.js, and connect the backend to MySQL.

> **Note:** Follow the project’s guide:
> Installation, Configuration & Troubleshooting Guide →
> [https://github.com/pravinmishraaws/theepicbook/blob/main/Installation%20%26%20Configuration%20Guide.md](https://github.com/pravinmishraaws/theepicbook/blob/main/Installation%20%26%20Configuration%20Guide.md)
> Repo → [https://github.com/pravinmishraaws/theepicbook](https://github.com/pravinmishraaws/theepicbook)

## What you will do

* Create **VNet 10.0.0.0/16** with:

  * **Public subnet:** 10.0.1.0/24 (VM)
  * **Private subnet:** 10.0.2.0/24 (MySQL Flexible Server)
* Apply **NSGs**:

  * Public subnet NSG → **Allow** HTTP(80), SSH(22) from your IP; **Deny** everything else inbound
  * Private subnet NSG → **Allow** MySQL(3306) **only** from 10.0.1.0/24
* Create **Azure MySQL Flexible Server** in the **private** subnet (VNet integration)
* Create **Ubuntu 22.04 LTS** VM (B1s), install **Node.js, npm, Nginx, git, mysql-client**
* Clone and deploy **EpicBook**: build React frontend, run Node/Express backend, set DB env vars
* Configure **Nginx** to serve the frontend and reverse-proxy `/api` to backend
* Validate **end-to-end** (products/cart/orders)

## Prerequisites

* Active Azure subscription (from Assignment 23)
* SSH client (macOS/Linux terminal or Windows Terminal/PuTTY)
* Basic Git & Linux familiarity

## Estimated time

**90–150 minutes**

---

## Instructions (Step-by-Step)

### 1) Network & Security

**1.1 Create VNet + Subnets**

* VNet: `epic-vnet` — **10.0.0.0/16**
* Subnets:

  * `public-subnet` — **10.0.1.0/24** (VM)
  * `mysql-subnet` — **10.0.2.0/24** (MySQL)

    * When creating **MySQL Flexible Server** later, Azure will **delegate** this subnet to `Microsoft.DBforMySQL/flexibleServers`. If asked now, set that delegation.

**1.2 NSGs**

* `nsg-public` (associate to `public-subnet`)

  * Inbound allow: **TCP 22** (SSH) from **YourIP** (recommended)
  * Inbound allow: **TCP 80** from **Internet**
  * Default deny for others
* `nsg-mysql` (associate to `mysql-subnet`)

  * Inbound allow: **TCP 3306** **source** = `10.0.1.0/24` (the VM subnet)
  * No public inbound allowed

**1.3 Public IP & NIC**

* You’ll assign a **Public IP** to the VM NIC that sits in `public-subnet`.

> ✅ **Result:** A VNet with two subnets, strict NSGs, and a path where only the VM can talk to MySQL over 3306.

---

### 2) Provision the Azure VM

**VM Settings**

* Name: `epicbook-vm`
* Image: **Ubuntu 22.04 LTS**
* Size: **Standard\_B1s**
* VNet/Subnet: `epic-vnet / public-subnet`
* Inbound ports: **22**, **80**
* Username: `azureuser` (or your choice)
* SSH key or password (key recommended)

**On the VM (SSH in):**

```bash
ssh azureuser@<VM_PUBLIC_IP>

sudo apt update && sudo apt upgrade -y
sudo apt install -y nginx git mysql-client

# Node.js (recommended LTS via NodeSource)
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs
node -v && npm -v
```

---

### 3) Deploy EpicBook App (Frontend + Backend)

**3.1 Clone repo**

```bash
cd ~
git clone https://github.com/pravinmishraaws/theepicbook.git
cd theepicbook
```

> Refer to the project’s **Installation & Configuration Guide** in the repo for any project-specific steps.

**3.2 Install deps & build frontend**

```bash
# If frontend is inside /frontend
cd frontend
npm install
npm run build
# Result: ~/theepicbook/frontend/build/
```

**3.3 Configure backend (Node/Express)**

* If backend in `/backend`:

```bash
cd ~/theepicbook/backend
npm install
```

* Create a `.env` (or the app’s expected config file) with **MySQL** secrets and host:

  * **Important:** Azure MySQL Flexible Server uses an FQDN like:
    `yourserver.mysql.database.azure.com`
  * Typical ENV examples (adjust to your app’s expected variable names):

    ```
    DB_HOST=yourserver.mysql.database.azure.com
    DB_USER=epic_admin@yourserver
    DB_PASSWORD=<your-strong-password>
    DB_NAME=epicbook
    DB_PORT=3306
    # If the driver needs SSL for Azure MySQL (often required):
    DB_SSL=true
    ```
* Start backend to verify (temporary):

```bash
node server.js
# or npm start / pm2 start, depending on your app
```

> Note the backend port (e.g., **3001**). You will reverse-proxy this via Nginx.

---

### 4) Create **Azure Database for MySQL – Flexible Server** (Private)

**4.1 Create server (Portal recommended)**

* **Flexible Server**
* **Private access (VNet Integration)**
* VNet/Subnet: `epic-vnet / mysql-subnet` (subnet delegation to MySQL is required)
* Compute/storage: small for demo
* Admin user/pass: save securely
* **Auto-create private DNS** (e.g., `privatelink.mysql.database.azure.com`) and link to your VNet (usually automatic with private access)

**4.2 Create DB & import schema**
From the VM (has network path to private MySQL):

```bash
# Use the Flexible Server FQDN shown in portal
MYSQL_HOST=<yourserver>.mysql.database.azure.com
MYSQL_USER=<adminuser>@<yourserver>
MYSQL_DB=epicbook

# Create DB
mysql -h $MYSQL_HOST -u $MYSQL_USER -p -e "CREATE DATABASE $MYSQL_DB;"

# If you have a SQL dump (per project guide), import it:
mysql -h $MYSQL_HOST -u $MYSQL_USER -p $MYSQL_DB < /path/to/epicbook.sql
```

> **Firewall:** With **Private access**, public firewall rules are not used; connectivity happens over the VNet. Ensure `nsg-mysql` allows 3306 only from `10.0.1.0/24`.

> **SSL:** Azure MySQL typically enforces SSL. Use your driver’s SSL options (see project guide). For Node `mysql2`, you can pass `ssl: {}` to enable default secure connection, or use CA bundle if required.

---

### 5) Configure Nginx (static SPA + API reverse proxy)

**5.1 Serve the React build**

```bash
sudo nano /etc/nginx/sites-available/default
```

Replace `server { ... }` with (adjust username/paths/ports):

```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    server_name _;

    # Frontend (React build)
    root /home/azureuser/theepicbook/frontend/build;
    index index.html;

    location / {
        try_files $uri /index.html;
    }

    # Backend API proxy (change 3001 if your backend uses a different port)
    location /api/ {
        proxy_pass http://127.0.0.1:3001/;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-For $remote_addr;
        proxy_set_header Connection "";
    }
}
```

Apply and reload:

```bash
sudo nginx -t
sudo systemctl restart nginx
```

**5.2 Run backend as a service (optional but recommended)**

```bash
# Install pm2 (optional)
sudo npm i -g pm2
cd ~/theepicbook/backend
pm2 start server.js --name epicbook-api
pm2 save
pm2 status
```

> Alternatively create a **systemd** unit for the backend.

---

### 6) End-to-End Test

1. Visit `http://<VM_PUBLIC_IP>` → React UI loads
2. Navigate **Home → Gallery → Products → Cart**
3. Validate DB ops:

   * Products list shows from DB
   * Add to cart → order summary → checkout flow
4. Confirm API calls go through **/api/** (Nginx proxy) and the backend can reach **MySQL** over the private subnet

---

## Tools & Services

* Azure **VNet, Subnets, NSGs, Public IPs, NICs**
* Azure **VM (Ubuntu 22.04)**
* Azure **Database for MySQL – Flexible Server (Private access)**
* **Node.js, npm, Nginx, git, mysql-client**

---

## Deliverables (What to Submit)

Upload **one PDF** that includes:

1. **VM Public IP**
2. **Screenshots**:

   * VNet + Subnets overview (showing 10.0.1.0/24 & 10.0.2.0/24)
   * NSG rules for both subnets
   * VM **Overview** blade (name, RG, region)
   * Azure Database for MySQL server page (showing **Private access / VNet** integration)
   * App running in browser (Home/Products/Cart)
3. (Optional) Short terminal logs: successful MySQL connection or app startup
4. **One paragraph (3–6 lines)**: any challenge faced & how you solved it

---

## Troubleshooting (Read before asking)

**Can’t connect to MySQL from backend**

* Ensure **server type** is **Flexible Server** with **Private access**, in `mysql-subnet`
* Check `nsg-mysql` inbound rule **3306** source = `10.0.1.0/24`
* From VM:

  ```bash
  mysql -h <server>.mysql.database.azure.com -u <user>@<server> -p -e "SELECT 1;"
  ```
* For Node drivers, enable **SSL** (see repo guide). For `mysql2`, add `ssl: {}` in the connection config.

**Nginx shows default page or 404**

* Confirm `root` path points to `/home/<user>/theepicbook/frontend/build`
* Ensure `try_files $uri /index.html;` for SPA routing
* `sudo nginx -t && sudo systemctl restart nginx`

**API 502/504 via /api/**

* Check backend is **running** (pm2 or node). Correct port in `proxy_pass`.
* Test locally:

  ```bash
  curl -i http://127.0.0.1:3001/api/health
  ```

**MySQL DNS not resolving**

* Flexible Server (private) creates a **private DNS zone** linked to your VNet. If you built manually, link it: `privatelink.mysql.database.azure.com` → VNet.

**Costs**

* Use **B1s** VM; keep DB minimal. **Delete** RG after grading to avoid charges.

---

## Cleanup (Important)

Delete the entire **Resource Group** when done (removes VM, NIC, IP, disks, DB, etc.).

Portal: *Resource group → Delete*
CLI (if used):

```bash
az group delete --name <your-rg> --yes --no-wait
```
