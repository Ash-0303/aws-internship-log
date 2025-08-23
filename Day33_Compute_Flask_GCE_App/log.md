# Day 33 – Compute Engine VM + Flask (GCE)

**Date:** 19th Aug 2025  
**Internship Role:** Cloud Support Engineer Intern (Prime One Global)  
**Platform:** Google Cloud (Ubuntu on WSL)

---

## Task Summary
- Verified active `gcloud` configuration (project, region, zone).  
- Enabled **Compute Engine API** (already enabled in my project).  
- Created a VM `flask-vm` (Ubuntu 22.04, `e2-micro`) in `europe-west2-a`.  
- Opened HTTP **port 80** (future‑proofing for Nginx) and created an extra firewall rule for **port 5000**.  
- SSH’d into the VM and installed Python tooling.  
- Cloned the **pallets/flask** repo and deployed the `flaskr` tutorial app.  
- Debugged connectivity: realised I was using the **internal** IP and port **5000** wasn’t open; fixed with a firewall rule + network tag and used the **external** IP.  
- Confirmed the app was reachable at `http://34.39.46.195:5000` (ephemeral external IP).  
- Documented cleanup commands to avoid charges.

---

## Key Concepts Learned
- **Internal vs External IPs:** Internal (`10.x.x.x`) is only reachable inside the VPC; use external IP for public access.  
- **Firewall rules & network tags:** Rules only apply to instances that **have the tag** referenced by the rule.  
- **GCE + Flask basics:** Create VM → install deps → app → run → expose through firewall (or via Nginx on 80).  
- **Package management:** `python3-venv`/`pip`, editable installs (`pip install -e .`).  
- **Safer serving:** Development server is for testing; production should use **Gunicorn + Nginx** (planned next).

---

## Code Implementation

### A) Verify Config

```bash
gcloud config list
```

**Result:**  
```
[compute]
region = europe-west2
zone   = europe-west2-a
[core]
account = ashwinram44@gmail.com
project = prime-01-469403
```

---

### B) Enable/Check Required Service

```bash
gcloud services enable compute.googleapis.com
```

---

### C) Create VM

```bash
gcloud compute instances create flask-vm \
  --zone=europe-west2-a \
  --machine-type=e2-micro \
  --image-family=ubuntu-2204-lts \
  --image-project=ubuntu-os-cloud \
  --tags=http-server
```

**Result:**  
VM running. Example IPs shown by GCP at creation time:  
- **Internal IP:** `10.154.0.15`  
- **External IP:** `34.39.46.195`

---

### D) Allow HTTP (tcp:80) to Tagged VM (for later Nginx use)

```bash
gcloud compute firewall-rules create allow-http \
  --allow tcp:80 \
  --target-tags=http-server \
  --quiet || true
```

---

### E) Open Port 5000 (Flask dev server) and Tag the VM

```bash
# Create firewall rule bound to tag "flask-5000"
gcloud compute firewall-rules create allow-flask-5000 \
  --allow tcp:5000 \
  --target-tags=flask-5000

# Apply the tag to the VM
gcloud compute instances add-tags flask-vm \
  --zone=europe-west2-a \
  --tags=flask-5000
```

Check tags on the instance:
```bash
gcloud compute instances describe flask-vm \
  --zone=europe-west2-a \
  --format="get(tags.items)"
```

---

### F) SSH & Install Web Stack + Deploy Flaskr

```bash
# SSH
gcloud compute ssh "flask-vm" --zone="europe-west2-a"
```

On the VM:
```bash
# Packages
sudo apt update
sudo apt install -y python3-venv python3-pip git

# Get example app
git clone https://github.com/pallets/flask.git
cd flask/examples/tutorial

# Virtual env + install
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -e .

# Init DB and run dev server
flask --app flaskr init-db
flask --app flaskr run --host=0.0.0.0 --port=5000
```

Test locally from the VM:
```bash
curl http://127.0.0.1:5000
```

---

### G) Browser Test (from my machine)
```
http://34.39.46.195:5000
```
**Result:** Reached the **Flaskr** page (Posts/Register/Login visible).

---

## Problem Encountered & Fix

### Symptom
- Browsing to `http://10.154.0.15:5000` timed out.

### Root Causes
1) **Wrong IP** – `10.154.0.15` is the **internal** IP, not accessible from the public internet.  
2) **Firewall not open** – Port **5000** wasn’t allowed until I created the rule and tagged the VM.

### Resolution
- Created a firewall rule `allow-flask-5000` and applied tag `flask-5000` to the VM:  
  ```bash
  gcloud compute firewall-rules create allow-flask-5000 --allow tcp:5000 --target-tags=flask-5000
  gcloud compute instances add-tags flask-vm --zone=europe-west2-a --tags=flask-5000
  ```
- Used the **external** IP: `http://34.39.46.195:5000` → site loads successfully.

**Notes:** For production, prefer terminating on port 80/443 via Nginx and serving with Gunicorn.

---

## Cleanup (avoid charges)

```bash
# Delete VM
gcloud compute instances delete flask-vm --zone=europe-west2-a

# Remove demo firewall rules (optional)
gcloud compute firewall-rules delete allow-flask-5000 --quiet
gcloud compute firewall-rules delete allow-http --quiet
```

---

## Practice Tasks
1. Rebuild the VM and wire **Nginx + Gunicorn** to serve the app on **port 80**.  
2. Replace the ephemeral external IP with a **static** regional address and attach a DNS A record.  
3. Add a **systemd** unit for Gunicorn so the app starts on boot.  
4. Use a **startup script** to fully automate VM provisioning.  
5. Harden the instance: least‑privilege service account, OS patching, and UFW if needed.

---

## Lessons Learned
- **Network tags** are how firewall rules are scoped to instances. No tag → rule won’t apply.  
- Always check **external vs internal** IPs when testing from the internet.  
- Flask’s built‑in server is great for development only; use **Gunicorn + Nginx** in production.  
- Keep cleanup commands handy to prevent unwanted costs.
