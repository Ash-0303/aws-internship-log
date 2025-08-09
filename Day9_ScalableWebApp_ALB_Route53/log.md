# Day 9 – Scalable Web Application Deployment with ALB & Route 53

**Date:** 14th July 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Amazon Web Services (AWS)  

---

## Task Summary
- Deployed a scalable web application architecture using **Auto Scaling Groups (ASG)**, **Application Load Balancer (ALB)**, and **Route 53**.  
- Launched a web app across multiple EC2 instances using Auto Scaling.  
- Distributed HTTP traffic via an ALB.  
- Linked Route 53 domain to the ALB for public access.  

---

## Launch Template & ASG Setup
Created a Launch Template to define EC2 instance configuration within the ASG.

**Key Configuration Values:**
- **AMI:** Custom AMI with Nginx web server & basic HTML homepage.  
- **Instance Type:** t3.micro (low traffic testing).  
- **Key Pair:** Existing SSH key for manual debugging.  
- **Security Group:** Inbound HTTP (80) & SSH (22) allowed.

**ASG Configuration:**
- **Launch Template:** Newly created one.  
- **Availability Zones:** 2 subnets in different AZs for high availability.  
- **Desired Capacity:** 2  
- **Min Size:** 2  
- **Max Size:** 4  
- **Health Check Type:** EC2 & ELB.  
- **Scaling Policy:** Target Tracking – maintain 50% average CPU utilization.

---

## ALB Configuration
- **Scheme:** Internet-facing.  
- **Listener:** Forward HTTP (80) to target group.  
- **Target Group:** Registered ASG with HTTP health checks on port 80.  
- **Subnets:** Same as ASG for compatibility.  
- Verified correct routing to healthy EC2 instances.

---

## Route 53 Configuration
**Steps Taken:**
1. Opened hosted zone for domain in Route 53.  
2. Created new **A record (Alias)**.  
3. Pointed record to ALB’s DNS name.  
4. Set TTL to 60 seconds for quick DNS propagation during testing.  
5. Verified successful domain resolution to ALB.  

---

## Outcome
- Web application successfully deployed across multiple EC2 instances with auto scaling.  
- ALB effectively distributed incoming requests to healthy instances.  
- Domain name correctly resolved to ALB via Route 53, enabling public access.
