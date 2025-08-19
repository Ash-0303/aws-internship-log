# Day 31 – Compute Engine VM + SSH + HTTP Test

**Date:** 16th August 2025  
**Internship Role:** Cloud Support Engineer Intern (Prime One Global)  
**Platform:** Google Cloud (Windows 11 + PowerShell)

---

## Task Summary
- Confirmed active config: **project `prime-01-469403`**, **region `europe-west2`**, **zone `europe-west2-a`**.
- Verified **Compute Engine API** is enabled.
- Created a Debian 12 VM (**e2-medium**) with the `http-server` tag.
- Opened HTTP via a firewall rule.
- SSH’d into the VM, installed **NGINX**, and validated the default page from the **external IP**.
- Captured screenshots for each step.

---

## Key Concepts Learned
1) **GCE building blocks** – project ↔ region ↔ zone; machine types; images & image families.  
2) **Access patterns** – SSH key generation via `gcloud`, host key verification, and non-interactive commands.  
3) **Network exposure** – using instance **tags** + **firewall rules** to control inbound traffic (HTTP on tcp:80).  
4) **Service management** – `systemctl` to start/stop/enable services; verifying with `systemctl status` and `curl`.

---

## Code Implementation

### A) Verify Config
```powershell
gcloud config list
```
**Result:**  
- region: `europe-west2`  
- zone: `europe-west2-a`  
- project: `prime-01-469403`

### B) Check/Enable Required Service
```powershell
gcloud services list       # confirmed compute.googleapis.com present
# (If needed) gcloud services enable compute.googleapis.com --project prime-01-469403
```

### C) Create VM
```powershell
gcloud compute instances create my-vm `
  --project=prime-01-469403 `
  --zone=europe-west2-a `
  --machine-type=e2-medium `
  --image-family=debian-12 `
  --image-project=debian-cloud `
  --tags=http-server
```
**Result:** `my-vm` running.  
**Internal IP:** `10.154.0.2` **External IP:** `35.246.2.201`

### D) Allow HTTP (tcp:80) to Tagged VM
```powershell
gcloud compute firewall-rules create allow-http `
  --project=prime-01-469403 `
  --allow=tcp:80 `
  --target-tags=http-server `
  --description="Allow HTTP to tagged instances"
```

### E) Get External IP (for browser test)
```powershell
gcloud compute instances describe my-vm --zone=europe-west2-a `
  --format="get(networkInterfaces[0].accessConfigs[0].natIP)"
```
**Output:** `35.246.2.201`

### F) SSH & Install Web Server
```powershell
gcloud compute ssh my-vm --zone=europe-west2-a
```
_On the VM:_
```bash
sudo apt-get update && sudo apt-get install -y nginx
systemctl status nginx
```
**Result:** `Active: active (running)` and NGINX default page accessible at  
`http://35.246.2.201` (verified in browser).

### G) Cleanup (avoid charges)
```powershell
# when finished for the day
# gcloud compute instances delete my-vm --zone=europe-west2-a
# gcloud compute firewall-rules delete allow-http
```

---

## Practice Tasks
1. Recreate the VM with a **startup script** that installs NGINX automatically; confirm idempotency.  
2. Remove the `http-server` tag and instead open port 80 with a **narrow source range** (e.g., your IP).  
3. Reserve a **static external IP**, attach to the VM, and test persistence across restarts.  
4. Use `gcloud compute ssh --command "..."` to stop/start nginx without an interactive shell.  
5. Snapshot the boot disk and launch a new VM **from snapshot**.  
6. Enable **OS Login** and test access vs. project-wide SSH keys.

---

## Lessons Learned
- Tags + firewall rules are the cleanest way to manage public access to a VM.  
- `gcloud` generates and propagates SSH keys automatically; first connection may take ~30s.  
- Always verify service state with `systemctl` and confirm reachability from the client (browser/`curl`).  
- Keep a cleanup routine to prevent idle resource costs.
