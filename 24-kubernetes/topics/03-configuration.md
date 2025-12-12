# Configuration Management

## Overview

Kubernetes provides several mechanisms to manage application configuration separately from container images. This allows you to keep configuration flexible, secure, and environment-specific without rebuilding images.

---

## Why External Configuration?

### Problems with Hardcoded Configuration

```dockerfile
# Bad Practice - Configuration in Dockerfile
ENV DATABASE_URL="postgres://prod-db:5432/myapp"
ENV API_KEY="hardcoded-secret-key"
```

**Issues**:
- Need to rebuild image for different environments
- Secrets exposed in image layers
- No separation of concerns
- Difficult to manage across environments

### Kubernetes Configuration Solutions

| Resource | Purpose | Use Case |
|----------|---------|----------|
| **ConfigMap** | Non-sensitive configuration data | App settings, feature flags, URLs |
| **Secret** | Sensitive information | Passwords, API keys, certificates |
| **Environment Variables** | Simple key-value pairs | Basic configuration |
| **Volume Mounts** | Configuration files | Complex config files, certificates |

---

## ConfigMaps

### What is a ConfigMap?

A **ConfigMap** is a Kubernetes object used to store non-confidential configuration data in key-value pairs. Pods can consume ConfigMaps as environment variables, command-line arguments, or configuration files in volumes.

**Key Features**:
- Store configuration separate from image
- Update configuration without rebuilding images
- Share configuration across multiple pods
- Support both literal values and file contents

### Creating ConfigMaps

#### 1. From Literal Values

```bash
kubectl create configmap app-config \
  --from-literal=database_host=mysql \
  --from-literal=database_port=3306 \
  --from-literal=log_level=info
```

#### 2. From File

```bash
# Create from a configuration file
kubectl create configmap app-config --from-file=app.properties

# Create from multiple files
kubectl create configmap app-config \
  --from-file=app.properties \
  --from-file=logging.conf
```

Example `app.properties`:
```properties
database.host=mysql
database.port=3306
cache.enabled=true
log.level=info
```

#### 3. From Directory

```bash
kubectl create configmap app-config --from-file=./config/
```

#### 4. From YAML (Declarative)

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: default
data:
  # Simple key-value pairs
  database_host: "mysql"
  database_port: "3306"
  log_level: "info"

  # Multi-line configuration
  app.properties: |
    database.host=mysql
    database.port=3306
    cache.enabled=true
    log.level=info

  # JSON configuration
  config.json: |
    {
      "database": {
        "host": "mysql",
        "port": 3306
      },
      "cache": {
        "enabled": true,
        "ttl": 300
      }
    }
```

### Using ConfigMaps in Pods

#### 1. As Environment Variables

##### Inject All Keys

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app
    image: myapp:1.0
    envFrom:
    - configMapRef:
        name: app-config
```

##### Inject Specific Keys

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app
    image: myapp:1.0
    env:
    - name: DATABASE_HOST
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: database_host
    - name: DATABASE_PORT
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: database_port
```

##### With Custom Environment Variable Names

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app
    image: myapp:1.0
    env:
    - name: DB_HOST              # Custom name
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: database_host      # ConfigMap key
    - name: DB_PORT
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: database_port
```

#### 2. As Volume Mounts (Files)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app
    image: myapp:1.0
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
      readOnly: true
  volumes:
  - name: config-volume
    configMap:
      name: app-config
```

**Result**: Each key in ConfigMap becomes a file in `/etc/config/`
- `/etc/config/database_host` → "mysql"
- `/etc/config/database_port` → "3306"
- `/etc/config/app.properties` → (file contents)

#### 3. Mount Specific Keys

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app
    image: myapp:1.0
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: app-config
      items:
      - key: app.properties
        path: application.properties    # Custom filename
      - key: config.json
        path: config/app-config.json    # With subdirectory
```

### ConfigMap with Deployment

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: webapp-config
data:
  APP_ENV: "production"
  API_URL: "https://api.example.com"
  CACHE_ENABLED: "true"
  nginx.conf: |
    server {
      listen 80;
      server_name example.com;
      location / {
        proxy_pass http://backend:8080;
      }
    }
---
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
      - name: nginx
        image: nginx:1.19
        ports:
        - containerPort: 80
        env:
        - name: APP_ENV
          valueFrom:
            configMapKeyRef:
              name: webapp-config
              key: APP_ENV
        - name: API_URL
          valueFrom:
            configMapKeyRef:
              name: webapp-config
              key: API_URL
        volumeMounts:
        - name: nginx-config
          mountPath: /etc/nginx/conf.d
          readOnly: true
      volumes:
      - name: nginx-config
        configMap:
          name: webapp-config
          items:
          - key: nginx.conf
            path: default.conf
```

### ConfigMap Commands

```bash
# Create ConfigMap
kubectl create configmap app-config --from-literal=key=value
kubectl apply -f configmap.yaml

# List ConfigMaps
kubectl get configmaps
kubectl get cm

# Describe ConfigMap
kubectl describe configmap app-config

# View ConfigMap data
kubectl get configmap app-config -o yaml

# Edit ConfigMap
kubectl edit configmap app-config

# Delete ConfigMap
kubectl delete configmap app-config
```

### Updating ConfigMaps

```bash
# Update ConfigMap from file
kubectl create configmap app-config --from-file=app.properties --dry-run=client -o yaml | kubectl apply -f -

# Edit directly
kubectl edit configmap app-config
```

**Important**: Pods using ConfigMaps as environment variables won't see updates until restarted. Volume-mounted ConfigMaps update automatically (with delay).

```bash
# Restart pods to pick up ConfigMap changes
kubectl rollout restart deployment/webapp
```

---

## Secrets

### What is a Secret?

A **Secret** is similar to a ConfigMap but specifically designed to hold sensitive information such as passwords, OAuth tokens, and SSH keys.

**Key Differences from ConfigMap**:
- Base64 encoded (not encrypted by default!)
- Can be encrypted at rest with additional configuration
- More restricted RBAC controls
- Smaller size limit (1MB)

### Secret Types

| Type | Purpose |
|------|---------|
| **Opaque** | Generic secret (default) |
| **kubernetes.io/service-account-token** | Service account token |
| **kubernetes.io/dockerconfigjson** | Docker registry credentials |
| **kubernetes.io/tls** | TLS certificate and key |
| **kubernetes.io/ssh-auth** | SSH authentication |
| **kubernetes.io/basic-auth** | Basic authentication |

### Creating Secrets

#### 1. From Literal Values

```bash
kubectl create secret generic db-credentials \
  --from-literal=username=admin \
  --from-literal=password='SuperSecret123!'
```

#### 2. From Files

```bash
# Create secret from files
kubectl create secret generic ssh-key \
  --from-file=ssh-privatekey=~/.ssh/id_rsa \
  --from-file=ssh-publickey=~/.ssh/id_rsa.pub
```

#### 3. From YAML (Declarative)

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-credentials
type: Opaque
data:
  # Values must be base64 encoded
  username: YWRtaW4=              # "admin" in base64
  password: U3VwZXJTZWNyZXQxMjMh  # "SuperSecret123!" in base64
```

**Encode/Decode Base64**:
```bash
# Encode
echo -n "admin" | base64
# Output: YWRtaW4=

# Decode
echo "YWRtaW4=" | base64 --decode
# Output: admin
```

#### 4. Using stringData (No Encoding Required)

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-credentials
type: Opaque
stringData:
  # No base64 encoding needed with stringData
  username: admin
  password: SuperSecret123!
```

**Note**: `stringData` is write-only. When you read the secret, it appears in `data` as base64.

### Docker Registry Secret

```bash
kubectl create secret docker-registry regcred \
  --docker-server=https://index.docker.io/v1/ \
  --docker-username=myuser \
  --docker-password=mypassword \
  --docker-email=myemail@example.com
```

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: regcred
type: kubernetes.io/dockerconfigjson
data:
  .dockerconfigjson: eyJhdXRocyI6eyJodHRwczovL2luZGV4...
```

**Use in Pod**:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: private-pod
spec:
  containers:
  - name: app
    image: private-registry.io/myapp:1.0
  imagePullSecrets:
  - name: regcred
```

### TLS Secret

```bash
kubectl create secret tls tls-secret \
  --cert=path/to/cert.crt \
  --key=path/to/cert.key
```

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: tls-secret
type: kubernetes.io/tls
data:
  tls.crt: LS0tLS1CRUdJTi...
  tls.key: LS0tLS1CRUdJTi...
```

### Using Secrets in Pods

#### 1. As Environment Variables

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app
    image: myapp:1.0
    env:
    - name: DB_USERNAME
      valueFrom:
        secretKeyRef:
          name: db-credentials
          key: username
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: db-credentials
          key: password
```

#### 2. Inject All Secret Keys

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app
    image: myapp:1.0
    envFrom:
    - secretRef:
        name: db-credentials
```

#### 3. As Volume Mounts

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app
    image: myapp:1.0
    volumeMounts:
    - name: secret-volume
      mountPath: /etc/secrets
      readOnly: true
  volumes:
  - name: secret-volume
    secret:
      secretName: db-credentials
      defaultMode: 0400    # Read-only for owner
```

**Result**: Files created in `/etc/secrets/`
- `/etc/secrets/username`
- `/etc/secrets/password`

#### 4. Specific Secret Keys with Custom Paths

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app
    image: myapp:1.0
    volumeMounts:
    - name: secret-volume
      mountPath: /etc/secrets
      readOnly: true
  volumes:
  - name: secret-volume
    secret:
      secretName: db-credentials
      items:
      - key: username
        path: db-user
        mode: 0400
      - key: password
        path: db-pass
        mode: 0400
```

### Complete Example: Application with Secrets

```yaml
# Secret for database credentials
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
  namespace: production
type: Opaque
stringData:
  username: dbadmin
  password: ComplexPassword123!
  connection-string: "postgresql://dbadmin:ComplexPassword123!@postgres:5432/mydb"
---
# Secret for API keys
apiVersion: v1
kind: Secret
metadata:
  name: api-secrets
  namespace: production
type: Opaque
stringData:
  stripe-api-key: "sk_live_xxxxxxxxxxxxx"
  sendgrid-api-key: "SG.xxxxxxxxxxxxx"
  jwt-secret: "random-jwt-secret-key-here"
---
# Deployment using secrets
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp
  namespace: production
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
      - name: app
        image: myapp:2.0
        ports:
        - containerPort: 8080
        env:
        # Database credentials
        - name: DB_USERNAME
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: username
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: password
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: connection-string
        # API keys
        - name: STRIPE_API_KEY
          valueFrom:
            secretKeyRef:
              name: api-secrets
              key: stripe-api-key
        - name: JWT_SECRET
          valueFrom:
            secretKeyRef:
              name: api-secrets
              key: jwt-secret
        volumeMounts:
        - name: api-keys
          mountPath: /etc/api-keys
          readOnly: true
      volumes:
      - name: api-keys
        secret:
          secretName: api-secrets
          items:
          - key: sendgrid-api-key
            path: sendgrid
```

### Secret Commands

```bash
# Create secret
kubectl create secret generic my-secret --from-literal=key=value
kubectl apply -f secret.yaml

# List secrets
kubectl get secrets

# Describe secret (won't show values)
kubectl describe secret db-credentials

# View secret data (base64 encoded)
kubectl get secret db-credentials -o yaml

# View decoded secret values
kubectl get secret db-credentials -o jsonpath='{.data.password}' | base64 --decode

# Edit secret
kubectl edit secret db-credentials

# Delete secret
kubectl delete secret db-credentials
```

---

## YAML Configuration Files

### YAML Basics for Kubernetes

#### YAML Syntax

```yaml
# Comments start with #

# Key-value pairs
key: value
name: "John Doe"
age: 30

# Nested objects (indentation matters!)
person:
  name: John
  address:
    street: 123 Main St
    city: Boston

# Lists/Arrays
fruits:
  - apple
  - banana
  - orange

# Inline list
numbers: [1, 2, 3, 4, 5]

# Multi-line strings
description: |
  This is a multi-line string.
  It preserves line breaks.

# Folded strings (newlines become spaces)
summary: >
  This is a long string
  that will be folded
  into a single line.
```

### Kubernetes YAML Structure

Every Kubernetes resource has this structure:

```yaml
apiVersion: <api-group>/<version>  # Required
kind: <ResourceType>                # Required
metadata:                           # Required
  name: <name>
  namespace: <namespace>
  labels:
    key: value
  annotations:
    key: value
spec:                               # Required (defines desired state)
  # Resource-specific configuration
status:                             # Managed by Kubernetes
  # Current state
```

### apiVersion Reference

| Resource | apiVersion |
|----------|------------|
| Pod, Service, ConfigMap, Secret | v1 |
| Deployment, ReplicaSet, StatefulSet | apps/v1 |
| Ingress | networking.k8s.io/v1 |
| HorizontalPodAutoscaler | autoscaling/v2 |
| CronJob | batch/v1 |
| NetworkPolicy | networking.k8s.io/v1 |
| PersistentVolumeClaim | v1 |

### Labels and Selectors

#### Labels

Labels are key-value pairs attached to objects.

```yaml
metadata:
  labels:
    app: nginx
    tier: frontend
    environment: production
    version: v1.2.0
    team: platform
```

**Best Practices**:
- Use meaningful, consistent labels
- Include app name, tier, environment, version
- Use for grouping and selecting resources

#### Selectors

##### Equality-Based

```yaml
selector:
  matchLabels:
    app: nginx
    tier: frontend
```

##### Set-Based

```yaml
selector:
  matchExpressions:
  - key: environment
    operator: In
    values:
    - production
    - staging
  - key: tier
    operator: NotIn
    values:
    - cache
  - key: app
    operator: Exists
```

**Operators**: `In`, `NotIn`, `Exists`, `DoesNotExist`

### Annotations

Annotations are non-identifying metadata.

```yaml
metadata:
  annotations:
    description: "This is the main application deployment"
    contact: "platform-team@example.com"
    kubernetes.io/change-cause: "Update to version 2.0"
    prometheus.io/scrape: "true"
    prometheus.io/port: "8080"
    nginx.ingress.kubernetes.io/rewrite-target: /
```

**Use Cases**:
- Build/release information
- Contact information
- Tool-specific configuration
- External system references

### Multi-Resource YAML Files

Separate resources with `---`:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: myapp
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: myapp
data:
  app.env: "production"
---
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
  namespace: myapp
type: Opaque
stringData:
  api-key: "secret-key"
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp
  namespace: myapp
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
      - name: app
        image: myapp:1.0
        envFrom:
        - configMapRef:
            name: app-config
        - secretRef:
            name: app-secret
```

### Resource Specifications

#### CPU and Memory

```yaml
resources:
  requests:              # Minimum guaranteed
    cpu: "100m"         # 0.1 CPU cores
    memory: "128Mi"     # 128 Mebibytes
  limits:                # Maximum allowed
    cpu: "500m"         # 0.5 CPU cores
    memory: "512Mi"     # 512 Mebibytes
```

**Units**:
- CPU: millicores (m) - 1000m = 1 core
- Memory:
  - Ki (Kibibyte) = 1024 bytes
  - Mi (Mebibyte) = 1024 Ki
  - Gi (Gibibyte) = 1024 Mi

#### Health Probes

##### Liveness Probe

Determines if container is alive. Restarts if fails.

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
    httpHeaders:
    - name: Custom-Header
      value: Awesome
  initialDelaySeconds: 30
  periodSeconds: 10
  timeoutSeconds: 5
  failureThreshold: 3
  successThreshold: 1
```

##### Readiness Probe

Determines if container is ready to serve traffic.

```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 5
  timeoutSeconds: 3
  failureThreshold: 3
```

##### Startup Probe

For slow-starting containers.

```yaml
startupProbe:
  httpGet:
    path: /startup
    port: 8080
  initialDelaySeconds: 0
  periodSeconds: 10
  timeoutSeconds: 3
  failureThreshold: 30    # 30 * 10s = 5 minutes max startup time
```

##### Probe Types

```yaml
# HTTP GET
livenessProbe:
  httpGet:
    path: /health
    port: 8080

# TCP Socket
livenessProbe:
  tcpSocket:
    port: 3306

# Command Execution
livenessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
```

### Complete Application Template

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: myapp-production
  labels:
    environment: production
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: myapp-config
  namespace: myapp-production
data:
  LOG_LEVEL: "info"
  APP_ENV: "production"
  CACHE_TTL: "300"
  application.yml: |
    server:
      port: 8080
    spring:
      profiles:
        active: production
---
apiVersion: v1
kind: Secret
metadata:
  name: myapp-secrets
  namespace: myapp-production
type: Opaque
stringData:
  database-password: "SuperSecret123!"
  api-key: "sk_live_xxxxxxxxxx"
  jwt-secret: "random-secret-key"
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  namespace: myapp-production
  labels:
    app: myapp
    version: v2.0.0
  annotations:
    kubernetes.io/change-cause: "Deploy version 2.0.0"
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
        version: v2.0.0
    spec:
      containers:
      - name: myapp
        image: myregistry/myapp:2.0.0
        imagePullPolicy: Always
        ports:
        - name: http
          containerPort: 8080
          protocol: TCP
        env:
        - name: LOG_LEVEL
          valueFrom:
            configMapKeyRef:
              name: myapp-config
              key: LOG_LEVEL
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: myapp-secrets
              key: database-password
        - name: API_KEY
          valueFrom:
            secretKeyRef:
              name: myapp-secrets
              key: api-key
        volumeMounts:
        - name: config
          mountPath: /etc/config
          readOnly: true
        - name: secrets
          mountPath: /etc/secrets
          readOnly: true
        resources:
          requests:
            cpu: 200m
            memory: 256Mi
          limits:
            cpu: 1000m
            memory: 512Mi
        livenessProbe:
          httpGet:
            path: /actuator/health/liveness
            port: 8080
          initialDelaySeconds: 60
          periodSeconds: 10
          timeoutSeconds: 5
          failureThreshold: 3
        readinessProbe:
          httpGet:
            path: /actuator/health/readiness
            port: 8080
          initialDelaySeconds: 10
          periodSeconds: 5
          timeoutSeconds: 3
          failureThreshold: 3
      volumes:
      - name: config
        configMap:
          name: myapp-config
      - name: secrets
        secret:
          secretName: myapp-secrets
          defaultMode: 0400
---
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
  namespace: myapp-production
  labels:
    app: myapp
spec:
  type: ClusterIP
  selector:
    app: myapp
  ports:
  - name: http
    port: 80
    targetPort: 8080
    protocol: TCP
```

---

## Best Practices

### ConfigMaps
1. **Separate per environment** - dev, staging, prod ConfigMaps
2. **Version control** - Store YAML in Git
3. **Immutable ConfigMaps** - Create new ConfigMap, update references
4. **Size limits** - Keep under 1MB
5. **Naming convention** - `<app>-config-<env>`

### Secrets
1. **Never commit secrets to Git** - Use secret management tools
2. **Enable encryption at rest** - Encrypt etcd
3. **Use RBAC** - Limit who can read secrets
4. **Rotate regularly** - Implement secret rotation
5. **External secret management** - Use Vault, AWS Secrets Manager
6. **Minimal exposure** - Only mount secrets where needed

### YAML Files
1. **Validation** - Use `kubectl --dry-run=client` before applying
2. **Organization** - Group related resources in single file
3. **Comments** - Document non-obvious configuration
4. **Formatting** - Use consistent indentation (2 spaces)
5. **Templates** - Use Helm or Kustomize for complex deployments

---

## Summary

**Key Concepts**:

| Resource | Purpose | Sensitivity |
|----------|---------|-------------|
| **ConfigMap** | Non-sensitive configuration | Public |
| **Secret** | Sensitive information | Private |
| **Environment Variables** | Simple configuration | Depends |
| **Volume Mounts** | File-based configuration | Depends |

**Remember**:
- ConfigMaps for settings, Secrets for credentials
- Base64 encoding ≠ encryption
- Volume-mounted configs update automatically
- Environment variable configs need pod restart

---

## Next Topic

Continue to [Networking](./04-networking.md) to learn about Kubernetes networking, service discovery, and ingress controllers.
