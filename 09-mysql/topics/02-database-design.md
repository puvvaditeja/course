# Database Design

## Normalization

Normalization is the process of organizing data in a database to reduce redundancy and improve data integrity. It involves decomposing tables into smaller, well-structured tables and defining relationships between them.

### Why Normalize?

**Problems with Unnormalized Data:**

```sql
-- Poor design: Unnormalized table
CREATE TABLE orders_bad (
    order_id INT,
    customer_name VARCHAR(100),
    customer_email VARCHAR(100),
    customer_phone VARCHAR(15),
    product1_name VARCHAR(100),
    product1_price DECIMAL(10,2),
    product2_name VARCHAR(100),
    product2_price DECIMAL(10,2),
    product3_name VARCHAR(100),
    product3_price DECIMAL(10,2)
);
```

**Issues:**
- Data redundancy (customer info repeated)
- Update anomalies (change email in multiple places)
- Insert anomalies (can't add customer without order)
- Delete anomalies (deleting order loses customer info)
- Limited products (only 3 products per order)

### Normal Forms

Normalization is achieved through a series of normal forms (NF):

```
Unnormalized
    ↓
1NF (First Normal Form)
    ↓
2NF (Second Normal Form)
    ↓
3NF (Third Normal Form)
    ↓
BCNF (Boyce-Codd Normal Form)
    ↓
4NF, 5NF (Advanced forms)
```

---

### First Normal Form (1NF)

**Rules:**
1. Each column contains atomic (indivisible) values
2. Each column contains values of a single type
3. Each row is unique (has a primary key)
4. No repeating groups or arrays

**Before 1NF:**
```
orders
┌──────────┬───────────────┬──────────────────────────┐
│ order_id │ customer_name │       products           │
├──────────┼───────────────┼──────────────────────────┤
│    1     │   John Doe    │ Laptop, Mouse, Keyboard  │  ❌ Multiple values
│    2     │   Jane Smith  │ Phone                    │
└──────────┴───────────────┴──────────────────────────┘
```

**After 1NF:**
```
orders
┌──────────┬───────────────┬──────────────┐
│ order_id │ customer_name │   product    │
├──────────┼───────────────┼──────────────┤
│    1     │   John Doe    │   Laptop     │  ✓ Atomic values
│    1     │   John Doe    │   Mouse      │
│    1     │   John Doe    │   Keyboard   │
│    2     │   Jane Smith  │   Phone      │
└──────────┴───────────────┴──────────────┘
```

**SQL Example:**
```sql
-- Violates 1NF
CREATE TABLE bad_students (
    student_id INT PRIMARY KEY,
    name VARCHAR(100),
    phone_numbers VARCHAR(255)  -- Multiple phone numbers in one field
);

-- Conforms to 1NF
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE student_phones (
    phone_id INT PRIMARY KEY AUTO_INCREMENT,
    student_id INT,
    phone_number VARCHAR(15),
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

---

### Second Normal Form (2NF)

**Rules:**
1. Must be in 1NF
2. No partial dependencies (all non-key attributes fully depend on the primary key)

**Applies to:** Tables with composite primary keys

**Before 2NF:**
```
order_items (order_id + product_id = Composite Primary Key)
┌──────────┬────────────┬──────────────┬───────────────┬──────────┐
│ order_id │ product_id │ product_name │ product_price │ quantity │
├──────────┼────────────┼──────────────┼───────────────┼──────────┤
│    1     │    101     │   Laptop     │    800.00     │    2     │
│    1     │    102     │   Mouse      │     25.00     │    1     │
└──────────┴────────────┴──────────────┴───────────────┴──────────┘
           ↑             ↑              ↑
           |             |              |
    Composite PK    Depends only    Depends only
                    on product_id   on product_id
                    (Partial dependency ❌)
```

**After 2NF:**
```
order_items                          products
┌──────────┬────────────┬──────────┐  ┌────────────┬──────────────┬───────────────┐
│ order_id │ product_id │ quantity │  │ product_id │ product_name │ product_price │
├──────────┼────────────┼──────────┤  ├────────────┼──────────────┼───────────────┤
│    1     │    101     │    2     │  │    101     │   Laptop     │    800.00     │
│    1     │    102     │    1     │  │    102     │   Mouse      │     25.00     │
└──────────┴────────────┴──────────┘  └────────────┴──────────────┴───────────────┘
                                                ✓ No partial dependencies
```

**SQL Example:**
```sql
-- Violates 2NF
CREATE TABLE course_enrollment (
    student_id INT,
    course_id INT,
    course_name VARCHAR(100),      -- Depends only on course_id
    course_credits INT,             -- Depends only on course_id
    enrollment_date DATE,           -- Depends on both
    PRIMARY KEY (student_id, course_id)
);

-- Conforms to 2NF
CREATE TABLE enrollments (
    student_id INT,
    course_id INT,
    enrollment_date DATE,
    PRIMARY KEY (student_id, course_id),
    FOREIGN KEY (student_id) REFERENCES students(student_id),
    FOREIGN KEY (course_id) REFERENCES courses(course_id)
);

CREATE TABLE courses (
    course_id INT PRIMARY KEY,
    course_name VARCHAR(100),
    course_credits INT
);
```

---

### Third Normal Form (3NF)

**Rules:**
1. Must be in 2NF
2. No transitive dependencies (non-key attributes don't depend on other non-key attributes)

**Before 3NF:**
```
employees
┌─────────────┬──────────┬───────────────┬──────────────────┐
│ employee_id │   name   │ department_id │ department_name  │
├─────────────┼──────────┼───────────────┼──────────────────┤
│     1       │   John   │      10       │   Engineering    │
│     2       │   Jane   │      10       │   Engineering    │
│     3       │   Bob    │      20       │      Sales       │
└─────────────┴──────────┴───────────────┴──────────────────┘
                              ↑                  ↑
                              └──────────────────┘
                          Transitive dependency ❌
                    (department_name depends on department_id,
                     not directly on employee_id)
```

**After 3NF:**
```
employees                          departments
┌─────────────┬──────────┬───────────────┐  ┌───────────────┬──────────────────┐
│ employee_id │   name   │ department_id │  │ department_id │ department_name  │
├─────────────┼──────────┼───────────────┤  ├───────────────┼──────────────────┤
│     1       │   John   │      10       │  │      10       │   Engineering    │
│     2       │   Jane   │      10       │  │      20       │      Sales       │
│     3       │   Bob    │      20       │  └───────────────┴──────────────────┘
└─────────────┴──────────┴───────────────┘
                    ✓ No transitive dependencies
```

**SQL Example:**
```sql
-- Violates 3NF
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    customer_name VARCHAR(100),     -- Depends on customer_id
    customer_email VARCHAR(100),    -- Depends on customer_id
    order_date DATE,
    total_amount DECIMAL(10,2)
);

-- Conforms to 3NF
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    total_amount DECIMAL(10,2),
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    customer_name VARCHAR(100),
    customer_email VARCHAR(100)
);
```

---

### Boyce-Codd Normal Form (BCNF)

**Rules:**
1. Must be in 3NF
2. For every functional dependency X → Y, X must be a super key

BCNF is a stricter version of 3NF. Most tables in 3NF are also in BCNF.

**When to Use Each Normal Form:**

| Normal Form | Use Case | Trade-off |
|-------------|----------|-----------|
| **1NF** | Minimum requirement | Basic structure |
| **2NF** | Tables with composite keys | Eliminates partial dependencies |
| **3NF** | Most business applications | Good balance of normalization vs performance |
| **BCNF** | Strict data integrity needs | May require more tables |

### Denormalization

Sometimes, intentionally violating normalization rules improves performance:

**When to Denormalize:**
- Read-heavy applications (reporting, analytics)
- Performance is critical
- Reduce complex joins
- Data warehouse scenarios

**Example:**
```sql
-- Normalized (3NF)
SELECT o.order_id, c.customer_name, c.email
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id;

-- Denormalized (faster reads, but redundant data)
SELECT order_id, customer_name, email
FROM orders_denormalized;
```

---

## Multiplicity

Multiplicity (also called cardinality) defines the numeric relationship between entities in a database. It describes how many instances of one entity can be associated with instances of another entity.

### Types of Relationships

#### 1. One-to-One (1:1)

One record in Table A relates to exactly one record in Table B.

**Example:** User and User Profile

```
users                    user_profiles
┌─────────┬──────────┐   ┌─────────┬────────────┬─────────┐
│ user_id │ username │   │ user_id │    bio     │ avatar  │
├─────────┼──────────┤   ├─────────┼────────────┼─────────┤
│    1    │  john    │━━━│    1    │  Bio text  │ img.jpg │
│    2    │  jane    │━━━│    2    │  Bio text  │ pic.png │
└─────────┴──────────┘   └─────────┴────────────┴─────────┘
        One user has one profile
```

**SQL Implementation:**
```sql
CREATE TABLE users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL
);

CREATE TABLE user_profiles (
    user_id INT PRIMARY KEY,  -- PK and FK together enforces 1:1
    bio TEXT,
    avatar VARCHAR(255),
    date_of_birth DATE,
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE
);
```

---

#### 2. One-to-Many (1:N)

One record in Table A can relate to multiple records in Table B.

**Example:** Customer and Orders

```
customers                orders
┌─────────────┬──────┐   ┌──────────┬─────────────┬────────┐
│ customer_id │ name │   │ order_id │ customer_id │ amount │
├─────────────┼──────┤   ├──────────┼─────────────┼────────┤
│      1      │ John │━┳━│    101   │      1      │  50.00 │
│      2      │ Jane │ ┃ │    102   │      1      │  75.00 │
└─────────────┴──────┘ ┃ │    103   │      1      │  30.00 │
                       ┗━│    104   │      2      │ 100.00 │
                         └──────────┴─────────────┴────────┘
                    One customer has many orders
```

**SQL Implementation:**
```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    total_amount DECIMAL(10,2),
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);
```

---

#### 3. Many-to-Many (M:N)

Multiple records in Table A can relate to multiple records in Table B.

**Example:** Students and Courses

```
students                 enrollments              courses
┌────────────┬──────┐   ┌────────────┬───────────┐   ┌───────────┬─────────────┐
│ student_id │ name │   │ student_id │ course_id │   │ course_id │ course_name │
├────────────┼──────┤   ├────────────┼───────────┤   ├───────────┼─────────────┤
│     1      │ John │━┳━│     1      │    101    │━┳━│    101    │   Math      │
│     2      │ Jane │ ┃ │     1      │    102    │ ┃ │    102    │   Science   │
└────────────┴──────┘ ┃ │     2      │    101    │━┛ │    103    │   History   │
                      ┗━│     2      │    103    │━━━│           │             │
                        └────────────┴───────────┘   └───────────┴─────────────┘
                        Junction/Bridge/Associative Table
```

**SQL Implementation:**
```sql
CREATE TABLE students (
    student_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE
);

CREATE TABLE courses (
    course_id INT PRIMARY KEY AUTO_INCREMENT,
    course_name VARCHAR(100) NOT NULL,
    credits INT
);

-- Junction/Bridge table
CREATE TABLE enrollments (
    enrollment_id INT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    course_id INT NOT NULL,
    enrollment_date DATE,
    grade VARCHAR(2),
    FOREIGN KEY (student_id) REFERENCES students(student_id),
    FOREIGN KEY (course_id) REFERENCES courses(course_id),
    UNIQUE KEY (student_id, course_id)  -- Prevent duplicate enrollments
);
```

---

### Multiplicity Notation

```
Notation Types:

Crow's Foot Notation:
│  = One
├┤ = One or Many
○  = Zero
│○ = Zero or One
├<┤ = Many

Example:
Customer ├──── Orders    (One customer has many orders)
                ├<┤
Student ├<┤────├<┤ Course (Many students, many courses)
```

### Choosing the Right Relationship

| Relationship | Use When | Example |
|--------------|----------|---------|
| **One-to-One** | Extending a table with optional data | User → Profile |
| **One-to-Many** | Most common relationship | Customer → Orders |
| **Many-to-Many** | Entities can have multiple of each other | Students ↔ Courses |

---

## Data Modeling And ERD

Entity-Relationship Diagram (ERD) is a visual representation of database structure, showing entities (tables), attributes (columns), and relationships.

### ERD Components

```
┌──────────────────┐
│   ENTITY NAME    │  ← Entity (Table)
├──────────────────┤
│ PK attribute_id  │  ← Primary Key (PK)
│    attribute1    │  ← Regular Attribute
│    attribute2    │
│ FK other_id      │  ← Foreign Key (FK)
└──────────────────┘
```

### ERD Symbols

```
Primary Key (PK):  Underlined or marked with PK
Foreign Key (FK):  Marked with FK
Required:          Solid line
Optional:          Dashed line

Relationships:
─────        One
─────<       Many
○────        Optional (Zero or One)
●────        Mandatory (Exactly One)
```

---

### Complete ERD Example: E-Commerce System

```
┌─────────────────────┐                ┌──────────────────────┐
│     CUSTOMERS       │                │       ADDRESSES      │
├─────────────────────┤                ├──────────────────────┤
│ PK customer_id      │                │ PK address_id        │
│    first_name       │                │ FK customer_id       │
│    last_name        │                │    street            │
│    email            │                │    city              │
│    phone            │                │    state             │
│    created_at       │                │    zip_code          │
└─────────────────────┘                │    country           │
         │ 1                           │    address_type      │
         │                             └──────────────────────┘
         │                                      ▲
         │ has many                             │
         │                                      │ belongs to
         ▼ *                                    │ 1
┌─────────────────────┐                         │
│       ORDERS        │─────────────────────────┘
├─────────────────────┤
│ PK order_id         │
│ FK customer_id      │
│ FK shipping_addr_id │
│ FK billing_addr_id  │
│    order_date       │
│    status           │
│    total_amount     │
│    created_at       │
└─────────────────────┘
         │ 1
         │
         │ contains
         │
         ▼ *
┌─────────────────────┐         ┌──────────────────────┐
│    ORDER_ITEMS      │         │      PRODUCTS        │
├─────────────────────┤         ├──────────────────────┤
│ PK order_item_id    │         │ PK product_id        │
│ FK order_id         │*  refers│ FK category_id       │
│ FK product_id       │────────>│    product_name      │
│    quantity         │      1  │    description       │
│    unit_price       │         │    price             │
│    subtotal         │         │    stock_quantity    │
└─────────────────────┘         │    image_url         │
                                │    created_at        │
                                └──────────────────────┘
                                         │ *
                                         │
                                         │ belongs to
                                         │
                                         ▼ 1
                                ┌──────────────────────┐
                                │     CATEGORIES       │
                                ├──────────────────────┤
                                │ PK category_id       │
                                │    category_name     │
                                │    description       │
                                └──────────────────────┘
```

**Relationship Summary:**
- **Customer → Addresses**: 1:N (one customer, many addresses)
- **Customer → Orders**: 1:N (one customer, many orders)
- **Order → Order Items**: 1:N (one order, many items)
- **Product → Order Items**: 1:N (one product in many order items)
- **Category → Products**: 1:N (one category, many products)
- **Order → Address**: M:1 (many orders, one shipping address)

---

### SQL Implementation of ERD

```sql
-- Categories table
CREATE TABLE categories (
    category_id INT PRIMARY KEY AUTO_INCREMENT,
    category_name VARCHAR(100) NOT NULL UNIQUE,
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Customers table
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    phone VARCHAR(15),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Addresses table
CREATE TABLE addresses (
    address_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    street VARCHAR(255) NOT NULL,
    city VARCHAR(100) NOT NULL,
    state VARCHAR(50) NOT NULL,
    zip_code VARCHAR(10) NOT NULL,
    country VARCHAR(50) NOT NULL,
    address_type ENUM('billing', 'shipping', 'both') DEFAULT 'shipping',
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id) ON DELETE CASCADE
);

-- Products table
CREATE TABLE products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    category_id INT,
    product_name VARCHAR(100) NOT NULL,
    description TEXT,
    price DECIMAL(10, 2) NOT NULL,
    stock_quantity INT DEFAULT 0,
    image_url VARCHAR(255),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (category_id) REFERENCES categories(category_id) ON DELETE SET NULL
);

-- Orders table
CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    shipping_address_id INT,
    billing_address_id INT,
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    status ENUM('pending', 'processing', 'shipped', 'delivered', 'cancelled') DEFAULT 'pending',
    total_amount DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id),
    FOREIGN KEY (shipping_address_id) REFERENCES addresses(address_id),
    FOREIGN KEY (billing_address_id) REFERENCES addresses(address_id)
);

-- Order items table (junction for orders and products)
CREATE TABLE order_items (
    order_item_id INT PRIMARY KEY AUTO_INCREMENT,
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL CHECK (quantity > 0),
    unit_price DECIMAL(10, 2) NOT NULL,
    subtotal DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (order_id) REFERENCES orders(order_id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

---

### ERD Design Process

**Step 1: Identify Entities**
- What are the main objects? (Customer, Product, Order)

**Step 2: Identify Attributes**
- What properties does each entity have?

**Step 3: Identify Relationships**
- How are entities related?
- What is the multiplicity?

**Step 4: Define Primary Keys**
- Unique identifier for each entity

**Step 5: Define Foreign Keys**
- Implement relationships

**Step 6: Apply Normalization**
- Ensure data integrity and reduce redundancy

**Step 7: Add Constraints**
- NOT NULL, UNIQUE, CHECK, DEFAULT

---

### Best Practices

1. **Use Meaningful Names**: `customer_id` not `cid`
2. **Consistent Naming**: Pick a convention (snake_case or camelCase)
3. **Primary Keys**: Always use surrogate keys (auto-increment IDs)
4. **Foreign Keys**: Name clearly: `customer_id`, not `c_id`
5. **Normalize to 3NF**: Good balance for most applications
6. **Use Constraints**: Enforce business rules in database
7. **Index Foreign Keys**: Improve join performance
8. **Document**: Keep ERD updated with schema changes

---

## Summary

| Concept | Key Points |
|---------|------------|
| **Normalization** | Organize data to reduce redundancy: 1NF → 2NF → 3NF → BCNF |
| **1NF** | Atomic values, no repeating groups, primary key exists |
| **2NF** | 1NF + no partial dependencies on composite keys |
| **3NF** | 2NF + no transitive dependencies (non-key → non-key) |
| **Multiplicity** | 1:1 (User-Profile), 1:N (Customer-Orders), M:N (Student-Course) |
| **ERD** | Visual database design: entities, attributes, relationships |
| **Junction Tables** | Implement many-to-many relationships |

## Next Topic

Continue to [DDL and Constraints](./03-ddl-and-constraints.md) to learn about creating and modifying database structures with constraints.
