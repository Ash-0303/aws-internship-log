# Day 43 Hardening: RBAC, Resources, Probes & Rollback

**Date:** 4th September 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Kubernetes (Minikube on WSL/Ubuntu)

---

## Task Summary
- Restricted deployment permissions in the target namespace using a dedicated ServiceAccount and Role/RoleBinding.
- Tuned resources, replicas and health probes through chart values.
- Demonstrated a safe rollback after a failed upgrade.
- Documented the reasoning and verification steps.

---

## 1. Namespace‑scoped RBAC (example for namespace `demo`)
Save as `rbac-helm.yaml`:
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: helm-deployer
  namespace: demo
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: helm-deployer-role
  namespace: demo
rules:
  - apiGroups: ["", "apps", "networking.k8s.io"]
    resources: ["deployments","replicasets","pods","services","ingresses","configmaps","secrets"]
    verbs: ["get","list","watch","create","update","patch","delete"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: helm-deployer-rb
  namespace: demo
subjects:
  - kind: ServiceAccount
    name: helm-deployer
    namespace: demo
roleRef:
  kind: Role
  name: helm-deployer-role
  apiGroup: rbac.authorization.k8s.io
```
Apply:
```bash
kubectl apply -f rbac-helm.yaml
```

---

## 2. Tell the chart to use this SA + tune resources
Create `values.yaml` and apply to your `hello-web` chart:
```yaml
serviceAccount:
  create: false
  name: helm-deployer

replicaCount: 2

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

Upgrade and verify:
```bash
helm upgrade --install hello ./hello-web -n demo -f values.yaml
kubectl -n demo get deploy hello-hello-web -o jsonpath='{.spec.template.spec.serviceAccountName}{"\n"}'
kubectl rollout status deploy/hello-hello-web -n demo
```

---

## 3. Rollback drill
```bash
# introduce a bad change
helm upgrade hello ./hello-web -n demo -f values.yaml --set image.tag=does-not-exist || true

helm history hello -n demo
helm rollback hello 1 -n demo
kubectl rollout status deploy/hello-hello-web -n demo
```

---

## Lessons Learned
- Keep RBAC minimal and namespace‑scoped; deployers use a dedicated SA rather than default permissions.
- Always set resource requests/limits and probes for predictable scheduling and self‑healing.
- `helm rollback` is a powerful safety valve—practice it before you need it in production.