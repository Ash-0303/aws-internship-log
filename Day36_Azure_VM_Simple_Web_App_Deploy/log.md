# Day 36 — Azure VM: Simple Web App Deploy (HTTP/HTTPS)

**Date:** 22nd August 2025  
**Internship Role:** Cloud Support Engineer Intern (Prime One Global)  
**Platform:** Microsoft Azure (Windows 11 PowerShell + Ubuntu 22.04 VM)

---

## Task Summary
- Verified Azure CLI context (subscription, defaults) and set **group/location** defaults.
- Provisioned Linux VM **vm-ubuntu-uks-01** in **rg-lab-core-uks-01** (Region: **UK South**).
- Generated/used SSH keys for secure access; retrieved public IP.
- Opened inbound **HTTP 80** and **HTTPS 443** with correct **NSG priorities** (1010/1011).
- Connected via **SSH**, installed **Nginx**, and published a custom landing page.
- Confirmed site over **public IP** in a browser.
- Captured cleanup options and cost guardrails.

---

## Core Concepts Learned
1) **CLI Context & Defaults**  
   - `az account show`, `az group list`, and `az configure --list-defaults` help confirm the current subscription and saved defaults.  
   - Setting defaults with `az configure --defaults group=<RG> location=<region>` reduces mistakes and typing.

2) **VM Networking & NSGs**  
   - `az vm open-port` creates **NSG rules** on the NIC’s NSG.  
   - **Priority** determines rule order (lower = higher precedence). Priorities must be unique (100–4096).

3) **Lightweight Web Hosting on a VM**  
   - Nginx install + a single HTML file is enough to validate end-to-end connectivity.  
   - Always open only the ports you need (start with SSH 22; add 80/443 when ready).

4) **Cost Guardrails**  
   - Compute billed when **running**; disk/IP may incur costs at rest.  
   - Use **auto-shutdown** or **deallocate** to limit spend in labs.

---

## Code Implementation

### A) Configure Context (PowerShell)
```powershell
# Inspect context
az account show -o table
az group list -o table
az configure --list-defaults

# Set helpful defaults (so -g/-l aren’t needed each time)
az configure --defaults group="rg-lab-core-uks-01" location="uksouth"
```

### B) Create the VM
```powershell
az vm create `
  --resource-group rg-lab-core-uks-01 `
  --name vm-ubuntu-uks-01 `
  --image Ubuntu2204 `
  --admin-username azureuser `
  --size Standard_B1s `
  --generate-ssh-keys

# Capture the public IP (optional)
az vm show -d -g rg-lab-core-uks-01 -n vm-ubuntu-uks-01 --query publicIps -o tsv
```

### C) Open HTTP/HTTPS with Explicit Priorities
```powershell
az vm open-port -g rg-lab-core-uks-01 -n vm-ubuntu-uks-01 --port 80  --priority 1010
az vm open-port -g rg-lab-core-uks-01 -n vm-ubuntu-uks-01 --port 443 --priority 1011
```

### D) SSH to the VM & Install Nginx
```powershell
# Windows OpenSSH (uses default key at $HOME\.ssh\id_rsa)
ssh -i $HOME\.ssh\id_rsa azureuser@<PUBLIC_IP>

# On the VM shell
sudo apt-get update -y
sudo apt-get install -y nginx
sudo systemctl enable --now nginx

# Replace default landing page
echo "<h1>Hello from $HOSTNAME (Nginx on Azure)</h1>" | sudo tee /var/www/html/index.nginx-debian.html
```

### E) Verify
- Browser → `http://<PUBLIC_IP>` → page renders “Hello from vm-ubuntu-uks-01 (Nginx on Azure)”.

---

## Practice Tasks
1. Restrict SSH (22) to your current public IP in the NSG rule source filter.  
2. Attach a 20 GB data disk, format, and mount at `/data`; make it persistent via `/etc/fstab`.  
3. Assign a DNS label to the public IP and browse with `http://<label>.<region>.cloudapp.azure.com`.  
4. Install **Certbot** and configure a free TLS cert (requires a real domain pointing to the IP).  
5. Export this VM as an **ARM template** from the Portal and read through the JSON to map settings to CLI flags.

---

## Lessons Learned
- Saving **defaults** for group/location reduces mistakes and speeds up workflows.  
- **NSG priorities** matter: lower numbers win and must be unique.  
- Minimal viable web hosting on a VM is a great way to validate networking end-to-end.  
- Keep lab costs low with **deallocate**/**auto-shutdown** and delete unused resources promptly.

---
