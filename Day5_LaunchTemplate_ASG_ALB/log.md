## Day 5 – Launch Template, Auto Scaling Group & Application Load Balancer Setup  
**Date:** 7th July 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Amazon Web Services (AWS)

---

### Task Summary
- Researched AWS services for Auto Scaling and Load Balancing.
- Created a Launch Template for standardized EC2 provisioning.
- Set up an Auto Scaling Group (ASG) across multiple Availability Zones.
- Configured an Application Load Balancer (ALB) and linked it to the ASG.
- Prepared infrastructure for scalable, highly available web workloads.

---

### Launch Template Creation
1. EC2 Console → **Create launch template**.  
2. **Name:** `asg-launch-template` (v1).  
3. Selected **AMI**, **instance type**, and **key pair** for SSH.  
4. Left **network settings** blank (managed by ASG).  
5. Reviewed and created the template.

---

### Auto Scaling Group (ASG) Setup
1. EC2 Console → **Auto Scaling Groups → Create**.  
2. **Launch template:** `asg-launch-template`.  
3. Selected **multiple subnets** across different **AZs** for high availability.  
4. Skipped load balancing here (to attach target group later).  
5. Created the ASG.

---

### Application Load Balancer (ALB) Configuration
1. EC2 Console → **Load Balancers → Create** → **Application Load Balancer**.  
2. **Name:** `alb-demo` • **Scheme:** Internet-facing • **IP type:** IPv4.  
3. **Listener:** HTTP :80.  
4. Selected VPC and **at least two subnets** in different AZs.  
5. Security group: allow **HTTP (80)** inbound.  
6. **Target group:** `alb-target-group` (Target type: **Instance**, Protocol: **HTTP:80**).  
7. Skipped registering instances (to let ASG attach them).  
8. Created the ALB.

---

### Link ASG to ALB Target Group
1. EC2 Console → **Auto Scaling Groups** → select ASG → **Edit**.  
2. Under **Load balancing**, choose **Attach to an existing target group**.  
3. Select **`alb-target-group`** → **Update**.  
4. New instances launched by the ASG will auto-register with the target group.

---

### Outcome
- Instances launched by the ASG are distributed across AZs and registered behind the ALB.  
- Traffic is routed via the ALB, enabling scaling without manual intervention.

---

### Lessons Learned
- Launch Templates standardize instance configuration for ASGs.  
- Multi-AZ subnets increase resilience.  
- ASG + ALB integration via target groups automates traffic distribution.  
- Ensure security groups permit ALB → Instance HTTP (80) and temporary SSH for troubleshooting.
