# Week 03 - Interview FAQ

This document contains frequently asked interview questions and comprehensive answers for Week 03 topics: MySQL and Core Java.

---

## Table of Contents

1. [MySQL Fundamentals](#mysql-fundamentals)
2. [SQL Queries](#sql-queries)
3. [Database Design](#database-design)
4. [Java Fundamentals](#java-fundamentals)
5. [Object-Oriented Programming](#object-oriented-programming)
6. [Java Collections & Streams](#java-collections--streams)

---

## MySQL Fundamentals

### Q1: What is MySQL? How is it different from SQL?

**Answer:**

**MySQL** is a relational database management system (RDBMS) that stores and manages data. **SQL** (Structured Query Language) is the language used to interact with databases.

**Key differences:**

| Aspect | SQL | MySQL |
|--------|-----|-------|
| Type | Query language | Database software |
| Purpose | Write queries | Store and manage data |
| Vendor | ANSI standard | Oracle Corporation |
| Example | `SELECT * FROM users` | The server that executes this query |

**Why MySQL is popular:**
- Open source (free to use)
- High performance and reliability
- Wide community support
- Cross-platform compatibility
- Used by major companies (Facebook, Twitter, YouTube)

---

### Q2: Explain the different types of SQL commands (DDL, DML, DQL, DCL, TCL).

**Answer:**

SQL commands are categorized by their purpose:

**DDL (Data Definition Language):**
- Define and modify database structure
- Commands: CREATE, ALTER, DROP, TRUNCATE
```sql
CREATE TABLE users (id INT PRIMARY KEY, name VARCHAR(100));
ALTER TABLE users ADD email VARCHAR(100);
DROP TABLE users;
```

**DML (Data Manipulation Language):**
- Modify data in tables
- Commands: INSERT, UPDATE, DELETE
```sql
INSERT INTO users VALUES (1, 'John');
UPDATE users SET name = 'Jane' WHERE id = 1;
DELETE FROM users WHERE id = 1;
```

**DQL (Data Query Language):**
- Retrieve data from database
- Command: SELECT
```sql
SELECT * FROM users WHERE status = 'active';
```

**DCL (Data Control Language):**
- Control access permissions
- Commands: GRANT, REVOKE
```sql
GRANT SELECT ON users TO 'username';
REVOKE INSERT ON users FROM 'username';
```

**TCL (Transaction Control Language):**
- Manage transactions
- Commands: COMMIT, ROLLBACK, SAVEPOINT
```sql
START TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT;
```

---

### Q3: What are constraints in MySQL? Explain different types.

**Answer:**

**Constraints** enforce rules on data to maintain integrity.

| Constraint | Purpose | Example |
|------------|---------|---------|
| PRIMARY KEY | Unique identifier, not null | `id INT PRIMARY KEY` |
| FOREIGN KEY | Reference to another table | `FOREIGN KEY (user_id) REFERENCES users(id)` |
| UNIQUE | No duplicate values | `email VARCHAR(100) UNIQUE` |
| NOT NULL | No null values allowed | `name VARCHAR(50) NOT NULL` |
| CHECK | Validate against condition | `CHECK (age >= 18)` |
| DEFAULT | Default value if not provided | `status VARCHAR(20) DEFAULT 'active'` |

**Example with multiple constraints:**
```sql
CREATE TABLE employees (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    department_id INT,
    salary DECIMAL(10,2) CHECK (salary > 0),
    hire_date DATE DEFAULT (CURRENT_DATE),
    FOREIGN KEY (department_id) REFERENCES departments(id)
);
```

---

### Q4: What is the difference between TRUNCATE and DELETE?

**Answer:**

| Feature | DELETE | TRUNCATE |
|---------|--------|----------|
| Type | DML | DDL |
| WHERE clause | Supported | Not supported |
| Transaction | Can be rolled back | Cannot be rolled back (auto-commit) |
| Speed | Slower (row by row) | Faster (deallocates pages) |
| Triggers | Fires DELETE triggers | Does not fire triggers |
| Identity reset | No | Yes (resets AUTO_INCREMENT) |
| Logging | Logs each row | Minimal logging |

**When to use:**
```sql
-- DELETE: Remove specific rows
DELETE FROM orders WHERE status = 'cancelled';

-- TRUNCATE: Remove all rows quickly
TRUNCATE TABLE logs;  -- Faster than DELETE FROM logs
```

**Key point:** Use DELETE when you need to remove specific rows or want transaction control. Use TRUNCATE for clearing entire tables quickly.

---

### Q5: Explain the different types of JOINs with examples.

**Answer:**

JOINs combine rows from multiple tables based on related columns.

**INNER JOIN** - Returns matching rows only:
```sql
SELECT u.name, o.total
FROM users u
INNER JOIN orders o ON u.id = o.user_id;
-- Only users who have orders
```

**LEFT JOIN** - All left rows + matching right rows:
```sql
SELECT u.name, o.total
FROM users u
LEFT JOIN orders o ON u.id = o.user_id;
-- All users, even those without orders (NULL for orders)
```

**RIGHT JOIN** - All right rows + matching left rows:
```sql
SELECT u.name, o.total
FROM users u
RIGHT JOIN orders o ON u.id = o.user_id;
-- All orders, even if user is missing
```

**CROSS JOIN** - Cartesian product (all combinations):
```sql
SELECT colors.name, sizes.name
FROM colors
CROSS JOIN sizes;
-- Every color with every size
```

**SELF JOIN** - Table joined with itself:
```sql
SELECT e.name AS employee, m.name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;
```

**Visual representation:**
```
INNER: Only intersection
LEFT:  All left + intersection
RIGHT: All right + intersection
FULL:  Both tables (MySQL uses UNION)
```

---

## SQL Queries

### Q6: What is the difference between WHERE and HAVING?

**Answer:**

| Aspect | WHERE | HAVING |
|--------|-------|--------|
| Filters | Individual rows | Grouped results |
| Timing | Before GROUP BY | After GROUP BY |
| Aggregates | Cannot use | Can use |
| Use case | Filter source data | Filter aggregated data |

**Example:**
```sql
-- WHERE filters rows before grouping
SELECT department, AVG(salary) as avg_salary
FROM employees
WHERE status = 'active'           -- Filter: only active employees
GROUP BY department
HAVING AVG(salary) > 50000;       -- Filter: groups with high avg salary
```

**Execution order:**
1. FROM (get tables)
2. WHERE (filter rows)
3. GROUP BY (create groups)
4. HAVING (filter groups)
5. SELECT (choose columns)
6. ORDER BY (sort results)

---

### Q7: Explain subqueries and their types.

**Answer:**

A **subquery** is a query nested inside another query.

**Scalar subquery** (returns single value):
```sql
SELECT name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

**Column subquery** (returns single column):
```sql
SELECT * FROM users
WHERE id IN (SELECT DISTINCT user_id FROM orders);
```

**Row subquery** (returns single row):
```sql
SELECT * FROM products
WHERE (category_id, price) = (
    SELECT category_id, MAX(price) FROM products
);
```

**Table subquery** (returns table - used in FROM):
```sql
SELECT dept, avg_sal
FROM (
    SELECT department AS dept, AVG(salary) AS avg_sal
    FROM employees
    GROUP BY department
) AS dept_averages
WHERE avg_sal > 50000;
```

**Correlated subquery** (references outer query):
```sql
SELECT * FROM employees e
WHERE salary > (
    SELECT AVG(salary)
    FROM employees
    WHERE department = e.department
);
```

---

### Q8: What are aggregate functions? Give examples.

**Answer:**

**Aggregate functions** perform calculations on multiple rows and return a single value.

| Function | Purpose | Example |
|----------|---------|---------|
| COUNT() | Count rows | `COUNT(*)`, `COUNT(column)` |
| SUM() | Sum values | `SUM(quantity)` |
| AVG() | Average | `AVG(price)` |
| MIN() | Minimum | `MIN(created_at)` |
| MAX() | Maximum | `MAX(salary)` |

**Examples:**
```sql
-- Basic aggregates
SELECT
    COUNT(*) AS total_orders,
    SUM(amount) AS total_revenue,
    AVG(amount) AS avg_order_value,
    MIN(amount) AS smallest_order,
    MAX(amount) AS largest_order
FROM orders;

-- With GROUP BY
SELECT
    category,
    COUNT(*) AS products,
    AVG(price) AS avg_price
FROM products
GROUP BY category
HAVING COUNT(*) > 5;

-- COUNT variations
SELECT
    COUNT(*) AS all_rows,          -- Counts all rows
    COUNT(email) AS has_email,     -- Counts non-null emails
    COUNT(DISTINCT city) AS cities -- Counts unique cities
FROM users;
```

---

### Q9: What is ACID? Why is it important for databases?

**Answer:**

**ACID** properties ensure reliable database transactions:

| Property | Description | Example |
|----------|-------------|---------|
| **A**tomicity | All or nothing | Transfer: debit AND credit both succeed or both fail |
| **C**onsistency | Valid state to valid state | Balance constraints maintained |
| **I**solation | Transactions don't interfere | Concurrent transfers don't mix |
| **D**urability | Committed data persists | Survives system crashes |

**Transaction example:**
```sql
START TRANSACTION;

-- Debit from account A
UPDATE accounts SET balance = balance - 500 WHERE id = 1;

-- Credit to account B
UPDATE accounts SET balance = balance + 500 WHERE id = 2;

-- If both succeed
COMMIT;

-- If any fails
-- ROLLBACK;
```

**Why ACID matters:**
- **Banking**: Money transfers must be complete
- **Inventory**: Stock updates must be accurate
- **E-commerce**: Orders and payments must be consistent

---

### Q10: What are indexes? How do they improve performance?

**Answer:**

An **index** is a data structure that improves query speed by providing quick lookups.

**How indexes work:**
```
Without index:  Full table scan (check every row)
With index:     B-tree lookup (logarithmic time)
```

**Creating indexes:**
```sql
-- Single column index
CREATE INDEX idx_email ON users(email);

-- Composite index
CREATE INDEX idx_name_city ON users(last_name, city);

-- Unique index
CREATE UNIQUE INDEX idx_username ON users(username);
```

**When to use indexes:**
- Columns in WHERE clauses
- Columns in JOIN conditions
- Columns in ORDER BY
- Columns with high selectivity (many unique values)

**When NOT to index:**
- Small tables
- Columns with few unique values
- Frequently updated columns
- Columns rarely used in queries

**Trade-offs:**
- **Pros**: Faster reads, faster sorts
- **Cons**: Slower writes (index maintenance), storage space

---

## Database Design

### Q11: What is normalization? Explain the normal forms.

**Answer:**

**Normalization** organizes data to reduce redundancy and improve integrity.

**First Normal Form (1NF):**
- Each cell contains atomic (single) values
- Each column has unique name
- Order doesn't matter

```
Before: Phone: "123-456, 789-012"
After:  Phone table with separate rows
```

**Second Normal Form (2NF):**
- Must be in 1NF
- All non-key columns depend on the entire primary key

```
Before: OrderItems(OrderID, ProductID, ProductName, Quantity)
After:  Separate Products table for ProductName
```

**Third Normal Form (3NF):**
- Must be in 2NF
- No transitive dependencies (A → B → C)

```
Before: Employees(ID, DeptID, DeptName, DeptLocation)
After:  Departments table for DeptName, DeptLocation
```

**Benefits of normalization:**
- Eliminates data redundancy
- Reduces update anomalies
- Improves data integrity
- Saves storage space

**When to denormalize:**
- Read-heavy applications
- Reporting databases
- When joins become too expensive

---

### Q12: What are primary keys and foreign keys? How do they work together?

**Answer:**

**Primary Key:**
- Uniquely identifies each row
- Cannot be NULL
- Only one per table
- Automatically creates an index

**Foreign Key:**
- References a primary key in another table
- Establishes relationships between tables
- Can be NULL (optional relationship)
- Enforces referential integrity

**Example:**
```sql
-- Parent table
CREATE TABLE departments (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL
);

-- Child table
CREATE TABLE employees (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    department_id INT,
    FOREIGN KEY (department_id) REFERENCES departments(id)
        ON DELETE SET NULL
        ON UPDATE CASCADE
);
```

**Referential actions:**

| Action | ON DELETE | ON UPDATE |
|--------|-----------|-----------|
| CASCADE | Delete child rows | Update child values |
| SET NULL | Set FK to NULL | Set FK to NULL |
| RESTRICT | Prevent deletion | Prevent update |
| NO ACTION | Same as RESTRICT | Same as RESTRICT |

---

## Java Fundamentals

### Q13: What is the difference between JDK, JRE, and JVM?

**Answer:**

| Component | Purpose | Contains |
|-----------|---------|----------|
| JVM | Executes bytecode | Runtime engine |
| JRE | Run Java programs | JVM + libraries |
| JDK | Develop Java programs | JRE + compiler + tools |

**Relationship:**
```
JDK = JRE + Development Tools (javac, javadoc, jar)
JRE = JVM + Standard Libraries (java.lang, java.util)
JVM = Bytecode Interpreter + JIT Compiler + Memory Management
```

**How Java runs:**
```
Source (.java) → Compiler (javac) → Bytecode (.class) → JVM → Machine Code
```

**Platform independence:**
- Bytecode is the same everywhere
- JVM is platform-specific
- "Write Once, Run Anywhere"

---

### Q14: Explain Java data types - primitives vs reference types.

**Answer:**

**Primitive Types** (8 total):

| Type | Size | Default | Range |
|------|------|---------|-------|
| byte | 8-bit | 0 | -128 to 127 |
| short | 16-bit | 0 | -32,768 to 32,767 |
| int | 32-bit | 0 | ~±2.1 billion |
| long | 64-bit | 0L | ~±9.2 quintillion |
| float | 32-bit | 0.0f | ~7 decimal digits |
| double | 64-bit | 0.0d | ~15 decimal digits |
| char | 16-bit | '\u0000' | Unicode characters |
| boolean | 1-bit | false | true/false |

**Reference Types:**
- Classes, interfaces, arrays, enums
- Store memory address (reference)
- Default value: null
- Examples: String, ArrayList, Object

**Key differences:**

| Aspect | Primitive | Reference |
|--------|-----------|-----------|
| Storage | Stack (value) | Heap (object) |
| Default | Type-specific (0, false) | null |
| Comparison | By value (==) | By reference (== for address, equals() for content) |
| Nullable | No | Yes |

**Wrapper classes:**
```java
// Primitives ↔ Wrappers
int n = 5;
Integer num = Integer.valueOf(n);  // Boxing
int back = num.intValue();         // Unboxing

// Autoboxing/Unboxing (automatic)
Integer auto = 10;    // Autoboxing
int prim = auto;      // Auto-unboxing
```

---

### Q15: What is the difference between `==` and `equals()` in Java?

**Answer:**

**`==` (equality operator):**
- For primitives: compares values
- For objects: compares references (memory addresses)

**`equals()` method:**
- Compares object content (when properly overridden)
- Defined in Object class
- Must override for custom classes

**Examples:**
```java
// Primitives - use ==
int a = 5;
int b = 5;
System.out.println(a == b);  // true

// Strings
String s1 = "hello";
String s2 = "hello";
String s3 = new String("hello");

System.out.println(s1 == s2);       // true (string pool)
System.out.println(s1 == s3);       // false (different objects)
System.out.println(s1.equals(s3));  // true (same content)

// Custom objects
Person p1 = new Person("John");
Person p2 = new Person("John");
System.out.println(p1 == p2);       // false (different objects)
System.out.println(p1.equals(p2));  // depends on equals() implementation
```

**Proper equals() override:**
```java
@Override
public boolean equals(Object obj) {
    if (this == obj) return true;
    if (obj == null || getClass() != obj.getClass()) return false;
    Person other = (Person) obj;
    return Objects.equals(name, other.name);
}
```

---

### Q16: What are access modifiers in Java? Explain their scope.

**Answer:**

| Modifier | Class | Package | Subclass | World |
|----------|-------|---------|----------|-------|
| public | ✓ | ✓ | ✓ | ✓ |
| protected | ✓ | ✓ | ✓ | ✗ |
| (default) | ✓ | ✓ | ✗ | ✗ |
| private | ✓ | ✗ | ✗ | ✗ |

**Usage guidelines:**

```java
public class Example {
    // Private: internal implementation
    private int internalData;

    // Default: package-level access
    int packageData;

    // Protected: for inheritance
    protected int inheritableData;

    // Public: API for everyone
    public int getData() {
        return internalData;
    }
}
```

**Best practices:**
- Start with private, increase as needed
- Use public only for APIs
- Use protected for inheritance hooks
- Default is often sufficient for package-internal classes

---

## Object-Oriented Programming

### Q17: Explain the four pillars of OOP with examples.

**Answer:**

**1. Encapsulation** - Bundling data with methods, hiding internals:
```java
public class BankAccount {
    private double balance;  // Hidden

    public void deposit(double amount) {  // Controlled access
        if (amount > 0) balance += amount;
    }

    public double getBalance() {
        return balance;
    }
}
```

**2. Inheritance** - Creating new classes from existing ones:
```java
public class Animal {
    protected String name;
    public void eat() { System.out.println("Eating"); }
}

public class Dog extends Animal {
    public void bark() { System.out.println("Woof!"); }

    @Override
    public void eat() { System.out.println("Dog eating"); }
}
```

**3. Polymorphism** - Many forms:
```java
// Compile-time (overloading)
public int add(int a, int b) { return a + b; }
public double add(double a, double b) { return a + b; }

// Runtime (overriding)
Animal animal = new Dog();
animal.eat();  // Calls Dog's eat()
```

**4. Abstraction** - Hiding complexity:
```java
public abstract class Shape {
    public abstract double area();  // What, not how
}

public interface Drawable {
    void draw();  // Contract
}

public class Circle extends Shape implements Drawable {
    private double radius;

    @Override
    public double area() { return Math.PI * radius * radius; }

    @Override
    public void draw() { /* implementation */ }
}
```

---

### Q18: What is the difference between abstract classes and interfaces?

**Answer:**

| Feature | Abstract Class | Interface |
|---------|---------------|-----------|
| Methods | Abstract + concrete | Abstract (+ default since Java 8) |
| Variables | Any type | public static final only |
| Constructors | Yes | No |
| Multiple inheritance | No (single extends) | Yes (multiple implements) |
| Access modifiers | Any | public only (methods) |

**When to use:**

**Abstract class** - "is-a" relationship with shared code:
```java
public abstract class Vehicle {
    protected String brand;

    public Vehicle(String brand) {
        this.brand = brand;
    }

    public void start() {
        System.out.println("Starting " + brand);
    }

    public abstract void drive();  // Must implement
}
```

**Interface** - "can-do" capability:
```java
public interface Flyable {
    void fly();

    default void land() {
        System.out.println("Landing...");
    }
}

public class Bird extends Animal implements Flyable {
    @Override
    public void fly() { /* implementation */ }
}
```

**Java 8+ interface features:**
- Default methods (with implementation)
- Static methods
- Private methods (Java 9+)

---

### Q19: Explain exception handling in Java.

**Answer:**

**Exception hierarchy:**
```
Throwable
├── Error (don't catch: OutOfMemoryError, StackOverflowError)
└── Exception
    ├── RuntimeException (unchecked: NullPointerException, IndexOutOfBoundsException)
    └── Checked exceptions (must handle: IOException, SQLException)
```

**Basic try-catch-finally:**
```java
try {
    // Risky code
    FileReader file = new FileReader("file.txt");
} catch (FileNotFoundException e) {
    // Handle specific exception
    System.out.println("File not found: " + e.getMessage());
} catch (Exception e) {
    // Handle general exception
    e.printStackTrace();
} finally {
    // Always executes (cleanup)
    System.out.println("Cleanup");
}
```

**Try-with-resources (auto-close):**
```java
try (FileReader reader = new FileReader("file.txt");
     BufferedReader br = new BufferedReader(reader)) {
    String line = br.readLine();
} catch (IOException e) {
    e.printStackTrace();
}
// Resources automatically closed
```

**throw vs throws:**
```java
// throws: declare that method may throw
public void read() throws IOException {
    // ...
}

// throw: actually throw exception
public void validate(int age) {
    if (age < 0) {
        throw new IllegalArgumentException("Age cannot be negative");
    }
}
```

**Custom exceptions:**
```java
public class InsufficientFundsException extends Exception {
    private double amount;

    public InsufficientFundsException(double amount) {
        super("Insufficient funds. Need: " + amount);
        this.amount = amount;
    }
}
```

---

### Q20: What is method overloading vs method overriding?

**Answer:**

**Method Overloading** (compile-time polymorphism):
- Same method name, different parameters
- Same or different return type
- Same class
- Resolved at compile time

```java
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }

    public double add(double a, double b) {
        return a + b;
    }

    public int add(int a, int b, int c) {
        return a + b + c;
    }
}
```

**Method Overriding** (runtime polymorphism):
- Same method signature (name + parameters)
- Same or covariant return type
- Parent-child classes
- Resolved at runtime
- @Override annotation recommended

```java
public class Animal {
    public void makeSound() {
        System.out.println("Some sound");
    }
}

public class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Bark!");
    }
}

// Runtime polymorphism
Animal animal = new Dog();
animal.makeSound();  // Outputs: "Bark!"
```

**Key differences:**

| Aspect | Overloading | Overriding |
|--------|-------------|------------|
| Location | Same class | Different classes (inheritance) |
| Parameters | Must differ | Must be same |
| Return type | Can differ | Same or covariant |
| Static methods | Can overload | Cannot override (hidden) |
| Access modifier | Can change | Cannot reduce visibility |
| Binding | Compile-time | Runtime |

---

## Java Collections & Streams

### Q21: Compare ArrayList vs LinkedList. When would you use each?

**Answer:**

| Operation | ArrayList | LinkedList |
|-----------|-----------|------------|
| Random access (get/set) | O(1) | O(n) |
| Add/remove at end | O(1) amortized | O(1) |
| Add/remove at beginning | O(n) | O(1) |
| Add/remove in middle | O(n) | O(n) - find O(n), remove O(1) |
| Memory | Contiguous | Extra (node pointers) |

**ArrayList** - use when:
- Frequent random access by index
- Mostly adding at end
- Iterating through elements
- Memory efficiency matters

```java
List<String> list = new ArrayList<>();
list.add("A");
list.get(0);  // Fast
```

**LinkedList** - use when:
- Frequent insertions/deletions at beginning
- Implementing queue/deque
- No random access needed

```java
LinkedList<String> list = new LinkedList<>();
list.addFirst("A");  // Fast
list.removeFirst();   // Fast
```

**In practice:** ArrayList is preferred in most cases due to CPU cache efficiency and simpler implementation.

---

### Q22: Explain HashMap. How does it work internally?

**Answer:**

**HashMap** stores key-value pairs using hashing.

**Internal structure:**
- Array of buckets (Node[])
- Each bucket contains a linked list (or tree in Java 8+)
- Keys are hashed to determine bucket index

**How it works:**
```java
Map<String, Integer> map = new HashMap<>();
map.put("apple", 100);

// Internally:
// 1. hash("apple") → hash code
// 2. hash code % array_length → bucket index
// 3. Store entry in that bucket
```

**Key operations:**
```java
map.put("key", value);           // Add/update
map.get("key");                  // Retrieve (O(1) average)
map.containsKey("key");          // Check existence
map.remove("key");               // Remove
map.getOrDefault("key", 0);      // Get with default
```

**Important concepts:**
- **Load factor** (default 0.75): When to resize
- **Collision handling**: Linked list → Tree (8+ collisions)
- **Null handling**: One null key allowed

**equals() and hashCode() contract:**
```java
public class Person {
    private String name;

    @Override
    public boolean equals(Object obj) {
        if (!(obj instanceof Person)) return false;
        return name.equals(((Person) obj).name);
    }

    @Override
    public int hashCode() {
        return name.hashCode();
    }
}
```

**Rule:** If two objects are equal, they must have the same hash code.

---

### Q23: What is the Stream API? Give practical examples.

**Answer:**

**Stream API** enables functional-style operations on collections.

**Key characteristics:**
- Not a data structure (doesn't store data)
- Lazy evaluation
- Can be parallelized
- Single use (cannot reuse)

**Common operations:**

```java
List<Person> people = getpeople();

// Filter
List<Person> adults = people.stream()
    .filter(p -> p.getAge() >= 18)
    .collect(Collectors.toList());

// Map (transform)
List<String> names = people.stream()
    .map(Person::getName)
    .collect(Collectors.toList());

// Sort
List<Person> sorted = people.stream()
    .sorted(Comparator.comparing(Person::getAge))
    .collect(Collectors.toList());

// Find
Optional<Person> first = people.stream()
    .filter(p -> p.getCity().equals("NYC"))
    .findFirst();

// Reduce
int totalAge = people.stream()
    .mapToInt(Person::getAge)
    .sum();

// Group
Map<String, List<Person>> byCity = people.stream()
    .collect(Collectors.groupingBy(Person::getCity));
```

**Pipeline pattern:**
```java
double avgSalary = employees.stream()
    .filter(e -> e.getDepartment().equals("IT"))
    .filter(e -> e.getYearsOfExperience() > 5)
    .mapToDouble(Employee::getSalary)
    .average()
    .orElse(0.0);
```

---

### Q24: What is Optional in Java? How does it help avoid NullPointerException?

**Answer:**

**Optional** is a container that may or may not contain a non-null value.

**Creating Optional:**
```java
Optional<String> opt1 = Optional.of("Hello");        // Must not be null
Optional<String> opt2 = Optional.ofNullable(null);   // Can be null
Optional<String> opt3 = Optional.empty();            // Empty optional
```

**Using Optional safely:**
```java
Optional<User> user = findUserById(id);

// Check and get
if (user.isPresent()) {
    System.out.println(user.get());
}

// Better alternatives
String name = user
    .map(User::getName)
    .orElse("Unknown");

// With default value
User u = user.orElse(new User("Guest"));

// With exception
User u = user.orElseThrow(() -> new UserNotFoundException(id));

// If present, do something
user.ifPresent(u -> System.out.println(u.getName()));
```

**Chaining operations:**
```java
String city = findUser(userId)
    .flatMap(User::getAddress)    // Address is Optional too
    .map(Address::getCity)
    .orElse("Unknown");
```

**Avoid:**
```java
// Don't do this
if (optional.isPresent()) {
    return optional.get();
}
return null;

// Do this instead
return optional.orElse(null);
```

---

### Q25: Explain lambda expressions and functional interfaces.

**Answer:**

**Lambda expression** is a concise way to represent an anonymous function.

**Syntax:**
```java
// Full syntax
(parameters) -> { statements; }

// Simplified
parameter -> expression
() -> expression
(a, b) -> a + b
```

**Examples:**
```java
// Before lambdas
Runnable r = new Runnable() {
    @Override
    public void run() {
        System.out.println("Hello");
    }
};

// With lambda
Runnable r = () -> System.out.println("Hello");

// Comparator
Comparator<String> comp = (s1, s2) -> s1.length() - s2.length();
```

**Functional interface** - interface with exactly one abstract method:
```java
@FunctionalInterface
public interface Calculator {
    int calculate(int a, int b);
}

// Usage
Calculator add = (a, b) -> a + b;
Calculator multiply = (a, b) -> a * b;
```

**Built-in functional interfaces:**

| Interface | Method | Use Case |
|-----------|--------|----------|
| Predicate<T> | test(T) → boolean | Filtering |
| Function<T,R> | apply(T) → R | Transformation |
| Consumer<T> | accept(T) → void | Side effects |
| Supplier<T> | get() → T | Providing values |
| BiFunction<T,U,R> | apply(T,U) → R | Two-arg transformation |

**Method references:**
```java
// Static method
Function<String, Integer> f = Integer::parseInt;

// Instance method
Consumer<String> c = System.out::println;

// Constructor
Supplier<ArrayList<String>> s = ArrayList::new;
```

---

### Q26: What is the difference between String, StringBuilder, and StringBuffer?

**Answer:**

| Feature | String | StringBuilder | StringBuffer |
|---------|--------|---------------|--------------|
| Mutability | Immutable | Mutable | Mutable |
| Thread-safe | Yes (immutable) | No | Yes |
| Performance | Slow for concat | Fast | Slower (synchronized) |
| Memory | New object each change | Same object | Same object |

**String (immutable):**
```java
String s = "Hello";
s = s + " World";  // Creates new String object
// Original "Hello" is unchanged
```

**StringBuilder (mutable, not thread-safe):**
```java
StringBuilder sb = new StringBuilder("Hello");
sb.append(" World");  // Modifies same object
// Result: "Hello World"
```

**StringBuffer (mutable, thread-safe):**
```java
StringBuffer sb = new StringBuffer("Hello");
sb.append(" World");  // Thread-safe modification
```

**When to use:**
- **String**: Small strings, few modifications, thread safety needed
- **StringBuilder**: Many modifications, single-threaded (most common)
- **StringBuffer**: Many modifications, multi-threaded (rare)

**Performance example:**
```java
// Slow - creates many String objects
String result = "";
for (int i = 0; i < 1000; i++) {
    result += i;
}

// Fast - modifies same StringBuilder
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append(i);
}
String result = sb.toString();
```

---

### Q27: What is multithreading? How do you create threads in Java?

**Answer:**

**Multithreading** allows concurrent execution of multiple threads within a process.

**Creating threads:**

**1. Extend Thread class:**
```java
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Thread: " + getName());
    }
}

MyThread thread = new MyThread();
thread.start();
```

**2. Implement Runnable (preferred):**
```java
class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("Running");
    }
}

Thread thread = new Thread(new MyRunnable());
thread.start();

// Lambda version
Thread thread = new Thread(() -> System.out.println("Lambda"));
thread.start();
```

**3. ExecutorService (best practice):**
```java
ExecutorService executor = Executors.newFixedThreadPool(5);

executor.submit(() -> {
    System.out.println("Task 1");
});

executor.shutdown();
```

**Synchronization:**
```java
public class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }
}
```

**Thread lifecycle:**
```
NEW → RUNNABLE → RUNNING → BLOCKED/WAITING → TERMINATED
```

---

### Q28: What are generics in Java? Why are they useful?

**Answer:**

**Generics** provide type parameters for classes, interfaces, and methods.

**Benefits:**
- Type safety at compile time
- Eliminates casting
- Code reusability

**Generic class:**
```java
public class Box<T> {
    private T content;

    public void set(T content) {
        this.content = content;
    }

    public T get() {
        return content;
    }
}

// Usage
Box<String> stringBox = new Box<>();
stringBox.set("Hello");
String s = stringBox.get();  // No casting needed
```

**Generic method:**
```java
public static <T> T getFirst(List<T> list) {
    return list.isEmpty() ? null : list.get(0);
}

// Usage
String first = getFirst(Arrays.asList("a", "b", "c"));
```

**Bounded types:**
```java
// Upper bound - T must extend Number
public <T extends Number> double sum(List<T> numbers) {
    return numbers.stream().mapToDouble(Number::doubleValue).sum();
}

// Wildcards
public void printList(List<?> list) { }              // Unbounded
public void printNumbers(List<? extends Number> list) { }  // Upper bound
public void addNumbers(List<? super Integer> list) { }     // Lower bound
```

---

### Q29: What is the `static` keyword used for in Java?

**Answer:**

**Static** means belonging to the class, not instances.

**Static variable:**
```java
public class Counter {
    static int count = 0;  // Shared by all instances

    public Counter() {
        count++;
    }
}

Counter c1 = new Counter();  // count = 1
Counter c2 = new Counter();  // count = 2
System.out.println(Counter.count);  // Access via class name
```

**Static method:**
```java
public class Math {
    public static int add(int a, int b) {
        return a + b;
    }
}

int result = Math.add(5, 3);  // No instance needed
```

**Static block:**
```java
public class Config {
    static Map<String, String> settings;

    static {
        // Runs once when class loads
        settings = new HashMap<>();
        settings.put("env", "production");
    }
}
```

**Static nested class:**
```java
public class Outer {
    static class Nested {
        // Can be instantiated without Outer instance
    }
}

Outer.Nested nested = new Outer.Nested();
```

**Key points:**
- Cannot use `this` in static context
- Cannot directly access instance members
- Memory allocated once when class loads
- Commonly used for utility methods

---

### Q30: What are the new features in modern Java (Java 8+)?

**Answer:**

**Java 8:**
- Lambda expressions: `(a, b) -> a + b`
- Stream API: `list.stream().filter().map().collect()`
- Optional: `Optional.ofNullable(value)`
- Default methods in interfaces
- java.time API: `LocalDate`, `LocalDateTime`
- Method references: `String::toUpperCase`

**Java 11:**
- var keyword for local variables
- String methods: `isBlank()`, `lines()`, `strip()`
- Files.readString() / writeString()
- HTTP Client API

**Java 14-16:**
- Records: `record Person(String name, int age) {}`
- Pattern matching: `if (obj instanceof String s)`
- Text blocks: `"""multi-line string"""`
- Sealed classes

**Java 17 (LTS):**
- Pattern matching in switch
- Sealed classes (finalized)

**Java 21 (LTS):**
- Virtual threads
- Record patterns
- String templates (preview)
- Sequenced collections

**Example - Modern Java code:**
```java
// Record
public record Person(String name, int age) {}

// Text block
String json = """
    {
        "name": "John",
        "age": 30
    }
    """;

// Pattern matching
if (obj instanceof Person p) {
    System.out.println(p.name());
}

// Switch expression
String result = switch (day) {
    case MONDAY, FRIDAY -> "Work";
    case SATURDAY, SUNDAY -> "Rest";
    default -> "Unknown";
};
```

---

## Quick Reference Tips

### MySQL Commands to Know
```sql
-- DDL
CREATE TABLE, ALTER TABLE, DROP TABLE, TRUNCATE TABLE

-- DML
INSERT INTO, UPDATE, DELETE FROM

-- DQL
SELECT, FROM, WHERE, GROUP BY, HAVING, ORDER BY, LIMIT

-- Joins
INNER JOIN, LEFT JOIN, RIGHT JOIN, CROSS JOIN

-- Aggregates
COUNT, SUM, AVG, MIN, MAX

-- Transactions
START TRANSACTION, COMMIT, ROLLBACK, SAVEPOINT
```

### Java Concepts to Know
- OOP: Encapsulation, Inheritance, Polymorphism, Abstraction
- Exception handling: try-catch-finally, throw/throws
- Collections: List, Set, Map, Queue
- Stream API: filter, map, reduce, collect
- Functional: Lambda, method reference, Optional
- Concurrency: Thread, Runnable, ExecutorService
- Modern features: Records, var, text blocks
