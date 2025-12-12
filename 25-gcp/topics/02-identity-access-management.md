# Identity and Access Management (IAM)

## What is GCP IAM?

Google Cloud Identity and Access Management (IAM) lets you control who (identity) has what access (role) to which resources. IAM follows the principle of least privilege, granting only the necessary access to perform specific tasks.

### IAM Security Model

IAM is based on three core components:

```
WHO (Identity)    +    WHAT (Role)    →    WHERE (Resource)
     ↓                      ↓                      ↓
Principal              Permission              Resource
(User/Group/SA)        (Predefined)         (Project/VM/Bucket)
```

### Key Concepts

- **Principal**: An identity that can access a resource (user, service account, group)
- **Role**: A collection of permissions
- **Permission**: Determines what operations are allowed on a resource
- **Policy**: Binds one or more principals to a role for a specific resource
- **Resource**: A GCP service or object (project, VM, bucket, etc.)

### IAM Policy Structure

```json
{
  "bindings": [
    {
      "role": "roles/storage.objectViewer",
      "members": [
        "user:alice@example.com",
        "serviceAccount:my-app@project.iam.gserviceaccount.com",
        "group:developers@example.com"
      ]
    },
    {
      "role": "roles/storage.admin",
      "members": [
        "user:admin@example.com"
      ],
      "condition": {
        "title": "Expires in 2024",
        "expression": "request.time < timestamp('2024-12-31T23:59:59Z')"
      }
    }
  ]
}
```

---

## Users, Groups, and Service Accounts

### Users

Users are Google Accounts (individual people) that can access GCP resources.

```bash
# Grant a user access to a project
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:alice@example.com" \
  --role="roles/viewer"

# Grant multiple roles to a user
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:bob@example.com" \
  --role="roles/compute.instanceAdmin.v1"

gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:bob@example.com" \
  --role="roles/storage.objectViewer"

# Remove user access
gcloud projects remove-iam-policy-binding PROJECT_ID \
  --member="user:alice@example.com" \
  --role="roles/viewer"

# View IAM policy for a project
gcloud projects get-iam-policy PROJECT_ID

# View IAM policy in readable format
gcloud projects get-iam-policy PROJECT_ID \
  --format=json | jq
```

### Google Groups

Groups are collections of users managed through Google Workspace or Cloud Identity.

**Benefits of Groups**:
- Manage permissions for multiple users at once
- Easy onboarding/offboarding
- Better organization and auditing
- Reduced policy complexity

```bash
# Grant access to a group
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="group:developers@example.com" \
  --role="roles/editor"

# Grant storage access to a group
gcloud storage buckets add-iam-policy-binding gs://my-bucket \
  --member="group:data-team@example.com" \
  --role="roles/storage.objectViewer"

# Remove group access
gcloud projects remove-iam-policy-binding PROJECT_ID \
  --member="group:developers@example.com" \
  --role="roles/editor"
```

**Best Practices for Groups**:
```bash
# Organize groups by function
developers@example.com          # All developers
developers-frontend@example.com # Frontend team
developers-backend@example.com  # Backend team
data-engineers@example.com      # Data team
security-team@example.com       # Security team

# Grant appropriate roles
# Frontend team - needs App Engine and Cloud Storage
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="group:developers-frontend@example.com" \
  --role="roles/appengine.appAdmin"

# Backend team - needs Compute Engine and Cloud SQL
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="group:developers-backend@example.com" \
  --role="roles/compute.instanceAdmin.v1"
```

### Service Accounts

Service accounts are special accounts used by applications and VMs to make authorized API calls.

**Types of Service Accounts**:
1. **User-managed service accounts**: Created by users
2. **Default service accounts**: Created automatically by GCP services
3. **Google-managed service accounts**: Used by Google services

#### Creating and Managing Service Accounts

```bash
# Create a service account
gcloud iam service-accounts create my-app-sa \
  --display-name="My Application Service Account" \
  --description="Service account for my application"

# List service accounts
gcloud iam service-accounts list

# Get service account details
gcloud iam service-accounts describe \
  my-app-sa@PROJECT_ID.iam.gserviceaccount.com

# Grant service account access to project
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="serviceAccount:my-app-sa@PROJECT_ID.iam.gserviceaccount.com" \
  --role="roles/storage.objectViewer"

# Grant service account access to specific bucket
gcloud storage buckets add-iam-policy-binding gs://my-bucket \
  --member="serviceAccount:my-app-sa@PROJECT_ID.iam.gserviceaccount.com" \
  --role="roles/storage.objectAdmin"

# Delete a service account
gcloud iam service-accounts delete \
  my-app-sa@PROJECT_ID.iam.gserviceaccount.com
```

#### Service Account Keys

Service account keys allow applications running outside GCP to authenticate.

```bash
# Create a JSON key file
gcloud iam service-accounts keys create ~/key.json \
  --iam-account=my-app-sa@PROJECT_ID.iam.gserviceaccount.com

# List keys for a service account
gcloud iam service-accounts keys list \
  --iam-account=my-app-sa@PROJECT_ID.iam.gserviceaccount.com

# Delete a key
gcloud iam service-accounts keys delete KEY_ID \
  --iam-account=my-app-sa@PROJECT_ID.iam.gserviceaccount.com

# Use service account key in application
export GOOGLE_APPLICATION_CREDENTIALS="~/key.json"

# Or in code (Python example)
from google.cloud import storage
client = storage.Client.from_service_account_json('~/key.json')
```

**Security Best Practices for Service Account Keys**:
```bash
# ⚠️ AVOID: Downloading keys when possible
# Use Workload Identity or instance service accounts instead

# ✅ GOOD: Attach service account to VM
gcloud compute instances create my-instance \
  --service-account=my-app-sa@PROJECT_ID.iam.gserviceaccount.com \
  --scopes=https://www.googleapis.com/auth/cloud-platform

# ✅ GOOD: Use Workload Identity for GKE
# No keys needed - pods authenticate automatically

# If keys are necessary:
# 1. Rotate keys regularly (90 days)
# 2. Use short-lived credentials when possible
# 3. Never commit keys to version control
# 4. Store keys in Secret Manager
```

#### Impersonating Service Accounts

Allow users to temporarily act as a service account without creating keys.

```bash
# Grant user permission to impersonate service account
gcloud iam service-accounts add-iam-policy-binding \
  my-app-sa@PROJECT_ID.iam.gserviceaccount.com \
  --member="user:developer@example.com" \
  --role="roles/iam.serviceAccountUser"

# Run command as service account
gcloud compute instances list \
  --impersonate-service-account=my-app-sa@PROJECT_ID.iam.gserviceaccount.com

# Create temporary credentials
gcloud auth application-default login \
  --impersonate-service-account=my-app-sa@PROJECT_ID.iam.gserviceaccount.com
```

---

## Roles and Permissions

### Permission Format

Permissions follow the format: `service.resource.verb`

Examples:
- `compute.instances.create` - Create VM instances
- `storage.buckets.list` - List storage buckets
- `storage.objects.get` - Get storage objects
- `iam.serviceAccounts.actAs` - Act as a service account

### Role Types

#### 1. Basic Roles (Primitive Roles)

**⚠️ WARNING**: Basic roles are too broad for production use.

```bash
# Viewer - Read-only access to all resources
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:alice@example.com" \
  --role="roles/viewer"

# Editor - Viewer + modify resources (but not permissions)
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:bob@example.com" \
  --role="roles/editor"

# Owner - Editor + manage permissions and billing
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:admin@example.com" \
  --role="roles/owner"
```

| Role | Permissions | Use Case |
|------|-------------|----------|
| **Viewer** | Read-only | Auditors, monitoring |
| **Editor** | Viewer + modify | Developers |
| **Owner** | Editor + admin | Project administrators |

#### 2. Predefined Roles

Google-managed roles with specific permissions for each service.

```bash
# Compute Engine roles
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:dev@example.com" \
  --role="roles/compute.instanceAdmin.v1"  # Manage VM instances

gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:ops@example.com" \
  --role="roles/compute.networkAdmin"  # Manage networking

# Storage roles
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:data@example.com" \
  --role="roles/storage.objectAdmin"  # Full control of objects

gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:reader@example.com" \
  --role="roles/storage.objectViewer"  # Read-only access

# Kubernetes Engine roles
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:k8s-admin@example.com" \
  --role="roles/container.clusterAdmin"  # Manage GKE clusters

gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:developer@example.com" \
  --role="roles/container.developer"  # Deploy to GKE

# App Engine roles
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:app-admin@example.com" \
  --role="roles/appengine.appAdmin"  # Manage App Engine apps

# Cloud SQL roles
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:dba@example.com" \
  --role="roles/cloudsql.admin"  # Manage Cloud SQL instances

# List all available roles
gcloud iam roles list

# List all predefined roles (Google-managed)
gcloud iam roles list --filter="name:roles/"

# Get role details
gcloud iam roles describe roles/compute.instanceAdmin.v1

# Get role details with permissions
gcloud iam roles describe roles/storage.objectViewer \
  --format=json | jq '.includedPermissions'
```

**Common Predefined Roles**:

| Service | Role | Description |
|---------|------|-------------|
| Compute Engine | `roles/compute.instanceAdmin.v1` | Create, modify, delete VMs |
| Compute Engine | `roles/compute.viewer` | Read-only access to VMs |
| Storage | `roles/storage.admin` | Full control of buckets and objects |
| Storage | `roles/storage.objectViewer` | Read objects only |
| GKE | `roles/container.clusterAdmin` | Full control of GKE clusters |
| GKE | `roles/container.developer` | Deploy to GKE |
| Cloud SQL | `roles/cloudsql.client` | Connect to Cloud SQL |
| BigQuery | `roles/bigquery.dataViewer` | Read BigQuery datasets |
| Logging | `roles/logging.viewer` | View logs |

#### 3. Custom Roles

Create your own roles with specific permissions for your organization.

```bash
# Create a custom role from YAML file
cat > custom-role.yaml << EOF
title: "Custom VM Operator"
description: "Can start and stop VMs but not create or delete"
stage: "GA"
includedPermissions:
- compute.instances.start
- compute.instances.stop
- compute.instances.get
- compute.instances.list
- compute.zones.list
EOF

gcloud iam roles create customVmOperator \
  --project=PROJECT_ID \
  --file=custom-role.yaml

# Create custom role with gcloud command
gcloud iam roles create customStorageRole \
  --project=PROJECT_ID \
  --title="Custom Storage Role" \
  --description="Custom permissions for storage" \
  --permissions=storage.buckets.list,storage.objects.get,storage.objects.list \
  --stage=GA

# List custom roles
gcloud iam roles list --project=PROJECT_ID

# Describe custom role
gcloud iam roles describe customVmOperator --project=PROJECT_ID

# Update custom role - add permissions
gcloud iam roles update customVmOperator \
  --project=PROJECT_ID \
  --add-permissions=compute.instances.reset

# Update custom role - remove permissions
gcloud iam roles update customVmOperator \
  --project=PROJECT_ID \
  --remove-permissions=compute.instances.reset

# Disable a custom role
gcloud iam roles update customVmOperator \
  --project=PROJECT_ID \
  --stage=DISABLED

# Delete a custom role
gcloud iam roles delete customVmOperator --project=PROJECT_ID

# Assign custom role to user
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:operator@example.com" \
  --role="projects/PROJECT_ID/roles/customVmOperator"
```

**Custom Role Best Practices**:
- Only include permissions you actually need
- Test custom roles in dev before production
- Document the purpose and use case
- Use meaningful names and descriptions
- Review and update periodically

---

## Policy Management

### IAM Policy Hierarchy

IAM policies are inherited from parent to child resources:

```
Organization
    ↓ (inherits)
Folder
    ↓ (inherits)
Project
    ↓ (inherits)
Resource (VM, Bucket, etc.)
```

**Policy Inheritance Example**:
```bash
# Grant at organization level (applies to all projects)
gcloud organizations add-iam-policy-binding ORG_ID \
  --member="user:auditor@example.com" \
  --role="roles/viewer"

# Grant at folder level (applies to all projects in folder)
gcloud resource-manager folders add-iam-policy-binding FOLDER_ID \
  --member="group:dev-team@example.com" \
  --role="roles/editor"

# Grant at project level
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:dev@example.com" \
  --role="roles/compute.instanceAdmin.v1"

# Grant at resource level (specific bucket)
gcloud storage buckets add-iam-policy-binding gs://my-bucket \
  --member="user:data-analyst@example.com" \
  --role="roles/storage.objectViewer"
```

### Managing IAM Policies

```bash
# Get current IAM policy
gcloud projects get-iam-policy PROJECT_ID > policy.yaml

# View policy in JSON format
gcloud projects get-iam-policy PROJECT_ID --format=json

# Edit policy file (policy.yaml)
cat > policy.yaml << EOF
bindings:
- members:
  - user:alice@example.com
  - serviceAccount:my-app@PROJECT_ID.iam.gserviceaccount.com
  role: roles/viewer
- members:
  - user:bob@example.com
  - group:developers@example.com
  role: roles/editor
- members:
  - user:admin@example.com
  role: roles/owner
etag: BwX1234567890=
version: 1
EOF

# Set IAM policy (replace entire policy)
gcloud projects set-iam-policy PROJECT_ID policy.yaml

# Add individual binding
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:new-user@example.com" \
  --role="roles/compute.viewer"

# Remove individual binding
gcloud projects remove-iam-policy-binding PROJECT_ID \
  --member="user:old-user@example.com" \
  --role="roles/viewer"

# Add binding with condition
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:temp-contractor@example.com" \
  --role="roles/viewer" \
  --condition='expression=request.time < timestamp("2024-12-31T23:59:59Z"),title=Temporary Access,description=Access expires end of 2024'
```

### Conditional IAM Policies

Grant access based on conditions like time, resource attributes, or request properties.

```bash
# Time-based access (expires on specific date)
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:contractor@example.com" \
  --role="roles/viewer" \
  --condition='expression=request.time < timestamp("2024-12-31T23:59:59Z"),title=Contract End Date'

# Resource-based access (only specific VMs)
gcloud compute instances add-iam-policy-binding my-instance \
  --zone=us-central1-a \
  --member="user:dev@example.com" \
  --role="roles/compute.instanceAdmin.v1" \
  --condition='expression=resource.name.startsWith("projects/PROJECT_ID/zones/us-central1-a/instances/dev-"),title=Dev VMs Only'

# IP-based access
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:remote-worker@example.com" \
  --role="roles/editor" \
  --condition='expression=origin.ip in ["203.0.113.0/24"],title=Office IP Range Only'

# Combined conditions
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:temp@example.com" \
  --role="roles/viewer" \
  --condition='expression=request.time < timestamp("2024-12-31T23:59:59Z") && request.time.getHours("America/New_York") >= 9 && request.time.getHours("America/New_York") <= 17,title=Business Hours Until Year End'
```

### IAM Best Practices

#### 1. Principle of Least Privilege
```bash
# ❌ BAD: Granting Owner role to everyone
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:developer@example.com" \
  --role="roles/owner"

# ✅ GOOD: Grant specific role needed
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:developer@example.com" \
  --role="roles/compute.instanceAdmin.v1"
```

#### 2. Use Groups Instead of Individual Users
```bash
# ❌ BAD: Granting to individual users
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:alice@example.com" \
  --role="roles/editor"
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:bob@example.com" \
  --role="roles/editor"
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:charlie@example.com" \
  --role="roles/editor"

# ✅ GOOD: Grant to group
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="group:developers@example.com" \
  --role="roles/editor"
```

#### 3. Use Service Accounts for Applications
```bash
# ❌ BAD: Using user credentials in application
# Never do this!

# ✅ GOOD: Create and use service account
gcloud iam service-accounts create my-app \
  --display-name="My Application"

gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="serviceAccount:my-app@PROJECT_ID.iam.gserviceaccount.com" \
  --role="roles/storage.objectViewer"

# Attach to VM
gcloud compute instances create app-server \
  --service-account=my-app@PROJECT_ID.iam.gserviceaccount.com
```

#### 4. Regular Access Reviews
```bash
# Audit who has access
gcloud projects get-iam-policy PROJECT_ID \
  --flatten="bindings[].members" \
  --format="table(bindings.role, bindings.members)"

# Find all service accounts
gcloud iam service-accounts list --format="table(email, displayName)"

# Find unused service accounts
gcloud iam service-accounts list --format="value(email)" | while read SA; do
  echo "Checking: $SA"
  gcloud iam service-accounts keys list --iam-account=$SA --filter="validAfterTime<-P90D"
done

# Export IAM policy for audit
gcloud projects get-iam-policy PROJECT_ID \
  --format=json > iam-audit-$(date +%Y%m%d).json
```

### IAM Policy Troubleshooting

```bash
# Check if user has specific permission
gcloud projects get-iam-policy PROJECT_ID \
  --flatten="bindings[].members" \
  --filter="bindings.members:user:alice@example.com" \
  --format="table(bindings.role)"

# Test IAM permissions
gcloud projects get-iam-policy PROJECT_ID \
  --format=json | jq '.bindings[] | select(.members[] | contains("alice@example.com"))'

# Check effective permissions on resource
gcloud compute instances get-iam-policy my-instance \
  --zone=us-central1-a \
  --flatten="bindings[].members" \
  --format="table(bindings.role, bindings.members)"

# Use Policy Troubleshooter (via Console or API)
# Console: IAM & Admin > Policy Troubleshooter
# Checks: Why does/doesn't a principal have access?

# Enable audit logs for IAM changes
gcloud logging read "protoPayload.serviceName=\"iam.googleapis.com\"" \
  --limit 50 \
  --format json
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| **IAM Model** | Who (Principal) + What (Role) → Where (Resource) |
| **Users** | Individual Google Accounts, use for human access |
| **Groups** | Collections of users, easier management and organization |
| **Service Accounts** | For applications and VMs, avoid using keys when possible |
| **Basic Roles** | Owner, Editor, Viewer - too broad for production |
| **Predefined Roles** | Google-managed, service-specific permissions |
| **Custom Roles** | Create specific permissions for your needs |
| **Policies** | Bindings that grant roles to principals on resources |
| **Best Practices** | Least privilege, use groups, regular audits, service accounts for apps |

## Next Topic

Continue to [Compute Services](./03-compute-services.md) to learn about GCP's compute offerings.
