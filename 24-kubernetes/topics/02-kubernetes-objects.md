# Kubernetes Objects

## Overview

Kubernetes objects are persistent entities in the Kubernetes system that represent the state of your cluster. They describe what containerized applications are running, the resources available to them, and the policies governing their behavior.

---

## Understanding Kubernetes Objects

### What are Kubernetes Objects?

Kubernetes objects are records of intent - once you create an object, the Kubernetes system constantly works to ensure that object exists. By creating an object, you're telling Kubernetes what you want your cluster's workload to look like (the desired state).

### Object Spec and Status

Every Kubernetes object includes two nested fields:

1. **Spec**: The desired state you want the object to have (you provide this)
2. **Status**: The current state of the object (Kubernetes provides and updates this)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:                    # Desired state
  containers:
  - name: nginx
    image: nginx:1.19
status:                  # Current state (managed by Kubernetes)
  phase: Running
  conditions:
  - type: Ready
    status: "True"
```

### Required Fields

Every Kubernetes object must have:

| Field | Description |
|-------|-------------|
| **apiVersion** | Which version of the Kubernetes API you're using |
| **kind** | What kind of object you want to create |
| **metadata** | Data that helps uniquely identify the object |
| **spec** | The desired state for the object |

---

## Pods

### What is a Pod?

A **Pod** is the smallest deployable unit in Kubernetes. It represents a single instance of a running process in your cluster.

**Key Characteristics**:
- Can contain one or more containers
- Containers in a pod share network namespace and storage
- Pods are ephemeral - they're created, destroyed, but never "healed"
- Each pod gets a unique IP address

### When to Use Pods

| Scenario | Description |
|----------|-------------|
| **Single Container** | Most common - one container per pod |
| **Multi-Container** | Tightly coupled containers that need to share resources |
| **Sidecar Pattern** | Helper containers alongside main application |
| **Ambassador Pattern** | Proxy container for external service access |

### Pod YAML Structure

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
    tier: frontend
spec:
  containers:
  - name: nginx
    image: nginx:1.19
    ports:
    - containerPort: 80
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

### Multi-Container Pod Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
spec:
  containers:
  # Main application container
  - name: webapp
    image: myapp:1.0
    ports:
    - containerPort: 8080
    volumeMounts:
    - name: shared-data
      mountPath: /app/data

  # Sidecar container (log collector)
  - name: log-collector
    image: fluentd:latest
    volumeMounts:
    - name: shared-data
      mountPath: /var/log

  volumes:
  - name: shared-data
    emptyDir: {}
```

### Pod Lifecycle

```
Pending → Running → Succeeded/Failed
           ↓
        CrashLoopBackOff (if container keeps failing)
```

**Pod Phases**:

| Phase | Description |
|-------|-------------|
| **Pending** | Pod accepted but containers not yet created |
| **Running** | Pod bound to node, at least one container running |
| **Succeeded** | All containers terminated successfully |
| **Failed** | All containers terminated, at least one failed |
| **Unknown** | Pod state cannot be determined |

### Pod Management Commands

```bash
# Create a pod from YAML
kubectl apply -f pod.yaml

# Create a pod imperatively
kubectl run nginx --image=nginx:1.19

# List all pods
kubectl get pods

# List pods with more details
kubectl get pods -o wide

# Describe a specific pod
kubectl describe pod nginx-pod

# View pod logs
kubectl logs nginx-pod

# Execute command in pod
kubectl exec -it nginx-pod -- /bin/bash

# Delete a pod
kubectl delete pod nginx-pod

# Watch pods in real-time
kubectl get pods -w
```

---

## ReplicaSets

### What is a ReplicaSet?

A **ReplicaSet** ensures that a specified number of pod replicas are running at any given time. It's the next-generation Replication Controller.

**Key Features**:
- Maintains stable set of replica pods
- Guarantees availability of specified number of identical pods
- Uses label selectors to identify pods to manage
- Self-healing - replaces failed pods automatically

### ReplicaSet YAML

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replicaset
  labels:
    app: nginx
spec:
  replicas: 3                    # Number of desired pods
  selector:
    matchLabels:
      app: nginx
      tier: frontend
  template:                      # Pod template
    metadata:
      labels:
        app: nginx
        tier: frontend
    spec:
      containers:
      - name: nginx
        image: nginx:1.19
        ports:
        - containerPort: 80
```

### Advanced Selector

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: advanced-replicaset
spec:
  replicas: 3
  selector:
    matchExpressions:
    - key: app
      operator: In
      values:
      - nginx
      - apache
    - key: tier
      operator: NotIn
      values:
      - cache
  template:
    metadata:
      labels:
        app: nginx
        tier: frontend
    spec:
      containers:
      - name: nginx
        image: nginx:1.19
```

### ReplicaSet Commands

```bash
# Create ReplicaSet
kubectl apply -f replicaset.yaml

# List ReplicaSets
kubectl get rs
kubectl get replicasets

# Describe ReplicaSet
kubectl describe rs nginx-replicaset

# Scale ReplicaSet
kubectl scale rs nginx-replicaset --replicas=5

# Delete ReplicaSet (keeps pods)
kubectl delete rs nginx-replicaset --cascade=orphan

# Delete ReplicaSet (deletes pods)
kubectl delete rs nginx-replicaset
```

### Important Note

While you can use ReplicaSets directly, **Deployments** are preferred as they provide additional features like rollout and rollback capabilities.

---

## Deployments

### What is a Deployment?

A **Deployment** provides declarative updates for Pods and ReplicaSets. It's the recommended way to manage stateless applications in Kubernetes.

**Key Features**:
- Rolling updates and rollbacks
- Scaling applications
- Pause and resume deployments
- Versioning and revision history
- Self-healing capabilities

### Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.19
        ports:
        - containerPort: 80
        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 5
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
```

### Deployment Strategies

#### 1. RollingUpdate (Default)

Gradually replaces old pods with new ones.

```yaml
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1           # Max new pods above desired count
      maxUnavailable: 0     # Max pods that can be unavailable
```

**Benefits**: Zero downtime, gradual rollout
**Use Case**: Most production applications

#### 2. Recreate

Terminates all old pods before creating new ones.

```yaml
spec:
  strategy:
    type: Recreate
```

**Benefits**: Simple, ensures no version mixing
**Use Case**: Development, or when version mixing is problematic

### Advanced Deployment Example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp-deployment
  namespace: production
  annotations:
    kubernetes.io/change-cause: "Update to version 2.0"
spec:
  replicas: 5
  revisionHistoryLimit: 10      # Number of old ReplicaSets to retain
  progressDeadlineSeconds: 600  # Max time for deployment to progress
  minReadySeconds: 10           # Min time for pod to be ready
  selector:
    matchLabels:
      app: webapp
      version: v2
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 2
      maxUnavailable: 1
  template:
    metadata:
      labels:
        app: webapp
        version: v2
    spec:
      containers:
      - name: webapp
        image: myapp:2.0
        ports:
        - containerPort: 8080
          protocol: TCP
        env:
        - name: ENV
          value: "production"
        - name: DB_HOST
          value: "db.example.com"
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 60
          periodSeconds: 10
          timeoutSeconds: 5
          failureThreshold: 3
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 10
          periodSeconds: 5
          timeoutSeconds: 3
          failureThreshold: 3
        resources:
          requests:
            cpu: 200m
            memory: 256Mi
          limits:
            cpu: 500m
            memory: 512Mi
```

### Deployment Commands

```bash
# Create deployment
kubectl apply -f deployment.yaml

# Create deployment imperatively
kubectl create deployment nginx --image=nginx:1.19 --replicas=3

# List deployments
kubectl get deployments
kubectl get deploy

# Describe deployment
kubectl describe deployment nginx-deployment

# Scale deployment
kubectl scale deployment nginx-deployment --replicas=5

# Update image (rolling update)
kubectl set image deployment/nginx-deployment nginx=nginx:1.20

# Check rollout status
kubectl rollout status deployment/nginx-deployment

# View rollout history
kubectl rollout history deployment/nginx-deployment

# Rollback to previous version
kubectl rollout undo deployment/nginx-deployment

# Rollback to specific revision
kubectl rollout undo deployment/nginx-deployment --to-revision=2

# Pause deployment (stops rollout)
kubectl rollout pause deployment/nginx-deployment

# Resume deployment
kubectl rollout resume deployment/nginx-deployment

# Restart deployment (recreates all pods)
kubectl rollout restart deployment/nginx-deployment

# Edit deployment
kubectl edit deployment nginx-deployment

# Delete deployment
kubectl delete deployment nginx-deployment
```

### Monitoring Deployments

```bash
# Watch deployment in real-time
kubectl get deployment nginx-deployment -w

# View deployment events
kubectl describe deployment nginx-deployment | grep Events -A 20

# Check ReplicaSet created by deployment
kubectl get rs -l app=nginx

# View all resources related to deployment
kubectl get all -l app=nginx
```

---

## Services

### What is a Service?

A **Service** is an abstraction that defines a logical set of pods and a policy to access them. Services enable loose coupling between dependent pods.

**Why Services?**
- Pods are ephemeral (IP addresses change)
- Services provide stable networking
- Built-in load balancing
- Service discovery through DNS

### Service Types

| Type | Description | Use Case |
|------|-------------|----------|
| **ClusterIP** | Internal IP only (default) | Internal communication |
| **NodePort** | Exposes on each node's IP at a static port | Testing, development |
| **LoadBalancer** | Cloud provider's load balancer | Production external access |
| **ExternalName** | Maps to DNS name | External service reference |

### 1. ClusterIP Service

Default type - only accessible within the cluster.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: ClusterIP
  selector:
    app: nginx
  ports:
  - name: http
    protocol: TCP
    port: 80           # Service port
    targetPort: 80     # Container port
```

**Access within cluster**:
```bash
# From another pod
curl http://nginx-service:80
curl http://nginx-service.default.svc.cluster.local:80
```

### 2. NodePort Service

Exposes service on each node's IP at a static port (30000-32767).

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
  - name: http
    protocol: TCP
    port: 80
    targetPort: 80
    nodePort: 30080    # Optional: auto-assigned if not specified
```

**Access**:
```bash
# From outside cluster
curl http://<node-ip>:30080
```

### 3. LoadBalancer Service

Creates an external load balancer (cloud provider specific).

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-loadbalancer
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - name: http
    protocol: TCP
    port: 80
    targetPort: 80
  loadBalancerSourceRanges:
  - "10.0.0.0/8"      # Optional: restrict source IPs
```

### 4. Multi-Port Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: multi-port-service
spec:
  selector:
    app: webapp
  ports:
  - name: http
    protocol: TCP
    port: 80
    targetPort: 8080
  - name: https
    protocol: TCP
    port: 443
    targetPort: 8443
  - name: metrics
    protocol: TCP
    port: 9090
    targetPort: 9090
```

### Headless Service

Service without ClusterIP - returns pod IPs directly.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: headless-service
spec:
  clusterIP: None      # Makes it headless
  selector:
    app: database
  ports:
  - port: 3306
    targetPort: 3306
```

**Use Case**: StatefulSets, direct pod-to-pod communication

### Service with Session Affinity

```yaml
apiVersion: v1
kind: Service
metadata:
  name: sticky-service
spec:
  selector:
    app: webapp
  sessionAffinity: ClientIP
  sessionAffinityConfig:
    clientIP:
      timeoutSeconds: 10800
  ports:
  - port: 80
    targetPort: 8080
```

### Service Commands

```bash
# Create service
kubectl apply -f service.yaml

# Create service imperatively
kubectl expose deployment nginx-deployment --port=80 --target-port=80

# List services
kubectl get services
kubectl get svc

# Describe service
kubectl describe svc nginx-service

# Get service endpoints
kubectl get endpoints nginx-service

# Access service (from within cluster)
kubectl run -it --rm debug --image=busybox --restart=Never -- wget -O- nginx-service

# Port forward service to local machine
kubectl port-forward svc/nginx-service 8080:80

# Delete service
kubectl delete svc nginx-service
```

---

## Namespaces

### What is a Namespace?

**Namespaces** provide a mechanism for isolating groups of resources within a single cluster. They're intended for use in environments with many users spread across multiple teams or projects.

**Key Features**:
- Resource isolation
- Resource quotas per namespace
- RBAC policies per namespace
- DNS scoping

### Default Namespaces

| Namespace | Purpose |
|-----------|---------|
| **default** | Default namespace for objects with no other namespace |
| **kube-system** | For objects created by the Kubernetes system |
| **kube-public** | Readable by all users, reserved for cluster usage |
| **kube-node-lease** | For node heartbeat information |

### Namespace YAML

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: development
  labels:
    name: development
    environment: dev
```

### Creating Resources in Namespace

#### Method 1: In YAML

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  namespace: development    # Specify namespace
spec:
  containers:
  - name: nginx
    image: nginx:1.19
```

#### Method 2: With kubectl

```bash
kubectl apply -f pod.yaml -n development
```

### Namespace with Resource Quotas

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: production
---
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: production
spec:
  hard:
    requests.cpu: "10"
    requests.memory: 20Gi
    limits.cpu: "20"
    limits.memory: 40Gi
    pods: "100"
---
apiVersion: v1
kind: LimitRange
metadata:
  name: limit-range
  namespace: production
spec:
  limits:
  - max:
      cpu: "2"
      memory: 4Gi
    min:
      cpu: "100m"
      memory: 128Mi
    default:
      cpu: "500m"
      memory: 512Mi
    defaultRequest:
      cpu: "250m"
      memory: 256Mi
    type: Container
```

### Namespace Commands

```bash
# Create namespace
kubectl create namespace development
kubectl apply -f namespace.yaml

# List namespaces
kubectl get namespaces
kubectl get ns

# Describe namespace
kubectl describe ns development

# Set default namespace for current context
kubectl config set-context --current --namespace=development

# View resources in namespace
kubectl get pods -n development
kubectl get all -n development

# View resources in all namespaces
kubectl get pods --all-namespaces
kubectl get pods -A

# Delete namespace (deletes all resources in it!)
kubectl delete namespace development
```

### Cross-Namespace Communication

Pods in different namespaces can communicate using fully qualified domain names:

```
<service-name>.<namespace>.svc.cluster.local
```

Example:
```bash
# From a pod in namespace 'frontend' accessing service in 'backend'
curl http://api-service.backend.svc.cluster.local:8080
```

---

## Complete Application Example

### Full Stack Application with All Objects

```yaml
# Namespace
apiVersion: v1
kind: Namespace
metadata:
  name: myapp
---
# Backend Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
  namespace: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
      - name: api
        image: myapp/backend:1.0
        ports:
        - containerPort: 8080
        env:
        - name: DB_HOST
          value: database-service
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 500m
            memory: 512Mi
---
# Backend Service
apiVersion: v1
kind: Service
metadata:
  name: backend-service
  namespace: myapp
spec:
  selector:
    app: backend
  ports:
  - port: 80
    targetPort: 8080
---
# Frontend Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  namespace: myapp
spec:
  replicas: 2
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: webapp
        image: myapp/frontend:1.0
        ports:
        - containerPort: 80
        env:
        - name: API_URL
          value: http://backend-service
        resources:
          requests:
            cpu: 50m
            memory: 64Mi
          limits:
            cpu: 200m
            memory: 256Mi
---
# Frontend Service (LoadBalancer)
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
  namespace: myapp
spec:
  type: LoadBalancer
  selector:
    app: frontend
  ports:
  - port: 80
    targetPort: 80
---
# Database StatefulSet (simplified)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: database
  namespace: myapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: database
  template:
    metadata:
      labels:
        app: database
    spec:
      containers:
      - name: postgres
        image: postgres:13
        ports:
        - containerPort: 5432
        env:
        - name: POSTGRES_PASSWORD
          value: "password"    # Use Secrets in production!
---
# Database Service
apiVersion: v1
kind: Service
metadata:
  name: database-service
  namespace: myapp
spec:
  clusterIP: None    # Headless
  selector:
    app: database
  ports:
  - port: 5432
    targetPort: 5432
```

### Deploy the Application

```bash
# Apply all resources
kubectl apply -f app.yaml

# Check everything
kubectl get all -n myapp

# Get service external IP (for LoadBalancer)
kubectl get svc frontend-service -n myapp

# Test the application
curl http://<external-ip>
```

---

## Best Practices

### Pods
1. **Don't use naked pods** - Always use Deployments or StatefulSets
2. **Resource requests/limits** - Always specify them
3. **Health probes** - Use liveness and readiness probes
4. **Single responsibility** - One main container per pod (with sidecars as needed)

### Deployments
1. **Version your images** - Never use `latest` tag
2. **Rolling updates** - Use for zero-downtime deployments
3. **Resource limits** - Prevent resource starvation
4. **Readiness probes** - Ensure traffic only goes to ready pods
5. **Revision history** - Keep adequate revision history for rollbacks

### Services
1. **Use ClusterIP by default** - Only expose externally when needed
2. **Named ports** - Use named ports for clarity
3. **Health checks** - Ensure backend pods are healthy
4. **Stable selectors** - Don't change service selectors frequently

### Namespaces
1. **Environment separation** - dev, staging, production namespaces
2. **Resource quotas** - Prevent resource monopolization
3. **RBAC** - Implement proper access controls
4. **Naming convention** - Use clear, consistent naming

---

## Summary

**Key Concepts**:

| Object | Purpose | Managed By |
|--------|---------|------------|
| **Pod** | Smallest deployable unit | Usually not directly |
| **ReplicaSet** | Maintains desired number of pods | Deployment |
| **Deployment** | Declarative pod management | You |
| **Service** | Stable networking for pods | You |
| **Namespace** | Resource isolation | You |

**Remember**:
- Pods are ephemeral, Services are stable
- Use Deployments, not naked Pods
- Services enable pod discovery
- Namespaces isolate resources

---

## Next Topic

Continue to [Configuration Management](./03-configuration.md) to learn about ConfigMaps, Secrets, and Kubernetes configuration files.
