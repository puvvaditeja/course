# Docker Images

## What is a Docker Image?

A Docker image is a lightweight, standalone, executable package that includes everything needed to run a piece of software: code, runtime, system tools, libraries, and settings. Images are the blueprint from which Docker containers are created.

### Key Characteristics

- **Read-only**: Images are immutable; changes create new layers
- **Layered**: Built from layers stacked on top of each other
- **Reusable**: Same image can create multiple containers
- **Versioned**: Tagged for version control and organization
- **Portable**: Can be shared via registries like Docker Hub

### Image vs Container

Understanding the relationship between images and containers is fundamental:

```
┌─────────────────────┐
│   Docker Image      │  ← Read-only template (blueprint)
│   (nginx:latest)    │
└──────────┬──────────┘
           │ docker run
           ▼
┌─────────────────────┐
│   Container 1       │  ← Running instance
│   (web-server-1)    │
└─────────────────────┘
           │ docker run
           ▼
┌─────────────────────┐
│   Container 2       │  ← Another running instance
│   (web-server-2)    │
└─────────────────────┘
```

**Analogy:**
- **Image** = Class definition (blueprint)
- **Container** = Object instance (running application)

| Aspect | Image | Container |
|--------|-------|-----------|
| **State** | Read-only | Read-write layer on top |
| **Purpose** | Template/Blueprint | Running instance |
| **Lifecycle** | Exists until deleted | Can be started/stopped |
| **Storage** | Stored in registry | Runs on host |
| **Mutability** | Immutable | Mutable (during runtime) |

---

## Docker Image Layers

Docker images are built using a layered architecture. Each layer represents a set of filesystem changes.

### Understanding Layers

```
Image: myapp:1.0
┌─────────────────────────────┐
│  Layer 4: COPY app.js /app  │  ← Your application code
├─────────────────────────────┤
│  Layer 3: RUN npm install   │  ← Application dependencies
├─────────────────────────────┤
│  Layer 2: COPY package.json │  ← Package definition
├─────────────────────────────┤
│  Layer 1: FROM node:18      │  ← Base image (multiple layers)
└─────────────────────────────┘
```

### Layer Benefits

1. **Efficiency**: Layers are cached and reused
2. **Speed**: Only changed layers need to be downloaded/uploaded
3. **Space Saving**: Common layers are shared between images
4. **Version Control**: Each layer represents a specific change

### Viewing Image Layers

```bash
# View image history and layers
docker history nginx:latest

# Output example:
IMAGE          CREATED        CREATED BY                                      SIZE
2b7d6430f78d   2 weeks ago    /bin/sh -c #(nop)  CMD ["nginx" "-g" "daemon…   0B
<missing>      2 weeks ago    /bin/sh -c #(nop)  EXPOSE 80                    0B
<missing>      2 weeks ago    /bin/sh -c #(nop) COPY file:1234 in /etc/ng…    1.2kB
<missing>      2 weeks ago    /bin/sh -c apt-get update && apt-get instal…    50MB
```

### Layer Caching

Docker caches layers to speed up builds:

```dockerfile
# Dockerfile
FROM node:18                    # Layer 1 (cached if unchanged)
COPY package*.json ./           # Layer 2 (cached if files unchanged)
RUN npm install                 # Layer 3 (cached if Layer 2 unchanged)
COPY . .                        # Layer 4 (rebuilt if code changes)
CMD ["npm", "start"]            # Layer 5 (cached if unchanged)
```

**Best Practice**: Order Dockerfile instructions from least to most frequently changing.

---

## Working with Docker Images

### Pulling Images from Registry

```bash
# Pull latest version
docker pull ubuntu

# Pull specific version
docker pull ubuntu:22.04

# Pull from specific registry
docker pull nginx:1.25-alpine

# Pull all tags of an image
docker pull -a nginx
```

### Listing Images

```bash
# List all images
docker images

# Output:
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
nginx         latest    2b7d6430f78d   2 weeks ago    187MB
ubuntu        22.04     3b418d7b466a   3 weeks ago    77.8MB
node          18        74e9f9e5f3e8   1 month ago    993MB

# List with additional details
docker images -a

# Filter images
docker images nginx
docker images "nginx:*"

# Show image digests
docker images --digests

# Format output
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"
```

### Inspecting Images

```bash
# Get detailed information about an image
docker inspect nginx:latest

# Get specific field
docker inspect --format='{{.Config.Env}}' nginx:latest

# View image configuration
docker inspect --format='{{json .Config}}' nginx:latest | jq
```

### Tagging Images

Tags are used to version and identify images:

```bash
# Tag an existing image
docker tag nginx:latest myregistry.com/nginx:v1.0

# Tag with multiple tags
docker tag myapp:latest myapp:1.0.0
docker tag myapp:latest myapp:stable

# Tag pattern
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
```

**Common Tagging Conventions:**
- `latest`: Most recent version (default)
- `1.0.0`, `1.0`, `1`: Semantic versioning
- `stable`, `dev`, `prod`: Environment tags
- `alpine`, `slim`: Variant tags

### Removing Images

```bash
# Remove a specific image
docker rmi nginx:latest

# Remove by image ID
docker rmi 2b7d6430f78d

# Force remove (even if containers exist)
docker rmi -f nginx:latest

# Remove all unused images
docker image prune

# Remove all images
docker rmi $(docker images -q)

# Remove dangling images (untagged)
docker image prune -f
```

### Searching for Images

```bash
# Search Docker Hub
docker search nginx

# Limit results
docker search --limit 5 nginx

# Filter by stars
docker search --filter stars=100 nginx

# Filter official images only
docker search --filter is-official=true nginx
```

---

## Building Docker Images

### Building from Dockerfile

```bash
# Build from Dockerfile in current directory
docker build -t myapp:latest .

# Build from specific Dockerfile
docker build -f Dockerfile.dev -t myapp:dev .

# Build with build arguments
docker build --build-arg VERSION=1.0 -t myapp:1.0 .

# Build without cache
docker build --no-cache -t myapp:latest .

# Build with specific target (multi-stage)
docker build --target production -t myapp:prod .
```

### Build Context

The build context is the set of files at the specified location (usually `.`):

```bash
# Current directory is build context
docker build -t myapp .

# Specific directory as build context
docker build -t myapp /path/to/context

# URL as build context
docker build -t myapp https://github.com/user/repo.git
```

**Tip**: Use `.dockerignore` to exclude files from build context:

```
# .dockerignore
node_modules
.git
.env
*.log
dist
```

### Build Arguments

Pass variables at build time:

```dockerfile
# Dockerfile
FROM node:${NODE_VERSION}
ARG NODE_VERSION=18
ARG APP_ENV=production

ENV ENVIRONMENT=${APP_ENV}
```

```bash
# Build with custom arguments
docker build \
  --build-arg NODE_VERSION=20 \
  --build-arg APP_ENV=development \
  -t myapp:dev .
```

---

## Image Naming and Versioning

### Image Naming Convention

```
[REGISTRY_HOST[:REGISTRY_PORT]/]REPOSITORY[:TAG]
```

Examples:
```
nginx                              # Docker Hub, default tag (latest)
nginx:1.25                         # Docker Hub with version tag
ubuntu:22.04                       # Docker Hub with OS version
docker.io/library/nginx:latest     # Full Docker Hub path
gcr.io/google-samples/hello-app:1.0   # Google Container Registry
myregistry.com:5000/myapp:v1       # Private registry
```

### Semantic Versioning

```bash
# Application version 1.2.3
docker tag myapp:latest myapp:1.2.3    # Specific version
docker tag myapp:latest myapp:1.2      # Minor version
docker tag myapp:latest myapp:1        # Major version
docker tag myapp:latest myapp:latest   # Latest stable
```

---

## Optimizing Docker Images

### 1. Use Smaller Base Images

```dockerfile
# Large image (~1GB)
FROM node:18

# Smaller alpine variant (~170MB)
FROM node:18-alpine

# Minimal distroless image
FROM gcr.io/distroless/nodejs:18
```

### 2. Multi-Stage Builds

```dockerfile
# Build stage
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Production stage
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
CMD ["node", "dist/index.js"]
```

### 3. Minimize Layers

```dockerfile
# Bad: Multiple RUN commands (3 layers)
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get clean

# Good: Single RUN command (1 layer)
RUN apt-get update && \
    apt-get install -y curl && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
```

### 4. Order Instructions Properly

```dockerfile
# Good: Least changing to most changing
FROM node:18-alpine
WORKDIR /app

# Dependencies (changes less frequently)
COPY package*.json ./
RUN npm ci --only=production

# Application code (changes more frequently)
COPY . .

CMD ["node", "index.js"]
```

### 5. Use .dockerignore

```
# .dockerignore
node_modules
npm-debug.log
.git
.gitignore
.env
.DS_Store
dist
coverage
```

### Image Size Comparison

```bash
# Check image sizes
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"

# Example output:
REPOSITORY    TAG            SIZE
myapp         full           1.2GB
myapp         alpine         350MB
myapp         multi-stage    180MB
myapp         optimized      90MB
```

---

## Saving and Loading Images

### Save Image to File

```bash
# Save single image
docker save -o nginx.tar nginx:latest

# Save multiple images
docker save -o images.tar nginx:latest ubuntu:22.04

# Save and compress
docker save nginx:latest | gzip > nginx.tar.gz
```

### Load Image from File

```bash
# Load image
docker load -i nginx.tar

# Load compressed image
gunzip -c nginx.tar.gz | docker load
```

### Export vs Save

```bash
# save: Preserves layers and history (for images)
docker save -o image.tar nginx:latest

# export: Flattens to single layer (for containers)
docker export container_id > container.tar

# import: Load flattened container as image
docker import container.tar myapp:latest
```

---

## Image Best Practices

### Security

1. **Use Official Images**: Start with verified base images
2. **Scan for Vulnerabilities**: Use tools like `docker scan`
3. **Keep Images Updated**: Regularly rebuild with latest base images
4. **Don't Run as Root**: Create and use non-root user
5. **Use Specific Tags**: Avoid `latest` in production

```dockerfile
# Good security practices
FROM node:18-alpine

# Create non-root user
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

# Set working directory
WORKDIR /app

# Copy files with correct ownership
COPY --chown=nodejs:nodejs . .

# Switch to non-root user
USER nodejs

CMD ["node", "index.js"]
```

### Performance

1. **Leverage Build Cache**: Order Dockerfile efficiently
2. **Use .dockerignore**: Reduce build context size
3. **Multi-Stage Builds**: Separate build and runtime dependencies
4. **Minimize Layers**: Combine related commands

### Maintainability

1. **Document Your Images**: Use LABEL instructions
2. **Version Everything**: Use explicit tags
3. **Keep Dockerfiles Simple**: One service per container
4. **Use Environment Variables**: For configuration

```dockerfile
FROM node:18-alpine

# Labels for documentation
LABEL maintainer="dev@example.com"
LABEL version="1.0.0"
LABEL description="Node.js application"

# Environment variables
ENV NODE_ENV=production \
    PORT=3000

WORKDIR /app
COPY . .
RUN npm ci --only=production

EXPOSE 3000
CMD ["node", "index.js"]
```

---

## Common Image Commands Reference

```bash
# Pulling and searching
docker pull IMAGE[:TAG]           # Pull image from registry
docker search TERM                # Search Docker Hub

# Building
docker build -t NAME:TAG .        # Build image from Dockerfile
docker build --no-cache .         # Build without cache

# Listing and inspecting
docker images                     # List all images
docker inspect IMAGE              # Detailed image info
docker history IMAGE              # Show image layers

# Tagging
docker tag SOURCE TARGET          # Create new tag

# Removing
docker rmi IMAGE                  # Remove image
docker image prune                # Remove unused images
docker image prune -a             # Remove all unused images

# Saving and loading
docker save -o file.tar IMAGE     # Save image to tar
docker load -i file.tar           # Load image from tar
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| **Images** | Read-only templates for creating containers |
| **Layers** | Images built from stackable, cacheable layers |
| **Building** | Create images using Dockerfile and build context |
| **Optimization** | Use alpine images, multi-stage builds, proper ordering |
| **Versioning** | Use semantic versioning and specific tags |
| **Best Practices** | Security, performance, and maintainability focus |

## Next Topic

Continue to [Dockerfile](./03-dockerfile.md) to learn how to write efficient Dockerfiles.
