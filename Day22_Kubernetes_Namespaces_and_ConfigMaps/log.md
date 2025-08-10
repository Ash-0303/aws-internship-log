# Day 22 – Organising Kubernetes Resources with Namespaces and ConfigMaps

**Date:** 5th August 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Kubernetes (Minikube on Linux/Ubuntu)

---

## Task Summary

- Learned about Kubernetes **Namespaces** for separating and organising resources.
- Created and applied **ConfigMaps** to store non-sensitive configuration data.
- Injected ConfigMaps into Pods using environment variables and volume mounts.
- Debugged an issue with namespace-scoped ConfigMaps.

---

## 1. Namespaces in Kubernetes

A **Namespace** is like a folder or environment (e.g., `dev`, `test`, `prod`).  
It keeps resources isolated from each other.

**Create a Namespace:**
```bash
kubectl create namespace my-namespace
```

**Deploy a Pod into the Namespace:**
```bash
kubectl run my-nginx --image=nginx --namespace=my-namespace
```

---

## 2. ConfigMaps in Kubernetes

A **ConfigMap** stores non-sensitive configuration data and injects it into containers.

**Example:** Settings for different environments (`colour=blue`, `environment=production`).

**Create ConfigMap Manifest (`configmap.yaml`):**
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  app.properties: |
    colour=blue
    environment=production
```

**Apply the ConfigMap:**
```bash
kubectl apply -f configmap.yaml
```

---

## 3. Using a ConfigMap in a Pod

### Option 1: Inject as Environment Variables
```yaml
envFrom:
  - configMapRef:
      name: my-config
```

### Option 2: Mount as a Volume
```yaml
volumes:
  - name: config-volume
    configMap:
      name: my-config

volumeMounts:
  - name: config-volume
    mountPath: /etc/config
```
The container will then see a file `/etc/config/app.properties`.

---

## 4. Troubleshooting Issue – ConfigMap in Wrong Namespace

**Problem:**  
The Pod was stuck in `Pending` state because it could not find the `my-config` ConfigMap.

**Cause:**  
The ConfigMap was created in the **default namespace**, but the Pod was running in `my-namespace`.

**Fix:**  
Recreate the ConfigMap in the correct namespace:
```bash
kubectl apply -f configmap.yaml --namespace=my-namespace
```

---

## Lessons Learned

- Namespaces keep Kubernetes resources isolated.
- ConfigMaps allow injecting configuration without rebuilding images.
- Always create ConfigMaps in the same namespace as the Pods that use them.
- ConfigMaps can be consumed as environment variables or as mounted volumes.