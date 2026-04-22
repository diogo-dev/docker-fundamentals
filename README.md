# 🐳 Docker Notes & Cheat Sheet

Comprehensive notes on Docker, concepts, commands, best practices and essential components.

---

## 📌 Problem: "It works on my machine"

### Challenges
- **Management tools**: Different tools in each environment
- **Different configuration**: Configuration specific to OS and hardware
- **Hardware dependencies**: Hardware dependencies vary between machines

### Previous Solutions
- Configuration management tools (require knowledge of hardware and OS)
- Virtual machines

### ✅ Docker Solution
Docker uses **images and containers** to allow apps to run anywhere, in a **consistent** way!

---

## 🧱 Docker Fundamentals

### 📦 Docker Images
- Created from **Dockerfile** (configuration file)
- Describe everything your application needs to run
- Composed of **compressed layers** from other images (intermediate images)
- Each command in the Dockerfile creates a layer

### 📦 Containers
- Instances of images in execution
- Isolated and lightweight
- Portable and consistent

---

## ⚖️ Container vs Virtual Machine

### Virtual Machine (VM)
- ✓ Virtualize complete hardware
- ✓ Run on top of hypervisors
- ✓ Require hardware emulation
- ✓ Require OS configuration
- ✓ Can run multiple apps simultaneously
- ✗ Cannot interact with host machine
- ✗ More secure but **slower and heavier**

### Containers
- ✓ Run in container runtime
- ✓ Work alongside the OS (share Linux kernel)
- ✓ Do not require OS configuration
- ✓ Usually run one app at a time
- ✓ Can interact with the host
- ✓ **Faster and lighter**
- ⚠ Less secure than VMs

---

## 🏗️ Anatomy of a Container

A **CONTAINER** is composed of two main components:

### 1. Linux Namespace
- Allows administrator to restrict what processes can see on the system
- Isolates resources and visibility between containers

### 2. Linux Control Group (cgroup)
Limits resources that a container can use:
- Monitor and restrict CPU usage
- Monitor and restrict network and disk bandwidth
- Monitor and restrict memory consumption
  - Prevents one container from slowing down other containers

---

## ⚠️ Docker Limitations

- **Runs natively on Linux only**
- **Container images are bound to parent OS**
  - Example: Containers created for Linux images only run on Linux
  - Windows containers only run on Windows
- **Currently there are workarounds** for these limitations

---

## ✨ 3 Major Advantages of Docker

1. **Dockerfiles** make configuring and packaging apps and their environments really easy
2. **Docker Hub** makes sharing images with anyone in the world easy
3. **Docker CLI** makes it really easy to start your apps in containers

---

## 🔧 Docker Machine and Docker Desktop

### Docker Machine (First Solution)
First solution to run Docker on Windows and Mac:
- Created a small VM (VirtualBox) that only ran the Docker Engine
- **Problems:**
  - Slower than running on Linux
  - Required VirtualBox knowledge when things went wrong

### Docker Desktop (2016) - 3 Improvements
- Uses much smaller and more tightly integrated VM
- Automatically handles volumes and port mapping
- Comes with really nice GUI

---

## 🌐 Docker Alternatives

### Podman
- Created by **RED HAT**
- Container runtime that emphasizes more security
- Supports **rootless containers** (containers that cannot run as root)
- More secure than Docker in certain scenarios

---

## 🧪 Docker Commands - Complete Guide

### ▶️ Running a Container
```bash
docker run IMAGE_NAME
# Equivalent to: docker container create + docker container start + docker container attach

docker run -d --name=my-container -p 3000:3000 my-image
```
**Flags:**
- `-d` (detached mode): Container runs not attached to the terminal
- `--name`: Give a name for the container
- `-p OUTSIDE:INSIDE`: Port binding. Binds OUTSIDE Port (host machine) with INSIDE Port (container)

### 📜 Logs
```bash
docker logs my-container
# Use when container ran in detached mode (-d flag)
```

### 🏗️ Building an Image
```bash
docker build -t image-name .

# Useful flags:
docker build --rm=true image-name          # Remove intermediate containers if build succeeds
docker build --force-rm=true image-name    # Remove intermediate containers even if build fails
docker build --no-cache image-name         # Ignore cache (useful when previous build failed)
```

**What happens:**
- Docker creates an image for each Dockerfile command
- After reading the entire Dockerfile, it "squashes" all images into a single image
- You can see image IDs being printed on the terminal (intermediate images)

### 🔍 Searching Images on Docker Hub
```bash
docker search IMAGE_TO_SEARCH

# With filters:
docker search --filter is-official=true python      # Only official images
docker search --filter stars=100 python             # Images with minimum 100 stars
docker search --filter is-automated=true python     # Automated images
```

### 📥 Pulling an Image
```bash
docker image pull IMAGE_NAME
```

### 📋 Listing Images
```bash
docker image ls
docker images

# Options:
docker image ls --all              # Include intermediate images
docker image ls --no-trunc         # Output not truncated
docker image ls --quiet            # Only IDs
docker image ls --filter dangling=true  # Filter intermediate images
```

### 🏷️ Tag
```bash
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
```

### 🔧 Executing a Command in a Container
```bash
docker exec CONTAINER_ID COMMANDS

# Interactive mode:
docker exec --interactive --tty CONTAINER_ID bash
docker exec -it CONTAINER_ID bash

# Practical example (PostgreSQL):
docker exec -it postgres:15 psql -U diogo -d my_db -p 5432
```

### ⛔ Stopping a Container
```bash
docker stop CONTAINER_ID

# Stop immediately (careful - can cause data loss):
docker stop -t 0 CONTAINER_ID
```

---

## 🧠 Docker Commands - Tricks and Tips

### Listing Containers
```bash
docker ps -a        # All containers
docker ps -aq       # Only IDs
docker ps -a -q     # Only IDs (alternative)
```

### Removing Containers
```bash
docker rm -f CONTAINER_ID  # Remove even if running
```

### Remove ALL Containers with One Command

**Linux/Mac:**
```bash
docker ps -aq | xargs docker rm
```
*`xargs` captures output from one command and converts it into arguments for another command*

**Windows (PowerShell):**
```bash
docker ps -aq | ForEach-Object { docker rm $_ }
docker rm $(docker ps -aq)
```

### Removing Images
**Linux/Mac:**
```bash
docker images -q | xargs docker rmi
```

**Windows (PowerShell):**
```bash
docker images -q | ForEach { docker rmi $_ }
```

### Removing Unnecessary Space
```bash
# Removes unused Docker objects (smartly)
docker system prune
docker system prune --volumes     # Volumes are not pruned by default

# See how much space is currently being used (safety check!)
docker system df
```

---

## 📊 Troubleshooting - Slow Containers

### docker stats
- Snapshot of your container's performance in real-time

### docker top
- Shows what's running inside the container without needing to exec

### docker inspect
- Shows advanced information about a running container in JSON format
- Convenient for scripting

---

## 💾 Data Persistence - Volumes and Bind Mounts

### The Problem
Everything created within a container stays within a container. Once the container stops, its data gets deleted with it.

### Solution: Volume Mounting
Use the `-v` flag to map files from your computer to the container:

```bash
-v OUTSIDE:INSIDE
```

**Example:**
```bash
docker run --rm --entrypoint sh -v /tmp/container:/tmp ubuntu -c "echo 'Hello there.' > /tmp/file && cat /tmp/file"
```

**⚠️ Important:** If you map a file that doesn't exist, Docker maps it as a **directory**

```bash
# File doesn't exist - creates directory:
docker run --rm --entrypoint sh -v /tmp/this_file_does_not_exist:/tmp/file ubuntu -c "echo 'Hello' > /tmp/file"
# Result: cannot create /tmp/file: Is a directory

# File exists - maps as file:
touch /tmp/change_this_file
docker run --rm --entrypoint sh -v /tmp/change_this_file:/tmp/file ubuntu -c "echo 'Hello' > /tmp/file && cat /tmp/file"
# Result: Hello (file was modified on host too!)
```

---

## 📦 Docker Volumes vs Bind Mounts

### Main Differences

**DOCKER VOLUMES:**
- Type of data storage provided by Docker
- Directory on the host accessible to a container
- Data persists even if container is deleted
- **Docker manages the location** where it's stored
- Creation: `-v ${VOLUME_NAME}:/path/on/container`

```bash
docker volume create VOLUME_NAME
docker volume ls
docker volume inspect VOLUME_NAME
```

**BIND MOUNTS:**
- **User is responsible** for the location where it's stored
- Creation: `-v /path/on/host:/path/on/container`
- **Very useful for Hot Reload Development**

### Access Modes
```bash
SOURCE:TARGET:ACCESS_MODE
# rw = read write (default)
# ro = read only
```

### Analogy: Your HD is a Warehouse

**Bind Mount:**
- You rent a specific spot on a shelf right at the entrance
- **You** decide where it stays, you organize it, you clean it
- Docker only has permission to put things in and take things out
- If you move the shelf, Docker gets lost

**Volume:**
- Docker says: "Leave it to me, I have a locked room in the back that only I organize"
- You don't need to know where the room is or what the shelf number is
- You just ask Docker: "Save this in the volume 'my_database'"
- Docker handles the rest

---

## 📦 Image Registry

### Docker Hub
- Default public registry used by Docker Client
- Anyone can push images

### Pushing an Image to Registry
```bash
docker tag my-app $DOCKER_HUB_USERNAME/my-app:v1
docker push $DOCKER_HUB_USERNAME/my-app:v1

# Practical example:
docker tag our-web-server diogouser/our-web-server:0.0.1
docker push diogouser/our-web-server:0.0.1
```

### Other Popular Registries
- GitHub Container Registry
- GitLab Container Registry (https://docs.gitlab.com/user/packages/container_registry)
- Artifact Registry (Google Cloud)
- Elastic Container Registry (AWS)
- Azure Container Registry
- Oracle Container Registry
- IBM Container Registry

### Private Image Registries
- Can be self-hosted
- Provide security, lower latency and useful features for enterprise
- Examples: JFrog Artifactory, Sonatype Nexus, Red Hat Quay, Project Harbor
- All cloud providers offer registry services

---

## 🚀 Buildkit: docker buildx

### What is it
Docker Desktop after 2023 already uses Buildkit as default.

### How to use with more options
```bash
docker buildx -t image-name .
# Gives more options than traditional docker build
```

📚 Learn more: https://docs.docker.com/build/buildkit

---

## 🌍 Docker Context

### What is it
Context enables you to create shortcuts that configure the machine that the Docker Client uses to do Docker-related things.

### What it's used for
- Build and run containers remotely
- Common in automated scenarios like CI/CD pipelines

### Commands
```bash
docker context ls              # List existing contexts
docker context use CONTEXT_NAME  # Switch to different context
```

### Environment Variables
```bash
$DOCKER_CONTEXT  # Override context
$DOCKER_HOST     # Override endpoint
```

---

## 🏷️ Tagging and Labeling Images

### TAGS
- Used to identify distinct versions of your image
- Common practice: mark each release with a tag

```bash
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
docker tag my-app:v1 ${DOCKERHUB_USER}/my-app:v1
```

### LABELS
- Use `LABEL` instruction in Dockerfile to keep your image organized
- Add metadata to an image using key-value pairs

```dockerfile
LABEL description="my description here"
```

### Filtering
```bash
# Can filter by labels (but NOT by tags)
docker images ls --filter label="description=my description here"
```

---

## 🔐 Docker Best Practices

### Security
- ✅ Use **Verified Images** (more secure)
- ✅ Use **container image scanner** if using unverified image
  - Clair, Trivy, Dagda (open source image scanners)
- ✅ Use **Non-root Users** (makes containers more secure)
  ```bash
  docker run --user somebody-else
  ```

### Versioning
- ❌ **Avoid tagging as `latest`**
  - You don't know the version of the app you're downloading
  - The app's version can change when you run `docker pull` later
  - `latest` can be overridden, making rollback difficult
- ✅ Use specific versioned tags

---

## 🐋 Kubernetes Overview

Kubernetes is a popular container orchestrator capable of managing very large numbers of containers:

- Uses distributed architecture to run and connect hundreds of thousands of containers with minimal hardware
- Makes grouping, scaling and connecting containers with the outside world really easy
- Load balancing and securing container traffic to/from the outside world are much easier with Kubernetes

---

## ⚙️ Docker Compose - Complete Guide

### What is it
**Configuration as Code:** A file (docker-compose.yaml) containing all settings that allow a system to run

### Benefits
- ✓ Version control
- ✓ Self-documenting
- ✓ Easy management
- ✓ **Declarative Configuration Tool** (specify desired end result, abstracts steps)

### Designed for
- ✅ Local development (non-production environments)
- ✅ Staging server
- ✅ Continuous integration testing environment

### NOT designed for
- ❌ Distributed systems
- ❌ Complex production environments
- For these production problems (scaling, etc), use an **orchestration tool** (like Kubernetes)

### Basic Commands
```bash
# Bring everything up
docker-compose up
# Equivalent to: docker-compose build && docker-compose create && docker-compose start
# - Build the images
# - Create the containers
# - Start the application

# Stop and remove everything
docker-compose down
# - Stop all containers
# - Delete all containers and images
# - Remove all artifacts

# Stop containers (less aggressive)
docker-compose stop
# - Save battery
# - Free up memory
# - Less aggressive than down, but down is better if you've made changes

# Restart
docker-compose restart
# Equivalent to: docker-compose stop + docker-compose start
```

### Environment Variables vs Build Arguments

#### Environment Variables
- Accessible **only inside the running container**
- Defined at runtime

```yaml
environment:
  - runtime_env=dev
```

Or with `.env` file:
```yaml
services:
  database:
    image: "mysql"
    env_file: 
      - ./.env
```

#### Build Arguments
- **Available only at build time**, not inside running container
- Used for: build tool versions, cloud platform configuration, etc

```yaml
build:
  context: .
  args:
    - region=us-east-1
    - node_version=18
```

### Volumes in Docker Compose

#### Without source (Docker creates automatically)
```yaml
volumes:
  - /var/lib/mysql
```
⚠️ **Careful:** Docker creates a new volume every `docker-compose up`, taking up unnecessary space

#### With source (Named Volumes - RECOMMENDED)
```yaml
volumes:
  - my_volume:/var/lib/mysql
```
✅ Prevents Docker from creating a new volume on each run

#### With bind mount
```yaml
volumes:
  - ./mysql:/var/lib/mysql
```

#### Access Modes
```yaml
volumes:
  - ./mysql:/var/lib/mysql:rw  # read write (default)
  - ./mysql:/var/lib/mysql:ro  # read only
```

### Enforcing Startup Order

#### depends_on
```yaml
depends_on:
  - database
```

⚠️ **Important:** `depends_on` only guarantees that a dependency has been **started** by Docker Compose, **not that it is available or healthy**

### Named Subsets of Services (Profiles)

Service profiles allow you to organize Docker services into categories for easy startup:

```yaml
services:
  web:
    image: web
    profiles:
      - front_end_services

  api:
    image: api
    profiles:
      - api_services
```

Use with:
```bash
docker-compose --profile front_end_services up
docker-compose --profile front_end_services down
docker-compose --profile front_end_services stop
docker-compose --profile front_end_services restart
```

### Multiple Compose Files

Docker Compose supports multiple configuration files, enabling modular composition

### Environment Variables

An environment variable can be set in three different places:
1. Inline
2. `.env` files
3. Shell commands

**Precedence:** Shell variables always override defaults

---

## 📚 Summary

Docker enables:
- ✅ Complete portability across environments
- ✅ Guaranteed consistency
- ✅ Simplified deployment
- ✅ Isolation and security
- ✅ Efficient scalability
# 🐳 Docker Notes & Cheat Sheet

Este repositório contém anotações completas sobre Docker, incluindo conceitos, comandos e boas práticas.

---

## 📌 Problema: “It works on my machine”
Diferenças entre ambientes:
- Ferramentas
- Configurações
- Hardware

Docker resolve isso com **containers e imagens portáveis**.

---

## 🧱 Conceitos

### 📦 Imagens
- Criadas via Dockerfile
- Compostas por camadas

### 📦 Containers
- Instâncias de imagens
- Isolados e leves

---

## ⚖️ Container vs VM

| VM | Container |
|--|--|
| Pesada | Leve |
| OS completo | Compartilha kernel |
| Mais segura | Mais rápida |

---

## 🧪 Comandos Docker

### ▶️ Rodar container
```bash
docker run -d --name=my-container -p 3000:3000 my-image
```

### 📜 Logs
```bash
docker logs my-container
```

### 🏗️ Build
```bash
docker build -t image-name .
docker build --no-cache .
docker build --rm=true .
docker build --force-rm=true .
```

### 🔍 Buscar imagens
```bash
docker search python
docker search --filter is-official=true python
```

### 📥 Baixar imagem
```bash
docker pull IMAGE_NAME
```

### 📋 Listar imagens
```bash
docker images
docker image ls -a
docker image ls --filter dangling=true
```

### 🏷️ Tag
```bash
docker tag SOURCE TARGET
```

### 🔧 Executar comando
```bash
docker exec -it CONTAINER_ID bash
```

### ⛔ Parar container
```bash
docker stop CONTAINER_ID
docker stop -t 0 CONTAINER_ID
```

---

## 🧠 Dicas

### Listar containers
```bash
docker ps -a
docker ps -aq
```

### Remover containers
```bash
docker rm -f $(docker ps -aq) (on Windows)
docker ps -aq | xargs docker rm (on Linux/Mac)
```

### Limpar sistema
```bash
docker system prune
docker system prune --volumes
```

### Uso de disco
```bash
docker system df
```

---

## 📊 Monitoramento

```bash
docker stats
docker top CONTAINER_ID
docker inspect CONTAINER_ID
```

---

## 💾 Persistência

### Bind Mount
```bash
-v /host/path:/container/path
```

### Volumes
```bash
docker volume create my_volume
docker volume ls
docker volume inspect my_volume
```

---

## 📦 Registry

Publicar imagem:
```bash
docker tag my-app user/my-app:v1
docker push user/my-app:v1
```

---

## ⚙️ Docker Compose

### Subir
```bash
docker-compose up
```

### Parar
```bash
docker-compose down
docker-compose stop
docker-compose restart
```

---

## 🔐 Boas práticas

- Evitar `latest`
- Usar imagens oficiais
- Usar usuário não-root

---

## 🧠 Resumo

Docker permite:
- Portabilidade
- Consistência
- Deploy simplificado
