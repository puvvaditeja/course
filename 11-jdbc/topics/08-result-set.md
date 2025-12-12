# ResultSet

## Overview

`ResultSet` is a JDBC interface that represents the result of a database query. It provides methods to navigate through results and retrieve data from each row.

---

## ResultSet Types

### Type Constants

| Type | Description | Navigation |
|------|-------------|------------|
| `TYPE_FORWARD_ONLY` | Default; can only move forward | `next()` only |
| `TYPE_SCROLL_INSENSITIVE` | Scrollable; doesn't reflect DB changes | All navigation methods |
| `TYPE_SCROLL_SENSITIVE` | Scrollable; reflects DB changes | All navigation methods |

### Concurrency Constants

| Concurrency | Description |
|-------------|-------------|
| `CONCUR_READ_ONLY` | Default; read-only ResultSet |
| `CONCUR_UPDATABLE` | Can update rows through ResultSet |

### Creating Different ResultSet Types

```java
// Default: Forward-only, Read-only
Statement stmt = conn.createStatement();

// Scrollable, Read-only
Statement stmt = conn.createStatement(
    ResultSet.TYPE_SCROLL_INSENSITIVE,
    ResultSet.CONCUR_READ_ONLY
);

// Scrollable, Updatable
Statement stmt = conn.createStatement(
    ResultSet.TYPE_SCROLL_SENSITIVE,
    ResultSet.CONCUR_UPDATABLE
);

// With PreparedStatement
PreparedStatement pstmt = conn.prepareStatement(
    sql,
    ResultSet.TYPE_SCROLL_INSENSITIVE,
    ResultSet.CONCUR_READ_ONLY
);
```

---

## Navigation Methods

### Forward Navigation

```java
ResultSet rs = stmt.executeQuery("SELECT * FROM employees");

// Move to next row (returns false when no more rows)
while (rs.next()) {
    // Process current row
    System.out.println(rs.getString("name"));
}
```

### Scrollable Navigation

```java
Statement stmt = conn.createStatement(
    ResultSet.TYPE_SCROLL_INSENSITIVE,
    ResultSet.CONCUR_READ_ONLY
);
ResultSet rs = stmt.executeQuery("SELECT * FROM employees");

// Move to first row
rs.first();

// Move to last row
rs.last();

// Move to previous row
rs.previous();

// Move to specific row (1-based)
rs.absolute(5);  // Go to row 5

// Move relative to current position
rs.relative(2);   // Move forward 2 rows
rs.relative(-1);  // Move back 1 row

// Move before first row
rs.beforeFirst();

// Move after last row
rs.afterLast();

// Check position
boolean isFirst = rs.isFirst();
boolean isLast = rs.isLast();
boolean isBeforeFirst = rs.isBeforeFirst();
boolean isAfterLast = rs.isAfterLast();

// Get current row number
int rowNum = rs.getRow();
```

---

## Retrieving Data

### By Column Index (1-based)

```java
while (rs.next()) {
    int id = rs.getInt(1);
    String name = rs.getString(2);
    double salary = rs.getDouble(3);
}
```

### By Column Name (Recommended)

```java
while (rs.next()) {
    int id = rs.getInt("id");
    String name = rs.getString("name");
    double salary = rs.getDouble("salary");
    Date hireDate = rs.getDate("hire_date");
    Timestamp createdAt = rs.getTimestamp("created_at");
}
```

### Common Getter Methods

| Method | SQL Type | Java Type |
|--------|----------|-----------|
| `getString()` | VARCHAR, CHAR | String |
| `getInt()` | INTEGER | int |
| `getLong()` | BIGINT | long |
| `getDouble()` | DOUBLE, FLOAT | double |
| `getFloat()` | FLOAT, REAL | float |
| `getBoolean()` | BOOLEAN, BIT | boolean |
| `getDate()` | DATE | java.sql.Date |
| `getTime()` | TIME | java.sql.Time |
| `getTimestamp()` | TIMESTAMP | java.sql.Timestamp |
| `getBigDecimal()` | DECIMAL, NUMERIC | BigDecimal |
| `getBytes()` | BLOB, BINARY | byte[] |
| `getObject()` | Any | Object |

### Handling NULL Values

```java
// Check if last value was NULL
int salary = rs.getInt("salary");
if (rs.wasNull()) {
    System.out.println("Salary is NULL");
}

// Using getObject for nullable values
Integer salaryObj = (Integer) rs.getObject("salary");
if (salaryObj == null) {
    System.out.println("Salary is NULL");
}

// Better approach with Optional (Java 8+)
public Optional<Integer> getSalary(ResultSet rs) throws SQLException {
    int salary = rs.getInt("salary");
    return rs.wasNull() ? Optional.empty() : Optional.of(salary);
}
```

---

## ResultSet Metadata

### Getting Column Information

```java
ResultSetMetaData metaData = rs.getMetaData();

// Get number of columns
int columnCount = metaData.getColumnCount();

// Get column information
for (int i = 1; i <= columnCount; i++) {
    String columnName = metaData.getColumnName(i);
    String columnLabel = metaData.getColumnLabel(i);
    String columnType = metaData.getColumnTypeName(i);
    int columnSize = metaData.getColumnDisplaySize(i);
    boolean isNullable = metaData.isNullable(i) == ResultSetMetaData.columnNullable;

    System.out.printf("%s (%s) size=%d nullable=%b%n",
                      columnName, columnType, columnSize, isNullable);
}
```

### Generic Row Processor

```java
public List<Map<String, Object>> resultSetToList(ResultSet rs) throws SQLException {
    List<Map<String, Object>> results = new ArrayList<>();
    ResultSetMetaData metaData = rs.getMetaData();
    int columnCount = metaData.getColumnCount();

    while (rs.next()) {
        Map<String, Object> row = new LinkedHashMap<>();
        for (int i = 1; i <= columnCount; i++) {
            String columnName = metaData.getColumnName(i);
            Object value = rs.getObject(i);
            row.put(columnName, value);
        }
        results.add(row);
    }

    return results;
}
```

---

## Updatable ResultSet

### Updating Rows

```java
Statement stmt = conn.createStatement(
    ResultSet.TYPE_SCROLL_SENSITIVE,
    ResultSet.CONCUR_UPDATABLE
);

ResultSet rs = stmt.executeQuery("SELECT * FROM employees WHERE department = 'Sales'");

while (rs.next()) {
    double currentSalary = rs.getDouble("salary");

    // Update the current row
    rs.updateDouble("salary", currentSalary * 1.10);  // 10% raise
    rs.updateRow();  // Save changes to database
}
```

### Inserting Rows

```java
ResultSet rs = stmt.executeQuery("SELECT * FROM employees");

// Move to insert row
rs.moveToInsertRow();

// Set values
rs.updateString("name", "New Employee");
rs.updateString("email", "new@example.com");
rs.updateDouble("salary", 50000.00);
rs.updateString("department", "Engineering");

// Insert the row
rs.insertRow();

// Move back to current row
rs.moveToCurrentRow();
```

### Deleting Rows

```java
ResultSet rs = stmt.executeQuery("SELECT * FROM employees WHERE status = 'inactive'");

while (rs.next()) {
    rs.deleteRow();  // Delete current row
}
```

---

## Best Practices for ResultSet

### 1. Always Use Try-With-Resources

```java
public List<Employee> getAllEmployees() {
    List<Employee> employees = new ArrayList<>();
    String sql = "SELECT * FROM employees";

    try (Connection conn = DatabaseUtil.getConnection();
         Statement stmt = conn.createStatement();
         ResultSet rs = stmt.executeQuery(sql)) {

        while (rs.next()) {
            employees.add(mapToEmployee(rs));
        }

    } catch (SQLException e) {
        logger.error("Error fetching employees", e);
    }

    return employees;
}
```

### 2. Use Column Names Instead of Indexes

```java
// Preferred - more readable and maintainable
String name = rs.getString("name");
double salary = rs.getDouble("salary");

// Avoid - harder to maintain if query changes
String name = rs.getString(2);
double salary = rs.getDouble(3);
```

### 3. Create Mapper Methods

```java
private Employee mapToEmployee(ResultSet rs) throws SQLException {
    Employee emp = new Employee();
    emp.setId(rs.getInt("id"));
    emp.setName(rs.getString("name"));
    emp.setEmail(rs.getString("email"));
    emp.setSalary(rs.getDouble("salary"));
    emp.setDepartment(rs.getString("department"));
    emp.setHireDate(rs.getDate("hire_date").toLocalDate());
    return emp;
}
```

### 4. Handle Large ResultSets

```java
// Set fetch size for better performance
stmt.setFetchSize(100);

// Process in batches
int count = 0;
while (rs.next()) {
    processEmployee(rs);
    count++;

    if (count % 1000 == 0) {
        logger.info("Processed {} records", count);
    }
}
```

### 5. Use Forward-Only When Possible

```java
// Forward-only is more memory efficient
Statement stmt = conn.createStatement(
    ResultSet.TYPE_FORWARD_ONLY,
    ResultSet.CONCUR_READ_ONLY
);
```

---

## Complete Example: Employee Repository

```java
public class EmployeeRepository {

    public List<Employee> findAll() throws SQLException {
        List<Employee> employees = new ArrayList<>();
        String sql = "SELECT id, name, email, salary, department, hire_date FROM employees";

        try (Connection conn = DatabaseUtil.getConnection();
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(sql)) {

            while (rs.next()) {
                employees.add(mapToEmployee(rs));
            }
        }
        return employees;
    }

    public Optional<Employee> findById(int id) throws SQLException {
        String sql = "SELECT * FROM employees WHERE id = ?";

        try (Connection conn = DatabaseUtil.getConnection();
             PreparedStatement pstmt = conn.prepareStatement(sql)) {

            pstmt.setInt(1, id);

            try (ResultSet rs = pstmt.executeQuery()) {
                if (rs.next()) {
                    return Optional.of(mapToEmployee(rs));
                }
            }
        }
        return Optional.empty();
    }

    public List<Employee> findByDepartment(String department) throws SQLException {
        List<Employee> employees = new ArrayList<>();
        String sql = "SELECT * FROM employees WHERE department = ? ORDER BY name";

        try (Connection conn = DatabaseUtil.getConnection();
             PreparedStatement pstmt = conn.prepareStatement(sql)) {

            pstmt.setString(1, department);

            try (ResultSet rs = pstmt.executeQuery()) {
                while (rs.next()) {
                    employees.add(mapToEmployee(rs));
                }
            }
        }
        return employees;
    }

    public Page<Employee> findAllPaginated(int page, int size) throws SQLException {
        List<Employee> employees = new ArrayList<>();
        int offset = page * size;

        String countSql = "SELECT COUNT(*) FROM employees";
        String dataSql = "SELECT * FROM employees ORDER BY id LIMIT ? OFFSET ?";

        try (Connection conn = DatabaseUtil.getConnection()) {

            // Get total count
            int total;
            try (Statement stmt = conn.createStatement();
                 ResultSet rs = stmt.executeQuery(countSql)) {
                rs.next();
                total = rs.getInt(1);
            }

            // Get page data
            try (PreparedStatement pstmt = conn.prepareStatement(dataSql)) {
                pstmt.setInt(1, size);
                pstmt.setInt(2, offset);

                try (ResultSet rs = pstmt.executeQuery()) {
                    while (rs.next()) {
                        employees.add(mapToEmployee(rs));
                    }
                }
            }

            return new Page<>(employees, page, size, total);
        }
    }

    private Employee mapToEmployee(ResultSet rs) throws SQLException {
        Employee emp = new Employee();
        emp.setId(rs.getInt("id"));
        emp.setName(rs.getString("name"));
        emp.setEmail(rs.getString("email"));
        emp.setSalary(rs.getDouble("salary"));
        emp.setDepartment(rs.getString("department"));

        Date hireDate = rs.getDate("hire_date");
        if (hireDate != null) {
            emp.setHireDate(hireDate.toLocalDate());
        }

        return emp;
    }
}
```

---

## Summary

| Feature | Description |
|---------|-------------|
| Navigation | `next()`, `previous()`, `first()`, `last()`, `absolute()`, `relative()` |
| Data Retrieval | `getXxx(index)` or `getXxx(columnName)` |
| NULL Handling | Use `wasNull()` after getter |
| Metadata | `ResultSetMetaData` for column info |
| Updates | Use `CONCUR_UPDATABLE` for modify operations |
| Best Practice | Always close with try-with-resources |

---

## Module Complete

You have completed the JDBC module. You should now understand:
- JDBC architecture and interfaces
- Driver types and registration
- Connection management and utility classes
- Statement types and their use cases
- SQL injection prevention
- Calling stored procedures
- ResultSet navigation and data retrieval

Continue to the next module: [Testing](../../12-testing/)
