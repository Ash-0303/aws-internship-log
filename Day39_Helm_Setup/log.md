# Day 39 – Getting the Cluster Ready (kubectl + Minikube + Helm)

**Date:** 28th August 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Kubernetes (Minikube on WSL/Ubuntu)

---

## Task Summary
- Verified `kubectl` installation and context configuration.
- Installed and started a local Kubernetes cluster using Minikube (Docker driver).
- **Installed Helm 3** and verified the client version.
- Confirmed cluster health: API server reachable, node Ready, core add-ons running.
- Learned how kubeconfig/contexts work and how `kubectl` discovers the cluster.

---

## 1. Verify kubectl & contexts
```bash
kubectl version --client
kubectl config get-contexts
kubectl config current-context
```

**If the client shows** _“The connection to the server localhost:8080 was refused”_: you don’t have a cluster/context yet—move to Step 2.

---

## 2. Install & start Minikube
```bash
# Install (one time)
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64   && sudo install minikube /usr/local/bin/minikube

# Start a 1-node cluster using Docker
minikube start --driver=docker
```

---

## 3. Basic health checks
```bash
kubectl cluster-info
kubectl get nodes
kubectl get pods -A
```

- Node should be **Ready**.
- `kube-system` pods should be **Running**.

---

## 4. Optional: Enable dynamic storage (handy later)
```bash
minikube addons enable storage-provisioner
minikube addons enable default-storageclass
kubectl get sc
```

---

## 5. Install Helm 3 & verify
```bash
# One-line installer from the official Helm repo
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

# Confirm Helm is installed
helm version
```

> Optional (for later labs): add a repo and refresh indices
> ```bash
> helm repo add bitnami https://charts.bitnami.com/bitnami
> helm repo update
> ```

---

## Lessons Learned
- `kubectl` talks to a cluster using **kubeconfig contexts**; if no context exists, connections go to `localhost:8080` and fail.
- Minikube creates a single-node cluster perfect for local development and Helm testing.
- Helm 3 is a client-side package manager for Kubernetes; once installed, it can install/upgrade/rollback apps via charts.
- Add-ons such as the **storage provisioner** provide dynamic PVC provisioning to avoid Pending PVCs later.
