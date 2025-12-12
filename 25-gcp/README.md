# Google Cloud Platform (GCP)

## Overview
Google Cloud Platform provides cloud computing services including compute, storage, databases, and machine learning. This module covers essential GCP services and concepts needed for building and deploying cloud applications.

## Learning Objectives
By the end of this module, you will be able to:
- Navigate and use the GCP Console and Cloud Shell
- Manage projects, billing, and resources
- Implement identity and access management (IAM)
- Deploy applications using various compute services
- Store and manage data using GCP storage solutions
- Configure networking, load balancing, and CDN
- Choose and use appropriate database services

## Topics Covered

### 1. [GCP Introduction](./topics/01-gcp-introduction.md)
- What is Google Cloud Platform?
- GCP Console and Cloud Shell
- Projects and Billing Management
- Cloud Deployment Models (IaaS, PaaS, SaaS)
- Cloud Service Providers Comparison (AWS, Azure, GCP)
- Getting Started with GCP

### 2. [Identity and Access Management](./topics/02-identity-access-management.md)
- GCP IAM Overview
- Users, Groups, and Service Accounts
- Roles and Permissions (Basic, Predefined, Custom)
- Policy Management and Best Practices
- Service Account Keys and Impersonation
- IAM Policy Hierarchy

### 3. [Compute Services](./topics/03-compute-services.md)
- Compute Engine (Virtual Machines)
- App Engine (Platform as a Service)
- Kubernetes Engine (GKE)
- Cloud Functions (Serverless)
- Instance Groups and Auto-scaling
- Choosing the Right Compute Service

### 4. [Storage Services](./topics/04-storage-services.md)
- Cloud Storage (Object Storage)
- Persistent Disks (Block Storage)
- Filestore (File Storage)
- Storage Classes and Lifecycle Policies
- Backups and Snapshots
- Storage Best Practices

### 5. [Networking](./topics/05-networking.md)
- Virtual Private Cloud (VPC)
- Subnets and Firewall Rules
- Load Balancing (Global and Regional)
- Cloud CDN (Content Delivery Network)
- Cloud DNS
- Cloud NAT and VPN

### 6. [Database Services](./topics/06-database-services.md)
- Cloud SQL (MySQL, PostgreSQL, SQL Server)
- Cloud Spanner (Global Relational Database)
- Firestore (NoSQL Document Database)
- Bigtable (NoSQL Wide-column Database)
- Memorystore (In-memory Cache)
- Choosing the Right Database

## Key Concepts

### GCP Global Infrastructure
- **Regions**: Geographic locations (e.g., us-central1, europe-west1)
- **Zones**: Isolated locations within regions (e.g., us-central1-a)
- **Multi-region**: Geo-redundant locations (e.g., us, eu, asia)

### Essential gcloud Commands

```bash
# Authentication and Configuration
gcloud auth login
gcloud config set project PROJECT_ID
gcloud config list

# Compute Engine
gcloud compute instances create my-vm
gcloud compute instances list
gcloud compute ssh my-vm

# Cloud Storage
gsutil mb gs://my-bucket
gsutil cp file.txt gs://my-bucket/
gsutil ls gs://my-bucket

# Container/Kubernetes
gcloud container clusters create my-cluster
kubectl get pods

# IAM
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:email@example.com" \
  --role="roles/viewer"
```

## Exercises

See the [exercises](./exercises/) directory for hands-on practice problems and solutions covering:

### Exercise 1: GCP Setup and Configuration
1. Create a GCP account and project
2. Install and configure gcloud CLI
3. Explore the GCP Console
4. Set up billing alerts

### Exercise 2: Virtual Machines
1. Create a Compute Engine VM instance
2. Configure firewall rules
3. SSH into the instance
4. Install and run a web server

### Exercise 3: Cloud Storage
1. Create Cloud Storage buckets
2. Upload and download objects
3. Configure lifecycle policies
4. Set up static website hosting

### Exercise 4: Kubernetes Deployment
1. Create a GKE cluster
2. Deploy a containerized application
3. Expose the application via LoadBalancer
4. Scale the deployment

### Exercise 5: Database Setup
1. Create a Cloud SQL instance
2. Connect to the database
3. Create tables and insert data
4. Set up automated backups

## Quick Start Guide

### 1. Install Google Cloud SDK

```bash
# macOS
brew install --cask google-cloud-sdk

# Linux
curl https://sdk.cloud.google.com | bash
exec -l $SHELL

# Windows - Download from:
# https://cloud.google.com/sdk/docs/install
```

### 2. Initialize and Authenticate

```bash
# Initialize gcloud
gcloud init

# Authenticate
gcloud auth login

# Set default project
gcloud config set project PROJECT_ID

# Set default region and zone
gcloud config set compute/region us-central1
gcloud config set compute/zone us-central1-a
```

### 3. Enable Required APIs

```bash
# Enable common APIs
gcloud services enable compute.googleapis.com
gcloud services enable storage-api.googleapis.com
gcloud services enable container.googleapis.com
gcloud services enable sqladmin.googleapis.com
gcloud services enable dns.googleapis.com
```

### 4. Create Your First Resources

```bash
# Create a VM
gcloud compute instances create my-first-vm \
  --machine-type=e2-micro \
  --zone=us-central1-a

# Create a Cloud Storage bucket
gsutil mb gs://my-unique-bucket-name-12345

# Upload a file
echo "Hello GCP" > hello.txt
gsutil cp hello.txt gs://my-unique-bucket-name-12345/
```

## Additional Resources

### Official Documentation
- [Google Cloud Documentation](https://cloud.google.com/docs)
- [gcloud CLI Reference](https://cloud.google.com/sdk/gcloud/reference)
- [GCP Architecture Center](https://cloud.google.com/architecture)
- [GCP Best Practices](https://cloud.google.com/docs/enterprise/best-practices-for-enterprise-organizations)

### Interactive Learning
- [Google Cloud Skills Boost](https://www.cloudskillsboost.google/)
- [Qwiklabs](https://www.qwiklabs.com/)
- [Coursera - Google Cloud Courses](https://www.coursera.org/googlecloud)

### Community and Support
- [Google Cloud Community](https://www.googlecloudcommunity.com/)
- [Stack Overflow - GCP Tag](https://stackoverflow.com/questions/tagged/google-cloud-platform)
- [Reddit - r/googlecloud](https://www.reddit.com/r/googlecloud/)

### YouTube Channels
- [Google Cloud Tech](https://www.youtube.com/user/googlecloudplatform)
- [Google Cloud Events](https://cloudonair.withgoogle.com/)

### Cheat Sheets
- See [resources/cheat-sheets/gcp.md](../resources/cheat-sheets/) for quick reference

### Free Tier
Google Cloud offers:
- $300 credit for new users (90 days)
- Always Free tier including:
  - 1 f1-micro VM instance per month
  - 30 GB of standard persistent disk
  - 5 GB of Cloud Storage
  - Cloud Functions (2M invocations/month)
  - Cloud Run (2M requests/month)

## Certification Path

Consider pursuing these certifications to validate your GCP skills:

1. **Associate Cloud Engineer**
   - Entry-level certification
   - Covers fundamental GCP services
   - Recommended first certification

2. **Professional Cloud Architect**
   - Advanced certification
   - Focuses on designing and planning cloud solutions
   - Requires hands-on experience

3. **Professional Cloud Developer**
   - Application development focus
   - Building scalable applications on GCP

## Assessment

You should be comfortable with:
- [ ] Navigating the GCP Console and using Cloud Shell
- [ ] Creating and managing projects
- [ ] Understanding IAM roles and permissions
- [ ] Deploying VMs with Compute Engine
- [ ] Using Cloud Storage for object storage
- [ ] Creating and managing VPCs and firewall rules
- [ ] Setting up load balancers
- [ ] Choosing appropriate database services
- [ ] Using gcloud CLI for common operations
- [ ] Understanding billing and cost management

## Best Practices Summary

### Security
- Use least privilege IAM roles
- Enable MFA for user accounts
- Use service accounts for applications
- Regularly audit IAM policies
- Enable VPC Flow Logs for network monitoring

### Cost Optimization
- Use committed use discounts for predictable workloads
- Leverage preemptible/spot instances for batch jobs
- Set up billing alerts and budgets
- Use appropriate storage classes
- Delete unused resources regularly

### Performance
- Choose regions close to your users
- Use Cloud CDN for static content
- Implement caching strategies
- Use appropriate machine types
- Monitor and optimize based on metrics

### Reliability
- Design for failure (use multiple zones/regions)
- Implement health checks
- Use managed instance groups for auto-healing
- Set up proper monitoring and alerting
- Regular backups and disaster recovery planning

## Common GCP Patterns

### Three-Tier Web Application
```
Internet → Cloud Load Balancer → Compute Engine/GKE (App Tier)
                                          ↓
                                    Cloud SQL (Database Tier)
                                          ↓
                                  Cloud Storage (Static Assets)
```

### Serverless Application
```
API Gateway → Cloud Functions → Firestore
                    ↓
              Cloud Storage → Cloud CDN
```

### Microservices on Kubernetes
```
Internet → Load Balancer → GKE Cluster
                              ├─ Service A
                              ├─ Service B
                              └─ Service C
                                    ↓
                           Cloud SQL / Firestore / Bigtable
```

## Next Steps

Once you've completed this module and feel confident with GCP fundamentals, you can:

1. **Build a Project**: Deploy a full-stack application on GCP
2. **Explore Advanced Topics**:
   - Cloud Run for containerized applications
   - BigQuery for data analytics
   - Vertex AI for machine learning
   - Cloud Pub/Sub for messaging
   - Cloud Armor for DDoS protection
3. **Study for Certification**: Prepare for Associate Cloud Engineer exam
4. **Continue with Next Module**: Proceed to the next module in the curriculum

---

**Time Estimate:** 5 days | **Difficulty:** Intermediate | **Prerequisites:** Basic Linux, networking concepts, understanding of web applications
