# Day 25 – Introduction to Terraform & Infrastructure as Code
**Date:** 8th August 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Terraform on AWS (Windows)  

---

## Task Summary
- Learned the fundamentals of **Infrastructure as Code (IaC)** and why it is used.  
- Understood the differences between **Infrastructure Orchestration** and **Configuration Management** tools.  
- Installed Terraform on Windows and configured AWS CLI for use.  
- Created a simple Terraform configuration to define an AWS provider.  

---

## Key Concepts Learned

### 1. Infrastructure as Code (IaC) Basics
- IaC allows you to define infrastructure in code, enabling **automation**, **reproducibility**, and **scalability**.  
- Unlike manual setup, IaC is **faster**, **consistent**, and **auditable**.  
- Typical workflow: Write IaC → Store in version control (Git) → Review changes → Apply changes.

### 2. Choosing the Right IaC Tool
- **Infrastructure Orchestration Tools** (e.g., Terraform, CloudFormation) manage raw infrastructure like VMs, networks, and storage.  
- **Configuration Management Tools** (e.g., Ansible, Chef) manage software and settings inside already-running servers.  
- Terraform is cloud-agnostic, making it suitable for multi-cloud environments.

### 3. Core Terraform Concepts
- **Providers** – Plugins to interact with cloud services (AWS, Azure, GCP, etc.).  
- **Resources** – Infrastructure components to be created/managed.  
- **Variables** – Parameterized values for reusability.  
- **State** – Tracks deployed resources to manage changes safely.  
- **Modules** – Reusable collections of Terraform configurations.

---

## Environment Setup

### Install Terraform on Windows
1. Download Terraform from the official HashiCorp site.  
2. Unzip the file and place the executable in a directory (e.g., `C:\terraform`).  
3. Add the directory to the Windows **PATH** environment variable.  
4. Open **Command Prompt** or **PowerShell** and run:
   ```powershell
   terraform -version
   ```
   to verify installation.

### Configure AWS CLI
```powershell
aws configure
```
(Entered AWS Access Key, Secret Key, Region, and Output format.)

---

## Practice Task
Created a simple `main.tf` defining AWS as the provider:
```hcl
provider "aws" {
  region = "us-east-1"
}
```
Verified Terraform CLI recognized the configuration and initialized successfully.

---

## Lessons Learned
- IaC reduces manual errors and ensures consistency across environments.  
- Terraform’s **state file** is crucial for managing infrastructure lifecycle.  
- Always start with orchestration for provisioning, then apply configuration management if needed.  
- Version control is essential for collaboration and tracking changes.  
