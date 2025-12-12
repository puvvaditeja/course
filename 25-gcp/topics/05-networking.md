# GCP Networking

## Overview of GCP Networking

Google Cloud Platform's networking services provide a global, secure, and high-performance infrastructure for your applications.

### GCP Networking Services

| Service | Purpose | Use Case |
|---------|---------|----------|
| **VPC (Virtual Private Cloud)** | Private network | Isolate resources, define IP ranges |
| **Cloud Load Balancing** | Distribute traffic | High availability, auto-scaling |
| **Cloud CDN** | Content delivery | Cache static content globally |
| **Cloud DNS** | Domain name resolution | Manage DNS records |
| **Cloud NAT** | Network address translation | Outbound internet for private VMs |
| **Cloud VPN** | Secure connection | Connect on-premise to GCP |
| **Cloud Interconnect** | Dedicated connection | High-bandwidth, low-latency to on-premise |
| **Firewall Rules** | Network security | Control ingress/egress traffic |

---

## Virtual Private Cloud (VPC)

VPC is a private network within GCP that provides connectivity for your resources.

### VPC Key Concepts

- **VPC**: Global resource spanning all regions
- **Subnet**: Regional resource with IP range (CIDR)
- **Routes**: Define paths for network traffic
- **Firewall Rules**: Control traffic to/from instances

### Creating and Managing VPCs

```bash
# Create VPC (auto mode - subnets created automatically)
gcloud compute networks create my-vpc --subnet-mode=auto

# Create VPC (custom mode - you create subnets)
gcloud compute networks create my-custom-vpc --subnet-mode=custom

# List VPCs
gcloud compute networks list

# Describe VPC
gcloud compute networks describe my-custom-vpc

# Delete VPC (must delete all resources first)
gcloud compute networks delete my-vpc
```

### Creating and Managing Subnets

```bash
# Create subnet in custom VPC
gcloud compute networks subnets create my-subnet \
  --network=my-custom-vpc \
  --region=us-central1 \
  --range=10.0.1.0/24

# Create subnet with secondary IP range (for GKE pods)
gcloud compute networks subnets create gke-subnet \
  --network=my-custom-vpc \
  --region=us-central1 \
  --range=10.0.2.0/24 \
  --secondary-range=pods=10.1.0.0/16,services=10.2.0.0/20

# Create subnet with private Google access
gcloud compute networks subnets create private-subnet \
  --network=my-custom-vpc \
  --region=us-central1 \
  --range=10.0.3.0/24 \
  --enable-private-ip-google-access

# List subnets
gcloud compute networks subnets list

# List subnets in specific region
gcloud compute networks subnets list --filter="region:us-central1"

# Describe subnet
gcloud compute networks subnets describe my-subnet --region=us-central1

# Expand subnet IP range (can only expand, not shrink)
gcloud compute networks subnets expand-ip-range my-subnet \
  --region=us-central1 \
  --prefix-length=20

# Delete subnet
gcloud compute networks subnets delete my-subnet --region=us-central1
```

### Firewall Rules

```bash
# Create firewall rule to allow HTTP
gcloud compute firewall-rules create allow-http \
  --network=my-custom-vpc \
  --allow=tcp:80 \
  --source-ranges=0.0.0.0/0 \
  --target-tags=http-server

# Create firewall rule to allow HTTPS
gcloud compute firewall-rules create allow-https \
  --network=my-custom-vpc \
  --allow=tcp:443 \
  --source-ranges=0.0.0.0/0 \
  --target-tags=https-server

# Create firewall rule to allow SSH from specific IP
gcloud compute firewall-rules create allow-ssh \
  --network=my-custom-vpc \
  --allow=tcp:22 \
  --source-ranges=203.0.113.0/24 \
  --target-tags=ssh-access

# Create firewall rule for internal traffic
gcloud compute firewall-rules create allow-internal \
  --network=my-custom-vpc \
  --allow=tcp:0-65535,udp:0-65535,icmp \
  --source-ranges=10.0.0.0/8

# Create firewall rule with priority
gcloud compute firewall-rules create high-priority-rule \
  --network=my-custom-vpc \
  --priority=100 \
  --allow=tcp:8080 \
  --source-ranges=0.0.0.0/0

# Create egress (outbound) firewall rule
gcloud compute firewall-rules create deny-internet-egress \
  --network=my-custom-vpc \
  --direction=EGRESS \
  --action=DENY \
  --rules=all \
  --destination-ranges=0.0.0.0/0 \
  --priority=1000 \
  --target-tags=no-internet

# List firewall rules
gcloud compute firewall-rules list

# List firewall rules for specific network
gcloud compute firewall-rules list --filter="network:my-custom-vpc"

# Describe firewall rule
gcloud compute firewall-rules describe allow-http

# Update firewall rule
gcloud compute firewall-rules update allow-http \
  --source-ranges=10.0.0.0/8

# Delete firewall rule
gcloud compute firewall-rules delete allow-http
```

### VPC Network Peering

```bash
# Create VPC peering (connect two VPCs)
gcloud compute networks peerings create peer-vpc1-to-vpc2 \
  --network=vpc1 \
  --peer-network=vpc2 \
  --peer-project=PROJECT_ID

# Create reverse peering
gcloud compute networks peerings create peer-vpc2-to-vpc1 \
  --network=vpc2 \
  --peer-network=vpc1 \
  --peer-project=PROJECT_ID

# List peerings
gcloud compute networks peerings list

# Delete peering
gcloud compute networks peerings delete peer-vpc1-to-vpc2 --network=vpc1
```

### Shared VPC

```bash
# Enable shared VPC on host project
gcloud compute shared-vpc enable HOST_PROJECT_ID

# Attach service project to host project
gcloud compute shared-vpc associated-projects add SERVICE_PROJECT_ID \
  --host-project=HOST_PROJECT_ID

# List associated projects
gcloud compute shared-vpc get-host-project SERVICE_PROJECT_ID

# Grant access to service project admins
gcloud projects add-iam-policy-binding HOST_PROJECT_ID \
  --member="serviceAccount:SERVICE_PROJECT_NUMBER@cloudservices.gserviceaccount.com" \
  --role="roles/compute.networkUser"
```

### Static IP Addresses

```bash
# Reserve external static IP (regional)
gcloud compute addresses create my-static-ip --region=us-central1

# Reserve external static IP (global - for load balancers)
gcloud compute addresses create my-global-ip --global

# Reserve internal static IP
gcloud compute addresses create my-internal-ip \
  --region=us-central1 \
  --subnet=my-subnet \
  --addresses=10.0.1.100

# List IP addresses
gcloud compute addresses list

# Describe IP address
gcloud compute addresses describe my-static-ip --region=us-central1

# Release IP address
gcloud compute addresses delete my-static-ip --region=us-central1
```

### Cloud NAT

```bash
# Create Cloud Router (required for Cloud NAT)
gcloud compute routers create my-router \
  --network=my-custom-vpc \
  --region=us-central1

# Create Cloud NAT configuration
gcloud compute routers nats create my-nat \
  --router=my-router \
  --region=us-central1 \
  --nat-all-subnet-ip-ranges \
  --auto-allocate-nat-external-ips

# Create NAT with specific IP addresses
gcloud compute addresses create nat-ip1 --region=us-central1
gcloud compute addresses create nat-ip2 --region=us-central1

gcloud compute routers nats create my-custom-nat \
  --router=my-router \
  --region=us-central1 \
  --nat-all-subnet-ip-ranges \
  --nat-external-ip-pool=nat-ip1,nat-ip2

# List Cloud NAT configurations
gcloud compute routers nats list --router=my-router --region=us-central1

# Delete Cloud NAT
gcloud compute routers nats delete my-nat --router=my-router --region=us-central1
```

---

## Load Balancing

Google Cloud Load Balancing distributes traffic across multiple instances to ensure high availability and reliability.

### Load Balancer Types

| Type | Scope | Traffic Type | Use Case |
|------|-------|--------------|----------|
| **HTTP(S) Load Balancer** | Global | HTTP/HTTPS | Web applications, APIs |
| **TCP Proxy Load Balancer** | Global | TCP | Non-HTTP traffic (global) |
| **SSL Proxy Load Balancer** | Global | SSL/TLS | SSL offloading (global) |
| **Network Load Balancer** | Regional | TCP/UDP | Low latency, preserve client IP |
| **Internal HTTP(S) Load Balancer** | Regional | HTTP/HTTPS | Internal microservices |
| **Internal TCP/UDP Load Balancer** | Regional | TCP/UDP | Internal services |

### Creating HTTP(S) Load Balancer

```bash
# Step 1: Create instance template
gcloud compute instance-templates create web-template \
  --machine-type=e2-medium \
  --tags=http-server \
  --metadata=startup-script='#!/bin/bash
    apt-get update
    apt-get install -y nginx
    systemctl start nginx
    echo "Server $(hostname)" > /var/www/html/index.html'

# Step 2: Create managed instance group
gcloud compute instance-groups managed create web-group \
  --base-instance-name=web \
  --template=web-template \
  --size=3 \
  --zone=us-central1-a

# Step 3: Set named port on instance group
gcloud compute instance-groups managed set-named-ports web-group \
  --named-ports=http:80 \
  --zone=us-central1-a

# Step 4: Create health check
gcloud compute health-checks create http web-health-check \
  --port=80 \
  --check-interval=10s \
  --timeout=5s \
  --healthy-threshold=2 \
  --unhealthy-threshold=3 \
  --request-path=/

# Step 5: Create backend service
gcloud compute backend-services create web-backend \
  --protocol=HTTP \
  --health-checks=web-health-check \
  --global

# Step 6: Add instance group to backend service
gcloud compute backend-services add-backend web-backend \
  --instance-group=web-group \
  --instance-group-zone=us-central1-a \
  --balancing-mode=UTILIZATION \
  --max-utilization=0.8 \
  --global

# Step 7: Create URL map
gcloud compute url-maps create web-map \
  --default-service=web-backend

# Step 8: Create target HTTP proxy
gcloud compute target-http-proxies create web-proxy \
  --url-map=web-map

# Step 9: Reserve global static IP
gcloud compute addresses create web-lb-ip --global

# Step 10: Create forwarding rule
gcloud compute forwarding-rules create web-forwarding-rule \
  --global \
  --target-http-proxy=web-proxy \
  --address=web-lb-ip \
  --ports=80

# Get load balancer IP
gcloud compute addresses describe web-lb-ip --global --format="value(address)"

# Test load balancer
curl http://LOAD_BALANCER_IP
```

### HTTPS Load Balancer with SSL Certificate

```bash
# Create SSL certificate (managed)
gcloud compute ssl-certificates create my-ssl-cert \
  --domains=example.com,www.example.com \
  --global

# Or upload existing certificate
gcloud compute ssl-certificates create my-ssl-cert \
  --certificate=cert.pem \
  --private-key=key.pem \
  --global

# Create target HTTPS proxy
gcloud compute target-https-proxies create web-https-proxy \
  --url-map=web-map \
  --ssl-certificates=my-ssl-cert

# Create HTTPS forwarding rule
gcloud compute forwarding-rules create web-https-forwarding-rule \
  --global \
  --target-https-proxy=web-https-proxy \
  --address=web-lb-ip \
  --ports=443

# Redirect HTTP to HTTPS (create URL map redirect)
gcloud compute url-maps import web-map \
  --source=<(cat << EOF
name: web-map
defaultService: https://www.googleapis.com/compute/v1/projects/PROJECT_ID/global/backendServices/web-backend
hostRules:
- hosts:
  - "*"
  pathMatcher: path-matcher-1
pathMatchers:
- name: path-matcher-1
  defaultService: https://www.googleapis.com/compute/v1/projects/PROJECT_ID/global/backendServices/web-backend
EOF
)
```

### Network Load Balancer

```bash
# Create health check
gcloud compute health-checks create tcp tcp-health-check \
  --port=80

# Create target pool
gcloud compute target-pools create my-target-pool \
  --region=us-central1 \
  --health-check=tcp-health-check

# Add instances to target pool
gcloud compute target-pools add-instances my-target-pool \
  --instances=instance-1,instance-2,instance-3 \
  --instances-zone=us-central1-a

# Reserve static IP
gcloud compute addresses create network-lb-ip --region=us-central1

# Create forwarding rule
gcloud compute forwarding-rules create network-lb-forwarding-rule \
  --region=us-central1 \
  --ports=80 \
  --address=network-lb-ip \
  --target-pool=my-target-pool

# Get load balancer IP
gcloud compute addresses describe network-lb-ip --region=us-central1
```

### Internal Load Balancer

```bash
# Create internal HTTP health check
gcloud compute health-checks create http internal-health-check \
  --region=us-central1 \
  --port=80

# Create backend service (regional)
gcloud compute backend-services create internal-backend \
  --protocol=HTTP \
  --health-checks=internal-health-check \
  --health-checks-region=us-central1 \
  --region=us-central1

# Add instance group to backend
gcloud compute backend-services add-backend internal-backend \
  --instance-group=web-group \
  --instance-group-zone=us-central1-a \
  --region=us-central1

# Create URL map (regional)
gcloud compute url-maps create internal-map \
  --default-service=internal-backend \
  --region=us-central1

# Create target proxy (regional)
gcloud compute target-http-proxies create internal-proxy \
  --url-map=internal-map \
  --region=us-central1

# Create forwarding rule (internal IP)
gcloud compute forwarding-rules create internal-forwarding-rule \
  --load-balancing-scheme=INTERNAL_MANAGED \
  --network=my-custom-vpc \
  --subnet=my-subnet \
  --address=10.0.1.100 \
  --target-http-proxy=internal-proxy \
  --target-http-proxy-region=us-central1 \
  --ports=80 \
  --region=us-central1
```

---

## Cloud CDN

Cloud CDN (Content Delivery Network) caches content at Google's edge locations for faster delivery.

### Enabling Cloud CDN

```bash
# Enable Cloud CDN on backend service
gcloud compute backend-services update web-backend \
  --enable-cdn \
  --global

# Configure cache mode
gcloud compute backend-services update web-backend \
  --cache-mode=CACHE_ALL_STATIC \
  --global

# Set custom TTL
gcloud compute backend-services update web-backend \
  --enable-cdn \
  --cache-mode=USE_ORIGIN_HEADERS \
  --default-ttl=3600 \
  --max-ttl=86400 \
  --client-ttl=3600 \
  --global

# Configure cache key policy
gcloud compute backend-services update web-backend \
  --cache-key-include-protocol \
  --cache-key-include-host \
  --cache-key-include-query-string \
  --global

# Invalidate cache
gcloud compute url-maps invalidate-cdn-cache web-map \
  --path="/*" \
  --global

# Invalidate specific paths
gcloud compute url-maps invalidate-cdn-cache web-map \
  --path="/images/*" \
  --path="/css/style.css" \
  --global
```

### Cloud CDN with Cloud Storage

```bash
# Create Cloud Storage bucket
gsutil mb gs://my-cdn-bucket

# Upload static content
gsutil cp -r ./static-content/* gs://my-cdn-bucket/

# Make bucket public
gsutil iam ch allUsers:objectViewer gs://my-cdn-bucket

# Create backend bucket
gcloud compute backend-buckets create static-backend \
  --gcs-bucket-name=my-cdn-bucket \
  --enable-cdn

# Add to URL map
gcloud compute url-maps add-path-matcher web-map \
  --path-matcher-name=static-matcher \
  --default-service=web-backend \
  --backend-bucket-path-rules="/static/*=static-backend" \
  --global
```

---

## Cloud DNS

Cloud DNS is a scalable, reliable, and managed authoritative Domain Name System (DNS) service.

### Creating and Managing DNS Zones

```bash
# Create public DNS zone
gcloud dns managed-zones create my-zone \
  --dns-name="example.com." \
  --description="My DNS zone"

# Create private DNS zone (for VPC)
gcloud dns managed-zones create private-zone \
  --dns-name="internal.example.com." \
  --description="Private DNS zone" \
  --visibility=private \
  --networks=my-custom-vpc

# List DNS zones
gcloud dns managed-zones list

# Describe DNS zone
gcloud dns managed-zones describe my-zone

# Delete DNS zone (must remove all records first except NS and SOA)
gcloud dns managed-zones delete my-zone
```

### Managing DNS Records

```bash
# Start transaction
gcloud dns record-sets transaction start --zone=my-zone

# Add A record
gcloud dns record-sets transaction add "203.0.113.1" \
  --name="www.example.com." \
  --ttl=300 \
  --type=A \
  --zone=my-zone

# Add CNAME record
gcloud dns record-sets transaction add "www.example.com." \
  --name="blog.example.com." \
  --ttl=300 \
  --type=CNAME \
  --zone=my-zone

# Add MX record
gcloud dns record-sets transaction add "10 mail.example.com." \
  --name="example.com." \
  --ttl=300 \
  --type=MX \
  --zone=my-zone

# Add TXT record (SPF)
gcloud dns record-sets transaction add "v=spf1 include:_spf.google.com ~all" \
  --name="example.com." \
  --ttl=300 \
  --type=TXT \
  --zone=my-zone

# Execute transaction
gcloud dns record-sets transaction execute --zone=my-zone

# List all records
gcloud dns record-sets list --zone=my-zone

# Update existing record (transaction)
gcloud dns record-sets transaction start --zone=my-zone
gcloud dns record-sets transaction remove "203.0.113.1" \
  --name="www.example.com." \
  --ttl=300 \
  --type=A \
  --zone=my-zone
gcloud dns record-sets transaction add "203.0.113.2" \
  --name="www.example.com." \
  --ttl=300 \
  --type=A \
  --zone=my-zone
gcloud dns record-sets transaction execute --zone=my-zone

# Delete record
gcloud dns record-sets transaction start --zone=my-zone
gcloud dns record-sets transaction remove "203.0.113.2" \
  --name="www.example.com." \
  --ttl=300 \
  --type=A \
  --zone=my-zone
gcloud dns record-sets transaction execute --zone=my-zone
```

### DNS Policies

```bash
# Create DNS policy to enable inbound forwarding
gcloud dns policies create inbound-policy \
  --description="Allow inbound DNS queries" \
  --enable-inbound-forwarding \
  --networks=my-custom-vpc

# Create DNS policy with outbound forwarding
gcloud dns policies create outbound-policy \
  --description="Forward to on-premise DNS" \
  --networks=my-custom-vpc \
  --alternative-name-servers=10.0.0.53,10.0.0.54

# List DNS policies
gcloud dns policies list

# Delete DNS policy
gcloud dns policies delete outbound-policy
```

---

## Network Connectivity

### Cloud VPN

```bash
# Create VPN gateway
gcloud compute vpn-gateways create my-vpn-gateway \
  --network=my-custom-vpc \
  --region=us-central1

# Create Cloud Router
gcloud compute routers create vpn-router \
  --network=my-custom-vpc \
  --region=us-central1 \
  --asn=65001

# Create VPN tunnel
gcloud compute vpn-tunnels create tunnel-1 \
  --peer-address=203.0.113.1 \
  --region=us-central1 \
  --ike-version=2 \
  --shared-secret=mysharedsecret \
  --router=vpn-router \
  --vpn-gateway=my-vpn-gateway

# Configure BGP peering
gcloud compute routers add-bgp-peer vpn-router \
  --peer-name=on-premise \
  --peer-asn=65002 \
  --interface=0 \
  --peer-ip-address=169.254.1.1 \
  --region=us-central1

# List VPN gateways
gcloud compute vpn-gateways list

# List VPN tunnels
gcloud compute vpn-tunnels list
```

---

## Summary

| Service | Purpose | Scope | Key Command |
|---------|---------|-------|-------------|
| **VPC** | Private network | Global | `gcloud compute networks create` |
| **Subnet** | IP range | Regional | `gcloud compute networks subnets create` |
| **Firewall** | Network security | VPC | `gcloud compute firewall-rules create` |
| **Load Balancer** | Traffic distribution | Global/Regional | `gcloud compute backend-services create` |
| **Cloud CDN** | Content caching | Global | `gcloud compute backend-services update --enable-cdn` |
| **Cloud DNS** | Domain name resolution | Global | `gcloud dns managed-zones create` |

**Common Networking Patterns**:

```bash
# Pattern 1: Public web application
# VPC → Subnet → Firewall (allow HTTP/HTTPS) → Instance Group → Load Balancer → Cloud CDN

# Pattern 2: Internal microservices
# VPC → Multiple Subnets → Internal Load Balancer → Service Mesh

# Pattern 3: Hybrid cloud
# VPC → Cloud VPN/Interconnect → On-Premise Network

# Pattern 4: Private VMs with internet access
# VPC → Private Subnet → Cloud NAT → Internet
```

## Next Topic

Continue to [Database Services](./06-database-services.md) to learn about GCP's database offerings.
