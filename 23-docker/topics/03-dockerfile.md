# Dockerfile

## What is a Dockerfile?

A Dockerfile is a text file containing a set of instructions that Docker uses to build an image automatically. It defines the base image, application code, dependencies, configuration, and commands needed to run your application.

### Key Characteristics

- **Declarative**: Describes the desired state of the image
- **Versioned**: Can be stored in version control (Git)
- **Reproducible**: Same Dockerfile produces same image
- **Layered**: Each instruction creates a new layer
- **Automated**: Enables automated image building

### Basic Dockerfile Structure

```dockerfile
# Comment
INSTRUCTION arguments
```

Example:
```dockerfile
# Use official Node.js runtime
FROM node:18-alpine

# Set working directory
WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy application code
COPY . .

# Expose port
EXPOSE 3000

# Define startup command
CMD ["node", "index.js"]
```

---

## Dockerfile Instructions

### FROM - Base Image

Specifies the parent image to build upon. Must be the first instruction (except for ARG).

```dockerfile
# Use official image
FROM node:18

# Use specific version
FROM node:18.17.0

# Use Alpine variant (smaller)
FROM node:18-alpine

# Use specific registry
FROM docker.io/library/node:18

# Multi-stage build with name
FROM node:18 AS builder
```

**Best Practices:**
- Use official images from trusted sources
- Specify exact versions for reproducibility
- Consider Alpine variants for smaller images
- Use minimal base images when possible

### WORKDIR - Set Working Directory

Sets the working directory for subsequent instructions.

```dockerfile
# Set working directory
WORKDIR /app

# Creates directory if it doesn't exist
WORKDIR /app/src/components

# Use environment variables
WORKDIR ${APP_HOME}
```

**Benefits:**
- Avoids repeating paths in commands
- Clearer than using `cd` in RUN commands
- Creates directory automatically

### COPY - Copy Files

Copies files from build context to image.

```dockerfile
# Copy single file
COPY package.json /app/

# Copy multiple files
COPY package.json package-lock.json ./

# Copy directory
COPY src/ /app/src/

# Copy everything (respect .dockerignore)
COPY . .

# Copy with ownership
COPY --chown=node:node . .

# Copy from another stage (multi-stage builds)
COPY --from=builder /app/dist ./dist
```

**Best Practices:**
- Use specific paths rather than wildcards when possible
- Leverage .dockerignore to exclude unnecessary files
- Copy package files before application code for better caching

### ADD - Advanced Copy

Similar to COPY but with additional features.

```dockerfile
# Copy and auto-extract tar archives
ADD archive.tar.gz /app/

# Download from URL
ADD https://example.com/file.txt /app/

# Regular file copy (use COPY instead)
ADD package.json /app/
```

**When to Use:**
- **ADD**: Only when you need auto-extraction or URL download
- **COPY**: Preferred for regular file copying (more transparent)

### RUN - Execute Commands

Executes commands during image build.

```dockerfile
# Shell form (invokes /bin/sh -c)
RUN npm install
RUN apt-get update && apt-get install -y curl

# Exec form (no shell processing)
RUN ["npm", "install"]

# Multi-line commands
RUN apt-get update && \
    apt-get install -y \
        curl \
        vim \
        git && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Multiple commands in one layer
RUN npm install && \
    npm run build && \
    npm prune --production
```

**Best Practices:**
- Combine related commands to minimize layers
- Clean up in the same RUN instruction
- Use `&&` to chain commands
- Use `\` for multi-line readability

### CMD - Default Command

Specifies default command to run when container starts.

```dockerfile
# Exec form (preferred)
CMD ["node", "index.js"]

# Shell form
CMD node index.js

# Provide default parameters to ENTRYPOINT
CMD ["--port", "3000"]
```

**Important:**
- Only one CMD instruction per Dockerfile (last one wins)
- Can be overridden by `docker run` command
- Preferred form: `CMD ["executable", "param1", "param2"]`

### ENTRYPOINT - Configure Container Executable

Configures container to run as an executable.

```dockerfile
# Exec form (preferred)
ENTRYPOINT ["node"]

# With CMD as default parameters
ENTRYPOINT ["node"]
CMD ["index.js"]

# Shell form (not recommended)
ENTRYPOINT node index.js
```

**ENTRYPOINT vs CMD:**

```dockerfile
# Using only CMD (can be overridden completely)
CMD ["node", "index.js"]
# docker run myimage              → node index.js
# docker run myimage node app.js  → node app.js

# Using ENTRYPOINT (executable is fixed)
ENTRYPOINT ["node"]
CMD ["index.js"]
# docker run myimage              → node index.js
# docker run myimage app.js       → node app.js
```

### ENV - Environment Variables

Sets environment variables.

```dockerfile
# Single variable
ENV NODE_ENV=production

# Multiple variables
ENV NODE_ENV=production \
    PORT=3000 \
    LOG_LEVEL=info

# Using in subsequent instructions
ENV APP_HOME=/app
WORKDIR ${APP_HOME}
```

**Best Practices:**
- Use for configuration that might change
- Group related variables
- Sensitive data should use secrets, not ENV

### ARG - Build Arguments

Defines build-time variables.

```dockerfile
# Define argument with default
ARG NODE_VERSION=18
ARG APP_ENV=production

# Use in FROM
FROM node:${NODE_VERSION}

# Use in RUN
RUN echo "Building for ${APP_ENV}"

# Build with custom values
# docker build --build-arg NODE_VERSION=20 --build-arg APP_ENV=dev -t myapp .
```

**ARG vs ENV:**
- **ARG**: Available only during build
- **ENV**: Available during build and runtime

```dockerfile
ARG BUILD_VERSION=1.0
ENV APP_VERSION=${BUILD_VERSION}
```

### EXPOSE - Document Ports

Documents which ports the container listens on.

```dockerfile
# Single port
EXPOSE 3000

# Multiple ports
EXPOSE 3000 8080

# With protocol
EXPOSE 3000/tcp
EXPOSE 8080/udp

# Using variable
ENV PORT=3000
EXPOSE ${PORT}
```

**Note:** EXPOSE is documentation only. Use `-p` flag with `docker run` to actually publish ports.

### VOLUME - Create Mount Point

Creates a mount point for external volumes.

```dockerfile
# Single volume
VOLUME /data

# Multiple volumes
VOLUME /var/log /var/db

# Array format
VOLUME ["/data"]
```

**Use Cases:**
- Database storage
- Log files
- Configuration files
- Any data that should persist

### USER - Set User

Sets the user for subsequent instructions.

```dockerfile
# Create user and group
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

# Switch to user
USER nodejs

# Switch to user:group
USER nodejs:nodejs

# Use numeric IDs
USER 1001:1001
```

**Security Best Practice:**
- Always run containers as non-root user
- Create dedicated user for application

### LABEL - Add Metadata

Adds metadata to image.

```dockerfile
# Single label
LABEL version="1.0.0"

# Multiple labels
LABEL maintainer="dev@example.com" \
      version="1.0.0" \
      description="Node.js application"

# Standard labels
LABEL org.opencontainers.image.authors="dev@example.com"
LABEL org.opencontainers.image.version="1.0.0"
LABEL org.opencontainers.image.source="https://github.com/user/repo"
```

### HEALTHCHECK - Container Health

Tells Docker how to test container health.

```dockerfile
# Basic healthcheck
HEALTHCHECK CMD curl -f http://localhost:3000/health || exit 1

# With options
HEALTHCHECK --interval=30s \
            --timeout=3s \
            --start-period=40s \
            --retries=3 \
            CMD curl -f http://localhost:3000/health || exit 1

# Disable healthcheck from base image
HEALTHCHECK NONE
```

### SHELL - Default Shell

Changes the default shell for RUN commands.

```dockerfile
# Use bash instead of sh
SHELL ["/bin/bash", "-c"]

# PowerShell on Windows
SHELL ["powershell", "-command"]
```

---

## Multi-Stage Builds

Multi-stage builds allow you to use multiple FROM statements and copy artifacts between stages, resulting in smaller final images.

### Basic Multi-Stage Build

```dockerfile
# Stage 1: Build
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Production
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY package*.json ./
USER node
CMD ["node", "dist/index.js"]
```

**Benefits:**
- Smaller production images
- Separate build and runtime dependencies
- Better security (no build tools in production)
- Cleaner image structure

### Java Application Example

```dockerfile
# Build stage
FROM maven:3.8-openjdk-17 AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn package -DskipTests

# Runtime stage
FROM openjdk:17-slim
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### React Application Example

```dockerfile
# Build stage
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Production stage with nginx
FROM nginx:alpine
COPY --from=builder /app/build /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Using Multiple Stages

```dockerfile
# Dependencies stage
FROM node:18-alpine AS dependencies
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

# Build stage
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Test stage
FROM builder AS tester
RUN npm run test

# Production stage
FROM node:18-alpine AS production
WORKDIR /app
COPY --from=dependencies /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY package*.json ./
USER node
CMD ["node", "dist/index.js"]

# Build specific stage:
# docker build --target production -t myapp:prod .
# docker build --target tester -t myapp:test .
```

### Named Stages Benefits

```dockerfile
# Name your stages for clarity
FROM node:18 AS development
# ... development setup

FROM node:18-alpine AS production
# ... production setup

# Build specific stage
# docker build --target development -t myapp:dev .
# docker build --target production -t myapp:prod .
```

---

## Dockerfile Best Practices

### 1. Order Instructions Efficiently

```dockerfile
# Good: Least frequently changed to most frequently changed
FROM node:18-alpine

# Install system dependencies (rarely changes)
RUN apk add --no-cache python3 make g++

# Set working directory
WORKDIR /app

# Copy dependency files (changes occasionally)
COPY package*.json ./

# Install dependencies (changes when package.json changes)
RUN npm ci --only=production

# Copy source code (changes frequently)
COPY . .

# Start application
CMD ["node", "index.js"]
```

### 2. Minimize Layers

```dockerfile
# Bad: Multiple layers
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get install -y vim
RUN apt-get clean

# Good: Single layer
RUN apt-get update && \
    apt-get install -y \
        curl \
        vim && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
```

### 3. Use .dockerignore

```
# .dockerignore
node_modules
npm-debug.log
.git
.gitignore
.env
.env.*
.DS_Store
dist
coverage
*.md
Dockerfile
.dockerignore
```

### 4. Don't Run as Root

```dockerfile
FROM node:18-alpine

# Create app directory
WORKDIR /app

# Create non-root user
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

# Copy files with correct ownership
COPY --chown=nodejs:nodejs . .

# Switch to non-root user
USER nodejs

CMD ["node", "index.js"]
```

### 5. Use Specific Tags

```dockerfile
# Bad: Unpredictable
FROM node:latest

# Good: Specific version
FROM node:18.17.0-alpine3.18

# Acceptable: Major version
FROM node:18-alpine
```

### 6. Clean Up in Same Layer

```dockerfile
# Bad: Cleanup in separate layer (doesn't reduce size)
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get clean

# Good: Cleanup in same layer
RUN apt-get update && \
    apt-get install -y curl && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
```

### 7. Leverage Build Cache

```dockerfile
# Copy package files first
COPY package*.json ./

# Install dependencies (cached unless package files change)
RUN npm ci

# Copy source code (doesn't invalidate dependency cache)
COPY . .
```

### 8. Use COPY Instead of ADD

```dockerfile
# Good: Clear intent
COPY package.json .

# Bad: Unnecessary use of ADD
ADD package.json .

# OK: Need auto-extraction
ADD archive.tar.gz /app/
```

### 9. Document Your Image

```dockerfile
FROM node:18-alpine

# Metadata
LABEL maintainer="dev@example.com"
LABEL version="1.0.0"
LABEL description="Node.js REST API"

# Document port
EXPOSE 3000

# Document volume
VOLUME /app/data

# Add healthcheck
HEALTHCHECK --interval=30s CMD wget --quiet --tries=1 --spider http://localhost:3000/health || exit 1
```

### 10. Handle Signals Properly

```dockerfile
# Use exec form to receive signals
CMD ["node", "index.js"]

# Not shell form (won't receive SIGTERM)
# CMD node index.js

# Or use tini as init
RUN apk add --no-cache tini
ENTRYPOINT ["/sbin/tini", "--"]
CMD ["node", "index.js"]
```

---

## Complete Examples

### Node.js Application

```dockerfile
# Build stage
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build && \
    npm prune --production

# Production stage
FROM node:18-alpine

# Add tini for proper signal handling
RUN apk add --no-cache tini

# Create app user
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

# Set working directory
WORKDIR /app

# Copy built assets and dependencies
COPY --from=builder --chown=nodejs:nodejs /app/dist ./dist
COPY --from=builder --chown=nodejs:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=nodejs:nodejs /app/package*.json ./

# Environment variables
ENV NODE_ENV=production \
    PORT=3000

# Switch to non-root user
USER nodejs

# Expose port
EXPOSE 3000

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=40s \
    CMD node -e "require('http').get('http://localhost:3000/health', (r) => r.statusCode === 200 ? process.exit(0) : process.exit(1))"

# Use tini as entrypoint
ENTRYPOINT ["/sbin/tini", "--"]

# Start application
CMD ["node", "dist/index.js"]
```

### Python Application

```dockerfile
# Build stage
FROM python:3.11-slim AS builder

WORKDIR /app

# Install build dependencies
RUN apt-get update && \
    apt-get install -y --no-install-recommends gcc && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Copy requirements and install
COPY requirements.txt .
RUN pip wheel --no-cache-dir --no-deps --wheel-dir /app/wheels -r requirements.txt

# Production stage
FROM python:3.11-slim

# Create non-root user
RUN useradd -m -u 1001 appuser

WORKDIR /app

# Copy wheels and install
COPY --from=builder /app/wheels /wheels
COPY --from=builder /app/requirements.txt .
RUN pip install --no-cache /wheels/*

# Copy application
COPY --chown=appuser:appuser . .

# Switch to non-root user
USER appuser

# Expose port
EXPOSE 8000

# Start application
CMD ["python", "app.py"]
```

---

## Common Patterns

### Environment-Based Configuration

```dockerfile
FROM node:18-alpine

ARG NODE_ENV=production
ENV NODE_ENV=${NODE_ENV}

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .

CMD ["node", "index.js"]

# Build for different environments:
# docker build --build-arg NODE_ENV=development -t myapp:dev .
# docker build --build-arg NODE_ENV=production -t myapp:prod .
```

### Conditional Instructions

```dockerfile
FROM ubuntu:22.04

ARG INSTALL_DEV_TOOLS=false

RUN apt-get update && \
    apt-get install -y curl && \
    if [ "$INSTALL_DEV_TOOLS" = "true" ]; then \
        apt-get install -y vim git; \
    fi && \
    apt-get clean

# docker build --build-arg INSTALL_DEV_TOOLS=true -t myapp:dev .
```

---

## Summary

| Instruction | Purpose | Example |
|------------|---------|---------|
| `FROM` | Base image | `FROM node:18-alpine` |
| `WORKDIR` | Set working directory | `WORKDIR /app` |
| `COPY` | Copy files | `COPY . .` |
| `ADD` | Copy with extras | `ADD archive.tar.gz /app/` |
| `RUN` | Execute commands | `RUN npm install` |
| `CMD` | Default command | `CMD ["node", "index.js"]` |
| `ENTRYPOINT` | Container executable | `ENTRYPOINT ["node"]` |
| `ENV` | Environment variable | `ENV PORT=3000` |
| `ARG` | Build argument | `ARG VERSION=1.0` |
| `EXPOSE` | Document port | `EXPOSE 3000` |
| `VOLUME` | Create mount point | `VOLUME /data` |
| `USER` | Set user | `USER nodejs` |
| `HEALTHCHECK` | Health check | `HEALTHCHECK CMD curl ...` |

## Next Topic

Continue to [Docker Containers](./04-docker-containers.md) to learn about creating and managing containers.
