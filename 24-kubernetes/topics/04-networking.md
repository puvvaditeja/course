# Kubernetes Networking

## Overview

Kubernetes networking is designed to support communication between various components of a distributed application. Understanding Kubernetes networking is essential for deploying and maintaining applications effectively.

---

## Kubernetes Networking Model

### Core Principles

Kubernetes enforces these fundamental networking requirements:

1. **Pod-to-Pod Communication**: All pods can communicate with each other without NAT
2. **Node-to-Pod Communication**: Nodes can communicate with all pods without NAT
3. **Pod IP Awareness**: A pod sees itself with the same IP that others see it with (no NAT)
4. **Flat Network**: All pods exist in a flat network namespace

### Network Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    KUBERNETES CLUSTER                   │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  ┌──────────────────┐          ┌──────────────────┐    │
│  │   NODE 1         │          │   NODE 2         │    │
│  │  10.0.1.0/24     │          │  10.0.2.0/24     │    │
│  │                  │          │                  │    │
│  │  ┌────────────┐  │          │  ┌────────────┐  │    │
│  │  │ Pod A      │  │          │  │ Pod C      │  │    │
│  │  │ 10.0.1.10  │◄─┼──────────┼─►│ 10.0.2.10  │  │    │
│  │  └────────────┘  │          │  └────────────┘  │    │
│  │                  │          │                  │    │
│  │  ┌────────────┐  │          │  ┌────────────┐  │    │
│  │  │ Pod B      │  │          │  │ Pod D      │  │    │
│  │  │ 10.0.1.11  │  │          │  │ 10.0.2.11  │  │    │
│  │  └────────────┘  │          │  └────────────┘  │    │
│  └──────────────────┘          └──────────────────┘    │
│           ▲                             ▲               │
│           │         ┌─────────┐         │               │
│           └─────────┤ Service ├─────────┘               │
│                     │10.96.0.1│                         │
│                     └─────────┘                         │
└─────────────────────────────────────────────────────────┘
```

### Network Layers

| Layer | Description | Responsibility |
|-------|-------------|----------------|
| **Container-to-Container** | Within the same pod | Shared network namespace (localhost) |
| **Pod-to-Pod** | Across the cluster | CNI plugin (Calico, Flannel, etc.) |
| **Pod-to-Service** | Load-balanced access | kube-proxy + iptables/ipvs |
| **External-to-Service** | Outside cluster | Service types (NodePort, LoadBalancer, Ingress) |

---

## Pod Networking

### Pod IP Assignment

Each pod gets its own unique IP address:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: network-pod
spec:
  containers:
  - name: nginx
    image: nginx:1.19
```

```bash
# Check pod IP
kubectl get pod network-pod -o wide

# Output example:
# NAME          READY   STATUS    IP           NODE
# network-pod   1/1     Running   10.244.1.5   node-1
```

### Container-to-Container Communication (Same Pod)

Containers in the same pod share:
- Network namespace
- IP address
- Port space
- Localhost communication

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
spec:
  containers:
  - name: webapp
    image: nginx:1.19
    ports:
    - containerPort: 80

  - name: sidecar
    image: curlimages/curl:latest
    command: ['sh', '-c', 'while true; do curl localhost:80; sleep 5; done']
```

In this example, the sidecar can access nginx using `localhost:80`.

### Pod-to-Pod Communication

Pods can communicate directly using their IP addresses:

```yaml
# Pod 1
apiVersion: v1
kind: Pod
metadata:
  name: pod-a
  labels:
    app: sender
spec:
  containers:
  - name: alpine
    image: alpine:latest
    command: ['sh', '-c', 'while true; do sleep 3600; done']
---
# Pod 2
apiVersion: v1
kind: Pod
metadata:
  name: pod-b
  labels:
    app: receiver
spec:
  containers:
  - name: nginx
    image: nginx:1.19
```

```bash
# Get pod-b IP
POD_B_IP=$(kubectl get pod pod-b -o jsonpath='{.status.podIP}')

# Access pod-b from pod-a
kubectl exec pod-a -- wget -qO- http://$POD_B_IP
```

**Note**: Direct pod IP communication is fragile because pod IPs change. Use Services instead.

---

## Service Discovery

### DNS-Based Service Discovery

Kubernetes provides built-in DNS for service discovery using CoreDNS.

#### DNS Records

##### Service DNS Format

```
<service-name>.<namespace>.svc.cluster.local
```

Examples:
```bash
# Full qualified domain name
curl http://nginx-service.default.svc.cluster.local

# Within same namespace
curl http://nginx-service

# Cross-namespace
curl http://api-service.backend.svc.cluster.local
```

##### Pod DNS Format

```
<pod-ip-with-dashes>.<namespace>.pod.cluster.local
```

Example:
```bash
# Pod with IP 10.244.1.5
curl http://10-244-1-5.default.pod.cluster.local
```

### Service Discovery Example

```yaml
# Backend Service
apiVersion: v1
kind: Service
metadata:
  name: backend-api
  namespace: production
spec:
  selector:
    app: backend
  ports:
  - port: 8080
    targetPort: 8080
---
# Backend Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
  namespace: production
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
---
# Frontend Deployment (discovers backend)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  namespace: production
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
        env:
        - name: BACKEND_URL
          value: "http://backend-api:8080"
          # Or use full DNS name:
          # value: "http://backend-api.production.svc.cluster.local:8080"
```

### Environment Variable-Based Discovery

Kubernetes automatically creates environment variables for services:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: database
spec:
  selector:
    app: mysql
  ports:
  - port: 3306
```

Created environment variables in pods:
```bash
DATABASE_SERVICE_HOST=10.96.0.10
DATABASE_SERVICE_PORT=3306
DATABASE_PORT=tcp://10.96.0.10:3306
DATABASE_PORT_3306_TCP=tcp://10.96.0.10:3306
DATABASE_PORT_3306_TCP_PROTO=tcp
DATABASE_PORT_3306_TCP_PORT=3306
DATABASE_PORT_3306_TCP_ADDR=10.96.0.10
```

**Limitation**: Services must exist before pods are created.

### Headless Services for Direct Pod Discovery

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql-headless
spec:
  clusterIP: None    # Headless service
  selector:
    app: mysql
  ports:
  - port: 3306
```

DNS returns all pod IPs instead of service IP:

```bash
nslookup mysql-headless.default.svc.cluster.local

# Returns:
# mysql-headless.default.svc.cluster.local has address 10.244.1.5
# mysql-headless.default.svc.cluster.local has address 10.244.2.7
# mysql-headless.default.svc.cluster.local has address 10.244.3.9
```

---

## Ingress Controllers

### What is Ingress?

**Ingress** exposes HTTP and HTTPS routes from outside the cluster to services within the cluster. It provides:
- URL-based routing
- SSL/TLS termination
- Name-based virtual hosting
- Load balancing

### Ingress vs Service

| Feature | Service (LoadBalancer) | Ingress |
|---------|----------------------|---------|
| **Layer** | L4 (TCP/UDP) | L7 (HTTP/HTTPS) |
| **Cost** | One LB per service | One LB for all services |
| **Routing** | Simple port-based | Path/host-based |
| **SSL** | Pass-through | Termination |
| **Features** | Basic | Advanced (rewrite, redirect, auth) |

### Ingress Architecture

```
┌──────────────────────────────────────────────────────┐
│                     INTERNET                          │
└────────────────────┬─────────────────────────────────┘
                     │
                     ▼
              ┌─────────────┐
              │Load Balancer│ (Cloud Provider)
              └──────┬──────┘
                     │
                     ▼
         ┌──────────────────────┐
         │  Ingress Controller  │ (nginx, traefik, etc.)
         └──────────┬───────────┘
                    │
        ┌───────────┼───────────┐
        │           │           │
        ▼           ▼           ▼
   ┌────────┐  ┌────────┐  ┌────────┐
   │Service1│  │Service2│  │Service3│
   └───┬────┘  └───┬────┘  └───┬────┘
       │           │           │
   ┌───┴───┐   ┌───┴───┐   ┌───┴───┐
   │Pods..│    │Pods..│    │Pods..│
   └───────┘   └───────┘   └───────┘
```

### Installing Ingress Controller

#### NGINX Ingress Controller

```bash
# Install NGINX Ingress Controller
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.1/deploy/static/provider/cloud/deploy.yaml

# Verify installation
kubectl get pods -n ingress-nginx
kubectl get svc -n ingress-nginx
```

### Basic Ingress Resource

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: basic-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: webapp-service
            port:
              number: 80
```

### Path-Based Routing

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: path-based-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  ingressClassName: nginx
  rules:
  - host: api.example.com
    http:
      paths:
      # Route /api/* to api-service
      - path: /api(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 8080
      # Route /admin/* to admin-service
      - path: /admin(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: admin-service
            port:
              number: 8080
      # Route /web/* to web-service
      - path: /web(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
```

### Host-Based Routing (Virtual Hosting)

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: host-based-ingress
spec:
  ingressClassName: nginx
  rules:
  # Route api.example.com to api-service
  - host: api.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 8080
  # Route www.example.com to web-service
  - host: www.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
  # Route admin.example.com to admin-service
  - host: admin.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: admin-service
            port:
              number: 8080
```

### TLS/SSL Termination

```yaml
# First, create TLS secret
apiVersion: v1
kind: Secret
metadata:
  name: tls-secret
type: kubernetes.io/tls
data:
  tls.crt: <base64-encoded-cert>
  tls.key: <base64-encoded-key>
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: tls-ingress
spec:
  ingressClassName: nginx
  tls:
  - hosts:
    - myapp.example.com
    secretName: tls-secret
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: webapp-service
            port:
              number: 80
```

```bash
# Create TLS secret from certificate files
kubectl create secret tls tls-secret \
  --cert=path/to/tls.crt \
  --key=path/to/tls.key
```

### Advanced Ingress Example

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: advanced-ingress
  annotations:
    # Force SSL redirect
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
    # CORS configuration
    nginx.ingress.kubernetes.io/enable-cors: "true"
    nginx.ingress.kubernetes.io/cors-allow-methods: "GET, POST, PUT, DELETE, OPTIONS"
    nginx.ingress.kubernetes.io/cors-allow-origin: "https://example.com"
    # Rate limiting
    nginx.ingress.kubernetes.io/limit-rps: "100"
    # Connection limits
    nginx.ingress.kubernetes.io/limit-connections: "10"
    # Custom headers
    nginx.ingress.kubernetes.io/configuration-snippet: |
      more_set_headers "X-Frame-Options: DENY";
      more_set_headers "X-Content-Type-Options: nosniff";
    # Authentication
    nginx.ingress.kubernetes.io/auth-type: basic
    nginx.ingress.kubernetes.io/auth-secret: basic-auth
    nginx.ingress.kubernetes.io/auth-realm: "Authentication Required"
    # Backend timeout
    nginx.ingress.kubernetes.io/proxy-connect-timeout: "600"
    nginx.ingress.kubernetes.io/proxy-send-timeout: "600"
    nginx.ingress.kubernetes.io/proxy-read-timeout: "600"
spec:
  ingressClassName: nginx
  tls:
  - hosts:
    - app.example.com
    - api.example.com
    secretName: tls-wildcard-secret
  rules:
  - host: app.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend-service
            port:
              number: 80
  - host: api.example.com
    http:
      paths:
      - path: /v1
        pathType: Prefix
        backend:
          service:
            name: api-v1-service
            port:
              number: 8080
      - path: /v2
        pathType: Prefix
        backend:
          service:
            name: api-v2-service
            port:
              number: 8080
```

### Default Backend

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: default-backend-ingress
spec:
  ingressClassName: nginx
  defaultBackend:
    service:
      name: default-backend-service
      port:
        number: 80
  rules:
  - host: app.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: app-service
            port:
              number: 80
```

### Ingress Commands

```bash
# Create ingress
kubectl apply -f ingress.yaml

# List ingresses
kubectl get ingress
kubectl get ing

# Describe ingress
kubectl describe ingress basic-ingress

# Get ingress details with address
kubectl get ingress basic-ingress -o wide

# Edit ingress
kubectl edit ingress basic-ingress

# Delete ingress
kubectl delete ingress basic-ingress

# View ingress controller logs
kubectl logs -n ingress-nginx deployment/ingress-nginx-controller
```

---

## Network Policies

### What are Network Policies?

**Network Policies** are firewall rules for pod-to-pod communication. They control traffic flow at the IP address or port level.

**Requirements**:
- Network plugin must support NetworkPolicy (Calico, Cilium, Weave Net)
- Default behavior: All pods can communicate (if no policies exist)
- Policies are additive (whitelist approach)

### Policy Types

| Type | Description |
|------|-------------|
| **Ingress** | Controls incoming traffic to pods |
| **Egress** | Controls outgoing traffic from pods |

### Default Deny All Traffic

```yaml
# Deny all ingress traffic
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
  namespace: production
spec:
  podSelector: {}    # Applies to all pods in namespace
  policyTypes:
  - Ingress
```

```yaml
# Deny all egress traffic
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-egress
  namespace: production
spec:
  podSelector: {}
  policyTypes:
  - Egress
```

```yaml
# Deny all ingress and egress
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-all
  namespace: production
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
```

### Allow Specific Ingress Traffic

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-backend
  namespace: production
spec:
  podSelector:
    matchLabels:
      app: backend        # Apply to backend pods
      tier: api
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend   # Allow from frontend pods
    ports:
    - protocol: TCP
      port: 8080
```

### Allow Specific Egress Traffic

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-backend-to-database
  namespace: production
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
  - Egress
  egress:
  - to:
    - podSelector:
        matchLabels:
          app: database
    ports:
    - protocol: TCP
      port: 5432
```

### Namespace-Based Policy

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-from-frontend-namespace
  namespace: backend
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes:
  - Ingress
  ingress:
  - from:
    # Allow from all pods in 'frontend' namespace
    - namespaceSelector:
        matchLabels:
          name: frontend
    ports:
    - protocol: TCP
      port: 8080
```

### IP Block-Based Policy

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-external-clients
  namespace: production
spec:
  podSelector:
    matchLabels:
      app: web
  policyTypes:
  - Ingress
  ingress:
  - from:
    # Allow from specific IP ranges
    - ipBlock:
        cidr: 192.168.1.0/24
        except:
        - 192.168.1.5/32
    ports:
    - protocol: TCP
      port: 80
```

### Complex Policy Example

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: api-network-policy
  namespace: production
spec:
  podSelector:
    matchLabels:
      app: api
      tier: backend
  policyTypes:
  - Ingress
  - Egress
  ingress:
  # Allow from frontend pods
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 8080
  # Allow from monitoring namespace
  - from:
    - namespaceSelector:
        matchLabels:
          name: monitoring
    - podSelector:
        matchLabels:
          app: prometheus
    ports:
    - protocol: TCP
      port: 9090
  egress:
  # Allow to database
  - to:
    - podSelector:
        matchLabels:
          app: database
    ports:
    - protocol: TCP
      port: 5432
  # Allow to cache
  - to:
    - podSelector:
        matchLabels:
          app: redis
    ports:
    - protocol: TCP
      port: 6379
  # Allow DNS
  - to:
    - namespaceSelector:
        matchLabels:
          name: kube-system
    - podSelector:
        matchLabels:
          k8s-app: kube-dns
    ports:
    - protocol: UDP
      port: 53
  # Allow external API calls
  - to:
    - ipBlock:
        cidr: 0.0.0.0/0
        except:
        - 10.0.0.0/8
        - 192.168.0.0/16
        - 172.16.0.0/12
    ports:
    - protocol: TCP
      port: 443
```

### Complete Multi-Tier Application with Network Policies

```yaml
# Namespace
apiVersion: v1
kind: Namespace
metadata:
  name: secure-app
  labels:
    name: secure-app
---
# Default deny all
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-all
  namespace: secure-app
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
---
# Frontend policy
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: frontend-policy
  namespace: secure-app
spec:
  podSelector:
    matchLabels:
      tier: frontend
  policyTypes:
  - Ingress
  - Egress
  ingress:
  # Accept traffic from anywhere (it's public)
  - from:
    - ipBlock:
        cidr: 0.0.0.0/0
    ports:
    - protocol: TCP
      port: 80
  egress:
  # Can call backend API
  - to:
    - podSelector:
        matchLabels:
          tier: backend
    ports:
    - protocol: TCP
      port: 8080
  # Allow DNS
  - to:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          k8s-app: kube-dns
    ports:
    - protocol: UDP
      port: 53
---
# Backend policy
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: backend-policy
  namespace: secure-app
spec:
  podSelector:
    matchLabels:
      tier: backend
  policyTypes:
  - Ingress
  - Egress
  ingress:
  # Accept from frontend only
  - from:
    - podSelector:
        matchLabels:
          tier: frontend
    ports:
    - protocol: TCP
      port: 8080
  egress:
  # Can access database
  - to:
    - podSelector:
        matchLabels:
          tier: database
    ports:
    - protocol: TCP
      port: 5432
  # Allow DNS
  - to:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          k8s-app: kube-dns
    ports:
    - protocol: UDP
      port: 53
  # Allow external HTTPS for third-party APIs
  - to:
    - ipBlock:
        cidr: 0.0.0.0/0
    ports:
    - protocol: TCP
      port: 443
---
# Database policy
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: database-policy
  namespace: secure-app
spec:
  podSelector:
    matchLabels:
      tier: database
  policyTypes:
  - Ingress
  - Egress
  ingress:
  # Accept from backend only
  - from:
    - podSelector:
        matchLabels:
          tier: backend
    ports:
    - protocol: TCP
      port: 5432
  egress:
  # Allow DNS only (database shouldn't need external access)
  - to:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          k8s-app: kube-dns
    ports:
    - protocol: UDP
      port: 53
```

### Network Policy Commands

```bash
# Create network policy
kubectl apply -f network-policy.yaml

# List network policies
kubectl get networkpolicies
kubectl get netpol

# Describe network policy
kubectl describe networkpolicy allow-frontend-to-backend

# Delete network policy
kubectl delete networkpolicy allow-frontend-to-backend

# Test connectivity (from one pod to another)
kubectl exec -it frontend-pod -- curl http://backend-service:8080
```

### Testing Network Policies

```bash
# Deploy test pods
kubectl run test-pod-1 --image=busybox --labels="app=test" -- sleep 3600
kubectl run test-pod-2 --image=busybox --labels="app=test" -- sleep 3600

# Test connectivity before policy
kubectl exec test-pod-1 -- wget -qO- --timeout=2 http://test-pod-2

# Apply deny-all policy
kubectl apply -f default-deny-all.yaml

# Test connectivity after policy (should fail)
kubectl exec test-pod-1 -- wget -qO- --timeout=2 http://test-pod-2

# Apply allow policy
kubectl apply -f allow-policy.yaml

# Test connectivity (should work now)
kubectl exec test-pod-1 -- wget -qO- --timeout=2 http://test-pod-2
```

---

## Best Practices

### Service Discovery
1. **Use DNS** - Prefer DNS over environment variables
2. **Fully qualified names** - Use full DNS names for cross-namespace communication
3. **Headless services** - For StatefulSets and direct pod access
4. **Service naming** - Use clear, consistent service names

### Ingress
1. **Single ingress controller** - One per cluster (or per environment)
2. **TLS everywhere** - Always use HTTPS in production
3. **Path-based routing** - Organize by API version or functionality
4. **Annotations** - Use for controller-specific features
5. **Rate limiting** - Protect backend services from abuse

### Network Policies
1. **Start with deny-all** - Default deny, then allow specific traffic
2. **Principle of least privilege** - Only allow necessary connections
3. **Label consistently** - Use clear labels for pod selection
4. **DNS access** - Always allow DNS (kube-dns/CoreDNS)
5. **Test thoroughly** - Verify connectivity after applying policies
6. **Document policies** - Explain the reasoning behind rules

### General Networking
1. **CNI plugin** - Choose one that supports your requirements
2. **Monitor traffic** - Use network monitoring tools
3. **Avoid pod IPs** - Always use Services
4. **Resource limits** - Prevent bandwidth monopolization
5. **Security** - Use Network Policies and Ingress TLS

---

## Summary

**Key Concepts**:

| Component | Purpose | Level |
|-----------|---------|-------|
| **Pod Networking** | Direct pod-to-pod communication | L3 |
| **Services** | Stable networking abstraction | L4 |
| **Ingress** | HTTP/HTTPS routing and load balancing | L7 |
| **Network Policies** | Pod traffic firewall rules | L3/L4 |
| **DNS** | Service discovery | Application |

**Remember**:
- All pods can communicate by default
- Services provide stable endpoints
- Ingress provides L7 routing
- Network Policies control traffic flow
- DNS is the preferred service discovery method

---

## Next Topic

Continue to [kubectl CLI Commands](./05-kubectl-cli.md) to master the Kubernetes command-line interface.
