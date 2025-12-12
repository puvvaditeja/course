# DDL and Constraints

## DDL - Data Definition Language

DDL (Data Definition Language) consists of SQL commands used to define, modify, and delete database structures such as databases, tables, indexes, and views.

### DDL Commands Overview

```
DDL Commands
├── CREATE   - Create new database objects
├── ALTER    - Modify existing database objects
├── DROP     - Delete database objects
├── TRUNCATE - Remove all data from table (structure remains)
└── RENAME   - Rename database objects
```

---

## CREATE

The CREATE statement is used to create databases, tables, views, indexes, and other database objects.

### CREATE DATABASE

```sql
-- Create a new database
CREATE DATABASE company_db;

-- Create database only if it doesn't exist
CREATE DATABASE IF NOT EXISTS company_db;

-- Create with character set and collation
CREATE DATABASE company_db
    CHARACTER SET utf8mb4
    COLLATE utf8mb4_unicode_ci;

-- View all databases
SHOW DATABASES;

-- Select/use a database
USE company_db;

-- See currently selected database
SELECT DATABASE();
```

### CREATE TABLE

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

-- Create table only if it doesn't exist
CREATE TABLE IF NOT EXISTS employees (
    employee_id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    hire_date DATE DEFAULT (CURRENT_DATE),
    salary DECIMAL(10, 2) CHECK (salary > 0)
);

-- Create table from another table (copy structure and data)
CREATE TABLE employees_backup
AS SELECT * FROM employees;

-- Create table from another table (copy structure only)
CREATE TABLE employees_template
AS SELECT * FROM employees WHERE 1=0;

-- View table structure
DESCRIBE employees;
-- or
SHOW COLUMNS FROM employees;

-- View CREATE statement of existing table
SHOW CREATE TABLE employees;
```

### CREATE TABLE with Constraints

```sql
CREATE TABLE departments (
    department_id INT PRIMARY KEY AUTO_INCREMENT,
    department_name VARCHAR(100) NOT NULL UNIQUE,
    manager_id INT,
    location VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE employees (
    employee_id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    phone VARCHAR(15),
    hire_date DATE NOT NULL DEFAULT (CURRENT_DATE),
    job_title VARCHAR(100),
    salary DECIMAL(10, 2) CHECK (salary >= 0),
    department_id INT,
    manager_id INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,

    -- Foreign key constraints
    CONSTRAINT fk_department
        FOREIGN KEY (department_id) REFERENCES departments(department_id)
        ON DELETE SET NULL
        ON UPDATE CASCADE,

    CONSTRAINT fk_manager
        FOREIGN KEY (manager_id) REFERENCES employees(employee_id)
        ON DELETE SET NULL
        ON UPDATE CASCADE
);
```

### CREATE INDEX

```sql
-- Create index on single column
CREATE INDEX idx_email ON employees(email);

-- Create index on multiple columns
CREATE INDEX idx_name ON employees(first_name, last_name);

-- Create unique index
CREATE UNIQUE INDEX idx_email_unique ON employees(email);

-- View indexes on table
SHOW INDEX FROM employees;

-- Drop index
DROP INDEX idx_email ON employees;
```

### CREATE VIEW

```sql
-- Create a view
CREATE VIEW employee_details AS
SELECT
    e.employee_id,
    e.first_name,
    e.last_name,
    e.email,
    e.salary,
    d.department_name
FROM employees e
LEFT JOIN departments d ON e.department_id = d.department_id;

-- Query the view
SELECT * FROM employee_details WHERE department_name = 'Engineering';

-- View all views
SHOW FULL TABLES WHERE Table_type = 'VIEW';

-- Drop view
DROP VIEW employee_details;
```

---

## ALTER

The ALTER statement modifies the structure of existing database objects.

### ALTER TABLE - Add Column

```sql
-- Add single column
ALTER TABLE employees
ADD COLUMN middle_name VARCHAR(50);

-- Add column with position
ALTER TABLE employees
ADD COLUMN ssn VARCHAR(11) AFTER last_name;

-- Add column at the beginning
ALTER TABLE employees
ADD COLUMN employee_code VARCHAR(20) FIRST;

-- Add multiple columns
ALTER TABLE employees
ADD COLUMN date_of_birth DATE,
ADD COLUMN address VARCHAR(255);
```

### ALTER TABLE - Modify Column

```sql
-- Change data type
ALTER TABLE employees
MODIFY COLUMN salary DECIMAL(12, 2);

-- Change column with constraints
ALTER TABLE employees
MODIFY COLUMN email VARCHAR(150) NOT NULL UNIQUE;

-- Rename column
ALTER TABLE employees
RENAME COLUMN phone TO phone_number;

-- Change column definition and name
ALTER TABLE employees
CHANGE COLUMN middle_name middle_initial CHAR(1);
```

### ALTER TABLE - Drop Column

```sql
-- Drop single column
ALTER TABLE employees
DROP COLUMN middle_initial;

-- Drop multiple columns
ALTER TABLE employees
DROP COLUMN ssn,
DROP COLUMN employee_code;
```

### ALTER TABLE - Constraints

```sql
-- Add primary key
ALTER TABLE employees
ADD PRIMARY KEY (employee_id);

-- Drop primary key
ALTER TABLE employees
DROP PRIMARY KEY;

-- Add foreign key
ALTER TABLE employees
ADD CONSTRAINT fk_department
    FOREIGN KEY (department_id) REFERENCES departments(department_id);

-- Drop foreign key
ALTER TABLE employees
DROP FOREIGN KEY fk_department;

-- Add unique constraint
ALTER TABLE employees
ADD CONSTRAINT uk_email UNIQUE (email);

-- Drop unique constraint
ALTER TABLE employees
DROP INDEX uk_email;

-- Add check constraint
ALTER TABLE employees
ADD CONSTRAINT chk_salary CHECK (salary > 0);

-- Drop check constraint
ALTER TABLE employees
DROP CHECK chk_salary;

-- Set default value
ALTER TABLE employees
ALTER COLUMN hire_date SET DEFAULT (CURRENT_DATE);

-- Remove default value
ALTER TABLE employees
ALTER COLUMN hire_date DROP DEFAULT;
```

### ALTER TABLE - Rename

```sql
-- Rename table
ALTER TABLE employees RENAME TO staff;

-- Or using RENAME statement
RENAME TABLE staff TO employees;

-- Rename multiple tables
RENAME TABLE
    employees TO staff,
    departments TO divisions;
```

---

## DROP

The DROP statement permanently deletes database objects.

### DROP DATABASE

```sql
-- Drop database (permanent deletion)
DROP DATABASE company_db;

-- Drop only if exists (no error if doesn't exist)
DROP DATABASE IF EXISTS company_db;

-- ⚠️ WARNING: This deletes all data and structure permanently!
```

### DROP TABLE

```sql
-- Drop table
DROP TABLE employees;

-- Drop only if exists
DROP TABLE IF EXISTS employees;

-- Drop multiple tables
DROP TABLE IF EXISTS employees, departments, projects;

-- Drop table with foreign key dependencies
-- Must drop child tables first or disable foreign key checks
SET FOREIGN_KEY_CHECKS = 0;
DROP TABLE employees;
SET FOREIGN_KEY_CHECKS = 1;
```

### DROP vs TRUNCATE vs DELETE

```sql
-- DELETE: Removes rows, can use WHERE, can rollback, slower
DELETE FROM employees WHERE department_id = 5;

-- TRUNCATE: Removes all rows, faster, resets auto-increment, can't rollback
TRUNCATE TABLE employees;

-- DROP: Removes table structure and data, can't rollback
DROP TABLE employees;
```

| Command | Removes | Structure | Auto-increment | Rollback | Where Clause | Speed |
|---------|---------|-----------|----------------|----------|--------------|-------|
| DELETE | Rows | Kept | Preserved | Yes | Yes | Slow |
| TRUNCATE | All rows | Kept | Reset | No | No | Fast |
| DROP | Table | Removed | N/A | No | No | Fastest |

---

## TRUNCATE

TRUNCATE removes all rows from a table quickly but keeps the table structure.

```sql
-- Remove all data from table
TRUNCATE TABLE employees;

-- Equivalent to (but faster than)
DELETE FROM employees;

-- Key differences:
-- 1. TRUNCATE is faster (doesn't log individual row deletions)
-- 2. TRUNCATE resets AUTO_INCREMENT counter
-- 3. TRUNCATE cannot be rolled back
-- 4. TRUNCATE cannot use WHERE clause
-- 5. TRUNCATE doesn't activate DELETE triggers
```

### TRUNCATE Example

```sql
-- Create and populate table
CREATE TABLE test_data (
    id INT PRIMARY KEY AUTO_INCREMENT,
    value VARCHAR(50)
);

INSERT INTO test_data (value) VALUES ('A'), ('B'), ('C');
SELECT * FROM test_data;  -- id: 1, 2, 3

-- Delete and insert
DELETE FROM test_data;
INSERT INTO test_data (value) VALUES ('D');
SELECT * FROM test_data;  -- id: 4 (counter continues)

-- Truncate and insert
TRUNCATE TABLE test_data;
INSERT INTO test_data (value) VALUES ('E');
SELECT * FROM test_data;  -- id: 1 (counter reset)
```

---

## Constraints

Constraints are rules enforced on data columns to maintain data integrity and accuracy.

### Types of Constraints

```
Constraints
├── PRIMARY KEY  - Unique identifier for each row
├── FOREIGN KEY  - Links to primary key in another table
├── UNIQUE       - Ensures all values are unique
├── NOT NULL     - Prevents NULL values
├── CHECK        - Validates data against condition
└── DEFAULT      - Provides default value if none specified
```

---

## PRIMARY KEY

A PRIMARY KEY uniquely identifies each record in a table. It must contain unique values and cannot be NULL.

### Characteristics

- **Unique**: No duplicate values allowed
- **Not NULL**: Cannot contain NULL values
- **One per table**: Only one primary key per table
- **Indexed**: Automatically creates an index

### Syntax

```sql
-- Method 1: Column-level constraint
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    name VARCHAR(100)
);

-- Method 2: Table-level constraint
CREATE TABLE employees (
    employee_id INT,
    name VARCHAR(100),
    PRIMARY KEY (employee_id)
);

-- Method 3: Named constraint
CREATE TABLE employees (
    employee_id INT,
    name VARCHAR(100),
    CONSTRAINT pk_employee PRIMARY KEY (employee_id)
);

-- Composite primary key (multiple columns)
CREATE TABLE enrollments (
    student_id INT,
    course_id INT,
    enrollment_date DATE,
    PRIMARY KEY (student_id, course_id)
);
```

### Adding/Removing Primary Key

```sql
-- Add primary key to existing table
ALTER TABLE employees
ADD PRIMARY KEY (employee_id);

-- Add named primary key
ALTER TABLE employees
ADD CONSTRAINT pk_employee PRIMARY KEY (employee_id);

-- Drop primary key
ALTER TABLE employees
DROP PRIMARY KEY;
```

---

## FOREIGN KEY

A FOREIGN KEY links two tables together by referencing the PRIMARY KEY of another table, ensuring referential integrity.

### Characteristics

- **Referential Integrity**: Ensures relationships between tables are valid
- **References Primary Key**: Must reference a primary key or unique key
- **Can be NULL**: Unless NOT NULL is specified
- **Multiple Allowed**: Table can have multiple foreign keys

### Syntax

```sql
-- Method 1: Column-level constraint
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

-- Method 2: Named constraint
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    CONSTRAINT fk_customer
        FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

-- Method 3: With ON DELETE and ON UPDATE actions
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    CONSTRAINT fk_customer
        FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
        ON DELETE CASCADE
        ON UPDATE CASCADE
);
```

### Referential Actions

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
        ON DELETE CASCADE      -- Delete orders when customer is deleted
        ON UPDATE CASCADE      -- Update orders when customer_id changes
);
```

---

## CASCADE

CASCADE defines what happens to child records when parent record is updated or deleted.

### Referential Actions

| Action | ON DELETE | ON UPDATE |
|--------|-----------|-----------|
| **CASCADE** | Delete child rows | Update child rows |
| **SET NULL** | Set foreign key to NULL | Set foreign key to NULL |
| **SET DEFAULT** | Set foreign key to default value | Set foreign key to default value |
| **RESTRICT** | Prevent deletion/update if children exist | Prevent deletion/update if children exist |
| **NO ACTION** | Same as RESTRICT | Same as RESTRICT |

### CASCADE Examples

```sql
-- Create parent table
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE
);

-- Example 1: CASCADE - Delete/update children automatically
CREATE TABLE orders_cascade (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT,
    order_date DATE,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
        ON DELETE CASCADE
        ON UPDATE CASCADE
);

-- Example 2: SET NULL - Set to NULL when parent deleted/updated
CREATE TABLE orders_null (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT,
    order_date DATE,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
        ON DELETE SET NULL
        ON UPDATE SET NULL
);

-- Example 3: RESTRICT - Prevent deletion if children exist
CREATE TABLE orders_restrict (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    order_date DATE,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
        ON DELETE RESTRICT
        ON UPDATE RESTRICT
);
```

### CASCADE in Action

```sql
-- Setup
INSERT INTO customers (name, email) VALUES ('John Doe', 'john@example.com');
INSERT INTO orders_cascade (customer_id, order_date) VALUES (1, '2024-01-01');
INSERT INTO orders_null (customer_id, order_date) VALUES (1, '2024-01-01');
INSERT INTO orders_restrict (customer_id, order_date) VALUES (1, '2024-01-01');

-- Delete customer
DELETE FROM customers WHERE customer_id = 1;

-- Results:
-- orders_cascade: Order is deleted (CASCADE)
-- orders_null: customer_id set to NULL (SET NULL)
-- orders_restrict: Error - cannot delete (RESTRICT)
```

---

## UNIQUE

UNIQUE constraint ensures all values in a column or group of columns are distinct.

### Characteristics

- **Unique Values**: No duplicates allowed
- **NULL Allowed**: Can contain multiple NULL values (NULL ≠ NULL)
- **Multiple Allowed**: Table can have multiple unique constraints
- **Indexed**: Automatically creates an index

### Syntax

```sql
-- Method 1: Column-level
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    email VARCHAR(100) UNIQUE,
    username VARCHAR(50) UNIQUE
);

-- Method 2: Table-level
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    email VARCHAR(100),
    username VARCHAR(50),
    UNIQUE (email),
    UNIQUE (username)
);

-- Method 3: Named constraint
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    email VARCHAR(100),
    username VARCHAR(50),
    CONSTRAINT uk_email UNIQUE (email),
    CONSTRAINT uk_username UNIQUE (username)
);

-- Composite unique (combination must be unique)
CREATE TABLE enrollments (
    student_id INT,
    course_id INT,
    semester VARCHAR(20),
    UNIQUE (student_id, course_id, semester)
);
```

### Adding/Removing Unique Constraint

```sql
-- Add unique constraint
ALTER TABLE users
ADD UNIQUE (email);

-- Add named unique constraint
ALTER TABLE users
ADD CONSTRAINT uk_email UNIQUE (email);

-- Drop unique constraint
ALTER TABLE users
DROP INDEX uk_email;
```

---

## NOT NULL

NOT NULL constraint ensures a column cannot have NULL values.

### Syntax

```sql
-- Method 1: During table creation
CREATE TABLE employees (
    employee_id INT NOT NULL,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL,
    phone VARCHAR(15)  -- NULL allowed
);

-- Method 2: Add to existing column
ALTER TABLE employees
MODIFY COLUMN phone VARCHAR(15) NOT NULL;

-- Remove NOT NULL constraint
ALTER TABLE employees
MODIFY COLUMN phone VARCHAR(15) NULL;
```

### NOT NULL vs DEFAULT

```sql
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,           -- Must be provided
    status VARCHAR(20) NOT NULL DEFAULT 'active',  -- Will use 'active' if not provided
    bio TEXT                                 -- NULL allowed
);

-- Valid inserts
INSERT INTO users (user_id, username) VALUES (1, 'john');  -- status = 'active' (default)
INSERT INTO users (user_id, username, status) VALUES (2, 'jane', 'inactive');
INSERT INTO users (user_id, username, bio) VALUES (3, 'bob', 'Bio text');

-- Invalid insert
INSERT INTO users (user_id) VALUES (4);  -- Error: username is NOT NULL
```

---

## CHECK

CHECK constraint validates data based on a specified condition.

### Syntax

```sql
-- Method 1: Column-level
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    age INT CHECK (age >= 18),
    salary DECIMAL(10, 2) CHECK (salary > 0)
);

-- Method 2: Table-level
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    age INT,
    salary DECIMAL(10, 2),
    CHECK (age >= 18),
    CHECK (salary > 0)
);

-- Method 3: Named constraint
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    age INT,
    salary DECIMAL(10, 2),
    hire_date DATE,
    CONSTRAINT chk_age CHECK (age >= 18 AND age <= 65),
    CONSTRAINT chk_salary CHECK (salary BETWEEN 30000 AND 500000),
    CONSTRAINT chk_hire_date CHECK (hire_date <= CURDATE())
);

-- Multi-column check
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    order_date DATE,
    ship_date DATE,
    CONSTRAINT chk_dates CHECK (ship_date >= order_date)
);
```

### CHECK Examples

```sql
-- Age validation
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    age INT CHECK (age >= 13 AND age <= 120)
);

-- Email format validation (basic)
CREATE TABLE contacts (
    contact_id INT PRIMARY KEY,
    email VARCHAR(100) CHECK (email LIKE '%@%.%')
);

-- Enum-like validation
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    status VARCHAR(20) CHECK (status IN ('active', 'inactive', 'discontinued'))
);

-- Range validation
CREATE TABLE scores (
    score_id INT PRIMARY KEY,
    percentage DECIMAL(5, 2) CHECK (percentage BETWEEN 0 AND 100)
);

-- Complex validation
CREATE TABLE discounts (
    discount_id INT PRIMARY KEY,
    discount_type VARCHAR(20) CHECK (discount_type IN ('percentage', 'fixed')),
    discount_value DECIMAL(10, 2),
    CHECK (
        (discount_type = 'percentage' AND discount_value BETWEEN 0 AND 100)
        OR
        (discount_type = 'fixed' AND discount_value > 0)
    )
);
```

### Adding/Removing CHECK Constraint

```sql
-- Add check constraint
ALTER TABLE employees
ADD CONSTRAINT chk_salary CHECK (salary > 0);

-- Drop check constraint
ALTER TABLE employees
DROP CHECK chk_salary;
```

---

## DEFAULT

DEFAULT constraint provides a default value for a column when no value is specified.

### Syntax

```sql
-- Basic default values
CREATE TABLE employees (
    employee_id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(50) NOT NULL,
    status VARCHAR(20) DEFAULT 'active',
    is_remote BOOLEAN DEFAULT FALSE,
    department VARCHAR(50) DEFAULT 'Unassigned'
);

-- Default with functions
CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    order_date DATE DEFAULT (CURRENT_DATE),
    order_time TIME DEFAULT (CURRENT_TIME),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

-- Default with expressions (MySQL 8.0+)
CREATE TABLE products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    product_name VARCHAR(100) NOT NULL,
    sku VARCHAR(50) DEFAULT (UUID()),
    created_year INT DEFAULT (YEAR(CURRENT_DATE))
);
```

### Common Default Values

```sql
CREATE TABLE comprehensive_example (
    id INT PRIMARY KEY AUTO_INCREMENT,

    -- String defaults
    status VARCHAR(20) DEFAULT 'pending',
    country VARCHAR(50) DEFAULT 'USA',

    -- Numeric defaults
    quantity INT DEFAULT 0,
    rating DECIMAL(3, 2) DEFAULT 0.00,

    -- Boolean defaults
    is_active BOOLEAN DEFAULT TRUE,
    is_deleted BOOLEAN DEFAULT FALSE,

    -- Date/Time defaults
    created_date DATE DEFAULT (CURRENT_DATE),
    created_time TIME DEFAULT (CURRENT_TIME),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

### Adding/Removing DEFAULT

```sql
-- Add default to existing column
ALTER TABLE employees
ALTER COLUMN status SET DEFAULT 'active';

-- Remove default
ALTER TABLE employees
ALTER COLUMN status DROP DEFAULT;

-- Change default
ALTER TABLE employees
ALTER COLUMN status SET DEFAULT 'pending';
```

---

## Auto Incrementing

AUTO_INCREMENT automatically generates unique sequential numbers for a column, typically used for primary keys.

### Characteristics

- **Automatic**: Generates next value automatically
- **Unique**: Each value is unique
- **Sequential**: Increments by 1 (default)
- **One per table**: Only one AUTO_INCREMENT column per table
- **Must be indexed**: Usually part of primary key

### Syntax

```sql
-- Basic auto increment
CREATE TABLE users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL
);

-- Insert without specifying ID
INSERT INTO users (username, email) VALUES ('john', 'john@example.com');
INSERT INTO users (username, email) VALUES ('jane', 'jane@example.com');
-- user_id will be 1, 2 automatically

-- Insert with explicit ID (not recommended)
INSERT INTO users (user_id, username, email) VALUES (10, 'bob', 'bob@example.com');
INSERT INTO users (username, email) VALUES ('alice', 'alice@example.com');
-- Next ID will be 11
```

### Starting Value and Increment

```sql
-- Set starting value
CREATE TABLE products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    product_name VARCHAR(100)
) AUTO_INCREMENT = 1000;  -- Start from 1000

-- Change auto increment value
ALTER TABLE products AUTO_INCREMENT = 2000;

-- View current auto increment value
SELECT AUTO_INCREMENT
FROM information_schema.TABLES
WHERE TABLE_SCHEMA = 'your_database'
AND TABLE_NAME = 'products';
```

### AUTO_INCREMENT Behavior

```sql
-- Create test table
CREATE TABLE test (
    id INT PRIMARY KEY AUTO_INCREMENT,
    value VARCHAR(50)
);

-- Insert values
INSERT INTO test (value) VALUES ('A');  -- id = 1
INSERT INTO test (value) VALUES ('B');  -- id = 2
INSERT INTO test (value) VALUES ('C');  -- id = 3

-- Delete a row
DELETE FROM test WHERE id = 2;

-- Insert new value
INSERT INTO test (value) VALUES ('D');  -- id = 4 (not 2!)

-- Manual reset (use with caution)
ALTER TABLE test AUTO_INCREMENT = 1;

-- Gaps remain after DELETE, but TRUNCATE resets
TRUNCATE TABLE test;
INSERT INTO test (value) VALUES ('E');  -- id = 1
```

### Best Practices

```sql
-- ✓ DO: Use AUTO_INCREMENT for primary keys
CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    order_date DATE
);

-- ✓ DO: Let database handle ID generation
INSERT INTO orders (customer_id, order_date) VALUES (1, CURDATE());

-- ✗ DON'T: Manually specify AUTO_INCREMENT values
-- INSERT INTO orders (order_id, customer_id) VALUES (100, 1);

-- ✗ DON'T: Use AUTO_INCREMENT for non-primary key
-- CREATE TABLE bad_table (
--     id INT PRIMARY KEY,
--     code INT AUTO_INCREMENT  -- Error: only one AUTO_INCREMENT allowed
-- );

-- ✓ DO: Use BIGINT for tables with many records
CREATE TABLE logs (
    log_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    message TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

## Complete Example: All Constraints Together

```sql
-- Create database
CREATE DATABASE IF NOT EXISTS school_db;
USE school_db;

-- Parent table: departments
CREATE TABLE departments (
    department_id INT PRIMARY KEY AUTO_INCREMENT,
    department_name VARCHAR(100) NOT NULL UNIQUE,
    building VARCHAR(50) DEFAULT 'Main Campus',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Parent table: students
CREATE TABLE students (
    student_id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    date_of_birth DATE NOT NULL,
    enrollment_year INT DEFAULT (YEAR(CURRENT_DATE)),
    gpa DECIMAL(3, 2) CHECK (gpa BETWEEN 0.00 AND 4.00),
    status ENUM('active', 'inactive', 'graduated') DEFAULT 'active',
    department_id INT,

    CONSTRAINT fk_student_dept
        FOREIGN KEY (department_id) REFERENCES departments(department_id)
        ON DELETE SET NULL
        ON UPDATE CASCADE,

    CONSTRAINT chk_birth_date
        CHECK (date_of_birth <= CURDATE()),

    CONSTRAINT chk_email_format
        CHECK (email LIKE '%@%.%')
);

-- Parent table: courses
CREATE TABLE courses (
    course_id INT PRIMARY KEY AUTO_INCREMENT,
    course_code VARCHAR(10) NOT NULL UNIQUE,
    course_name VARCHAR(100) NOT NULL,
    credits INT NOT NULL DEFAULT 3,
    department_id INT,
    max_capacity INT DEFAULT 30,

    CONSTRAINT fk_course_dept
        FOREIGN KEY (department_id) REFERENCES departments(department_id)
        ON DELETE SET NULL
        ON UPDATE CASCADE,

    CONSTRAINT chk_credits
        CHECK (credits BETWEEN 1 AND 6),

    CONSTRAINT chk_capacity
        CHECK (max_capacity BETWEEN 10 AND 100)
);

-- Junction table: enrollments (many-to-many)
CREATE TABLE enrollments (
    enrollment_id INT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    course_id INT NOT NULL,
    semester VARCHAR(20) NOT NULL,
    enrollment_date DATE DEFAULT (CURRENT_DATE),
    grade VARCHAR(2),
    status VARCHAR(20) DEFAULT 'enrolled',

    CONSTRAINT fk_enrollment_student
        FOREIGN KEY (student_id) REFERENCES students(student_id)
        ON DELETE CASCADE
        ON UPDATE CASCADE,

    CONSTRAINT fk_enrollment_course
        FOREIGN KEY (course_id) REFERENCES courses(course_id)
        ON DELETE CASCADE
        ON UPDATE CASCADE,

    CONSTRAINT uk_student_course_semester
        UNIQUE (student_id, course_id, semester),

    CONSTRAINT chk_grade
        CHECK (grade IN ('A', 'A-', 'B+', 'B', 'B-', 'C+', 'C', 'C-', 'D', 'F', 'W', 'I')),

    CONSTRAINT chk_status
        CHECK (status IN ('enrolled', 'completed', 'dropped', 'withdrawn'))
);
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| **CREATE** | Create databases, tables, indexes, views |
| **ALTER** | Modify table structure: add/modify/drop columns and constraints |
| **DROP** | Permanently delete database objects |
| **TRUNCATE** | Remove all rows, keep structure, reset AUTO_INCREMENT |
| **PRIMARY KEY** | Unique identifier, NOT NULL, one per table, indexed |
| **FOREIGN KEY** | Links tables, enforces referential integrity |
| **CASCADE** | Auto delete/update child records: CASCADE, SET NULL, RESTRICT |
| **UNIQUE** | Ensures distinct values, allows NULL, multiple per table |
| **NOT NULL** | Prevents NULL values |
| **CHECK** | Validates data against conditions |
| **DEFAULT** | Provides default value if none specified |
| **AUTO_INCREMENT** | Generates sequential unique numbers |

## Next Topic

Continue to [DML and DQL](./04-dml-and-dql.md) to learn about manipulating and querying data.
