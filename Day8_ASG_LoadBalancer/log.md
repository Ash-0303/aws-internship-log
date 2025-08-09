# Day 8 – Auto Scaling Groups (ASG) & Load Balancer Integration

**Date:** 12th July 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Amazon Web Services (AWS)  

---

## Task Summary
- Recapped **Auto Scaling Groups (ASG)** and **Load Balancers**.  
- Reviewed **Launch Templates** and their role in deploying consistent EC2 configurations.  
- Configured an ASG for high availability with scaling policies based on CPU utilization.  
- Installed and tested CPU stress to trigger scaling events.  
- Linked ASG with an **Application Load Balancer (ALB)**.  
- Verified automatic load balancing and scaling behaviour.  

---

## Launch Template Recap
A Launch Template stores reusable configuration settings for EC2 instances, which ASG uses to launch instances automatically.

**Key Launch Template Parameters:**
1. **AMI (Amazon Machine Image):** OS and software image for the instance.  
2. **Instance Type:** Hardware specs (e.g., t3.micro, m5.large).  
3. **Key Pair:** SSH access credentials.  
4. **Block Device Mappings:** Storage configuration.  
5. **Security Groups:** Firewall rules for instance traffic.  
6. **IAM Role:** Optional role for permissions.  
7. **User Data:** Optional script to run on instance startup.  

---

## ASG Creation
1. **Minimum Size:** 2  
2. **Desired Capacity:** 2  
3. **Maximum Size:** 4  
4. Selected multiple subnets across Availability Zones for **high availability**.  
5. Set scaling policy to increase instances when average CPU exceeds 60% for 1 minute.  

---

## Testing Scaling Policy
1. SSH into an EC2 instance that’s part of the ASG.  
2. Installed stress tool:  
   ```bash
   sudo amazon-linux-extras install epel -y  
   sudo yum install stress -y
   ```
3. Verified installation:  
   ```bash
   stress --version
   ```
4. Simulated CPU load for 60 seconds:  
   ```bash
   stress --cpu 2 --timeout 60
   ```
5. Observed in **ASG dashboard**: scaling event triggered additional instances.  

---

## Linking ASG with ALB
**Step 1: Create Target Group**
1. EC2 Console → Target Groups → Create Target Group.  
2. Config:  
   - Target Type: Instance  
   - Protocol: HTTP  
   - Port: 80  
   - VPC: Selected VPC  
3. Skipped registering targets initially.  

**Step 2: Attach Target Group to ASG**
1. EC2 Console → Auto Scaling Groups → Select ASG → Edit.  
2. Under **Load Balancing**, selected **Attach to existing load balancer target group**.  
3. Selected created target group.  
4. Enabled health checks.  
5. Updated ASG.  

**Step 3: Test Load Balancing**
- Modified HTML file on each instance to display different text:  
  - Server A: “This is Server A”  
  - Server B: “This is Server B”  
- Accessed ALB DNS name in browser and observed traffic switching between server responses.  

---

## Outcome
- ASG scaled out based on CPU utilization.  
- ALB successfully distributed traffic between healthy instances.  
- Confirmed that linking ASG with ALB allows seamless scaling and load distribution.