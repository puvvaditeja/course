# DML and DQL

## DML - Data Manipulation Language

DML (Data Manipulation Language) consists of SQL commands used to manipulate data within database tables. The three main DML commands are INSERT, UPDATE, and DELETE.

```
DML Commands
├── INSERT - Add new records to table
├── UPDATE - Modify existing records
└── DELETE - Remove records from table
```

---

## INSERT

The INSERT statement adds new rows to a table.

### INSERT Single Row

```sql
-- Basic syntax
INSERT INTO table_name (column1, column2, column3)
VALUES (value1, value2, value3);

-- Example
INSERT INTO employees (first_name, last_name, email, hire_date, salary)
VALUES ('John', 'Doe', 'john.doe@company.com', '2024-01-15', 50000);

-- Insert without specifying columns (must provide all values in order)
INSERT INTO employees
VALUES (NULL, 'Jane', 'Smith', 'jane.smith@company.com', '2024-01-20', 60000);

-- Insert with AUTO_INCREMENT (let database generate ID)
INSERT INTO employees (first_name, last_name, email, hire_date, salary)
VALUES ('Bob', 'Johnson', 'bob.johnson@company.com', CURDATE(), 55000);
```

### INSERT Multiple Rows

```sql
-- Insert multiple rows at once (more efficient)
INSERT INTO employees (first_name, last_name, email, hire_date, salary)
VALUES
    ('Alice', 'Williams', 'alice.williams@company.com', '2024-02-01', 65000),
    ('Charlie', 'Brown', 'charlie.brown@company.com', '2024-02-05', 58000),
    ('Diana', 'Davis', 'diana.davis@company.com', '2024-02-10', 62000);
```

### INSERT with Default Values

```sql
-- Use DEFAULT keyword
INSERT INTO employees (first_name, last_name, email, hire_date, salary, status)
VALUES ('Eve', 'Wilson', 'eve.wilson@company.com', CURDATE(), 54000, DEFAULT);

-- Omit columns with defaults (they'll use default values)
INSERT INTO employees (first_name, last_name, email)
VALUES ('Frank', 'Moore', 'frank.moore@company.com');
```

### INSERT from SELECT

```sql
-- Insert data from another table
INSERT INTO employees_backup
SELECT * FROM employees WHERE department_id = 5;

-- Insert specific columns
INSERT INTO employees_summary (employee_name, total_salary)
SELECT CONCAT(first_name, ' ', last_name), salary
FROM employees
WHERE salary > 50000;
```

### INSERT IGNORE and ON DUPLICATE KEY UPDATE

```sql
-- INSERT IGNORE: Skip rows that would cause duplicate key errors
INSERT IGNORE INTO employees (employee_id, first_name, last_name, email)
VALUES (1, 'John', 'Doe', 'john@company.com');  -- Will be skipped if employee_id=1 exists

-- ON DUPLICATE KEY UPDATE: Update if duplicate key exists
INSERT INTO employees (employee_id, first_name, last_name, email, salary)
VALUES (1, 'John', 'Doe', 'john@company.com', 55000)
ON DUPLICATE KEY UPDATE salary = 55000;

-- Update multiple columns on duplicate
INSERT INTO products (product_code, product_name, price, stock)
VALUES ('P001', 'Laptop', 999.99, 10)
ON DUPLICATE KEY UPDATE
    product_name = VALUES(product_name),
    price = VALUES(price),
    stock = stock + VALUES(stock);  -- Add to existing stock
```

### Getting Last Inserted ID

```sql
-- Insert and get the auto-generated ID
INSERT INTO employees (first_name, last_name, email)
VALUES ('Grace', 'Taylor', 'grace.taylor@company.com');

SELECT LAST_INSERT_ID();  -- Returns the auto-generated employee_id
```

---

## UPDATE

The UPDATE statement modifies existing records in a table.

### Basic UPDATE

```sql
-- Update single column
UPDATE employees
SET salary = 60000
WHERE employee_id = 1;

-- Update multiple columns
UPDATE employees
SET
    salary = 65000,
    job_title = 'Senior Developer',
    updated_at = CURRENT_TIMESTAMP
WHERE employee_id = 1;

-- Update all rows (use with caution!)
UPDATE employees
SET status = 'active';  -- Updates ALL employees
```

### UPDATE with Calculations

```sql
-- Increase salary by 10%
UPDATE employees
SET salary = salary * 1.10
WHERE department_id = 5;

-- Give $5000 raise to employees hired before 2023
UPDATE employees
SET salary = salary + 5000
WHERE hire_date < '2023-01-01';

-- Update based on condition
UPDATE products
SET
    discount = CASE
        WHEN price > 1000 THEN 0.20
        WHEN price > 500 THEN 0.15
        WHEN price > 100 THEN 0.10
        ELSE 0.05
    END;
```

### UPDATE with JOIN

```sql
-- Update based on data from another table
UPDATE employees e
JOIN departments d ON e.department_id = d.department_id
SET e.location = d.location
WHERE d.department_name = 'Engineering';

-- Update with multiple tables
UPDATE orders o
JOIN customers c ON o.customer_id = c.customer_id
SET o.discount = 0.10
WHERE c.loyalty_points > 1000;
```

### UPDATE with Subquery

```sql
-- Update using subquery
UPDATE employees
SET salary = (
    SELECT AVG(salary)
    FROM employees
    WHERE department_id = 5
)
WHERE employee_id = 10;

-- Update based on subquery condition
UPDATE products
SET status = 'discontinued'
WHERE product_id IN (
    SELECT product_id
    FROM order_items
    GROUP BY product_id
    HAVING COUNT(*) = 0
);
```

### Safe UPDATE Mode

```sql
-- MySQL safe update mode prevents updates without WHERE clause
-- Disable temporarily (use with caution)
SET SQL_SAFE_UPDATES = 0;

UPDATE employees SET status = 'active';

SET SQL_SAFE_UPDATES = 1;
```

---

## DELETE

The DELETE statement removes rows from a table.

### Basic DELETE

```sql
-- Delete specific rows
DELETE FROM employees
WHERE employee_id = 1;

-- Delete with multiple conditions
DELETE FROM employees
WHERE department_id = 5 AND hire_date < '2020-01-01';

-- Delete all rows (dangerous - use with caution!)
DELETE FROM employees;  -- Deletes all data but keeps table structure
```

### DELETE with JOIN

```sql
-- Delete based on data from another table
DELETE e
FROM employees e
JOIN departments d ON e.department_id = d.department_id
WHERE d.department_name = 'Temporary';

-- Delete with multiple table join
DELETE o
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
WHERE c.status = 'inactive' AND o.order_date < '2020-01-01';
```

### DELETE with Subquery

```sql
-- Delete using subquery
DELETE FROM employees
WHERE department_id IN (
    SELECT department_id
    FROM departments
    WHERE department_name = 'Closed Department'
);

-- Delete orphaned records
DELETE FROM order_items
WHERE order_id NOT IN (
    SELECT order_id FROM orders
);
```

### DELETE vs TRUNCATE

```sql
-- DELETE: Can use WHERE, slower, can rollback, keeps AUTO_INCREMENT
DELETE FROM employees WHERE department_id = 5;

-- TRUNCATE: Removes all rows, faster, resets AUTO_INCREMENT, can't rollback
TRUNCATE TABLE employees;
```

---

## DQL - Data Query Language

DQL (Data Query Language) is used to query and retrieve data from the database. The SELECT statement is the primary DQL command.

```
SELECT Statement Components
├── SELECT    - Specify columns to retrieve
├── FROM      - Specify table(s)
├── WHERE     - Filter rows
├── GROUP BY  - Group rows
├── HAVING    - Filter groups
├── ORDER BY  - Sort results
└── LIMIT     - Limit number of rows
```

---

## SELECT

The SELECT statement retrieves data from one or more tables.

### Basic SELECT

```sql
-- Select all columns
SELECT * FROM employees;

-- Select specific columns
SELECT first_name, last_name, email FROM employees;

-- Select with alias
SELECT
    first_name AS 'First Name',
    last_name AS 'Last Name',
    salary AS 'Annual Salary'
FROM employees;

-- Select distinct values (remove duplicates)
SELECT DISTINCT department_id FROM employees;

-- Select with expressions
SELECT
    first_name,
    last_name,
    salary,
    salary * 12 AS annual_salary,
    salary * 0.1 AS tax
FROM employees;
```

### SELECT with Literals

```sql
-- Add literal values to result
SELECT
    'Employee' AS type,
    first_name,
    last_name,
    100 AS bonus
FROM employees;
```

---

## WHERE Clause

The WHERE clause filters rows based on specified conditions.

### Basic WHERE

```sql
-- Single condition
SELECT * FROM employees WHERE department_id = 5;

-- Multiple conditions with AND
SELECT * FROM employees
WHERE department_id = 5 AND salary > 50000;

-- Multiple conditions with OR
SELECT * FROM employees
WHERE department_id = 5 OR department_id = 10;

-- NOT operator
SELECT * FROM employees
WHERE NOT department_id = 5;
```

### Comparison Operators

| Operator | Description | Example |
|----------|-------------|---------|
| = | Equal to | WHERE salary = 50000 |
| != or <> | Not equal to | WHERE status != 'inactive' |
| > | Greater than | WHERE salary > 50000 |
| < | Less than | WHERE age < 30 |
| >= | Greater than or equal | WHERE salary >= 50000 |
| <= | Less than or equal | WHERE age <= 65 |

```sql
-- Examples
SELECT * FROM employees WHERE salary > 50000;
SELECT * FROM employees WHERE hire_date < '2023-01-01';
SELECT * FROM products WHERE price >= 100 AND price <= 500;
```

### BETWEEN Operator

```sql
-- Range query (inclusive)
SELECT * FROM employees
WHERE salary BETWEEN 50000 AND 80000;

-- Equivalent to:
SELECT * FROM employees
WHERE salary >= 50000 AND salary <= 80000;

-- Date range
SELECT * FROM orders
WHERE order_date BETWEEN '2024-01-01' AND '2024-12-31';
```

### IN Operator

```sql
-- Multiple specific values
SELECT * FROM employees
WHERE department_id IN (5, 10, 15);

-- Equivalent to:
SELECT * FROM employees
WHERE department_id = 5 OR department_id = 10 OR department_id = 15;

-- NOT IN
SELECT * FROM employees
WHERE department_id NOT IN (5, 10);

-- IN with subquery
SELECT * FROM employees
WHERE department_id IN (
    SELECT department_id FROM departments WHERE location = 'New York'
);
```

### LIKE Operator (Pattern Matching)

```sql
-- Wildcards: % (any characters), _ (single character)

-- Starts with 'John'
SELECT * FROM employees WHERE first_name LIKE 'John%';

-- Ends with 'son'
SELECT * FROM employees WHERE last_name LIKE '%son';

-- Contains 'tech'
SELECT * FROM products WHERE description LIKE '%tech%';

-- Second character is 'o'
SELECT * FROM employees WHERE first_name LIKE '_o%';

-- NOT LIKE
SELECT * FROM employees WHERE email NOT LIKE '%@gmail.com';

-- Case-insensitive by default in MySQL
SELECT * FROM employees WHERE first_name LIKE 'john%';  -- Matches 'John', 'JOHN', 'john'
```

### IS NULL / IS NOT NULL

```sql
-- Find NULL values
SELECT * FROM employees WHERE manager_id IS NULL;

-- Find non-NULL values
SELECT * FROM employees WHERE phone IS NOT NULL;

-- NULL in expressions
SELECT * FROM employees
WHERE department_id IS NULL OR salary < 40000;
```

---

## ORDER BY Clause

The ORDER BY clause sorts the result set.

### Basic ORDER BY

```sql
-- Ascending order (default)
SELECT * FROM employees ORDER BY salary;
-- or
SELECT * FROM employees ORDER BY salary ASC;

-- Descending order
SELECT * FROM employees ORDER BY salary DESC;

-- Multiple columns
SELECT * FROM employees
ORDER BY department_id ASC, salary DESC;

-- Order by column position (not recommended for maintainability)
SELECT first_name, last_name, salary
FROM employees
ORDER BY 3 DESC;  -- Orders by 3rd column (salary)
```

### ORDER BY with Expressions

```sql
-- Order by calculated value
SELECT first_name, last_name, salary, salary * 12 AS annual_salary
FROM employees
ORDER BY annual_salary DESC;

-- Order by CASE
SELECT first_name, last_name, department_id
FROM employees
ORDER BY CASE department_id
    WHEN 1 THEN 1
    WHEN 5 THEN 2
    WHEN 10 THEN 3
    ELSE 4
END;
```

---

## GROUP BY Clause

The GROUP BY clause groups rows with the same values in specified columns.

### Basic GROUP BY

```sql
-- Count employees per department
SELECT department_id, COUNT(*) AS employee_count
FROM employees
GROUP BY department_id;

-- Average salary per department
SELECT department_id, AVG(salary) AS avg_salary
FROM employees
GROUP BY department_id;

-- Multiple columns
SELECT department_id, job_title, COUNT(*) AS count
FROM employees
GROUP BY department_id, job_title;
```

### GROUP BY Rules

```sql
-- ✓ Valid: Select columns in GROUP BY or aggregate functions
SELECT department_id, COUNT(*), AVG(salary)
FROM employees
GROUP BY department_id;

-- ✗ Invalid: Select column not in GROUP BY without aggregate
-- SELECT department_id, first_name, COUNT(*)
-- FROM employees
-- GROUP BY department_id;  -- Error: first_name not in GROUP BY
```

---

## HAVING Clause

The HAVING clause filters groups (use after GROUP BY). WHERE filters rows before grouping, HAVING filters groups after grouping.

### HAVING vs WHERE

```sql
-- WHERE: Filters rows before grouping
SELECT department_id, COUNT(*) AS employee_count
FROM employees
WHERE salary > 50000  -- Filter individual rows
GROUP BY department_id;

-- HAVING: Filters groups after grouping
SELECT department_id, COUNT(*) AS employee_count
FROM employees
GROUP BY department_id
HAVING COUNT(*) > 5;  -- Filter groups

-- Both together
SELECT department_id, AVG(salary) AS avg_salary
FROM employees
WHERE hire_date > '2020-01-01'  -- Filter rows first
GROUP BY department_id
HAVING AVG(salary) > 60000;     -- Filter groups
```

### HAVING Examples

```sql
-- Departments with more than 10 employees
SELECT department_id, COUNT(*) AS emp_count
FROM employees
GROUP BY department_id
HAVING COUNT(*) > 10;

-- Products sold more than 100 times
SELECT product_id, SUM(quantity) AS total_sold
FROM order_items
GROUP BY product_id
HAVING SUM(quantity) > 100;

-- Customers with total orders > $1000
SELECT customer_id, SUM(total_amount) AS total_spent
FROM orders
GROUP BY customer_id
HAVING SUM(total_amount) > 1000
ORDER BY total_spent DESC;
```

---

## Aggregate Functions

Aggregate functions perform calculations on sets of values and return a single value.

### Common Aggregate Functions

| Function | Description | Example |
|----------|-------------|---------|
| COUNT() | Count rows | COUNT(*), COUNT(column) |
| SUM() | Sum of values | SUM(salary) |
| AVG() | Average of values | AVG(price) |
| MIN() | Minimum value | MIN(age) |
| MAX() | Maximum value | MAX(salary) |

### COUNT

```sql
-- Count all rows
SELECT COUNT(*) FROM employees;

-- Count non-NULL values in specific column
SELECT COUNT(manager_id) FROM employees;

-- Count distinct values
SELECT COUNT(DISTINCT department_id) FROM employees;

-- Count with condition
SELECT COUNT(*) FROM employees WHERE salary > 50000;

-- Count per group
SELECT department_id, COUNT(*) AS employee_count
FROM employees
GROUP BY department_id;
```

### SUM

```sql
-- Total of all salaries
SELECT SUM(salary) FROM employees;

-- Sum per group
SELECT department_id, SUM(salary) AS total_salary
FROM employees
GROUP BY department_id;

-- Sum with condition
SELECT SUM(salary) FROM employees WHERE department_id = 5;

-- Sum of calculation
SELECT SUM(quantity * price) AS total_revenue
FROM order_items;
```

### AVG

```sql
-- Average salary
SELECT AVG(salary) FROM employees;

-- Average per group
SELECT department_id, AVG(salary) AS avg_salary
FROM employees
GROUP BY department_id;

-- Average with rounding
SELECT ROUND(AVG(salary), 2) AS avg_salary FROM employees;

-- Average with condition
SELECT AVG(salary) FROM employees WHERE hire_date > '2023-01-01';
```

### MIN and MAX

```sql
-- Minimum and maximum salary
SELECT MIN(salary) AS min_salary, MAX(salary) AS max_salary
FROM employees;

-- Per group
SELECT
    department_id,
    MIN(salary) AS min_salary,
    MAX(salary) AS max_salary
FROM employees
GROUP BY department_id;

-- Find employee with highest salary
SELECT * FROM employees
WHERE salary = (SELECT MAX(salary) FROM employees);
```

### Multiple Aggregates

```sql
-- Comprehensive statistics
SELECT
    department_id,
    COUNT(*) AS employee_count,
    SUM(salary) AS total_salary,
    AVG(salary) AS avg_salary,
    MIN(salary) AS min_salary,
    MAX(salary) AS max_salary,
    MAX(salary) - MIN(salary) AS salary_range
FROM employees
GROUP BY department_id
ORDER BY avg_salary DESC;
```

---

## LIMIT Clause

The LIMIT clause restricts the number of rows returned.

```sql
-- Get first 10 rows
SELECT * FROM employees LIMIT 10;

-- Get top 5 highest paid employees
SELECT * FROM employees
ORDER BY salary DESC
LIMIT 5;

-- Pagination: Skip first 10, get next 10
SELECT * FROM employees
LIMIT 10 OFFSET 10;

-- Alternative syntax
SELECT * FROM employees
LIMIT 10, 10;  -- LIMIT offset, count
```

---

## Complete Query Examples

### Example 1: Employee Report

```sql
SELECT
    d.department_name,
    COUNT(e.employee_id) AS employee_count,
    ROUND(AVG(e.salary), 2) AS avg_salary,
    MIN(e.salary) AS min_salary,
    MAX(e.salary) AS max_salary
FROM employees e
JOIN departments d ON e.department_id = d.department_id
WHERE e.status = 'active'
GROUP BY d.department_id, d.department_name
HAVING AVG(e.salary) > 50000
ORDER BY avg_salary DESC;
```

### Example 2: Sales Analysis

```sql
SELECT
    p.product_name,
    COUNT(DISTINCT o.order_id) AS order_count,
    SUM(oi.quantity) AS total_quantity,
    SUM(oi.quantity * oi.unit_price) AS total_revenue,
    AVG(oi.unit_price) AS avg_price
FROM products p
JOIN order_items oi ON p.product_id = oi.product_id
JOIN orders o ON oi.order_id = o.order_id
WHERE o.order_date BETWEEN '2024-01-01' AND '2024-12-31'
GROUP BY p.product_id, p.product_name
HAVING SUM(oi.quantity) > 100
ORDER BY total_revenue DESC
LIMIT 10;
```

### Example 3: Customer Summary

```sql
SELECT
    c.customer_id,
    CONCAT(c.first_name, ' ', c.last_name) AS customer_name,
    c.email,
    COUNT(o.order_id) AS order_count,
    COALESCE(SUM(o.total_amount), 0) AS total_spent,
    COALESCE(AVG(o.total_amount), 0) AS avg_order_value,
    MAX(o.order_date) AS last_order_date
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.first_name, c.last_name, c.email
HAVING COUNT(o.order_id) > 0
ORDER BY total_spent DESC;
```

---

## Query Execution Order

Understanding the order SQL processes your query helps write better queries:

```
SQL Query Execution Order:

1. FROM       - Get data from tables
2. JOIN       - Combine tables
3. WHERE      - Filter rows
4. GROUP BY   - Group rows
5. HAVING     - Filter groups
6. SELECT     - Select columns
7. DISTINCT   - Remove duplicates
8. ORDER BY   - Sort results
9. LIMIT      - Limit rows
```

**Written Order vs Execution Order:**

```sql
-- Written order:
SELECT department_id, AVG(salary) AS avg_salary  -- 6
FROM employees                                    -- 1
WHERE status = 'active'                          -- 3
GROUP BY department_id                           -- 4
HAVING AVG(salary) > 50000                       -- 5
ORDER BY avg_salary DESC                         -- 8
LIMIT 5;                                         -- 9
```

---

## Best Practices

### Performance Tips

```sql
-- ✓ Use specific columns instead of *
SELECT first_name, last_name FROM employees;  -- Good
SELECT * FROM employees;  -- Avoid when possible

-- ✓ Use indexes for WHERE, JOIN, ORDER BY columns
CREATE INDEX idx_department ON employees(department_id);

-- ✓ Limit results when testing
SELECT * FROM large_table LIMIT 100;

-- ✓ Use EXPLAIN to analyze query performance
EXPLAIN SELECT * FROM employees WHERE department_id = 5;
```

### Writing Clean Queries

```sql
-- ✓ Use proper formatting
SELECT
    e.first_name,
    e.last_name,
    d.department_name
FROM employees e
JOIN departments d ON e.department_id = d.department_id
WHERE e.salary > 50000
ORDER BY e.last_name;

-- ✓ Use meaningful aliases
SELECT
    c.customer_name,
    o.order_date,
    oi.quantity
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
JOIN order_items oi ON o.order_id = oi.order_id;

-- ✓ Comment complex queries
-- Get top 10 customers by total spending in 2024
SELECT
    c.customer_id,
    CONCAT(c.first_name, ' ', c.last_name) AS name,
    SUM(o.total_amount) AS total_spent
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
WHERE YEAR(o.order_date) = 2024
GROUP BY c.customer_id, c.first_name, c.last_name
ORDER BY total_spent DESC
LIMIT 10;
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| **INSERT** | Add new rows: single, multiple, from SELECT, IGNORE, ON DUPLICATE KEY |
| **UPDATE** | Modify rows: simple, with JOIN, with subquery, calculations |
| **DELETE** | Remove rows: simple, with JOIN, with subquery |
| **SELECT** | Retrieve data: columns, expressions, DISTINCT, aliases |
| **WHERE** | Filter rows: =, !=, >, <, BETWEEN, IN, LIKE, IS NULL |
| **ORDER BY** | Sort results: ASC, DESC, multiple columns |
| **GROUP BY** | Group rows: use with aggregate functions |
| **HAVING** | Filter groups: use after GROUP BY |
| **Aggregates** | COUNT, SUM, AVG, MIN, MAX - operate on sets |
| **LIMIT** | Restrict rows returned: top N, pagination |

## Next Topic

Continue to [DCL and TCL](./05-dcl-and-tcl.md) to learn about access control and transaction management.
