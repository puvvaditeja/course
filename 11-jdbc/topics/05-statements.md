# Simple and Prepared Statements

## Overview

JDBC provides two main types of statements for executing SQL: `Statement` (simple) and `PreparedStatement` (parameterized). Understanding when and how to use each is crucial for writing efficient and secure database code.

---

## Statement (Simple Statement)

`Statement` is used for executing static SQL queries without parameters.

### Creating a Statement

```java
Connection conn = DatabaseUtil.getConnection();
Statement stmt = conn.createStatement();
```

### Executing Queries

#### executeQuery() - For SELECT statements

```java
String sql = "SELECT id, name, salary FROM employees";
ResultSet rs = stmt.executeQuery(sql);

while (rs.next()) {
    int id = rs.getInt("id");
    String name = rs.getString("name");
    double salary = rs.getDouble("salary");
    System.out.println(id + ": " + name + " - $" + salary);
}
```

#### executeUpdate() - For INSERT, UPDATE, DELETE

```java
// INSERT
String insertSql = "INSERT INTO employees (name, salary) VALUES ('John Doe', 50000)";
int rowsInserted = stmt.executeUpdate(insertSql);
System.out.println(rowsInserted + " row(s) inserted");

// UPDATE
String updateSql = "UPDATE employees SET salary = 55000 WHERE name = 'John Doe'";
int rowsUpdated = stmt.executeUpdate(updateSql);
System.out.println(rowsUpdated + " row(s) updated");

// DELETE
String deleteSql = "DELETE FROM employees WHERE salary < 30000";
int rowsDeleted = stmt.executeUpdate(deleteSql);
System.out.println(rowsDeleted + " row(s) deleted");
```

#### execute() - For any SQL

```java
String sql = "SELECT * FROM employees";
boolean hasResultSet = stmt.execute(sql);

if (hasResultSet) {
    ResultSet rs = stmt.getResultSet();
    // Process result set
} else {
    int updateCount = stmt.getUpdateCount();
    System.out.println("Rows affected: " + updateCount);
}
```

### Statement with Generated Keys

```java
String sql = "INSERT INTO employees (name, salary) VALUES ('Jane Doe', 60000)";

// Request generated keys
int rows = stmt.executeUpdate(sql, Statement.RETURN_GENERATED_KEYS);

// Get the generated key
ResultSet generatedKeys = stmt.getGeneratedKeys();
if (generatedKeys.next()) {
    int newId = generatedKeys.getInt(1);
    System.out.println("New employee ID: " + newId);
}
```

### Batch Execution with Statement

```java
Statement stmt = conn.createStatement();

stmt.addBatch("INSERT INTO employees (name, salary) VALUES ('Alice', 50000)");
stmt.addBatch("INSERT INTO employees (name, salary) VALUES ('Bob', 55000)");
stmt.addBatch("INSERT INTO employees (name, salary) VALUES ('Carol', 60000)");

int[] results = stmt.executeBatch();

for (int i = 0; i < results.length; i++) {
    System.out.println("Statement " + (i+1) + ": " + results[i] + " row(s) affected");
}
```

---

## PreparedStatement (Parameterized Statement)

`PreparedStatement` is a precompiled SQL statement that accepts parameters. It's more secure and efficient than `Statement`.

### Why Use PreparedStatement?

| Feature | Statement | PreparedStatement |
|---------|-----------|-------------------|
| SQL Injection | Vulnerable | Protected |
| Performance | Compiled each time | Precompiled once |
| Parameterized | No | Yes |
| Readable Code | String concatenation | Clean parameter binding |
| Batch Support | Basic | Optimized |

### Creating a PreparedStatement

```java
String sql = "SELECT * FROM employees WHERE department = ? AND salary > ?";
PreparedStatement pstmt = conn.prepareStatement(sql);
```

### Setting Parameters

Parameters are set by index (starting at 1):

```java
String sql = "INSERT INTO employees (name, email, salary, hire_date) VALUES (?, ?, ?, ?)";
PreparedStatement pstmt = conn.prepareStatement(sql);

pstmt.setString(1, "John Doe");        // First ?
pstmt.setString(2, "john@example.com"); // Second ?
pstmt.setDouble(3, 55000.00);          // Third ?
pstmt.setDate(4, java.sql.Date.valueOf("2024-01-15")); // Fourth ?

int rowsInserted = pstmt.executeUpdate();
```

### Common setter Methods

| Method | SQL Type | Java Type |
|--------|----------|-----------|
| `setString(int, String)` | VARCHAR, CHAR | String |
| `setInt(int, int)` | INTEGER | int |
| `setLong(int, long)` | BIGINT | long |
| `setDouble(int, double)` | DOUBLE, DECIMAL | double |
| `setFloat(int, float)` | FLOAT, REAL | float |
| `setBoolean(int, boolean)` | BOOLEAN | boolean |
| `setDate(int, Date)` | DATE | java.sql.Date |
| `setTimestamp(int, Timestamp)` | TIMESTAMP | java.sql.Timestamp |
| `setTime(int, Time)` | TIME | java.sql.Time |
| `setNull(int, int)` | Any | null |
| `setObject(int, Object)` | Any | Object |
| `setBytes(int, byte[])` | BLOB, BINARY | byte[] |

### PreparedStatement Examples

#### SELECT with Parameters

```java
public Employee findByEmail(String email) throws SQLException {
    String sql = "SELECT id, name, email, salary FROM employees WHERE email = ?";

    try (Connection conn = DatabaseUtil.getConnection();
         PreparedStatement pstmt = conn.prepareStatement(sql)) {

        pstmt.setString(1, email);

        try (ResultSet rs = pstmt.executeQuery()) {
            if (rs.next()) {
                Employee emp = new Employee();
                emp.setId(rs.getInt("id"));
                emp.setName(rs.getString("name"));
                emp.setEmail(rs.getString("email"));
                emp.setSalary(rs.getDouble("salary"));
                return emp;
            }
        }
    }
    return null;
}
```

#### INSERT with Generated Keys

```java
public int createEmployee(Employee emp) throws SQLException {
    String sql = "INSERT INTO employees (name, email, salary, department) VALUES (?, ?, ?, ?)";

    try (Connection conn = DatabaseUtil.getConnection();
         PreparedStatement pstmt = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS)) {

        pstmt.setString(1, emp.getName());
        pstmt.setString(2, emp.getEmail());
        pstmt.setDouble(3, emp.getSalary());
        pstmt.setString(4, emp.getDepartment());

        int affectedRows = pstmt.executeUpdate();

        if (affectedRows > 0) {
            try (ResultSet generatedKeys = pstmt.getGeneratedKeys()) {
                if (generatedKeys.next()) {
                    return generatedKeys.getInt(1);
                }
            }
        }
    }
    return -1;
}
```

#### UPDATE with Parameters

```java
public boolean updateSalary(int employeeId, double newSalary) throws SQLException {
    String sql = "UPDATE employees SET salary = ?, updated_at = ? WHERE id = ?";

    try (Connection conn = DatabaseUtil.getConnection();
         PreparedStatement pstmt = conn.prepareStatement(sql)) {

        pstmt.setDouble(1, newSalary);
        pstmt.setTimestamp(2, new Timestamp(System.currentTimeMillis()));
        pstmt.setInt(3, employeeId);

        int rowsUpdated = pstmt.executeUpdate();
        return rowsUpdated > 0;
    }
}
```

#### DELETE with Parameters

```java
public boolean deleteEmployee(int employeeId) throws SQLException {
    String sql = "DELETE FROM employees WHERE id = ?";

    try (Connection conn = DatabaseUtil.getConnection();
         PreparedStatement pstmt = conn.prepareStatement(sql)) {

        pstmt.setInt(1, employeeId);

        int rowsDeleted = pstmt.executeUpdate();
        return rowsDeleted > 0;
    }
}
```

### Handling NULL Values

```java
// Setting NULL
if (employee.getDepartment() == null) {
    pstmt.setNull(4, Types.VARCHAR);
} else {
    pstmt.setString(4, employee.getDepartment());
}

// Using setObject (handles null automatically)
pstmt.setObject(4, employee.getDepartment(), Types.VARCHAR);
```

### Batch Operations with PreparedStatement

```java
public void insertEmployeesBatch(List<Employee> employees) throws SQLException {
    String sql = "INSERT INTO employees (name, email, salary) VALUES (?, ?, ?)";

    try (Connection conn = DatabaseUtil.getConnection();
         PreparedStatement pstmt = conn.prepareStatement(sql)) {

        conn.setAutoCommit(false);

        for (Employee emp : employees) {
            pstmt.setString(1, emp.getName());
            pstmt.setString(2, emp.getEmail());
            pstmt.setDouble(3, emp.getSalary());
            pstmt.addBatch();
        }

        int[] results = pstmt.executeBatch();
        conn.commit();

        System.out.println("Inserted " + results.length + " employees");

    } catch (SQLException e) {
        // Rollback handled by try-with-resources closing connection
        throw e;
    }
}
```

### Reusing PreparedStatement

```java
String sql = "INSERT INTO logs (message, level) VALUES (?, ?)";

try (Connection conn = DatabaseUtil.getConnection();
     PreparedStatement pstmt = conn.prepareStatement(sql)) {

    // First execution
    pstmt.setString(1, "Application started");
    pstmt.setString(2, "INFO");
    pstmt.executeUpdate();

    // Clear and reuse
    pstmt.clearParameters();

    // Second execution
    pstmt.setString(1, "Error occurred");
    pstmt.setString(2, "ERROR");
    pstmt.executeUpdate();
}
```

---

## Comparison Example

### Using Statement (Vulnerable to SQL Injection)

```java
// DANGEROUS - Don't do this!
String username = request.getParameter("username");
String sql = "SELECT * FROM users WHERE username = '" + username + "'";
ResultSet rs = stmt.executeQuery(sql);

// If username = "' OR '1'='1", the query becomes:
// SELECT * FROM users WHERE username = '' OR '1'='1'
// This returns ALL users!
```

### Using PreparedStatement (Safe)

```java
// SAFE - Use this approach
String username = request.getParameter("username");
String sql = "SELECT * FROM users WHERE username = ?";
PreparedStatement pstmt = conn.prepareStatement(sql);
pstmt.setString(1, username);
ResultSet rs = pstmt.executeQuery();

// The username is treated as a literal value, not SQL code
```

---

## Best Practices

1. **Always use PreparedStatement** for queries with user input
2. **Use try-with-resources** for automatic resource cleanup
3. **Use batch operations** for multiple similar inserts
4. **Get generated keys** when inserting records with auto-increment
5. **Clear parameters** when reusing PreparedStatement
6. **Use appropriate setter methods** for correct type mapping

---

## Summary

| Feature | Statement | PreparedStatement |
|---------|-----------|-------------------|
| **Use Case** | Static SQL, DDL | Dynamic SQL with parameters |
| **Security** | SQL injection risk | SQL injection safe |
| **Performance** | Slower for repeated queries | Faster (precompiled) |
| **Parameterized** | No | Yes |
| **Best For** | One-time queries, admin scripts | Application code, CRUD operations |

---

## Next Topic

Continue to [SQL Injection](./06-sql-injection.md) to understand SQL injection attacks and how to prevent them.
