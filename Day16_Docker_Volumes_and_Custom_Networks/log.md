# Day 16 – Persisting Data with Docker Volumes and Connecting Containers via Custom Networks

**Date:** 27th July 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Docker on AWS EC2 (Ubuntu)

---

## Task Summary

- Created and used Docker volumes to persist data beyond container lifecycles.
- Mounted volumes into containers to store persistent web server data.
- Created custom networks for inter-container communication using DNS names.
- Verified connectivity between containers on the same network.

---

## Step 1: Persist Data with Docker Volumes

### 1.1 Create a Docker Volume
This stores persistent data outside the container lifecycle.
```bash
docker volume create my-volume
```
- Creates a volume named `my-volume` that Docker manages.

### 1.2 Run a Container with Volume Mounted
Mount the volume into a container (e.g., Nginx web server):
```bash
docker run -d --name my-nginx -v my-volume:/usr/share/nginx/html nginx
```
- Mounts the volume to Nginx's default web directory.

---

## Step 2: Docker Containers on the Same Network

### 2.1 Create a Custom Network
Custom bridge network allows containers to communicate via DNS names:
```bash
docker network create my-network
```
- Creates a user-defined bridge network named `my-network`.

### 2.2 Run Containers on the Same Network
```bash
docker run -d --name my-nginx --network my-network nginx
docker run -d --name my-python-app --network my-network my-python-app
```
- Both containers are on the same network and can resolve each other by name.

---

## Step 3: Practice Task – Verify Container Communication

### 3.1 Connect to Python App Container
```bash
docker exec -it my-python-app bash
```
Inside the container:
```bash
ping my-nginx
```
- Received responses from `my-nginx`, confirming that the containers can communicate.

---

## Lessons Learned

- **Docker volumes** allow persistent storage independent of container lifecycle.
- **User-defined networks** enable DNS-based container name resolution.
- Containers on the same custom bridge network can communicate without using IP addresses.
- Useful for multi-container applications where services need to interact securely.
