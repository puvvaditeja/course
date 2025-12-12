# Docker Containers

## What is a Docker Container?

A Docker container is a lightweight, standalone, executable package that includes everything needed to run an application: code, runtime, system tools, libraries, and settings. Containers are running instances of Docker images.

### Key Characteristics

- **Isolated**: Own filesystem, network, and process space
- **Ephemeral**: Designed to be temporary and disposable
- **Portable**: Run consistently across different environments
- **Lightweight**: Share host OS kernel, minimal overhead
- **Fast**: Start in seconds or milliseconds
- **Immutable**: Built from immutable images

### Container Lifecycle

```
┌──────────┐
│  Created │ ← docker create
└────┬─────┘
     │ docker start
┌────▼─────┐
│  Running │ ← docker run (create + start)
└────┬─────┘
     │ docker stop / docker kill
┌────▼─────┐
│  Stopped │
└────┬─────┘
     │ docker start (restart)
     │ docker rm (remove)
┌────▼─────┐
│  Removed │
└──────────┘
```

---

## Creating Containers

### Basic Container Creation

```bash
# Run a container (create and start)
docker run nginx

# Run container in detached mode (background)
docker run -d nginx

# Run with name
docker run --name web-server nginx

# Run with port mapping
docker run -p 8080:80 nginx

# Run with environment variables
docker run -e NODE_ENV=production -e PORT=3000 node-app

# Run with volume mount
docker run -v /host/path:/container/path nginx

# Create but don't start
docker create --name my-app nginx
```

### Interactive Containers

```bash
# Run with interactive terminal
docker run -it ubuntu bash

# Run command in existing container
docker exec -it container_name bash

# Run as specific user
docker run -it --user 1001 ubuntu bash

# Run with pseudo-TTY
docker run -t ubuntu date
```

### Container Run Options

```bash
# Complete example with common options
docker run \
  --name my-app \              # Container name
  -d \                         # Detached mode
  -p 8080:80 \                # Port mapping (host:container)
  -e ENV=production \          # Environment variable
  -v $(pwd)/data:/app/data \  # Volume mount
  --restart unless-stopped \   # Restart policy
  --memory="512m" \           # Memory limit
  --cpus="1.5" \              # CPU limit
  --network my-network \       # Custom network
  nginx:latest                 # Image to use
```

---

## Managing Containers

### Listing Containers

```bash
# List running containers
docker ps

# Output:
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                  NAMES
a1b2c3d4e5f6   nginx     "/docker-entrypoint.…"   2 minutes ago   Up 2 minutes   0.0.0.0:8080->80/tcp   web-server

# List all containers (including stopped)
docker ps -a

# List with specific format
docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}"

# List last N containers
docker ps -n 5

# List only container IDs
docker ps -q

# Filter containers
docker ps --filter "name=web"
docker ps --filter "status=running"
docker ps --filter "ancestor=nginx"
```

### Starting and Stopping Containers

```bash
# Start stopped container
docker start container_name

# Start and attach to container
docker start -a container_name

# Start multiple containers
docker start container1 container2

# Stop running container (graceful, sends SIGTERM)
docker stop container_name

# Stop with timeout (default 10 seconds)
docker stop -t 30 container_name

# Kill container immediately (sends SIGKILL)
docker kill container_name

# Restart container
docker restart container_name

# Pause container (freeze processes)
docker pause container_name

# Unpause container
docker unpause container_name
```

### Removing Containers

```bash
# Remove stopped container
docker rm container_name

# Force remove running container
docker rm -f container_name

# Remove multiple containers
docker rm container1 container2 container3

# Remove all stopped containers
docker container prune

# Remove container after it exits
docker run --rm nginx

# Remove all containers (careful!)
docker rm -f $(docker ps -aq)
```

---

## Container Information and Inspection

### Viewing Container Details

```bash
# Detailed container information
docker inspect container_name

# Get specific information (JSON path)
docker inspect --format='{{.State.Status}}' container_name
docker inspect --format='{{.NetworkSettings.IPAddress}}' container_name
docker inspect --format='{{json .Config}}' container_name | jq

# View container logs
docker logs container_name

# Follow logs in real-time
docker logs -f container_name

# Show timestamps
docker logs -t container_name

# Show last N lines
docker logs --tail 100 container_name

# Show logs since timestamp
docker logs --since 2024-01-01T00:00:00 container_name
docker logs --since 1h container_name

# View container processes
docker top container_name

# View resource usage statistics
docker stats container_name

# View stats for all containers
docker stats

# View container changes (filesystem)
docker diff container_name
```

### Monitoring Containers

```bash
# Real-time stats
docker stats

# Output:
CONTAINER ID   NAME          CPU %     MEM USAGE / LIMIT   MEM %     NET I/O       BLOCK I/O
a1b2c3d4e5f6   web-server    0.01%     10MiB / 512MiB      1.95%     1.2kB / 0B    0B / 0B

# Stats for specific containers
docker stats web-server db-server

# Single snapshot (no streaming)
docker stats --no-stream

# Events monitoring
docker events

# Filter events
docker events --filter 'event=start'
docker events --filter 'container=web-server'
```

---

## Interacting with Running Containers

### Executing Commands

```bash
# Execute command in running container
docker exec container_name ls -la

# Execute interactive command
docker exec -it container_name bash

# Execute as specific user
docker exec -it --user root container_name bash

# Execute with environment variable
docker exec -e VAR=value container_name env

# Execute with working directory
docker exec -w /app container_name pwd
```

### Copying Files

```bash
# Copy from container to host
docker cp container_name:/path/in/container /host/path

# Copy from host to container
docker cp /host/path container_name:/path/in/container

# Copy directory
docker cp container_name:/app/logs ./logs

# Examples
docker cp web-server:/var/log/nginx/access.log ./access.log
docker cp config.json web-server:/app/config.json
```

### Attaching to Containers

```bash
# Attach to running container
docker attach container_name

# Attach with no stdin
docker attach --no-stdin container_name

# Detach from container: Ctrl+P, Ctrl+Q
```

---

## Container Networking

### Port Mapping

```bash
# Map single port
docker run -p 8080:80 nginx

# Map to specific host interface
docker run -p 127.0.0.1:8080:80 nginx

# Map multiple ports
docker run -p 8080:80 -p 8443:443 nginx

# Map all exposed ports to random host ports
docker run -P nginx

# View port mappings
docker port container_name
```

### Network Management

```bash
# List networks
docker network ls

# Create network
docker network create my-network

# Create network with subnet
docker network create --subnet=172.18.0.0/16 my-network

# Run container on specific network
docker run --network my-network nginx

# Connect container to network
docker network connect my-network container_name

# Disconnect from network
docker network disconnect my-network container_name

# Inspect network
docker network inspect my-network

# Remove network
docker network rm my-network
```

### Network Communication

```bash
# Containers on same network can communicate by name
docker network create app-network

docker run -d --name database \
  --network app-network \
  postgres

docker run -d --name api \
  --network app-network \
  -e DB_HOST=database \
  my-api

# api can reach database at hostname "database"
```

---

## Container Storage

### Volume Mounts

```bash
# Create named volume
docker volume create my-data

# Use named volume
docker run -v my-data:/app/data nginx

# List volumes
docker volume ls

# Inspect volume
docker volume inspect my-data

# Remove volume
docker volume rm my-data

# Remove all unused volumes
docker volume prune
```

### Bind Mounts

```bash
# Mount host directory (absolute path)
docker run -v /host/path:/container/path nginx

# Mount current directory
docker run -v $(pwd):/app node

# Read-only mount
docker run -v $(pwd):/app:ro nginx

# Mount specific file
docker run -v $(pwd)/config.json:/app/config.json nginx
```

### tmpfs Mounts (Temporary)

```bash
# Mount tmpfs (stored in memory)
docker run --tmpfs /app/cache nginx

# With options
docker run --tmpfs /app/cache:rw,size=64m nginx
```

### Volume Examples

```bash
# Database with persistent volume
docker run -d \
  --name postgres \
  -v pgdata:/var/lib/postgresql/data \
  -e POSTGRES_PASSWORD=secret \
  postgres

# Development with code sync
docker run -d \
  --name dev-server \
  -v $(pwd):/app \
  -p 3000:3000 \
  node:18

# Configuration file mount
docker run -d \
  --name nginx \
  -v $(pwd)/nginx.conf:/etc/nginx/nginx.conf:ro \
  -p 80:80 \
  nginx
```

---

## Container Lifecycle Management

### Restart Policies

```bash
# No restart (default)
docker run --restart no nginx

# Always restart
docker run --restart always nginx

# Restart on failure
docker run --restart on-failure nginx

# Restart on failure with max attempts
docker run --restart on-failure:5 nginx

# Restart unless stopped manually
docker run --restart unless-stopped nginx

# Update restart policy
docker update --restart unless-stopped container_name
```

### Health Checks

```bash
# View health status
docker inspect --format='{{.State.Health.Status}}' container_name

# Run container with health check
docker run -d \
  --name web \
  --health-cmd="curl -f http://localhost/ || exit 1" \
  --health-interval=30s \
  --health-timeout=3s \
  --health-retries=3 \
  nginx

# Health status in ps
docker ps
# STATUS column shows: Up 2 minutes (healthy)
```

---

## Resource Management

### Memory Limits

```bash
# Limit memory
docker run --memory="512m" nginx

# Memory with swap limit
docker run --memory="512m" --memory-swap="1g" nginx

# Memory reservation (soft limit)
docker run --memory-reservation="256m" nginx

# Out of memory behavior
docker run --oom-kill-disable nginx
```

### CPU Limits

```bash
# Limit CPU shares (relative weight)
docker run --cpu-shares=512 nginx

# Limit CPU cores
docker run --cpus="1.5" nginx

# Pin to specific CPUs
docker run --cpuset-cpus="0,1" nginx

# CPU quota
docker run --cpu-quota=50000 nginx
```

### Combined Resource Limits

```bash
docker run -d \
  --name resource-limited \
  --memory="512m" \
  --memory-swap="1g" \
  --cpus="1.5" \
  --pids-limit=100 \
  nginx
```

### Updating Container Resources

```bash
# Update memory limit
docker update --memory="1g" container_name

# Update CPU limit
docker update --cpus="2" container_name

# Update restart policy
docker update --restart unless-stopped container_name

# Update multiple containers
docker update --memory="1g" --cpus="2" container1 container2
```

---

## Container Best Practices

### 1. Use Specific Tags

```bash
# Bad: unpredictable
docker run nginx:latest

# Good: specific version
docker run nginx:1.25-alpine
```

### 2. Remove Containers After Use

```bash
# Automatically remove after exit
docker run --rm nginx

# For testing/one-off commands
docker run --rm -it ubuntu bash
```

### 3. Name Your Containers

```bash
# Good: easy to identify and manage
docker run --name web-server nginx

# Easier to reference
docker logs web-server
docker stop web-server
```

### 4. Use Volume for Persistent Data

```bash
# Good: data persists
docker run -v data:/var/lib/postgresql/data postgres

# Bad: data lost when container removed
docker run postgres
```

### 5. Don't Run as Root

```bash
# Specify user
docker run --user 1001 nginx

# Or in Dockerfile
USER nodejs
```

### 6. Set Resource Limits

```bash
docker run \
  --memory="512m" \
  --cpus="1" \
  nginx
```

### 7. Use Health Checks

```bash
docker run \
  --health-cmd="curl -f http://localhost/ || exit 1" \
  --health-interval=30s \
  nginx
```

### 8. Implement Proper Logging

```bash
# View logs
docker logs -f container_name

# Configure log driver
docker run --log-driver json-file --log-opt max-size=10m nginx
```

---

## Common Container Patterns

### 1. Database Container

```bash
docker run -d \
  --name postgres \
  --restart unless-stopped \
  -e POSTGRES_PASSWORD=secret \
  -e POSTGRES_USER=myuser \
  -e POSTGRES_DB=mydb \
  -v pgdata:/var/lib/postgresql/data \
  -p 5432:5432 \
  postgres:15-alpine
```

### 2. Web Application

```bash
docker run -d \
  --name web-app \
  --restart unless-stopped \
  -p 80:3000 \
  -e NODE_ENV=production \
  -v $(pwd)/logs:/app/logs \
  --memory="512m" \
  --cpus="1" \
  myapp:latest
```

### 3. Development Container

```bash
docker run -it --rm \
  --name dev \
  -v $(pwd):/app \
  -w /app \
  -p 3000:3000 \
  node:18-alpine \
  sh
```

### 4. Temporary Test Container

```bash
docker run --rm -it \
  --name test \
  -v $(pwd):/app \
  -w /app \
  node:18-alpine \
  npm test
```

---

## Troubleshooting Containers

### Common Issues

```bash
# Container won't start
docker logs container_name
docker inspect container_name

# Port already in use
docker ps --filter "publish=8080"
docker run -p 8081:80 nginx  # Use different port

# Permission denied
docker run --user $(id -u):$(id -g) nginx
docker exec -it --user root container_name chown -R user:group /path

# Out of memory
docker stats container_name
docker update --memory="1g" container_name

# Check container exit code
docker inspect --format='{{.State.ExitCode}}' container_name

# View container events
docker events --filter container=container_name
```

### Debugging

```bash
# Enter container for debugging
docker exec -it container_name bash

# Check processes
docker top container_name

# Check resource usage
docker stats container_name

# View filesystem changes
docker diff container_name

# Export container filesystem
docker export container_name > container.tar
```

---

## Container Commands Reference

```bash
# Lifecycle
docker create IMAGE              # Create container
docker run IMAGE                 # Create and start
docker start CONTAINER           # Start stopped container
docker stop CONTAINER            # Stop gracefully
docker restart CONTAINER         # Restart container
docker kill CONTAINER            # Force stop
docker rm CONTAINER              # Remove container
docker pause CONTAINER           # Pause container
docker unpause CONTAINER         # Unpause container

# Information
docker ps                        # List running containers
docker ps -a                     # List all containers
docker logs CONTAINER            # View logs
docker inspect CONTAINER         # Detailed info
docker top CONTAINER             # Running processes
docker stats CONTAINER           # Resource usage
docker port CONTAINER            # Port mappings

# Interaction
docker exec CONTAINER CMD        # Execute command
docker attach CONTAINER          # Attach to container
docker cp SRC DEST              # Copy files

# Resource Management
docker update CONTAINER          # Update configuration
docker rename OLD NEW            # Rename container
docker wait CONTAINER            # Wait for container to stop

# Cleanup
docker container prune           # Remove stopped containers
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| **Containers** | Running instances of images, isolated and ephemeral |
| **Creating** | Use `docker run` with appropriate options |
| **Managing** | Start, stop, restart, remove containers |
| **Networking** | Port mapping, custom networks, container communication |
| **Storage** | Volumes for persistence, bind mounts for development |
| **Resources** | Set memory and CPU limits for stability |
| **Best Practices** | Name containers, use volumes, set limits, health checks |

## Next Topic

Continue to [Docker CLI](./05-docker-cli.md) to master Docker command-line operations.
