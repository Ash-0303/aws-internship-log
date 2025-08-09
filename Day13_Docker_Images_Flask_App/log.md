# Day 13 – Pulling, Managing, and Creating Custom Docker Images

**Date:** 23rd July 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Docker on AWS EC2 (Ubuntu)

---

## Task Summary

- Pulled a Docker image from Docker Hub.  
- Listed and managed Docker images.  
- Created a custom Docker image with Python and Flask.  
- Built and ran a simple Flask web app inside a Docker container.

---

## Step 1: Pull Docker Image from Docker Hub

Docker Hub hosts prebuilt images you can use immediately.

**Command:**

```bash
docker pull nginx
```

This pulls the latest nginx image.

---

## Step 2: List and Manage Images

- **List all downloadable images:**

```bash
docker images
```

- **Remove an image:**

```bash
docker rmi hello-world
```

---

## Step 3: Create a Custom Docker Image

### 3.1. Create your project directory

```bash
mkdir my-python-app
cd my-python-app
```

### 3.2. Add your app files

- **Create a file named `app.py`:**

```bash
touch app.py
```

- Paste the following code inside the file:

```python
print("Hello World")
```

- **Create a file named `requirements.txt`** and inside the file paste the following content:

```txt
# requirements.txt
# (Empty or add any dependencies like Flask if needed)
```

### 3.3. Create a Dockerfile

- Create a file named `Dockerfile` (no file extension) and paste:

```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```

### 3.4. Build the Docker image

```bash
docker build -t my-python-app .
```

---

## Step 4: Run a Flask Web App

### 4.1. Update `app.py` so that it contains the following code:

```python
# app.py
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello from Flask in Docker!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=80)
```

### 4.2. Update your `requirements.txt`:

```txt
flask
```

### 4.3. Rebuild the image and run the container

```bash
docker build -t my-python-app .
docker run -d -p 80:80 my-python-app
```

### 4.4. Check if it works

Open a browser and go to:

```
http://<public_ip_of_your_EC2>:80
```

You should see:

```
Hello from Flask in Docker!
```

---

## Lessons Learned

- Docker Hub provides quick access to prebuilt images.  
- You can easily create custom Docker images by defining a `Dockerfile` and building it.  
- Flask can be containerized and run with minimal setup inside Docker.  
- Mapping ports with `-p` allows external access to containerized apps.
