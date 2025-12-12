# GCP Storage Services

## Overview of Storage Services

Google Cloud Platform provides various storage options optimized for different data types and access patterns:

| Service | Type | Use Case | Durability | Access Speed |
|---------|------|----------|------------|--------------|
| **Cloud Storage** | Object Storage | Unstructured data, media, backups | 99.999999999% | Fast, HTTP-based |
| **Persistent Disk** | Block Storage | VM boot/data disks | 99.9999999% | Very fast, local |
| **Filestore** | File Storage | Shared file system (NFS) | 99.99% | Fast, shared access |
| **Cloud SQL** | Relational DB | Structured data with transactions | 99.95% | Fast queries |
| **Firestore** | NoSQL Document | Mobile/web apps, real-time sync | 99.999% | Fast, global |
| **Bigtable** | NoSQL Wide-column | Time-series, IoT, analytics | 99.9% | Very fast, low latency |

---

## Cloud Storage (Buckets)

Cloud Storage is Google's object storage service for storing and accessing unstructured data like images, videos, backups, and static website content.

### Storage Classes

| Class | Use Case | Min Storage | Retrieval Cost | Monthly Cost (per GB) |
|-------|----------|-------------|----------------|----------------------|
| **Standard** | Frequently accessed | None | None | $0.020 |
| **Nearline** | <1/month access | 30 days | Yes | $0.010 |
| **Coldline** | <1/quarter access | 90 days | Yes | $0.004 |
| **Archive** | <1/year access | 365 days | Yes | $0.001 |

### Creating and Managing Buckets

```bash
# Create bucket (globally unique name required)
gsutil mb gs://my-unique-bucket-name

# Create bucket in specific location
gsutil mb -l us-central1 gs://my-bucket-us-central1

# Create bucket with specific storage class
gsutil mb -c NEARLINE -l us gs://my-nearline-bucket

# List all buckets
gsutil ls

# Get bucket details
gsutil ls -L -b gs://my-bucket

# List bucket with metadata
gcloud storage buckets describe gs://my-bucket

# Delete bucket (must be empty)
gsutil rb gs://my-bucket

# Delete bucket and all contents (careful!)
gsutil rm -r gs://my-bucket
```

### Uploading and Downloading Objects

```bash
# Upload single file
gsutil cp local-file.txt gs://my-bucket/

# Upload with different name
gsutil cp local-file.txt gs://my-bucket/remote-file.txt

# Upload directory recursively
gsutil cp -r ./my-directory gs://my-bucket/

# Upload with parallel composite uploads (faster for large files)
gsutil -m cp -r ./large-files gs://my-bucket/

# Download file
gsutil cp gs://my-bucket/remote-file.txt ./local-file.txt

# Download directory
gsutil cp -r gs://my-bucket/my-directory ./

# List objects in bucket
gsutil ls gs://my-bucket

# List with details
gsutil ls -l gs://my-bucket

# List recursively
gsutil ls -r gs://my-bucket/**

# Delete object
gsutil rm gs://my-bucket/file.txt

# Delete all objects with prefix
gsutil rm gs://my-bucket/logs/*.log

# Move/rename object
gsutil mv gs://my-bucket/old-name.txt gs://my-bucket/new-name.txt

# Copy between buckets
gsutil cp gs://source-bucket/file.txt gs://dest-bucket/
```

### Object Metadata and Properties

```bash
# Set object metadata
gsutil setmeta -h "Content-Type:application/json" \
  -h "Cache-Control:public, max-age=3600" \
  gs://my-bucket/data.json

# Set custom metadata
gsutil setmeta -h "x-goog-meta-author:John Doe" \
  -h "x-goog-meta-version:1.0" \
  gs://my-bucket/file.txt

# View object metadata
gsutil stat gs://my-bucket/file.txt

# Set storage class for object
gsutil rewrite -s NEARLINE gs://my-bucket/file.txt

# Set storage class for all objects
gsutil -m rewrite -s COLDLINE gs://my-bucket/*
```

### Bucket Configuration

```bash
# Enable versioning
gsutil versioning set on gs://my-bucket

# Check versioning status
gsutil versioning get gs://my-bucket

# List all versions of an object
gsutil ls -a gs://my-bucket/file.txt

# Restore previous version
gsutil cp gs://my-bucket/file.txt#1234567890 gs://my-bucket/file.txt

# Set lifecycle policy
cat > lifecycle.json << EOF
{
  "lifecycle": {
    "rule": [
      {
        "action": {"type": "Delete"},
        "condition": {
          "age": 30,
          "matchesPrefix": ["logs/"]
        }
      },
      {
        "action": {"type": "SetStorageClass", "storageClass": "NEARLINE"},
        "condition": {
          "age": 60,
          "matchesPrefix": ["archive/"]
        }
      }
    ]
  }
}
EOF

gsutil lifecycle set lifecycle.json gs://my-bucket

# Get lifecycle configuration
gsutil lifecycle get gs://my-bucket

# Enable uniform bucket-level access
gsutil uniformbucketlevelaccess set on gs://my-bucket

# Set CORS configuration
cat > cors.json << EOF
[
  {
    "origin": ["https://example.com"],
    "method": ["GET", "POST"],
    "responseHeader": ["Content-Type"],
    "maxAgeSeconds": 3600
  }
]
EOF

gsutil cors set cors.json gs://my-bucket

# Get CORS configuration
gsutil cors get gs://my-bucket
```

### Access Control

```bash
# Make object public
gsutil acl ch -u AllUsers:R gs://my-bucket/public-file.txt

# Make entire bucket public
gsutil iam ch allUsers:objectViewer gs://my-bucket

# Grant user access to bucket
gsutil iam ch user:alice@example.com:objectViewer gs://my-bucket

# Grant service account access
gsutil iam ch serviceAccount:my-app@PROJECT_ID.iam.gserviceaccount.com:objectAdmin gs://my-bucket

# Remove access
gsutil iam ch -d user:alice@example.com:objectViewer gs://my-bucket

# Get bucket IAM policy
gsutil iam get gs://my-bucket

# Set bucket IAM policy
gsutil iam set policy.json gs://my-bucket

# Make bucket private (remove all public access)
gsutil iam ch -d allUsers:objectViewer gs://my-bucket
```

### Signed URLs (Temporary Access)

```bash
# Generate signed URL (valid for 1 hour)
gsutil signurl -d 1h ~/key.json gs://my-bucket/private-file.txt

# Generate signed URL for upload
gsutil signurl -d 1h -m PUT ~/key.json gs://my-bucket/upload-here.txt

# Using gcloud (easier, uses your credentials)
gcloud storage sign-url gs://my-bucket/file.txt --duration=1h

# Generate signed URL with specific permissions
gcloud storage sign-url gs://my-bucket/file.txt \
  --duration=2h \
  --method=GET
```

### Static Website Hosting

```bash
# Set main page
gsutil web set -m index.html gs://my-bucket

# Set error page
gsutil web set -m index.html -e 404.html gs://my-bucket

# Upload website files
gsutil -m cp -r ./website/* gs://my-bucket/

# Make all files public
gsutil iam ch allUsers:objectViewer gs://my-bucket

# Access website
# https://storage.googleapis.com/my-bucket/index.html
# or use Cloud Load Balancer with custom domain
```

### Performance and Best Practices

```bash
# Enable parallel uploads (-m flag)
gsutil -m cp -r ./large-directory gs://my-bucket/

# Use composite uploads for large files
gsutil -o GSUtil:parallel_composite_upload_threshold=150M cp large-file.zip gs://my-bucket/

# Enable faster crc32c (vs md5)
gsutil -o GSUtil:check_hashes=if_fast_else_skip cp file.txt gs://my-bucket/

# Increase parallel thread count
gsutil -m -o GSUtil:parallel_thread_count=24 cp -r ./files gs://my-bucket/

# Use regional buckets for lowest latency
gsutil mb -l us-central1 -c STANDARD gs://my-regional-bucket

# Use multi-region for high availability
gsutil mb -l us -c STANDARD gs://my-multi-region-bucket

# Enable requester pays (users pay for data transfer)
gcloud storage buckets update gs://my-bucket --requester-pays
```

### Monitoring and Logging

```bash
# Enable logging
cat > logging.json << EOF
{
  "logBucket": "my-logs-bucket",
  "logObjectPrefix": "access-logs/"
}
EOF

gsutil logging set on -b gs://my-logs-bucket -o access-logs/ gs://my-bucket

# Get logging configuration
gsutil logging get gs://my-bucket

# Enable Cloud Storage insights
gcloud storage buckets update gs://my-bucket \
  --enable-autoclass \
  --enable-per-object-retention

# View bucket usage
gcloud storage buckets describe gs://my-bucket --format="value(size,objectCount)"
```

---

## Persistent Disks

Persistent Disks are durable block storage devices attached to Compute Engine VMs.

### Disk Types

| Type | Performance | Use Case | Max IOPS | Max Throughput |
|------|-------------|----------|----------|----------------|
| **Standard (pd-standard)** | HDD, lower cost | Batch processing, sequential I/O | 7,500 | 1,200 MB/s |
| **Balanced (pd-balanced)** | SSD, cost-effective | General purpose | 80,000 | 1,200 MB/s |
| **SSD (pd-ssd)** | SSD, high performance | Databases, low latency | 100,000 | 1,200 MB/s |
| **Extreme (pd-extreme)** | Highest performance | High-end databases | 120,000 | 2,400 MB/s |

### Creating and Managing Disks

```bash
# Create standard persistent disk
gcloud compute disks create my-disk \
  --size=100GB \
  --type=pd-standard \
  --zone=us-central1-a

# Create SSD persistent disk
gcloud compute disks create my-ssd-disk \
  --size=100GB \
  --type=pd-ssd \
  --zone=us-central1-a

# Create balanced persistent disk
gcloud compute disks create my-balanced-disk \
  --size=200GB \
  --type=pd-balanced \
  --zone=us-central1-a

# Create disk from snapshot
gcloud compute disks create my-restored-disk \
  --source-snapshot=my-snapshot \
  --zone=us-central1-a

# Create disk from image
gcloud compute disks create my-boot-disk \
  --image-family=ubuntu-2204-lts \
  --image-project=ubuntu-os-cloud \
  --size=20GB \
  --zone=us-central1-a

# List disks
gcloud compute disks list

# Describe disk
gcloud compute disks describe my-disk --zone=us-central1-a

# Resize disk (can only increase)
gcloud compute disks resize my-disk \
  --size=200GB \
  --zone=us-central1-a

# Delete disk
gcloud compute disks delete my-disk --zone=us-central1-a
```

### Attaching Disks to VMs

```bash
# Create VM with additional disk
gcloud compute instances create my-vm \
  --zone=us-central1-a \
  --create-disk=name=my-data-disk,size=100GB,type=pd-ssd

# Attach existing disk to VM
gcloud compute instances attach-disk my-vm \
  --disk=my-disk \
  --zone=us-central1-a

# Attach disk as read-only
gcloud compute instances attach-disk my-vm \
  --disk=my-disk \
  --mode=ro \
  --zone=us-central1-a

# Detach disk from VM
gcloud compute instances detach-disk my-vm \
  --disk=my-disk \
  --zone=us-central1-a

# Format and mount disk (inside VM)
# SSH into VM
gcloud compute ssh my-vm --zone=us-central1-a

# Inside VM:
sudo lsblk  # List block devices
sudo mkfs.ext4 -m 0 -E lazy_itable_init=0,lazy_journal_init=0,discard /dev/sdb
sudo mkdir -p /mnt/disks/data
sudo mount -o discard,defaults /dev/sdb /mnt/disks/data
sudo chmod a+w /mnt/disks/data

# Auto-mount on reboot (add to /etc/fstab)
echo '/dev/sdb /mnt/disks/data ext4 discard,defaults 0 2' | sudo tee -a /etc/fstab
```

### Snapshots

```bash
# Create snapshot
gcloud compute disks snapshot my-disk \
  --snapshot-names=my-snapshot \
  --zone=us-central1-a

# Create snapshot with description
gcloud compute disks snapshot my-disk \
  --snapshot-names=backup-2024-01-01 \
  --description="Monthly backup" \
  --zone=us-central1-a

# List snapshots
gcloud compute snapshots list

# Describe snapshot
gcloud compute snapshots describe my-snapshot

# Delete snapshot
gcloud compute snapshots delete my-snapshot

# Create snapshot schedule
gcloud compute resource-policies create snapshot-schedule daily-backup \
  --region=us-central1 \
  --max-retention-days=14 \
  --on-source-disk-delete=keep-auto-snapshots \
  --daily-schedule \
  --start-time=03:00

# Attach schedule to disk
gcloud compute disks add-resource-policies my-disk \
  --resource-policies=daily-backup \
  --zone=us-central1-a

# List resource policies
gcloud compute resource-policies list
```

### Regional Persistent Disks

```bash
# Create regional disk (replicated across zones)
gcloud compute disks create my-regional-disk \
  --size=100GB \
  --type=pd-ssd \
  --region=us-central1 \
  --replica-zones=us-central1-a,us-central1-b

# Attach regional disk to VM
gcloud compute instances attach-disk my-vm \
  --disk=my-regional-disk \
  --disk-scope=regional \
  --zone=us-central1-a

# Force attach (if VM in one zone fails)
gcloud compute instances attach-disk my-vm-backup \
  --disk=my-regional-disk \
  --disk-scope=regional \
  --zone=us-central1-b \
  --force-attach
```

---

## Filestore

Filestore is a fully managed NFS file server for applications requiring a shared file system.

### Filestore Tiers

| Tier | Performance | Use Case | Min Size | Max Size |
|------|-------------|----------|----------|----------|
| **Basic HDD** | 100 MB/s per TB | File sharing, dev/test | 1 TB | 63.9 TB |
| **Basic SSD** | 350 MB/s per TB | Medium performance | 2.5 TB | 63.9 TB |
| **High Scale SSD** | Up to 1,200 MB/s | High-throughput workloads | 10 TB | 100 TB |
| **Enterprise** | Up to 1,200 MB/s + HA | Production, high availability | 1 TB | 10 TB |

### Creating and Managing Filestore Instances

```bash
# Create basic Filestore instance
gcloud filestore instances create my-filestore \
  --zone=us-central1-a \
  --tier=BASIC_HDD \
  --file-share=name=data,capacity=1TB \
  --network=name=default

# Create high-performance instance
gcloud filestore instances create prod-filestore \
  --zone=us-central1-a \
  --tier=BASIC_SSD \
  --file-share=name=share1,capacity=2560GB \
  --network=name=default

# List instances
gcloud filestore instances list

# Describe instance
gcloud filestore instances describe my-filestore --zone=us-central1-a

# Update instance size
gcloud filestore instances update my-filestore \
  --zone=us-central1-a \
  --file-share=name=data,capacity=2TB

# Delete instance
gcloud filestore instances delete my-filestore --zone=us-central1-a
```

### Mounting Filestore on VMs

```bash
# Get Filestore IP and share name
gcloud filestore instances describe my-filestore \
  --zone=us-central1-a \
  --format="value(networks[0].ipAddresses[0])"

# SSH into VM
gcloud compute ssh my-vm --zone=us-central1-a

# Inside VM - Install NFS client
sudo apt-get update
sudo apt-get install -y nfs-common

# Create mount point
sudo mkdir -p /mnt/filestore

# Mount Filestore
sudo mount FILESTORE_IP:/data /mnt/filestore

# Verify mount
df -h | grep filestore

# Auto-mount on reboot (add to /etc/fstab)
echo 'FILESTORE_IP:/data /mnt/filestore nfs defaults,_netdev 0 0' | sudo tee -a /etc/fstab

# Test read/write
sudo chmod a+w /mnt/filestore
echo "Hello Filestore" > /mnt/filestore/test.txt
cat /mnt/filestore/test.txt
```

### Filestore Backups

```bash
# Create backup
gcloud filestore backups create my-backup \
  --instance=my-filestore \
  --instance-zone=us-central1-a \
  --file-share=data \
  --region=us-central1

# List backups
gcloud filestore backups list --region=us-central1

# Describe backup
gcloud filestore backups describe my-backup --region=us-central1

# Restore from backup
gcloud filestore instances create restored-filestore \
  --zone=us-central1-a \
  --tier=BASIC_HDD \
  --file-share=name=data,capacity=1TB \
  --network=name=default \
  --source-backup=my-backup \
  --source-backup-region=us-central1

# Delete backup
gcloud filestore backups delete my-backup --region=us-central1
```

### Filestore in Kubernetes (GKE)

```bash
# Create StorageClass
cat > filestore-sc.yaml << EOF
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: filestore-sc
provisioner: filestore.csi.storage.gke.io
volumeBindingMode: Immediate
allowVolumeExpansion: true
parameters:
  tier: standard
  network: default
EOF

kubectl apply -f filestore-sc.yaml

# Create PersistentVolumeClaim
cat > pvc.yaml << EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: filestore-pvc
spec:
  accessModes:
  - ReadWriteMany
  storageClassName: filestore-sc
  resources:
    requests:
      storage: 1Ti
EOF

kubectl apply -f pvc.yaml

# Use in Pod
cat > pod.yaml << EOF
apiVersion: v1
kind: Pod
metadata:
  name: filestore-pod
spec:
  containers:
  - name: app
    image: nginx
    volumeMounts:
    - mountPath: /data
      name: filestore-volume
  volumes:
  - name: filestore-volume
    persistentVolumeClaim:
      claimName: filestore-pvc
EOF

kubectl apply -f pod.yaml
```

---

## Storage Best Practices

### Choosing the Right Storage

```bash
# Use Cloud Storage for:
# - Object/blob storage (images, videos, backups)
# - Static website hosting
# - Data lake / archive
# - Content delivery
gsutil mb -c STANDARD -l us gs://my-object-storage

# Use Persistent Disk for:
# - VM boot disks
# - Database storage (attached to single VM)
# - High-performance applications
gcloud compute disks create db-disk --type=pd-ssd --size=500GB

# Use Filestore for:
# - Shared file system (multiple VMs/containers)
# - Home directories
# - Media rendering
# - Legacy applications requiring NFS
gcloud filestore instances create shared-fs --tier=BASIC_SSD

# Use Cloud SQL/Firestore/Bigtable for:
# - Structured data with queries
# - See Database Services topic
```

### Cost Optimization

```bash
# Use appropriate storage class
gsutil mb -c NEARLINE gs://infrequent-access  # <1/month
gsutil mb -c COLDLINE gs://quarterly-access    # <1/quarter
gsutil mb -c ARCHIVE gs://yearly-archive       # <1/year

# Set lifecycle policies to auto-transition
cat > lifecycle.json << EOF
{
  "lifecycle": {
    "rule": [
      {
        "action": {"type": "SetStorageClass", "storageClass": "NEARLINE"},
        "condition": {"age": 30}
      },
      {
        "action": {"type": "SetStorageClass", "storageClass": "COLDLINE"},
        "condition": {"age": 90}
      },
      {
        "action": {"type": "Delete"},
        "condition": {"age": 365}
      }
    ]
  }
}
EOF

gsutil lifecycle set lifecycle.json gs://my-bucket

# Use balanced disks instead of SSD where possible
gcloud compute disks create cost-effective \
  --type=pd-balanced \
  --size=100GB

# Delete unused snapshots
gcloud compute snapshots list --format="table(name,creationTimestamp)"
gcloud compute snapshots delete old-snapshot

# Use regional storage instead of multi-region if possible
gsutil mb -l us-central1 gs://regional-bucket  # Cheaper than multi-region
```

### Security Best Practices

```bash
# Enable encryption with customer-managed keys
gcloud kms keyrings create my-keyring --location=us-central1
gcloud kms keys create my-key --keyring=my-keyring --location=us-central1 --purpose=encryption

gsutil -o "GSUtil:encryption_key=projects/PROJECT_ID/locations/us-central1/keyRings/my-keyring/cryptoKeys/my-key" \
  cp file.txt gs://my-bucket/

# Enable uniform bucket-level access
gsutil uniformbucketlevelaccess set on gs://my-bucket

# Block public access
gcloud storage buckets update gs://my-bucket --public-access-prevention

# Enable audit logging
# In Cloud Console: IAM & Admin > Audit Logs > Cloud Storage

# Use VPC Service Controls
# Restricts data access to specific VPCs
```

---

## Summary

| Service | Type | Best For | Capacity | Access Pattern |
|---------|------|----------|----------|----------------|
| **Cloud Storage** | Object | Unstructured data, backups, media | Unlimited | HTTP/HTTPS, global |
| **Persistent Disk** | Block | VM disks, databases | 64 TB | Attached to single VM |
| **Filestore** | File (NFS) | Shared file system | 100 TB | Multiple VMs/containers |

**Key Commands**:
```bash
# Cloud Storage
gsutil mb gs://bucket      # Create bucket
gsutil cp file gs://bucket # Upload
gsutil ls gs://bucket      # List

# Persistent Disk
gcloud compute disks create   # Create disk
gcloud compute instances attach-disk  # Attach to VM
gcloud compute disks snapshot  # Backup

# Filestore
gcloud filestore instances create  # Create instance
mount IP:/share /mnt/path         # Mount on VM
```

## Next Topic

Continue to [Networking](./05-networking.md) to learn about GCP's networking services.
