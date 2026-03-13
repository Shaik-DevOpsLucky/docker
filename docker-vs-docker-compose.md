# Docker vs Docker Compose

## 1️⃣ What is Docker?

**Docker** is a containerization platform used to **build, run, and manage containers**.

A container packages:

* application code
* runtime
* libraries
* dependencies

So the application runs **the same everywhere**.

Example:

```bash
docker run -d -p 8080:80 nginx
```

This command:

1. Pulls the **nginx image**
2. Creates a **container**
3. Runs it in **detached mode**
4. Maps **port 8080 → 80**

---

## Docker Architecture (Simple Flow)

```
Docker CLI
     │
     ▼
Docker Engine
     │
     ▼
Docker Image → Docker Container
```

---

## Common Docker Commands

Build image

```bash
docker build -t myapp .
```

Run container

```bash
docker run -d -p 8080:80 myapp
```

List containers

```bash
docker ps
```

Stop container

```bash
docker stop container_id
```

---

# 2️⃣ What is Docker Compose?

**Docker Compose** is a tool used to **define and run multi-container Docker applications**.

Instead of running multiple `docker run` commands, we define everything in **one YAML file**.

Example use case:

Application with:

* frontend
* backend
* database

---

## Docker Compose Architecture

```
docker-compose.yml
        │
        ▼
Docker Compose
        │
        ▼
Multiple Containers
(frontend + backend + db)
```

---

# Example Docker Compose File

`docker-compose.yml`

```yaml
version: "3"

services:

  web:
    image: nginx
    ports:
      - "8080:80"

  database:
    image: mysql
    environment:
      MYSQL_ROOT_PASSWORD: root
```

---

# Run Entire Application

Start all containers:

```bash
docker-compose up -d
```

Stop all containers:

```bash
docker-compose down
```

View running services:

```bash
docker-compose ps
```

---

# Real DevOps Example

Without Docker Compose you run:

```bash
docker run -d --name db mysql
docker run -d --name backend backend-image
docker run -d --name frontend frontend-image
```

With Docker Compose:

```bash
docker-compose up -d
```

Everything starts automatically.

---

# Docker vs Docker Compose (Key Differences)

| Feature       | Docker                      | Docker Compose                   |
| ------------- | --------------------------- | -------------------------------- |
| Purpose       | Run single container        | Run multi-container applications |
| Configuration | CLI commands                | YAML file                        |
| Complexity    | Manual container management | Automated service orchestration  |
| Use case      | Single services             | Microservices applications       |
| Networking    | Manual                      | Automatic service networking     |

---

# Important Interview Answer (Short)

If interviewer asks:

**What is the difference between Docker and Docker Compose?**

Answer like this:

> Docker is used to build and run individual containers, whereas Docker Compose is a tool used to define and manage multi-container Docker applications using a YAML configuration file.

---

# DevOps Interview Tip

Many companies ask this follow-up question:

**Why do we use Docker Compose?**

Answer:

* simplifies multi-container applications
* manages networking automatically
* easy environment setup
* infrastructure defined as code

---
# Prepared by:
*Shaik Moulali*
# DevOps & Multi Cloud Engineer
