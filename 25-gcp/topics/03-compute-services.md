# GCP Compute Services

## Overview of Compute Services

Google Cloud Platform offers multiple compute options to run your applications, each optimized for different use cases:

| Service | Type | Management | Best For |
|---------|------|------------|----------|
| **Compute Engine** | IaaS | You manage VMs | Full control, custom software, lift-and-shift |
| **Kubernetes Engine (GKE)** | CaaS | Managed Kubernetes | Containerized microservices, hybrid cloud |
| **App Engine** | PaaS | Fully managed | Web apps, mobile backends, simple scaling |
| **Cloud Run** | Serverless | Fully managed | Stateless containers, microservices, APIs |
| **Cloud Functions** | FaaS | Fully managed | Event-driven, short tasks, integrations |

**Choosing the Right Service**:

```
Need full VM control? → Compute Engine
Using containers + orchestration? → GKE
Simple web app/API? → App Engine or Cloud Run
Event-driven functions? → Cloud Functions
```

---

## Compute Engine (Virtual Machines)

Compute Engine provides scalable, high-performance virtual machines running on Google's infrastructure.

### Machine Types

#### Predefined Machine Types

```bash
# General-purpose machines
E2 - Cost-optimized, 2-32 vCPUs, 0.5-8 GB RAM per vCPU
N2 - Balanced, 2-80 vCPUs, 0.5-8 GB RAM per vCPU
N2D - AMD-based, 2-224 vCPUs, cost-effective
N1 - First-generation, 1-96 vCPUs

# Compute-optimized
C2 - High performance, 4-60 vCPUs, 4 GB RAM per vCPU
C2D - AMD-based compute, 2-112 vCPUs

# Memory-optimized
M2 - Ultra-high memory, 208-416 vCPUs, 5.9 TB RAM
M1 - High memory, 40-160 vCPUs, up to 3.8 TB RAM

# Accelerator-optimized (GPUs)
A2 - For ML/AI workloads with NVIDIA A100 GPUs

# List available machine types
gcloud compute machine-types list --filter="zone:us-central1-a"

# Filter by criteria
gcloud compute machine-types list \
  --filter="zone:us-central1-a AND guestCpus>=4 AND memoryMb<=16384" \
  --sort-by="~guestCpus"
```

#### Custom Machine Types

```bash
# Create custom machine (specify vCPUs and memory)
gcloud compute instances create custom-vm \
  --custom-cpu=4 \
  --custom-memory=8GB \
  --zone=us-central1-a
```

### Creating Virtual Machines

#### Basic VM Creation

```bash
# Create a basic VM
gcloud compute instances create my-vm \
  --machine-type=e2-medium \
  --zone=us-central1-a

# Create VM with specific OS
gcloud compute instances create ubuntu-vm \
  --machine-type=e2-medium \
  --zone=us-central1-a \
  --image-family=ubuntu-2204-lts \
  --image-project=ubuntu-os-cloud

# Create VM with specific disk size
gcloud compute instances create large-disk-vm \
  --machine-type=e2-medium \
  --zone=us-central1-a \
  --boot-disk-size=100GB \
  --boot-disk-type=pd-standard

# List available image families
gcloud compute images list
gcloud compute images list --project=ubuntu-os-cloud
gcloud compute images list --project=debian-cloud
```

#### Advanced VM Creation

```bash
# Create VM with startup script
gcloud compute instances create web-server \
  --machine-type=e2-medium \
  --zone=us-central1-a \
  --tags=http-server,https-server \
  --metadata=startup-script='#!/bin/bash
    apt-get update
    apt-get install -y nginx
    systemctl start nginx
    echo "Hello from $(hostname)" > /var/www/html/index.html'

# Create VM with startup script file
gcloud compute instances create app-server \
  --machine-type=e2-medium \
  --zone=us-central1-a \
  --metadata-from-file=startup-script=startup.sh

# Create VM with service account
gcloud compute instances create secure-vm \
  --machine-type=e2-medium \
  --zone=us-central1-a \
  --service-account=my-app@PROJECT_ID.iam.gserviceaccount.com \
  --scopes=cloud-platform

# Create preemptible VM (80% cheaper, can be terminated)
gcloud compute instances create preemptible-vm \
  --machine-type=e2-medium \
  --zone=us-central1-a \
  --preemptible

# Create spot VM (successor to preemptible)
gcloud compute instances create spot-vm \
  --machine-type=e2-medium \
  --zone=us-central1-a \
  --provisioning-model=SPOT

# Create VM with static IP
gcloud compute addresses create my-static-ip --region=us-central1
gcloud compute instances create vm-with-static-ip \
  --machine-type=e2-medium \
  --zone=us-central1-a \
  --address=my-static-ip
```

### Managing Virtual Machines

```bash
# List all VMs
gcloud compute instances list

# List VMs in specific zone
gcloud compute instances list --filter="zone:us-central1-a"

# Get VM details
gcloud compute instances describe my-vm --zone=us-central1-a

# Start VM
gcloud compute instances start my-vm --zone=us-central1-a

# Stop VM
gcloud compute instances stop my-vm --zone=us-central1-a

# Restart VM
gcloud compute instances reset my-vm --zone=us-central1-a

# Delete VM
gcloud compute instances delete my-vm --zone=us-central1-a

# SSH into VM
gcloud compute ssh my-vm --zone=us-central1-a

# SSH with specific user
gcloud compute ssh username@my-vm --zone=us-central1-a

# Copy files to VM
gcloud compute scp local-file.txt my-vm:~/remote-file.txt --zone=us-central1-a

# Copy files from VM
gcloud compute scp my-vm:~/remote-file.txt ./local-file.txt --zone=us-central1-a
```

### VM Metadata and Customization

```bash
# Add metadata to existing VM
gcloud compute instances add-metadata my-vm \
  --zone=us-central1-a \
  --metadata=environment=production,team=backend

# Add labels to VM
gcloud compute instances add-labels my-vm \
  --zone=us-central1-a \
  --labels=env=prod,app=web

# Update machine type (VM must be stopped)
gcloud compute instances stop my-vm --zone=us-central1-a
gcloud compute instances set-machine-type my-vm \
  --zone=us-central1-a \
  --machine-type=e2-standard-4
gcloud compute instances start my-vm --zone=us-central1-a

# Attach additional disk
gcloud compute disks create my-data-disk \
  --size=100GB \
  --zone=us-central1-a
gcloud compute instances attach-disk my-vm \
  --disk=my-data-disk \
  --zone=us-central1-a

# Detach disk
gcloud compute instances detach-disk my-vm \
  --disk=my-data-disk \
  --zone=us-central1-a
```

### Instance Groups and Auto-scaling

```bash
# Create instance template
gcloud compute instance-templates create web-template \
  --machine-type=e2-medium \
  --tags=http-server \
  --metadata=startup-script='#!/bin/bash
    apt-get update
    apt-get install -y nginx
    systemctl start nginx'

# Create managed instance group
gcloud compute instance-groups managed create web-group \
  --base-instance-name=web \
  --template=web-template \
  --size=3 \
  --zone=us-central1-a

# Create autoscaler
gcloud compute instance-groups managed set-autoscaling web-group \
  --zone=us-central1-a \
  --min-num-replicas=2 \
  --max-num-replicas=10 \
  --target-cpu-utilization=0.6 \
  --cool-down-period=90

# List instances in group
gcloud compute instance-groups managed list-instances web-group \
  --zone=us-central1-a

# Resize instance group
gcloud compute instance-groups managed resize web-group \
  --size=5 \
  --zone=us-central1-a

# Update instance template (rolling update)
gcloud compute instance-templates create web-template-v2 \
  --machine-type=e2-standard-2 \
  --tags=http-server

gcloud compute instance-groups managed rolling-action start-update web-group \
  --version=template=web-template-v2 \
  --zone=us-central1-a
```

---

## App Engine

App Engine is a fully managed platform for building and hosting web applications at scale.

### App Engine Environments

#### Standard Environment
- Fully managed, auto-scaling
- Faster scaling (milliseconds)
- Free tier available
- Language runtimes: Python, Java, Node.js, Go, PHP, Ruby

#### Flexible Environment
- Runs in Docker containers
- More flexibility, custom runtimes
- Slower scaling (minutes)
- Support for any language

### Deploying to App Engine

#### Python Application

```bash
# Create application structure
mkdir my-app && cd my-app

# Create main.py
cat > main.py << 'EOF'
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello from App Engine!'

@app.route('/api/health')
def health():
    return {'status': 'healthy'}

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080)
EOF

# Create requirements.txt
cat > requirements.txt << EOF
Flask==2.3.0
gunicorn==20.1.0
EOF

# Create app.yaml (Standard Environment)
cat > app.yaml << EOF
runtime: python39
entrypoint: gunicorn -b :$PORT main:app

env_variables:
  DATABASE_URL: "your-connection-string"

handlers:
- url: /static
  static_dir: static
- url: /.*
  script: auto

automatic_scaling:
  min_instances: 1
  max_instances: 10
  target_cpu_utilization: 0.65
EOF

# Initialize App Engine (first time only)
gcloud app create --region=us-central

# Deploy application
gcloud app deploy

# View application
gcloud app browse

# View logs
gcloud app logs tail -s default

# View app details
gcloud app describe
```

#### Node.js Application

```bash
# Create package.json
cat > package.json << 'EOF'
{
  "name": "my-app",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.18.0"
  }
}
EOF

# Create server.js
cat > server.js << 'EOF'
const express = require('express');
const app = express();
const PORT = process.env.PORT || 8080;

app.get('/', (req, res) => {
  res.send('Hello from App Engine!');
});

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
EOF

# Create app.yaml
cat > app.yaml << EOF
runtime: nodejs18
env_variables:
  NODE_ENV: "production"
EOF

# Deploy
gcloud app deploy
```

### App Engine Management

```bash
# Deploy specific version
gcloud app deploy --version=v2 --no-promote

# List versions
gcloud app versions list

# Split traffic between versions (A/B testing)
gcloud app services set-traffic default \
  --splits=v1=0.5,v2=0.5

# Migrate all traffic to new version
gcloud app services set-traffic default --splits=v2=1

# Delete old version
gcloud app versions delete v1

# View instances
gcloud app instances list

# SSH into instance
gcloud app instances ssh INSTANCE_ID --service=default --version=v1

# Set scaling
gcloud app deploy --no-promote
gcloud app versions update v1 \
  --min-instances=2 \
  --max-instances=20
```

---

## Kubernetes Engine (GKE)

GKE is a managed Kubernetes service for deploying, managing, and scaling containerized applications.

### Creating GKE Clusters

```bash
# Create basic cluster
gcloud container clusters create my-cluster \
  --zone=us-central1-a \
  --num-nodes=3

# Create cluster with specific machine type
gcloud container clusters create prod-cluster \
  --zone=us-central1-a \
  --machine-type=e2-standard-4 \
  --num-nodes=3 \
  --disk-size=50GB

# Create autopilot cluster (fully managed)
gcloud container clusters create-auto autopilot-cluster \
  --region=us-central1

# Create cluster with autoscaling
gcloud container clusters create autoscale-cluster \
  --zone=us-central1-a \
  --num-nodes=2 \
  --enable-autoscaling \
  --min-nodes=2 \
  --max-nodes=10

# Create cluster with network policy
gcloud container clusters create secure-cluster \
  --zone=us-central1-a \
  --enable-network-policy \
  --enable-ip-alias

# List clusters
gcloud container clusters list

# Get cluster credentials
gcloud container clusters get-credentials my-cluster --zone=us-central1-a

# Verify kubectl configuration
kubectl config current-context
kubectl cluster-info
```

### Deploying to GKE

```bash
# Create deployment
cat > deployment.yaml << EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
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
        image: nginx:latest
        ports:
        - containerPort: 80
EOF

kubectl apply -f deployment.yaml

# Create service (LoadBalancer)
cat > service.yaml << EOF
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
EOF

kubectl apply -f service.yaml

# Get service external IP
kubectl get service nginx-service

# Scale deployment
kubectl scale deployment nginx-deployment --replicas=5

# Update image
kubectl set image deployment/nginx-deployment nginx=nginx:1.21

# View deployment status
kubectl rollout status deployment/nginx-deployment

# View pods
kubectl get pods

# View logs
kubectl logs -l app=nginx

# Execute command in pod
kubectl exec -it POD_NAME -- /bin/bash

# Delete resources
kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
```

### GKE Cluster Management

```bash
# Resize cluster
gcloud container clusters resize my-cluster \
  --num-nodes=5 \
  --zone=us-central1-a

# Upgrade cluster
gcloud container clusters upgrade my-cluster \
  --zone=us-central1-a \
  --master \
  --cluster-version=latest

# Enable autoscaling
gcloud container clusters update my-cluster \
  --enable-autoscaling \
  --min-nodes=2 \
  --max-nodes=10 \
  --zone=us-central1-a

# Add node pool
gcloud container node-pools create high-memory-pool \
  --cluster=my-cluster \
  --machine-type=n2-highmem-4 \
  --num-nodes=2 \
  --zone=us-central1-a

# List node pools
gcloud container node-pools list --cluster=my-cluster --zone=us-central1-a

# Delete cluster
gcloud container clusters delete my-cluster --zone=us-central1-a
```

### GKE with Workload Identity

```bash
# Create cluster with Workload Identity
gcloud container clusters create wi-cluster \
  --workload-pool=PROJECT_ID.svc.id.goog \
  --zone=us-central1-a

# Create Kubernetes service account
kubectl create serviceaccount my-ksa

# Create GCP service account
gcloud iam service-accounts create my-gsa

# Bind Kubernetes SA to GCP SA
gcloud iam service-accounts add-iam-policy-binding \
  my-gsa@PROJECT_ID.iam.gserviceaccount.com \
  --role=roles/iam.workloadIdentityUser \
  --member="serviceAccount:PROJECT_ID.svc.id.goog[default/my-ksa]"

# Annotate Kubernetes service account
kubectl annotate serviceaccount my-ksa \
  iam.gke.io/gcp-service-account=my-gsa@PROJECT_ID.iam.gserviceaccount.com

# Use in pod
cat > pod-wi.yaml << EOF
apiVersion: v1
kind: Pod
metadata:
  name: workload-identity-test
spec:
  serviceAccountName: my-ksa
  containers:
  - name: cloud-sdk
    image: google/cloud-sdk:slim
    command: ["sleep", "infinity"]
EOF

kubectl apply -f pod-wi.yaml
```

---

## Cloud Functions

Cloud Functions is a serverless execution environment for building and connecting cloud services.

### Creating Cloud Functions

#### HTTP Function (Python)

```bash
# Create function directory
mkdir my-function && cd my-function

# Create main.py
cat > main.py << 'EOF'
import functions_framework

@functions_framework.http
def hello_http(request):
    name = request.args.get('name', 'World')
    return f'Hello {name}!'
EOF

# Create requirements.txt
cat > requirements.txt << EOF
functions-framework==3.3.0
EOF

# Deploy function
gcloud functions deploy hello-http \
  --runtime=python39 \
  --trigger-http \
  --allow-unauthenticated \
  --region=us-central1

# Get function URL
gcloud functions describe hello-http --region=us-central1

# Test function
curl https://REGION-PROJECT_ID.cloudfunctions.net/hello-http?name=GCP

# View logs
gcloud functions logs read hello-http --region=us-central1
```

#### Event-Driven Function (Pub/Sub)

```bash
# Create main.py for Pub/Sub trigger
cat > main.py << 'EOF'
import base64
import functions_framework

@functions_framework.cloud_event
def hello_pubsub(cloud_event):
    message = base64.b64decode(cloud_event.data["message"]["data"]).decode()
    print(f'Received message: {message}')
EOF

# Create Pub/Sub topic
gcloud pubsub topics create my-topic

# Deploy function with Pub/Sub trigger
gcloud functions deploy hello-pubsub \
  --runtime=python39 \
  --trigger-topic=my-topic \
  --region=us-central1

# Test by publishing message
gcloud pubsub topics publish my-topic --message="Hello from Pub/Sub"

# View logs
gcloud functions logs read hello-pubsub --region=us-central1
```

#### Storage Trigger Function

```bash
# Create function for Storage events
cat > main.py << 'EOF'
import functions_framework

@functions_framework.cloud_event
def hello_storage(cloud_event):
    data = cloud_event.data
    bucket = data["bucket"]
    name = data["name"]
    print(f'File {name} uploaded to bucket {bucket}')
EOF

# Deploy with Cloud Storage trigger
gcloud functions deploy hello-storage \
  --runtime=python39 \
  --trigger-resource=my-bucket \
  --trigger-event=google.storage.object.finalize \
  --region=us-central1

# Test by uploading file
echo "test content" > test.txt
gsutil cp test.txt gs://my-bucket/

# View logs
gcloud functions logs read hello-storage --region=us-central1
```

### Node.js Cloud Function

```bash
# Create index.js
cat > index.js << 'EOF'
const functions = require('@google-cloud/functions-framework');

functions.http('helloWorld', (req, res) => {
  const name = req.query.name || 'World';
  res.send(`Hello ${name}!`);
});
EOF

# Create package.json
cat > package.json << 'EOF'
{
  "name": "my-function",
  "version": "1.0.0",
  "dependencies": {
    "@google-cloud/functions-framework": "^3.1.0"
  }
}
EOF

# Deploy
gcloud functions deploy helloWorld \
  --runtime=nodejs18 \
  --trigger-http \
  --allow-unauthenticated \
  --region=us-central1
```

### Managing Cloud Functions

```bash
# List all functions
gcloud functions list

# Describe function
gcloud functions describe hello-http --region=us-central1

# Update function
gcloud functions deploy hello-http \
  --runtime=python39 \
  --trigger-http \
  --region=us-central1 \
  --set-env-vars=KEY1=value1,KEY2=value2

# Delete function
gcloud functions delete hello-http --region=us-central1

# Call function
gcloud functions call hello-http \
  --region=us-central1 \
  --data='{"name":"GCP"}'

# Get function logs
gcloud functions logs read hello-http \
  --region=us-central1 \
  --limit=50
```

---

## Summary

| Service | Use Case | Management Level | Scaling |
|---------|----------|------------------|---------|
| **Compute Engine** | Full control, custom workloads | You manage OS and apps | Manual or instance groups |
| **App Engine** | Web apps, mobile backends | Fully managed | Automatic |
| **GKE** | Containerized microservices | Managed Kubernetes | Horizontal pod autoscaling |
| **Cloud Run** | Stateless containers | Fully managed | Automatic, scales to zero |
| **Cloud Functions** | Event-driven, short tasks | Fully managed | Automatic, scales to zero |

## Next Topic

Continue to [Storage Services](./04-storage-services.md) to learn about GCP's storage options.
