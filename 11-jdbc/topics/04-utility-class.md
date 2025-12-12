# Setting Up the Utility Class

## Overview

A database utility class centralizes connection management, making your code cleaner and more maintainable. This pattern prevents code duplication and simplifies configuration changes.

---

## Basic Connection Utility

### Simple Implementation

```java
package com.example.util;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class DatabaseUtil {

    private static final String URL = "jdbc:mysql://localhost:3306/mydb";
    private static final String USER = "root";
    private static final String PASSWORD = "password";

    // Private constructor to prevent instantiation
    private DatabaseUtil() {}

    /**
     * Get a database connection
     * @return Connection object
     * @throws SQLException if connection fails
     */
    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(URL, USER, PASSWORD);
    }

    /**
     * Close a connection safely
     * @param conn Connection to close
     */
    public static void closeConnection(Connection conn) {
        if (conn != null) {
            try {
                conn.close();
            } catch (SQLException e) {
                System.err.println("Error closing connection: " + e.getMessage());
            }
        }
    }
}
```

### Usage Example

```java
public class EmployeeDAO {

    public List<Employee> getAllEmployees() {
        List<Employee> employees = new ArrayList<>();
        String sql = "SELECT * FROM employees";

        try (Connection conn = DatabaseUtil.getConnection();
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(sql)) {

            while (rs.next()) {
                Employee emp = new Employee();
                emp.setId(rs.getInt("id"));
                emp.setName(rs.getString("name"));
                emp.setSalary(rs.getDouble("salary"));
                employees.add(emp);
            }

        } catch (SQLException e) {
            System.err.println("Error fetching employees: " + e.getMessage());
        }

        return employees;
    }
}
```

---

## Properties-Based Configuration

### Enhanced Utility with Properties File

```java
package com.example.util;

import java.io.IOException;
import java.io.InputStream;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.util.Properties;

public class DatabaseUtil {

    private static final Properties properties = new Properties();
    private static String url;
    private static String user;
    private static String password;

    // Static initialization block
    static {
        try (InputStream input = DatabaseUtil.class
                .getClassLoader()
                .getResourceAsStream("database.properties")) {

            if (input == null) {
                throw new RuntimeException("database.properties not found in classpath");
            }

            properties.load(input);

            url = properties.getProperty("db.url");
            user = properties.getProperty("db.user");
            password = properties.getProperty("db.password");

            // Optional: Load driver class (not required for JDBC 4.0+)
            String driver = properties.getProperty("db.driver");
            if (driver != null) {
                Class.forName(driver);
            }

        } catch (IOException | ClassNotFoundException e) {
            throw new RuntimeException("Error initializing database configuration", e);
        }
    }

    private DatabaseUtil() {}

    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(url, user, password);
    }

    public static void closeConnection(Connection conn) {
        if (conn != null) {
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### database.properties

```properties
# Database Configuration
db.driver=com.mysql.cj.jdbc.Driver
db.url=jdbc:mysql://localhost:3306/mydb?useSSL=false&serverTimezone=UTC
db.user=root
db.password=password
```

---

## Singleton Pattern Implementation

### Thread-Safe Singleton

```java
package com.example.util;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.util.Properties;
import java.io.InputStream;
import java.io.IOException;

public class ConnectionManager {

    private static ConnectionManager instance;
    private String url;
    private String user;
    private String password;

    // Private constructor
    private ConnectionManager() {
        loadProperties();
    }

    // Thread-safe singleton instance
    public static synchronized ConnectionManager getInstance() {
        if (instance == null) {
            instance = new ConnectionManager();
        }
        return instance;
    }

    private void loadProperties() {
        Properties props = new Properties();
        try (InputStream input = getClass().getClassLoader()
                .getResourceAsStream("database.properties")) {
            props.load(input);
            this.url = props.getProperty("db.url");
            this.user = props.getProperty("db.user");
            this.password = props.getProperty("db.password");
        } catch (IOException e) {
            throw new RuntimeException("Failed to load database properties", e);
        }
    }

    public Connection getConnection() throws SQLException {
        return DriverManager.getConnection(url, user, password);
    }
}
```

### Usage

```java
// Get connection from singleton
Connection conn = ConnectionManager.getInstance().getConnection();
```

---

## Complete Utility with Resource Management

### Full-Featured Database Utility

```java
package com.example.util;

import java.io.IOException;
import java.io.InputStream;
import java.sql.*;
import java.util.Properties;

public class DatabaseUtil {

    private static final Properties properties = new Properties();
    private static String url;
    private static String user;
    private static String password;
    private static boolean initialized = false;

    static {
        initialize();
    }

    private DatabaseUtil() {}

    /**
     * Initialize database configuration
     */
    private static synchronized void initialize() {
        if (initialized) return;

        try (InputStream input = DatabaseUtil.class
                .getClassLoader()
                .getResourceAsStream("database.properties")) {

            if (input == null) {
                throw new RuntimeException("database.properties not found");
            }

            properties.load(input);
            url = properties.getProperty("db.url");
            user = properties.getProperty("db.user");
            password = properties.getProperty("db.password");

            initialized = true;
            System.out.println("Database configuration loaded successfully");

        } catch (IOException e) {
            throw new RuntimeException("Failed to load database configuration", e);
        }
    }

    /**
     * Get a new database connection
     */
    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(url, user, password);
    }

    /**
     * Close ResultSet safely
     */
    public static void close(ResultSet rs) {
        if (rs != null) {
            try {
                rs.close();
            } catch (SQLException e) {
                System.err.println("Error closing ResultSet: " + e.getMessage());
            }
        }
    }

    /**
     * Close Statement safely
     */
    public static void close(Statement stmt) {
        if (stmt != null) {
            try {
                stmt.close();
            } catch (SQLException e) {
                System.err.println("Error closing Statement: " + e.getMessage());
            }
        }
    }

    /**
     * Close Connection safely
     */
    public static void close(Connection conn) {
        if (conn != null) {
            try {
                conn.close();
            } catch (SQLException e) {
                System.err.println("Error closing Connection: " + e.getMessage());
            }
        }
    }

    /**
     * Close all resources safely
     */
    public static void closeAll(ResultSet rs, Statement stmt, Connection conn) {
        close(rs);
        close(stmt);
        close(conn);
    }

    /**
     * Rollback transaction safely
     */
    public static void rollback(Connection conn) {
        if (conn != null) {
            try {
                conn.rollback();
            } catch (SQLException e) {
                System.err.println("Error rolling back: " + e.getMessage());
            }
        }
    }

    /**
     * Test database connection
     */
    public static boolean testConnection() {
        try (Connection conn = getConnection()) {
            return conn != null && !conn.isClosed();
        } catch (SQLException e) {
            System.err.println("Connection test failed: " + e.getMessage());
            return false;
        }
    }

    /**
     * Get database metadata
     */
    public static void printDatabaseInfo() {
        try (Connection conn = getConnection()) {
            DatabaseMetaData meta = conn.getMetaData();
            System.out.println("Database: " + meta.getDatabaseProductName());
            System.out.println("Version: " + meta.getDatabaseProductVersion());
            System.out.println("Driver: " + meta.getDriverName());
            System.out.println("Driver Version: " + meta.getDriverVersion());
            System.out.println("URL: " + meta.getURL());
            System.out.println("User: " + meta.getUserName());
        } catch (SQLException e) {
            System.err.println("Error getting database info: " + e.getMessage());
        }
    }
}
```

---

## Usage Patterns

### Pattern 1: Try-With-Resources (Recommended)

```java
public Employee findById(int id) throws SQLException {
    String sql = "SELECT * FROM employees WHERE id = ?";

    try (Connection conn = DatabaseUtil.getConnection();
         PreparedStatement pstmt = conn.prepareStatement(sql)) {

        pstmt.setInt(1, id);

        try (ResultSet rs = pstmt.executeQuery()) {
            if (rs.next()) {
                return mapResultSetToEmployee(rs);
            }
        }
    }
    return null;
}

private Employee mapResultSetToEmployee(ResultSet rs) throws SQLException {
    Employee emp = new Employee();
    emp.setId(rs.getInt("id"));
    emp.setName(rs.getString("name"));
    emp.setSalary(rs.getDouble("salary"));
    return emp;
}
```

### Pattern 2: Manual Resource Management

```java
public Employee findById(int id) {
    Connection conn = null;
    PreparedStatement pstmt = null;
    ResultSet rs = null;

    try {
        conn = DatabaseUtil.getConnection();
        pstmt = conn.prepareStatement("SELECT * FROM employees WHERE id = ?");
        pstmt.setInt(1, id);
        rs = pstmt.executeQuery();

        if (rs.next()) {
            return mapResultSetToEmployee(rs);
        }

    } catch (SQLException e) {
        System.err.println("Error finding employee: " + e.getMessage());
    } finally {
        DatabaseUtil.closeAll(rs, pstmt, conn);
    }

    return null;
}
```

### Pattern 3: Transaction Management

```java
public void transferFunds(int fromAccount, int toAccount, double amount) {
    Connection conn = null;

    try {
        conn = DatabaseUtil.getConnection();
        conn.setAutoCommit(false);  // Start transaction

        // Debit from source account
        String debitSql = "UPDATE accounts SET balance = balance - ? WHERE id = ?";
        try (PreparedStatement debitStmt = conn.prepareStatement(debitSql)) {
            debitStmt.setDouble(1, amount);
            debitStmt.setInt(2, fromAccount);
            debitStmt.executeUpdate();
        }

        // Credit to target account
        String creditSql = "UPDATE accounts SET balance = balance + ? WHERE id = ?";
        try (PreparedStatement creditStmt = conn.prepareStatement(creditSql)) {
            creditStmt.setDouble(1, amount);
            creditStmt.setInt(2, toAccount);
            creditStmt.executeUpdate();
        }

        conn.commit();  // Commit transaction
        System.out.println("Transfer successful");

    } catch (SQLException e) {
        DatabaseUtil.rollback(conn);
        System.err.println("Transfer failed, rolled back: " + e.getMessage());
    } finally {
        DatabaseUtil.close(conn);
    }
}
```

---

## Environment-Aware Configuration

```java
public class DatabaseUtil {

    private static String url;
    private static String user;
    private static String password;

    static {
        String env = System.getProperty("app.env", "development");
        String propertiesFile = "database-" + env + ".properties";

        try (InputStream input = DatabaseUtil.class
                .getClassLoader()
                .getResourceAsStream(propertiesFile)) {

            Properties props = new Properties();
            props.load(input);

            url = props.getProperty("db.url");
            user = props.getProperty("db.user");
            password = props.getProperty("db.password");

        } catch (IOException e) {
            throw new RuntimeException("Failed to load " + propertiesFile, e);
        }
    }

    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(url, user, password);
    }
}
```

Run with: `java -Dapp.env=production MyApp`

---

## Best Practices

1. **Use try-with-resources** for automatic resource cleanup
2. **Externalize configuration** in properties files
3. **Don't hardcode credentials** in source code
4. **Make utility methods static** for easy access
5. **Use connection pooling** in production (HikariCP, C3P0)
6. **Log errors** rather than just printing them
7. **Test connections** on application startup

---

## Next Topic

Continue to [Simple and Prepared Statements](./05-statements.md) to learn about executing SQL queries.
