# DCL and TCL

## DCL - Data Control Language

DCL (Data Control Language) consists of SQL commands used to control access to data in the database. DCL commands manage user permissions and privileges.

```
DCL Commands
├── GRANT  - Give privileges to users
└── REVOKE - Remove privileges from users
```

---

## User Management

Before granting or revoking privileges, you need to understand user management in MySQL.

### Creating Users

```sql
-- Create a new user
CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';

-- Create user accessible from any host
CREATE USER 'username'@'%' IDENTIFIED BY 'password';

-- Create user from specific IP
CREATE USER 'username'@'192.168.1.100' IDENTIFIED BY 'password';

-- Create user without password (not recommended)
CREATE USER 'username'@'localhost';

-- View all users
SELECT User, Host FROM mysql.user;
```

### Modifying Users

```sql
-- Change user password
ALTER USER 'username'@'localhost' IDENTIFIED BY 'new_password';

-- Change current user password
ALTER USER USER() IDENTIFIED BY 'new_password';

-- Rename user
RENAME USER 'old_username'@'localhost' TO 'new_username'@'localhost';

-- Delete user
DROP USER 'username'@'localhost';

-- Delete multiple users
DROP USER 'user1'@'localhost', 'user2'@'localhost';
```

---

## GRANT

The GRANT statement gives specific privileges to database users.

### Privilege Types

| Privilege | Description | Scope |
|-----------|-------------|-------|
| **ALL PRIVILEGES** | All available privileges | Global, Database, Table |
| **SELECT** | Read data | Table, Column |
| **INSERT** | Add new data | Table, Column |
| **UPDATE** | Modify existing data | Table, Column |
| **DELETE** | Remove data | Table |
| **CREATE** | Create databases/tables | Global, Database |
| **DROP** | Delete databases/tables | Global, Database |
| **ALTER** | Modify table structure | Database, Table |
| **INDEX** | Create/drop indexes | Database, Table |
| **EXECUTE** | Execute stored procedures | Global, Database |
| **GRANT OPTION** | Grant privileges to others | Global, Database, Table |

### Global Privileges

```sql
-- Grant all privileges on all databases
GRANT ALL PRIVILEGES ON *.* TO 'admin_user'@'localhost';

-- Grant specific privilege on all databases
GRANT CREATE ON *.* TO 'developer'@'localhost';

-- Grant multiple privileges
GRANT SELECT, INSERT, UPDATE ON *.* TO 'app_user'@'localhost';
```

### Database-Level Privileges

```sql
-- Grant all privileges on specific database
GRANT ALL PRIVILEGES ON company_db.* TO 'db_admin'@'localhost';

-- Grant specific privileges on database
GRANT SELECT, INSERT, UPDATE, DELETE ON company_db.* TO 'app_user'@'localhost';

-- Grant read-only access to database
GRANT SELECT ON company_db.* TO 'readonly_user'@'localhost';

-- Grant create and drop privileges
GRANT CREATE, DROP, ALTER ON company_db.* TO 'developer'@'localhost';
```

### Table-Level Privileges

```sql
-- Grant privileges on specific table
GRANT SELECT, INSERT ON company_db.employees TO 'hr_user'@'localhost';

-- Grant all privileges on specific table
GRANT ALL PRIVILEGES ON company_db.salaries TO 'payroll_user'@'localhost';

-- Grant UPDATE on specific columns
GRANT UPDATE (email, phone) ON company_db.employees TO 'support_user'@'localhost';
```

### Column-Level Privileges

```sql
-- Grant SELECT on specific columns
GRANT SELECT (employee_id, first_name, last_name, email)
ON company_db.employees
TO 'public_user'@'localhost';

-- Grant UPDATE on specific columns
GRANT UPDATE (salary, bonus)
ON company_db.employees
TO 'hr_manager'@'localhost';
```

### GRANT with WITH Clause

```sql
-- Grant with ability to grant to others
GRANT SELECT, INSERT ON company_db.* TO 'power_user'@'localhost'
WITH GRANT OPTION;

-- Grant with resource limits
GRANT ALL PRIVILEGES ON company_db.* TO 'limited_user'@'localhost'
WITH MAX_QUERIES_PER_HOUR 1000
     MAX_UPDATES_PER_HOUR 100
     MAX_CONNECTIONS_PER_HOUR 50;
```

### Apply Privileges

```sql
-- Reload privilege tables (apply changes immediately)
FLUSH PRIVILEGES;
```

### View Privileges

```sql
-- Show current user privileges
SHOW GRANTS;

-- Show specific user privileges
SHOW GRANTS FOR 'username'@'localhost';

-- Show privileges in detail
SELECT * FROM mysql.user WHERE User = 'username';
SELECT * FROM mysql.db WHERE User = 'username';
```

---

## REVOKE

The REVOKE statement removes privileges from database users.

### Basic REVOKE

```sql
-- Revoke specific privilege
REVOKE SELECT ON company_db.* FROM 'username'@'localhost';

-- Revoke multiple privileges
REVOKE SELECT, INSERT, UPDATE ON company_db.* FROM 'username'@'localhost';

-- Revoke all privileges on database
REVOKE ALL PRIVILEGES ON company_db.* FROM 'username'@'localhost';

-- Revoke all privileges on all databases
REVOKE ALL PRIVILEGES ON *.* FROM 'username'@'localhost';
```

### REVOKE from Table and Column

```sql
-- Revoke privileges on specific table
REVOKE DELETE ON company_db.employees FROM 'hr_user'@'localhost';

-- Revoke column-level privileges
REVOKE UPDATE (salary) ON company_db.employees FROM 'support_user'@'localhost';
```

### REVOKE GRANT OPTION

```sql
-- Revoke ability to grant privileges to others
REVOKE GRANT OPTION ON company_db.* FROM 'power_user'@'localhost';

-- Revoke all privileges and grant option
REVOKE ALL PRIVILEGES, GRANT OPTION ON *.* FROM 'username'@'localhost';
```

---

## Common DCL Scenarios

### Scenario 1: Create Application User

```sql
-- Create user for web application
CREATE USER 'webapp'@'192.168.1.%' IDENTIFIED BY 'secure_password';

-- Grant necessary privileges
GRANT SELECT, INSERT, UPDATE, DELETE ON ecommerce_db.* TO 'webapp'@'192.168.1.%';

-- Apply changes
FLUSH PRIVILEGES;

-- Verify
SHOW GRANTS FOR 'webapp'@'192.168.1.%';
```

### Scenario 2: Create Read-Only User

```sql
-- Create read-only user for reporting
CREATE USER 'reporter'@'localhost' IDENTIFIED BY 'report_password';

-- Grant only SELECT privilege
GRANT SELECT ON company_db.* TO 'reporter'@'localhost';

FLUSH PRIVILEGES;
```

### Scenario 3: Create Database Administrator

```sql
-- Create DBA user
CREATE USER 'dba'@'localhost' IDENTIFIED BY 'strong_password';

-- Grant all privileges with grant option
GRANT ALL PRIVILEGES ON *.* TO 'dba'@'localhost' WITH GRANT OPTION;

-- Grant system privileges
GRANT SUPER, RELOAD, PROCESS, REPLICATION CLIENT ON *.* TO 'dba'@'localhost';

FLUSH PRIVILEGES;
```

### Scenario 4: Limited Access User

```sql
-- Create user with specific table access
CREATE USER 'analyst'@'localhost' IDENTIFIED BY 'analyst_password';

-- Grant access to specific tables only
GRANT SELECT ON sales_db.orders TO 'analyst'@'localhost';
GRANT SELECT ON sales_db.customers TO 'analyst'@'localhost';
GRANT SELECT ON sales_db.products TO 'analyst'@'localhost';

-- Grant specific columns on sensitive table
GRANT SELECT (customer_id, order_date, total_amount)
ON sales_db.invoices TO 'analyst'@'localhost';

FLUSH PRIVILEGES;
```

### Scenario 5: Revoke and Cleanup

```sql
-- Revoke all privileges from user
REVOKE ALL PRIVILEGES, GRANT OPTION FROM 'old_user'@'localhost';

-- Drop user
DROP USER 'old_user'@'localhost';

-- Verify user is gone
SELECT User, Host FROM mysql.user WHERE User = 'old_user';
```

---

## TCL - Transaction Control Language

TCL (Transaction Control Language) consists of SQL commands used to manage transactions in the database. Transactions ensure data integrity and consistency.

```
TCL Commands
├── START TRANSACTION / BEGIN - Start a transaction
├── COMMIT                    - Save transaction changes
├── ROLLBACK                  - Undo transaction changes
└── SAVEPOINT                 - Set a point within transaction
```

---

## Transactions

A transaction is a sequence of SQL operations treated as a single unit of work. All operations in a transaction either succeed together or fail together.

### ACID Properties Recap

| Property | Description |
|----------|-------------|
| **Atomicity** | All operations succeed or all fail (all-or-nothing) |
| **Consistency** | Database moves from one valid state to another |
| **Isolation** | Concurrent transactions don't interfere with each other |
| **Durability** | Committed changes persist even after system failure |

---

## START TRANSACTION / BEGIN

Starts a new transaction. All subsequent SQL statements are part of the transaction until COMMIT or ROLLBACK.

```sql
-- Method 1: START TRANSACTION
START TRANSACTION;

-- Method 2: BEGIN
BEGIN;

-- MySQL default: Autocommit is ON (each statement is auto-committed)
-- Check autocommit status
SELECT @@autocommit;

-- Disable autocommit (start manual transaction mode)
SET autocommit = 0;

-- Enable autocommit
SET autocommit = 1;
```

---

## COMMIT

COMMIT saves all changes made in the current transaction permanently to the database.

```sql
-- Basic transaction with commit
START TRANSACTION;

INSERT INTO accounts (account_id, balance) VALUES (1, 1000);
INSERT INTO accounts (account_id, balance) VALUES (2, 2000);

COMMIT;  -- Changes are now permanent
```

### Complete Transaction Example

```sql
-- Bank transfer: Move $500 from account 1 to account 2
START TRANSACTION;

-- Deduct from sender
UPDATE accounts SET balance = balance - 500 WHERE account_id = 1;

-- Add to receiver
UPDATE accounts SET balance = balance + 500 WHERE account_id = 2;

-- Verify balances
SELECT * FROM accounts WHERE account_id IN (1, 2);

-- If everything looks good, commit
COMMIT;
```

---

## ROLLBACK

ROLLBACK undoes all changes made in the current transaction and ends the transaction.

```sql
-- Basic transaction with rollback
START TRANSACTION;

DELETE FROM employees WHERE department_id = 5;

-- Oops, wrong department! Undo the delete
ROLLBACK;  -- DELETE is undone

-- Verify data is back
SELECT * FROM employees WHERE department_id = 5;
```

### Conditional ROLLBACK

```sql
START TRANSACTION;

UPDATE accounts SET balance = balance - 1000 WHERE account_id = 1;

-- Check if balance is negative
SELECT balance FROM accounts WHERE account_id = 1;

-- If negative, rollback; otherwise, commit
-- (In application code, you would check the value)
ROLLBACK;  -- or COMMIT;
```

### Error Handling with ROLLBACK

```sql
-- Simulated error handling (would be in application code)
START TRANSACTION;

UPDATE inventory SET quantity = quantity - 10 WHERE product_id = 101;

-- If quantity becomes negative, rollback
-- SELECT quantity FROM inventory WHERE product_id = 101;
-- IF quantity < 0 THEN
ROLLBACK;
-- ELSE
-- COMMIT;
-- END IF;
```

---

## SAVEPOINT

SAVEPOINT creates a point within a transaction that you can roll back to without rolling back the entire transaction.

### Creating and Using SAVEPOINT

```sql
-- Transaction with multiple savepoints
START TRANSACTION;

-- First operation
INSERT INTO orders (order_id, customer_id, total) VALUES (1, 100, 500);
SAVEPOINT sp1;

-- Second operation
INSERT INTO order_items (order_id, product_id, quantity) VALUES (1, 201, 2);
SAVEPOINT sp2;

-- Third operation
UPDATE inventory SET quantity = quantity - 2 WHERE product_id = 201;
SAVEPOINT sp3;

-- Fourth operation
INSERT INTO order_items (order_id, product_id, quantity) VALUES (1, 202, 1);

-- Oops, product 202 doesn't exist. Rollback to before last insert
ROLLBACK TO SAVEPOINT sp3;

-- Continue with valid operations
INSERT INTO order_items (order_id, product_id, quantity) VALUES (1, 203, 1);

-- Commit entire transaction
COMMIT;
```

### SAVEPOINT Example: Nested Operations

```sql
START TRANSACTION;

-- Create order
INSERT INTO orders (customer_id, order_date) VALUES (1, CURDATE());
SET @order_id = LAST_INSERT_ID();
SAVEPOINT order_created;

-- Add first item
INSERT INTO order_items (order_id, product_id, quantity, price)
VALUES (@order_id, 101, 2, 29.99);
SAVEPOINT item1_added;

-- Add second item
INSERT INTO order_items (order_id, product_id, quantity, price)
VALUES (@order_id, 102, 1, 49.99);
SAVEPOINT item2_added;

-- Add third item - error occurs
-- INSERT INTO order_items (order_id, product_id, quantity, price)
-- VALUES (@order_id, 999, 1, 99.99);  -- Product doesn't exist

-- Rollback just the failed item
ROLLBACK TO SAVEPOINT item2_added;

-- Continue and commit
COMMIT;
```

### Release SAVEPOINT

```sql
START TRANSACTION;

INSERT INTO logs (message) VALUES ('Operation 1');
SAVEPOINT sp1;

INSERT INTO logs (message) VALUES ('Operation 2');
SAVEPOINT sp2;

-- Release savepoint (can't rollback to sp1 anymore)
RELEASE SAVEPOINT sp1;

-- Can still rollback to sp2
ROLLBACK TO SAVEPOINT sp2;

COMMIT;
```

---

## Transaction Examples

### Example 1: Bank Transfer

```sql
-- Transfer $500 from Alice to Bob
START TRANSACTION;

-- Check Alice's balance
SELECT balance FROM accounts WHERE account_id = 1;  -- Assume balance = 1000

-- Deduct from Alice
UPDATE accounts SET balance = balance - 500 WHERE account_id = 1;

-- Check for negative balance
SELECT balance FROM accounts WHERE account_id = 1;

-- If balance >= 0, continue; otherwise rollback
-- Assuming balance is OK:

-- Add to Bob
UPDATE accounts SET balance = balance + 500 WHERE account_id = 2;

-- Log the transaction
INSERT INTO transaction_log (from_account, to_account, amount, trans_date)
VALUES (1, 2, 500, NOW());

-- Verify totals
SELECT
    (SELECT SUM(balance) FROM accounts WHERE account_id IN (1, 2)) AS total_balance;

-- If all looks good, commit
COMMIT;
```

### Example 2: Order Processing

```sql
START TRANSACTION;

-- Create order
INSERT INTO orders (customer_id, order_date, status)
VALUES (101, CURDATE(), 'pending');

SET @order_id = LAST_INSERT_ID();
SAVEPOINT order_created;

-- Add order items and update inventory
INSERT INTO order_items (order_id, product_id, quantity, unit_price)
VALUES (@order_id, 1, 2, 29.99);

UPDATE products SET stock_quantity = stock_quantity - 2 WHERE product_id = 1;
SAVEPOINT product1_processed;

INSERT INTO order_items (order_id, product_id, quantity, unit_price)
VALUES (@order_id, 2, 1, 49.99);

UPDATE products SET stock_quantity = stock_quantity - 1 WHERE product_id = 2;
SAVEPOINT product2_processed;

-- Check if any product is out of stock
SELECT product_id, stock_quantity
FROM products
WHERE product_id IN (1, 2) AND stock_quantity < 0;

-- If any negative stock, rollback to before that product
-- Otherwise, calculate total and commit

-- Calculate order total
UPDATE orders
SET total_amount = (
    SELECT SUM(quantity * unit_price)
    FROM order_items
    WHERE order_id = @order_id
)
WHERE order_id = @order_id;

-- Update status
UPDATE orders SET status = 'confirmed' WHERE order_id = @order_id;

COMMIT;
```

### Example 3: Bulk Update with Verification

```sql
START TRANSACTION;

-- Give 10% raise to all employees in department 5
UPDATE employees
SET salary = salary * 1.10
WHERE department_id = 5;

SAVEPOINT salary_updated;

-- Verify: Check average salary didn't exceed budget
SELECT
    AVG(salary) AS avg_salary,
    SUM(salary) AS total_salary
FROM employees
WHERE department_id = 5;

-- If total_salary > budget, rollback; otherwise commit
-- Assuming it's within budget:

-- Log the salary update
INSERT INTO salary_changes (department_id, change_date, change_type, change_pct)
VALUES (5, CURDATE(), 'raise', 10);

COMMIT;
```

### Example 4: Error Recovery

```sql
-- Demonstrating error handling
START TRANSACTION;

-- Step 1: Update user status
UPDATE users SET status = 'inactive' WHERE user_id = 100;
SAVEPOINT user_updated;

-- Step 2: Delete user sessions
DELETE FROM user_sessions WHERE user_id = 100;
SAVEPOINT sessions_deleted;

-- Step 3: Archive user data
INSERT INTO archived_users SELECT * FROM users WHERE user_id = 100;
-- If this fails (e.g., duplicate key), rollback to before archiving
-- ROLLBACK TO SAVEPOINT sessions_deleted;

-- Step 4: Delete user
DELETE FROM users WHERE user_id = 100;

-- If foreign key constraint error, rollback entire transaction
-- ROLLBACK;

-- If all succeeded, commit
COMMIT;
```

---

## Isolation Levels

Transaction isolation levels control how concurrent transactions interact with each other.

### Isolation Levels

| Level | Dirty Read | Non-Repeatable Read | Phantom Read |
|-------|------------|---------------------|--------------|
| **READ UNCOMMITTED** | Possible | Possible | Possible |
| **READ COMMITTED** | Not possible | Possible | Possible |
| **REPEATABLE READ** (MySQL default) | Not possible | Not possible | Possible |
| **SERIALIZABLE** | Not possible | Not possible | Not possible |

### Setting Isolation Level

```sql
-- Set global isolation level
SET GLOBAL TRANSACTION ISOLATION LEVEL READ COMMITTED;

-- Set session isolation level
SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ;

-- Set for next transaction only
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;

-- View current isolation level
SELECT @@transaction_isolation;
```

### Isolation Level Examples

```sql
-- READ UNCOMMITTED (see uncommitted changes from other transactions)
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
START TRANSACTION;
SELECT * FROM accounts;  -- May see uncommitted changes
COMMIT;

-- READ COMMITTED (see only committed changes)
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
START TRANSACTION;
SELECT * FROM accounts;  -- Sees only committed data
COMMIT;

-- REPEATABLE READ (same query returns same results within transaction)
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
START TRANSACTION;
SELECT * FROM accounts WHERE account_id = 1;  -- balance = 1000
-- Another transaction updates and commits balance to 1500
SELECT * FROM accounts WHERE account_id = 1;  -- Still sees balance = 1000
COMMIT;

-- SERIALIZABLE (transactions run as if serial)
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
START TRANSACTION;
SELECT * FROM accounts;
-- Other transactions must wait
COMMIT;
```

---

## Transaction Best Practices

### 1. Keep Transactions Short

```sql
-- ✓ Good: Short transaction
START TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;
COMMIT;

-- ✗ Bad: Long transaction with user input
-- START TRANSACTION;
-- UPDATE inventory SET quantity = quantity - 1 WHERE product_id = 1;
-- -- Wait for user confirmation (locks held for too long)
-- COMMIT;
```

### 2. Handle Errors Properly

```sql
-- In application code, use try-catch with rollback
-- Pseudocode:
-- TRY
START TRANSACTION;
-- ... operations ...
COMMIT;
-- CATCH
-- ROLLBACK;
-- END TRY
```

### 3. Use Appropriate Isolation Level

```sql
-- For most applications, READ COMMITTED or REPEATABLE READ is sufficient
SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

### 4. Avoid Nested Transactions

```sql
-- MySQL doesn't support true nested transactions
-- Use SAVEPOINTs instead
START TRANSACTION;
-- ... operations ...
SAVEPOINT sp1;
-- ... more operations ...
ROLLBACK TO SAVEPOINT sp1;  -- Not ROLLBACK in nested block
COMMIT;
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| **DCL** | GRANT and REVOKE control user access to database resources |
| **GRANT** | Give privileges: SELECT, INSERT, UPDATE, DELETE, ALL, etc. |
| **REVOKE** | Remove privileges from users |
| **User Management** | CREATE USER, ALTER USER, DROP USER, FLUSH PRIVILEGES |
| **TCL** | Manage transactions: START TRANSACTION, COMMIT, ROLLBACK |
| **Transactions** | Ensure ACID properties for data integrity |
| **COMMIT** | Save transaction changes permanently |
| **ROLLBACK** | Undo transaction changes |
| **SAVEPOINT** | Create checkpoint within transaction for partial rollback |
| **Isolation Levels** | READ UNCOMMITTED, READ COMMITTED, REPEATABLE READ, SERIALIZABLE |

## Next Topic

Continue to [Joins and Subqueries](./06-joins-and-subqueries.md) to learn about combining data from multiple tables and complex SQL queries.
