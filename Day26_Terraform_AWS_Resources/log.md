# Day 26 – Creating AWS Resources with Terraform
**Date:** 9th August 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Terraform on AWS (Windows)  

---

## Task Summary
- Learned how to create AWS infrastructure using **Terraform**.  
- Wrote a `main.tf` to define AWS Provider, EC2 Instance, Key Pair, and Security Group.  
- Deployed an EC2 instance and verified it in the AWS Management Console.  
- Practiced updating an instance type in Terraform to reflect changes in AWS.  

---

## Key Concepts Learned

### 1. Defining AWS Infrastructure in Terraform
- **AWS Provider** specifies the cloud service to interact with (in this case, AWS).  
- **Key Pair** allows secure SSH access to the EC2 instance.  
- **Security Group** acts as a virtual firewall controlling inbound/outbound traffic.  
- **EC2 Instance Resource** provisions the virtual server.

### 2. Terraform Workflow
1. Write infrastructure code in `main.tf`.  
2. Initialize Terraform:
   ```bash
   terraform init
   ```
3. Preview changes:
   ```bash
   terraform plan
   ```
4. Apply changes:
   ```bash
   terraform apply
   ```

### 3. Updating Infrastructure with Terraform
- Changing a parameter (e.g., `instance_type`) in `main.tf` and re-running `terraform plan` + `terraform apply` updates resources while maintaining state.  

---

## Environment Setup & Code

**main.tf Example:**
```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_key_pair" "my_key" {
  key_name   = "first-server"
  public_key = file("C:/Users/ashwi/OneDrive/Desktop/SSH keys/first_server.pub")
}

resource "aws_security_group" "web_sg" {
  name        = "web_sg"
  description = "Allow SSH and HTTP inbound traffic"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_instance" "myec2" {
  ami                    = "ami-0c55b159cbfafe1f0" # Amazon Linux 2 AMI
  instance_type          = "t2.micro"
  key_name               = aws_key_pair.my_key.key_name
  vpc_security_group_ids = [aws_security_group.web_sg.id]

  tags = {
    Name = "Terraform-EC2"
  }
}
```

---

## Practice Task

### 1. Initial Deployment
- Created an EC2 instance with instance type `t2.micro`.  
- Verified creation in AWS Management Console.  

### 2. Modification Task
- Updated `instance_type` in `main.tf` from `t2.micro` to `t2.small`.  
- Ran:
  ```bash
  terraform plan
  terraform apply
  ```
- Verified updated instance type in AWS Console.

---

## Lessons Learned
- Terraform makes it easy to **provision** and **modify** cloud resources while keeping track of changes via its state file.  
- Security Groups must be configured carefully to allow only required ports for better security.  
- Updating infrastructure via Terraform ensures changes are tracked and reproducible.  
- The workflow of **init → plan → apply** is essential for safe infrastructure changes.  
