# Day 40 – Deploying WordPress with Helm & Exploring `values.yaml`

**Date:** 29th August 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Kubernetes (Minikube on WSL/Ubuntu)

---

## Task Summary
- Added the Bitnami Helm repository and updated indexes.
- Installed WordPress via Helm into a dedicated namespace.
- Exposed the app (NodePort or LoadBalancer + `minikube tunnel`) and verified it in a browser.
- Explored the chart’s configuration through `helm show values` to understand tunables.

---

## 1. Add repo & update
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm search repo wordpress
```

---

## 2. Install WordPress (NodePort option)
```bash
kubectl create ns wordpress
helm install my-wp bitnami/wordpress -n wordpress   --set service.type=NodePort   --set wordpressUsername=admin   --set wordpressPassword='ChangeMe123!'   --set mariadb.auth.rootPassword='RootPass123!'
```

**Open it:**
```bash
minikube service -n wordpress my-wp-wordpress --url
```

> **LoadBalancer option:** set `--set service.type=LoadBalancer` and run `sudo -E minikube tunnel`, then:  
> `kubectl get svc -n wordpress my-wp-wordpress -w` to obtain the external IP.

---

## 3. Verify
```bash
kubectl get pods -n wordpress
kubectl get svc -n wordpress
helm status my-wp -n wordpress
```

---

## 4. Explore `values.yaml`
```bash
helm show values bitnami/wordpress | less
```
Check keys such as: `service.type`, `wordpressUsername`, `resources`, `mariadb.primary.persistence`, `livenessProbe`, `readinessProbe`.

---

## Lessons Learned
- Helm packages (charts) allow quick, repeatable app installs.
- `values.yaml` is the contract surface for configuration—prefer overriding values instead of editing templates.
- On Minikube, `NodePort` is simplest; `LoadBalancer` requires `minikube tunnel` to allocate an external IP.
