# Docker CLI (Command Line Interface)

## Overview

The Docker CLI is the primary interface for interacting with Docker. It provides a comprehensive set of commands for managing images, containers, networks, volumes, and more.

### CLI Structure

```bash
docker [OPTIONS] COMMAND [ARG...]
```

- **docker**: The base command
- **OPTIONS**: Global options (e.g., `--debug`, `--host`)
- **COMMAND**: Specific Docker operation (e.g., `run`, `build`, `ps`)
- **ARG**: Arguments for the command

### Getting Help

```bash
# Docker version and info
docker --version
docker version
docker info

# General help
docker --help

# Command-specific help
docker run --help
docker build --help

# Management command help
docker container --help
docker image --help
```

---

## Image Commands

### docker pull - Download Images

```bash
# Pull latest version
docker pull nginx

# Pull specific version
docker pull nginx:1.25-alpine

# Pull from different registry
docker pull gcr.io/google-samples/hello-app:1.0

# Pull all tags
docker pull --all-tags nginx

# Pull with platform specification
docker pull --platform linux/amd64 nginx
```

### docker build - Build Images

```bash
# Basic build
docker build -t myapp:latest .

# Build with tag
docker build -t myapp:1.0.0 .

# Build from specific Dockerfile
docker build -f Dockerfile.dev -t myapp:dev .

# Build with build arguments
docker build --build-arg VERSION=1.0 -t myapp .

# Build without cache
docker build --no-cache -t myapp .

# Build with target stage
docker build --target production -t myapp:prod .

# Build with labels
docker build -t myapp --label version=1.0 .

# Build from URL
docker build -t myapp https://github.com/user/repo.git

# Build with progress output
docker build --progress=plain -t myapp .
```

### docker images - List Images

```bash
# List all images
docker images

# List with all information
docker images -a

# List specific repository
docker images nginx

# Show image digests
docker images --digests

# Filter images
docker images --filter "dangling=true"
docker images --filter "reference=nginx:*"

# Format output
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"
docker images --format "{{.Repository}}:{{.Tag}}"

# List image IDs only
docker images -q
```

### docker tag - Tag Images

```bash
# Create tag from image
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]

# Examples
docker tag myapp:latest myapp:1.0.0
docker tag myapp:latest myregistry.com/myapp:latest
docker tag nginx:latest mynginx:custom
```

### docker rmi - Remove Images

```bash
# Remove single image
docker rmi nginx:latest

# Remove by ID
docker rmi a1b2c3d4e5f6

# Force remove
docker rmi -f nginx:latest

# Remove multiple images
docker rmi nginx:latest ubuntu:22.04

# Remove all images
docker rmi $(docker images -q)

# Remove dangling images
docker image prune

# Remove all unused images
docker image prune -a
```

### docker push - Upload Images

```bash
# Push to Docker Hub
docker push username/myapp:latest

# Push to private registry
docker push myregistry.com/myapp:1.0.0

# Push all tags
docker push --all-tags username/myapp
```

### docker save / load - Export/Import Images

```bash
# Save image to tar file
docker save -o myapp.tar myapp:latest
docker save myapp:latest | gzip > myapp.tar.gz

# Load image from tar file
docker load -i myapp.tar
gunzip -c myapp.tar.gz | docker load
```

### docker history - View Image Layers

```bash
# Show image history
docker history nginx:latest

# Show with no truncation
docker history --no-trunc nginx:latest

# Show with human-readable sizes
docker history --human nginx:latest
```

### docker inspect - Detailed Image Info

```bash
# Inspect image
docker inspect nginx:latest

# Get specific field
docker inspect --format='{{.Config.Env}}' nginx:latest
docker inspect --format='{{.Architecture}}' nginx:latest
docker inspect --format='{{json .Config}}' nginx:latest | jq
```

---

## Container Commands

### docker run - Create and Start Container

```bash
# Basic run
docker run nginx

# Run in background (detached)
docker run -d nginx

# Run with name
docker run --name web-server nginx

# Run with port mapping
docker run -p 8080:80 nginx

# Run with environment variables
docker run -e NODE_ENV=production node-app

# Run with volume
docker run -v /host/path:/container/path nginx

# Run interactively
docker run -it ubuntu bash

# Run and remove after exit
docker run --rm alpine ls

# Complete example
docker run -d \
  --name web \
  -p 80:80 \
  -e ENV=prod \
  -v $(pwd):/app \
  --restart unless-stopped \
  --memory="512m" \
  --cpus="1" \
  nginx:alpine
```

### docker ps - List Containers

```bash
# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# List last N containers
docker ps -n 5

# List latest created container
docker ps -l

# Show only IDs
docker ps -q

# Filter containers
docker ps --filter "name=web"
docker ps --filter "status=running"
docker ps --filter "status=exited"

# Format output
docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}"
docker ps --format "{{.Names}}: {{.Status}}"

# Show size
docker ps -s
```

### docker start / stop / restart

```bash
# Start stopped container
docker start container_name

# Start multiple containers
docker start web db cache

# Start and attach to output
docker start -a container_name

# Start interactively
docker start -i container_name

# Stop container (SIGTERM)
docker stop container_name

# Stop with timeout
docker stop -t 30 container_name

# Stop multiple containers
docker stop web db cache

# Restart container
docker restart container_name

# Restart with timeout
docker restart -t 30 container_name
```

### docker kill - Force Stop Container

```bash
# Kill container (SIGKILL)
docker kill container_name

# Kill with specific signal
docker kill -s SIGINT container_name

# Kill multiple containers
docker kill web db cache
```

### docker pause / unpause

```bash
# Pause container
docker pause container_name

# Unpause container
docker unpause container_name
```

### docker rm - Remove Containers

```bash
# Remove stopped container
docker rm container_name

# Force remove running container
docker rm -f container_name

# Remove multiple containers
docker rm web db cache

# Remove and delete volumes
docker rm -v container_name

# Remove all stopped containers
docker container prune

# Remove all containers
docker rm -f $(docker ps -aq)
```

### docker exec - Execute Commands in Container

```bash
# Execute command
docker exec container_name ls -la

# Execute interactively
docker exec -it container_name bash

# Execute as specific user
docker exec -it --user root container_name bash

# Execute with environment variable
docker exec -e VAR=value container_name env

# Execute in specific directory
docker exec -w /app container_name pwd

# Examples
docker exec web cat /etc/nginx/nginx.conf
docker exec -it db mysql -u root -p
docker exec api npm test
```

### docker logs - View Container Logs

```bash
# View logs
docker logs container_name

# Follow logs in real-time
docker logs -f container_name

# Show timestamps
docker logs -t container_name

# Show last N lines
docker logs --tail 100 container_name

# Show logs since timestamp
docker logs --since "2024-01-01T00:00:00" container_name
docker logs --since 1h container_name
docker logs --since 30m container_name

# Show logs until timestamp
docker logs --until "2024-01-01T12:00:00" container_name

# Combine options
docker logs -f --tail 50 --since 10m container_name
```

### docker inspect - Container Details

```bash
# Inspect container
docker inspect container_name

# Multiple containers
docker inspect web db cache

# Get specific field
docker inspect --format='{{.State.Status}}' container_name
docker inspect --format='{{.NetworkSettings.IPAddress}}' container_name
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name
docker inspect --format='{{.State.ExitCode}}' container_name

# JSON output with jq
docker inspect container_name | jq '.[0].State'
```

### docker top - Container Processes

```bash
# Show running processes
docker top container_name

# Show with custom ps options
docker top container_name aux
```

### docker stats - Resource Usage

```bash
# Show stats for all running containers
docker stats

# Stats for specific containers
docker stats web db

# Single snapshot (no streaming)
docker stats --no-stream

# Format output
docker stats --format "table {{.Container}}\t{{.CPUPerc}}\t{{.MemUsage}}"

# Show all containers (including stopped)
docker stats -a
```

### docker attach - Attach to Container

```bash
# Attach to running container
docker attach container_name

# Attach with no stdin
docker attach --no-stdin container_name

# Detach: Ctrl+P, Ctrl+Q (if started with -t)
# Exit: Ctrl+C or Ctrl+D (stops container)
```

### docker cp - Copy Files

```bash
# Copy from container to host
docker cp container_name:/path/in/container /host/path

# Copy from host to container
docker cp /host/path container_name:/path/in/container

# Copy directory
docker cp container_name:/app/logs ./logs

# Follow symlinks
docker cp -L container_name:/path /host/path

# Examples
docker cp web:/var/log/nginx/access.log ./logs/
docker cp config.json web:/etc/app/config.json
docker cp web:/app/dist ./dist
```

### docker wait - Wait for Container

```bash
# Wait for container to stop and get exit code
docker wait container_name

# Wait for multiple containers
docker wait web db cache
```

### docker rename - Rename Container

```bash
# Rename container
docker rename old-name new-name

# Example
docker rename web web-server
```

### docker update - Update Container Config

```bash
# Update memory limit
docker update --memory="1g" container_name

# Update CPU limit
docker update --cpus="2" container_name

# Update restart policy
docker update --restart unless-stopped container_name

# Update multiple settings
docker update --memory="1g" --cpus="2" --restart always container_name

# Update multiple containers
docker update --memory="512m" web db cache
```

---

## Network Commands

### docker network ls - List Networks

```bash
# List all networks
docker network ls

# Filter networks
docker network ls --filter "driver=bridge"
docker network ls --filter "name=my"

# Format output
docker network ls --format "table {{.ID}}\t{{.Name}}\t{{.Driver}}"
```

### docker network create - Create Network

```bash
# Create network
docker network create my-network

# Create with driver
docker network create --driver bridge my-network

# Create with subnet
docker network create --subnet=172.18.0.0/16 my-network

# Create with gateway
docker network create \
  --subnet=172.18.0.0/16 \
  --gateway=172.18.0.1 \
  my-network

# Create with options
docker network create \
  --driver bridge \
  --subnet=172.18.0.0/16 \
  --ip-range=172.18.0.0/24 \
  --gateway=172.18.0.1 \
  my-network
```

### docker network connect / disconnect

```bash
# Connect container to network
docker network connect my-network container_name

# Connect with IP address
docker network connect --ip 172.18.0.10 my-network container_name

# Connect with alias
docker network connect --alias db my-network container_name

# Disconnect from network
docker network disconnect my-network container_name

# Force disconnect
docker network disconnect -f my-network container_name
```

### docker network inspect - Network Details

```bash
# Inspect network
docker network inspect my-network

# Get specific field
docker network inspect --format='{{range .Containers}}{{.Name}}{{end}}' my-network
```

### docker network rm - Remove Network

```bash
# Remove network
docker network rm my-network

# Remove multiple networks
docker network rm network1 network2

# Remove all unused networks
docker network prune
```

---

## Volume Commands

### docker volume ls - List Volumes

```bash
# List all volumes
docker volume ls

# Filter volumes
docker volume ls --filter "dangling=true"

# Format output
docker volume ls --format "table {{.Name}}\t{{.Driver}}"
```

### docker volume create - Create Volume

```bash
# Create volume
docker volume create my-data

# Create with driver
docker volume create --driver local my-data

# Create with options
docker volume create \
  --driver local \
  --opt type=nfs \
  --opt o=addr=192.168.1.1,rw \
  --opt device=:/path/to/dir \
  my-nfs-volume
```

### docker volume inspect - Volume Details

```bash
# Inspect volume
docker volume inspect my-data

# Get mount point
docker volume inspect --format='{{.Mountpoint}}' my-data
```

### docker volume rm - Remove Volume

```bash
# Remove volume
docker volume rm my-data

# Remove multiple volumes
docker volume rm vol1 vol2 vol3

# Force remove
docker volume rm -f my-data

# Remove all unused volumes
docker volume prune
```

---

## System Commands

### docker info - System Information

```bash
# Show system information
docker info

# Get specific info
docker info --format '{{.ServerVersion}}'
docker info --format '{{.DriverStatus}}'
```

### docker version - Version Information

```bash
# Show version
docker version

# Short version
docker --version

# Format output
docker version --format '{{.Server.Version}}'
```

### docker events - Real-time Events

```bash
# Stream events
docker events

# Filter by type
docker events --filter 'event=start'
docker events --filter 'event=stop'

# Filter by container
docker events --filter 'container=web'

# Filter by time
docker events --since '2024-01-01'
docker events --since '1h'

# Multiple filters
docker events --filter 'event=start' --filter 'type=container'
```

### docker system df - Disk Usage

```bash
# Show disk usage
docker system df

# Verbose output
docker system df -v
```

### docker system prune - Clean Up

```bash
# Remove all unused data
docker system prune

# Remove all unused data (including volumes)
docker system prune --volumes

# Remove all (including unused images)
docker system prune -a

# Force without prompt
docker system prune -f

# Prune with filter
docker system prune --filter "until=24h"
```

---

## Advanced CLI Features

### Filtering

```bash
# Filter by name
docker ps --filter "name=web"

# Filter by status
docker ps --filter "status=running"
docker ps --filter "status=exited"

# Filter by label
docker ps --filter "label=environment=production"

# Filter by volume
docker ps --filter "volume=/data"

# Filter by network
docker ps --filter "network=my-network"

# Combine filters
docker ps --filter "name=web" --filter "status=running"
```

### Formatting Output

```bash
# Table format
docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}"

# JSON format
docker inspect --format='{{json .}}' container_name

# Custom format
docker ps --format "{{.Names}}: {{.Status}}"

# Multiple fields
docker images --format "{{.Repository}}:{{.Tag}} - {{.Size}}"

# Using templates
docker ps --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}'
```

### Using Labels

```bash
# Create container with labels
docker run -d \
  --label environment=production \
  --label version=1.0 \
  --label maintainer=dev@example.com \
  nginx

# Filter by label
docker ps --filter "label=environment=production"

# Inspect labels
docker inspect --format='{{json .Config.Labels}}' container_name
```

### Shell Aliases for Docker

```bash
# Add to ~/.bashrc or ~/.zshrc

# Container aliases
alias dps='docker ps'
alias dpsa='docker ps -a'
alias dlog='docker logs -f'
alias dexec='docker exec -it'

# Image aliases
alias di='docker images'
alias drmi='docker rmi'
alias dbuild='docker build -t'

# Cleanup aliases
alias dprune='docker system prune -f'
alias dprunea='docker system prune -af'

# Network aliases
alias dnls='docker network ls'
alias dninspect='docker network inspect'

# Volume aliases
alias dvls='docker volume ls'
alias dvprune='docker volume prune -f'
```

---

## Common CLI Workflows

### Development Workflow

```bash
# 1. Build image
docker build -t myapp:dev .

# 2. Run container
docker run -d \
  --name myapp-dev \
  -p 3000:3000 \
  -v $(pwd):/app \
  myapp:dev

# 3. View logs
docker logs -f myapp-dev

# 4. Execute commands
docker exec -it myapp-dev npm test

# 5. Stop and remove
docker stop myapp-dev
docker rm myapp-dev
```

### Production Deployment

```bash
# 1. Pull latest image
docker pull myregistry.com/myapp:latest

# 2. Stop old container
docker stop myapp-prod

# 3. Remove old container
docker rm myapp-prod

# 4. Run new container
docker run -d \
  --name myapp-prod \
  --restart unless-stopped \
  -p 80:80 \
  --memory="1g" \
  --cpus="2" \
  myregistry.com/myapp:latest

# 5. Verify
docker ps
docker logs myapp-prod
```

### Debugging Workflow

```bash
# 1. Check if container is running
docker ps

# 2. View logs
docker logs container_name

# 3. Inspect container
docker inspect container_name

# 4. Check resource usage
docker stats container_name

# 5. Execute shell in container
docker exec -it container_name bash

# 6. Check processes
docker top container_name

# 7. View events
docker events --filter container=container_name
```

---

## Docker CLI Cheat Sheet

### Image Management
```bash
docker pull IMAGE           # Download image
docker build -t NAME .      # Build image
docker images              # List images
docker tag SRC DEST        # Tag image
docker push IMAGE          # Upload image
docker rmi IMAGE           # Remove image
docker history IMAGE       # View layers
```

### Container Management
```bash
docker run IMAGE           # Create and start
docker ps                  # List running
docker ps -a               # List all
docker start CONTAINER     # Start container
docker stop CONTAINER      # Stop container
docker restart CONTAINER   # Restart container
docker rm CONTAINER        # Remove container
docker logs CONTAINER      # View logs
docker exec CONTAINER CMD  # Execute command
docker inspect CONTAINER   # Detailed info
```

### Network Management
```bash
docker network ls          # List networks
docker network create NET  # Create network
docker network connect     # Connect container
docker network disconnect  # Disconnect container
docker network rm NET      # Remove network
```

### Volume Management
```bash
docker volume ls           # List volumes
docker volume create VOL   # Create volume
docker volume inspect VOL  # Inspect volume
docker volume rm VOL       # Remove volume
```

### System Management
```bash
docker info                # System information
docker version             # Docker version
docker system df           # Disk usage
docker system prune        # Clean up
docker events              # Stream events
```

---

## Summary

| Category | Key Commands | Purpose |
|----------|--------------|---------|
| **Images** | pull, build, push, rmi | Manage Docker images |
| **Containers** | run, ps, start, stop, rm | Manage containers |
| **Execution** | exec, logs, attach | Interact with containers |
| **Networks** | network ls, create, connect | Manage networking |
| **Volumes** | volume ls, create, rm | Manage persistent storage |
| **System** | info, prune, events | System management |

## Next Topic

Continue to [Docker Registry](./06-docker-registry.md) to learn about managing images with Docker Hub and private registries.
