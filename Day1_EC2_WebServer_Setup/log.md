# 📅 Day 1 – EC2 Instance & Apache Web Server Setup

**🗓️ Date:** 2nd July 2025  
**🎓 Internship Role:** Cloud Support Engineer Intern  
**☁️ Platform:** Amazon Web Services (AWS)

---

## ✅ Task Summary

- Launched my first EC2 instance on AWS.
- Installed and configured a basic Apache web server.
- Created and hosted a simple static web page.
- Documented the full SSH setup and troubleshooting steps.

---

## 🖥️ EC2 Instance Setup

- **Instance type:** `t2.micro`
- **Operating System:** Amazon Linux 2
- **Key Pair:** Downloaded `.pem` file
- **Security Group Rules:**
  - ✅ SSH (Port 22) → `0.0.0.0/0`
  - ✅ HTTP (Port 80) → `0.0.0.0/0`
  - ✅ HTTPS (Port 443) → `0.0.0.0/0`

⚠️ *Note:* Allowing SSH from anywhere is **not secure**. In future deployments, I plan to restrict SSH access to **only my personal IP**.

---

## 🔐 SSH Configuration Using PuTTY

Since I used a Windows machine:

1. Installed **PuTTY** and **PuTTYgen**.
2. Converted the downloaded `.pem` key to a `.ppk` file using PuTTYgen.
3. Connected to the EC2 instance using:
   - **Public IP Address**
   - **`.ppk` key**
   - **Username:** `ec2-user`

---

## ⚙️ Apache Web Server Installation

Switched to root user and executed the following commands:

```bash
sudo -i                            # Switch to root user
sudo yum update -y                # Update all packages
sudo yum install httpd -y         # Install Apache
sudo systemctl start httpd        # Start Apache service
sudo systemctl enable httpd       # Enable Apache to start on boot
sudo systemctl status httpd       # Verify Apache is running
```

---

## 🌐 Hosting a Static HTML Page

Created a simple HTML file:

```bash
cd /var/www/html
echo "Hello from EC2" > index.html
```

Then accessed the web page using the EC2 instance’s public IP.

---

## ❌ Initial Issue with HTTP

- The page did **not** load over `HTTPS`.
- However, switching to `HTTP` worked and displayed:
  > “Hello from EC2”

### 🔍 Possible Reasons:
- Apache config may need explicit HTTPS listener.
- Browser may have cached a forced redirect to HTTP.
- Firewall or antivirus on local machine could be blocking HTTPS.
- Apache may need SSL module and virtual host configuration adjusted.

---

## 🧠 Lessons Learned

- Always test both `HTTP` and `HTTPS` when configuring web servers.
- Validate both **Security Group** and **local firewall rules**.
- Use limited IP ranges for SSH access to improve security.
- Understand how browser behavior can affect server testing.

---