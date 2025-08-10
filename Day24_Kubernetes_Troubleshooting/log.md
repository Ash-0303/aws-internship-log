# Day 24 – Troubleshooting Common Kubernetes Issues

**Date:** 7th August 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Kubernetes (Minikube on Linux/Ubuntu)

---

## Task Summary

- Reviewed common Kubernetes issues and their troubleshooting steps.
- Learned how to diagnose Pods that are not running.
- Learned how to fix inaccessible Services.

---

## Issue 1: Pod Not Running

**Symptoms:**
- Pod stuck in `CrashLoopBackOff`, `Error`, or `Pending` state.

**What to do:**

1. **Check Pod logs:**
```bash
kubectl logs <pod-name>
```
- Shows container output/error messages.

2. **Describe the Pod:**
```bash
kubectl describe pod <pod-name>
```
- Gives detailed info about why it might be failing (e.g., wrong image, crash inside container).

---

## Issue 2: Service Not Accessible

**Symptoms:**
- Service is deployed but app cannot be accessed (no response on expected IP/port).

**What to check:**

1. **Check Service type:**
```bash
kubectl get service
```
- Service `TYPE` could be:
  - **ClusterIP**: Accessible only within the cluster.
  - **NodePort**: Exposes app on a port of your cluster node.
  - **LoadBalancer**: Public IP (in cloud environments).

2. **Check Service selector:**
```bash
kubectl describe service <service-name>
```
- Ensure selector labels match labels on your Pods.

3. **Check Pod labels:**
```bash
kubectl describe pods --show-labels
```

---

## Lessons Learned

- Always check logs first when troubleshooting Pods.
- `kubectl describe` provides detailed diagnostic info for both Pods and Services.
- Service accessibility issues are often due to incorrect `type` or mismatched selectors.
