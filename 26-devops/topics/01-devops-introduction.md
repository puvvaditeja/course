# DevOps Introduction

## What is DevOps?

DevOps is a set of practices, cultural philosophies, and tools that combines software development (Dev) and IT operations (Ops) to shorten the systems development life cycle while delivering features, fixes, and updates frequently in close alignment with business objectives.

### Core Philosophy

DevOps breaks down the traditional silos between development and operations teams, fostering a culture of collaboration, shared responsibility, and continuous improvement.

**Key Principles:**
- **Collaboration**: Dev and Ops teams work together throughout the entire lifecycle
- **Automation**: Automate repetitive tasks to increase efficiency and reduce errors
- **Continuous Improvement**: Regularly assess and optimize processes
- **Customer-Centric**: Focus on delivering value to end users
- **Fast Feedback**: Quick feedback loops enable rapid iteration

### DevOps vs Traditional Approaches

| Aspect | Traditional | DevOps |
|--------|-------------|--------|
| Team Structure | Siloed teams | Cross-functional teams |
| Deployment Frequency | Weeks/Months | Hours/Days |
| Change Failure Rate | Higher | Lower |
| Mean Time to Recovery | Hours/Days | Minutes/Hours |
| Manual Processes | Many | Automated |
| Feedback Cycle | Slow | Fast |

---

## DevOps Culture and Practices

### The Three Ways of DevOps

#### 1. Systems Thinking (Flow)
Focus on the performance of the entire system, not individual components or teams.

**Practices:**
- Optimize the entire value stream from development to production
- Make work visible through Kanban boards and dashboards
- Limit work in progress (WIP)
- Reduce batch sizes and intervals of work
- Build systems and organizations that are safe to change

#### 2. Amplify Feedback Loops
Create short feedback loops at all stages of the process.

**Practices:**
- Continuous integration and automated testing
- Monitoring and logging in production
- User feedback and A/B testing
- Post-incident reviews and blameless postmortems
- Peer reviews and pair programming

#### 3. Culture of Continual Experimentation and Learning
Foster a culture that encourages innovation and learning from failures.

**Practices:**
- Allocate time for experimentation
- Reward innovation and calculated risk-taking
- Share knowledge across teams
- Convert local discoveries into global improvements
- Build resilience through chaos engineering

### Key DevOps Practices

#### 1. Version Control
All code, configuration, and infrastructure definitions should be in version control.

```bash
# Initialize a git repository
git init

# Track all changes
git add .
git commit -m "Initial commit"

# Use branches for features
git checkout -b feature/new-api-endpoint
```

#### 2. Continuous Integration (CI)
Developers integrate code into a shared repository frequently, with automated builds and tests.

**Benefits:**
- Early detection of integration issues
- Reduced debugging time
- Faster development cycle
- Better code quality

#### 3. Continuous Delivery (CD)
Ensure that code is always in a deployable state and can be released to production at any time.

**Benefits:**
- Reduced deployment risk
- Faster time to market
- Better product quality
- Higher productivity

#### 4. Infrastructure as Code (IaC)
Manage and provision infrastructure through code rather than manual processes.

**Benefits:**
- Consistency across environments
- Version control for infrastructure
- Repeatability and automation
- Documentation through code

#### 5. Monitoring and Logging
Continuous monitoring of applications and infrastructure to detect and resolve issues quickly.

**Key Metrics:**
- Application performance metrics
- Infrastructure health metrics
- Business metrics
- Security metrics

#### 6. Communication and Collaboration
Tools and practices that facilitate team communication and collaboration.

**Practices:**
- ChatOps (Slack, Teams)
- Collaborative documentation (Wikis, Confluence)
- Shared dashboards and visibility
- Regular stand-ups and retrospectives

---

## CI/CD Pipeline

A CI/CD pipeline automates the process of building, testing, and deploying applications.

### Pipeline Stages

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│    Source    │────▶│    Build     │────▶│     Test     │────▶│    Deploy    │
│   Control    │     │   & Package  │     │  & Validate  │     │ to Staging   │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                         │
                                                                         ▼
                                                                ┌──────────────┐
                                                                │    Deploy    │
                                                                │ to Production│
                                                                └──────────────┘
```

### 1. Source Control Stage

Code is committed to a version control system, triggering the pipeline.

```yaml
# Example: Trigger on push to specific branches
trigger:
  branches:
    include:
      - main
      - develop
      - feature/*
```

### 2. Build Stage

Source code is compiled, dependencies are resolved, and artifacts are created.

```yaml
# Example Cloud Build configuration
steps:
  - name: 'gcr.io/cloud-builders/mvn'
    args: ['clean', 'package']
  - name: 'gcr.io/cloud-builders/docker'
    args: ['build', '-t', 'gcr.io/$PROJECT_ID/myapp:$SHORT_SHA', '.']
```

### 3. Test Stage

Automated tests are executed to validate code quality and functionality.

**Test Types:**
- **Unit Tests**: Test individual components
- **Integration Tests**: Test component interactions
- **Security Tests**: Scan for vulnerabilities
- **Performance Tests**: Validate performance requirements

```yaml
# Example test stage
steps:
  - name: 'gcr.io/cloud-builders/mvn'
    args: ['test']
  - name: 'gcr.io/cloud-builders/mvn'
    args: ['verify']
  - name: 'gcr.io/cloud-builders/npm'
    args: ['run', 'test:integration']
```

### 4. Deploy Stage

Artifacts are deployed to target environments (staging, production).

```yaml
# Example deployment stage
steps:
  - name: 'gcr.io/cloud-builders/gcloud'
    args:
      - 'run'
      - 'deploy'
      - 'myapp'
      - '--image=gcr.io/$PROJECT_ID/myapp:$SHORT_SHA'
      - '--region=us-central1'
      - '--platform=managed'
```

### Pipeline Best Practices

1. **Keep It Fast**: Optimize pipeline to complete in under 10 minutes
2. **Fail Fast**: Run quick tests first, expensive tests later
3. **Make It Visible**: Dashboard showing pipeline status
4. **Version Everything**: Pipeline configuration should be versioned
5. **Secure Secrets**: Never hardcode credentials
6. **Idempotent Operations**: Pipeline should be repeatable
7. **Rollback Strategy**: Always have a way to roll back

### Example: Complete CI/CD Pipeline

```yaml
# cloudbuild.yaml - Complete CI/CD pipeline
steps:
  # Step 1: Run unit tests
  - name: 'gcr.io/cloud-builders/mvn'
    id: 'run-tests'
    args: ['test']

  # Step 2: Build application
  - name: 'gcr.io/cloud-builders/mvn'
    id: 'build-app'
    args: ['clean', 'package', '-DskipTests']
    waitFor: ['run-tests']

  # Step 3: Build Docker image
  - name: 'gcr.io/cloud-builders/docker'
    id: 'build-image'
    args: [
      'build',
      '-t', 'gcr.io/$PROJECT_ID/myapp:$SHORT_SHA',
      '-t', 'gcr.io/$PROJECT_ID/myapp:latest',
      '.'
    ]
    waitFor: ['build-app']

  # Step 4: Push to Container Registry
  - name: 'gcr.io/cloud-builders/docker'
    id: 'push-image'
    args: ['push', '--all-tags', 'gcr.io/$PROJECT_ID/myapp']
    waitFor: ['build-image']

  # Step 5: Deploy to staging
  - name: 'gcr.io/cloud-builders/gcloud'
    id: 'deploy-staging'
    args:
      - 'run'
      - 'deploy'
      - 'myapp-staging'
      - '--image=gcr.io/$PROJECT_ID/myapp:$SHORT_SHA'
      - '--region=us-central1'
      - '--platform=managed'
    waitFor: ['push-image']

  # Step 6: Run integration tests on staging
  - name: 'gcr.io/cloud-builders/curl'
    id: 'test-staging'
    args: ['-f', 'https://myapp-staging.example.com/health']
    waitFor: ['deploy-staging']

  # Step 7: Deploy to production (only on main branch)
  - name: 'gcr.io/cloud-builders/gcloud'
    id: 'deploy-production'
    args:
      - 'run'
      - 'deploy'
      - 'myapp-production'
      - '--image=gcr.io/$PROJECT_ID/myapp:$SHORT_SHA'
      - '--region=us-central1'
      - '--platform=managed'
    waitFor: ['test-staging']

# Specify images to be pushed to registry
images:
  - 'gcr.io/$PROJECT_ID/myapp:$SHORT_SHA'
  - 'gcr.io/$PROJECT_ID/myapp:latest'

# Build configuration
options:
  machineType: 'N1_HIGHCPU_8'
  logging: CLOUD_LOGGING_ONLY

timeout: '1200s'
```

---

## Infrastructure as Code (IaC)

Infrastructure as Code is the practice of managing and provisioning infrastructure through machine-readable definition files.

### Benefits of IaC

1. **Version Control**: Track infrastructure changes like application code
2. **Consistency**: Same configuration across all environments
3. **Automation**: Eliminate manual configuration errors
4. **Documentation**: Code serves as documentation
5. **Disaster Recovery**: Quickly rebuild infrastructure
6. **Scalability**: Easily replicate environments

### IaC Tools

| Tool | Type | Use Case |
|------|------|----------|
| Terraform | Declarative | Multi-cloud infrastructure |
| CloudFormation | Declarative | AWS-specific |
| Ansible | Imperative | Configuration management |
| Puppet | Declarative | Configuration management |
| Chef | Imperative | Configuration management |
| Pulumi | Imperative | Infrastructure using code |

### Terraform Example

```hcl
# main.tf - Provision GCP resources
provider "google" {
  project = "my-project-id"
  region  = "us-central1"
}

# Create VPC network
resource "google_compute_network" "vpc" {
  name                    = "my-vpc"
  auto_create_subnetworks = false
}

# Create subnet
resource "google_compute_subnetwork" "subnet" {
  name          = "my-subnet"
  ip_cidr_range = "10.0.0.0/24"
  region        = "us-central1"
  network       = google_compute_network.vpc.id
}

# Create GKE cluster
resource "google_container_cluster" "primary" {
  name     = "my-gke-cluster"
  location = "us-central1"

  # We can't create a cluster with no node pool
  remove_default_node_pool = true
  initial_node_count       = 1

  network    = google_compute_network.vpc.name
  subnetwork = google_compute_subnetwork.subnet.name
}

# Create managed node pool
resource "google_container_node_pool" "primary_nodes" {
  name       = "my-node-pool"
  location   = "us-central1"
  cluster    = google_container_cluster.primary.name
  node_count = 3

  node_config {
    machine_type = "e2-medium"
    disk_size_gb = 100

    oauth_scopes = [
      "https://www.googleapis.com/auth/cloud-platform"
    ]

    labels = {
      env = "production"
    }

    tags = ["gke-node", "production"]
  }

  autoscaling {
    min_node_count = 1
    max_node_count = 5
  }

  management {
    auto_repair  = true
    auto_upgrade = true
  }
}

# Output cluster endpoint
output "kubernetes_cluster_endpoint" {
  value       = google_container_cluster.primary.endpoint
  description = "GKE Cluster Endpoint"
}
```

### Using Terraform

```bash
# Initialize Terraform
terraform init

# Preview changes
terraform plan

# Apply changes
terraform apply

# Show current state
terraform show

# Destroy infrastructure
terraform destroy
```

### Deployment Descriptor Example

```yaml
# deployment.yaml - Kubernetes deployment configuration
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  labels:
    app: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: gcr.io/my-project/myapp:v1.2.3
        ports:
        - containerPort: 8080
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: url
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
  type: LoadBalancer
```

### Configuration Management with Ansible

```yaml
# playbook.yml - Configure web servers
---
- name: Configure web servers
  hosts: webservers
  become: yes

  vars:
    app_user: webapp
    app_dir: /opt/webapp

  tasks:
    - name: Install required packages
      apt:
        name:
          - nginx
          - python3
          - python3-pip
        state: present
        update_cache: yes

    - name: Create application user
      user:
        name: "{{ app_user }}"
        system: yes
        shell: /bin/bash

    - name: Create application directory
      file:
        path: "{{ app_dir }}"
        state: directory
        owner: "{{ app_user }}"
        group: "{{ app_user }}"
        mode: '0755'

    - name: Copy application files
      synchronize:
        src: ./app/
        dest: "{{ app_dir }}"
        delete: yes
      notify: restart application

    - name: Install Python dependencies
      pip:
        requirements: "{{ app_dir }}/requirements.txt"
        virtualenv: "{{ app_dir }}/venv"

    - name: Configure Nginx
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/sites-available/webapp
      notify: reload nginx

    - name: Enable Nginx site
      file:
        src: /etc/nginx/sites-available/webapp
        dest: /etc/nginx/sites-enabled/webapp
        state: link
      notify: reload nginx

  handlers:
    - name: restart application
      systemd:
        name: webapp
        state: restarted

    - name: reload nginx
      systemd:
        name: nginx
        state: reloaded
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| DevOps | Culture of collaboration between Dev and Ops teams |
| CI/CD | Automated pipeline: Source → Build → Test → Deploy |
| Infrastructure as Code | Manage infrastructure through version-controlled code |
| Key Practices | Automation, monitoring, continuous improvement |

## Next Topic

Continue to [GCP DevOps Tools](./02-gcp-devops-tools.md) to learn about Google Cloud Platform's DevOps offerings.
