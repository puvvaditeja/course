# GCP Cloud Build

## What is Cloud Build?

Cloud Build is a fully managed continuous integration and continuous delivery (CI/CD) platform provided by Google Cloud Platform. It executes builds on GCP infrastructure, importing source code from various repositories, executing build steps, and producing artifacts such as Docker containers or Java archives.

### Key Features

- **Fully Managed**: No infrastructure to maintain
- **Scalable**: Automatically scales based on build demand
- **Fast**: Distributed builds with caching capabilities
- **Flexible**: Support for any language or framework
- **Secure**: Integration with GCP security services
- **Native Docker Support**: Build containers without Docker installed locally

### Cloud Build Architecture

```
┌─────────────────┐
│  Source Code    │
│  (Git Repo)     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐      ┌──────────────────┐
│  Build Trigger  │─────▶│   Cloud Build    │
└─────────────────┘      │   (Build Steps)  │
                         └────────┬─────────┘
                                  │
                    ┌─────────────┼─────────────┐
                    ▼             ▼             ▼
              ┌──────────┐  ┌──────────┐  ┌──────────┐
              │ Artifact │  │Container │  │  Deploy  │
              │ Registry │  │ Registry │  │ to GCP   │
              └──────────┘  └──────────┘  └──────────┘
```

### Cloud Build vs Other CI/CD Tools

| Feature | Cloud Build | Jenkins | GitHub Actions |
|---------|-------------|---------|----------------|
| Hosting | Fully managed | Self-hosted | Fully managed |
| Configuration | YAML | Groovy/Declarative | YAML |
| Scaling | Automatic | Manual | Automatic |
| Pricing | Pay per build minute | Infrastructure cost | Free tier + paid |
| GCP Integration | Native | Via plugins | Via actions |
| Docker Support | Native | Via plugins | Native |

---

## Build Configuration

Cloud Build uses a configuration file to define build steps. The default configuration file is `cloudbuild.yaml`.

### Basic cloudbuild.yaml Structure

```yaml
# cloudbuild.yaml
steps:
  - name: 'builder-image'
    args: ['arg1', 'arg2']
    env:
      - 'KEY=VALUE'

options:
  machineType: 'N1_HIGHCPU_8'
  logging: 'CLOUD_LOGGING_ONLY'

timeout: '1200s'
```

### Configuration Components

#### 1. Steps

Each step runs in a Docker container and executes a specific task.

```yaml
steps:
  # Step 1: Install dependencies
  - name: 'gcr.io/cloud-builders/npm'
    args: ['install']

  # Step 2: Run tests
  - name: 'gcr.io/cloud-builders/npm'
    args: ['test']

  # Step 3: Build application
  - name: 'gcr.io/cloud-builders/npm'
    args: ['run', 'build']
```

#### 2. Built-in Cloud Builders

Google provides official builder images for common tools.

| Builder | Purpose | Example |
|---------|---------|---------|
| `gcr.io/cloud-builders/docker` | Build Docker images | `docker build` |
| `gcr.io/cloud-builders/gcloud` | Run gcloud commands | `gcloud deploy` |
| `gcr.io/cloud-builders/kubectl` | Kubernetes operations | `kubectl apply` |
| `gcr.io/cloud-builders/mvn` | Maven builds | `mvn package` |
| `gcr.io/cloud-builders/gradle` | Gradle builds | `gradle build` |
| `gcr.io/cloud-builders/npm` | Node.js builds | `npm install` |
| `gcr.io/cloud-builders/go` | Go builds | `go build` |
| `gcr.io/cloud-builders/git` | Git operations | `git clone` |

#### 3. Environment Variables

Pass configuration to build steps.

```yaml
steps:
  - name: 'gcr.io/cloud-builders/docker'
    args: ['build', '-t', 'gcr.io/$PROJECT_ID/myapp:$SHORT_SHA', '.']
    env:
      - 'ENVIRONMENT=production'
      - 'DEBUG=false'
```

**Built-in Variables:**
- `$PROJECT_ID`: GCP project ID
- `$BUILD_ID`: Unique build identifier
- `$COMMIT_SHA`: Full commit SHA
- `$SHORT_SHA`: Short commit SHA (7 characters)
- `$REPO_NAME`: Repository name
- `$BRANCH_NAME`: Branch name
- `$TAG_NAME`: Tag name (if triggered by tag)
- `$REVISION_ID`: Commit SHA or tag

#### 4. Substitutions

Custom variables that can be used in build configuration.

```yaml
substitutions:
  _IMAGE_NAME: myapp
  _REGION: us-central1
  _ENVIRONMENT: production

steps:
  - name: 'gcr.io/cloud-builders/docker'
    args: ['build', '-t', 'gcr.io/$PROJECT_ID/${_IMAGE_NAME}:$SHORT_SHA', '.']

  - name: 'gcr.io/cloud-builders/gcloud'
    args:
      - 'run'
      - 'deploy'
      - '${_IMAGE_NAME}'
      - '--image=gcr.io/$PROJECT_ID/${_IMAGE_NAME}:$SHORT_SHA'
      - '--region=${_REGION}'
```

### Example Build Configurations

#### Java Application Build

```yaml
# cloudbuild.yaml - Java Spring Boot application
steps:
  # Step 1: Run unit tests
  - name: 'gcr.io/cloud-builders/mvn'
    args: ['test']
    id: 'test'

  # Step 2: Build JAR file
  - name: 'gcr.io/cloud-builders/mvn'
    args: ['package', '-DskipTests']
    id: 'build'
    waitFor: ['test']

  # Step 3: Build Docker image
  - name: 'gcr.io/cloud-builders/docker'
    args: [
      'build',
      '-t', 'gcr.io/$PROJECT_ID/spring-app:$SHORT_SHA',
      '-t', 'gcr.io/$PROJECT_ID/spring-app:latest',
      '.'
    ]
    id: 'docker-build'
    waitFor: ['build']

  # Step 4: Push to Container Registry
  - name: 'gcr.io/cloud-builders/docker'
    args: ['push', '--all-tags', 'gcr.io/$PROJECT_ID/spring-app']
    id: 'docker-push'
    waitFor: ['docker-build']

  # Step 5: Deploy to Cloud Run
  - name: 'gcr.io/cloud-builders/gcloud'
    args:
      - 'run'
      - 'deploy'
      - 'spring-app'
      - '--image=gcr.io/$PROJECT_ID/spring-app:$SHORT_SHA'
      - '--region=us-central1'
      - '--platform=managed'
      - '--allow-unauthenticated'
    id: 'deploy'
    waitFor: ['docker-push']

# Store images in Container Registry
images:
  - 'gcr.io/$PROJECT_ID/spring-app:$SHORT_SHA'
  - 'gcr.io/$PROJECT_ID/spring-app:latest'

# Build options
options:
  machineType: 'N1_HIGHCPU_8'
  logging: 'CLOUD_LOGGING_ONLY'

timeout: '1200s'
```

#### Node.js Application Build

```yaml
# cloudbuild.yaml - Node.js/React application
steps:
  # Step 1: Install dependencies
  - name: 'gcr.io/cloud-builders/npm'
    args: ['install']
    id: 'install'

  # Step 2: Run linter
  - name: 'gcr.io/cloud-builders/npm'
    args: ['run', 'lint']
    id: 'lint'
    waitFor: ['install']

  # Step 3: Run unit tests
  - name: 'gcr.io/cloud-builders/npm'
    args: ['test']
    env:
      - 'CI=true'
    id: 'test'
    waitFor: ['install']

  # Step 4: Build production bundle
  - name: 'gcr.io/cloud-builders/npm'
    args: ['run', 'build']
    id: 'build'
    waitFor: ['lint', 'test']

  # Step 5: Build Docker image
  - name: 'gcr.io/cloud-builders/docker'
    args: [
      'build',
      '-t', 'gcr.io/$PROJECT_ID/react-app:$SHORT_SHA',
      '-t', 'gcr.io/$PROJECT_ID/react-app:latest',
      '-f', 'Dockerfile',
      '.'
    ]
    id: 'docker-build'
    waitFor: ['build']

  # Step 6: Push to Container Registry
  - name: 'gcr.io/cloud-builders/docker'
    args: ['push', '--all-tags', 'gcr.io/$PROJECT_ID/react-app']
    id: 'docker-push'
    waitFor: ['docker-build']

  # Step 7: Deploy to Cloud Run
  - name: 'gcr.io/cloud-builders/gcloud'
    args:
      - 'run'
      - 'deploy'
      - 'react-app'
      - '--image=gcr.io/$PROJECT_ID/react-app:$SHORT_SHA'
      - '--region=us-central1'
      - '--platform=managed'
    id: 'deploy'
    waitFor: ['docker-push']

images:
  - 'gcr.io/$PROJECT_ID/react-app:$SHORT_SHA'
  - 'gcr.io/$PROJECT_ID/react-app:latest'

options:
  machineType: 'N1_HIGHCPU_8'

timeout: '1800s'
```

#### Python Application Build

```yaml
# cloudbuild.yaml - Python Flask application
steps:
  # Step 1: Install dependencies
  - name: 'python:3.9'
    entrypoint: 'pip'
    args: ['install', '-r', 'requirements.txt', '--user']
    id: 'install'

  # Step 2: Run tests
  - name: 'python:3.9'
    entrypoint: 'python'
    args: ['-m', 'pytest', 'tests/', '-v']
    id: 'test'
    waitFor: ['install']

  # Step 3: Run linter
  - name: 'python:3.9'
    entrypoint: 'python'
    args: ['-m', 'flake8', 'src/']
    id: 'lint'
    waitFor: ['install']

  # Step 4: Build Docker image
  - name: 'gcr.io/cloud-builders/docker'
    args: [
      'build',
      '-t', 'gcr.io/$PROJECT_ID/flask-app:$SHORT_SHA',
      '-t', 'gcr.io/$PROJECT_ID/flask-app:latest',
      '.'
    ]
    id: 'docker-build'
    waitFor: ['test', 'lint']

  # Step 5: Push image
  - name: 'gcr.io/cloud-builders/docker'
    args: ['push', '--all-tags', 'gcr.io/$PROJECT_ID/flask-app']
    id: 'docker-push'
    waitFor: ['docker-build']

  # Step 6: Deploy to Cloud Run
  - name: 'gcr.io/cloud-builders/gcloud'
    args:
      - 'run'
      - 'deploy'
      - 'flask-app'
      - '--image=gcr.io/$PROJECT_ID/flask-app:$SHORT_SHA'
      - '--region=us-central1'
      - '--platform=managed'
      - '--set-env-vars=ENVIRONMENT=production'
    id: 'deploy'
    waitFor: ['docker-push']

images:
  - 'gcr.io/$PROJECT_ID/flask-app:$SHORT_SHA'
  - 'gcr.io/$PROJECT_ID/flask-app:latest'

timeout: '1200s'
```

---

## Build Triggers

Build triggers automatically start builds in response to code changes.

### Trigger Types

1. **Push to Branch**: Trigger on push to specific branches
2. **Push to Tag**: Trigger on Git tags
3. **Pull Request**: Trigger on pull requests (GitHub only)
4. **Manual**: Manually initiated builds
5. **Webhook**: Trigger via webhook
6. **Pub/Sub**: Trigger via Cloud Pub/Sub messages

### Creating Build Triggers

#### Using gcloud CLI

```bash
# Enable Cloud Build API
gcloud services enable cloudbuild.googleapis.com

# Create trigger for Cloud Source Repository
gcloud builds triggers create cloud-source-repositories \
    --name="main-branch-trigger" \
    --repo="my-app-repo" \
    --branch-pattern="^main$" \
    --build-config="cloudbuild.yaml" \
    --description="Build on push to main branch"

# Create trigger for GitHub
gcloud builds triggers create github \
    --name="github-main-trigger" \
    --repo-name="my-app" \
    --repo-owner="myusername" \
    --branch-pattern="^main$" \
    --build-config="cloudbuild.yaml"

# Create trigger with inline build config
gcloud builds triggers create cloud-source-repositories \
    --name="inline-trigger" \
    --repo="my-app-repo" \
    --branch-pattern="^develop$" \
    --inline-config='
steps:
  - name: gcr.io/cloud-builders/docker
    args: ["build", "-t", "gcr.io/$PROJECT_ID/app:$SHORT_SHA", "."]
images:
  - gcr.io/$PROJECT_ID/app:$SHORT_SHA'

# Create trigger with substitutions
gcloud builds triggers create cloud-source-repositories \
    --name="staging-trigger" \
    --repo="my-app-repo" \
    --branch-pattern="^staging$" \
    --build-config="cloudbuild.yaml" \
    --substitutions="_ENV=staging,_REGION=us-east1"
```

#### List and Manage Triggers

```bash
# List all triggers
gcloud builds triggers list

# Describe a trigger
gcloud builds triggers describe TRIGGER_ID

# Update a trigger
gcloud builds triggers update TRIGGER_ID \
    --branch-pattern="^(main|develop)$"

# Delete a trigger
gcloud builds triggers delete TRIGGER_ID

# Run a trigger manually
gcloud builds triggers run TRIGGER_ID \
    --branch=main
```

### Trigger Configuration Examples

#### Branch-based Triggers

```bash
# Trigger on main branch only
gcloud builds triggers create cloud-source-repositories \
    --name="production-trigger" \
    --repo="webapp-repo" \
    --branch-pattern="^main$" \
    --build-config="cloudbuild.prod.yaml"

# Trigger on any feature branch
gcloud builds triggers create cloud-source-repositories \
    --name="feature-trigger" \
    --repo="webapp-repo" \
    --branch-pattern="^feature/.*" \
    --build-config="cloudbuild.dev.yaml"

# Trigger on multiple branches
gcloud builds triggers create cloud-source-repositories \
    --name="multi-branch-trigger" \
    --repo="webapp-repo" \
    --branch-pattern="^(main|develop|staging)$" \
    --build-config="cloudbuild.yaml"
```

#### Tag-based Triggers

```bash
# Trigger on version tags
gcloud builds triggers create cloud-source-repositories \
    --name="release-trigger" \
    --repo="webapp-repo" \
    --tag-pattern="^v[0-9]+\.[0-9]+\.[0-9]+$" \
    --build-config="cloudbuild.release.yaml"
```

#### Advanced Trigger Configuration

```yaml
# trigger-config.yaml
name: advanced-trigger
description: Advanced trigger with multiple conditions
github:
  owner: mycompany
  name: myapp
  push:
    branch: ^main$
includedFiles:
  - 'src/**'
  - 'Dockerfile'
  - 'cloudbuild.yaml'
ignoredFiles:
  - '**/*.md'
  - 'docs/**'
filename: cloudbuild.yaml
substitutions:
  _ENVIRONMENT: production
  _REGION: us-central1
```

```bash
# Create trigger from configuration file
gcloud builds triggers create github \
    --trigger-config=trigger-config.yaml
```

---

## Automated Builds

### Build Execution Flow

```
1. Trigger Event → 2. Fetch Source → 3. Execute Steps → 4. Store Artifacts → 5. Notify
```

### Running Builds Manually

```bash
# Run build with default cloudbuild.yaml
gcloud builds submit --config=cloudbuild.yaml

# Run build with custom config
gcloud builds submit --config=custom-build.yaml

# Run build from specific directory
gcloud builds submit --config=cloudbuild.yaml ./src

# Run build with substitutions
gcloud builds submit \
    --config=cloudbuild.yaml \
    --substitutions=_ENV=staging,_VERSION=1.2.3

# Run build without config (auto-detect)
gcloud builds submit --tag=gcr.io/PROJECT_ID/myapp
```

### Viewing Build History

```bash
# List all builds
gcloud builds list

# List builds with specific status
gcloud builds list --filter="status=SUCCESS"

# List recent builds
gcloud builds list --limit=10

# Get build details
gcloud builds describe BUILD_ID

# View build logs
gcloud builds log BUILD_ID

# Stream build logs in real-time
gcloud builds log BUILD_ID --stream
```

### Build Caching

Improve build performance with caching.

```yaml
# cloudbuild.yaml with caching
steps:
  # Pull previous image for layer caching
  - name: 'gcr.io/cloud-builders/docker'
    entrypoint: 'bash'
    args:
      - '-c'
      - |
        docker pull gcr.io/$PROJECT_ID/myapp:latest || exit 0

  # Build with cache
  - name: 'gcr.io/cloud-builders/docker'
    args: [
      'build',
      '-t', 'gcr.io/$PROJECT_ID/myapp:$SHORT_SHA',
      '--cache-from', 'gcr.io/$PROJECT_ID/myapp:latest',
      '.'
    ]

  # Push new image
  - name: 'gcr.io/cloud-builders/docker'
    args: ['push', 'gcr.io/$PROJECT_ID/myapp:$SHORT_SHA']

images:
  - 'gcr.io/$PROJECT_ID/myapp:$SHORT_SHA'
  - 'gcr.io/$PROJECT_ID/myapp:latest'
```

---

## Container Building

Cloud Build is optimized for building container images.

### Docker Image Build

```yaml
# cloudbuild.yaml - Simple Docker build
steps:
  - name: 'gcr.io/cloud-builders/docker'
    args: [
      'build',
      '-t', 'gcr.io/$PROJECT_ID/myapp:$SHORT_SHA',
      '.'
    ]

images:
  - 'gcr.io/$PROJECT_ID/myapp:$SHORT_SHA'
```

### Multi-stage Docker Build

```dockerfile
# Dockerfile - Multi-stage build
# Stage 1: Build
FROM maven:3.8-openjdk-17 AS build
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests

# Stage 2: Runtime
FROM openjdk:17-jdk-slim
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

```yaml
# cloudbuild.yaml for multi-stage build
steps:
  - name: 'gcr.io/cloud-builders/docker'
    args: [
      'build',
      '-t', 'gcr.io/$PROJECT_ID/myapp:$SHORT_SHA',
      '-f', 'Dockerfile',
      '.'
    ]

images:
  - 'gcr.io/$PROJECT_ID/myapp:$SHORT_SHA'
```

### Building Multiple Images

```yaml
# cloudbuild.yaml - Build multiple services
steps:
  # Build frontend
  - name: 'gcr.io/cloud-builders/docker'
    args: [
      'build',
      '-t', 'gcr.io/$PROJECT_ID/frontend:$SHORT_SHA',
      './frontend'
    ]

  # Build backend API
  - name: 'gcr.io/cloud-builders/docker'
    args: [
      'build',
      '-t', 'gcr.io/$PROJECT_ID/backend-api:$SHORT_SHA',
      './backend'
    ]

  # Build worker service
  - name: 'gcr.io/cloud-builders/docker'
    args: [
      'build',
      '-t', 'gcr.io/$PROJECT_ID/worker:$SHORT_SHA',
      './worker'
    ]

images:
  - 'gcr.io/$PROJECT_ID/frontend:$SHORT_SHA'
  - 'gcr.io/$PROJECT_ID/backend-api:$SHORT_SHA'
  - 'gcr.io/$PROJECT_ID/worker:$SHORT_SHA'
```

### Kaniko for Building in Kubernetes

Kaniko builds container images without requiring Docker daemon.

```yaml
# cloudbuild.yaml - Using Kaniko
steps:
  - name: 'gcr.io/kaniko-project/executor:latest'
    args: [
      '--destination=gcr.io/$PROJECT_ID/myapp:$SHORT_SHA',
      '--destination=gcr.io/$PROJECT_ID/myapp:latest',
      '--cache=true',
      '--cache-ttl=24h'
    ]

images:
  - 'gcr.io/$PROJECT_ID/myapp:$SHORT_SHA'
  - 'gcr.io/$PROJECT_ID/myapp:latest'
```

### BuildKit for Advanced Builds

```yaml
# cloudbuild.yaml - Using BuildKit
steps:
  - name: 'gcr.io/cloud-builders/docker'
    entrypoint: 'bash'
    args:
      - '-c'
      - |
        docker buildx create --use
        docker buildx build \
          --platform linux/amd64,linux/arm64 \
          -t gcr.io/$PROJECT_ID/myapp:$SHORT_SHA \
          --push \
          .
    env:
      - 'DOCKER_BUILDKIT=1'
```

### Container Security Scanning

```yaml
# cloudbuild.yaml - Build with vulnerability scanning
steps:
  # Build image
  - name: 'gcr.io/cloud-builders/docker'
    args: [
      'build',
      '-t', 'gcr.io/$PROJECT_ID/myapp:$SHORT_SHA',
      '.'
    ]

  # Push image
  - name: 'gcr.io/cloud-builders/docker'
    args: ['push', 'gcr.io/$PROJECT_ID/myapp:$SHORT_SHA']

  # Scan for vulnerabilities
  - name: 'gcr.io/cloud-builders/gcloud'
    args: [
      'artifacts', 'docker', 'images', 'scan',
      'gcr.io/$PROJECT_ID/myapp:$SHORT_SHA'
    ]

images:
  - 'gcr.io/$PROJECT_ID/myapp:$SHORT_SHA'
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Cloud Build | Fully managed CI/CD platform on GCP |
| Configuration | YAML-based build steps with built-in builders |
| Build Triggers | Automated builds on code changes |
| Container Building | Native Docker support with caching |

## Next Topic

Continue to [Cloud Deploy](./04-cloud-deploy.md) to learn about automated deployment pipelines.
