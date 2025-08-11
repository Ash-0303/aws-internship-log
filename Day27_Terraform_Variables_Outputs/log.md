# Day 27 – Variables and Outputs in Terraform
**Date:** 10th August 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Terraform on AWS (Windows)  

---

## Task Summary
- Learned how to make Terraform configurations **reusable** by using variables and outputs.  
- Created `variables.tf` to define reusable parameters for instance type, region, and AMI.  
- Created `terraform.tfvars` to store variable values separately.  
- Created `outputs.tf` to display important deployment details after execution.  
- Modified EC2 deployment to utilize variables and outputs instead of hardcoded values.  

---

## Key Concepts Learned

### 1. Variables in Terraform
- Defined using the `variable` block in `variables.tf`.  
- Allow configuration to be dynamic and reusable.  
- `terraform.tfvars` stores variable values to keep sensitive or environment-specific data separate from the main configuration.

### 2. Outputs in Terraform
- Defined in `outputs.tf`.  
- Provide useful information after resource creation, such as Public IP or Instance ID.  
- Can be referenced in other Terraform configurations or modules.

### 3. Benefits of Using Variables and Outputs
- Easier configuration management across multiple environments.  
- Cleaner code with less hardcoding.  
- Facilitates collaboration by separating logic from configuration data.

---

## Code Implementation

**main.tf:**
```hcl
provider "aws" {
  region = var.region
}

resource "aws_instance" "web" {
  ami           = var.ami_id
  instance_type = var.instance_type

  tags = merge(var.tags, {
    Name = "day3-web"
  })
}
```

**variables.tf:**
```hcl
variable "region" {
  description = "AWS region to deploy into"
  type        = string
}

variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t3.micro"
}

variable "ami_id" {
  description = "AMI ID to use for the instance"
  type        = string
}

variable "tags" {
  description = "Common resource tags"
  type        = map(string)
  default = {
    Project = "Internship-Log"
    Env     = "Dev"
  }
}
```

**terraform.tfvars:**
```hcl
region        = "eu-west-2"
instance_type = "t3.micro"
ami_id        = "ami-0cb22668278979e9"
tags = {
  Project = "Internship-Log"
  Env     = "Dev"
  Owner   = "Ashwin"
}
```

**outputs.tf:**
```hcl
output "instance_public_ip" {
  description = "Public IP of the EC2 instance"
  value       = aws_instance.web.public_ip
}

output "instance_id" {
  description = "ID of the EC2 instance"
  value       = aws_instance.web.id
}
```

---

## Practice Task
1. Updated the EC2 configuration to use variables from `variables.tf` and `terraform.tfvars`.  
2. Added outputs in `outputs.tf` to display the instance’s **public IP** and **instance ID** after deployment.  
3. Verified in the AWS Management Console that:  
   - The EC2 instance deployed with correct parameters.  
   - The outputs displayed as expected after running `terraform apply`.

---

## Lessons Learned
- Separating variables from main Terraform files improves flexibility and reusability.  
- Outputs provide quick access to important resource details without manually checking the AWS Console.  
- Using `merge()` in tags helps combine default tags with resource-specific tags.  
- The structure of `variables.tf` + `terraform.tfvars` + `outputs.tf` is a best practice for production-ready Terraform code.  