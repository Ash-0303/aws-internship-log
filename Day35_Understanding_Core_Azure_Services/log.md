# Day 35 — Understanding Core Azure Services (VMs & AKS)

**Date:** 21st August 2025  
**Internship Role:** Cloud Support Engineer Intern (Prime One Global)  
**Platform:** Microsoft Azure (Portal + Windows 11 PowerShell)

---

## Task Summary
- Navigated **Create a resource → Compute** in the Azure Portal.
- Provisioned a Linux VM **vm-ubuntu-uks-01** in resource group **rg-lab-core-uks-01** (Region: **UK South**).
- Used **SSH key** authentication; enabled minimal inbound access (**SSH 22**) during creation.
- Configured **OS disk** as Standard SSD and enabled **auto-shutdown** (cost guardrail).
- Connected via **SSH**, updated packages, installed **Nginx**, and published a simple landing page.
- Opened **HTTP (80)** inbound to validate the web server.
- Reviewed VM blades: Overview, Networking/NSG, Disks, Monitoring, and Identity.
- Studied **AKS fundamentals** and contrasted **when to use VMs vs AKS** for workloads.
- Documented cleanup steps (deallocate/delete) to avoid unexpected charges.

---

## Core Concepts: VMs & AKS (Mental Model + When to Use)
**Azure Virtual Machines (VMs)**  
- General-purpose compute (like AWS EC2). You choose **image**, **size**, **disk**, **vNet/subnet**, **NSG**, and **identity**.  
- Billed while **running** (compute); disks/IPs can incur charges even when stopped.  
- Best for: single servers, pets/legacy apps, custom OS control, long-running services, jump hosts.

**Azure Kubernetes Service (AKS)**  
- Managed Kubernetes: Microsoft operates the **control plane**; you manage **node pools** (VMs).  
- Brings rolling updates, self-healing, autoscaling, service discovery, and rich ecosystem (ingress, CSI, CNI).  
- Best for: microservices, multiple containers, CI/CD pipelines, high availability, elastic scaling.

**Choosing VM vs AKS**  
- **Choose a VM** when you need a single workload, custom OS control, or you’re learning basics.  
- **Choose AKS** when you have many containerized services, need orchestrated deployments, or autoscaling.  
- Middle ground: **App Service** / **Container Apps** for simpler container hosting without full Kubernetes.

---

## Key Configuration Notes (What I Deployed)
- **Resource group:** `rg-lab-core-uks-01` (tags: `Environment=Lab`, `Owner=Ashwin Ram`, `CostCenter=Training`)  
- **VM name:** `vm-ubuntu-uks-01`  
- **Region:** `UK South (uksouth)`  
- **Image:** Ubuntu Server 24.04 LTS (Gen2)  
- **Size:** small general purpose (e.g., Standard D2s v3 or Standard B1s for labs)  
- **Auth:** SSH public key (`azureuser`)  
- **Networking:** new vNet + subnet; Public IP enabled; NSG allowing SSH (22) initially  
- **Disks:** OS disk = Standard SSD; Delete with VM = **enabled**  
- **Management:** Auto-shutdown enabled; Boot diagnostics enabled

---

## Code Implementation

### A) Connect via SSH (Windows/macOS/Linux)
```bash
# Ensure the private key permissions are correct on *nix
chmod 600 ./vm-ubuntu-uks-01_key.pem

# Connect
ssh -i ./vm-ubuntu-uks-01_key.pem azureuser@<PUBLIC_IP>
```

---

## Practice Tasks
1. **Harden SSH:** Restrict inbound SSH to your current public IP (source filter on NSG).  
2. **Add Data Disk:** Attach a 20 GB Standard SSD; partition/format and mount at `/data`.  
3. **Resize VM:** Stop the VM and change to `Standard_B2s`; verify `lscpu` and `free -m`.  
4. **DNS Label:** Assign a DNS label to the public IP and browse `http://<label>.<region>.cloudapp.azure.com`.  
5. **AKS Exploratory:** Create a no-cost draft by walking through the AKS wizard to understand options (cancel before create).  
6. **CLI Proficiency:** Recreate the VM purely with CLI, tagging it at creation time.  
7. **Cost Check:** Create a monthly £10 budget and email alert at 80%/100%.

---

## Lessons Learned
- **VMs vs AKS**: pick the simplest platform that meets the workload’s needs; don’t jump to Kubernetes prematurely.  
- **NSGs** gate inbound traffic—open the minimum required (start with SSH only; add HTTP when needed).  
- **Auto-shutdown** and **deallocate** are quick wins to control spend in labs.  
- Keeping a consistent **naming and tagging** scheme makes governance and cost allocation easier.  
- You can mix **Portal** for discovery with **CLI** for repeatability and speed.

---
