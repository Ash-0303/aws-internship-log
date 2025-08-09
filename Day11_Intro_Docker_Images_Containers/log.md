# Day 11 – Introduction to Docker, Images & Containers

**Date:** 21st July 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Docker on Ubuntu Server (AWS EC2)  

---

## Task Summary
- Learned the fundamentals of **Docker**, including the difference between Docker **images** and **containers**.
- Installed Docker on an Ubuntu EC2 instance.
- Ran and tested the official `hello-world` container.
- Researched key Docker concepts from provided learning materials.
- Compared Docker’s advantages to traditional software installation.
- Learned how to run and access an Nginx container.

---

## Practical Implementation

### Step 1 – Launch EC2 Instance
- Created an Ubuntu Server instance in AWS EC2 to act as the Docker host.

### Step 2 – Connect via SSH
```bash
ssh -i my-key.pem ubuntu@<public-ip-address>
```
- Successfully accessed the Ubuntu terminal.

### Step 3 – Install Docker
```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
newgrp docker
```

### Step 4 – Verify Installation
```bash
docker --version
sudo systemctl status docker
```

### Step 5 – Run First Docker Container
```bash
docker run hello-world
```
- Expected output confirms successful Docker installation.

### Step 6 – View Running & Exited Containers
```bash
docker ps
docker ps -a
```

---

## Research Notes

### What is Docker?
Docker is an open-source platform for building, shipping, and running applications inside **containers**.  
Containers bundle the application with all its dependencies, ensuring it runs consistently across environments.

### Advantages of Docker
- **Portability:** Runs on any OS that supports Docker (Windows, Linux, macOS).
- **Consistency:** Eliminates "it works on my machine" issues.
- **Simplified Deployment:** Dependencies are included in the container.

### Docker Image vs Docker Container
| Docker Image | Docker Container |
|--------------|------------------|
| Read-only template with application code, dependencies, and configurations. | Running instance of a Docker image. |
| Example: Nginx image includes binary, default config, and libraries. | Executes application in an isolated environment. |
| Stored in Docker Hub or private registry. | Can be started, stopped, or deleted. |

---

## Nginx Demo (Research Example)

**Step 1:** Pull image:
```bash
docker pull nginx
```

**Step 2:** Run container:
```bash
docker run -dt -p 8080:80 nginx
```
- `-d`: Detached mode  
- `-t`: Allocate TTY  
- `-p`: Map host port 8080 to container port 80

**Step 3:** Verify in browser:  
Navigate to `http://<server-ip>:8080` – should display "Welcome to Nginx".

**Step 4:** Useful commands:
```bash
docker images
docker ps
docker stop <id>
docker start <id>
docker ps -a
```

---

## Outcome
- Installed and tested Docker on Ubuntu EC2.
- Understood difference between Docker images and containers.
- Practiced running and managing containers.
- Learned how to run Nginx in Docker and access it via browser.

---
