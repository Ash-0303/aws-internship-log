# Day 30 – Google Cloud Setup & First Cloud Run Deploy

**Date:** 15th August 2025  
**Internship Role:** Cloud Support Engineer Intern (Prime One Global)  
**Platform:** Google Cloud (Windows 11 • PowerShell)

---

## Task Summary
- Selected project **Prime-01** (`prime-01-469403`) in Google Cloud Console.
- Installed the Google Cloud SDK via Chocolatey and initialized `gcloud`.
- Authenticated account and set defaults: **region** `europe-west2`, **zone** `europe-west2-a`.
- Enabled/verified key services: Compute, Cloud Run, Cloud Build, Artifact Registry.
- Deployed sample **Hello World** container to **Cloud Run** and verified the public URL.

---

## Key Concepts Learned

### 1) Projects & Billing
- All resources live inside a **project**; commands use the **Project ID**, not the name.
- Many services prompt to enable APIs on first use (e.g., Compute, Cloud Run).

### 2) `gcloud` Configuration
- Use `gcloud config set` to avoid repeating flags.  
  Defaults stored: project, region, zone.  
- Check the active config with `gcloud config list`.

### 3) Cloud Run Basics
- Fully managed, HTTP-only, stateless containers with **scale-to-zero** and per‑request billing.
- Public endpoints require `--allow-unauthenticated`; otherwise access is controlled with IAM.
- Each deployment creates a **revision**; traffic can be split/rolled back.

---

## Code Implementation

### A) Install & Initialize SDK (Windows PowerShell)
```powershell
# Install (Chocolatey)
choco install gcloudsdk -y

# Start login & guided setup
gcloud init
# (Signed in as ashwinram44@gmail.com)

# Set defaults
gcloud config set project prime-01-469403
gcloud config set compute/region europe-west2          # will enable Compute API if needed
gcloud config set compute/zone europe-west2-a

# Verify & update
gcloud --version
gcloud config list
gcloud info
gcloud components update
```

### B) Enable / Verify Required Services
```powershell
# Enable (if not already)
gcloud services enable run.googleapis.com cloudbuild.googleapis.com artifactregistry.googleapis.com

# List enabled services (snippet includes Run, Build, Artifact Registry, Compute, IAM, Logging, Monitoring)
gcloud services list --enabled
```

### C) Deploy “Hello World” to Cloud Run
```powershell
# Deploy Google’s sample image
gcloud run deploy "hello-$env:USERNAME" `
  --image gcr.io/cloudrun/hello `
  --platform managed `
  --allow-unauthenticated `
  --region europe-west2

# Get the URL later if needed
gcloud run services describe "hello-$env:USERNAME" --region europe-west2 --format="value(status.url)"
```

**Result observed:**  
`Service [hello-ashwi] ... Service URL: https://hello-ashwi-659646024029.europe-west2.run.app`

### D) Cleanup (to avoid charges)
```powershell
gcloud run services delete "hello-$env:USERNAME" --region europe-west2
# Optional: delete any test Artifact Registry repos/images created later
```

---

## Practice Tasks
1. **Private Service:** redeploy without `--allow-unauthenticated` and test with IAM.
2. **Env Vars:** deploy with `--set-env-vars GREETING=Hi` and read it from app code.
3. **Revisions & Rollback:** deploy a second revision, split traffic (e.g., 90/10), then roll back.
4. **Artifact Registry:** build & push a custom image with Cloud Build, then deploy from your registry.

---

## Lessons Learned
- The correct syntax is **`gcloud config set …`** (there is no top‑level `gcloud set`).  
- Setting a default region may prompt to **enable the Compute API**—approve to proceed.  
- Always verify the active project/region/zone before deploying.  
- Cloud Run handles revisions and traffic automatically; confirm with `gcloud run services describe`.  
- Clean up unused services and images to keep environments tidy and costs minimal.