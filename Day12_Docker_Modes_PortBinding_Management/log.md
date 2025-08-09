# Day 12 – Docker Container Modes, Port Binding, Identification & Management

**Date:** 22nd July 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Docker on Ubuntu Server (AWS EC2)  

---

## Task Summary
- Learned the difference between **attached** and **detached** container modes.
- Understood **Docker port binding** and how it enables external access to container services.
- Explored different ways to **identify containers** (UUID, short ID, and names).
- Practiced stopping and removing containers individually and in bulk.
- Applied concepts using Nginx containers for hands-on practice.

---

## Practical Implementation

### 1. Attached vs Detached Modes
- **Attached Mode** (default): Runs in foreground, shows logs in real-time, blocks terminal until stopped.
  ```bash
  docker run --name attached -p 8080:80 nginx
  ```
  - Stop with `Ctrl + C` (container exits).
  - Good for immediate log monitoring.

- **Detached Mode** (`-d` flag): Runs in background, returns control to terminal immediately.
  ```bash
  docker run -d --name detached -p 8081:80 nginx
  ```
  - Service accessible at `http://<your-ip>:8081`.
  - View logs using:
    ```bash
    docker logs detached
    ```

---

### 2. Docker Port Binding
- Maps a host port to a container port, enabling external access.
- Syntax:
  ```bash
  docker run -d -p <host_port>:<container_port> <image>
  ```
- Example:
  ```bash
  docker run -d -p 8080:80 nginx
  ```
  - Maps host port 8080 → container port 80.
  - Access via browser: `http://<host-ip>:8080`.

---

### 3. Container Identification
Docker supports three identification methods:
1. **Full UUID** – complete unique ID.
2. **Short ID** – first 12 characters of UUID.
3. **Container Name** – user-defined or auto-generated.

- Naming containers:
  ```bash
  docker run -d -p 8000:80 --name mynginx nginx
  ```
- All identification forms can be used in commands:
  ```bash
  docker stop mynginx
  docker stop 711abf1d2f3c
  ```

---

### 4. Stopping and Removing Containers
- **View running containers**:
  ```bash
  docker ps
  ```
- **View all containers** (including stopped):
  ```bash
  docker ps -a
  ```
- **Stop a container**:
  ```bash
  docker container stop <name_or_id>
  ```
- **Remove a container**:
  ```bash
  docker container rm <name_or_id>
  ```
- **Bulk stop all running containers**:
  ```bash
  docker container stop $(docker container ls -q)
  ```
- **Bulk remove all containers**:
  ```bash
  docker container rm $(docker container ls -aq)
  ```

---

## Outcome
- Confidently run containers in both attached and detached modes.
- Understand and use Docker port binding for external access.
- Identify containers using UUID, short ID, and names.
- Manage container lifecycle with stop and remove commands, both individually and in bulk.

---