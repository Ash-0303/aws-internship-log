# Day 21 – Exposing Kubernetes Apps with Services and Deploying a Flask App

**Date:** 4th August 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Kubernetes (Minikube on Linux/Ubuntu)

---

## Task Summary

- Learned about Kubernetes Services and how they expose applications to external traffic.
- Created a Service to expose an Nginx Pod to the outside world.
- Deployed a custom Flask application in Kubernetes.
- Built and pushed a Docker image for the Flask app.
- Exposed the Flask app using a Kubernetes Service.

---

## 1. What is a Kubernetes Service?

A **Service** allows external access to Pods running inside a Kubernetes cluster.  
Think of a Service as a **door** or **load balancer** that directs traffic to one or more Pods.

Example use case:
- A web server running inside a Pod needs to be accessible from your local machine or the internet.

---

## 2. Creating a Service for Nginx

### Example Service Manifest (`service.yaml`):
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: NodePort
```

**Apply the Service:**
```bash
kubectl apply -f service.yaml
```

**Access the Nginx app:**
```bash
minikube service my-nginx-service --url
```
This command:
- Creates a tunnel to the Service.
- Prints a URL that opens the Nginx welcome page.

---

## 3. Practice Task – Deploy a Flask App

### Step 1: Create Flask App Files

**`app.py`:**
```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello from Flask on Kubernetes!"
```

**`Dockerfile`:**
```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY app.py .
RUN pip install flask
CMD ["python", "app.py"]
```

---

### Step 2: Build and Push Docker Image

Inside Minikube Docker daemon:
```bash
eval $(minikube docker-env)
docker build -t flask-app:latest .
```

---

### Step 3: Create Pod Manifest (`flask-pod.yaml`):
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: flask-pod
  labels:
    app: flask-app
spec:
  containers:
    - name: flask-container
      image: flask-app:latest
      ports:
        - containerPort: 5000
```

**Apply the Pod:**
```bash
kubectl apply -f flask-pod.yaml
```

---

### Step 4: Create Service Manifest (`flask-service.yaml`):
```yaml
apiVersion: v1
kind: Service
metadata:
  name: flask-service
spec:
  selector:
    app: flask-app
  ports:
    - protocol: TCP
      port: 5000
      targetPort: 5000
  type: NodePort
```

**Apply the Service:**
```bash
kubectl apply -f flask-service.yaml
```

**Access the Flask app:**
```bash
minikube service flask-service --url
```
Opens the Flask app in the browser.

---

## Lessons Learned

- Kubernetes Services enable external access to internal Pods.
- **NodePort** type Services are a simple way to expose an app locally in Minikube.
- Building custom Docker images allows deployment of your own applications in Kubernetes.
- A Service can be used for both single-Pod and multi-Pod workloads.