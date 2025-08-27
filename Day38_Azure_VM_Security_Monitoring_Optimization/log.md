# Day 38 – Azure VM Security, Optimization & Monitoring (Windows PowerShell)

**Date:** 25th Aug 2025  
**Internship Role:** Cloud Support Engineer Intern (Prime One Global)  
**Platform:** Microsoft Azure (Windows 11 + PowerShell)

---

## Task Summary
- Removed the **default SSH allow** rule and added a new **SSH-only-from-my-IP** rule; opened **HTTP/HTTPS** only when needed.  
- **Hardened SSH** on the VM by disabling password authentication and root login; validated and restarted `sshd`.  
- Enabled a **host firewall (UFW)** with deny-incoming / allow-outgoing and OpenSSH allowed.  
- Set up **Azure Monitor** by creating a **Log Analytics workspace (UK South)** and configuring **VM Insights via a Data Collection Rule (DCR)**.  
- Reviewed **right-sizing** options post-provisioning.

---

## Key Concepts Learned
1) **Network Security Groups (NSGs)** – tighten inbound access by removing permissive defaults and allowing only required ports (22/80/443).  
2) **SSH Hardening** – prefer key-based access; set `PasswordAuthentication no` and `PermitRootLogin no`.  
3) **Host Firewall** – layer UFW with NSGs for defense-in-depth.  
4) **Monitoring Pipeline (Modern)** – **Azure Monitor Agent (AMA) + DCR + Log Analytics**; VM and DCR must be in the **same region**.  
5) **Right-sizing** – assess VM sizes after initial deployment to balance cost and performance.

---

## Code Implementation

### A) NSG – Remove default SSH & add least-privilege rules (PowerShell + Azure CLI)
```powershell
$RG  = "rg-lab-core-uks-01"
$VM  = "vm-node-ubuntu"
$NIC = (az vm show -g $RG -n $VM --query "networkProfile.networkInterfaces[0].id" -o tsv).Split("/")[-1]
$NSG = (az network nic show -g $RG -n $NIC --query "networkSecurityGroup.id" -o tsv).Split("/")[-1]
$MYIP = (Invoke-WebRequest -UseBasicParsing -Uri "https://ifconfig.me").Content

# Remove permissive default SSH (if present)
az network nsg rule delete -g $RG --nsg-name $NSG -n default-allow-ssh 2>$null

# SSH from my IP only
az network nsg rule upsert -g $RG --nsg-name $NSG -n Allow-SSH-MyIP `
  --priority 100 --access Allow --protocol Tcp --direction Inbound `
  --source-address-prefixes $MYIP --destination-port-ranges 22

# Web ports (only if needed)
az network nsg rule upsert -g $RG --nsg-name $NSG -n Allow-HTTP  --priority 110 --access Allow --protocol Tcp --direction Inbound --destination-port-ranges 80
az network nsg rule upsert -g $RG --nsg-name $NSG -n Allow-HTTPS --priority 120 --access Allow --protocol Tcp --direction Inbound --destination-port-ranges 443
```

### B) SSH Hardening on the VM
```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
sudo sed -i 's/^#\?PasswordAuthentication .*/PasswordAuthentication no/' /etc/ssh/sshd_config
sudo sed -i 's/^#\?PermitRootLogin .*/PermitRootLogin no/' /etc/ssh/sshd_config
sudo systemctl restart sshd
sudo sshd -t && echo "sshd config OK"
```

### C) Host Firewall (UFW)
```bash
sudo apt update
sudo apt install -y ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow OpenSSH
# If you’ll host a site:
# sudo ufw allow 80/tcp
# sudo ufw allow 443/tcp
yes | sudo ufw enable
sudo ufw status verbose
```

### D) Monitoring – Log Analytics + DCR (VM Insights)
```powershell
# Create workspace in UK South
az monitor log-analytics workspace create `
  -g rg-lab-core-uks-01 `
  -n law-rg-lab-core-uks-01 `
  -l uksouth
```
Portal steps:
1. **Create Data Collection Rule** in **UK South** (Platform: **Linux** or **All**).  
2. **Resources** → **Add** the VM.  
3. **Collect and deliver** → send to **law-rg-lab-core-uks-01**.  
4. Confirm **VM Insights** shows metrics after the rule applies.

### E) Right-size (post-provision)
```powershell
az vm list-vm-resize-options -g rg-lab-core-uks-01 -n vm-node-ubuntu -o table
```

---

## Practice Tasks
1. Re-run the DCR wizard with **Platform type = Linux**, verify the VM is selectable under **Resources**, and confirm Insights charts populate.  
2. Reproduce the **delete default SSH → create SSH-from-my-IP** flow on a new VM and verify with a fresh SSH session.  
3. Toggle UFW rules to add/remove **80/443** and confirm reachability from a browser.

---

## Lessons Learned
- Replace permissive defaults with **least-privilege** NSG rules.  
- Lock down SSH at the service level and keep a backup of `sshd_config`.  
- For VM Insights, ensure **workspace + DCR** configuration matches the VM region; otherwise the VM won’t appear in the selector.
