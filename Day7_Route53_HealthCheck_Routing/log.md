# Day 7 – Amazon Route 53 Health Checks, Failover Routing & Weighted Routing

**Date:** 11th July 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Amazon Web Services (AWS)  

---

## Task Summary
- Learned about **Route 53 Health Checks** to monitor endpoint availability and performance.  
- Implemented **Failover Routing Policy** for automatic traffic redirection when the primary resource fails.  
- Configured **Weighted Routing Policy** to distribute traffic between multiple resources.  
- Tested failover between two EC2 instances in different Availability Zones.  
- Verified traffic splitting with weighted records.  

---

## Health Check Setup
- A Route 53 Health Check monitors an endpoint (e.g., EC2 instance/web server) by sending HTTP, HTTPS, or TCP requests.  
- If the endpoint fails to respond correctly (times out or errors), Route 53 marks it as unhealthy.  
- This status can trigger failover or stop routing traffic to the failed endpoint.  

**Example:**  
Created a health check for: `http://<ec2-instance>/public-ip/`  
If the server goes down, Route 53 detects it.  

**Steps Taken:**
1. Retrieved EC2's Public IPv4 address (already running a web server).  
2. Created a new health check:  
   - **Name:** `Primary-EC2-HealthCheck`  
   - **Endpoint Type:** IP Address  
   - **Domain Name:** EC2 Public DNS Name  
   - **Protocol:** HTTP, Port 80  
3. Left default health thresholds & intervals.  
4. Waited 1–2 minutes for status to turn **Healthy**.  

---

## Failover Routing Setup
1. Created a **Secondary EC2 instance** in a different Availability Zone (same region) with a basic HTML page called **"Fail Pafe"**.  
   - Yes, "Fail Pafe". Proof that even error pages can have typos — it builds character.  
2. Created two **A records** in Hosted Zone:  
   - **Record 1:**  
     - Name: `Primary EC2’s IP`  
     - Routing Policy: Failover  
     - Failover Record Type: Primary  
     - Associated Health Check: `Primary-EC2-HealthCheck`  
   - **Record 2:**  
     - Name: Same domain (e.g., `www`)  
     - Value: Secondary EC2’s IP  
     - Routing Policy: Failover  
     - Failover Record Type: Secondary  
     - No health check required.  
3. Tested by stopping Primary Instance and confirming traffic routed to Secondary.  

---

## Weighted Routing Setup
- Weighted routing splits DNS traffic between resources based on percentage values.  
- Useful for:  
  - A/B testing  
  - Gradually rolling out updates  
  - Load distribution  

**Steps Taken:**
1. Created two weighted A records for the same domain:  
   - **Record 1:**  
     - Name: `www`  
     - Value: Primary EC2’s IP  
     - Routing Policy: Weighted (50%)  
     - Associated Health Check: Optional  
   - **Record 2:**  
     - Name: `www`  
     - Value: Secondary EC2’s IP  
     - Routing Policy: Weighted (50%)  
     - Associated Health Check: Optional  
2. Tested by refreshing the site multiple times to confirm load splitting between servers.  

---

## Outcome
- Successfully implemented health checks and failover routing in Route 53.  
- Created weighted routing to distribute traffic evenly between instances.  
- Learned how DNS can be used not only for resolution but also for **traffic control and resilience**.  
