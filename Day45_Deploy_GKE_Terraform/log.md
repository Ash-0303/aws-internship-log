# Day 45: Provision GKE with Terraform + Validate via GitHub Actions

**Date:** 9th September 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Terraform (GKE) • Google Cloud SDK • kubectl • WSL/Ubuntu

---

## Task Summary
- Installed Terraform locally on WSL (ARM64 build) and verified **gcloud** project/region/zone.
- Confirmed required Google Cloud services (Compute, Kubernetes Engine) are available.
- Authored minimal Terraform for a **regional** GKE cluster + managed node pool.
- Ran `terraform init → plan → apply` to create the cluster.
- Configured `kubectl` with `gcloud container clusters get-credentials` and verified nodes/pods.
- Cleaned up with `terraform destroy`.

---

## 1) Initial Setup

_Install Terraform (ARM64) and verify_
```bash
# Install dependencies
sudo apt update && sudo apt install -y wget unzip

# Download Terraform 1.9.5 (ARM64)
wget https://releases.hashicorp.com/terraform/1.9.5/terraform_1.9.5_linux_arm64.zip

# Unzip and move to PATH
unzip terraform_1.9.5_linux_arm64.zip
sudo mv terraform /usr/local/bin/

# Check version
terraform version
```

_Verify gcloud configuration_
```bash
gcloud config list
# region: europe-west2
# zone:   europe-west2-a
# project: prime-01-469403
```
(Optional) ensure required services are enabled:
```bash
# View services
gcloud services list
# If needed, enable:
# gcloud services enable container.googleapis.com compute.googleapis.com
```

---

## 2) Create Terraform files

_Structure_
```
gke-terraform/
├── main.tf
├── outputs.tf
├── variables.tf
├── versions.tf
└── terraform.tfvars
```

**versions.tf**
```hcl
terraform {
  required_version = ">= 1.5.0"
  required_providers {
    google = {
      source  = "hashicorp/google"
      version = ">= 5.0"
    }
  }
}
```

**variables.tf**
```hcl
variable "project_id"   { type = string  default = "prime-01-469403" }
variable "region"       { type = string  default = "europe-west2" }
variable "cluster_name" { type = string  default = "my-gke-cluster" }
variable "machine_type" { type = string  default = "e2-medium" }
variable "node_count"   { type = number  default = 1 }
```

**main.tf**
```hcl
provider "google" {
  project = var.project_id
  region  = var.region
}

resource "google_container_cluster" "primary" {
  name                     = var.cluster_name
  location                 = var.region
  remove_default_node_pool = true
  initial_node_count       = 1 # required by API, ignored when removing default
}

resource "google_container_node_pool" "primary_nodes" {
  name       = "primary-nodes"
  cluster    = google_container_cluster.primary.name
  location   = var.region
  node_count = var.node_count

  node_config {
    machine_type = var.machine_type
  }
}
```

**outputs.tf**
```hcl
output "cluster_name" { value = google_container_cluster.primary.name }
output "location"     { value = google_container_cluster.primary.location }
```

**terraform.tfvars**
```hcl
project_id   = "prime-01-469403"
region       = "europe-west2"
cluster_name = "my-gke-cluster"
machine_type = "e2-medium"
node_count   = 3
```

---

## 3) Initialise, plan and apply

_Apply_
```bash
cd gke-terraform

terraform init
terraform plan

# Create the cluster
terraform apply -auto-approve
```
**Expected outputs**
```
Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

Outputs:
cluster_name = "my-gke-cluster"
location     = "europe-west2"
```

---

## 4) Configure kubectl and verify

_Fetch kubeconfig & check nodes/pods_
```bash
gcloud container clusters get-credentials my-gke-cluster   --region=europe-west2   --project=prime-01-469403

kubectl get nodes
kubectl get pods -A
```
You should see three ready worker nodes and core system pods running (metrics-server, kube-proxy, gke-metrics-agent, etc.).

---

## 5) Cleanup

_Destroy all resources created by Terraform_
```bash
terraform destroy -auto-approve
```
**Expected**: `Destroy complete! Resources: 2 destroyed.`

---

## Lessons Learned
- For **regional** GKE, use `location = var.region` and the `--region` flag when fetching credentials.
- Remove default node pool and manage pools explicitly for cleaner lifecycle and CI workflows.
- Always run `terraform init` before `validate/plan/apply`; lock your provider to a sensible version range.
- Keep a simple `outputs.tf` to confirm values quickly after apply.
