# Day 15 – Docker Networking Overview and Implementation

**Date:** 26th July 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Docker on AWS EC2 (Ubuntu)

---

## Task Summary

- Studied Docker networking concepts and common network drivers.
- Explored `bridge`, `host`, and `none` network modes.
- Implemented user-defined bridge networks for improved DNS resolution.
- Ran containers with different networking modes and tested connectivity.
- Understood when to use specific network types and their pros/cons.

---

## Step 1: Overview of Docker Networking

- Docker’s networking subsystem is **pluggable** via network drivers.
- Common drivers:
  - **bridge** – Default for standalone containers; creates `docker0` interface.
  - **host** – Shares the host’s network stack (no isolation).
  - **none** – Disables networking entirely.
  - **overlay** – Multi-host networking (e.g., Docker Swarm).
  - **macvlan** – Assigns a MAC address to the container, making it appear as a physical device.

**Key Commands:**
```bash
docker network ls
docker network inspect <network_name>
docker container run --name <name> --network <network_name> <image>
```

---

## Step 2: None Network

- Disables the networking stack for a container.
- No IP address; only loopback (`lo`) is available.
- Use cases: **security testing** and **debugging without network access**.

**Commands:**
```bash
docker network ls
docker container run --name my-none --network none alpine
docker ps
```

---

## Step 3: Bridge Network Driver

- **Default network** for containers without a specified network.
- Containers on the same bridge network can communicate via IP.
- Traffic flows through `docker0` (default gateway: `172.17.0.1`).

**Example:**
```bash
docker container run -dit --name bridge01 ubuntu
docker container run -dit --name bridge02 ubuntu
docker container inspect bridge01 | grep "IPAddress"
apt-get update && apt-get install net-tools iputils-ping
ping <bridge02_IP>
```

---

## Step 4: Host Networking

- Removes isolation; container uses host’s network stack directly.
- No `-p` port publishing required—container binds directly to host ports.
- **Advantages:** Low latency, easy port binding.
- **Disadvantages:** Reduced isolation, port conflicts.

**Example:**
```bash
docker container run -dt --name myhost --network host ubuntu
```

---

## Step 5: User-Defined Bridge Network

- Advantages over default bridge:
  - Automatic DNS resolution (use container names).
  - Better isolation and flexibility.
  - Custom subnets and gateways.
- **Commands:**
```bash
docker network create --driver bridge my_bridge
docker container run -d --name my_bridge01 --network my_bridge nginx
docker container run -d --name my_bridge02 --network my_bridge nginx
ping my_bridge02
```

---

## Lessons Learned

- The `docker0` interface acts as the default gateway for containers on bridge networks.
- The **none** network is ideal for isolating containers.
- **Host** networking is useful for high-performance or monitoring scenarios but has security trade-offs.
- **User-defined bridge** networks improve DNS-based communication and allow custom configurations.
- Containers in different networks cannot communicate unless explicitly connected.
