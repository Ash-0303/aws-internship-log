## Day 3 – AWS Lightsail WordPress Deployment & Elastic IP Testing  
**Date:** 5th July 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Amazon Web Services (AWS)

---

### Task Summary
- Explored AWS Lightsail and deployed a WordPress instance.
- Learned to connect to Lightsail instances via browser SSH and terminal.
- Accessed and configured the WordPress admin panel.
- Created and associated an Elastic IP with a web server for persistent public access.
- Discovered HTTPS access limitations and learned about the need for SSL/TLS certificates.

---

### AWS Lightsail: WordPress Deployment

1. Navigated to **Lightsail** (under "Compute" services).
2. Clicked **"Create Instance"** and configured:
   - **Instance Location:** `eu-west-1`  
   - **Platform:** Linux/Unix  
   - **Blueprint:** WordPress (Bitnami image)  
   - **Instance Name:** `wordpress-practice`
3. Created the instance.

---

### Connecting to Lightsail Instance

- Used **browser-based SSH** from the Lightsail dashboard.
- Also practiced connecting via local terminal:
  ```bash
  ssh -i /path/to/LightsailKey.pem ubuntu@<PublicIP>
  ```

---

### WordPress Setup

- Accessed WordPress site via:  
  `http://<PublicIP>`
- Retrieved admin password:
  ```bash
  cat bitnami_application_password
  ```
- Logged into WordPress Admin Panel via:  
  `http://<PublicIP>/wp-admin`
  - **Username:** user  
  - **Password:** (retrieved from SSH session)
- Customised site: changed themes and added plugins.

---

### Elastic IP Address Testing

1. Navigated to:  
   **EC2 → Network & Security → Elastic IPs → Allocate Elastic IP address**
2. Used default settings and clicked **"Allocate"**
3. Created a web server from previously created AMI (`hello-world-webserver-ami`).
4. Associated the Elastic IP with the running EC2 instance.
5. Verified accessibility via browser:  
   `http://<ElasticIP>` → Web server loaded successfully.

---

### HTTPS Testing & Findings

- Attempted to access:  
  `https://<ElasticIP>` → Connection failed.
- Verified security group had port 443 open.
- Discovered:
  > An open port 443 does not enable HTTPS by itself.  
  > The web server must have a valid **SSL/TLS certificate** configured.

---

### Lessons Learned

- AWS Lightsail simplifies cloud hosting for small applications.
- WordPress setup via Lightsail is user-friendly and ideal for quick deployment.
- Elastic IPs provide static IP addresses across instance restarts.
- Simply allowing HTTPS (443) in security groups is **not sufficient** — an SSL/TLS certificate must be installed.

---

### Next Steps (Guidance Requested)

- Learn and implement **SSL/TLS certificate configuration** (e.g., using Let's Encrypt or AWS Certificate Manager).