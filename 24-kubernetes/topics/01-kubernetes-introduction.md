# Kubernetes Introduction

## Overview

Kubernetes (K8s) is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications. Originally developed by Google and now maintained by the Cloud Native Computing Foundation (CNCF), Kubernetes has become the de facto standard for container orchestration.

---

## What is Container Orchestration?

### The Problem

As applications grow and adopt microservices architecture, managing containers at scale becomes complex:

- **Manual container management** is time-consuming and error-prone
- **Scaling** containers up or down based on demand requires automation
- **Service discovery** becomes difficult when containers are spread across multiple hosts
- **Load balancing** between container instances needs coordination
- **Health monitoring** and automatic recovery of failed containers is essential
- **Rolling updates** and rollbacks need to be seamless

### The Solution: Container Orchestration

Container orchestration platforms like Kubernetes provide:

1. **Automated Deployment**: Deploy containers across a cluster of machines
2. **Auto-scaling**: Automatically scale applications based on resource usage or custom metrics
3. **Self-healing**: Restart failed containers, replace containers, kill unresponsive containers
4. **Load Balancing**: Distribute network traffic to maintain stability
5. **Service Discovery**: Automatically discover and connect services
6. **Configuration Management**: Manage secrets and configuration without rebuilding images
7. **Storage Orchestration**: Automatically mount storage systems (local, cloud, network)
8. **Batch Execution**: Manage batch and CI workloads

### Why Kubernetes?

| Feature | Benefit |
|---------|---------|
| **Portability** | Run on any infrastructure (cloud, on-premise, hybrid) |
| **Scalability** | Scale from a few containers to thousands |
| **High Availability** | Ensure applications are always running |
| **Declarative Configuration** | Define desired state, Kubernetes maintains it |
| **Extensibility** | Rich ecosystem of tools and extensions |
| **Community Support** | Large community, extensive documentation |

---

## Kubernetes Architecture

Kubernetes follows a client-server architecture with a clear separation between control plane components and worker nodes.

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     KUBERNETES CLUSTER                       │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌────────────────────────────────────────────────────┐    │
│  │              CONTROL PLANE (Master)                │    │
│  │  ┌──────────┐  ┌──────────┐  ┌────────────────┐  │    │
│  │  │   API    │  │  etcd    │  │   Scheduler    │  │    │
│  │  │  Server  │  │          │  │                │  │    │
│  │  └──────────┘  └──────────┘  └────────────────┘  │    │
│  │  ┌──────────────────────────────────────────────┐ │    │
│  │  │      Controller Manager                      │ │    │
│  │  └──────────────────────────────────────────────┘ │    │
│  │  ┌──────────────────────────────────────────────┐ │    │
│  │  │      Cloud Controller Manager (Optional)     │ │    │
│  │  └──────────────────────────────────────────────┘ │    │
│  └────────────────────────────────────────────────────┘    │
│                                                              │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │   WORKER    │  │   WORKER    │  │   WORKER    │        │
│  │    NODE     │  │    NODE     │  │    NODE     │        │
│  │             │  │             │  │             │        │
│  │  ┌───────┐  │  │  ┌───────┐  │  │  ┌───────┐  │        │
│  │  │kubelet│  │  │  │kubelet│  │  │  │kubelet│  │        │
│  │  └───────┘  │  │  └───────┘  │  │  └───────┘  │        │
│  │  ┌───────┐  │  │  ┌───────┐  │  │  ┌───────┐  │        │
│  │  │ kube- │  │  │  │ kube- │  │  │  │ kube- │  │        │
│  │  │ proxy │  │  │  │ proxy │  │  │  │ proxy │  │        │
│  │  └───────┘  │  │  └───────┘  │  │  └───────┘  │        │
│  │  ┌───────┐  │  │  ┌───────┐  │  │  ┌───────┐  │        │
│  │  │Container│ │  │  │Container│ │  │  │Container│ │        │
│  │  │Runtime │  │  │  │Runtime │  │  │  │Runtime │  │        │
│  │  └───────┘  │  │  └───────┘  │  │  └───────┘  │        │
│  │             │  │             │  │             │        │
│  │  [Pods...]  │  │  [Pods...]  │  │  [Pods...]  │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## Master Node (Control Plane)

The control plane manages the Kubernetes cluster, making global decisions about the cluster and detecting/responding to cluster events.

### 1. API Server (kube-apiserver)

**Role**: The front-end for the Kubernetes control plane

**Responsibilities**:
- Exposes the Kubernetes API (RESTful interface)
- All cluster operations go through the API server
- Validates and processes REST requests
- Updates etcd with cluster state
- Acts as the gateway to the cluster

**Key Points**:
- Only component that communicates with etcd
- Horizontally scalable
- Stateless - all state stored in etcd

```bash
# Example: API server interaction via kubectl
kubectl get pods --v=8  # Shows API calls made to the API server
```

### 2. etcd

**Role**: Distributed key-value store for cluster state

**Responsibilities**:
- Stores all cluster data (configuration, state, metadata)
- Single source of truth for cluster state
- Provides reliable data storage with strong consistency
- Supports watch operations for real-time updates

**Key Points**:
- Distributed and highly available
- Critical component - cluster cannot function without it
- Should be backed up regularly
- Uses Raft consensus algorithm

**Data Stored**:
- Cluster configuration
- Current state of all resources
- Secrets and ConfigMaps
- Service account tokens

### 3. Scheduler (kube-scheduler)

**Role**: Assigns pods to nodes

**Responsibilities**:
- Watches for newly created pods with no assigned node
- Selects optimal node for each pod based on:
  - Resource requirements (CPU, memory)
  - Hardware/software/policy constraints
  - Affinity and anti-affinity specifications
  - Data locality
  - Resource availability
  - Quality of Service (QoS) requirements

**Scheduling Process**:
1. **Filtering**: Find all nodes that can run the pod
2. **Scoring**: Rank filtered nodes to find the best fit
3. **Binding**: Assign pod to the highest-scored node

```yaml
# Example: Pod with scheduling constraints
apiVersion: v1
kind: Pod
metadata:
  name: scheduled-pod
spec:
  containers:
  - name: nginx
    image: nginx
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
  nodeSelector:
    disktype: ssd
```

### 4. Controller Manager (kube-controller-manager)

**Role**: Runs controller processes that regulate cluster state

**Responsibilities**:
- Watches the cluster state through the API server
- Makes changes to move current state toward desired state
- Runs multiple controllers in a single process

**Key Controllers**:

| Controller | Purpose |
|------------|---------|
| **Node Controller** | Monitors node health, responds to node failures |
| **Replication Controller** | Maintains correct number of pods for ReplicaSets |
| **Endpoints Controller** | Populates Endpoints objects (joins Services & Pods) |
| **Service Account & Token Controllers** | Creates default accounts and API access tokens |
| **Deployment Controller** | Manages deployment rollouts and rollbacks |
| **StatefulSet Controller** | Manages stateful applications |
| **Job Controller** | Manages job execution |
| **CronJob Controller** | Manages time-based jobs |

**Control Loop Pattern**:
```
1. Read desired state from API server
2. Read current state
3. Make changes to match desired state
4. Update current state via API server
5. Repeat
```

### 5. Cloud Controller Manager (cloud-controller-manager)

**Role**: Integrates with cloud provider APIs (AWS, Azure, GCP)

**Responsibilities**:
- **Node Controller**: Checks if a node has been deleted in the cloud
- **Route Controller**: Sets up routes in cloud infrastructure
- **Service Controller**: Creates/deletes cloud load balancers
- **Volume Controller**: Creates/attaches/mounts cloud volumes

**Key Points**:
- Only runs when using cloud providers
- Allows cloud-specific logic to stay separate from core Kubernetes
- Enables portability across cloud providers

---

## Worker Nodes

Worker nodes run the actual application workloads in the form of pods.

### Components on Every Worker Node

#### 1. Kubelet

**Role**: Primary node agent that ensures containers are running in pods

**Responsibilities**:
- Registers node with the API server
- Watches for pod assignments from the scheduler
- Ensures containers in assigned pods are running and healthy
- Reports pod status and node status to the API server
- Executes container liveness and readiness probes
- Mounts volumes for pods

**Key Points**:
- Runs on every node (including master nodes in some setups)
- Does not manage containers not created by Kubernetes
- Works with container runtime via CRI (Container Runtime Interface)

```yaml
# Kubelet configuration example
apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
address: 0.0.0.0
port: 10250
authentication:
  anonymous:
    enabled: false
  webhook:
    enabled: true
```

#### 2. Kube-proxy

**Role**: Network proxy that maintains network rules on nodes

**Responsibilities**:
- Implements Kubernetes Service concept
- Maintains network rules for pod communication
- Handles load balancing across pods in a service
- Manages network forwarding rules (iptables/ipvs)
- Enables service discovery within the cluster

**Proxy Modes**:

| Mode | Description | Performance |
|------|-------------|-------------|
| **iptables** | Uses iptables rules for routing | Good for small-medium clusters |
| **ipvs** | Uses IP Virtual Server for routing | Better for large clusters |
| **userspace** | Legacy mode, routes traffic in userspace | Slower, rarely used |

**How it works**:
```
Client Pod → Service IP → kube-proxy → Backend Pod
```

#### 3. Container Runtime

**Role**: Software responsible for running containers

**Responsibilities**:
- Pull container images from registries
- Run and stop containers
- Manage container lifecycle
- Provide container isolation

**Supported Runtimes**:
- **containerd** (most common, Docker's core runtime)
- **CRI-O** (lightweight alternative)
- **Docker** (deprecated in K8s 1.24+, but still works via containerd)

**Container Runtime Interface (CRI)**:
- Standard interface between kubelet and container runtime
- Allows pluggable runtimes
- Ensures compatibility across different runtime implementations

---

## Communication Flow

### Pod Creation Flow

```
1. User → kubectl create pod → API Server
2. API Server → Validates request → Stores in etcd
3. Scheduler → Watches API Server → Detects unscheduled pod
4. Scheduler → Selects node → Updates API Server
5. Kubelet (on selected node) → Watches API Server → Detects pod assignment
6. Kubelet → Calls Container Runtime → Pulls image and starts container
7. Kubelet → Reports status → API Server → Updates etcd
```

### Service Request Flow

```
1. Client Pod → Makes request to Service IP
2. kube-proxy (iptables/ipvs rules) → Intercepts request
3. kube-proxy → Load balances → Routes to backend Pod
4. Backend Pod → Processes request → Returns response
```

---

## Cluster Types

### Development/Learning Clusters

| Tool | Description | Use Case |
|------|-------------|----------|
| **Minikube** | Single-node cluster on local machine | Local development, learning |
| **kind** | Kubernetes in Docker | Testing, CI/CD pipelines |
| **Docker Desktop** | Built-in Kubernetes | Simple local development |
| **k3s** | Lightweight Kubernetes | Edge computing, IoT, dev |

### Production Clusters

| Platform | Description | Best For |
|----------|-------------|----------|
| **EKS** (AWS) | Managed Kubernetes on AWS | AWS-based infrastructure |
| **GKE** (Google Cloud) | Managed Kubernetes on GCP | Google Cloud users |
| **AKS** (Azure) | Managed Kubernetes on Azure | Microsoft Azure users |
| **OpenShift** | Enterprise Kubernetes platform | Enterprise environments |
| **Rancher** | Multi-cluster management | Managing multiple clusters |

---

## Key Concepts Summary

### Control Plane vs Data Plane

| Control Plane | Data Plane |
|---------------|------------|
| Makes decisions about the cluster | Runs the actual workloads |
| API Server, Scheduler, Controllers | Kubelet, Container Runtime, kube-proxy |
| Stores state in etcd | Executes pods on worker nodes |
| Should be highly available | Can scale horizontally |

### Desired State vs Current State

**Kubernetes Philosophy**:
- You declare the **desired state** (how you want things to be)
- Kubernetes continuously works to maintain that state
- Controllers reconcile current state with desired state

```yaml
# Desired State Declaration
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3  # Desired state: 3 running pods
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.19
```

Kubernetes will:
1. Ensure 3 pods are always running
2. Restart failed pods
3. Reschedule pods if nodes fail
4. Maintain this state continuously

---

## Cluster Setup Example (Minikube)

### Installation

```bash
# Install Minikube (macOS)
brew install minikube

# Install Minikube (Linux)
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Install kubectl (if not already installed)
brew install kubectl  # macOS
# or
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install kubectl /usr/local/bin/kubectl  # Linux
```

### Starting a Cluster

```bash
# Start a local Kubernetes cluster
minikube start

# Check cluster status
minikube status

# View cluster info
kubectl cluster-info

# Get nodes in the cluster
kubectl get nodes

# Access Kubernetes dashboard
minikube dashboard
```

### Basic Cluster Operations

```bash
# Stop the cluster
minikube stop

# Delete the cluster
minikube delete

# SSH into the cluster node
minikube ssh

# Check cluster resource usage
minikube kubectl -- top nodes
```

---

## Best Practices

### Control Plane

1. **High Availability**: Run multiple master nodes (typically 3 or 5)
2. **Backup etcd**: Regular backups of etcd data
3. **Secure API Server**: Use RBAC, enable audit logging
4. **Resource Isolation**: Run control plane on dedicated nodes

### Worker Nodes

1. **Right-sizing**: Choose appropriate node sizes for workloads
2. **Node Pools**: Use different node types for different workload types
3. **Auto-scaling**: Enable cluster autoscaler for dynamic scaling
4. **Regular Updates**: Keep node OS and Kubernetes versions updated

### Security

1. **Network Policies**: Restrict pod-to-pod communication
2. **RBAC**: Implement role-based access control
3. **Pod Security**: Use Pod Security Standards
4. **Secrets Management**: Never store secrets in plain text
5. **Image Security**: Scan images for vulnerabilities

---

## Common Issues and Troubleshooting

### Node Not Ready

```bash
# Check node status
kubectl get nodes
kubectl describe node <node-name>

# Common causes:
# - kubelet not running
# - Network plugin issues
# - Insufficient resources
# - Container runtime issues
```

### API Server Unreachable

```bash
# Check API server status
kubectl cluster-info
kubectl get --raw /healthz

# Common causes:
# - Network connectivity issues
# - API server not running
# - Certificate issues
# - Firewall blocking access
```

### etcd Issues

```bash
# Check etcd health (if you have access to master node)
ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  endpoint health

# Common causes:
# - Disk space issues
# - Corruption
# - Network issues between etcd members
```

---

## Summary

**Key Takeaways**:

1. **Container Orchestration** automates deployment, scaling, and management of containerized applications
2. **Kubernetes Architecture** consists of:
   - **Control Plane**: API Server, etcd, Scheduler, Controller Manager
   - **Worker Nodes**: kubelet, kube-proxy, Container Runtime
3. **Master Nodes** manage the cluster state and make scheduling decisions
4. **Worker Nodes** run the actual application workloads
5. **Communication** flows through the API Server, which is the central hub
6. **Declarative Approach**: Define desired state, Kubernetes maintains it

---

## Next Topic

Continue to [Kubernetes Objects](./02-kubernetes-objects.md) to learn about Pods, Deployments, Services, and other core Kubernetes resources.
