# GCP Cloud Deploy

## What is Cloud Deploy?

Cloud Deploy is Google Cloud's fully managed continuous delivery service that automates deployment to Google Kubernetes Engine (GKE), Cloud Run, and Anthos. It provides progressive delivery patterns like canary deployments and blue-green deployments with built-in approval workflows.

### Key Features

- **Managed Delivery**: Fully managed deployment service
- **Progressive Delivery**: Canary, blue-green deployment strategies
- **Multi-environment**: Deploy to dev, staging, production
- **Approval Workflows**: Manual or automated approvals
- **Rollback**: Easy rollback to previous versions
- **Integration**: Works with Cloud Build, GKE, Cloud Run
- **Security**: Built-in security controls and attestations

### Cloud Deploy vs Traditional Deployment

| Feature | Cloud Deploy | Traditional |
|---------|--------------|-------------|
| Management | Fully managed | Self-managed |
| Deployment Strategies | Built-in canary, blue-green | Custom implementation |
| Approval Workflow | Native support | External tools |
| Rollback | One-click | Manual process |
| Multi-cluster | Native support | Complex setup |
| Visibility | Unified dashboard | Multiple tools |

### Cloud Deploy Architecture

```
┌─────────────────┐
│  Cloud Build    │  Build & Test
│  (CI Pipeline)  │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Artifact Registry│  Store Artifacts
└────────┬────────┘
         │
         ▼
┌─────────────────────────────────────────┐
│         Cloud Deploy Pipeline           │
├─────────────────────────────────────────┤
│  Dev → Staging → Production             │
│   ↓      ↓         ↓                    │
│  GKE    GKE       GKE Cluster(s)        │
└─────────────────────────────────────────┘
```

---

## Deployment Pipelines

A deployment pipeline defines the sequence of targets (environments) to which a release is delivered.

### Pipeline Components

1. **Pipeline**: Defines deployment stages (dev → staging → prod)
2. **Target**: Deployment destination (GKE cluster, Cloud Run)
3. **Release**: Specific version to deploy
4. **Rollout**: Actual deployment to a target

### Creating a Deployment Pipeline

#### Pipeline Configuration File

```yaml
# clouddeploy.yaml - Deployment pipeline definition
apiVersion: deploy.cloud.google.com/v1
kind: DeliveryPipeline
metadata:
  name: webapp-pipeline
description: Web application deployment pipeline
serialPipeline:
  stages:
    - targetId: dev
      profiles: [dev]
    - targetId: staging
      profiles: [staging]
      strategy:
        canary:
          runtimeConfig:
            cloudRun:
              automaticTrafficControl: true
          canaryDeployment:
            percentages: [25, 50, 75]
            verify: false
    - targetId: production
      profiles: [prod]
      strategy:
        canary:
          runtimeConfig:
            cloudRun:
              automaticTrafficControl: true
          canaryDeployment:
            percentages: [25, 50]
            verify: true
```

#### Target Definitions

```yaml
# dev-target.yaml
apiVersion: deploy.cloud.google.com/v1
kind: Target
metadata:
  name: dev
description: Development environment
run:
  location: projects/PROJECT_ID/locations/us-central1
---
# staging-target.yaml
apiVersion: deploy.cloud.google.com/v1
kind: Target
metadata:
  name: staging
description: Staging environment
requireApproval: false
run:
  location: projects/PROJECT_ID/locations/us-central1
---
# production-target.yaml
apiVersion: deploy.cloud.google.com/v1
kind: Target
metadata:
  name: production
description: Production environment
requireApproval: true
run:
  location: projects/PROJECT_ID/locations/us-central1
```

#### Creating Pipeline Resources

```bash
# Enable Cloud Deploy API
gcloud services enable clouddeploy.googleapis.com

# Create targets
gcloud deploy targets create dev \
    --type=run \
    --location=us-central1 \
    --project=PROJECT_ID \
    --description="Development environment"

gcloud deploy targets create staging \
    --type=run \
    --location=us-central1 \
    --project=PROJECT_ID \
    --require-approval=false \
    --description="Staging environment"

gcloud deploy targets create production \
    --type=run \
    --location=us-central1 \
    --project=PROJECT_ID \
    --require-approval=true \
    --description="Production environment"

# Create pipeline from configuration
gcloud deploy apply \
    --file=clouddeploy.yaml \
    --region=us-central1 \
    --project=PROJECT_ID

# List pipelines
gcloud deploy delivery-pipelines list \
    --region=us-central1

# Describe pipeline
gcloud deploy delivery-pipelines describe webapp-pipeline \
    --region=us-central1
```

### GKE Pipeline Example

```yaml
# clouddeploy.yaml - GKE deployment pipeline
apiVersion: deploy.cloud.google.com/v1
kind: DeliveryPipeline
metadata:
  name: gke-webapp-pipeline
description: GKE-based web application pipeline
serialPipeline:
  stages:
    - targetId: dev-cluster
      profiles: [dev]
    - targetId: staging-cluster
      profiles: [staging]
    - targetId: prod-cluster
      profiles: [prod]
      strategy:
        standard:
          verify: true
---
apiVersion: deploy.cloud.google.com/v1
kind: Target
metadata:
  name: dev-cluster
description: Development GKE cluster
gke:
  cluster: projects/PROJECT_ID/locations/us-central1/clusters/dev-cluster
---
apiVersion: deploy.cloud.google.com/v1
kind: Target
metadata:
  name: staging-cluster
description: Staging GKE cluster
requireApproval: false
gke:
  cluster: projects/PROJECT_ID/locations/us-central1/clusters/staging-cluster
---
apiVersion: deploy.cloud.google.com/v1
kind: Target
metadata:
  name: prod-cluster
description: Production GKE cluster
requireApproval: true
gke:
  cluster: projects/PROJECT_ID/locations/us-central1/clusters/prod-cluster
```

---

## Delivery Pipelines

### Creating Releases

A release is created from your application artifacts and moves through the pipeline.

```bash
# Create a release
gcloud deploy releases create release-001 \
    --delivery-pipeline=webapp-pipeline \
    --region=us-central1 \
    --images=webapp=gcr.io/PROJECT_ID/webapp:v1.0.0

# Create release with skaffold config
gcloud deploy releases create release-002 \
    --delivery-pipeline=webapp-pipeline \
    --region=us-central1 \
    --skaffold-file=skaffold.yaml \
    --images=webapp=gcr.io/PROJECT_ID/webapp:v1.1.0

# List releases
gcloud deploy releases list \
    --delivery-pipeline=webapp-pipeline \
    --region=us-central1

# Describe a release
gcloud deploy releases describe release-001 \
    --delivery-pipeline=webapp-pipeline \
    --region=us-central1
```

### Skaffold Configuration

Cloud Deploy uses Skaffold to render and deploy Kubernetes manifests.

```yaml
# skaffold.yaml - Skaffold configuration
apiVersion: skaffold/v4beta6
kind: Config
metadata:
  name: webapp
profiles:
  - name: dev
    manifests:
      kustomize:
        paths:
          - k8s/overlays/dev
  - name: staging
    manifests:
      kustomize:
        paths:
          - k8s/overlays/staging
  - name: prod
    manifests:
      kustomize:
        paths:
          - k8s/overlays/prod
deploy:
  kubectl: {}
```

### Kustomize Configuration

```yaml
# k8s/base/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
        - name: webapp
          image: webapp
          ports:
            - containerPort: 8080
          resources:
            requests:
              memory: "256Mi"
              cpu: "250m"
            limits:
              memory: "512Mi"
              cpu: "500m"
---
# k8s/base/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: webapp
spec:
  selector:
    app: webapp
  ports:
    - port: 80
      targetPort: 8080
  type: LoadBalancer
---
# k8s/base/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - deployment.yaml
  - service.yaml
---
# k8s/overlays/dev/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: dev
commonLabels:
  environment: dev
resources:
  - ../../base
replicas:
  - name: webapp
    count: 1
---
# k8s/overlays/staging/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: staging
commonLabels:
  environment: staging
resources:
  - ../../base
replicas:
  - name: webapp
    count: 2
---
# k8s/overlays/prod/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: production
commonLabels:
  environment: production
resources:
  - ../../base
replicas:
  - name: webapp
    count: 5
```

### Integrated CI/CD Pipeline

```yaml
# cloudbuild.yaml - Build and create release
steps:
  # Step 1: Run tests
  - name: 'gcr.io/cloud-builders/mvn'
    args: ['test']

  # Step 2: Build application
  - name: 'gcr.io/cloud-builders/mvn'
    args: ['package', '-DskipTests']

  # Step 3: Build Docker image
  - name: 'gcr.io/cloud-builders/docker'
    args: [
      'build',
      '-t', 'gcr.io/$PROJECT_ID/webapp:$SHORT_SHA',
      '.'
    ]

  # Step 4: Push to Container Registry
  - name: 'gcr.io/cloud-builders/docker'
    args: ['push', 'gcr.io/$PROJECT_ID/webapp:$SHORT_SHA']

  # Step 5: Create Cloud Deploy release
  - name: 'gcr.io/google.com/cloudsdktool/cloud-sdk'
    entrypoint: 'gcloud'
    args:
      - 'deploy'
      - 'releases'
      - 'create'
      - 'release-${SHORT_SHA}'
      - '--delivery-pipeline=webapp-pipeline'
      - '--region=us-central1'
      - '--images=webapp=gcr.io/$PROJECT_ID/webapp:$SHORT_SHA'
      - '--skaffold-file=skaffold.yaml'

images:
  - 'gcr.io/$PROJECT_ID/webapp:$SHORT_SHA'

options:
  machineType: 'N1_HIGHCPU_8'

timeout: '1200s'
```

---

## Rollout Management

### Understanding Rollouts

A rollout is the deployment of a release to a specific target. Cloud Deploy manages rollouts automatically as releases progress through the pipeline.

### Monitoring Rollouts

```bash
# List rollouts for a release
gcloud deploy rollouts list \
    --delivery-pipeline=webapp-pipeline \
    --release=release-001 \
    --region=us-central1

# Describe a rollout
gcloud deploy rollouts describe rollout-001 \
    --delivery-pipeline=webapp-pipeline \
    --release=release-001 \
    --region=us-central1

# Get rollout status
gcloud deploy rollouts describe rollout-001 \
    --delivery-pipeline=webapp-pipeline \
    --release=release-001 \
    --region=us-central1 \
    --format="value(state)"
```

### Advancing Rollouts

For canary deployments, manually advance through phases.

```bash
# Advance to next canary phase
gcloud deploy rollouts advance rollout-001 \
    --delivery-pipeline=webapp-pipeline \
    --release=release-001 \
    --region=us-central1

# Promote to stable
gcloud deploy rollouts promote rollout-001 \
    --delivery-pipeline=webapp-pipeline \
    --release=release-001 \
    --region=us-central1
```

### Approving Rollouts

When a target requires approval, manually approve the rollout.

```bash
# Approve a rollout
gcloud deploy rollouts approve rollout-001 \
    --delivery-pipeline=webapp-pipeline \
    --release=release-001 \
    --region=us-central1

# Reject a rollout
gcloud deploy rollouts reject rollout-001 \
    --delivery-pipeline=webapp-pipeline \
    --release=release-001 \
    --region=us-central1 \
    --reason="Failed validation tests"

# List pending approvals
gcloud deploy rollouts list \
    --delivery-pipeline=webapp-pipeline \
    --region=us-central1 \
    --filter="state=PENDING_APPROVAL"
```

### Deployment Strategies

#### Standard Deployment

Deploy all traffic to the new version at once.

```yaml
apiVersion: deploy.cloud.google.com/v1
kind: DeliveryPipeline
metadata:
  name: standard-pipeline
serialPipeline:
  stages:
    - targetId: production
      profiles: [prod]
      strategy:
        standard:
          verify: true
          predeploy:
            actions: ["predeploy-job"]
          postdeploy:
            actions: ["smoke-test"]
```

#### Canary Deployment

Gradually increase traffic to the new version.

```yaml
apiVersion: deploy.cloud.google.com/v1
kind: DeliveryPipeline
metadata:
  name: canary-pipeline
serialPipeline:
  stages:
    - targetId: production
      profiles: [prod]
      strategy:
        canary:
          runtimeConfig:
            cloudRun:
              automaticTrafficControl: true
          canaryDeployment:
            percentages: [10, 25, 50, 75]
            verify: true
          customCanaryDeployment:
            phaseConfigs:
              - phaseId: "stable-10"
                percentage: 10
                verify: true
                predeploy:
                  actions: ["notify-team"]
                postdeploy:
                  actions: ["run-tests"]
              - phaseId: "stable-25"
                percentage: 25
                verify: true
              - phaseId: "stable-50"
                percentage: 50
                verify: true
              - phaseId: "stable"
                percentage: 100
                verify: true
```

#### Blue-Green Deployment

Deploy to a separate environment, then switch traffic.

```yaml
apiVersion: deploy.cloud.google.com/v1
kind: DeliveryPipeline
metadata:
  name: blue-green-pipeline
serialPipeline:
  stages:
    - targetId: production
      profiles: [prod]
      strategy:
        standard:
          verify: true
          postdeploy:
            actions: ["switch-traffic"]
```

### Verification Jobs

Run automated checks before advancing deployments.

```yaml
# verification.yaml - Verification job
apiVersion: v1
kind: Job
metadata:
  name: verify-deployment
spec:
  template:
    spec:
      containers:
        - name: verify
          image: gcr.io/PROJECT_ID/verify:latest
          command:
            - /bin/sh
            - -c
            - |
              # Run health checks
              curl -f http://webapp-service/health || exit 1

              # Run smoke tests
              npm run smoke-test || exit 1

              # Check metrics
              python check_metrics.py || exit 1

              echo "Verification successful"
      restartPolicy: Never
  backoffLimit: 3
```

---

## Automated Deployment

### Automation Configuration

```yaml
# clouddeploy.yaml - Fully automated pipeline
apiVersion: deploy.cloud.google.com/v1
kind: DeliveryPipeline
metadata:
  name: automated-pipeline
description: Fully automated deployment pipeline
serialPipeline:
  stages:
    - targetId: dev
      profiles: [dev]
      # Automatic promotion to next stage
    - targetId: staging
      profiles: [staging]
      strategy:
        canary:
          runtimeConfig:
            cloudRun:
              automaticTrafficControl: true
          canaryDeployment:
            percentages: [50]
            verify: true
      # Auto-advance after verification
    - targetId: production
      profiles: [prod]
      requireApproval: false  # Automated approval
      strategy:
        canary:
          runtimeConfig:
            cloudRun:
              automaticTrafficControl: true
          canaryDeployment:
            percentages: [25, 50, 75]
            verify: true
```

### Automated Rollback

Configure automatic rollback on failure.

```yaml
# rollback-config.yaml
apiVersion: deploy.cloud.google.com/v1
kind: DeliveryPipeline
metadata:
  name: rollback-pipeline
serialPipeline:
  stages:
    - targetId: production
      profiles: [prod]
      strategy:
        canary:
          runtimeConfig:
            cloudRun:
              automaticTrafficControl: true
          canaryDeployment:
            percentages: [25, 50]
            verify: true
            # Automatic rollback on verification failure
            automaticRollback:
              enabled: true
              onFailure: true
```

### Complete End-to-End Example

```yaml
# Project structure
project/
├── cloudbuild.yaml          # CI pipeline
├── clouddeploy.yaml         # CD pipeline
├── skaffold.yaml            # Deployment config
├── Dockerfile
├── k8s/
│   ├── base/
│   │   ├── deployment.yaml
│   │   ├── service.yaml
│   │   └── kustomization.yaml
│   └── overlays/
│       ├── dev/
│       ├── staging/
│       └── prod/
└── src/
    └── main/
```

```yaml
# cloudbuild.yaml - Complete CI/CD pipeline
steps:
  # Build and test
  - name: 'gcr.io/cloud-builders/mvn'
    args: ['clean', 'test']

  - name: 'gcr.io/cloud-builders/mvn'
    args: ['package', '-DskipTests']

  # Build container
  - name: 'gcr.io/cloud-builders/docker'
    args: [
      'build',
      '-t', 'gcr.io/$PROJECT_ID/webapp:$SHORT_SHA',
      '-t', 'gcr.io/$PROJECT_ID/webapp:latest',
      '.'
    ]

  # Push container
  - name: 'gcr.io/cloud-builders/docker'
    args: ['push', '--all-tags', 'gcr.io/$PROJECT_ID/webapp']

  # Create Cloud Deploy release
  - name: 'gcr.io/google.com/cloudsdktool/cloud-sdk'
    entrypoint: 'gcloud'
    args:
      - 'deploy'
      - 'releases'
      - 'create'
      - 'rel-${SHORT_SHA}'
      - '--delivery-pipeline=webapp-pipeline'
      - '--region=us-central1'
      - '--images=webapp=gcr.io/$PROJECT_ID/webapp:$SHORT_SHA'
      - '--skaffold-file=skaffold.yaml'
      - '--annotations=commit-sha=$COMMIT_SHA,branch=$BRANCH_NAME'

images:
  - 'gcr.io/$PROJECT_ID/webapp:$SHORT_SHA'
  - 'gcr.io/$PROJECT_ID/webapp:latest'

substitutions:
  _REGION: us-central1

options:
  machineType: 'N1_HIGHCPU_8'
  logging: 'CLOUD_LOGGING_ONLY'

timeout: '1800s'
```

```yaml
# clouddeploy.yaml - Deployment pipeline
apiVersion: deploy.cloud.google.com/v1
kind: DeliveryPipeline
metadata:
  name: webapp-pipeline
description: Web application delivery pipeline
serialPipeline:
  stages:
    # Development environment
    - targetId: dev
      profiles: [dev]

    # Staging with canary
    - targetId: staging
      profiles: [staging]
      strategy:
        canary:
          runtimeConfig:
            kubernetes:
              serviceNetworking:
                service: webapp
                deployment: webapp
          canaryDeployment:
            percentages: [50]
            verify: true

    # Production with full canary and approval
    - targetId: production
      profiles: [prod]
      requireApproval: true
      strategy:
        canary:
          runtimeConfig:
            kubernetes:
              serviceNetworking:
                service: webapp
                deployment: webapp
          canaryDeployment:
            percentages: [25, 50, 75]
            verify: true
---
apiVersion: deploy.cloud.google.com/v1
kind: Target
metadata:
  name: dev
description: Development GKE cluster
gke:
  cluster: projects/PROJECT_ID/locations/us-central1/clusters/dev-cluster
---
apiVersion: deploy.cloud.google.com/v1
kind: Target
metadata:
  name: staging
description: Staging GKE cluster
requireApproval: false
gke:
  cluster: projects/PROJECT_ID/locations/us-central1/clusters/staging-cluster
---
apiVersion: deploy.cloud.google.com/v1
kind: Target
metadata:
  name: production
description: Production GKE cluster
requireApproval: true
gke:
  cluster: projects/PROJECT_ID/locations/us-central1/clusters/prod-cluster
```

### Notification Configuration

Configure notifications for deployment events.

```bash
# Create Pub/Sub topic for notifications
gcloud pubsub topics create cloud-deploy-notifications

# Subscribe to notifications
gcloud pubsub subscriptions create deploy-alerts \
    --topic=cloud-deploy-notifications

# Configure pipeline to publish notifications
gcloud deploy delivery-pipelines update webapp-pipeline \
    --region=us-central1 \
    --notifications=projects/PROJECT_ID/topics/cloud-deploy-notifications
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Cloud Deploy | Managed continuous delivery for GKE and Cloud Run |
| Delivery Pipeline | Define deployment stages: dev → staging → prod |
| Rollout Management | Track and control deployments to each target |
| Deployment Strategies | Standard, canary, blue-green deployments |
| Automation | Automated progression with approval workflows |

## Next Topic

Continue to [Build Pipelines](./05-build-pipelines.md) to learn about advanced pipeline configurations and testing strategies.
