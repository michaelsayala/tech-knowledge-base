# Docker – Quick Learning Guide

## Overview
This document provides a simple and practical explanation of Docker concepts, focusing on what they do and how they function in real-world scenarios.

---

## Docker

**What it is:**  
A platform for building, shipping, and running applications in containers.

**How it works:**
- Packages applications with dependencies into containers
- Ensures consistency across environments (dev, test, prod)
- Runs on a container runtime using the host OS kernel

---

## Images

**What it is:**  
A read-only template used to create containers.

**How it works:**
- Built from a Dockerfile
- Contains application code, runtime, libraries, and dependencies
- Stored in registries (e.g., Docker Hub)

---

## Containers

**What it is:**  
A running instance of a Docker image.

**How it works:**
- Lightweight and isolated environment
- Shares the host OS kernel
- Can be started, stopped, and deleted quickly

---

## Dockerfile

**What it is:**  
A script that defines how to build a Docker image.

**How it works:**
- Uses instructions such as:
  - `FROM` (base image)
  - `RUN` (execute commands)
  - `COPY` (add files)
  - `CMD` (default command)
- Built using `docker build`

---

## Docker Compose

**What it is:**  
A tool for defining and running multi-container applications.

**How it works:**
- Uses a `docker-compose.yml` file
- Defines services, networks, and volumes
- Start all services with a single command:
  - `docker-compose up`

---

## Volumes

**What it is:**  
Persistent storage for containers.

**How it works:**
- Stores data outside the container lifecycle
- Prevents data loss when containers are removed
- Can be shared across multiple containers

---

## Networks

**What it is:**  
Communication layer between containers.

**How it works:**
- Containers can communicate via internal DNS
- Supports:
  - Bridge networks (default)
  - Host networking
  - Custom networks for isolation

---

## Registries

**What it is:**  
Storage and distribution system for Docker images.

**How it works:**
- Public registry: Docker Hub
- Private registries available
- Common commands:
  - `docker pull`
  - `docker push`

---

## Docker CLI

**What it is:**  
Command-line interface to interact with Docker.

**How it works:**
- Common commands:
  - `docker build`
  - `docker run`
  - `docker ps`
  - `docker stop`
  - `docker rm`

---

## Container Lifecycle

**What it is:**  
The stages a container goes through.

**How it works:**
1. Create (from image)
2. Start (running state)
3. Stop (paused or terminated)
4. Remove (deleted)

---

## Best Practices

- Use small base images (e.g., Alpine)
- Minimize layers in Dockerfile
- Use `.dockerignore` to exclude unnecessary files
- Keep containers stateless when possible
- Use volumes for persistent data

---

## Summary

| Category        | Components                  |
|----------------|-----------------------------|
| Platform       | Docker                      |
| Build          | Dockerfile, Images          |
| Runtime        | Containers                  |
| Orchestration  | Docker Compose              |
| Storage        | Volumes                     |
| Networking     | Docker Networks             |
| Distribution   | Registries                  |
| Management     | Docker CLI                  |
