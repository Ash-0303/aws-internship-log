# Day 42 – Installing an Open‑Source Helm App (Podinfo) & Troubleshooting

**Date:** 3rd September 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Kubernetes (Minikube on WSL/Ubuntu)

---

## Task Summary
- Installed the community **podinfo** Helm chart to practice real‑world Helm operations.
- Exposed it via NodePort and verified endpoints.
- Practiced upgrades and observed rollout/rollback behavior.
- Gathered logs and events to troubleshoot common issues.

---

## 1. Install podinfo
```bash
helm repo add podinfo https://stefanprodan.github.io/podinfo
helm repo update

kubectl create ns oss
helm upgrade --install podinfo podinfo/podinfo -n oss   --set service.type=NodePort
```

**Open it:**
```bash
kubectl -n oss port-forward svc/podinfo 8080:9898
```


---

## Lessons Learned
- `helm upgrade` is declarative and safe to run repeatedly; `helm history` + `helm rollback` provide release safety nets.
- Watching rollouts shows how Deployments manage ReplicaSets and health checks.
- Logs and events are the fastest way to understand why pods aren’t starting.
