# GCP DevOps Tools

## Introduction to DevOps in GCP

Google Cloud Platform (GCP) provides a comprehensive suite of tools and services that support the entire DevOps lifecycle, from source code management to production deployment and monitoring.

### GCP DevOps Ecosystem

```
┌─────────────────────────────────────────────────────────────────┐
│                     GCP DevOps Pipeline                         │
├─────────────────────────────────────────────────────────────────┤
│  Source Code        │  Build & Test    │  Deploy & Release     │
│                     │                  │                       │
│  - Cloud Source     │  - Cloud Build   │  - Cloud Deploy       │
│    Repositories     │  - Artifact      │  - GKE                │
│  - GitHub           │    Registry      │  - Cloud Run          │
│  - Bitbucket        │  - Container     │  - Compute Engine     │
│                     │    Registry      │  - App Engine         │
├─────────────────────────────────────────────────────────────────┤
│  Infrastructure     │  Monitoring      │  Security             │
│                     │                  │                       │
│  - Cloud Deployment │  - Cloud         │  - Binary             │
│    Manager          │    Monitoring    │    Authorization      │
│  - Terraform        │  - Cloud Logging │  - Secret Manager     │
│  - Config Connector │  - Error         │  - Cloud IAM          │
│                     │    Reporting     │                       │
└─────────────────────────────────────────────────────────────────┘
```

### Key GCP DevOps Services

| Service | Purpose | Use Case |
|---------|---------|----------|
| Cloud Source Repositories | Git repository hosting | Private Git repositories |
| Cloud Build | CI/CD service | Build, test, and deploy |
| Cloud Deploy | Deployment automation | Progressive delivery |
| Artifact Registry | Package management | Store build artifacts |
| Container Registry | Container image storage | Docker images |
| Cloud Operations | Monitoring and logging | Observability |
| Secret Manager | Secrets management | API keys, passwords |
| Binary Authorization | Container security | Image verification |

### Benefits of GCP DevOps

1. **Fully Managed Services**: No infrastructure to maintain
2. **Scalability**: Automatic scaling of build and deployment resources
3. **Integration**: Seamless integration with other GCP services
4. **Security**: Built-in security features and compliance
5. **Cost-Effective**: Pay only for what you use
6. **Global Infrastructure**: Deploy applications globally

---

## Git Repositories in GCP

### Version Control Overview

Version control is the foundation of modern DevOps practices. GCP supports multiple Git hosting options.

### Git Hosting Options in GCP

#### 1. Cloud Source Repositories (CSR)
Google's fully-managed Git repository service.

**Features:**
- Private Git repositories
- Unlimited repositories and storage
- Integration with Cloud Build
- Code search across repositories
- Automatic mirroring from GitHub/Bitbucket

#### 2. External Repository Integration
Connect external Git providers to GCP services.

**Supported Providers:**
- GitHub
- Bitbucket
- GitLab

### Git Workflow Best Practices

```
main (production)
  │
  ├── develop (integration branch)
  │     │
  │     ├── feature/user-auth
  │     │
  │     ├── feature/payment-api
  │     │
  │     └── bugfix/login-error
  │
  └── hotfix/security-patch
```

**Branching Strategy:**
- `main`: Production-ready code
- `develop`: Integration branch for features
- `feature/*`: New features
- `bugfix/*`: Bug fixes
- `hotfix/*`: Urgent production fixes

---

## Cloud Source Repositories

Cloud Source Repositories (CSR) is a fully-featured, scalable, private Git repository hosted on Google Cloud Platform.

### Key Features

1. **Fully Managed**: No servers to maintain
2. **Scalable**: Handle large repositories and teams
3. **Secure**: IAM integration for access control
4. **Integrated**: Works with Cloud Build, Cloud Debugger, Cloud Trace
5. **Code Search**: Search across all repositories
6. **Mirroring**: Sync with GitHub or Bitbucket

### Creating a Cloud Source Repository

#### Using gcloud CLI

```bash
# Enable Cloud Source Repositories API
gcloud services enable sourcerepo.googleapis.com

# Create a new repository
gcloud source repos create my-app-repo

# List all repositories
gcloud source repos list

# Get repository details
gcloud source repos describe my-app-repo

# Clone the repository
gcloud source repos clone my-app-repo

# Delete a repository
gcloud source repos delete my-app-repo
```

#### Using the Console

1. Navigate to Cloud Source Repositories in GCP Console
2. Click "Add Repository"
3. Choose "Create new repository"
4. Enter repository name
5. Click "Create"

### Working with Cloud Source Repositories

#### Initial Setup

```bash
# Configure Git credentials
gcloud init

# Configure Git to use gcloud credentials
git config credential.helper gcloud.sh

# Set your Git identity
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```

#### Basic Git Operations

```bash
# Clone repository
gcloud source repos clone my-app-repo
cd my-app-repo

# Create a new file
echo "# My Application" > README.md

# Add and commit
git add README.md
git commit -m "Initial commit"

# Push to Cloud Source Repository
git push origin main

# Create a new branch
git checkout -b feature/new-api

# Make changes and push
echo "New feature code" > api.py
git add api.py
git commit -m "Add new API endpoint"
git push origin feature/new-api
```

### Repository Mirroring

Mirror repositories from GitHub or Bitbucket to Cloud Source Repositories.

#### Mirroring from GitHub

```bash
# Create a mirrored repository
gcloud source repos create my-github-mirror \
    --mirror=https://github.com/username/my-repo.git

# Configure authentication
gcloud source repos update my-github-mirror \
    --update-mirror-config=username:token
```

#### Configuration File

```yaml
# mirror-config.yaml
name: my-github-mirror
mirror_config:
  url: https://github.com/username/my-repo.git
  webhook_id: webhook-secret-id
  deploy_key_id: deploy-key-secret-id
```

### Access Control with IAM

Grant access to repositories using Cloud IAM.

```bash
# Grant read access
gcloud source repos set-iam-policy my-app-repo policy.yaml

# Grant write access to a user
gcloud source repos add-iam-policy-binding my-app-repo \
    --member="user:developer@example.com" \
    --role="roles/source.writer"

# Grant admin access to a group
gcloud source repos add-iam-policy-binding my-app-repo \
    --member="group:devops-team@example.com" \
    --role="roles/source.admin"

# View IAM policy
gcloud source repos get-iam-policy my-app-repo
```

#### IAM Policy File

```yaml
# policy.yaml
bindings:
  - members:
      - user:developer1@example.com
      - user:developer2@example.com
    role: roles/source.writer
  - members:
      - group:devops-team@example.com
    role: roles/source.admin
  - members:
      - serviceAccount:build-sa@project.iam.gserviceaccount.com
    role: roles/source.reader
```

### Cloud Source Repository Roles

| Role | Permissions | Use Case |
|------|-------------|----------|
| `roles/source.reader` | Read repository contents | CI/CD service account |
| `roles/source.writer` | Read and write to repository | Developers |
| `roles/source.admin` | Full control of repository | Team leads, DevOps |

### Integrating with Cloud Build

Cloud Source Repositories integrates seamlessly with Cloud Build for automated builds.

#### Trigger Configuration

```yaml
# cloudbuild.yaml in repository root
steps:
  - name: 'gcr.io/cloud-builders/docker'
    args: ['build', '-t', 'gcr.io/$PROJECT_ID/myapp', '.']
  - name: 'gcr.io/cloud-builders/docker'
    args: ['push', 'gcr.io/$PROJECT_ID/myapp']
```

#### Create Build Trigger

```bash
# Create trigger from gcloud
gcloud builds triggers create cloud-source-repositories \
    --repo=my-app-repo \
    --branch-pattern="^main$" \
    --build-config=cloudbuild.yaml \
    --description="Build on main branch"
```

### Code Search

Cloud Source Repositories includes powerful code search capabilities.

#### Search Examples

```bash
# Search for a function across all repositories
# In Cloud Console: Navigate to Source Repositories > Search

# Search patterns:
# - function:myFunction
# - class:UserController
# - file:config.yaml
# - lang:python error handling
```

### Working with Multiple Remotes

Configure multiple Git remotes for backup or synchronization.

```bash
# Add Cloud Source Repository as remote
git remote add google \
    https://source.developers.google.com/p/PROJECT_ID/r/REPO_NAME

# Add GitHub as remote
git remote add github \
    https://github.com/username/repo.git

# List remotes
git remote -v

# Push to multiple remotes
git push google main
git push github main

# Pull from specific remote
git pull google main
```

### Repository Structure Best Practices

```
my-app-repo/
├── .gitignore
├── README.md
├── cloudbuild.yaml          # Cloud Build configuration
├── skaffold.yaml            # Skaffold configuration (optional)
├── k8s/                     # Kubernetes manifests
│   ├── deployment.yaml
│   ├── service.yaml
│   └── ingress.yaml
├── terraform/               # Infrastructure as Code
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
├── src/                     # Application source code
│   ├── main/
│   └── test/
├── scripts/                 # Utility scripts
│   ├── deploy.sh
│   └── test.sh
└── docs/                    # Documentation
    ├── architecture.md
    └── api.md
```

### Example: Complete Repository Setup

```bash
# 1. Create repository
gcloud source repos create webapp-repo

# 2. Clone repository
gcloud source repos clone webapp-repo
cd webapp-repo

# 3. Initialize project structure
mkdir -p src/{main,test} k8s terraform scripts docs

# 4. Create README
cat > README.md << 'EOF'
# Web Application

This repository contains the source code and infrastructure for our web application.

## Structure
- `src/`: Application source code
- `k8s/`: Kubernetes deployment manifests
- `terraform/`: Infrastructure as Code
- `scripts/`: Deployment and utility scripts

## CI/CD
Builds are automatically triggered on push to main branch using Cloud Build.
EOF

# 5. Create .gitignore
cat > .gitignore << 'EOF'
# IDE
.idea/
.vscode/
*.iml

# Build artifacts
target/
build/
dist/
*.jar
*.war

# Environment
.env
.env.local

# Terraform
*.tfstate
*.tfstate.backup
.terraform/

# OS
.DS_Store
Thumbs.db
EOF

# 6. Create Cloud Build configuration
cat > cloudbuild.yaml << 'EOF'
steps:
  - name: 'gcr.io/cloud-builders/docker'
    args: ['build', '-t', 'gcr.io/$PROJECT_ID/webapp:$SHORT_SHA', '.']
  - name: 'gcr.io/cloud-builders/docker'
    args: ['push', 'gcr.io/$PROJECT_ID/webapp:$SHORT_SHA']
images:
  - 'gcr.io/$PROJECT_ID/webapp:$SHORT_SHA'
EOF

# 7. Create Dockerfile
cat > Dockerfile << 'EOF'
FROM openjdk:17-jdk-slim
WORKDIR /app
COPY target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
EOF

# 8. Commit and push
git add .
git commit -m "Initial repository structure"
git push origin main

# 9. Set up IAM permissions
gcloud source repos add-iam-policy-binding webapp-repo \
    --member="group:developers@example.com" \
    --role="roles/source.writer"

# 10. Create Cloud Build trigger
gcloud builds triggers create cloud-source-repositories \
    --repo=webapp-repo \
    --branch-pattern="^main$" \
    --build-config=cloudbuild.yaml
```

### Monitoring Repository Activity

```bash
# View recent commits
git log --oneline -10

# View commit history with details
git log --graph --decorate --all

# View commits by author
git log --author="developer@example.com" --oneline

# View file change history
git log --follow -- src/main/App.java

# View repository statistics
git shortlog -sn --all
```

### Advanced Configuration

#### Commit Hooks

```bash
# Create pre-commit hook
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash
# Run tests before commit
npm test || exit 1
# Run linter
npm run lint || exit 1
EOF

chmod +x .git/hooks/pre-commit
```

#### Git Attributes

```bash
# Create .gitattributes for consistent line endings
cat > .gitattributes << 'EOF'
# Auto detect text files and normalize line endings to LF
* text=auto

# Force LF for scripts
*.sh text eol=lf
*.bash text eol=lf

# Denote binary files
*.png binary
*.jpg binary
*.jar binary
EOF
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| GCP DevOps | Comprehensive suite of managed DevOps services |
| Git in GCP | Cloud Source Repositories, GitHub, Bitbucket integration |
| Cloud Source Repos | Fully-managed, scalable, private Git repositories |
| Integration | Seamless integration with Cloud Build and other GCP services |

## Next Topic

Continue to [Cloud Build](./03-cloud-build.md) to learn about GCP's continuous integration and delivery service.
