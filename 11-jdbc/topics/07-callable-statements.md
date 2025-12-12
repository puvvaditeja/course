# Callable Statements

## Overview

`CallableStatement` is a JDBC interface for executing stored procedures and functions in the database. It extends `PreparedStatement` and provides methods for handling IN, OUT, and INOUT parameters.

---

## Stored Procedures vs Functions

| Feature | Stored Procedure | Function |
|---------|------------------|----------|
| Return Value | Optional (via OUT params) | Required (single value) |
| Output | Multiple values possible | Single value |
| Use in SQL | Can't use in SELECT | Can use in SELECT |
| Transaction | Can control transactions | Usually no transaction control |
| Side Effects | Can modify data | Should avoid side effects |

---

## Creating Stored Procedures (MySQL Examples)

### Simple Procedure (No Parameters)

```sql
DELIMITER //
CREATE PROCEDURE GetAllEmployees()
BEGIN
    SELECT * FROM employees;
END //
DELIMITER ;
```

### Procedure with IN Parameters

```sql
DELIMITER //
CREATE PROCEDURE GetEmployeesByDepartment(IN dept_name VARCHAR(50))
BEGIN
    SELECT * FROM employees WHERE department = dept_name;
END //
DELIMITER ;
```

### Procedure with OUT Parameters

```sql
DELIMITER //
CREATE PROCEDURE GetEmployeeCount(OUT emp_count INT)
BEGIN
    SELECT COUNT(*) INTO emp_count FROM employees;
END //
DELIMITER ;
```

### Procedure with INOUT Parameters

```sql
DELIMITER //
CREATE PROCEDURE IncreaseSalary(INOUT current_salary DECIMAL(10,2), IN percentage INT)
BEGIN
    SET current_salary = current_salary * (1 + percentage / 100.0);
END //
DELIMITER ;
```

### Procedure with Multiple OUT Parameters

```sql
DELIMITER //
CREATE PROCEDURE GetEmployeeStats(
    IN dept_name VARCHAR(50),
    OUT emp_count INT,
    OUT avg_salary DECIMAL(10,2),
    OUT max_salary DECIMAL(10,2)
)
BEGIN
    SELECT COUNT(*), AVG(salary), MAX(salary)
    INTO emp_count, avg_salary, max_salary
    FROM employees
    WHERE department = dept_name;
END //
DELIMITER ;
```

---

## CallableStatement Syntax

### Basic Syntax

```java
// Syntax for calling procedures
CallableStatement cstmt = conn.prepareCall("{call procedure_name(?, ?, ?)}");

// Syntax for calling functions
CallableStatement cstmt = conn.prepareCall("{? = call function_name(?, ?)}");
```

### Parameter Types

- **IN**: Input parameter (passed to procedure)
- **OUT**: Output parameter (returned from procedure)
- **INOUT**: Both input and output

---

## Executing Stored Procedures

### Procedure with No Parameters

```java
public List<Employee> getAllEmployees() throws SQLException {
    List<Employee> employees = new ArrayList<>();
    String sql = "{call GetAllEmployees()}";

    try (Connection conn = DatabaseUtil.getConnection();
         CallableStatement cstmt = conn.prepareCall(sql);
         ResultSet rs = cstmt.executeQuery()) {

        while (rs.next()) {
            Employee emp = new Employee();
            emp.setId(rs.getInt("id"));
            emp.setName(rs.getString("name"));
            emp.setSalary(rs.getDouble("salary"));
            employees.add(emp);
        }
    }
    return employees;
}
```

### Procedure with IN Parameters

```java
public List<Employee> getEmployeesByDepartment(String department) throws SQLException {
    List<Employee> employees = new ArrayList<>();
    String sql = "{call GetEmployeesByDepartment(?)}";

    try (Connection conn = DatabaseUtil.getConnection();
         CallableStatement cstmt = conn.prepareCall(sql)) {

        // Set IN parameter
        cstmt.setString(1, department);

        try (ResultSet rs = cstmt.executeQuery()) {
            while (rs.next()) {
                Employee emp = new Employee();
                emp.setId(rs.getInt("id"));
                emp.setName(rs.getString("name"));
                emp.setDepartment(rs.getString("department"));
                employees.add(emp);
            }
        }
    }
    return employees;
}
```

### Procedure with OUT Parameters

```java
public int getEmployeeCount() throws SQLException {
    String sql = "{call GetEmployeeCount(?)}";

    try (Connection conn = DatabaseUtil.getConnection();
         CallableStatement cstmt = conn.prepareCall(sql)) {

        // Register OUT parameter
        cstmt.registerOutParameter(1, Types.INTEGER);

        // Execute the procedure
        cstmt.execute();

        // Get the OUT parameter value
        return cstmt.getInt(1);
    }
}
```

### Procedure with INOUT Parameters

```java
public double calculateNewSalary(double currentSalary, int percentageIncrease)
        throws SQLException {
    String sql = "{call IncreaseSalary(?, ?)}";

    try (Connection conn = DatabaseUtil.getConnection();
         CallableStatement cstmt = conn.prepareCall(sql)) {

        // Set INOUT parameter (both set and register)
        cstmt.setDouble(1, currentSalary);
        cstmt.registerOutParameter(1, Types.DECIMAL);

        // Set IN parameter
        cstmt.setInt(2, percentageIncrease);

        // Execute
        cstmt.execute();

        // Get modified INOUT value
        return cstmt.getDouble(1);
    }
}
```

### Procedure with Multiple OUT Parameters

```java
public DepartmentStats getDepartmentStats(String department) throws SQLException {
    String sql = "{call GetEmployeeStats(?, ?, ?, ?)}";

    try (Connection conn = DatabaseUtil.getConnection();
         CallableStatement cstmt = conn.prepareCall(sql)) {

        // IN parameter
        cstmt.setString(1, department);

        // Register OUT parameters
        cstmt.registerOutParameter(2, Types.INTEGER);   // emp_count
        cstmt.registerOutParameter(3, Types.DECIMAL);   // avg_salary
        cstmt.registerOutParameter(4, Types.DECIMAL);   // max_salary

        cstmt.execute();

        // Get OUT values
        DepartmentStats stats = new DepartmentStats();
        stats.setEmployeeCount(cstmt.getInt(2));
        stats.setAverageSalary(cstmt.getDouble(3));
        stats.setMaxSalary(cstmt.getDouble(4));

        return stats;
    }
}
```

---

## Using Named Parameters

Some JDBC drivers support named parameters instead of positional indexes.

```java
String sql = "{call GetEmployeeStats(:dept, :count, :avg, :max)}";

try (CallableStatement cstmt = conn.prepareCall(sql)) {
    cstmt.setString("dept", "Engineering");
    cstmt.registerOutParameter("count", Types.INTEGER);
    cstmt.registerOutParameter("avg", Types.DECIMAL);
    cstmt.registerOutParameter("max", Types.DECIMAL);

    cstmt.execute();

    int count = cstmt.getInt("count");
    double avg = cstmt.getDouble("avg");
    double max = cstmt.getDouble("max");
}
```

---

## Calling Functions

### Creating a Function (MySQL)

```sql
DELIMITER //
CREATE FUNCTION CalculateBonus(emp_salary DECIMAL(10,2), performance_rating INT)
RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
    DECLARE bonus DECIMAL(10,2);
    SET bonus = emp_salary * (performance_rating / 100.0);
    RETURN bonus;
END //
DELIMITER ;
```

### Calling a Function in JDBC

```java
public double calculateBonus(double salary, int performanceRating) throws SQLException {
    String sql = "{? = call CalculateBonus(?, ?)}";

    try (Connection conn = DatabaseUtil.getConnection();
         CallableStatement cstmt = conn.prepareCall(sql)) {

        // Register return value
        cstmt.registerOutParameter(1, Types.DECIMAL);

        // Set input parameters
        cstmt.setDouble(2, salary);
        cstmt.setInt(3, performanceRating);

        cstmt.execute();

        // Get return value
        return cstmt.getDouble(1);
    }
}
```

---

## Handling ResultSets from Stored Procedures

### Procedure Returning a ResultSet

```sql
DELIMITER //
CREATE PROCEDURE SearchEmployees(IN search_term VARCHAR(100))
BEGIN
    SELECT * FROM employees
    WHERE name LIKE CONCAT('%', search_term, '%')
       OR email LIKE CONCAT('%', search_term, '%');
END //
DELIMITER ;
```

```java
public List<Employee> searchEmployees(String searchTerm) throws SQLException {
    List<Employee> employees = new ArrayList<>();
    String sql = "{call SearchEmployees(?)}";

    try (Connection conn = DatabaseUtil.getConnection();
         CallableStatement cstmt = conn.prepareCall(sql)) {

        cstmt.setString(1, searchTerm);

        boolean hasResults = cstmt.execute();

        if (hasResults) {
            try (ResultSet rs = cstmt.getResultSet()) {
                while (rs.next()) {
                    employees.add(mapToEmployee(rs));
                }
            }
        }
    }
    return employees;
}
```

### Procedure with Multiple ResultSets

```sql
DELIMITER //
CREATE PROCEDURE GetDepartmentReport(IN dept_name VARCHAR(50))
BEGIN
    -- First result set: employees
    SELECT * FROM employees WHERE department = dept_name;

    -- Second result set: projects
    SELECT * FROM projects WHERE department = dept_name;
END //
DELIMITER ;
```

```java
public DepartmentReport getDepartmentReport(String department) throws SQLException {
    DepartmentReport report = new DepartmentReport();
    String sql = "{call GetDepartmentReport(?)}";

    try (Connection conn = DatabaseUtil.getConnection();
         CallableStatement cstmt = conn.prepareCall(sql)) {

        cstmt.setString(1, department);
        boolean hasResults = cstmt.execute();

        // First result set: employees
        if (hasResults) {
            try (ResultSet rs = cstmt.getResultSet()) {
                while (rs.next()) {
                    report.getEmployees().add(mapToEmployee(rs));
                }
            }
        }

        // Move to next result set
        if (cstmt.getMoreResults()) {
            try (ResultSet rs = cstmt.getResultSet()) {
                while (rs.next()) {
                    report.getProjects().add(mapToProject(rs));
                }
            }
        }
    }
    return report;
}
```

---

## Complete Example: Employee Management

### Stored Procedures

```sql
-- Create Employee
DELIMITER //
CREATE PROCEDURE CreateEmployee(
    IN p_name VARCHAR(100),
    IN p_email VARCHAR(100),
    IN p_salary DECIMAL(10,2),
    IN p_department VARCHAR(50),
    OUT p_id INT
)
BEGIN
    INSERT INTO employees (name, email, salary, department)
    VALUES (p_name, p_email, p_salary, p_department);

    SET p_id = LAST_INSERT_ID();
END //
DELIMITER ;

-- Update Employee Salary
DELIMITER //
CREATE PROCEDURE UpdateSalary(
    IN p_id INT,
    IN p_new_salary DECIMAL(10,2),
    OUT p_success BOOLEAN
)
BEGIN
    UPDATE employees SET salary = p_new_salary WHERE id = p_id;
    SET p_success = ROW_COUNT() > 0;
END //
DELIMITER ;
```

### Java Implementation

```java
public class EmployeeDAO {

    public int createEmployee(Employee emp) throws SQLException {
        String sql = "{call CreateEmployee(?, ?, ?, ?, ?)}";

        try (Connection conn = DatabaseUtil.getConnection();
             CallableStatement cstmt = conn.prepareCall(sql)) {

            cstmt.setString(1, emp.getName());
            cstmt.setString(2, emp.getEmail());
            cstmt.setDouble(3, emp.getSalary());
            cstmt.setString(4, emp.getDepartment());
            cstmt.registerOutParameter(5, Types.INTEGER);

            cstmt.execute();

            return cstmt.getInt(5);  // Return generated ID
        }
    }

    public boolean updateSalary(int employeeId, double newSalary) throws SQLException {
        String sql = "{call UpdateSalary(?, ?, ?)}";

        try (Connection conn = DatabaseUtil.getConnection();
             CallableStatement cstmt = conn.prepareCall(sql)) {

            cstmt.setInt(1, employeeId);
            cstmt.setDouble(2, newSalary);
            cstmt.registerOutParameter(3, Types.BOOLEAN);

            cstmt.execute();

            return cstmt.getBoolean(3);
        }
    }
}
```

---

## Best Practices

1. **Use stored procedures** for complex database operations
2. **Register OUT parameters** before executing
3. **Use appropriate SQL types** when registering parameters
4. **Handle multiple result sets** when expected
5. **Use transactions** for procedures that modify data
6. **Close resources** properly with try-with-resources
7. **Handle exceptions** appropriately

---

## Common SQL Types for Parameter Registration

| Java Type | SQL Type Constant |
|-----------|-------------------|
| int | Types.INTEGER |
| long | Types.BIGINT |
| double | Types.DOUBLE |
| float | Types.FLOAT |
| String | Types.VARCHAR |
| boolean | Types.BOOLEAN |
| Date | Types.DATE |
| Timestamp | Types.TIMESTAMP |
| BigDecimal | Types.DECIMAL |

---

## Next Topic

Continue to [ResultSet](./08-result-set.md) to learn about navigating and processing query results.
