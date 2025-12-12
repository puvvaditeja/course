# GCP Introduction

## What is Google Cloud Platform?

Google Cloud Platform (GCP) is a suite of cloud computing services provided by Google that runs on the same infrastructure that Google uses internally for its end-user products, such as Google Search, Gmail, YouTube, and Google Drive. GCP offers a wide range of services including computing, data storage, data analytics, and machine learning.

### Key Characteristics

- **Global Infrastructure**: Data centers in 40+ regions across 200+ countries
- **Security**: Built on Google's security model with encryption at rest and in transit
- **Scalability**: Automatic scaling to handle millions of users
- **Innovation**: Access to Google's cutting-edge technology (AI/ML, BigQuery, etc.)
- **Open Source**: Strong support for open-source technologies
- **Pay-as-you-go**: Only pay for what you use, no upfront costs

### GCP vs Other Cloud Providers

| Feature | GCP | AWS | Azure |
|---------|-----|-----|-------|
| Market Share | 11% | 32% | 23% |
| Strengths | Data Analytics, ML/AI, Kubernetes | Mature services, Market leader | Microsoft integration, Hybrid cloud |
| Pricing | Per-second billing | Per-second/hour billing | Per-minute billing |
| Machine Learning | TensorFlow, AutoML, Vertex AI | SageMaker, Rekognition | Azure ML, Cognitive Services |
| Kubernetes | GKE (Native) | EKS | AKS |
| Global Network | Premium tier routing | CloudFront CDN | Azure CDN |

---

## GCP Console

The GCP Console is a web-based graphical user interface for managing Google Cloud Platform resources.

### Accessing the Console

```bash
# Open in browser
https://console.cloud.google.com

# Login with Google Account
# - Personal Google Account (for learning/testing)
# - Google Workspace Account (for organizations)
```

### Console Features

1. **Navigation Menu**
   - Organized by product categories
   - Quick access to all services
   - Recently visited services
   - Pinned services for quick access

2. **Search Bar**
   - Search for resources across all projects
   - Search for products and documentation
   - Command palette for quick actions

3. **Project Selector**
   - Switch between projects
   - View project info
   - Create new projects

4. **Cloud Shell**
   - Browser-based terminal
   - Pre-installed gcloud CLI and tools
   - 5GB of persistent home directory storage
   - Built-in code editor

5. **Notifications**
   - Service health updates
   - Billing alerts
   - Resource recommendations

### Cloud Shell

Cloud Shell is a free, browser-based terminal that provides command-line access to cloud resources.

```bash
# Activate Cloud Shell (Click icon in top-right corner)

# Cloud Shell comes pre-installed with:
gcloud --version          # Google Cloud SDK
kubectl version --client  # Kubernetes CLI
docker --version         # Docker
git --version            # Git
python3 --version        # Python
node --version           # Node.js
terraform --version      # Terraform

# Set default project
gcloud config set project PROJECT_ID

# List available projects
gcloud projects list

# Open Cloud Shell Editor
cloudshell edit ~/.bashrc
```

---

## Projects and Billing

Projects are the fundamental organizing entity in GCP. All resources belong to a project.

### Understanding Projects

A project consists of:
- **Project Name**: Human-readable name (can be changed)
- **Project ID**: Unique identifier (cannot be changed after creation)
- **Project Number**: Unique numeric identifier (automatically assigned)

### Project Hierarchy

```
Organization
├── Folder: Production
│   ├── Project: prod-web-app
│   └── Project: prod-database
├── Folder: Development
│   ├── Project: dev-web-app
│   └── Project: dev-database
└── Folder: Shared Services
    ├── Project: shared-networking
    └── Project: shared-security
```

### Project Management with gcloud

```bash
# List all projects
gcloud projects list

# Create a new project
gcloud projects create PROJECT_ID \
  --name="My Application" \
  --labels=environment=development

# Get project details
gcloud projects describe PROJECT_ID

# Set default project
gcloud config set project PROJECT_ID

# Get current project
gcloud config get-value project

# Delete a project
gcloud projects delete PROJECT_ID

# Enable APIs for a project
gcloud services enable compute.googleapis.com
gcloud services enable storage.googleapis.com
gcloud services enable container.googleapis.com

# List enabled APIs
gcloud services list --enabled

# List available APIs
gcloud services list --available
```

### Billing Accounts

Billing accounts pay for project resource usage. A project must be linked to an active billing account to use paid services.

```bash
# List billing accounts
gcloud billing accounts list

# Link project to billing account
gcloud billing projects link PROJECT_ID \
  --billing-account=BILLING_ACCOUNT_ID

# Check billing status
gcloud billing projects describe PROJECT_ID

# Set up billing budget alerts
gcloud billing budgets create \
  --billing-account=BILLING_ACCOUNT_ID \
  --display-name="Monthly Budget" \
  --budget-amount=100USD \
  --threshold-rule=percent=50 \
  --threshold-rule=percent=90 \
  --threshold-rule=percent=100
```

### Cost Management Best Practices

1. **Set Budget Alerts**
   - Configure alerts at 50%, 90%, and 100% of budget
   - Send notifications to billing administrators
   - Use programmatic budget notifications for automation

2. **Use Labels for Cost Tracking**
   ```bash
   # Label resources for cost allocation
   gcloud compute instances create my-instance \
     --labels=team=engineering,env=production,cost-center=cc-1234
   ```

3. **Enable Billing Export**
   - Export detailed billing data to BigQuery
   - Create custom cost reports and dashboards
   - Analyze spending patterns

4. **Use Committed Use Discounts**
   - 1-year or 3-year commitments
   - Save up to 57% on compute resources
   - Ideal for predictable workloads

5. **Leverage Preemptible/Spot Instances**
   - Save up to 80% on compute costs
   - Suitable for batch jobs and fault-tolerant workloads

---

## Cloud Deployment Models

Understanding different deployment models helps you choose the right architecture for your applications.

### Infrastructure as a Service (IaaS)

**Definition**: Provides virtualized computing resources over the internet. You manage the OS, middleware, runtime, and applications.

**GCP IaaS Services**:
- Compute Engine (Virtual Machines)
- Persistent Disks
- Cloud Storage
- Virtual Private Cloud (VPC)

**Use Cases**:
- Lift-and-shift migrations
- Full control over infrastructure
- Custom software stacks
- Legacy applications

**Example**:
```bash
# Create a VM (IaaS)
gcloud compute instances create my-vm \
  --machine-type=e2-medium \
  --zone=us-central1-a \
  --image-family=ubuntu-2204-lts \
  --image-project=ubuntu-os-cloud \
  --boot-disk-size=20GB \
  --tags=web-server

# SSH into the VM
gcloud compute ssh my-vm --zone=us-central1-a

# Install and configure software manually
sudo apt update
sudo apt install nginx -y
sudo systemctl start nginx
```

**Responsibilities**:
- ✅ You manage: OS patches, runtime, applications, data
- ❌ Google manages: Physical hardware, networking, virtualization

---

### Platform as a Service (PaaS)

**Definition**: Provides a platform allowing customers to develop, run, and manage applications without managing underlying infrastructure.

**GCP PaaS Services**:
- App Engine (fully managed application platform)
- Cloud Run (containerized applications)
- Cloud Functions (serverless functions)

**Use Cases**:
- Web applications
- APIs and microservices
- Mobile backends
- Rapid development and deployment

**Example**:
```bash
# Deploy to App Engine (PaaS)
# Create app.yaml
cat > app.yaml << EOF
runtime: python39
entrypoint: gunicorn -b :$PORT main:app

env_variables:
  DATABASE_URL: "postgresql://..."
EOF

# Create main.py
cat > main.py << 'EOF'
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello from App Engine!'

if __name__ == '__main__':
    app.run()
EOF

# Create requirements.txt
cat > requirements.txt << EOF
Flask==2.3.0
gunicorn==20.1.0
EOF

# Deploy (Google manages everything except your code)
gcloud app deploy
```

**Responsibilities**:
- ✅ You manage: Application code, data, configurations
- ❌ Google manages: OS, runtime, scaling, load balancing, patching

---

### Software as a Service (SaaS)

**Definition**: Complete software applications delivered over the internet. Users access via web browser.

**GCP/Google SaaS Examples**:
- Google Workspace (Gmail, Docs, Drive, Sheets)
- Google Analytics
- Firebase (mobile backend)
- Looker (business intelligence)

**Use Cases**:
- Email and collaboration
- CRM systems
- Business analytics
- No infrastructure management needed

**Responsibilities**:
- ✅ You manage: User data, access permissions
- ❌ Google manages: Everything else (infrastructure, application, updates)

---

### Comparison: IaaS vs PaaS vs SaaS

| Aspect | IaaS | PaaS | SaaS |
|--------|------|------|------|
| **Control** | Maximum | Medium | Minimum |
| **Flexibility** | High | Medium | Low |
| **Management Overhead** | High | Low | None |
| **Time to Market** | Slow | Fast | Immediate |
| **Customization** | Full | Limited | Minimal |
| **Scaling** | Manual/Auto | Automatic | Automatic |
| **Cost Model** | Pay for VMs | Pay for usage | Subscription |
| **Examples** | Compute Engine | App Engine, Cloud Run | Gmail, Google Workspace |

---

## Cloud Service Providers Comparison

### Major Cloud Providers

#### Amazon Web Services (AWS)
- **Market Leader**: Largest market share (~32%)
- **Strengths**:
  - Most mature and feature-rich platform
  - Largest partner ecosystem
  - Extensive global infrastructure
  - Wide range of services (200+)
- **Popular Services**: EC2, S3, Lambda, RDS, DynamoDB
- **Best For**: Enterprises, startups, diverse workloads

#### Microsoft Azure
- **Enterprise Focus**: Second largest (~23% market share)
- **Strengths**:
  - Seamless Microsoft integration (AD, Office 365)
  - Strong hybrid cloud capabilities
  - Excellent for .NET applications
  - Government cloud certifications
- **Popular Services**: Virtual Machines, Azure SQL, Azure AD, AKS
- **Best For**: Microsoft shops, hybrid cloud, enterprise

#### Google Cloud Platform (GCP)
- **Innovation Leader**: Third largest (~11% market share)
- **Strengths**:
  - Superior data analytics and ML/AI capabilities
  - Best-in-class networking (premium tier)
  - Native Kubernetes (GKE)
  - Cost-effective pricing (per-second billing)
  - Open source friendly
- **Popular Services**: BigQuery, GKE, Cloud Run, Vertex AI
- **Best For**: Data-intensive apps, ML/AI, Kubernetes workloads

### Service Comparison Matrix

| Service Category | AWS | Azure | GCP |
|-----------------|-----|-------|-----|
| **Virtual Machines** | EC2 | Virtual Machines | Compute Engine |
| **Containers** | ECS, EKS | AKS | GKE, Cloud Run |
| **Serverless Functions** | Lambda | Azure Functions | Cloud Functions |
| **Object Storage** | S3 | Blob Storage | Cloud Storage |
| **Block Storage** | EBS | Managed Disks | Persistent Disk |
| **SQL Database** | RDS | Azure SQL Database | Cloud SQL |
| **NoSQL Database** | DynamoDB | Cosmos DB | Firestore, Bigtable |
| **Data Warehouse** | Redshift | Synapse Analytics | BigQuery |
| **Kubernetes** | EKS | AKS | GKE |
| **Load Balancer** | ELB, ALB | Load Balancer | Cloud Load Balancing |
| **CDN** | CloudFront | Azure CDN | Cloud CDN |
| **DNS** | Route 53 | Azure DNS | Cloud DNS |
| **Identity Management** | IAM | Azure AD | Cloud IAM |
| **Machine Learning** | SageMaker | Azure ML | Vertex AI |

### Multi-Cloud Strategy

Organizations often use multiple cloud providers for:

1. **Risk Mitigation**: Avoid vendor lock-in
2. **Best-of-Breed**: Use each provider's strengths
3. **Cost Optimization**: Compare pricing for different workloads
4. **Geographic Coverage**: Leverage regional presence
5. **Compliance**: Meet regulatory requirements

**Tools for Multi-Cloud**:
- Terraform (Infrastructure as Code)
- Kubernetes (Container orchestration)
- Anthos (Google's multi-cloud platform)
- Azure Arc (Microsoft's multi-cloud platform)

---

## GCP Getting Started

### Initial Setup

```bash
# Install Google Cloud SDK
# macOS
brew install --cask google-cloud-sdk

# Linux
curl https://sdk.cloud.google.com | bash
exec -l $SHELL

# Windows
# Download from: https://cloud.google.com/sdk/docs/install

# Initialize gcloud
gcloud init

# Authenticate
gcloud auth login

# Set default project
gcloud config set project PROJECT_ID

# Set default region and zone
gcloud config set compute/region us-central1
gcloud config set compute/zone us-central1-a

# View current configuration
gcloud config list

# Create new configuration
gcloud config configurations create development
gcloud config configurations activate development
```

### Free Tier and Credits

**GCP Free Tier** includes:
- $300 credit for new users (90 days)
- Always Free tier for specific services:
  - 1 f1-micro VM instance per month
  - 30 GB of standard persistent disk
  - 5 GB of Cloud Storage
  - 1 GB of Cloud Functions invocations
  - 28 instance hours per day for App Engine

### Essential First Commands

```bash
# Check your account
gcloud auth list

# List available regions
gcloud compute regions list

# List available zones
gcloud compute zones list

# List available machine types
gcloud compute machine-types list --filter="zone:us-central1-a"

# Check quotas
gcloud compute project-info describe --project=PROJECT_ID

# Enable required APIs
gcloud services enable compute.googleapis.com
gcloud services enable storage-api.googleapis.com
gcloud services enable cloudresourcemanager.googleapis.com

# Get help
gcloud help
gcloud compute --help
gcloud compute instances create --help
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| **GCP Overview** | Global infrastructure, strong ML/AI, Kubernetes native, competitive pricing |
| **Console** | Web UI, Cloud Shell, project management, comprehensive monitoring |
| **Projects** | Fundamental organizing entity, links to billing, enables APIs |
| **IaaS** | Compute Engine - full control, manage OS and applications |
| **PaaS** | App Engine, Cloud Run - focus on code, Google manages infrastructure |
| **SaaS** | Google Workspace - ready-to-use applications |
| **Cloud Providers** | AWS (market leader), Azure (enterprise), GCP (data/ML innovation) |

## Next Topic

Continue to [Identity and Access Management](./02-identity-access-management.md) to learn how to secure your GCP resources.
