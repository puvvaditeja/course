# Build Pipelines

## GCP Cloud Build Pipelines

Cloud Build Pipelines provide a way to orchestrate complex build and deployment workflows with multiple stages, parallel execution, and conditional logic.

### Pipeline Concepts

A build pipeline consists of:

1. **Steps**: Individual build tasks
2. **Stages**: Logical grouping of steps
3. **Dependencies**: Execution order between steps
4. **Artifacts**: Outputs from build steps
5. **Conditions**: Conditional execution logic

### Pipeline Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Build Pipeline                          │
├─────────────────────────────────────────────────────────────┤
│  Stage 1: Prepare    │  Stage 2: Build   │  Stage 3: Test  │
│  ┌──────────────┐    │  ┌──────────────┐ │ ┌──────────────┐│
│  │ Checkout     │    │  │ Compile      │ │ │ Unit Tests   ││
│  └──────┬───────┘    │  └──────┬───────┘ │ └──────┬───────┘│
│         │            │         │         │        │        │
│  ┌──────▼───────┐    │  ┌──────▼───────┐ │ ┌──────▼───────┐│
│  │ Dependencies │    │  │ Package      │ │ │ Integration  ││
│  └──────────────┘    │  └──────────────┘ │ │ Tests        ││
│                      │                   │ └──────────────┘│
├─────────────────────────────────────────────────────────────┤
│  Stage 4: Security   │  Stage 5: Deploy                     │
│  ┌──────────────┐    │  ┌──────────────┐  ┌──────────────┐ │
│  │ Scan Image   │    │  │ Deploy Staging│ │Deploy Prod   │ │
│  └──────────────┘    │  └──────────────┘  └──────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

---

## Pipeline as Code

Define entire pipelines in YAML configuration files stored in version control.

### Basic Pipeline Structure

```yaml
# cloudbuild.yaml - Basic pipeline structure
steps:
  # Step with explicit ID
  - id: 'prepare'
    name: 'gcr.io/cloud-builders/git'
    args: ['clone', 'https://github.com/user/repo.git']

  # Step with dependency
  - id: 'build'
    name: 'gcr.io/cloud-builders/mvn'
    args: ['package']
    waitFor: ['prepare']

  # Step with multiple dependencies
  - id: 'test'
    name: 'gcr.io/cloud-builders/mvn'
    args: ['test']
    waitFor: ['build']

  # Final step
  - id: 'deploy'
    name: 'gcr.io/cloud-builders/gcloud'
    args: ['app', 'deploy']
    waitFor: ['test']
```

### Step Dependencies

Control execution order with `waitFor`.

```yaml
steps:
  # Steps run in parallel (no waitFor)
  - id: 'lint-frontend'
    name: 'gcr.io/cloud-builders/npm'
    args: ['run', 'lint']
    dir: 'frontend'

  - id: 'lint-backend'
    name: 'gcr.io/cloud-builders/mvn'
    args: ['checkstyle:check']
    dir: 'backend'

  # Wait for all linting to complete
  - id: 'build-frontend'
    name: 'gcr.io/cloud-builders/npm'
    args: ['run', 'build']
    dir: 'frontend'
    waitFor: ['lint-frontend']

  - id: 'build-backend'
    name: 'gcr.io/cloud-builders/mvn'
    args: ['package', '-DskipTests']
    dir: 'backend'
    waitFor: ['lint-backend']

  # Wait for all builds
  - id: 'integration-test'
    name: 'gcr.io/cloud-builders/npm'
    args: ['run', 'test:integration']
    waitFor: ['build-frontend', 'build-backend']
```

### Dynamic Configuration

Use substitutions for dynamic pipeline behavior.

```yaml
# cloudbuild.yaml - Dynamic configuration
substitutions:
  _ENVIRONMENT: ${_ENVIRONMENT:-dev}
  _REGION: ${_REGION:-us-central1}
  _IMAGE_TAG: ${SHORT_SHA}
  _DEPLOY_ENABLED: 'true'

steps:
  # Build with dynamic tags
  - id: 'build'
    name: 'gcr.io/cloud-builders/docker'
    args: [
      'build',
      '-t', 'gcr.io/$PROJECT_ID/app:${_IMAGE_TAG}',
      '-t', 'gcr.io/$PROJECT_ID/app:${_ENVIRONMENT}-latest',
      '--build-arg', 'ENVIRONMENT=${_ENVIRONMENT}',
      '.'
    ]

  # Conditional deployment
  - id: 'deploy'
    name: 'bash'
    args:
      - '-c'
      - |
        if [ "${_DEPLOY_ENABLED}" = "true" ]; then
          gcloud run deploy app \
            --image=gcr.io/$PROJECT_ID/app:${_IMAGE_TAG} \
            --region=${_REGION} \
            --platform=managed
        else
          echo "Deployment skipped"
        fi
    waitFor: ['build']

# Override substitutions via CLI:
# gcloud builds submit --substitutions=_ENVIRONMENT=prod,_REGION=us-east1
```

### Reusable Pipeline Templates

```yaml
# templates/build-template.yaml
steps:
  - id: 'install-dependencies'
    name: 'gcr.io/cloud-builders/npm'
    args: ['ci']

  - id: 'run-tests'
    name: 'gcr.io/cloud-builders/npm'
    args: ['test']
    waitFor: ['install-dependencies']

  - id: 'build-app'
    name: 'gcr.io/cloud-builders/npm'
    args: ['run', 'build']
    waitFor: ['run-tests']

  - id: 'build-image'
    name: 'gcr.io/cloud-builders/docker'
    args: [
      'build',
      '-t', '${_IMAGE_URL}:${_TAG}',
      '.'
    ]
    waitFor: ['build-app']

# Use template with different configurations
# Project A:
# gcloud builds submit --config=templates/build-template.yaml \
#   --substitutions=_IMAGE_URL=gcr.io/project-a/app,_TAG=v1.0.0
```

---

## Multi-Stage Pipelines

Organize complex builds into logical stages.

### Stage Organization

```yaml
# cloudbuild.yaml - Multi-stage pipeline
steps:
  # ============ STAGE 1: VALIDATION ============
  - id: 'validate-yaml'
    name: 'gcr.io/cloud-builders/gcloud'
    args: ['app', 'validate', 'app.yaml']

  - id: 'validate-dockerfile'
    name: 'gcr.io/kaniko-project/executor:latest'
    args: ['--no-push', '--dockerfile=Dockerfile']

  # ============ STAGE 2: CODE QUALITY ============
  - id: 'lint-code'
    name: 'gcr.io/cloud-builders/npm'
    args: ['run', 'lint']
    waitFor: ['validate-yaml', 'validate-dockerfile']

  - id: 'security-scan'
    name: 'aquasec/trivy'
    args: ['filesystem', '--severity', 'HIGH,CRITICAL', '.']
    waitFor: ['validate-yaml', 'validate-dockerfile']

  # ============ STAGE 3: BUILD ============
  - id: 'install-deps'
    name: 'gcr.io/cloud-builders/npm'
    args: ['ci']
    waitFor: ['lint-code', 'security-scan']

  - id: 'build-app'
    name: 'gcr.io/cloud-builders/npm'
    args: ['run', 'build']
    waitFor: ['install-deps']

  # ============ STAGE 4: TEST ============
  - id: 'unit-tests'
    name: 'gcr.io/cloud-builders/npm'
    args: ['run', 'test:unit']
    env:
      - 'CI=true'
    waitFor: ['build-app']

  - id: 'integration-tests'
    name: 'gcr.io/cloud-builders/npm'
    args: ['run', 'test:integration']
    waitFor: ['build-app']

  - id: 'e2e-tests'
    name: 'cypress/included:latest'
    args: ['run']
    waitFor: ['build-app']

  # ============ STAGE 5: CONTAINERIZE ============
  - id: 'build-image'
    name: 'gcr.io/cloud-builders/docker'
    args: [
      'build',
      '-t', 'gcr.io/$PROJECT_ID/app:$SHORT_SHA',
      '-t', 'gcr.io/$PROJECT_ID/app:latest',
      '.'
    ]
    waitFor: ['unit-tests', 'integration-tests', 'e2e-tests']

  - id: 'scan-image'
    name: 'aquasec/trivy'
    args: [
      'image',
      '--severity', 'HIGH,CRITICAL',
      '--exit-code', '1',
      'gcr.io/$PROJECT_ID/app:$SHORT_SHA'
    ]
    waitFor: ['build-image']

  # ============ STAGE 6: PUBLISH ============
  - id: 'push-image'
    name: 'gcr.io/cloud-builders/docker'
    args: ['push', '--all-tags', 'gcr.io/$PROJECT_ID/app']
    waitFor: ['scan-image']

  # ============ STAGE 7: DEPLOY ============
  - id: 'deploy-staging'
    name: 'gcr.io/cloud-builders/gcloud'
    args: [
      'run', 'deploy', 'app-staging',
      '--image=gcr.io/$PROJECT_ID/app:$SHORT_SHA',
      '--region=us-central1',
      '--platform=managed'
    ]
    waitFor: ['push-image']

  - id: 'smoke-test-staging'
    name: 'curlimages/curl'
    args: ['-f', 'https://app-staging.example.com/health']
    waitFor: ['deploy-staging']

  - id: 'deploy-production'
    name: 'gcr.io/cloud-builders/gcloud'
    args: [
      'run', 'deploy', 'app-production',
      '--image=gcr.io/$PROJECT_ID/app:$SHORT_SHA',
      '--region=us-central1',
      '--platform=managed'
    ]
    waitFor: ['smoke-test-staging']

images:
  - 'gcr.io/$PROJECT_ID/app:$SHORT_SHA'
  - 'gcr.io/$PROJECT_ID/app:latest'

options:
  machineType: 'N1_HIGHCPU_8'
  logging: 'CLOUD_LOGGING_ONLY'

timeout: '3600s'
```

### Parallel Execution

Run independent tasks in parallel to reduce build time.

```yaml
# cloudbuild.yaml - Parallel execution
steps:
  # ============ PARALLEL: BUILD SERVICES ============
  - id: 'build-frontend'
    name: 'gcr.io/cloud-builders/npm'
    args: ['run', 'build']
    dir: 'frontend'
    # No waitFor - runs immediately

  - id: 'build-backend'
    name: 'gcr.io/cloud-builders/mvn'
    args: ['package', '-DskipTests']
    dir: 'backend'
    # No waitFor - runs in parallel with frontend

  - id: 'build-worker'
    name: 'gcr.io/cloud-builders/go'
    args: ['build', '-o', 'worker', './cmd/worker']
    dir: 'worker'
    # No waitFor - runs in parallel

  # ============ PARALLEL: BUILD IMAGES ============
  - id: 'image-frontend'
    name: 'gcr.io/cloud-builders/docker'
    args: [
      'build',
      '-t', 'gcr.io/$PROJECT_ID/frontend:$SHORT_SHA',
      './frontend'
    ]
    waitFor: ['build-frontend']

  - id: 'image-backend'
    name: 'gcr.io/cloud-builders/docker'
    args: [
      'build',
      '-t', 'gcr.io/$PROJECT_ID/backend:$SHORT_SHA',
      './backend'
    ]
    waitFor: ['build-backend']

  - id: 'image-worker'
    name: 'gcr.io/cloud-builders/docker'
    args: [
      'build',
      '-t', 'gcr.io/$PROJECT_ID/worker:$SHORT_SHA',
      './worker'
    ]
    waitFor: ['build-worker']

  # ============ PARALLEL: PUSH IMAGES ============
  - id: 'push-frontend'
    name: 'gcr.io/cloud-builders/docker'
    args: ['push', 'gcr.io/$PROJECT_ID/frontend:$SHORT_SHA']
    waitFor: ['image-frontend']

  - id: 'push-backend'
    name: 'gcr.io/cloud-builders/docker'
    args: ['push', 'gcr.io/$PROJECT_ID/backend:$SHORT_SHA']
    waitFor: ['image-backend']

  - id: 'push-worker'
    name: 'gcr.io/cloud-builders/docker'
    args: ['push', 'gcr.io/$PROJECT_ID/worker:$SHORT_SHA']
    waitFor: ['image-worker']

  # ============ SEQUENTIAL: DEPLOY ============
  - id: 'deploy-all'
    name: 'gcr.io/cloud-builders/kubectl'
    args: ['apply', '-f', 'k8s/']
    waitFor: ['push-frontend', 'push-backend', 'push-worker']

images:
  - 'gcr.io/$PROJECT_ID/frontend:$SHORT_SHA'
  - 'gcr.io/$PROJECT_ID/backend:$SHORT_SHA'
  - 'gcr.io/$PROJECT_ID/worker:$SHORT_SHA'
```

---

## Testing in Pipelines

Integrate various testing strategies into build pipelines.

### Unit Testing

```yaml
# cloudbuild.yaml - Unit testing
steps:
  # Java unit tests
  - id: 'test-java-unit'
    name: 'gcr.io/cloud-builders/mvn'
    args: ['test']
    env:
      - 'SPRING_PROFILES_ACTIVE=test'

  # JavaScript unit tests
  - id: 'test-js-unit'
    name: 'gcr.io/cloud-builders/npm'
    args: ['run', 'test:unit']
    env:
      - 'CI=true'
      - 'NODE_ENV=test'

  # Python unit tests
  - id: 'test-python-unit'
    name: 'python:3.9'
    entrypoint: 'pytest'
    args: [
      'tests/unit/',
      '--cov=src/',
      '--cov-report=xml',
      '--junitxml=test-results.xml'
    ]

  # Generate test coverage report
  - id: 'coverage-report'
    name: 'gcr.io/cloud-builders/gsutil'
    args: [
      'cp',
      'coverage.xml',
      'gs://$PROJECT_ID-test-reports/coverage-$BUILD_ID.xml'
    ]
    waitFor: ['test-python-unit']
```

### Integration Testing

```yaml
# cloudbuild.yaml - Integration testing
steps:
  # Start test dependencies
  - id: 'start-test-db'
    name: 'docker/compose:latest'
    args: ['up', '-d', 'postgres', 'redis']

  # Wait for services to be ready
  - id: 'wait-for-services'
    name: 'bash'
    args:
      - '-c'
      - |
        until pg_isready -h localhost -p 5432; do
          echo "Waiting for PostgreSQL..."
          sleep 2
        done
        echo "PostgreSQL is ready"
    waitFor: ['start-test-db']

  # Run integration tests
  - id: 'integration-tests'
    name: 'gcr.io/cloud-builders/mvn'
    args: [
      'verify',
      '-Dspring.profiles.active=integration-test',
      '-Dspring.datasource.url=jdbc:postgresql://localhost:5432/testdb'
    ]
    waitFor: ['wait-for-services']

  # Cleanup
  - id: 'cleanup-test-services'
    name: 'docker/compose:latest'
    args: ['down', '-v']
    waitFor: ['integration-tests']
```

### End-to-End Testing

```yaml
# cloudbuild.yaml - E2E testing
steps:
  # Build application
  - id: 'build-app'
    name: 'gcr.io/cloud-builders/npm'
    args: ['run', 'build']

  # Build and start application container
  - id: 'start-app'
    name: 'gcr.io/cloud-builders/docker'
    args: [
      'run', '-d',
      '--name', 'app-under-test',
      '-p', '8080:8080',
      '--network', 'cloudbuild',
      'gcr.io/$PROJECT_ID/app:$SHORT_SHA'
    ]
    waitFor: ['build-app']

  # Wait for application to be ready
  - id: 'wait-for-app'
    name: 'curlimages/curl'
    args:
      - 'sh'
      - '-c'
      - |
        for i in {1..30}; do
          if curl -f http://app-under-test:8080/health; then
            echo "Application is ready"
            exit 0
          fi
          echo "Waiting for application... ($i/30)"
          sleep 2
        done
        exit 1
    waitFor: ['start-app']

  # Run Cypress E2E tests
  - id: 'e2e-tests'
    name: 'cypress/included:latest'
    args: [
      'run',
      '--config', 'baseUrl=http://app-under-test:8080'
    ]
    env:
      - 'CYPRESS_baseUrl=http://app-under-test:8080'
    waitFor: ['wait-for-app']

  # Capture test videos
  - id: 'upload-test-artifacts'
    name: 'gcr.io/cloud-builders/gsutil'
    args: [
      '-m', 'cp', '-r',
      'cypress/videos/',
      'gs://$PROJECT_ID-test-artifacts/$BUILD_ID/'
    ]
    waitFor: ['e2e-tests']

  # Stop application
  - id: 'stop-app'
    name: 'gcr.io/cloud-builders/docker'
    args: ['stop', 'app-under-test']
    waitFor: ['e2e-tests']
```

### Performance Testing

```yaml
# cloudbuild.yaml - Performance testing
steps:
  # Deploy to test environment
  - id: 'deploy-test-env'
    name: 'gcr.io/cloud-builders/gcloud'
    args: [
      'run', 'deploy', 'app-perf-test',
      '--image=gcr.io/$PROJECT_ID/app:$SHORT_SHA',
      '--region=us-central1',
      '--platform=managed',
      '--cpu=2',
      '--memory=2Gi'
    ]

  # Run load tests with k6
  - id: 'load-test'
    name: 'grafana/k6'
    args: [
      'run',
      '--vus', '50',
      '--duration', '5m',
      '--out', 'json=loadtest-results.json',
      'tests/load/script.js'
    ]
    env:
      - 'BASE_URL=https://app-perf-test.example.com'
    waitFor: ['deploy-test-env']

  # Analyze results
  - id: 'analyze-performance'
    name: 'python:3.9'
    args:
      - 'python'
      - 'scripts/analyze-performance.py'
      - 'loadtest-results.json'
    waitFor: ['load-test']

  # Cleanup test environment
  - id: 'cleanup-test-env'
    name: 'gcr.io/cloud-builders/gcloud'
    args: [
      'run', 'services', 'delete', 'app-perf-test',
      '--region=us-central1',
      '--quiet'
    ]
    waitFor: ['analyze-performance']
```

### Security Testing

```yaml
# cloudbuild.yaml - Security testing
steps:
  # SAST - Static Application Security Testing
  - id: 'sast-scan'
    name: 'returntocorp/semgrep'
    args: [
      '--config=auto',
      '--json',
      '--output=sast-results.json'
    ]

  # Dependency vulnerability scanning
  - id: 'dependency-scan'
    name: 'gcr.io/cloud-builders/npm'
    args: ['audit', '--audit-level=high']

  # Build container
  - id: 'build-image'
    name: 'gcr.io/cloud-builders/docker'
    args: [
      'build',
      '-t', 'gcr.io/$PROJECT_ID/app:$SHORT_SHA',
      '.'
    ]
    waitFor: ['sast-scan', 'dependency-scan']

  # Container image vulnerability scanning
  - id: 'container-scan'
    name: 'aquasec/trivy'
    args: [
      'image',
      '--severity', 'CRITICAL,HIGH',
      '--exit-code', '1',
      '--format', 'json',
      '--output', 'trivy-results.json',
      'gcr.io/$PROJECT_ID/app:$SHORT_SHA'
    ]
    waitFor: ['build-image']

  # DAST - Dynamic Application Security Testing
  - id: 'dast-scan'
    name: 'owasp/zap2docker-stable'
    args: [
      'zap-baseline.py',
      '-t', 'https://app-test.example.com',
      '-r', 'dast-report.html'
    ]
    waitFor: ['container-scan']

  # Upload security reports
  - id: 'upload-security-reports'
    name: 'gcr.io/cloud-builders/gsutil'
    args: [
      '-m', 'cp',
      '*.json',
      'dast-report.html',
      'gs://$PROJECT_ID-security-reports/$BUILD_ID/'
    ]
    waitFor: ['dast-scan']
```

---

## Deployment Automation

Automate deployment across multiple environments.

### Multi-Environment Deployment

```yaml
# cloudbuild.yaml - Multi-environment deployment
substitutions:
  _ENV: ${_ENV:-dev}
  _REGION: us-central1

steps:
  # Build and push image
  - id: 'build-push'
    name: 'gcr.io/cloud-builders/docker'
    args: [
      'build',
      '-t', 'gcr.io/$PROJECT_ID/app:$SHORT_SHA',
      '-t', 'gcr.io/$PROJECT_ID/app:${_ENV}-latest',
      '.'
    ]

  - id: 'push-image'
    name: 'gcr.io/cloud-builders/docker'
    args: ['push', '--all-tags', 'gcr.io/$PROJECT_ID/app']
    waitFor: ['build-push']

  # Deploy to environment
  - id: 'deploy-app'
    name: 'gcr.io/cloud-builders/gcloud'
    args: [
      'run', 'deploy', 'app-${_ENV}',
      '--image=gcr.io/$PROJECT_ID/app:$SHORT_SHA',
      '--region=${_REGION}',
      '--platform=managed',
      '--set-env-vars=ENVIRONMENT=${_ENV}'
    ]
    waitFor: ['push-image']

  # Run smoke tests
  - id: 'smoke-test'
    name: 'bash'
    args:
      - '-c'
      - |
        APP_URL=$(gcloud run services describe app-${_ENV} \
          --region=${_REGION} \
          --format='value(status.url)')
        curl -f $APP_URL/health || exit 1
    waitFor: ['deploy-app']

images:
  - 'gcr.io/$PROJECT_ID/app:$SHORT_SHA'
  - 'gcr.io/$PROJECT_ID/app:${_ENV}-latest'

# Deploy to dev:
# gcloud builds submit --substitutions=_ENV=dev

# Deploy to staging:
# gcloud builds submit --substitutions=_ENV=staging

# Deploy to production:
# gcloud builds submit --substitutions=_ENV=production
```

### Blue-Green Deployment

```yaml
# cloudbuild.yaml - Blue-green deployment
steps:
  # Deploy new version (green)
  - id: 'deploy-green'
    name: 'gcr.io/cloud-builders/gcloud'
    args: [
      'run', 'deploy', 'app-green',
      '--image=gcr.io/$PROJECT_ID/app:$SHORT_SHA',
      '--region=us-central1',
      '--no-traffic',
      '--tag=green'
    ]

  # Test green deployment
  - id: 'test-green'
    name: 'bash'
    args:
      - '-c'
      - |
        GREEN_URL=$(gcloud run services describe app-green \
          --region=us-central1 \
          --format='value(status.traffic[0].url)')

        # Run comprehensive tests
        curl -f $GREEN_URL/health
        curl -f $GREEN_URL/api/status

        # Run integration tests against green
        npm run test:integration -- --baseUrl=$GREEN_URL
    waitFor: ['deploy-green']

  # Switch traffic to green
  - id: 'switch-to-green'
    name: 'gcr.io/cloud-builders/gcloud'
    args: [
      'run', 'services', 'update-traffic', 'app',
      '--to-revisions=LATEST=100',
      '--region=us-central1'
    ]
    waitFor: ['test-green']

  # Keep blue for quick rollback
  # Manual cleanup of old blue deployment after verification
```

### Canary Deployment

```yaml
# cloudbuild.yaml - Canary deployment
steps:
  # Deploy canary version
  - id: 'deploy-canary'
    name: 'gcr.io/cloud-builders/gcloud'
    args: [
      'run', 'deploy', 'app',
      '--image=gcr.io/$PROJECT_ID/app:$SHORT_SHA',
      '--region=us-central1',
      '--tag=canary',
      '--no-traffic'
    ]

  # Route 10% traffic to canary
  - id: 'canary-10'
    name: 'gcr.io/cloud-builders/gcloud'
    args: [
      'run', 'services', 'update-traffic', 'app',
      '--to-tags=canary=10',
      '--region=us-central1'
    ]
    waitFor: ['deploy-canary']

  # Monitor canary for 5 minutes
  - id: 'monitor-canary-10'
    name: 'bash'
    args:
      - '-c'
      - |
        sleep 300
        # Check error rate
        python scripts/check-metrics.py --version=canary --threshold=1
    waitFor: ['canary-10']

  # Route 50% traffic to canary
  - id: 'canary-50'
    name: 'gcr.io/cloud-builders/gcloud'
    args: [
      'run', 'services', 'update-traffic', 'app',
      '--to-tags=canary=50',
      '--region=us-central1'
    ]
    waitFor: ['monitor-canary-10']

  # Monitor canary for 10 minutes
  - id: 'monitor-canary-50'
    name: 'bash'
    args:
      - '-c'
      - |
        sleep 600
        python scripts/check-metrics.py --version=canary --threshold=1
    waitFor: ['canary-50']

  # Route 100% traffic to canary (promote)
  - id: 'promote-canary'
    name: 'gcr.io/cloud-builders/gcloud'
    args: [
      'run', 'services', 'update-traffic', 'app',
      '--to-revisions=LATEST=100',
      '--region=us-central1'
    ]
    waitFor: ['monitor-canary-50']

timeout: '3600s'
```

### Rollback Automation

```yaml
# cloudbuild-rollback.yaml
steps:
  # Get previous revision
  - id: 'get-previous-revision'
    name: 'gcr.io/cloud-builders/gcloud'
    entrypoint: 'bash'
    args:
      - '-c'
      - |
        gcloud run revisions list \
          --service=app \
          --region=us-central1 \
          --format='value(name)' \
          --limit=2 \
          --sort-by=~creationTimestamp | tail -n 1 > /workspace/prev-revision.txt

        PREV_REV=$(cat /workspace/prev-revision.txt)
        echo "Rolling back to: $PREV_REV"

  # Rollback to previous revision
  - id: 'rollback'
    name: 'gcr.io/cloud-builders/gcloud'
    entrypoint: 'bash'
    args:
      - '-c'
      - |
        PREV_REV=$(cat /workspace/prev-revision.txt)
        gcloud run services update-traffic app \
          --to-revisions=$PREV_REV=100 \
          --region=us-central1
    waitFor: ['get-previous-revision']

  # Verify rollback
  - id: 'verify-rollback'
    name: 'curlimages/curl'
    args: ['-f', 'https://app.example.com/health']
    waitFor: ['rollback']

  # Notify team
  - id: 'notify'
    name: 'bash'
    args:
      - '-c'
      - |
        PREV_REV=$(cat /workspace/prev-revision.txt)
        echo "Rollback completed to revision: $PREV_REV"
        # Send notification (Slack, email, etc.)
    waitFor: ['verify-rollback']
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Build Pipelines | Orchestrate complex multi-stage workflows |
| Pipeline as Code | Define pipelines in version-controlled YAML |
| Multi-Stage | Organize builds into logical stages with dependencies |
| Testing | Integrate unit, integration, E2E, and security tests |
| Deployment Automation | Automate multi-environment deployments with strategies |

## Summary of DevOps Module

You have completed the DevOps and CI/CD module. You should now understand:
- DevOps principles and practices
- GCP DevOps tools and Cloud Source Repositories
- Cloud Build for continuous integration
- Cloud Deploy for continuous delivery
- Building complex pipelines with testing and deployment automation

Continue practicing these concepts with the exercises in the [exercises](../exercises/) directory.
