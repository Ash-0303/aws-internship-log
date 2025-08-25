# Day 34 — Azure Setup & First Resource Group

**Date:** 20th August 2025  
**Internship Role:** Cloud Support Engineer Intern (Prime One Global)  
**Platform:** Microsoft Azure (Windows 11 + PowerShell)

---

## Task Summary
- Accessed the Azure Portal and verified active directory/tenant and *Azure subscription 1*.
- Installed **Azure CLI** on Windows via Chocolatey and verified the CLI version.
- Authenticated with `az login` and confirmed the correct subscription context.
- Set a default **region** to `uksouth`.
- Created a tagged **resource group** `rg-lab-core-uks-01` in UK South.
- Verified the resource group from both **CLI** and **Portal**.

---

## Key Concepts Learned

### 1) Subscriptions & Entra ID (Directory)
- A **directory (tenant)** holds identities (users, service principals) and policies; a **subscription** is the billing & RBAC boundary.
- The CLI must target the **correct subscription** before provisioning resources.
- A user may belong to multiple directories; switching directories changes what subscriptions are visible.

### 2) Resource Groups (RGs)
- Logical containers for resources with a shared **lifecycle** (create/update/delete together).
- The **location** of an RG stores metadata; service resources may have their own regions.
- **Tags** at RG-level are invaluable for cost allocation and ownership tracking.

### 3) Azure CLI Basics & Defaults
- `az login` authenticates; `az account list/show/set` manages the active subscription context.
- `az configure --defaults location=<region>` persists a default region to reduce errors/typing.
- Use `-o table` for concise, human-readable output.

### 4) Naming & Tagging
- Consistent naming helps ops and automation: `rg-<workload>-<region>-<nn>` → `rg-lab-core-uks-01`.
- Apply tags at creation time: `Environment=Lab`, `Owner=Ashwin Ram`, `CostCenter=Training`.

---

## Code Implementation

### A) Install & Verify Azure CLI (Windows PowerShell)
```powershell
# Run in an elevated PowerShell (Admin)
choco install azure-cli -y

# Confirm version
az --version
```

---

### B) Sign In & Select Subscription
```powershell
# Interactive browser/device-code login
az login

# (Optional) list available subscriptions
az account list -o table

# If needed, explicitly set the subscription
az account set --subscription "Azure subscription 1"

# Show the active context
az account show -o table
```

---

### C) Set Defaults & Create Resource Group
```powershell
# Save a default location for future commands
az configure --defaults location=uksouth

# Create the resource group with tags
az group create `
  --name rg-lab-core-uks-01 `
  --location uksouth `
  --tags "Environment=Lab" "Owner=Ashwin Ram" "CostCenter=Training"
```

---

### D) Verify from CLI & Portal
```powershell
# CLI verification
az group list -o table
```

Portal check (no commands): Navigate to **Resource groups** and confirm `rg-lab-core-uks-01` appears in **UK South** with your subscription.

---

### E) (Optional) Clean Up
```powershell
# WARNING: deletes everything in the group
az group delete -n rg-lab-core-uks-01 --yes --no-wait
```

---

## Practice Tasks
1. **Budgets:** In Cost Management, create a £10 monthly budget with email alerts at 80% and 100%.
2. **Service Principal:** Create a least-privileged service principal scoped to the RG for automation and store credentials securely.
3. **Smoke Test Resource:** Create and then delete a Standard_LRS Storage Account in the RG via CLI.
4. **Tag Hygiene:** Add a new tag `Project=Azure101` to the RG and confirm via `az group show --query tags`.
5. **Docs Deep-Dive:** Read about paired regions; list available locations with `az account list-locations -o table`.

---

## Lessons Learned
- Azure separates **identity (directory/tenant)** from **billing (subscription)**—always verify context before provisioning.
- Setting a default region with `az configure` prevents accidental cross-region deployments.
- **Tags** are essential for cost tracking and ownership; add them at creation time.
- Double-checking from both **CLI** and **Portal** ensures actions are reflected and permissions are correct.
- Consistent naming conventions make environments easier to search, script, and support.
