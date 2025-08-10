# Day 17 – Introduction to Docker Compose and Multi-Container Applications

**Date:** 28th July 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Docker on AWS EC2 (Ubuntu)

---

## Task Summary

- Learned the purpose and benefits of Docker Compose.
- Created and ran a multi-container application using a `docker-compose.yml` file.
- Connected WordPress and MySQL containers in one configuration.
- Troubleshot missing environment variables and fixed database connectivity.

---

## Step 1: Why Use Docker Compose?

- Simplifies multi-container management by using **one file** to define all services.
- Supports defining networks, volumes, and environment variables.
- Enables starting all services with a single command:
```bash
docker-compose up
```

**Key Benefits:**
- Uses **YAML** for easy readability.
- Manages multi-container apps as a single project.
- Supports:
  - Networks
  - Volumes
  - Dependencies
  - Environment variables

---

## Step 2: Install Docker Compose

Ensure Docker is installed, then:
```bash
sudo apt install docker-compose
```
Or install the Docker Compose plugin:
```bash
docker compose
```

---

## Step 3: Create a docker-compose.yml File

Inside your project folder:
```bash
mkdir wordpress-app && cd wordpress-app
nano docker-compose.yml
```

Paste the configuration:
```yaml
version: '3'
services:
  mysql:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: example

  wordpress:
    image: wordpress:latest
    restart: always
    ports:
      - "8000:80"
    environment:
      WORDPRESS_DB_HOST: mysql:3306
      WORDPRESS_DB_USER: root
      WORDPRESS_DB_PASSWORD: example
```

---

## Step 4: Run the Application

```bash
docker-compose up -d
```
- Pulls required images, creates a network, and starts containers in detached mode.

---

## Step 5: Test in Browser

Visit:
```
http://localhost:8000
```
You should see the WordPress setup page.

---

## Step 6: View Running Containers

```bash
docker ps
docker-compose ps
```

---

## Step 7: Stop and Remove Everything

```bash
docker-compose down
```

---

## Troubleshooting Issue – Missing Database Configuration

- **Problem:** WordPress couldn't connect to the database because `MYSQL_DATABASE` was missing from MySQL config.
- **Fix:** Added `MYSQL_DATABASE` to the MySQL service and `WORDPRESS_DB_NAME` to the WordPress service in `docker-compose.yml`.

Updated configuration:
```yaml
version: '3'
services:
  mysql:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: example
      MYSQL_DATABASE: wordpress

  wordpress:
    image: wordpress:latest
    restart: always
    ports:
      - "8000:80"
    environment:
      WORDPRESS_DB_HOST: mysql:3306
      WORDPRESS_DB_USER: root
      WORDPRESS_DB_PASSWORD: example
      WORDPRESS_DB_NAME: wordpress
```

---

## Lessons Learned

- Docker Compose streamlines multi-container workflows.
- YAML syntax makes service definitions readable and maintainable.
- Missing environment variables can cause connectivity issues between containers.
- Always define database names explicitly in both DB and application services.
