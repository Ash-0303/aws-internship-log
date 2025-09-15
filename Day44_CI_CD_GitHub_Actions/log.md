# Day 44: CI/CD for Terraform with GitHub Actions

**Date:** 8th September 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** GitHub Actions • Terraform (GKE & AKS) • Windows (Git Bash + VS Code)

---

## Task Summary
- Created a new **private** GitHub repository: `week8-cicd-iac`.
- Imported a **starter kit** (ZIP) with Terraform, scripts, and prebuilt workflows.
- Initialised local Git repo, committed starter kit, pushed to GitHub `main`.
- Implemented a **CI workflow** that validates Terraform for **GKE** and **AKS** on both `push` and `pull_request` events.
- Fixed the workflow by running **terraform init** (with `-backend=false`) **before** `terraform validate`.
- Opened a **feature branch** and a **Pull Request**; confirmed both checks passed; merged the PR.

---

## 1) Create repo & unpack starter kit

Steps:
- New repo on GitHub → **week8-cicd-iac** (private).
- Unzip starter bundle (`week8-cicd-iac-starter.zip`) containing:
  - `.github/workflows/ci.yml`, `terraform-gke.yml`, `terraform-aks.yml`
  - `terraform/gke/*`, `terraform/aks/*`
  - `scripts/*`, `k8s/deploy-nginx.yaml`, `day4/README.md`

_Apply_
```bash
unzip week8-cicd-iac-starter.zip
cd week8-cicd-iac-starter

git init
git add .
git commit -m "chore: add starter kit"
```
Verify: files are staged and committed (19 files created as per starter kit).

---

## 2) Push to GitHub (main)

_Apply_
```bash
git branch -M main
git remote add origin https://github.com/Ash-0303/week8-cicd-iac.git
git push -u origin main
```
Verify in the web UI that `.github/workflows/` shows `ci.yml`, `terraform-aks.yml`, `terraform-gke.yml`.

---

## 3) CI workflow to validate Terraform (on push & PR)

_Save as: `.github/workflows/ci.yml`_

```yaml
name: CI

on:
  push:
    branches: ['main']
  pull_request:
    branches: ['main']

jobs:
  terraform-validate-gke:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up Terraform
        uses: hashicorp/setup-terraform@v2
      - name: Validate GKE Terraform
        run: |
          terraform -chdir=terraform/gke init -backend=false
          terraform -chdir=terraform/gke validate

  terraform-validate-aks:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up Terraform
        uses: hashicorp/setup-terraform@v2
      - name: Validate AKS Terraform
        run: |
          terraform -chdir=terraform/aks init -backend=false
          terraform -chdir=terraform/aks validate
```
Notes
- `setup-terraform` installs the CLI.
- `-backend=false` lets `init` run safely in CI without touching remote state.
- Keeping GKE and AKS in **separate jobs** gives parallel, clear results.

_Upgrade and verify_
```bash
# After editing ci.yml:
git add .github/workflows/ci.yml
git commit -m "fix: add terraform init before validate in workflows"
git push
```
Verify: **Actions → CI** shows a successful run for the push to `main` (2 green jobs).

---

## 4) Make a PR and see CI run on PRs (5–10 min)

_First create a feature branch and push it_

```bash
git checkout -b feat/readme-tidy
# (Edit README.md as needed)
git add README.md
git commit -m "Version 2 - Let it go"
git push origin feat/readme-tidy
```

_Open a Pull Request_  
- Compare `feat/readme-tidy` → `main`.  
- Observe two checks: `terraform-validate-aks (pull_request)` and `terraform-validate-gke (pull_request)` pass.  
- Merge the PR once checks are green.

---

## Lessons Learned
- Always **init** Terraform in CI before `validate` (use `-backend=false` for safety).
- Splitting validation by stack (GKE vs AKS) gives faster, clearer feedback.
- Triggers on both **push** and **pull_request** provide early and pre‑merge validation.
- Short, deterministic CI steps keep runs under ~30s per job and improve developer feedback.
