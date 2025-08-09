# Day 6 – Amazon Route 53, DNS Records & Hosted Zone Setup

**Date:** 10th July 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Amazon Web Services (AWS)  

---

## Task Summary
- Researched **Amazon Route 53** for DNS management and domain registration.  
- Learned core **DNS concepts** and the role of Hosted Zones in routing traffic.  
- Registered a custom domain name using Route 53.  
- Created a **Public Hosted Zone** and configured DNS records.  
- Linked domain name to an EC2 instance via an **A record** pointing to its Elastic IP.  
- Verified public access to the web server using the registered domain.  

---

## Domain Registration
1. **AWS Console → Route 53 → Registered Domains → Register Domain.**  
2. Chose domain: `ashwin0303.com`.  
3. Initial attempt to use Freenom for a free domain failed due to service downtime.  

---

## Hosted Zone Setup
1. **AWS Console → Route 53 → Hosted Zones → Create Hosted Zone.**  
2. Selected domain: `ashwin0303.com` (Public Hosted Zone).  
3. Created **A record** mapping domain to EC2 Elastic IP.  
4. Allocated Elastic IPv4 address to a pre-existing web server.  
5. Verified domain access: [http://www.ashwin0303.com](http://www.ashwin0303.com)  

---

## DNS Record Types Learned
- **A Record (Address Record):** Maps domain to IPv4 address.  
- **CNAME Record (Canonical Name Record):** Maps domain/subdomain to another domain name.  
- **MX Record (Mail Exchange Record):** Specifies mail servers for email routing.  
- **TXT Record (Text Record):** Stores arbitrary text, e.g., domain verification or SPF records.  

---

## Outcome
- Successfully registered and configured a custom domain with Route 53.  
- Linked domain to EC2 instance using an A record.  
- Gained hands-on experience with DNS concepts and Hosted Zone configuration.  
