# Day 46: Deploy a Public Cloud Run Service with Terraform

**Date:** 10th September 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Google Cloud Run • Terraform • gcloud (WSL/Ubuntu)

---

## Task Summary
- Cloned the official **terraform-google-cloud-run** repo example.
- Verified active **gcloud** project/region/zone and enabled the **Cloud Run API**.
- Created `terraform.tfvars` with my project, region, service name, and container image.
- Ran `terraform init → plan → apply` to provision a **Cloud Run** service.
- Retrieved the **service URL** and made the service **public** with `roles/run.invoker` for `allUsers`.

---

## 1) Get the example module

_Save to home and enter repo_
```bash
git clone https://github.com/GoogleCloudPlatform/terraform-google-cloud-run.git
cd terraform-google-cloud-run/examples/simple_cloud_run
```
(Repo root is also visible one level up in `$HOME/terraform-google-cloud-run`.)

---

## 2) Confirm gcloud config & enable API

_Check current config_
```bash
gcloud config list
# project: prime-01-469403
# region:  europe-west2
# zone:    europe-west2-a
```

_Enable Cloud Run API (if not already)_
```bash
gcloud services enable run.googleapis.com
```

---

## 3) Configure Terraform variables

_Create `terraform.tfvars` in the example directory_
```hcl
project_id   = "prime-01-469403"
location     = "europe-west2"   # your preferred region
service_name = "hello-run"
image        = "gcr.io/cloudrun/hello"
```

---

## 4) Initialise, plan and apply

_Commands_
```bash
terraform init

# Create and save a plan file (as shown in the screenshots)
terraform plan -out plan.tfplan

# Apply the saved plan
terraform apply plan.tfplan
```

_Expected apply output (abridged)_
```
Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Outputs:
service_name      = "hello-run"
service_status    = "Ready"
location          = "europe-west2"
project_id        = "prime-01-469403"
service_url       = "https://<generated>.run.app"
apphub_service_uri = { ... }
```

---

## 5) Verify and make the service public

_List services and capture URL_
```bash
gcloud run services list
```

_Grant public invoker access_
```bash
gcloud run services add-iam-policy-binding hello-run   --region europe-west2   --member="allUsers"   --role="roles/run.invoker"
```
This allows anyone with the URL to invoke the service.

---

## (Optional) Clean up
```bash
terraform destroy -auto-approve
```

---

## Lessons Learned
- Cloud Run deploys quickly with Terraform; the minimal inputs are **project**, **location**, **service_name**, and **image**.
- Saving a plan (`plan.tfplan`) then applying it is a clean two-step workflow for reviewable changes.
- Public access requires explicit IAM: grant **roles/run.invoker** to **allUsers** (or a narrower principal for restricted apps).
- The `service_url` output gives you the ready-to-use endpoint immediately after apply.
