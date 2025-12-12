# Docker Registry and Repository

## What is a Docker Registry?

A Docker registry is a storage and distribution system for Docker images. It allows you to store, manage, and distribute Docker images across different environments and teams.

### Key Concepts

- **Registry**: Server-side application that stores and distributes Docker images
- **Repository**: Collection of related images (usually different versions of the same application)
- **Tag**: Version identifier for images within a repository
- **Image Name**: `registry/repository:tag` (e.g., `docker.io/nginx:1.25`)

### Registry Architecture

```
┌─────────────────────────────────────┐
│        Docker Client (CLI)          │
│     docker push / docker pull       │
└──────────────┬──────────────────────┘
               │ HTTPS
┌──────────────▼──────────────────────┐
│         Docker Registry             │
│  ┌───────────────────────────────┐  │
│  │      Image Repository 1       │  │
│  │  nginx:latest                 │  │
│  │  nginx:1.25                   │  │
│  │  nginx:1.24                   │  │
│  └───────────────────────────────┘  │
│  ┌───────────────────────────────┐  │
│  │      Image Repository 2       │  │
│  │  myapp:v1.0                   │  │
│  │  myapp:v1.1                   │  │
│  └───────────────────────────────┘  │
└─────────────────────────────────────┘
```

### Types of Registries

1. **Public Registries**
   - Docker Hub (default)
   - GitHub Container Registry (ghcr.io)
   - Google Container Registry (gcr.io)
   - Amazon ECR Public

2. **Private Registries**
   - Self-hosted Docker Registry
   - Docker Trusted Registry (DTR)
   - Amazon ECR
   - Azure Container Registry
   - Google Container Registry (private)
   - GitLab Container Registry
   - Harbor

---

## Docker Hub

Docker Hub is the default public registry for Docker images. It hosts millions of images from the community and official vendors.

### Docker Hub Features

- **Public Repositories**: Free, unlimited public image storage
- **Private Repositories**: Limited free private repos, paid for more
- **Official Images**: Verified images from vendors and Docker
- **Automated Builds**: Link to GitHub/Bitbucket for automatic builds
- **Webhooks**: Trigger actions when images are pushed
- **Organizations**: Manage teams and access control

### Creating a Docker Hub Account

```bash
# 1. Visit https://hub.docker.com and sign up

# 2. Login from CLI
docker login

# Enter username and password when prompted
# Login Succeeded

# Login to specific registry
docker login registry.example.com

# Login with credentials
docker login -u username -p password

# Login with token
echo $DOCKER_TOKEN | docker login -u username --password-stdin
```

### Docker Hub Repositories

#### Repository Naming Convention

```
[USERNAME]/[REPOSITORY]:[TAG]

Examples:
nginx                           # Official image
nginx:latest                    # Official with tag
myusername/myapp               # User image
myusername/myapp:1.0           # User image with tag
myorg/frontend:dev             # Organization image
```

---

## Pushing Images to Docker Hub

### Step-by-Step Process

```bash
# 1. Build your image
docker build -t myapp:latest .

# 2. Tag image with your Docker Hub username
docker tag myapp:latest username/myapp:latest

# 3. Login to Docker Hub (if not already logged in)
docker login

# 4. Push image
docker push username/myapp:latest

# Push output:
# The push refers to repository [docker.io/username/myapp]
# 5f70bf18a086: Pushed
# latest: digest: sha256:abcd1234... size: 1234
```

### Pushing Multiple Tags

```bash
# Tag with multiple versions
docker tag myapp:latest username/myapp:latest
docker tag myapp:latest username/myapp:1.0.0
docker tag myapp:latest username/myapp:1.0

# Push all tags
docker push username/myapp:latest
docker push username/myapp:1.0.0
docker push username/myapp:1.0

# Or push all tags at once
docker push --all-tags username/myapp
```

### Practical Example

```bash
# Build application
docker build -t mywebapp:latest .

# Tag for Docker Hub
docker tag mywebapp:latest johndoe/mywebapp:latest
docker tag mywebapp:latest johndoe/mywebapp:v1.0.0
docker tag mywebapp:latest johndoe/mywebapp:stable

# Login
docker login

# Push images
docker push johndoe/mywebapp:latest
docker push johndoe/mywebapp:v1.0.0
docker push johndoe/mywebapp:stable
```

---

## Pulling Images from Docker Hub

### Basic Pull Commands

```bash
# Pull latest version (default)
docker pull nginx

# Equivalent to
docker pull nginx:latest
docker pull docker.io/library/nginx:latest

# Pull specific version
docker pull nginx:1.25-alpine

# Pull specific digest
docker pull nginx@sha256:abcd1234...

# Pull from user repository
docker pull username/myapp:latest

# Pull from organization
docker pull organization/myapp:latest

# Pull all tags (use cautiously)
docker pull --all-tags nginx
```

### Pull and Run

```bash
# Pull and run in one command (docker run pulls if not present)
docker run -d -p 80:80 nginx:1.25-alpine

# Explicitly pull first, then run
docker pull nginx:1.25-alpine
docker run -d -p 80:80 nginx:1.25-alpine
```

---

## Managing Images on Docker Hub

### Via Web Interface

1. **Create Repository**
   - Go to Docker Hub
   - Click "Create Repository"
   - Set name, description, visibility (public/private)

2. **Update Repository**
   - Add README (Markdown supported)
   - Add description and full details
   - Set repository visibility

3. **Delete Repository**
   - Go to repository settings
   - Click "Delete repository"
   - Confirm deletion

### Via CLI

```bash
# Search Docker Hub
docker search nginx
docker search --limit 5 nginx
docker search --filter stars=100 nginx
docker search --filter is-official=true nginx

# View image information
docker inspect nginx:latest

# Remove local image
docker rmi username/myapp:latest

# Logout
docker logout
```

---

## Private Docker Registry

Running your own private registry gives you complete control over your images.

### Setting Up Private Registry

```bash
# Run registry container
docker run -d \
  -p 5000:5000 \
  --name registry \
  --restart always \
  -v /mnt/registry:/var/lib/registry \
  registry:2

# Verify registry is running
curl http://localhost:5000/v2/_catalog
```

### Using Private Registry

```bash
# Tag image for private registry
docker tag myapp:latest localhost:5000/myapp:latest

# Push to private registry
docker push localhost:5000/myapp:latest

# Pull from private registry
docker pull localhost:5000/myapp:latest

# List images in registry
curl http://localhost:5000/v2/_catalog

# List tags for image
curl http://localhost:5000/v2/myapp/tags/list
```

### Secure Private Registry with TLS

```bash
# Generate SSL certificate (for production use CA-signed cert)
mkdir -p certs
openssl req -newkey rsa:4096 -nodes -sha256 \
  -keyout certs/domain.key -x509 -days 365 \
  -out certs/domain.crt

# Run registry with TLS
docker run -d \
  -p 5000:5000 \
  --name registry \
  --restart always \
  -v $(pwd)/certs:/certs \
  -v /mnt/registry:/var/lib/registry \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  registry:2
```

### Registry with Authentication

```bash
# Create password file
mkdir auth
docker run --entrypoint htpasswd \
  registry:2 -Bbn username password > auth/htpasswd

# Run registry with authentication
docker run -d \
  -p 5000:5000 \
  --name registry \
  --restart always \
  -v $(pwd)/auth:/auth \
  -v /mnt/registry:/var/lib/registry \
  -e "REGISTRY_AUTH=htpasswd" \
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
  -e "REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd" \
  registry:2

# Login to registry
docker login localhost:5000
# Enter username and password

# Push image
docker push localhost:5000/myapp:latest
```

### Docker Registry Configuration

```yaml
# config.yml
version: 0.1
log:
  level: info
storage:
  filesystem:
    rootdirectory: /var/lib/registry
  delete:
    enabled: true
http:
  addr: :5000
  headers:
    X-Content-Type-Options: [nosniff]
health:
  storagedriver:
    enabled: true
    interval: 10s
    threshold: 3
```

```bash
# Run registry with custom config
docker run -d \
  -p 5000:5000 \
  --name registry \
  --restart always \
  -v $(pwd)/config.yml:/etc/docker/registry/config.yml \
  -v /mnt/registry:/var/lib/registry \
  registry:2
```

---

## Cloud-Based Registries

### Amazon Elastic Container Registry (ECR)

```bash
# Install AWS CLI and configure credentials
aws configure

# Login to ECR
aws ecr get-login-password --region us-east-1 | \
  docker login --username AWS --password-stdin \
  123456789012.dkr.ecr.us-east-1.amazonaws.com

# Create repository
aws ecr create-repository --repository-name myapp

# Tag image
docker tag myapp:latest \
  123456789012.dkr.ecr.us-east-1.amazonaws.com/myapp:latest

# Push image
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/myapp:latest

# Pull image
docker pull 123456789012.dkr.ecr.us-east-1.amazonaws.com/myapp:latest
```

### Google Container Registry (GCR)

```bash
# Install gcloud CLI and authenticate
gcloud auth login
gcloud auth configure-docker

# Tag image
docker tag myapp:latest gcr.io/project-id/myapp:latest

# Push image
docker push gcr.io/project-id/myapp:latest

# Pull image
docker pull gcr.io/project-id/myapp:latest
```

### Azure Container Registry (ACR)

```bash
# Install Azure CLI and login
az login

# Create registry
az acr create --resource-group myResourceGroup \
  --name myregistry --sku Basic

# Login to ACR
az acr login --name myregistry

# Tag image
docker tag myapp:latest myregistry.azurecr.io/myapp:latest

# Push image
docker push myregistry.azurecr.io/myapp:latest

# Pull image
docker pull myregistry.azurecr.io/myapp:latest
```

### GitHub Container Registry (GHCR)

```bash
# Create personal access token on GitHub with write:packages scope

# Login to GHCR
echo $GITHUB_TOKEN | docker login ghcr.io -u username --password-stdin

# Tag image
docker tag myapp:latest ghcr.io/username/myapp:latest

# Push image
docker push ghcr.io/username/myapp:latest

# Pull image (public)
docker pull ghcr.io/username/myapp:latest
```

---

## Registry Best Practices

### 1. Use Specific Tags

```bash
# Bad: Unpredictable
docker pull myapp:latest

# Good: Specific version
docker pull myapp:1.0.0
docker pull myapp:v2.3.1
```

### 2. Implement Tagging Strategy

```bash
# Semantic versioning
docker tag myapp:latest myapp:1.0.0    # Specific version
docker tag myapp:latest myapp:1.0      # Minor version
docker tag myapp:latest myapp:1        # Major version
docker tag myapp:latest myapp:latest   # Latest stable

# Environment tags
docker tag myapp:latest myapp:dev
docker tag myapp:latest myapp:staging
docker tag myapp:latest myapp:prod

# Git commit tags
docker tag myapp:latest myapp:${GIT_COMMIT_SHA}
docker tag myapp:latest myapp:$(git rev-parse --short HEAD)
```

### 3. Scan Images for Vulnerabilities

```bash
# Docker Hub scanning (automatic for public repos)

# Scan with docker scan (Snyk)
docker scan myapp:latest

# Scan with trivy
trivy image myapp:latest

# Scan with Clair
clairctl analyze myapp:latest
```

### 4. Clean Up Old Images

```bash
# On Docker Hub: Set up automated cleanup policies

# On private registry: Use lifecycle policies

# Local cleanup
docker image prune -a --filter "until=720h"  # Remove images older than 30 days
```

### 5. Use Multi-Architecture Images

```bash
# Build for multiple platforms
docker buildx create --use
docker buildx build --platform linux/amd64,linux/arm64 -t myapp:latest --push .

# Pull appropriate image for platform
docker pull myapp:latest  # Automatically pulls correct architecture
```

### 6. Implement Access Control

```bash
# Docker Hub: Use organizations and teams
# Private Registry: Implement RBAC
# Cloud Registries: Use IAM policies

# Example: ECR policy
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": {
      "AWS": "arn:aws:iam::123456789012:user/username"
    },
    "Action": [
      "ecr:GetDownloadUrlForLayer",
      "ecr:BatchGetImage"
    ]
  }]
}
```

### 7. Enable Image Signing

```bash
# Docker Content Trust (DCT)
export DOCKER_CONTENT_TRUST=1

# Push signed image
docker push username/myapp:latest

# Pull with signature verification
docker pull username/myapp:latest
```

---

## Repository Management

### Organizing Repositories

```
# Organizational structure
organization/
├── frontend:latest
├── frontend:v1.0.0
├── backend:latest
├── backend:v1.0.0
├── api:latest
└── api:v1.0.0

# Environment-based
myapp-dev:latest
myapp-staging:latest
myapp-prod:latest

# Component-based
myapp/web:latest
myapp/api:latest
myapp/worker:latest
```

### Repository Metadata

```bash
# Add labels to images
docker build -t myapp:latest \
  --label "version=1.0.0" \
  --label "maintainer=dev@example.com" \
  --label "description=My Application" \
  .

# View labels
docker inspect --format='{{json .Config.Labels}}' myapp:latest | jq
```

---

## Troubleshooting Registry Issues

### Common Issues and Solutions

```bash
# Issue: "denied: requested access to the resource is denied"
# Solution: Login to registry
docker login

# Issue: "unauthorized: authentication required"
# Solution: Check credentials
docker logout
docker login -u username

# Issue: "error parsing HTTP 403 response body"
# Solution: Check if repository exists and you have access

# Issue: "Get https://registry:5000/v2/: x509: certificate signed by unknown authority"
# Solution: Add certificate or use insecure registry

# For testing only (insecure)
# Edit /etc/docker/daemon.json
{
  "insecure-registries": ["myregistry.com:5000"]
}
# Restart Docker daemon
sudo systemctl restart docker

# Issue: "no basic auth credentials"
# Solution: Login with proper credentials
docker login myregistry.com:5000

# Issue: "name unknown: repository name not known to registry"
# Solution: Verify repository name
docker tag myapp:latest myregistry.com:5000/myapp:latest
docker push myregistry.com:5000/myapp:latest
```

### Debugging Registry

```bash
# Check registry health
curl http://localhost:5000/v2/

# List repositories
curl http://localhost:5000/v2/_catalog

# List tags
curl http://localhost:5000/v2/myapp/tags/list

# View registry logs
docker logs registry

# Test authentication
curl -u username:password http://localhost:5000/v2/_catalog

# Check TLS certificate
openssl s_client -connect myregistry.com:5000 -showcerts
```

---

## Registry API

### Common API Endpoints

```bash
# Check API version
curl http://localhost:5000/v2/

# List repositories
curl http://localhost:5000/v2/_catalog

# List tags for repository
curl http://localhost:5000/v2/myapp/tags/list

# Get manifest
curl -H "Accept: application/vnd.docker.distribution.manifest.v2+json" \
  http://localhost:5000/v2/myapp/manifests/latest

# Delete image (if deletion enabled)
curl -X DELETE http://localhost:5000/v2/myapp/manifests/sha256:digest
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| **Registry** | Storage system for Docker images |
| **Docker Hub** | Default public registry, free for public images |
| **Private Registry** | Self-hosted or cloud-based for private images |
| **Pushing** | `docker tag` then `docker push` |
| **Pulling** | `docker pull` downloads images |
| **Best Practices** | Version tags, scan images, access control |
| **Cloud Registries** | ECR, GCR, ACR, GHCR for managed solutions |

## Key Commands

```bash
# Authentication
docker login                          # Login to Docker Hub
docker login registry.example.com     # Login to private registry
docker logout                         # Logout

# Pushing
docker tag SOURCE TARGET              # Tag image
docker push IMAGE:TAG                 # Push to registry

# Pulling
docker pull IMAGE:TAG                 # Pull from registry
docker search TERM                    # Search Docker Hub

# Private Registry
docker run -p 5000:5000 registry:2   # Run registry
curl http://localhost:5000/v2/_catalog  # List repositories
```

---

## Next Steps

After mastering Docker registries, you should:
- Explore Docker Compose for multi-container applications
- Learn Kubernetes for container orchestration
- Implement CI/CD pipelines with Docker
- Study container security best practices
- Practice with real-world containerization projects

---

**Congratulations!** You've completed the Docker topics. You now have the knowledge to:
- Understand Docker architecture and containerization
- Build efficient Docker images with Dockerfiles
- Manage containers throughout their lifecycle
- Master Docker CLI commands
- Work with Docker registries and repositories

Continue practicing and building real-world applications with Docker!
