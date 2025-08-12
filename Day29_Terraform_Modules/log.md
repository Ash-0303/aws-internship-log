# Day 29 – Modules and Reusable Configurations in Terraform
**Date:** 12th August 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Terraform on AWS (Windows)  

---

## Task Summary
- Learned what **Terraform modules** are and how to use them for reusability and organization.  
- Created a **custom EC2 deployment module** and used it in a project.  
- Worked with a **public module** from the Terraform Registry for EC2 instance creation.  
- Deployed EC2 instances using both the custom module and the public module, verifying functionality in AWS.  

---

## Key Concepts Learned

### 1. What is a Module in Terraform?
- A **module** is a container for multiple Terraform resources that are used together.  
- Benefits:  
  - **Reusability** – Write once, use multiple times.  
  - **Organization** – Separates complex configurations into logical parts.  
  - **Consistency** – Standardizes deployments across environments.

### 2. Creating a Custom Module
- Defined variables in `variables.tf` for flexibility.  
- Created `main.tf` for EC2 + Security Group setup.  
- Added `outputs.tf` to expose instance details like ID, public IP, and DNS.  
- Called the module in the **root configuration** (`live/dev/main.tf`) with required variables.

### 3. Using Public Modules
- Pulled a ready-to-use **EC2 module** from the Terraform Registry.  
- Provided required inputs such as AMI ID, instance type, subnet ID, security group IDs, and key pair.  

---

## Code Implementation

**Custom Module Structure:**
```
modules/
└── ec2-basic/
    ├── main.tf
    ├── variables.tf
    └── output.tf
```

**Example – Module Call in `live/dev/main.tf`:**
```hcl
module "web" {
  source = "../../modules/ec2-basic"
  region = var.region
  name   = "day5-web"
  vpc_id = var.vpc_id
  subnet_id = var.subnet_id
  instance_type = var.instance_type
  key_name = var.key_name
  ssh_cidrs = var.ssh_cidrs
  associate_public_ip = true
  user_data = <<-EOT
    #!/bin/bash
    yum update -y
    yum install -y httpd
    systemctl enable httpd
    systemctl start httpd
    echo "Hello from Day 5 module!" > /var/www/html/index.html
  EOT
  tags = {
    Project = "aws-internship-log"
    Env     = "dev"
  }
}
```

**Public Module Example:**
```hcl
module "ec2" {
  source = "terraform-aws-modules/ec2-instance/aws"
  version = "~> 5.6"
  name = "day5-registry-ec2"
  ami = "ami-0cb22668278979e9"
  instance_type = "t3.micro"
  subnet_id = "subnet-045a04445f980650e"
  vpc_security_group_ids = ["sg-0a160e7bc997319fc"]
  associate_public_ip_address = true
  key_name = "my-keypair"
  tags = {
    Project = "aws-internship-log"
    Env     = "dev"
  }
}
```

---

## Practice Tasks
1. **Custom Module Task**  
   - Deployed an EC2 instance with Apache HTTP server installed and a custom HTML welcome message.
   - Verified deployment by visiting the **public IP** in a browser.  

2. **Public Module Task**  
   - Used the Terraform AWS EC2 module from the registry to deploy another instance.
   - Verified instance creation in AWS Management Console.

---

## Lessons Learned
- Custom modules give complete control over configuration but require more setup.  
- Public modules from the Terraform Registry speed up deployments but must be reviewed for security and compatibility.  
- Output values from modules make it easy to retrieve key information like IP addresses and DNS names.  
- Organizing Terraform code into modules improves scalability for large infrastructure projects.  
