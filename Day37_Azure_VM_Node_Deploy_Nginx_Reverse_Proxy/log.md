# Day 37 – Azure VM Node.js Deploy & Nginx Reverse Proxy

**Date:** 23rd August 2025  
**Internship Role:** Cloud Support Engineer Intern (Prime One Global)  
**Platform:** Microsoft Azure (Ubuntu 22.04 LTS · Node.js · Nginx · PM2 · Git · Azure CLI)

---

## Task Summary
- Reused Azure VM **vm-node-ubuntu** in **rg-lab-core-uks-01** (Private IP: `10.0.0.4`, Public IP: `172.167.171.183`).
- Installed modern **Node.js 20 LTS** (upgrade from Node 12, which blocked dependency install for the sample app).
- Cloned the open‑source **Express** app: `heroku/nodejs-getting-started`.
- Ran the app with **PM2** and configured **.env / PORT**.
- Set up **Nginx** reverse proxy (**80 → 127.0.0.1:<app-port>**) and validated access from the public IP.
- Troubleshot a **502 Bad Gateway** caused by an upstream port mismatch (app on `5006` while Nginx pointed at `3000`); fixed by aligning ports.
- Documented safe **cleanup** of individual Azure resources (without deleting the resource group).

---

## Key Concepts Learned
1) **IP scopes:** Public vs Private vs Loopback  
   - Public (what users hit): `172.167.171.183` → Nginx (port 80/443)  
   - Private (inside VNet): `10.0.0.4`  
   - Loopback (same VM only): `127.0.0.1` (safe upstream target for Node app)
2) **Node.js versions matter:** many modern deps require Node ≥ 18; upgrade via NodeSource to avoid build failures.
3) **Nginx as reverse proxy:** common 502 root cause = wrong upstream port or app down; verify with `curl 127.0.0.1:<port>`.
4) **PM2 for reliability:** process supervision, autostart on boot, simple logs & restarts.
5) **Azure NSG vs app security:** expose **80/443** only; keep app port bound to **127.0.0.1** and closed externally.
6) **Granular cleanup:** delete VM/NIC/PIP/OS disk/NSG individually to preserve the RG and shared network resources.

---

## Code Implementation

### A) VM Prep (existing VM)
```powershell
# Open web ports in the VM's NSG (if not already)
$RG="rg-lab-core-uks-01"
$VM="vm-node-ubuntu"
az vm open-port -g $RG -n $VM --port 80
az vm open-port -g $RG -n $VM --port 443

# Get public IP for testing
az vm list-ip-addresses -g $RG -n $VM -o table
```
SSH to the VM:
```bash
ssh azureuser@172.167.171.183
```

### B) Install Node.js LTS, Git, and PM2
```bash
# Base tools
sudo apt update && sudo apt -y install git build-essential curl

# Node 20 LTS via NodeSource (upgrade from Node 12)
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt -y install nodejs
node -v && npm -v

# PM2
sudo npm i -g pm2
```

### C) Clone & Run the Node App
```bash
cd ~
git clone https://github.com/heroku/nodejs-getting-started.git app
cd app

# Install dependencies
[ -f package-lock.json ] && npm ci || npm install

# Optional: create .env and set a port (3000 or 5006)
echo "PORT=5006" | tee -a .env

# Start under PM2 (bind to loopback for safety)
HOST=127.0.0.1 PORT=5006 pm2 start npm --name app -- start --update-env
pm2 save
pm2 startup systemd -u $USER --hp $HOME   # run the printed sudo command, then pm2 save again
```

### D) Nginx Reverse Proxy (80 → app port)
```bash
sudo apt -y install nginx
sudo rm -f /etc/nginx/sites-enabled/default

# If your app runs on 5006, proxy to 127.0.0.1:5006; change if using 3000
sudo tee /etc/nginx/sites-available/app <<'EOF'
server {
  listen 80 default_server;
  server_name _;
  location / {
    proxy_pass         http://127.0.0.1:5006;
    proxy_http_version 1.1;
    proxy_set_header   Upgrade $http_upgrade;
    proxy_set_header   Connection "upgrade";
    proxy_set_header   Host $host;
    proxy_set_header   X-Real-IP $remote_addr;
    proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header   X-Forwarded-Proto $scheme;
  }
}
EOF

sudo ln -sf /etc/nginx/sites-available/app /etc/nginx/sites-enabled/app
sudo nginx -t && sudo systemctl reload nginx
```

Validate:
```bash
# App listening?
sudo ss -lntp | grep node
curl -I http://127.0.0.1:5006

# From your laptop/browser:
# http://172.167.171.183/
```

### E) Troubleshooting Notes (502 Bad Gateway)
```bash
# Check app & port
pm2 status
sudo ss -lntp | grep node
curl -I http://127.0.0.1:<your-app-port>

# Check Nginx logs
sudo tail -n 50 /var/log/nginx/error.log

# Fix 1: change Nginx upstream to the correct port (e.g., 5006), then:
sudo nginx -t && sudo systemctl reload nginx

# Fix 2: keep Nginx on 3000, re-run app on 3000
pm2 delete app
HOST=127.0.0.1 PORT=3000 pm2 start npm --name app -- start --update-env
pm2 save && sudo systemctl reload nginx
```

### F) Cleanup (keep the Resource Group)
```powershell
$RG   = "rg-lab-core-uks-01"
$VM   = "vm-node-ubuntu"
$NIC  = "vm-node-ubuntuVMNic"
$PIP  = "vm-node-ubuntuPublicIP"
$NSG  = "vm-node-ubuntuNSG"
$DISK = "vm-node-ubuntu_OsDisk_1_dcad487d26014941b75119db70d0af3b"

# Delete compute & attached resources
az vm delete -g $RG -n $VM --yes
az disk delete -g $RG -n $DISK --yes
az network nic delete -g $RG -n $NIC
az network public-ip delete -g $RG -n $PIP
az network nsg delete -g $RG -n $NSG

# Verify remaining resources
az resource list -g $RG -o table
```

---

## Practice Tasks
1) Add a domain + **Let’s Encrypt** TLS via Certbot and force HTTPS.  
2) Lock down NSG: allow only 80/443; keep app port closed.  
3) Add a simple **health check** route and reference it in Nginx.  
4) Configure PM2 **log rotation** and retention; test a crash and auto‑restart.  
5) Swap the sample app for a more complex Node project (API + env secrets), then redeploy.  

---

## Lessons Learned
- Keep **Node.js** up‑to‑date to avoid dependency install failures; check engine requirements in `package.json`.  
- Always bind the app to **127.0.0.1** when fronted by Nginx; never expose the app port publicly.  
- 502 errors usually point to upstream mismatch or crashed app—validate with local `curl` before blaming Nginx.  
- **PM2** simplifies running Node in production: `pm2 start`, `pm2 logs`, `pm2 save`, `pm2 startup`.  
- Cleanups can be **granular**—delete VM/NIC/PIP/OS disk/NSG without touching the RG.  
