# Day 28 – State Management and Remote State in Terraform
**Date:** 11th August 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Terraform on AWS (Windows)  

---

## Task Summary
- Learned how Terraform tracks resources using the **state file** (`terraform.tfstate`).  
- Understood the importance of state for resource mapping and preventing drift.  
- Implemented **remote state storage** using an **S3 bucket** with a **DynamoDB table** for state locking.  
- Configured and tested an S3 backend for Terraform to centralize state management.  

---

## Key Concepts Learned

### 1. Terraform State
- The `terraform.tfstate` file maps real-world infrastructure to Terraform configuration.  
- State ensures that `terraform plan` knows what resources exist and whether they need changes.  
- Can be viewed with:
  ```bash
  terraform show
  ```

### 2. Remote State
- Stores the state file in a central location instead of locally.  
- Benefits:  
  - Enables collaboration in teams.  
  - Prevents conflicts via **state locking** using DynamoDB.  
  - Provides a backup if local files are lost.

### 3. S3 + DynamoDB Backend Setup
- **S3 bucket** stores the state file.  
- **DynamoDB table** with a partition key `LockID` handles state locking.  
- Backend configured in Terraform’s `backend.tf` file.

---

## Code Implementation

**backend.tf:**
```hcl
terraform {
  backend "s3" {
    bucket         = "ashwin-tfstate-ukw2-20250811"
    key            = "remote-backend-project/terraform.tfstate"
    region         = "eu-west-2"
    dynamodb_table = "terraform-locks"
    encrypt        = true
  }
}
```

---

## Steps Completed
1. Created **S3 bucket**: `ashwin-tfstate-ukw2-20250811` in region `eu-west-2`.  
2. Created **DynamoDB table**: `terraform-locks` with partition key `LockID (S)`.  
3. Configured backend in `backend.tf`.  
4. Ran:
   ```bash
   terraform init
   ```
   - Successfully initialized the backend with S3 and DynamoDB.  
5. Verified:
   - Terraform state file (`terraform.tfstate`) stored in S3.  
   - State locking enabled via DynamoDB table.

---

## Practice Task
- Set up an **S3 remote backend** for an existing Terraform project.  
- Verified state file upload to S3 and confirmed DynamoDB locking functionality.

---

## Lessons Learned
- State files are critical for Terraform’s ability to track and manage infrastructure.  
- Remote state enables safe collaboration between multiple engineers.  
- DynamoDB locking prevents race conditions and state corruption.  
- Backend configuration changes require reinitializing Terraform with:
  ```bash
  terraform init -reconfigure
  ```
