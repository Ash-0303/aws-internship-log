# Day 41 – Creating My Own Helm Chart (`hello-web`)

**Date:** 30th August 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Kubernetes (Minikube on WSL/Ubuntu)

---

## Task Summary
- Scaffolded a new chart using `helm create`.
- Customized the chart to run NGINX with sensible resources, probes, and a NodePort Service.
- Installed the chart as a release and verified accessibility in the browser.
- Learned the chart structure: `Chart.yaml`, `values.yaml`, `templates/`, helpers, and name templates.

---

## 1. Scaffold the chart
```bash
helm create hello-web
tree hello-web
```

---

## 2. Edit `values.yaml` (key bits)
```yaml
image:
  repository: nginx
  tag: "stable"

replicaCount: 2

service:
  type: NodePort
  port: 80

resources:
  requests: { cpu: "100m", memory: "128Mi" }
  limits:   { cpu: "300m", memory: "256Mi" }

livenessProbe:
  enabled: true
  initialDelaySeconds: 20
  periodSeconds: 10
readinessProbe:
  enabled: true
  initialDelaySeconds: 10
  periodSeconds: 5
```

---

## 3. Install your chart
```bash
kubectl create ns demo
helm upgrade --install hello ./hello-web -n demo
kubectl get pods,svc -n demo
```

**Open it:**
```bash
minikube service -n demo hello-hello-web --url
# or set --set fullnameOverride=hello-web and then:
# minikube service -n demo hello-web --url
```

---

## 4. Lessons Learned
- `helm create` generates a working chart plus helper templates that wire names and the service account automatically.
- Prefer recording overrides in a values file and use `helm upgrade --install` for idempotent deploys.
- `fullnameOverride` lets me control object names when I want predictable DNS/service names.
