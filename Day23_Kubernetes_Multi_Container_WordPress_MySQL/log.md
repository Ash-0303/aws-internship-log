# Day 23 – Deploying a Multi-Container Application in Kubernetes (WordPress + MySQL)

**Date:** 6th August 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Kubernetes (Minikube on Linux/Ubuntu)

---

## Task Summary

- Deployed a multi-container application (WordPress + MySQL) using Kubernetes.
- Learned how front-end (WordPress) and back-end (MySQL) components work together.
- Created and applied Kubernetes manifests for Deployments and Services.
- Used `minikube service` to access applications running inside Minikube.

---

## 1. What is a Multi-Container App?

Think of it as:
- **Front-end** – WordPress: User interface.
- **Back-end** – MySQL: Database.

---

## 2. Manifests to Create

1. `mysql-deployment.yaml` – Deploys MySQL container.
2. `mysql-service.yaml` – Makes MySQL DB accessible internally.
3. `wordpress-deployment.yaml` – Deploys WordPress container.
4. `wordpress-service.yaml` – Exposes WordPress via LoadBalancer or NodePort.

---

## 3. Why `minikube IP` Didn’t Work

**Reason:**
- Minikube runs inside a Docker container/VM on your machine.
- The Minikube IP (e.g., `192.168.94.2`) is internal to Docker, **not** directly routable from the host.
- Browser requests to `192.168.94.2:30080` never reach the Minikube container.

**Network Flow:**
```
Host (Laptop)
   |
   |---> Docker network (bridge)
             |
             ---> Minikube VM (192.168.94.2) – not routable from host
```

---

## 4. Correct Way – Using `minikube service`

Command:
```bash
minikube service wordpress
```
This does two things:
1. Opens a local port-forwarded tunnel from host to WordPress Service inside the cluster.
2. Provides a localhost-accessible URL:
```
http://127.0.0.1:37303
```

**Result:**  
The WordPress site loads successfully by bypassing Minikube’s internal isolation.

---

## Lessons Learned

- Multi-container apps in Kubernetes require separate manifests for each service and deployment.
- Minikube IP addresses are internal and not directly accessible from the host.
- `minikube service <service-name>` is the easiest way to access a Service running in Minikube locally.
- LoadBalancer/NodePort services are needed for external access in real clusters.