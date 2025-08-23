# Day 32 — Google Kubernetes Engine (GKE) on Ubuntu WSL

**Date:** 17th August 2025  
**Internship Role:** Cloud Support Engineer Intern (Prime One Global)  
**Platform:** Google Cloud (Ubuntu WSL + gcloud + kubectl)

---

## Task Summary

- Installed Google Cloud CLI **v535.0.0**, **gke-gcloud-auth-plugin**, and **kubectl** (client **v1.30.1**) on **WSL Ubuntu** using official APT repositories.  
- Verified project config: project **prime-01-469403**, region **europe-west2**, zone **europe-west2-a**; confirmed required APIs listed (`compute.googleapis.com`, `container.googleapis.com`).  
- Created a **GKE Autopilot** cluster named **demo-autopilot** in **europe-west2**.  
- Retrieved kubeconfig/context and verified access with `kubectl`.  
- Deployed Google sample **hello-app:1.0**, observed pods schedule, and saw Autopilot nodes appear on-demand.  
- Exposed the app as a **LoadBalancer** Service; tested the **EXTERNAL-IP** in browser.  
- Practiced rolling updates (`set image`), scaling, logs, and watch behavior.  
- Cleaned up workloads and (optionally) the cluster.

---

## Key Concepts Learned

1. **Autopilot** provisions/manages nodes automatically; nodes appear **after** workloads schedule.  
2. **GKE auth plugin** is required for kubectl auth; on WSL it’s provided by `google-cloud-cli-gke-gcloud-auth-plugin` and enabled by `USE_GKE_GCLOUD_AUTH_PLUGIN=True`.  
3. **Service type=LoadBalancer** allocates a Google external load balancer and public IP.  
4. **Rolling updates** via `kubectl set image` followed by `kubectl rollout status`.  
5. `kubectl get ... -w` is a **watch** stream; exit with **Ctrl+C**.  
6. Container names matter for `set image`; use `*=` to update all containers when unsure.

---

## Code Implementation

### A) Verify Toolchain

```bash
gcloud --version
gke-gcloud-auth-plugin --version
kubectl version --client
```

**Result**

```
Google Cloud SDK 535.0.0
gke-gcloud-auth-plugin 0.5.10
kubectl Client Version: v1.30.1
```

---

### B) Configure gcloud

```bash
gcloud config list
# (if needed)
gcloud config set project prime-01-469403
gcloud config set compute/region europe-west2
gcloud config set compute/zone europe-west2-a
```

**Result**

```
[compute]
region = europe-west2
zone   = europe-west2-a
[core]
account = ashwinram44@gmail.com
project = prime-01-469403
```

---

### C) (Check) Required APIs

```bash
gcloud services list | grep -E 'compute|container'
```

**Result**

```
compute.googleapis.com       Compute Engine API
container.googleapis.com     Kubernetes Engine API
```

---

### D) Create Cluster (Autopilot)

```bash
CLUSTER="demo-autopilot"
REGION="europe-west2"

gcloud container clusters create-auto "$CLUSTER" --region "$REGION"
```

**Result**

```
Creating cluster demo-autopilot in europe-west2... done.
MASTER_VERSION 1.33.2-gke.1240000   STATUS RUNNING   STACK_TYPE IPV4
```

---

### E) Connect & Inspect

```bash
gcloud container clusters get-credentials "$CLUSTER" --region "$REGION"
kubectl config current-context
kubectl get nodes
```

**Result (initial)**

```
gke_prime-01-469403_europe-west2_demo-autopilot
No resources found
```

**After deploying workloads (Autopilot spins up nodes):**

```
NAME                                         STATUS  AGE  VERSION
gk3-demo-autopilot-nap-...-jnqg              Ready   2m   v1.33.2-gke.1240000
gk3-demo-autopilot-nap-...-xzmn              Ready   2m   v1.33.2-gke.1240000
gk3-demo-autopilot-nap-...-w9mh              Ready   2m   v1.33.2-gke.1240000
```

---

### F) Deploy & Expose App

```bash
# Deploy
kubectl create deployment hello --image=gcr.io/google-samples/hello-app:1.0
kubectl get pods -w   # watch until Running, Ctrl+C to stop

# Expose
kubectl expose deployment hello --type=LoadBalancer --port=80 --target-port=8080
kubectl get svc hello -w
```

**Result**

```
NAME    TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)
hello   LoadBalancer   34.118.238.131  34.39.46.195    80:32375/TCP
```

**Test**

```bash
curl http://34.39.46.195/
# -> Hello, world!  Version: 1.0.0
```

---

### G) Scale, Update, Logs

```bash
# Scale replicas
kubectl scale deployment hello --replicas=3

# Update image (update all containers safely)
kubectl set image deploy/hello *=gcr.io/google-samples/hello-app:2.0
kubectl rollout status deploy/hello

# Logs and describe
kubectl logs deploy/hello --tail=50
kubectl describe svc hello
```

---

### H) Cleanup (avoid charges)

```bash
# Remove app resources
kubectl delete svc hello --ignore-not-found
kubectl delete deployment hello --ignore-not-found
kubectl delete hpa hello --ignore-not-found

# Optionally, delete cluster
gcloud container clusters delete demo-autopilot --region europe-west2 --quiet
```

**Result**

```
service "hello" deleted
deployment.apps "hello" deleted
# Cluster delete outputs status and removes the context from kubeconfig
```

---

## Practice Tasks

1. Create a separate **namespace**, apply **RBAC** for read-only viewing, deploy `hello` there.  
2. Build a small app, push to **Artifact Registry**, and deploy from the private image.  
3. Enable **HPA** and generate load to observe automatic scaling.  
4. Add a **ConfigMap** and **Secret**, mount them in the pod, and read values from the app.  
5. Try a **Standard** cluster and compare node visibility/management vs Autopilot.

---

## Lessons Learned

- With **Autopilot**, node provisioning is demand-driven—expect `No resources found` before deploying a pod.  
- The **GKE auth plugin** must be installed/enabled; WSL APT packages make this straightforward.  
- `-w` means “watch” and keeps the stream open; use **Ctrl+C** to return control.  
- Use `kubectl set image deploy/hello *=<image:tag>` when you don’t know the container name.  
- Always delete **LoadBalancer** Services and clusters when finished to avoid charges.