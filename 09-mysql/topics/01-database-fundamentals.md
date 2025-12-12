# Database Fundamentals

## What Is A Database?

A database is an organized collection of structured data stored electronically in a computer system. Databases are designed to manage, store, and retrieve large amounts of information efficiently.

### Key Characteristics

- **Persistent Storage**: Data persists beyond application runtime
- **Organized Structure**: Data is organized in a logical, accessible manner
- **Efficient Access**: Fast data retrieval through indexing and optimization
- **Data Integrity**: Ensures accuracy and consistency of data
- **Concurrent Access**: Multiple users can access data simultaneously
- **Security**: Controls who can access and modify data

### Types of Databases

| Type | Description | Examples | Use Cases |
|------|-------------|----------|-----------|
| Relational (RDBMS) | Data stored in tables with relationships | MySQL, PostgreSQL, Oracle | Business applications, transactions |
| Document | Stores data as documents (JSON, XML) | MongoDB, CouchDB | Content management, catalogs |
| Key-Value | Simple key-value pairs | Redis, DynamoDB | Caching, session storage |
| Graph | Nodes and edges representing relationships | Neo4j, ArangoDB | Social networks, recommendations |
| Columnar | Data stored in columns | Cassandra, HBase | Analytics, big data |

### Why Use Databases?

1. **Data Persistence**: Data survives application crashes and restarts
2. **Centralized Management**: Single source of truth for multiple applications
3. **Data Integrity**: Constraints and validations ensure data quality
4. **Concurrent Access**: Multiple users can work with data simultaneously
5. **Scalability**: Handle growing amounts of data efficiently
6. **Security**: Fine-grained access control and authentication
7. **Backup and Recovery**: Built-in mechanisms for data protection

---

## What Is SQL?

SQL (Structured Query Language) is a standardized programming language designed for managing and manipulating relational databases. It is the standard language for RDBMS.

### SQL Characteristics

- **Declarative Language**: You specify what you want, not how to get it
- **Set-Based Operations**: Works with sets of data, not individual records
- **Standardized**: ANSI/ISO standard, works across different databases
- **Portable**: SQL queries can run on different RDBMS with minimal changes
- **Powerful**: Can perform complex data operations with simple statements

### SQL Categories

SQL statements are divided into five main categories:

```
SQL Language
├── DDL (Data Definition Language)
│   ├── CREATE    - Create database objects
│   ├── ALTER     - Modify database objects
│   ├── DROP      - Delete database objects
│   └── TRUNCATE  - Remove all records from table
│
├── DML (Data Manipulation Language)
│   ├── INSERT    - Add new records
│   ├── UPDATE    - Modify existing records
│   └── DELETE    - Remove records
│
├── DQL (Data Query Language)
│   └── SELECT    - Retrieve data from database
│
├── DCL (Data Control Language)
│   ├── GRANT     - Give privileges to users
│   └── REVOKE    - Remove privileges from users
│
└── TCL (Transaction Control Language)
    ├── COMMIT    - Save transaction changes
    ├── ROLLBACK  - Undo transaction changes
    └── SAVEPOINT - Set a point within transaction
```

### SQL vs NoSQL

| Feature | SQL (Relational) | NoSQL (Non-Relational) |
|---------|------------------|------------------------|
| Schema | Fixed schema | Flexible/dynamic schema |
| Data Model | Tables with rows/columns | Documents, key-value, graph |
| Scalability | Vertical (more powerful server) | Horizontal (more servers) |
| ACID | Strong ACID compliance | Eventual consistency (mostly) |
| Query Language | SQL standard | Varies by database |
| Best For | Complex queries, transactions | Large-scale data, flexibility |

---

## Consistency

Consistency in databases ensures that data remains accurate, valid, and reliable throughout its lifecycle.

### Types of Consistency

#### 1. Data Consistency

Ensures that data adheres to defined rules and constraints:

```sql
-- Example: Email must be unique
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    age INT CHECK (age >= 18)
);

-- This ensures:
-- - No duplicate emails
-- - Email cannot be NULL
-- - Age must be 18 or older
```

#### 2. Transaction Consistency (ACID)

Part of ACID properties, ensures database moves from one valid state to another:

```sql
-- Bank transfer example
START TRANSACTION;

UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;

-- Both updates succeed or both fail - consistency maintained
COMMIT;
```

#### 3. Read Consistency

Ensures queries see a consistent snapshot of data:

- **Read Committed**: See only committed data
- **Repeatable Read**: Same query returns same results within transaction
- **Serializable**: Highest isolation, like transactions run serially

### ACID Properties

ACID is a set of properties that guarantee database transactions are processed reliably:

| Property | Description | Example |
|----------|-------------|---------|
| **Atomicity** | All operations succeed or all fail | Bank transfer: both debit and credit happen or neither |
| **Consistency** | Database moves from valid state to valid state | Balance cannot be negative (if rule exists) |
| **Isolation** | Concurrent transactions don't interfere | Two users booking same seat see consistent state |
| **Durability** | Committed changes persist even after crashes | Transaction committed before power failure is saved |

---

## Introduction To RDBMS

RDBMS (Relational Database Management System) is a database management system based on the relational model, which organizes data into tables with relationships between them.

### Key Concepts

#### Relational Model

Data is organized into relations (tables):

- **Table**: Collection of related data (e.g., Customers, Orders)
- **Row/Tuple**: Single record in a table
- **Column/Attribute**: Field in a table
- **Relationship**: Connection between tables

#### RDBMS Features

1. **Data Stored in Tables**: Organized in rows and columns
2. **Relationships**: Tables connected through keys
3. **SQL Interface**: Standard query language
4. **ACID Compliance**: Transaction guarantees
5. **Constraints**: Rules to maintain data integrity
6. **Indexing**: Fast data retrieval
7. **Views**: Virtual tables based on queries

### Popular RDBMS Systems

| Database | Developer | Characteristics |
|----------|-----------|-----------------|
| **MySQL** | Oracle | Open-source, widely used, fast |
| **PostgreSQL** | PostgreSQL Community | Advanced features, ACID compliant |
| **Oracle Database** | Oracle | Enterprise-grade, feature-rich |
| **SQL Server** | Microsoft | Windows integration, enterprise features |
| **SQLite** | D. Richard Hipp | Embedded, serverless, lightweight |
| **MariaDB** | MariaDB Foundation | MySQL fork, community-driven |

### MySQL Overview

MySQL is one of the most popular open-source RDBMS:

**Advantages:**
- Free and open-source
- Easy to install and use
- Fast and reliable
- Cross-platform support
- Large community support
- Works well with web applications (LAMP/LEMP stack)

**Use Cases:**
- Web applications (WordPress, Drupal)
- E-commerce platforms
- Data warehousing
- Logging applications
- Content management systems

---

## Schema

A database schema is the logical structure that defines how data is organized, including tables, columns, relationships, views, indexes, and other database objects.

### Schema Components

```
Database Schema
├── Tables
│   ├── Columns (fields)
│   ├── Data Types
│   └── Constraints
├── Relationships
│   ├── Primary Keys
│   └── Foreign Keys
├── Indexes
├── Views
├── Stored Procedures
└── Triggers
```

### Creating a Schema (Database)

In MySQL, a schema is equivalent to a database:

```sql
-- Create a new database
CREATE DATABASE company_db;

-- Use the database
USE company_db;

-- View all databases
SHOW DATABASES;

-- Delete a database
DROP DATABASE company_db;
```

### Schema Design Example

```sql
-- Create database
CREATE DATABASE e_commerce;
USE e_commerce;

-- Create tables with relationships
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    total_amount DECIMAL(10, 2) NOT NULL,
    status VARCHAR(20) DEFAULT 'pending',
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

CREATE TABLE products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    product_name VARCHAR(100) NOT NULL,
    description TEXT,
    price DECIMAL(10, 2) NOT NULL,
    stock_quantity INT DEFAULT 0
);

CREATE TABLE order_items (
    order_item_id INT PRIMARY KEY AUTO_INCREMENT,
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (order_id) REFERENCES orders(order_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

---

## Table Structure

A table is the fundamental structure in a relational database, organizing data in rows and columns.

### Table Anatomy

```
Table: employees
┌─────────────┬──────────────┬───────────────┬────────────┬────────┐
│ employee_id │  first_name  │   last_name   │   email    │ salary │
├─────────────┼──────────────┼───────────────┼────────────┼────────┤
│      1      │     John     │      Doe      │ john@...   │ 50000  │ ← Row/Record/Tuple
│      2      │     Jane     │     Smith     │ jane@...   │ 60000  │
│      3      │     Bob      │    Johnson    │ bob@...    │ 55000  │
└─────────────┴──────────────┴───────────────┴────────────┴────────┘
      ↑             ↑               ↑             ↑          ↑
   Column      Column         Column        Column     Column
   /Field      /Field         /Field        /Field     /Field
 /Attribute  /Attribute     /Attribute    /Attribute /Attribute
```

### Table Components

1. **Table Name**: Unique identifier for the table
2. **Columns/Fields**: Define the attributes of data
3. **Rows/Records**: Individual entries in the table
4. **Primary Key**: Uniquely identifies each row
5. **Foreign Key**: References primary key in another table

### Creating Tables

```sql
-- Basic table creation
CREATE TABLE employees (
    employee_id INT,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(100),
    hire_date DATE,
    salary DECIMAL(10, 2)
);

-- View table structure
DESCRIBE employees;
-- or
SHOW COLUMNS FROM employees;

-- View all tables in current database
SHOW TABLES;
```

### Modifying Table Structure

```sql
-- Add a new column
ALTER TABLE employees
ADD COLUMN phone VARCHAR(15);

-- Modify column data type
ALTER TABLE employees
MODIFY COLUMN salary DECIMAL(12, 2);

-- Rename a column
ALTER TABLE employees
RENAME COLUMN phone TO phone_number;

-- Drop a column
ALTER TABLE employees
DROP COLUMN phone_number;

-- Rename table
RENAME TABLE employees TO staff;
-- or
ALTER TABLE staff RENAME TO employees;
```

---

## SQL Data Types

Data types define the kind of data that can be stored in a column. Choosing the correct data type is crucial for data integrity and performance.

### Numeric Data Types

| Data Type | Storage | Range | Use Case |
|-----------|---------|-------|----------|
| **TINYINT** | 1 byte | -128 to 127 (signed)<br>0 to 255 (unsigned) | Small integers, flags |
| **SMALLINT** | 2 bytes | -32,768 to 32,767 | Medium integers |
| **MEDIUMINT** | 3 bytes | -8,388,608 to 8,388,607 | Larger integers |
| **INT** | 4 bytes | -2,147,483,648 to 2,147,483,647 | Standard integers, IDs |
| **BIGINT** | 8 bytes | -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 | Very large numbers |
| **DECIMAL(M,D)** | Varies | Fixed-point decimal | Money, precise calculations |
| **FLOAT** | 4 bytes | Approximate decimal | Scientific calculations |
| **DOUBLE** | 8 bytes | Larger approximate decimal | High-precision science |

```sql
CREATE TABLE products (
    product_id INT,                     -- Standard integer
    stock_quantity SMALLINT,            -- Small number
    price DECIMAL(10, 2),               -- Exact: 10 digits, 2 decimal places
    weight FLOAT,                       -- Approximate decimal
    views BIGINT                        -- Large number
);
```

### String Data Types

| Data Type | Maximum Length | Storage | Use Case |
|-----------|---------------|---------|----------|
| **CHAR(n)** | 255 characters | Fixed n bytes | Fixed-length strings (state codes) |
| **VARCHAR(n)** | 65,535 characters | Variable (n+1 or n+2 bytes) | Variable-length strings (names) |
| **TINYTEXT** | 255 characters | Length + 1 byte | Very short text |
| **TEXT** | 65,535 characters | Length + 2 bytes | Articles, descriptions |
| **MEDIUMTEXT** | 16,777,215 characters | Length + 3 bytes | Large documents |
| **LONGTEXT** | 4,294,967,295 characters | Length + 4 bytes | Very large text |
| **ENUM** | 65,535 values | 1 or 2 bytes | Predefined list (status) |

```sql
CREATE TABLE users (
    username VARCHAR(50),               -- Variable length
    country_code CHAR(2),               -- Fixed: 'US', 'UK', 'IN'
    bio TEXT,                          -- Long description
    status ENUM('active', 'inactive', 'banned')  -- Predefined values
);
```

### Date and Time Data Types

| Data Type | Format | Range | Use Case |
|-----------|--------|-------|----------|
| **DATE** | YYYY-MM-DD | '1000-01-01' to '9999-12-31' | Birth dates, deadlines |
| **TIME** | HH:MM:SS | '-838:59:59' to '838:59:59' | Duration, time of day |
| **DATETIME** | YYYY-MM-DD HH:MM:SS | '1000-01-01 00:00:00' to '9999-12-31 23:59:59' | Event timestamps |
| **TIMESTAMP** | YYYY-MM-DD HH:MM:SS | '1970-01-01 00:00:01' to '2038-01-19 03:14:07' | Auto-updated timestamps |
| **YEAR** | YYYY | 1901 to 2155 | Year only |

```sql
CREATE TABLE events (
    event_id INT,
    event_date DATE,                    -- Just the date
    event_time TIME,                    -- Just the time
    created_at DATETIME,                -- Date and time
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
               ON UPDATE CURRENT_TIMESTAMP  -- Auto-update
);
```

### Binary Data Types

| Data Type | Maximum Size | Use Case |
|-----------|-------------|----------|
| **BINARY(n)** | 255 bytes | Fixed-length binary |
| **VARBINARY(n)** | 65,535 bytes | Variable-length binary |
| **TINYBLOB** | 255 bytes | Small binary objects |
| **BLOB** | 65,535 bytes | Images, files |
| **MEDIUMBLOB** | 16MB | Larger files |
| **LONGBLOB** | 4GB | Very large files |

### JSON Data Type

MySQL supports native JSON data type (MySQL 5.7+):

```sql
CREATE TABLE user_preferences (
    user_id INT,
    preferences JSON
);

-- Insert JSON data
INSERT INTO user_preferences VALUES
(1, '{"theme": "dark", "language": "en", "notifications": true}');

-- Query JSON data
SELECT
    user_id,
    JSON_EXTRACT(preferences, '$.theme') AS theme
FROM user_preferences;
```

### Best Practices for Data Types

1. **Choose the Smallest Type**: Use SMALLINT instead of INT if values are small
2. **Use VARCHAR for Variable Length**: Don't use CHAR(255) for names
3. **DECIMAL for Money**: Never use FLOAT/DOUBLE for currency
4. **Use ENUM for Fixed Lists**: Status fields with few options
5. **DATE vs DATETIME**: Use DATE if you don't need time
6. **Unsigned for Positive**: Use UNSIGNED for IDs and counts
7. **Avoid TEXT for Sorting**: Don't use TEXT/BLOB for frequently sorted columns

```sql
-- Good practices example
CREATE TABLE orders (
    order_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    customer_id INT UNSIGNED NOT NULL,
    order_date DATE NOT NULL,
    order_time TIME,
    total_amount DECIMAL(10, 2) NOT NULL,
    status ENUM('pending', 'processing', 'shipped', 'delivered', 'cancelled'),
    notes TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| **Database** | Organized collection of data with persistent storage, integrity, and security |
| **SQL** | Standardized language for RDBMS: DDL, DML, DQL, DCL, TCL |
| **Consistency** | Data accuracy through constraints, ACID properties, and transaction control |
| **RDBMS** | Relational model with tables, relationships, SQL interface, ACID compliance |
| **Schema** | Logical structure defining tables, columns, relationships, and constraints |
| **Table Structure** | Rows (records) and columns (fields) with primary/foreign keys |
| **Data Types** | Numeric, string, date/time, binary - choose appropriate type for data |

## Next Topic

Continue to [Database Design](./02-database-design.md) to learn about normalization, multiplicity, and data modeling.
