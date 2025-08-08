## Day 4 – EC2 Ubuntu Web Server with Elastic IP & Lightsail Node.js Deployment  
**Date:** 6th July 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Amazon Web Services (AWS)

---

### Task Summary
- Reviewed and consolidated AWS EC2, Lightsail, Elastic IPs, and Security Groups knowledge.
- Created and tested an Ubuntu-based EC2 web server running Nginx with a static Elastic IP.
- Created an AMI of the web server for replication.
- Deployed a Node.js application on AWS Lightsail, overcoming multiple troubleshooting challenges.

---

### EC2 Ubuntu Web Server with Elastic IP

1. Launched an **Ubuntu EC2 instance**.
2. Installed Nginx:
   ```bash
   sudo apt update -y
   sudo apt install nginx -y
   ```
3. Hosted a custom HTML page in `/var/www/html`.
4. Allocated and associated an **Elastic IP** to the server.
5. Verified the server via browser using the EIP.
6. Restarted the instance to confirm the Elastic IP remained static.
7. Created an **AMI** of the existing web server.
8. Launched a **new instance** from the AMI.
   - Noted: New instance did not automatically have an EIP; would require allocating and associating a new Elastic IP.

---

### AWS Lightsail: Node.js Application Deployment

1. Created a **Lightsail instance**:
   - Platform: Linux/Unix  
   - Blueprint: Node.js
2. Connected via browser-based SSH and verified Node.js installation:
   ```bash
   node -v
   ```
3. Uploaded Node.js project to a new GitHub repository.
4. Installed Git on Lightsail:
   ```bash
   sudo apt install git -y
   ```
5. Cloned repository:
   ```bash
   git clone https://github.com/Ash-0303/your_nodejs-repo.git
   ```
6. Navigated into the project directory.

---

### Troubleshooting & Fixes

- **Issue 1:** `npm install` failed due to missing `package.json`.  
  **Fix:**  
  ```bash
  npm init -y
  npm install
  ```

- **Issue 2:** App failed when listening on port 80 without root privileges.  
  **Fix:** Changed port to `3000` in `main.js`, committed changes in GitHub, and pulled updates:
  ```bash
  git pull
  ```

- **Issue 3:** App still failed due to firewall blocking port 3000.  
  **Fix:** Updated Lightsail networking settings to allow **TCP port 3000**.

---

### Verification

- Started the Node.js app:
  ```bash
  node main.js
  ```
- Accessed successfully via browser:  
  `http://18.175.147.86:3000/`

---

### Lessons Learned
- Elastic IPs ensure static public IP persistence across instance restarts.
- AMIs allow replication of pre-configured server environments.
- AWS Lightsail provides a simple way to deploy apps but still requires proper firewall configuration.
- Common Node.js deployment issues include:
  - Missing `package.json`
  - Privileged ports restrictions (<1024)
  - Firewall rules blocking traffic