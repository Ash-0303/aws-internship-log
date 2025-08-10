# Day 20 – Kubernetes Pods and Deployments

**Date:** 3rd August 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Kubernetes (Minikube on Linux/Ubuntu)

---

## Task Summary

- Learned the difference between Pods and Deployments in Kubernetes.
- Created a Pod manifest to run a single Nginx container.
- Created a Deployment manifest to manage multiple replicas of the Nginx Pod.
- Practiced scaling Deployments to handle increased load.

---

## 1. Pods in Kubernetes

A **Pod** is the smallest unit in Kubernetes. Think of it as a wrapper around a container.

### Example Pod Manifest (`pod.yaml`):
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-nginx-pod
spec:
  containers:
    - name: nginx-container
      image: nginx
```

**What this does:**
- Creates a Pod named `my-nginx-pod`.
- Runs an Nginx container inside it.
- Will automatically restart the container if it crashes.

**Apply the manifest:**
```bash
kubectl apply -f pod.yaml
```

---

## 2. Deployments in Kubernetes

A **Deployment** manages Pods for you:
- Defines the desired state (number of replicas, image, etc.).
- Automatically restarts failed Pods.
- Supports scaling and rolling updates.

### Example Deployment Manifest (`deployment.yaml`):
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx-container
          image: nginx
```

**What this does:**
- Runs 3 replicas of the Nginx container.
- Each replica runs in its own Pod.

**Apply the manifest:**
```bash
kubectl apply -f deployment.yaml
```

---

## 3. Practice Task – Scaling the Deployment

Scale to 5 replicas:
```bash
kubectl scale deployment my-nginx-deployment --replicas=5
```

**Verify:**
```bash
kubectl get pods
```
You should now see **5 Pods** running with names starting with `my-nginx-deployment`.

---

## Lessons Learned

- **Pods** run containers and are the basic unit of deployment in Kubernetes.
- **Deployments** manage multiple Pods, providing scalability and resilience.
- Scaling Deployments is useful for handling increased application traffic.