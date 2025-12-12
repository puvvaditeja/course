# Joins and Subqueries

## Introduction to Joins

A JOIN clause combines rows from two or more tables based on a related column between them. Joins are fundamental to relational databases and allow you to retrieve data from multiple tables in a single query.

### Why Use Joins?

In normalized databases, data is split across multiple tables to reduce redundancy. Joins allow you to:
- Combine related data from different tables
- Retrieve comprehensive information in one query
- Maintain data integrity through relationships
- Query efficiently across related entities

### Join Syntax

```sql
-- Basic join syntax
SELECT columns
FROM table1
JOIN table2 ON table1.column = table2.column;

-- Alternative syntax (older style, avoid when possible)
SELECT columns
FROM table1, table2
WHERE table1.column = table2.column;
```

---

## Sample Tables for Examples

We'll use these tables for all join examples:

```sql
-- Customers table
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    customer_name VARCHAR(100),
    city VARCHAR(50)
);

INSERT INTO customers VALUES
    (1, 'Alice', 'New York'),
    (2, 'Bob', 'Los Angeles'),
    (3, 'Charlie', 'Chicago'),
    (4, 'Diana', 'Houston'),
    (5, 'Eve', 'Phoenix');

-- Orders table
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    amount DECIMAL(10, 2)
);

INSERT INTO orders VALUES
    (101, 1, '2024-01-15', 150.00),
    (102, 1, '2024-02-20', 200.00),
    (103, 2, '2024-01-25', 300.00),
    (104, 3, '2024-03-10', 450.00),
    (105, NULL, '2024-03-15', 100.00);  -- Order without customer

-- Products table
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100),
    category VARCHAR(50),
    price DECIMAL(10, 2)
);

INSERT INTO products VALUES
    (1, 'Laptop', 'Electronics', 999.99),
    (2, 'Mouse', 'Electronics', 29.99),
    (3, 'Desk Chair', 'Furniture', 199.99),
    (4, 'Monitor', 'Electronics', 299.99);
```

---

## INNER JOIN

INNER JOIN returns only the rows that have matching values in both tables.

### Syntax

```sql
SELECT columns
FROM table1
INNER JOIN table2 ON table1.column = table2.column;

-- INNER keyword is optional (JOIN defaults to INNER JOIN)
SELECT columns
FROM table1
JOIN table2 ON table1.column = table2.column;
```

### INNER JOIN Examples

```sql
-- Get all orders with customer information
SELECT
    o.order_id,
    c.customer_name,
    c.city,
    o.order_date,
    o.amount
FROM orders o
INNER JOIN customers c ON o.customer_id = c.customer_id;

-- Result: Returns 4 rows (only orders with matching customers)
-- Order 105 is excluded (customer_id is NULL)
-- Diana and Eve are excluded (no orders)
```

### INNER JOIN Visualization

```
customers                     orders
┌─────────────┬──────────┐   ┌──────────┬─────────────┐
│ customer_id │   name   │   │ order_id │ customer_id │
├─────────────┼──────────┤   ├──────────┼─────────────┤
│      1      │  Alice   │───│   101    │      1      │
│      1      │  Alice   │───│   102    │      1      │
│      2      │   Bob    │───│   103    │      2      │
│      3      │ Charlie  │───│   104    │      3      │
│      4      │  Diana   │   │   105    │    NULL     │
│      5      │   Eve    │   └──────────┴─────────────┘
└─────────────┴──────────┘
        ↑                              ↑
        └──────────────────────────────┘
        Only matching rows returned (4 rows)
```

### Multiple INNER JOINs

```sql
-- Join three tables
SELECT
    c.customer_name,
    o.order_id,
    oi.product_id,
    p.product_name,
    oi.quantity,
    oi.unit_price
FROM customers c
INNER JOIN orders o ON c.customer_id = o.customer_id
INNER JOIN order_items oi ON o.order_id = oi.order_id
INNER JOIN products p ON oi.product_id = p.product_id;
```

---

## LEFT JOIN (LEFT OUTER JOIN)

LEFT JOIN returns all rows from the left table and matching rows from the right table. If no match exists, NULL values are returned for right table columns.

### Syntax

```sql
SELECT columns
FROM table1
LEFT JOIN table2 ON table1.column = table2.column;

-- Or explicitly:
SELECT columns
FROM table1
LEFT OUTER JOIN table2 ON table1.column = table2.column;
```

### LEFT JOIN Examples

```sql
-- Get all customers and their orders (including customers without orders)
SELECT
    c.customer_id,
    c.customer_name,
    o.order_id,
    o.amount
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id;

-- Result: Returns 6 rows
-- Alice: 2 orders (101, 102)
-- Bob: 1 order (103)
-- Charlie: 1 order (104)
-- Diana: NULL (no orders)
-- Eve: NULL (no orders)
```

### LEFT JOIN Visualization

```
customers                     orders
┌─────────────┬──────────┐   ┌──────────┬─────────────┐
│ customer_id │   name   │   │ order_id │ customer_id │
├─────────────┼──────────┤   ├──────────┼─────────────┤
│      1      │  Alice   │───│   101    │      1      │
│      1      │  Alice   │───│   102    │      1      │
│      2      │   Bob    │───│   103    │      2      │
│      3      │ Charlie  │───│   104    │      3      │
│      4      │  Diana   │   │          │             │ NULL
│      5      │   Eve    │   │          │             │ NULL
└─────────────┴──────────┘   └──────────┴─────────────┘
  ALL rows from left     +    matching rows from right
  (6 rows total)
```

### Finding Records Without Matches

```sql
-- Find customers who have NOT placed any orders
SELECT
    c.customer_id,
    c.customer_name
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
WHERE o.order_id IS NULL;

-- Result: Diana and Eve
```

---

## RIGHT JOIN (RIGHT OUTER JOIN)

RIGHT JOIN returns all rows from the right table and matching rows from the left table. If no match exists, NULL values are returned for left table columns.

### Syntax

```sql
SELECT columns
FROM table1
RIGHT JOIN table2 ON table1.column = table2.column;

-- Or explicitly:
SELECT columns
FROM table1
RIGHT OUTER JOIN table2 ON table1.column = table2.column;
```

### RIGHT JOIN Examples

```sql
-- Get all orders and customer information (including orders without customers)
SELECT
    c.customer_name,
    o.order_id,
    o.amount
FROM customers c
RIGHT JOIN orders o ON c.customer_id = o.customer_id;

-- Result: Returns 5 rows
-- Alice: orders 101, 102
-- Bob: order 103
-- Charlie: order 104
-- NULL: order 105 (no customer)
```

### RIGHT JOIN Visualization

```
customers                     orders
┌─────────────┬──────────┐   ┌──────────┬─────────────┐
│ customer_id │   name   │   │ order_id │ customer_id │
├─────────────┼──────────┤   ├──────────┼─────────────┤
│      1      │  Alice   │───│   101    │      1      │
│      1      │  Alice   │───│   102    │      1      │
│      2      │   Bob    │───│   103    │      2      │
│      3      │ Charlie  │───│   104    │      3      │
│      4      │  Diana   │   │   105    │    NULL     │ NULL
│      5      │   Eve    │   │          │             │
└─────────────┴──────────┘   └──────────┴─────────────┘
  matching rows from left +    ALL rows from right
  (5 rows total)
```

### RIGHT JOIN vs LEFT JOIN

```sql
-- These queries return the same result (just reversed)
-- RIGHT JOIN
SELECT * FROM customers c
RIGHT JOIN orders o ON c.customer_id = o.customer_id;

-- Equivalent LEFT JOIN
SELECT * FROM orders o
LEFT JOIN customers c ON o.customer_id = c.customer_id;

-- Best Practice: Use LEFT JOIN (more intuitive)
```

---

## FULL OUTER JOIN

FULL OUTER JOIN returns all rows when there's a match in either left or right table. Returns NULL for non-matching rows from both sides.

### MySQL Note

MySQL doesn't directly support FULL OUTER JOIN, but you can simulate it using UNION.

### Simulating FULL OUTER JOIN

```sql
-- Simulate FULL OUTER JOIN using UNION
SELECT
    c.customer_id,
    c.customer_name,
    o.order_id,
    o.amount
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id

UNION

SELECT
    c.customer_id,
    c.customer_name,
    o.order_id,
    o.amount
FROM customers c
RIGHT JOIN orders o ON c.customer_id = o.customer_id;

-- Result: Returns 7 rows
-- All customers (including those without orders)
-- All orders (including those without customers)
```

### FULL OUTER JOIN Visualization

```
customers                     orders
┌─────────────┬──────────┐   ┌──────────┬─────────────┐
│ customer_id │   name   │   │ order_id │ customer_id │
├─────────────┼──────────┤   ├──────────┼─────────────┤
│      1      │  Alice   │───│   101    │      1      │
│      1      │  Alice   │───│   102    │      1      │
│      2      │   Bob    │───│   103    │      2      │
│      3      │ Charlie  │───│   104    │      3      │
│      4      │  Diana   │   │   105    │    NULL     │ NULL
│      5      │   Eve    │   │          │             │ NULL
└─────────────┴──────────┘   └──────────┴─────────────┘
  ALL rows from both tables (7 rows)
  NULLs where no match exists
```

---

## CROSS JOIN

CROSS JOIN returns the Cartesian product of both tables (all possible combinations).

### Syntax

```sql
-- Explicit CROSS JOIN
SELECT columns
FROM table1
CROSS JOIN table2;

-- Implicit cross join (no ON clause)
SELECT columns
FROM table1, table2;
```

### CROSS JOIN Examples

```sql
-- Create all possible customer-product combinations
SELECT
    c.customer_name,
    p.product_name,
    p.price
FROM customers c
CROSS JOIN products p;

-- If customers has 5 rows and products has 4 rows:
-- Result: 5 × 4 = 20 rows (all combinations)
```

### CROSS JOIN Visualization

```
customers (3)               products (2)
┌──────────┐               ┌───────────┐
│  Alice   │               │  Laptop   │
│   Bob    │    ×          │   Mouse   │
│ Charlie  │               └───────────┘
└──────────┘

Result: 3 × 2 = 6 combinations
┌──────────┬───────────┐
│  Alice   │  Laptop   │
│  Alice   │   Mouse   │
│   Bob    │  Laptop   │
│   Bob    │   Mouse   │
│ Charlie  │  Laptop   │
│ Charlie  │   Mouse   │
└──────────┴───────────┘
```

### Practical CROSS JOIN Use Cases

```sql
-- Generate all date-product combinations for a report
SELECT
    d.date,
    p.product_name,
    COALESCE(s.quantity_sold, 0) AS quantity_sold
FROM date_dimension d
CROSS JOIN products p
LEFT JOIN sales s ON d.date = s.sale_date AND p.product_id = s.product_id
WHERE d.date BETWEEN '2024-01-01' AND '2024-12-31';

-- Create size-color combinations for products
SELECT
    p.product_name,
    s.size,
    c.color
FROM products p
CROSS JOIN sizes s
CROSS JOIN colors c;
```

---

## SELF JOIN

A SELF JOIN joins a table to itself. Useful for hierarchical data or comparing rows within the same table.

### Syntax

```sql
SELECT columns
FROM table1 t1
JOIN table1 t2 ON t1.column = t2.column;
```

### SELF JOIN Examples

#### Example 1: Employee-Manager Relationship

```sql
-- Employees table with manager reference
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    employee_name VARCHAR(100),
    manager_id INT
);

INSERT INTO employees VALUES
    (1, 'Alice', NULL),      -- CEO (no manager)
    (2, 'Bob', 1),           -- Reports to Alice
    (3, 'Charlie', 1),       -- Reports to Alice
    (4, 'Diana', 2),         -- Reports to Bob
    (5, 'Eve', 2);           -- Reports to Bob

-- Find employees with their managers
SELECT
    e.employee_name AS employee,
    m.employee_name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.employee_id;

-- Result:
-- Alice → NULL (no manager)
-- Bob → Alice
-- Charlie → Alice
-- Diana → Bob
-- Eve → Bob
```

#### Example 2: Find Pairs

```sql
-- Find customers from the same city
SELECT
    c1.customer_name AS customer1,
    c2.customer_name AS customer2,
    c1.city
FROM customers c1
JOIN customers c2 ON c1.city = c2.city AND c1.customer_id < c2.customer_id;
-- Note: c1.customer_id < c2.customer_id prevents duplicates and self-matching
```

#### Example 3: Comparing Values

```sql
-- Find products with higher price than others in same category
SELECT
    p1.product_name,
    p1.price,
    p2.product_name AS cheaper_product,
    p2.price AS cheaper_price
FROM products p1
JOIN products p2 ON p1.category = p2.category AND p1.price > p2.price;
```

---

## EQUI JOIN vs THETA JOIN

### EQUI JOIN

Join using equality operator (=). Most common join type.

```sql
-- Equi join (uses =)
SELECT *
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id;
```

### THETA JOIN

Join using any comparison operator (=, <, >, <=, >=, !=).

```sql
-- Theta join (uses >)
SELECT
    e1.employee_name,
    e1.salary,
    e2.employee_name AS higher_paid,
    e2.salary AS higher_salary
FROM employees e1
JOIN employees e2 ON e1.salary < e2.salary;

-- Find orders placed before customer registration
SELECT
    o.order_id,
    c.customer_name,
    o.order_date,
    c.registration_date
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
    AND o.order_date < c.registration_date;
```

---

## Join Comparison Table

| Join Type | Returns | Use Case |
|-----------|---------|----------|
| **INNER JOIN** | Only matching rows from both tables | Get related data where relationship exists |
| **LEFT JOIN** | All from left + matching from right | Include all from primary table, with optional related data |
| **RIGHT JOIN** | All from right + matching from left | Same as LEFT JOIN (reversed) |
| **FULL OUTER JOIN** | All rows from both tables | Find all records with or without matches |
| **CROSS JOIN** | Cartesian product (all combinations) | Generate combinations, date dimensions |
| **SELF JOIN** | Table joined to itself | Hierarchies, comparisons within same table |

---

## Subqueries

A subquery (or inner query) is a query nested inside another query. Subqueries can be used in SELECT, FROM, WHERE, and HAVING clauses.

### Types of Subqueries

```
Subqueries
├── Scalar Subquery      - Returns single value
├── Row Subquery         - Returns single row (multiple columns)
├── Column Subquery      - Returns single column (multiple rows)
├── Table Subquery       - Returns table (multiple rows and columns)
├── Correlated Subquery  - References outer query
└── Non-Correlated       - Independent of outer query
```

---

## Scalar Subquery

Returns a single value (one row, one column).

```sql
-- Find employees earning more than average salary
SELECT employee_name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);

-- Get customer with highest number of orders
SELECT customer_name
FROM customers
WHERE customer_id = (
    SELECT customer_id
    FROM orders
    GROUP BY customer_id
    ORDER BY COUNT(*) DESC
    LIMIT 1
);

-- Update with scalar subquery
UPDATE products
SET price = price * 1.1
WHERE price < (SELECT AVG(price) FROM products);
```

---

## Column Subquery (Multiple Rows)

Returns a single column with multiple rows. Use with IN, ANY, ALL operators.

### IN Operator

```sql
-- Find customers who have placed orders
SELECT customer_name
FROM customers
WHERE customer_id IN (
    SELECT DISTINCT customer_id
    FROM orders
);

-- Find products never ordered
SELECT product_name
FROM products
WHERE product_id NOT IN (
    SELECT DISTINCT product_id
    FROM order_items
    WHERE product_id IS NOT NULL  -- Important: handle NULLs
);
```

### ANY Operator

```sql
-- Find employees earning more than ANY employee in department 5
SELECT employee_name, salary
FROM employees
WHERE salary > ANY (
    SELECT salary FROM employees WHERE department_id = 5
);
-- Returns employees earning more than the minimum salary in dept 5

-- Equivalent to:
WHERE salary > (SELECT MIN(salary) FROM employees WHERE department_id = 5);
```

### ALL Operator

```sql
-- Find employees earning more than ALL employees in department 5
SELECT employee_name, salary
FROM employees
WHERE salary > ALL (
    SELECT salary FROM employees WHERE department_id = 5
);
-- Returns employees earning more than the maximum salary in dept 5

-- Equivalent to:
WHERE salary > (SELECT MAX(salary) FROM employees WHERE department_id = 5);
```

---

## Row Subquery

Returns a single row with multiple columns.

```sql
-- Find employee with same job title and salary as employee 100
SELECT employee_name
FROM employees
WHERE (job_title, salary) = (
    SELECT job_title, salary
    FROM employees
    WHERE employee_id = 100
);
```

---

## Table Subquery (Derived Table)

Returns a table (multiple rows and columns). Used in FROM clause.

```sql
-- Use subquery as a table
SELECT dept_summary.department_id, dept_summary.avg_salary
FROM (
    SELECT
        department_id,
        AVG(salary) AS avg_salary,
        COUNT(*) AS employee_count
    FROM employees
    GROUP BY department_id
) AS dept_summary
WHERE dept_summary.avg_salary > 60000;

-- Find top 3 customers by order count
SELECT c.customer_name, order_stats.order_count
FROM customers c
JOIN (
    SELECT customer_id, COUNT(*) AS order_count
    FROM orders
    GROUP BY customer_id
) AS order_stats ON c.customer_id = order_stats.customer_id
ORDER BY order_stats.order_count DESC
LIMIT 3;
```

---

## Correlated Subquery

A correlated subquery references columns from the outer query. It's executed once for each row of the outer query.

```sql
-- Find employees earning more than average in their department
SELECT e1.employee_name, e1.department_id, e1.salary
FROM employees e1
WHERE e1.salary > (
    SELECT AVG(e2.salary)
    FROM employees e2
    WHERE e2.department_id = e1.department_id  -- Correlated
);

-- Find customers who have placed orders above average order amount
SELECT c.customer_name
FROM customers c
WHERE EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.customer_id = c.customer_id  -- Correlated
        AND o.amount > (SELECT AVG(amount) FROM orders)
);

-- Find products with above-average price in their category
SELECT p1.product_name, p1.category, p1.price
FROM products p1
WHERE p1.price > (
    SELECT AVG(p2.price)
    FROM products p2
    WHERE p2.category = p1.category  -- Correlated
);
```

---

## EXISTS and NOT EXISTS

EXISTS checks if subquery returns any rows (returns TRUE/FALSE).

### EXISTS

```sql
-- Find customers who have placed at least one order
SELECT customer_name
FROM customers c
WHERE EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.customer_id = c.customer_id
);

-- Find departments with employees
SELECT department_name
FROM departments d
WHERE EXISTS (
    SELECT 1
    FROM employees e
    WHERE e.department_id = d.department_id
);
```

### NOT EXISTS

```sql
-- Find customers who have NOT placed any orders
SELECT customer_name
FROM customers c
WHERE NOT EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.customer_id = c.customer_id
);

-- Find products never ordered
SELECT product_name
FROM products p
WHERE NOT EXISTS (
    SELECT 1
    FROM order_items oi
    WHERE oi.product_id = p.product_id
);
```

### EXISTS vs IN

```sql
-- EXISTS: Better performance for large datasets (stops when finds first match)
SELECT customer_name
FROM customers c
WHERE EXISTS (
    SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id
);

-- IN: Builds entire list before checking
SELECT customer_name
FROM customers c
WHERE c.customer_id IN (
    SELECT customer_id FROM orders
);

-- For NOT NULL columns, both work similarly
-- EXISTS is generally faster for correlated queries
-- IN is simpler for non-correlated queries
```

---

## Subquery in SELECT Clause

```sql
-- Add calculated columns using subqueries
SELECT
    c.customer_name,
    c.city,
    (SELECT COUNT(*)
     FROM orders o
     WHERE o.customer_id = c.customer_id) AS order_count,
    (SELECT COALESCE(SUM(amount), 0)
     FROM orders o
     WHERE o.customer_id = c.customer_id) AS total_spent
FROM customers c;
```

---

## Complex SQL Queries

Combining joins, subqueries, and aggregations for complex business logic.

### Example 1: Comprehensive Sales Report

```sql
-- Sales report with customer ranking
SELECT
    c.customer_id,
    c.customer_name,
    c.city,
    COUNT(DISTINCT o.order_id) AS order_count,
    COALESCE(SUM(o.amount), 0) AS total_spent,
    COALESCE(AVG(o.amount), 0) AS avg_order_value,
    MAX(o.order_date) AS last_order_date,
    DATEDIFF(CURDATE(), MAX(o.order_date)) AS days_since_last_order,
    (SELECT COUNT(DISTINCT customer_id) FROM orders) AS total_customers,
    RANK() OVER (ORDER BY SUM(o.amount) DESC) AS spending_rank
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.customer_name, c.city
ORDER BY total_spent DESC;
```

### Example 2: Product Performance Analysis

```sql
-- Analyze product performance across categories
SELECT
    p.category,
    p.product_name,
    p.price,
    COALESCE(sales.total_quantity, 0) AS total_sold,
    COALESCE(sales.total_revenue, 0) AS revenue,
    COALESCE(sales.order_count, 0) AS times_ordered,
    -- Compare to category average
    (SELECT AVG(price) FROM products WHERE category = p.category) AS category_avg_price,
    -- Performance indicator
    CASE
        WHEN COALESCE(sales.total_quantity, 0) = 0 THEN 'No Sales'
        WHEN COALESCE(sales.total_quantity, 0) < 10 THEN 'Low'
        WHEN COALESCE(sales.total_quantity, 0) < 50 THEN 'Medium'
        ELSE 'High'
    END AS performance
FROM products p
LEFT JOIN (
    SELECT
        product_id,
        SUM(quantity) AS total_quantity,
        SUM(quantity * unit_price) AS total_revenue,
        COUNT(DISTINCT order_id) AS order_count
    FROM order_items
    GROUP BY product_id
) AS sales ON p.product_id = sales.product_id
ORDER BY p.category, revenue DESC;
```

### Example 3: Customer Segmentation

```sql
-- Segment customers by purchase behavior
SELECT
    customer_segment,
    COUNT(*) AS customer_count,
    AVG(total_spent) AS avg_spent,
    AVG(order_count) AS avg_orders,
    MIN(total_spent) AS min_spent,
    MAX(total_spent) AS max_spent
FROM (
    SELECT
        c.customer_id,
        c.customer_name,
        COALESCE(SUM(o.amount), 0) AS total_spent,
        COUNT(o.order_id) AS order_count,
        CASE
            WHEN COALESCE(SUM(o.amount), 0) >= 1000 THEN 'Premium'
            WHEN COALESCE(SUM(o.amount), 0) >= 500 THEN 'Gold'
            WHEN COALESCE(SUM(o.amount), 0) >= 100 THEN 'Silver'
            WHEN COALESCE(SUM(o.amount), 0) > 0 THEN 'Bronze'
            ELSE 'Inactive'
        END AS customer_segment
    FROM customers c
    LEFT JOIN orders o ON c.customer_id = o.customer_id
    GROUP BY c.customer_id, c.customer_name
) AS customer_summary
GROUP BY customer_segment
ORDER BY
    FIELD(customer_segment, 'Premium', 'Gold', 'Silver', 'Bronze', 'Inactive');
```

### Example 4: Time-Series Analysis

```sql
-- Monthly sales trend with moving average
SELECT
    DATE_FORMAT(order_date, '%Y-%m') AS month,
    COUNT(DISTINCT order_id) AS order_count,
    SUM(amount) AS monthly_revenue,
    AVG(amount) AS avg_order_value,
    -- Previous month comparison
    LAG(SUM(amount)) OVER (ORDER BY DATE_FORMAT(order_date, '%Y-%m')) AS prev_month_revenue,
    -- Growth percentage
    ROUND(
        (SUM(amount) - LAG(SUM(amount)) OVER (ORDER BY DATE_FORMAT(order_date, '%Y-%m')))
        / LAG(SUM(amount)) OVER (ORDER BY DATE_FORMAT(order_date, '%Y-%m'))
        * 100,
        2
    ) AS growth_pct,
    -- 3-month moving average
    AVG(SUM(amount)) OVER (
        ORDER BY DATE_FORMAT(order_date, '%Y-%m')
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ) AS moving_avg_3month
FROM orders
GROUP BY DATE_FORMAT(order_date, '%Y-%m')
ORDER BY month;
```

### Example 5: Find Gaps and Islands

```sql
-- Find date ranges with consecutive orders
WITH RECURSIVE date_groups AS (
    SELECT
        order_date,
        ROW_NUMBER() OVER (ORDER BY order_date) AS rn,
        DATE_SUB(order_date, INTERVAL ROW_NUMBER() OVER (ORDER BY order_date) DAY) AS grp
    FROM orders
    GROUP BY order_date
)
SELECT
    MIN(order_date) AS range_start,
    MAX(order_date) AS range_end,
    DATEDIFF(MAX(order_date), MIN(order_date)) + 1 AS consecutive_days
FROM date_groups
GROUP BY grp
ORDER BY range_start;
```

---

## Query Optimization Tips

### 1. Use Indexes

```sql
-- Create indexes on JOIN, WHERE, ORDER BY columns
CREATE INDEX idx_customer_id ON orders(customer_id);
CREATE INDEX idx_order_date ON orders(order_date);
CREATE INDEX idx_composite ON orders(customer_id, order_date);
```

### 2. Avoid SELECT *

```sql
-- ✗ Bad: Returns all columns
SELECT * FROM orders o JOIN customers c ON o.customer_id = c.customer_id;

-- ✓ Good: Select only needed columns
SELECT o.order_id, o.amount, c.customer_name
FROM orders o JOIN customers c ON o.customer_id = c.customer_id;
```

### 3. Use EXISTS Instead of IN for Large Datasets

```sql
-- ✓ Better for large datasets
SELECT customer_name FROM customers c
WHERE EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id);

-- ✗ Slower for large datasets
SELECT customer_name FROM customers c
WHERE customer_id IN (SELECT customer_id FROM orders);
```

### 4. Use JOIN Instead of Subqueries When Possible

```sql
-- ✗ Subquery in SELECT (runs for each row)
SELECT
    c.customer_name,
    (SELECT COUNT(*) FROM orders o WHERE o.customer_id = c.customer_id) AS order_count
FROM customers c;

-- ✓ JOIN (more efficient)
SELECT c.customer_name, COUNT(o.order_id) AS order_count
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.customer_name;
```

### 5. Use EXPLAIN to Analyze Queries

```sql
-- Analyze query execution plan
EXPLAIN SELECT c.customer_name, o.order_id
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
WHERE o.order_date > '2024-01-01';

-- Extended explain
EXPLAIN FORMAT=JSON SELECT ...;
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| **INNER JOIN** | Only matching rows from both tables |
| **LEFT JOIN** | All from left + matching from right (NULLs for no match) |
| **RIGHT JOIN** | All from right + matching from left (use LEFT JOIN instead) |
| **FULL OUTER JOIN** | All from both tables (simulated with UNION in MySQL) |
| **CROSS JOIN** | Cartesian product - all combinations |
| **SELF JOIN** | Table joined to itself - hierarchies, comparisons |
| **Scalar Subquery** | Returns single value - use in WHERE, SELECT |
| **Column Subquery** | Returns column - use with IN, ANY, ALL |
| **Table Subquery** | Returns table - use in FROM clause |
| **Correlated Subquery** | References outer query - executed per row |
| **EXISTS** | Check if subquery returns rows - efficient for large data |
| **Complex Queries** | Combine JOINs, subqueries, aggregates, window functions |

---

## Practice Exercises

### Exercise 1: Multiple Joins

Write a query to get order details with customer name, product names, and quantities.

### Exercise 2: LEFT JOIN Analysis

Find all customers and count their orders (including customers with 0 orders).

### Exercise 3: Subquery Challenge

Find products with price higher than average price in their category.

### Exercise 4: Correlated Subquery

List employees earning more than average in their department.

### Exercise 5: Complex Report

Create a comprehensive sales report showing monthly revenue, customer count, and growth percentage.

---

**Congratulations!** You've completed the MySQL module. You now understand database fundamentals, design, DDL/DML operations, access control, transactions, joins, and complex queries.

## Continue Learning

- Practice writing complex queries
- Optimize slow queries using EXPLAIN
- Learn about indexes and query performance
- Explore stored procedures and triggers
- Study database administration and backup strategies
