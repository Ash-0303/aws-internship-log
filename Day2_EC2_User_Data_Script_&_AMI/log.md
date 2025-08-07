## Day 2 – EC2 User Data Script & AMI Creation  
**Date:** 3rd July 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Amazon Web Services (AWS)

---

### Task Summary
- Created a new EC2 instance using "User Data" to automate Apache web server setup.
- Hosted a "Hello World" web page without manual SSH configuration.
- Learned that EC2 User Data scripts run only at first launch.
- Created an AMI from the configured instance for reuse.
- Launched a second EC2 instance from the AMI and confirmed successful web page deployment.

---

### EC2 Instance Setup (instance_01)
- **Instance type:** t2.micro  
- **Operating System:** Amazon Linux 2  
- **User Data Script Used:**
    ```bash
    #!/bin/bash
    yum update -y
    yum install -y httpd
    systemctl enable httpd
    systemctl start httpd
    echo "Hello World" > /var/www/html/index.html
    ```
- **Security Group Rules:**
  - SSH (Port 22) → 0.0.0.0/0  
  - HTTP (Port 80) → 0.0.0.0/0  

---

### Verification
- Accessed the public IPv4 address in a browser.
- The message **"Hello World"** was displayed successfully, confirming that the Apache server was installed and running.

---

### AMI Creation
- Selected the running EC2 instance with Apache configured.
- Navigated to: **Actions → Image and templates → Create image**
- **AMI Name:** `hello-world-webserver-ami`  
- **Description:** For learning purposes  
- Clicked **Create Image** and waited for AMI to be available.

---

### Launching New Instance from AMI
- Navigated to **AMIs** and selected the newly created AMI.
- Clicked **"Launch instance from image"**.
- Instance settings:
  - **Instance type:** t2.micro (free tier)  
  - **Key pair:** Same as original instance  
  - **Security group:** Reused original SG (allowing HTTP and SSH)  
- Clicked **Launch instance**.

---

### Final Verification
- Copied the new instance’s public IPv4 address.
- Opened it in a browser — the page successfully displayed **"Hello World"**.
- This confirmed that the AMI replicated the full server setup without needing to repeat manual steps.

---

### Lessons Learned
- EC2 **User Data scripts** simplify provisioning and configuration at launch.
- These scripts only run **on first boot**, unless manually reconfigured.
- **AMI creation** is useful for creating pre-configured, reusable server images.
- No need to use `sudo` in User Data — commands run as root by default.