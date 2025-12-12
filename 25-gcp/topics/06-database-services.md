# GCP Database Services

## Overview of Database Services

Google Cloud Platform offers a variety of managed database services for different data models and use cases.

### Database Service Comparison

| Service | Type | Use Case | Scale | Consistency |
|---------|------|----------|-------|-------------|
| **Cloud SQL** | Relational (MySQL, PostgreSQL, SQL Server) | Traditional applications, OLTP | Up to 64 TB | Strong |
| **Cloud Spanner** | Relational (Distributed) | Global applications, high availability | Petabytes | Strong (global) |
| **Firestore** | NoSQL Document | Mobile/web apps, real-time sync | Terabytes | Eventual/Strong |
| **Bigtable** | NoSQL Wide-column | Time-series, IoT, analytics | Petabytes | Eventual |
| **Memorystore** | In-memory (Redis, Memcached) | Caching, session storage | 300 GB | N/A (cache) |
| **BigQuery** | Data Warehouse | Analytics, business intelligence | Petabytes | Strong |

### Choosing the Right Database

```
Need SQL and ACID transactions?
├─ Global with strong consistency? → Cloud Spanner
└─ Regional, traditional? → Cloud SQL

Need NoSQL?
├─ Document model with real-time? → Firestore
├─ Wide-column, time-series, IoT? → Bigtable
└─ Key-value, caching? → Memorystore

Need analytics?
└─ Data warehouse, BI queries? → BigQuery
```

---

## Cloud SQL

Cloud SQL is a fully managed relational database service for MySQL, PostgreSQL, and SQL Server.

### Creating Cloud SQL Instances

#### MySQL Instance

```bash
# Create MySQL instance
gcloud sql instances create my-mysql-instance \
  --database-version=MYSQL_8_0 \
  --tier=db-n1-standard-2 \
  --region=us-central1 \
  --root-password=MySecurePassword123

# Create with additional configuration
gcloud sql instances create prod-mysql \
  --database-version=MYSQL_8_0 \
  --tier=db-n1-standard-4 \
  --region=us-central1 \
  --storage-size=100GB \
  --storage-type=SSD \
  --storage-auto-increase \
  --backup-start-time=03:00 \
  --enable-bin-log \
  --maintenance-window-day=SUN \
  --maintenance-window-hour=4 \
  --root-password=MySecurePassword123

# Create high availability instance
gcloud sql instances create ha-mysql \
  --database-version=MYSQL_8_0 \
  --tier=db-n1-standard-2 \
  --region=us-central1 \
  --availability-type=REGIONAL \
  --root-password=MySecurePassword123
```

#### PostgreSQL Instance

```bash
# Create PostgreSQL instance
gcloud sql instances create my-postgres-instance \
  --database-version=POSTGRES_15 \
  --tier=db-custom-2-8192 \
  --region=us-central1 \
  --root-password=MySecurePassword123

# Create with custom machine type (2 vCPUs, 8 GB RAM)
gcloud sql instances create custom-postgres \
  --database-version=POSTGRES_15 \
  --tier=db-custom-4-16384 \
  --region=us-central1 \
  --storage-size=200GB \
  --root-password=MySecurePassword123
```

#### SQL Server Instance

```bash
# Create SQL Server instance
gcloud sql instances create my-sqlserver-instance \
  --database-version=SQLSERVER_2019_STANDARD \
  --tier=db-custom-2-8192 \
  --region=us-central1 \
  --root-password=MySecurePassword123
```

### Managing Cloud SQL Instances

```bash
# List instances
gcloud sql instances list

# Describe instance
gcloud sql instances describe my-mysql-instance

# Start instance
gcloud sql instances patch my-mysql-instance --activation-policy=ALWAYS

# Stop instance
gcloud sql instances patch my-mysql-instance --activation-policy=NEVER

# Restart instance
gcloud sql instances restart my-mysql-instance

# Delete instance
gcloud sql instances delete my-mysql-instance

# Update instance configuration
gcloud sql instances patch my-mysql-instance \
  --tier=db-n1-standard-4 \
  --storage-size=200GB

# Enable point-in-time recovery
gcloud sql instances patch my-mysql-instance \
  --enable-bin-log \
  --backup-start-time=03:00

# Clone instance
gcloud sql instances clone my-mysql-instance cloned-instance
```

### Creating Databases and Users

```bash
# Create database
gcloud sql databases create myapp_db --instance=my-mysql-instance

# List databases
gcloud sql databases list --instance=my-mysql-instance

# Delete database
gcloud sql databases delete myapp_db --instance=my-mysql-instance

# Create user
gcloud sql users create myuser \
  --instance=my-mysql-instance \
  --password=UserPassword123

# Create user for specific host
gcloud sql users create appuser \
  --instance=my-mysql-instance \
  --host=% \
  --password=AppPassword123

# List users
gcloud sql users list --instance=my-mysql-instance

# Change user password
gcloud sql users set-password myuser \
  --instance=my-mysql-instance \
  --password=NewPassword123

# Delete user
gcloud sql users delete myuser --instance=my-mysql-instance
```

### Connecting to Cloud SQL

#### Using Cloud SQL Proxy

```bash
# Download and install Cloud SQL Proxy
curl -o cloud-sql-proxy https://storage.googleapis.com/cloud-sql-connectors/cloud-sql-proxy/v2.8.0/cloud-sql-proxy.linux.amd64
chmod +x cloud-sql-proxy

# Start proxy
./cloud-sql-proxy PROJECT_ID:us-central1:my-mysql-instance

# Connect via mysql client
mysql -u root -p -h 127.0.0.1

# Or specify port
./cloud-sql-proxy PROJECT_ID:us-central1:my-mysql-instance --port=3307
mysql -u root -p -h 127.0.0.1 --port=3307
```

#### Using gcloud Command

```bash
# Connect directly
gcloud sql connect my-mysql-instance --user=root

# Connect to PostgreSQL
gcloud sql connect my-postgres-instance --user=postgres --database=postgres
```

#### Application Connection Strings

```bash
# MySQL
mysql://username:password@/database?unix_socket=/cloudsql/PROJECT_ID:us-central1:my-mysql-instance

# PostgreSQL
postgresql://username:password@/database?host=/cloudsql/PROJECT_ID:us-central1:my-postgres-instance

# Using private IP (if configured)
mysql://username:password@PRIVATE_IP:3306/database
```

#### Connection from Application (Python)

```python
# Using Cloud SQL Python Connector
from google.cloud.sql.connector import Connector
import sqlalchemy

# Initialize Connector
connector = Connector()

def getconn():
    conn = connector.connect(
        "PROJECT_ID:us-central1:my-mysql-instance",
        "pymysql",
        user="myuser",
        password="password",
        db="myapp_db"
    )
    return conn

# Create SQLAlchemy engine
pool = sqlalchemy.create_engine(
    "mysql+pymysql://",
    creator=getconn,
)

# Use the connection pool
with pool.connect() as db_conn:
    result = db_conn.execute(sqlalchemy.text("SELECT NOW()")).fetchone()
    print(result)
```

### Backups and Recovery

```bash
# Create on-demand backup
gcloud sql backups create \
  --instance=my-mysql-instance \
  --description="Before major upgrade"

# List backups
gcloud sql backups list --instance=my-mysql-instance

# Describe backup
gcloud sql backups describe BACKUP_ID --instance=my-mysql-instance

# Restore from backup
gcloud sql backups restore BACKUP_ID \
  --backup-instance=my-mysql-instance \
  --backup-id=BACKUP_ID

# Export database to Cloud Storage
gcloud sql export sql my-mysql-instance gs://my-bucket/backup.sql \
  --database=myapp_db

# Export in CSV format
gcloud sql export csv my-mysql-instance gs://my-bucket/export.csv \
  --database=myapp_db \
  --query="SELECT * FROM users WHERE active=1"

# Import from Cloud Storage
gcloud sql import sql my-mysql-instance gs://my-bucket/backup.sql \
  --database=myapp_db

# Import CSV
gcloud sql import csv my-mysql-instance gs://my-bucket/data.csv \
  --database=myapp_db \
  --table=users
```

---

## Cloud Spanner

Cloud Spanner is a fully managed, horizontally scalable, globally distributed relational database with strong consistency.

### Creating Spanner Instances

```bash
# Create Spanner instance
gcloud spanner instances create my-spanner-instance \
  --config=regional-us-central1 \
  --description="My Spanner Instance" \
  --nodes=1

# Create multi-region instance
gcloud spanner instances create global-spanner \
  --config=nam3 \
  --description="North America multi-region" \
  --nodes=3

# Create with processing units (more granular than nodes)
gcloud spanner instances create small-spanner \
  --config=regional-us-central1 \
  --processing-units=100

# List available configurations
gcloud spanner instance-configs list

# List instances
gcloud spanner instances list

# Describe instance
gcloud spanner instances describe my-spanner-instance

# Update instance (scale up/down)
gcloud spanner instances update my-spanner-instance --nodes=3

# Delete instance
gcloud spanner instances delete my-spanner-instance
```

### Creating and Managing Databases

```bash
# Create database
gcloud spanner databases create my-database \
  --instance=my-spanner-instance

# Create database with DDL
gcloud spanner databases create my-database \
  --instance=my-spanner-instance \
  --ddl='CREATE TABLE Users (
    UserId INT64 NOT NULL,
    UserName STRING(1024),
    Email STRING(1024)
  ) PRIMARY KEY (UserId)'

# List databases
gcloud spanner databases list --instance=my-spanner-instance

# Execute DDL
gcloud spanner databases ddl update my-database \
  --instance=my-spanner-instance \
  --ddl='CREATE TABLE Orders (
    OrderId INT64 NOT NULL,
    UserId INT64,
    OrderDate TIMESTAMP,
    FOREIGN KEY (UserId) REFERENCES Users (UserId)
  ) PRIMARY KEY (OrderId)'

# Describe database
gcloud spanner databases describe my-database \
  --instance=my-spanner-instance

# Delete database
gcloud spanner databases delete my-database \
  --instance=my-spanner-instance
```

### Querying Spanner

```bash
# Execute SQL query
gcloud spanner databases execute-sql my-database \
  --instance=my-spanner-instance \
  --sql='SELECT * FROM Users WHERE UserId = 1'

# Execute query with parameters
gcloud spanner databases execute-sql my-database \
  --instance=my-spanner-instance \
  --sql='SELECT * FROM Users WHERE Email = @email' \
  --params=email=user@example.com

# Query in JSON format
gcloud spanner databases execute-sql my-database \
  --instance=my-spanner-instance \
  --sql='SELECT * FROM Users LIMIT 10' \
  --format=json
```

### Application Code (Python)

```python
from google.cloud import spanner

# Initialize client
spanner_client = spanner.Client()

# Get instance and database
instance = spanner_client.instance('my-spanner-instance')
database = instance.database('my-database')

# Insert data
def insert_users(transaction):
    row_data = [
        (1, 'Alice', 'alice@example.com'),
        (2, 'Bob', 'bob@example.com'),
    ]
    transaction.insert(
        table='Users',
        columns=('UserId', 'UserName', 'Email'),
        values=row_data
    )

database.run_in_transaction(insert_users)

# Query data
with database.snapshot() as snapshot:
    results = snapshot.execute_sql(
        'SELECT UserId, UserName, Email FROM Users'
    )
    for row in results:
        print(f'User: {row[0]}, {row[1]}, {row[2]}')

# Update data
def update_user(transaction):
    transaction.update(
        table='Users',
        columns=('UserId', 'Email'),
        values=[(1, 'newemail@example.com')]
    )

database.run_in_transaction(update_user)
```

### Backups

```bash
# Create backup
gcloud spanner backups create my-backup \
  --instance=my-spanner-instance \
  --database=my-database \
  --retention-period=7d

# List backups
gcloud spanner backups list --instance=my-spanner-instance

# Describe backup
gcloud spanner backups describe my-backup \
  --instance=my-spanner-instance

# Restore from backup
gcloud spanner databases create restored-database \
  --instance=my-spanner-instance \
  --source-backup=my-backup \
  --source-instance=my-spanner-instance

# Delete backup
gcloud spanner backups delete my-backup \
  --instance=my-spanner-instance
```

---

## Firestore

Firestore is a NoSQL document database built for automatic scaling, high performance, and ease of application development.

### Firestore Modes

- **Native Mode**: Designed for mobile and web apps with real-time sync
- **Datastore Mode**: Compatible with Cloud Datastore, server-side applications

### Creating Firestore Database

```bash
# Create Firestore database in Native mode
gcloud firestore databases create --region=us-central1

# Create in Datastore mode
gcloud firestore databases create --type=datastore-mode --region=us-central1

# Note: Can only have one Firestore database per project
```

### Using Firestore (Python)

```python
from google.cloud import firestore

# Initialize Firestore client
db = firestore.Client()

# Add document
doc_ref = db.collection('users').document('user1')
doc_ref.set({
    'name': 'Alice',
    'email': 'alice@example.com',
    'age': 30,
    'created': firestore.SERVER_TIMESTAMP
})

# Add document with auto-generated ID
db.collection('users').add({
    'name': 'Bob',
    'email': 'bob@example.com',
    'age': 25
})

# Get document
doc = db.collection('users').document('user1').get()
if doc.exists:
    print(f'Document data: {doc.to_dict()}')
else:
    print('No such document!')

# Query documents
users_ref = db.collection('users')
query = users_ref.where('age', '>=', 25)
results = query.get()
for doc in results:
    print(f'{doc.id} => {doc.to_dict()}')

# Update document
doc_ref = db.collection('users').document('user1')
doc_ref.update({
    'age': 31,
    'updated': firestore.SERVER_TIMESTAMP
})

# Update specific fields
doc_ref.update({
    'favorites.color': 'blue'
})

# Delete document
db.collection('users').document('user1').delete()

# Delete field
doc_ref.update({
    'age': firestore.DELETE_FIELD
})

# Batch writes
batch = db.batch()
for i in range(10):
    doc_ref = db.collection('users').document(f'user{i}')
    batch.set(doc_ref, {'name': f'User {i}', 'index': i})
batch.commit()

# Transactions
@firestore.transactional
def update_user_transaction(transaction, user_ref):
    snapshot = user_ref.get(transaction=transaction)
    current_age = snapshot.get('age')
    transaction.update(user_ref, {'age': current_age + 1})

user_ref = db.collection('users').document('user1')
transaction = db.transaction()
update_user_transaction(transaction, user_ref)

# Real-time listeners
def on_snapshot(doc_snapshot, changes, read_time):
    for doc in doc_snapshot:
        print(f'Received document snapshot: {doc.id}')

doc_ref = db.collection('users').document('user1')
doc_watch = doc_ref.on_snapshot(on_snapshot)

# Stop listening
doc_watch.unsubscribe()
```

### Firestore Indexes

```bash
# Indexes are created automatically for simple queries
# For complex queries, create composite indexes

# Create index configuration file
cat > firestore.indexes.json << EOF
{
  "indexes": [
    {
      "collectionGroup": "users",
      "queryScope": "COLLECTION",
      "fields": [
        {"fieldPath": "age", "order": "ASCENDING"},
        {"fieldPath": "name", "order": "ASCENDING"}
      ]
    }
  ]
}
EOF

# Deploy indexes
gcloud firestore indexes composite create \
  --collection-group=users \
  --query-scope=COLLECTION \
  --field-config=field-path=age,order=ascending \
  --field-config=field-path=name,order=ascending

# List indexes
gcloud firestore indexes composite list

# Delete index
gcloud firestore indexes composite delete INDEX_NAME
```

### Export and Import

```bash
# Export entire database
gcloud firestore export gs://my-bucket/firestore-backup

# Export specific collections
gcloud firestore export gs://my-bucket/firestore-backup \
  --collection-ids=users,orders

# Import database
gcloud firestore import gs://my-bucket/firestore-backup

# Import specific collections
gcloud firestore import gs://my-bucket/firestore-backup \
  --collection-ids=users
```

---

## Bigtable

Cloud Bigtable is a NoSQL wide-column database service designed for low-latency, high-throughput workloads.

### Creating Bigtable Instances

```bash
# Create production instance
gcloud bigtable instances create my-bigtable-instance \
  --display-name="My Bigtable Instance" \
  --cluster=my-cluster \
  --cluster-zone=us-central1-a \
  --cluster-num-nodes=3

# Create development instance (cheaper, no SLA)
gcloud bigtable instances create dev-bigtable \
  --display-name="Development Bigtable" \
  --cluster=dev-cluster \
  --cluster-zone=us-central1-a \
  --instance-type=DEVELOPMENT

# Create with SSD storage
gcloud bigtable instances create ssd-bigtable \
  --display-name="SSD Bigtable" \
  --cluster=ssd-cluster \
  --cluster-zone=us-central1-a \
  --cluster-num-nodes=3 \
  --cluster-storage-type=SSD

# List instances
gcloud bigtable instances list

# Describe instance
gcloud bigtable instances describe my-bigtable-instance

# Update instance (scale)
gcloud bigtable clusters update my-cluster \
  --instance=my-bigtable-instance \
  --num-nodes=5

# Delete instance
gcloud bigtable instances delete my-bigtable-instance
```

### Creating Tables and Column Families

```bash
# Create table using cbt CLI
cbt -instance=my-bigtable-instance createtable users

# Create column family
cbt -instance=my-bigtable-instance createfamily users profile

# Create column family with GC policy
cbt -instance=my-bigtable-instance createfamily users activity
cbt -instance=my-bigtable-instance setgcpolicy users activity maxage=7d

# List tables
cbt -instance=my-bigtable-instance ls

# List column families
cbt -instance=my-bigtable-instance ls users

# Delete table
cbt -instance=my-bigtable-instance deletetable users
```

### Working with Data (cbt CLI)

```bash
# Set value
cbt -instance=my-bigtable-instance set users user1 profile:name=Alice
cbt -instance=my-bigtable-instance set users user1 profile:email=alice@example.com
cbt -instance=my-bigtable-instance set users user1 activity:login_count=5

# Read row
cbt -instance=my-bigtable-instance read users
cbt -instance=my-bigtable-instance lookup users user1

# Read with filter
cbt -instance=my-bigtable-instance read users columns="profile:.*"

# Count rows
cbt -instance=my-bigtable-instance count users

# Delete row
cbt -instance=my-bigtable-instance deleterow users user1

# Delete column
cbt -instance=my-bigtable-instance deletefamily users activity
```

### Application Code (Python)

```python
from google.cloud import bigtable
from google.cloud.bigtable import column_family

# Initialize client
client = bigtable.Client(project='PROJECT_ID', admin=True)
instance = client.instance('my-bigtable-instance')

# Create table
table_id = 'users'
table = instance.table(table_id)

# Create column families
max_age_rule = column_family.MaxAgeGCRule(timedelta(days=7))
cf = table.column_family('profile')
cf.create()

cf_activity = table.column_family('activity', gc_rule=max_age_rule)
cf_activity.create()

# Write data
row_key = 'user1'.encode()
row = table.direct_row(row_key)
row.set_cell('profile', 'name', 'Alice')
row.set_cell('profile', 'email', 'alice@example.com')
row.set_cell('activity', 'login_count', '5')
row.commit()

# Batch writes
rows = []
for i in range(100):
    row_key = f'user{i}'.encode()
    row = table.direct_row(row_key)
    row.set_cell('profile', 'name', f'User {i}')
    rows.append(row)
table.mutate_rows(rows)

# Read single row
row = table.read_row(row_key)
for cf, cols in row.cells.items():
    for col, cells in cols.items():
        for cell in cells:
            print(f'{cf}:{col} = {cell.value.decode()}')

# Read multiple rows
rows = table.read_rows()
for row_key, row in rows.items():
    print(f'Row: {row_key.decode()}')

# Read with filter
from google.cloud.bigtable import row_filters

row_filter = row_filters.ColumnQualifierRegexFilter(b'name')
rows = table.read_rows(filter_=row_filter)

# Delete row
row = table.row(row_key)
row.delete()
row.commit()
```

---

## Memorystore

Memorystore is a fully managed in-memory data store service for Redis and Memcached.

### Creating Redis Instance

```bash
# Create basic Redis instance
gcloud redis instances create my-redis \
  --size=1 \
  --region=us-central1

# Create with specific version and tier
gcloud redis instances create prod-redis \
  --size=5 \
  --region=us-central1 \
  --redis-version=redis_6_x \
  --tier=standard \
  --network=my-custom-vpc

# List instances
gcloud redis instances list

# Describe instance
gcloud redis instances describe my-redis --region=us-central1

# Update instance (scale)
gcloud redis instances update my-redis \
  --size=3 \
  --region=us-central1

# Delete instance
gcloud redis instances delete my-redis --region=us-central1

# Get connection info
gcloud redis instances describe my-redis \
  --region=us-central1 \
  --format="value(host,port)"
```

### Using Redis (Python)

```python
import redis

# Connect to Memorystore Redis
r = redis.Redis(
    host='REDIS_HOST',  # From gcloud command
    port=6379,
    decode_responses=True
)

# Set key-value
r.set('user:1:name', 'Alice')
r.set('user:1:email', 'alice@example.com')

# Get value
name = r.get('user:1:name')
print(name)  # Alice

# Set with expiration
r.setex('session:abc123', 3600, 'user_data')

# Hash operations
r.hset('user:2', mapping={
    'name': 'Bob',
    'email': 'bob@example.com',
    'age': 25
})

user = r.hgetall('user:2')
print(user)

# List operations
r.rpush('queue:tasks', 'task1', 'task2', 'task3')
task = r.lpop('queue:tasks')

# Delete key
r.delete('user:1:name')
```

---

## Summary

| Database | Type | Best For | Scale | Queries |
|----------|------|----------|-------|---------|
| **Cloud SQL** | Relational | Traditional apps, OLTP | Up to 64 TB | SQL |
| **Cloud Spanner** | Relational | Global apps, strong consistency | Petabytes | SQL |
| **Firestore** | NoSQL Document | Mobile/web, real-time | Terabytes | Document queries |
| **Bigtable** | NoSQL Wide-column | Time-series, IoT, analytics | Petabytes | Key-value, scan |
| **Memorystore** | In-memory | Caching, sessions | 300 GB | Key-value |

**Quick Decision Guide**:

```bash
# Need ACID transactions with SQL?
# Regional → Cloud SQL
gcloud sql instances create mydb --database-version=MYSQL_8_0

# Global → Cloud Spanner
gcloud spanner instances create myinstance --config=nam3

# Need NoSQL?
# Document-based → Firestore
gcloud firestore databases create

# Wide-column, high throughput → Bigtable
gcloud bigtable instances create myinstance

# Need caching?
# → Memorystore
gcloud redis instances create mycache
```

---

**Congratulations!** You've completed all GCP Database Services topics. Return to the [README](../README.md) for more resources and next steps.
