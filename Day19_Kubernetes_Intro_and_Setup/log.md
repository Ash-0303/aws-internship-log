# Day 19 – Introduction to Kubernetes and Setting Up a Local Cluster

**Date:** 2nd August 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Kubernetes (Minikube on Linux/Ubuntu)

---

## Task Summary

- Learned what Kubernetes is and why it’s used for container orchestration.
- Understood key Kubernetes concepts: Pod, Node, Deployment, Service, Namespace.
- Set up a local Kubernetes cluster using Minikube.
- Installed `kubectl` for cluster management.
- Deployed a simple Nginx application as a Kubernetes Pod.

---

## What is Kubernetes?

Kubernetes (K8s) is an open-source platform for **automating deployment, scaling, and management** of containerized applications.

**Why use Kubernetes?**
- Automatically manages containers across a cluster of machines.
- Ensures application **availability, scalability, and resilience**.
- Simplifies **rolling updates**, **load balancing**, and **self-healing**.

**Key Kubernetes Concepts:**
1. **Pod** – The smallest deployable unit in Kubernetes that runs one or more containers.
2. **Node** – A virtual or physical machine where Pods run.
3. **Deployment** – Manages the lifecycle of Pods (create, update, delete, rollback).
4. **Service** – Stable network endpoint to access Pods.
5. **Namespace** – Logical separation of resources in a cluster.

---

## Step 1: Set Up a Local Kubernetes Cluster

Using **Minikube** to create a single-node Kubernetes cluster.

### For Linux (Ubuntu/Debian):

**(a) Download Minikube:**
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```

**(b) Install Minikube (move binary to system path):**
```bash
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

---

## Step 2: Start Minikube

```bash
minikube start
```
This will:
- Create a local VM (or use Docker depending on your setup).
- Install Kubernetes on that VM.
- Start a Kubernetes node.

---

## Step 3: Install kubectl (Kubernetes Command-Line Tool)

**(a) Download kubectl:**
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

**(b) Install kubectl:**
```bash
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

---

## Step 4: Verify Setup

Check cluster nodes:
```bash
kubectl get nodes
```
Example output:
```
NAME           STATUS   ROLES           AGE   VERSION
minikube       Ready    master          5m    v1.28.3
```

---

## Practice Task: Deploy First App

### 1. Create a Pod running Nginx
```bash
kubectl run my-nginx --image=nginx
```

### 2. Check the Pod is running
```bash
kubectl get pods
```
Expected output:
```
NAME        READY   STATUS    RESTARTS   AGE
my-nginx    1/1     Running   0          10s
```

---

## Lessons Learned

- Kubernetes orchestrates containers across multiple machines for scalability and resilience.
- Minikube is a quick way to set up a local test cluster.
- `kubectl` is the primary tool for interacting with Kubernetes clusters.
- Pods are the smallest deployable unit in Kubernetes.