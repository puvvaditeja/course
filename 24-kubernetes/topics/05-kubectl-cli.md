# kubectl CLI Commands

## Overview

`kubectl` is the command-line tool for interacting with Kubernetes clusters. It communicates with the Kubernetes API server to manage cluster resources, deploy applications, inspect cluster state, and troubleshoot issues.

---

## Installation and Setup

### Installing kubectl

#### macOS

```bash
# Using Homebrew
brew install kubectl

# Verify installation
kubectl version --client
```

#### Linux

```bash
# Download latest release
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

# Make it executable
chmod +x kubectl

# Move to PATH
sudo mv kubectl /usr/local/bin/

# Verify installation
kubectl version --client
```

#### Windows

```powershell
# Using Chocolatey
choco install kubernetes-cli

# Verify installation
kubectl version --client
```

### Configuration

kubectl uses a config file located at `~/.kube/config`:

```bash
# View current configuration
kubectl config view

# View current context
kubectl config current-context

# List all contexts
kubectl config get-contexts

# Switch context
kubectl config use-context minikube

# Set namespace for current context
kubectl config set-context --current --namespace=development
```

### Kubeconfig Structure

```yaml
apiVersion: v1
kind: Config
clusters:
- cluster:
    certificate-authority-data: <base64-cert>
    server: https://kubernetes.example.com:6443
  name: production-cluster
contexts:
- context:
    cluster: production-cluster
    namespace: default
    user: admin
  name: production
current-context: production
users:
- name: admin
  user:
    client-certificate-data: <base64-cert>
    client-key-data: <base64-key>
```

---

## Command Structure

### Basic Syntax

```bash
kubectl [command] [TYPE] [NAME] [flags]
```

**Components**:
- **command**: Operation (get, create, apply, delete, etc.)
- **TYPE**: Resource type (pod, service, deployment, etc.)
- **NAME**: Resource name (optional, omit to list all)
- **flags**: Additional options

### Examples

```bash
kubectl get pods                           # List all pods
kubectl get pod nginx-pod                  # Get specific pod
kubectl describe deployment webapp         # Describe a deployment
kubectl delete service my-service          # Delete a service
kubectl apply -f deployment.yaml           # Apply configuration
```

---

## Resource Management

### Creating Resources

#### Imperative (Command-line)

```bash
# Create a deployment
kubectl create deployment nginx --image=nginx:1.19

# Create a service
kubectl create service clusterip my-service --tcp=80:8080

# Create a namespace
kubectl create namespace development

# Create a configmap
kubectl create configmap app-config --from-literal=key=value

# Create a secret
kubectl create secret generic db-secret --from-literal=password=secret123

# Run a pod
kubectl run nginx --image=nginx:1.19

# Expose a deployment
kubectl expose deployment nginx --port=80 --type=LoadBalancer
```

#### Declarative (YAML files)

```bash
# Create from file
kubectl apply -f deployment.yaml

# Create from multiple files
kubectl apply -f deployment.yaml -f service.yaml

# Create from directory
kubectl apply -f ./manifests/

# Create from URL
kubectl apply -f https://example.com/deployment.yaml

# Create with dry-run (test without creating)
kubectl apply -f deployment.yaml --dry-run=client

# Create and save configuration
kubectl apply -f deployment.yaml --save-config
```

### Viewing Resources

#### Get Resources

```bash
# List resources
kubectl get pods
kubectl get services
kubectl get deployments
kubectl get all                    # All resources

# Wide output (more details)
kubectl get pods -o wide

# YAML output
kubectl get pod nginx -o yaml

# JSON output
kubectl get pod nginx -o json

# Custom columns
kubectl get pods -o custom-columns=NAME:.metadata.name,STATUS:.status.phase,IP:.status.podIP

# JSONPath query
kubectl get pods -o jsonpath='{.items[*].metadata.name}'

# Watch for changes
kubectl get pods -w

# All namespaces
kubectl get pods --all-namespaces
kubectl get pods -A

# Specific namespace
kubectl get pods -n production

# Show labels
kubectl get pods --show-labels

# Label selector
kubectl get pods -l app=nginx
kubectl get pods -l 'environment in (production,staging)'
```

#### Describe Resources

```bash
# Detailed information
kubectl describe pod nginx-pod
kubectl describe deployment webapp
kubectl describe node node-1

# Events at the end show recent activities
kubectl describe pod nginx-pod | grep Events -A 20
```

#### Output Formats

| Format | Usage |
|--------|-------|
| `-o wide` | Additional columns |
| `-o yaml` | YAML format |
| `-o json` | JSON format |
| `-o name` | Resource name only |
| `-o jsonpath` | Custom JSONPath query |
| `-o custom-columns` | Custom column output |

### Editing Resources

```bash
# Edit resource in default editor
kubectl edit deployment nginx-deployment

# Edit with specific editor
KUBE_EDITOR="nano" kubectl edit pod nginx-pod

# Replace resource from file
kubectl replace -f deployment.yaml

# Replace with force (delete and recreate)
kubectl replace -f deployment.yaml --force
```

### Updating Resources

```bash
# Update image
kubectl set image deployment/nginx nginx=nginx:1.20

# Update multiple containers
kubectl set image deployment/webapp web=webapp:2.0 cache=redis:6.0

# Update environment variable
kubectl set env deployment/webapp ENV=production

# Update resource limits
kubectl set resources deployment nginx --limits=cpu=200m,memory=512Mi --requests=cpu=100m,memory=256Mi

# Scale deployment
kubectl scale deployment nginx --replicas=5

# Autoscale deployment
kubectl autoscale deployment nginx --min=2 --max=10 --cpu-percent=80
```

### Deleting Resources

```bash
# Delete specific resource
kubectl delete pod nginx-pod
kubectl delete deployment webapp

# Delete from file
kubectl delete -f deployment.yaml

# Delete by label
kubectl delete pods -l app=nginx

# Delete all pods in namespace
kubectl delete pods --all

# Delete namespace (deletes all resources in it)
kubectl delete namespace development

# Force delete (immediate)
kubectl delete pod nginx-pod --force --grace-period=0

# Delete with cascade (default - deletes dependents)
kubectl delete deployment webapp --cascade=foreground

# Delete without cascade (keeps dependents)
kubectl delete replicaset nginx-rs --cascade=orphan
```

---

## Basic Operations

### Pod Operations

```bash
# Get pod logs
kubectl logs nginx-pod

# Stream logs (follow)
kubectl logs -f nginx-pod

# Logs from specific container (multi-container pod)
kubectl logs nginx-pod -c sidecar-container

# Previous container logs (after restart)
kubectl logs nginx-pod --previous

# Tail last N lines
kubectl logs nginx-pod --tail=50

# Logs since timestamp
kubectl logs nginx-pod --since=1h
kubectl logs nginx-pod --since-time=2024-01-01T10:00:00Z

# Execute command in pod
kubectl exec nginx-pod -- ls /usr/share/nginx/html

# Interactive shell
kubectl exec -it nginx-pod -- /bin/bash
kubectl exec -it nginx-pod -- sh

# Execute in specific container
kubectl exec -it nginx-pod -c sidecar -- /bin/bash

# Copy files to/from pod
kubectl cp ./local-file.txt nginx-pod:/tmp/file.txt
kubectl cp nginx-pod:/var/log/app.log ./app.log

# Port forward to local machine
kubectl port-forward pod/nginx-pod 8080:80
kubectl port-forward service/nginx-service 8080:80
kubectl port-forward deployment/nginx 8080:80

# Access pod over proxy
kubectl proxy
# Then access: http://localhost:8001/api/v1/namespaces/default/pods/nginx-pod/proxy/
```

### Deployment Operations

```bash
# Create deployment
kubectl create deployment nginx --image=nginx:1.19 --replicas=3

# View deployment
kubectl get deployment nginx

# View deployment history
kubectl rollout history deployment/nginx

# View specific revision
kubectl rollout history deployment/nginx --revision=2

# Update deployment (triggers rollout)
kubectl set image deployment/nginx nginx=nginx:1.20

# Check rollout status
kubectl rollout status deployment/nginx

# Pause rollout
kubectl rollout pause deployment/nginx

# Resume rollout
kubectl rollout resume deployment/nginx

# Undo rollout (rollback to previous)
kubectl rollout undo deployment/nginx

# Rollback to specific revision
kubectl rollout undo deployment/nginx --to-revision=2

# Restart deployment (recreate all pods)
kubectl rollout restart deployment/nginx

# Scale deployment
kubectl scale deployment nginx --replicas=5

# Record change cause (for rollout history)
kubectl set image deployment/nginx nginx=nginx:1.20 --record
```

### Service Operations

```bash
# Create service
kubectl expose deployment nginx --port=80 --target-port=80

# Get service endpoints
kubectl get endpoints nginx

# Get service details
kubectl describe service nginx

# Access service via proxy
kubectl proxy
# http://localhost:8001/api/v1/namespaces/default/services/nginx/proxy/
```

### Namespace Operations

```bash
# Create namespace
kubectl create namespace development

# List namespaces
kubectl get namespaces

# Set default namespace for context
kubectl config set-context --current --namespace=development

# Get resources in namespace
kubectl get pods -n production

# Get resources in all namespaces
kubectl get pods --all-namespaces
kubectl get pods -A

# Delete namespace
kubectl delete namespace development
```

---

## Debugging Commands

### Cluster Information

```bash
# Cluster information
kubectl cluster-info

# Cluster API versions
kubectl api-versions

# Available resource types
kubectl api-resources

# Node information
kubectl get nodes
kubectl describe node node-1

# Cluster events
kubectl get events
kubectl get events --sort-by='.lastTimestamp'

# Component status (deprecated in newer versions)
kubectl get componentstatuses
kubectl get cs
```

### Pod Troubleshooting

```bash
# Get pod status
kubectl get pod nginx-pod
kubectl get pod nginx-pod -o wide

# Detailed pod information
kubectl describe pod nginx-pod

# Pod logs
kubectl logs nginx-pod
kubectl logs nginx-pod -f
kubectl logs nginx-pod --previous

# Events related to pod
kubectl get events --field-selector involvedObject.name=nginx-pod

# Pod resource usage
kubectl top pod nginx-pod
kubectl top pods --all-namespaces

# Check pod YAML
kubectl get pod nginx-pod -o yaml

# Debugging with temporary pod
kubectl run debug-pod --image=busybox --rm -it -- sh

# Debug with specific network tools
kubectl run debug --image=nicolaka/netshoot --rm -it -- bash

# Attach to running container
kubectl attach nginx-pod -it
```

### Network Debugging

```bash
# Test DNS resolution
kubectl run test-dns --image=busybox --rm -it -- nslookup kubernetes.default

# Test service connectivity
kubectl run test-svc --image=busybox --rm -it -- wget -qO- http://nginx-service

# Check service endpoints
kubectl get endpoints nginx-service

# Describe service
kubectl describe service nginx-service

# Network policy check
kubectl get networkpolicies
kubectl describe networkpolicy allow-frontend

# Port forward for testing
kubectl port-forward pod/nginx-pod 8080:80
```

### Resource Issues

```bash
# Check resource usage
kubectl top nodes
kubectl top pods
kubectl top pods --containers

# View resource quotas
kubectl get resourcequota
kubectl describe resourcequota

# View limit ranges
kubectl get limitrange
kubectl describe limitrange

# Check pod resource requests/limits
kubectl get pod nginx-pod -o jsonpath='{.spec.containers[*].resources}'
```

### Common Issues

#### Pod Not Starting

```bash
# Check pod status
kubectl get pod problematic-pod

# Common statuses:
# - Pending: Waiting to be scheduled
# - ImagePullBackOff: Can't pull image
# - CrashLoopBackOff: Container keeps crashing
# - Error: Container exited with error

# Describe for details
kubectl describe pod problematic-pod

# Check events
kubectl get events --field-selector involvedObject.name=problematic-pod

# Check logs
kubectl logs problematic-pod
kubectl logs problematic-pod --previous
```

#### ImagePullBackOff

```bash
# Check image name and tag
kubectl get pod problematic-pod -o jsonpath='{.spec.containers[*].image}'

# Check image pull secrets
kubectl get pod problematic-pod -o jsonpath='{.spec.imagePullSecrets}'

# Describe pod for detailed error
kubectl describe pod problematic-pod | grep -A 10 Events
```

#### CrashLoopBackOff

```bash
# Check current logs
kubectl logs problematic-pod

# Check previous container logs
kubectl logs problematic-pod --previous

# Describe pod
kubectl describe pod problematic-pod

# Check container exit code
kubectl get pod problematic-pod -o jsonpath='{.status.containerStatuses[0].lastState.terminated.exitCode}'
```

#### Pending Pod

```bash
# Describe pod to see why it's pending
kubectl describe pod pending-pod

# Common reasons:
# - Insufficient resources (CPU/memory)
# - Node selector doesn't match
# - Taints/tolerations mismatch
# - PersistentVolumeClaim not available

# Check node resources
kubectl top nodes
kubectl describe nodes
```

---

## Advanced Commands

### Labels and Annotations

```bash
# Show labels
kubectl get pods --show-labels

# Add label
kubectl label pod nginx-pod environment=production

# Update label
kubectl label pod nginx-pod environment=staging --overwrite

# Remove label
kubectl label pod nginx-pod environment-

# Label multiple resources
kubectl label pods --all environment=production

# Filter by label
kubectl get pods -l environment=production
kubectl get pods -l 'environment in (production,staging)'
kubectl get pods -l environment!=development

# Add annotation
kubectl annotate pod nginx-pod description="Main web server"

# Remove annotation
kubectl annotate pod nginx-pod description-
```

### Contexts and Namespaces

```bash
# View contexts
kubectl config get-contexts

# Switch context
kubectl config use-context production

# Set namespace for context
kubectl config set-context --current --namespace=production

# Create context
kubectl config set-context dev-context --cluster=dev-cluster --user=dev-user --namespace=development

# Delete context
kubectl config delete-context dev-context

# Rename context
kubectl config rename-context old-name new-name
```

### Resource Management

```bash
# Drain node (for maintenance)
kubectl drain node-1 --ignore-daemonsets --delete-emptydir-data

# Cordon node (mark unschedulable)
kubectl cordon node-1

# Uncordon node
kubectl uncordon node-1

# Taint node
kubectl taint nodes node-1 key=value:NoSchedule

# Remove taint
kubectl taint nodes node-1 key=value:NoSchedule-

# Set resource quota
kubectl create quota dev-quota --hard=cpu=10,memory=20Gi,pods=50 -n development
```

### Patch Resources

```bash
# Strategic merge patch
kubectl patch deployment nginx -p '{"spec":{"replicas":5}}'

# JSON patch
kubectl patch pod nginx -p '[{"op":"replace","path":"/spec/containers/0/image","value":"nginx:1.20"}]' --type=json

# Merge patch
kubectl patch service nginx -p '{"spec":{"type":"LoadBalancer"}}' --type=merge
```

### Diff and Apply

```bash
# See differences before applying
kubectl diff -f deployment.yaml

# Apply changes
kubectl apply -f deployment.yaml

# Server-side apply (recommended for newer versions)
kubectl apply -f deployment.yaml --server-side

# Prune resources not in files
kubectl apply -f ./manifests/ --prune -l app=myapp
```

---

## Productivity Tips

### Aliases

Add to `~/.bashrc` or `~/.zshrc`:

```bash
alias k=kubectl
alias kg='kubectl get'
alias kd='kubectl describe'
alias kdel='kubectl delete'
alias kl='kubectl logs'
alias kex='kubectl exec -it'
alias kaf='kubectl apply -f'

# Common operations
alias kgp='kubectl get pods'
alias kgd='kubectl get deployments'
alias kgs='kubectl get services'
alias kgn='kubectl get nodes'

# With watch
alias kgpw='kubectl get pods -w'

# All namespaces
alias kgpa='kubectl get pods --all-namespaces'
```

### Bash Completion

```bash
# Enable kubectl completion for bash
echo 'source <(kubectl completion bash)' >> ~/.bashrc

# Enable for zsh
echo 'source <(kubectl completion zsh)' >> ~/.zshrc

# If using alias 'k'
echo 'complete -F __start_kubectl k' >> ~/.bashrc
```

### Useful Shortcuts

```bash
# Short names for resources
kubectl get po              # pods
kubectl get svc             # services
kubectl get deploy          # deployments
kubectl get rs              # replicasets
kubectl get cm              # configmaps
kubectl get ns              # namespaces
kubectl get no              # nodes
kubectl get ing             # ingress

# Get multiple resource types
kubectl get pods,services,deployments

# Use JSONPath for specific fields
kubectl get pods -o jsonpath='{.items[*].metadata.name}'
kubectl get pods -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.status.phase}{"\n"}{end}'

# Quick pod creation for testing
kubectl run test --image=busybox --rm -it --restart=Never -- sh

# Generate YAML without creating
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml
kubectl run nginx --image=nginx --dry-run=client -o yaml

# Explain resource fields
kubectl explain pod
kubectl explain pod.spec.containers
kubectl explain deployment.spec.strategy
```

### kubectl Plugins

```bash
# Install krew (plugin manager)
# https://krew.sigs.k8s.io/docs/user-guide/setup/install/

# Useful plugins
kubectl krew install ctx      # Switch contexts
kubectl krew install ns       # Switch namespaces
kubectl krew install tree     # Show resource hierarchy
kubectl krew install tail     # Tail logs from multiple pods
kubectl krew install exec-all # Execute command in all pods
```

---

## Cheat Sheet

### Resource Types (Short Names)

| Resource | Short Name |
|----------|------------|
| pods | po |
| services | svc |
| deployments | deploy |
| replicasets | rs |
| statefulsets | sts |
| daemonsets | ds |
| namespaces | ns |
| nodes | no |
| configmaps | cm |
| secrets | secret |
| ingresses | ing |
| persistentvolumes | pv |
| persistentvolumeclaims | pvc |
| serviceaccounts | sa |
| networkpolicies | netpol |

### Essential Commands Quick Reference

```bash
# Cluster Info
kubectl cluster-info
kubectl get nodes
kubectl get namespaces

# Create Resources
kubectl create deployment nginx --image=nginx
kubectl run nginx --image=nginx
kubectl apply -f file.yaml

# View Resources
kubectl get pods
kubectl get pods -o wide
kubectl describe pod nginx
kubectl get pods -w
kubectl get all

# Update Resources
kubectl edit deployment nginx
kubectl set image deployment/nginx nginx=nginx:1.20
kubectl scale deployment nginx --replicas=3
kubectl rollout restart deployment/nginx

# Delete Resources
kubectl delete pod nginx
kubectl delete -f file.yaml
kubectl delete pods --all

# Debugging
kubectl logs nginx
kubectl logs -f nginx
kubectl exec -it nginx -- bash
kubectl describe pod nginx
kubectl get events

# Port Forwarding
kubectl port-forward pod/nginx 8080:80

# Context/Namespace
kubectl config use-context prod
kubectl config set-context --current --namespace=dev
kubectl get pods -n production
kubectl get pods -A
```

---

## Best Practices

### General
1. **Use declarative approach** - Prefer `kubectl apply` over imperative commands
2. **Version control** - Store YAML files in Git
3. **Namespace everything** - Use namespaces to organize resources
4. **Label consistently** - Use meaningful labels for all resources
5. **Dry-run first** - Test with `--dry-run=client` before applying

### Resource Management
1. **Set resource limits** - Always specify requests and limits
2. **Use liveness/readiness probes** - Ensure healthy pods
3. **Version images** - Never use `latest` tag in production
4. **Record changes** - Use `--record` flag for deployments
5. **Rollout carefully** - Check status before proceeding

### Security
1. **RBAC** - Use role-based access control
2. **Least privilege** - Give minimum necessary permissions
3. **Network policies** - Restrict pod-to-pod communication
4. **Secrets management** - Never commit secrets to version control
5. **Pod security** - Use Pod Security Standards

### Debugging
1. **Check events first** - Often shows the root cause
2. **Use describe** - Shows detailed information and recent events
3. **Check logs** - Both current and previous containers
4. **Resource usage** - Check with `kubectl top`
5. **Test connectivity** - Use temporary debug pods

---

## Summary

**Key Command Categories**:

| Category | Commands |
|----------|----------|
| **Cluster Management** | cluster-info, config, api-resources |
| **Resource Creation** | create, run, apply, expose |
| **Resource Viewing** | get, describe, logs |
| **Resource Updating** | edit, set, scale, patch |
| **Resource Deletion** | delete |
| **Debugging** | logs, exec, describe, events, top |
| **Rollout Management** | rollout status/history/undo/restart |

**Remember**:
- `kubectl get` - List resources
- `kubectl describe` - Detailed information
- `kubectl logs` - Container logs
- `kubectl exec` - Execute commands in pods
- `kubectl apply` - Create/update resources
- `kubectl delete` - Remove resources

---

## Additional Resources

### Official Documentation
- [kubectl Reference](https://kubernetes.io/docs/reference/kubectl/)
- [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
- [kubectl Commands](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)

### Practice
- [Kubernetes Playground](https://www.katacoda.com/courses/kubernetes/playground)
- [Play with Kubernetes](https://labs.play-with-k8s.com/)

### Tools
- [k9s](https://k9scli.io/) - Terminal UI for Kubernetes
- [lens](https://k8slens.dev/) - Kubernetes IDE
- [krew](https://krew.sigs.k8s.io/) - Plugin manager

---

**Congratulations!** You've completed the Kubernetes topics. Practice these commands regularly to build proficiency with Kubernetes cluster management.
