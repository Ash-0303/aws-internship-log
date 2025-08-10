# Day 18 – Common Docker Issues and Fixes

**Date:** 29th July 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Docker on AWS EC2 (Ubuntu)

---

## Task Summary

- Identified and resolved common Docker issues including port conflicts, volume permission errors, and networking problems.
- Learned commands to diagnose and fix these issues efficiently.

---

## 1. Port Conflicts

**Problem:**  
You get an error when starting a container because the port is already in use.

**Fix:**  
a. Check which service is using the port:
```bash
sudo lsof -i :<port>
```
b. Stop the conflicting service or run the container on a different port:
```bash
docker run -p 8081:80 <image>
```

---

## 2. Volume Permission Errors

**Problem:**  
Mounted volumes cannot be accessed or written to inside the container.

**Fix:**  
a. Check file ownership and permissions on the host:
```bash
ls -l /your/volume/path
```
b. Use appropriate flags or change ownership:
```bash
sudo chown -R 1000:1000 /your/volume/path
```

---

## 3. Networking Issues

**Problem:**  
Containers cannot communicate with each other.

**Fix:**  

(a) Ensure containers are on the **same Docker network**:
```bash
docker network ls
docker network inspect <network_name>
```

(b) Connect containers to a **user-defined bridge**:
```bash
docker network create my-network
docker run --network my-network <image>
```

---

## Lessons Learned

- Always check for port usage before running a container to avoid conflicts.
- Volume permission issues are often caused by mismatched user IDs between host and container.
- Networking problems are usually due to containers not being on the same network.
- User-defined networks improve container-to-container communication and DNS resolution.
