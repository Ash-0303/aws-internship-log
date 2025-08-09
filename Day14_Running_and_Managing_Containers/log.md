# Day 14 – Running and Managing Containers

**Date:** 25th July 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Docker on AWS EC2 (Ubuntu)

---

## Task Summary

- Ran an Nginx container in detached mode.
- Viewed and inspected container logs.
- Interacted with a running container via shell access.
- Stopped, restarted, and removed containers.
- Modified files and installed packages inside a container.
- Tested network connectivity and debugged issues.

---

## Step 1: Run a Container

### Command:
```bash
docker run -d --name my-nginx nginx
```
- `-d` means detached mode  
- `--name my-nginx` assigns a custom name  
- `nginx` is the image name (latest version by default)

### View logs:
```bash
docker logs my-nginx
```

---

## Step 2: Interact with the Container

### Open a shell session inside the container:
```bash
docker exec -it my-nginx /bin/bash
```
- `-it` allows interactive commands  
- `/bin/bash` starts a shell inside the container

### Stop and restart the container:
```bash
docker stop my-nginx
docker start my-nginx
```

---

## Step 3: Remove Containers

### Remove a stopped container:
```bash
docker rm my-nginx
```

---

## Common Commands Inside a Container

### 1. Inspect and Debug
```bash
cat /var/log/nginx/access.log
ps aux
env
```

### 2. Modify Files
```bash
nano /usr/share/nginx/html/index.html
mkdir /app && touch /app/test.txt
```

### 3. Install Packages (Temporary)
```bash
apt update && apt install curl -y
```

### 4. Test Network Connectivity
```bash
ping google.com
```

### 5. Explore the File System
```bash
ls /usr/share/nginx/html
cd /etc/nginx
```

---

## Lessons Learned

- Docker containers can be run in detached mode for background operation.  
- Logs can be accessed using `docker logs <container>`.
- `docker exec` allows interactive access to a running container.  
- Stopping and starting containers is managed via host-level commands.  
- Changes inside a container are lost unless made permanent via a Dockerfile or volumes.  
- Containers are minimal by default and require manual installation of tools for debugging.  
- Always persist important data outside of the container.

