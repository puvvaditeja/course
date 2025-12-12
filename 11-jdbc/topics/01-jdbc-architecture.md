# JDBC Architecture and Interfaces

## Overview

JDBC (Java Database Connectivity) is a Java API that enables Java programs to connect to and interact with relational databases. It provides a standard interface for database operations, making your code database-independent.

---

## JDBC Architecture

### Two-Tier Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Java Application                         │
│                                                              │
│    ┌──────────────────────────────────────────────────┐     │
│    │                  JDBC API                          │     │
│    │         (java.sql and javax.sql packages)         │     │
│    └──────────────────────────────────────────────────┘     │
│                            │                                 │
│    ┌──────────────────────────────────────────────────┐     │
│    │               JDBC Driver                          │     │
│    │     (Database-specific implementation)            │     │
│    └──────────────────────────────────────────────────┘     │
│                            │                                 │
└────────────────────────────│────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                       Database                               │
│            (MySQL, PostgreSQL, Oracle, etc.)                │
└─────────────────────────────────────────────────────────────┘
```

In the two-tier model, the Java application communicates directly with the database through the JDBC driver.

### Three-Tier Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Client Application                       │
│                   (Browser, Desktop App)                     │
└─────────────────────────────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                  Application Server                          │
│          (Servlets, Spring, Business Logic)                  │
│                                                              │
│    ┌──────────────────────────────────────────────────┐     │
│    │                  JDBC API                          │     │
│    └──────────────────────────────────────────────────┘     │
│                            │                                 │
│    ┌──────────────────────────────────────────────────┐     │
│    │               JDBC Driver                          │     │
│    └──────────────────────────────────────────────────┘     │
└─────────────────────────────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                       Database                               │
└─────────────────────────────────────────────────────────────┘
```

In the three-tier model, the middle tier (application server) handles business logic and database operations.

---

## JDBC Components

### Core Components

| Component | Description |
|-----------|-------------|
| **JDBC API** | Java interfaces and classes for database connectivity |
| **JDBC Driver Manager** | Manages database drivers and establishes connections |
| **JDBC Driver** | Database-specific implementation of JDBC interfaces |
| **Database** | The actual data store (MySQL, PostgreSQL, Oracle, etc.) |

---

## Key JDBC Interfaces

All core JDBC interfaces are in the `java.sql` package.

### 1. Driver Interface

The `Driver` interface is implemented by database vendors to provide database connectivity.

```java
public interface Driver {
    Connection connect(String url, Properties info) throws SQLException;
    boolean acceptsURL(String url) throws SQLException;
    DriverPropertyInfo[] getPropertyInfo(String url, Properties info) throws SQLException;
    int getMajorVersion();
    int getMinorVersion();
    boolean jdbcCompliant();
    Logger getParentLogger() throws SQLFeatureNotSupportedException;
}
```

### 2. DriverManager Class

`DriverManager` is a class (not interface) that manages JDBC drivers and creates connections.

```java
// Key methods
public static Connection getConnection(String url) throws SQLException
public static Connection getConnection(String url, String user, String password) throws SQLException
public static Connection getConnection(String url, Properties info) throws SQLException
public static void registerDriver(Driver driver) throws SQLException
public static void deregisterDriver(Driver driver) throws SQLException
```

**Example:**
```java
// Get a database connection
Connection conn = DriverManager.getConnection(
    "jdbc:mysql://localhost:3306/mydb",
    "username",
    "password"
);
```

### 3. Connection Interface

`Connection` represents a session with a specific database. It's used to create statements and manage transactions.

```java
public interface Connection extends Wrapper, AutoCloseable {
    // Statement creation
    Statement createStatement() throws SQLException;
    PreparedStatement prepareStatement(String sql) throws SQLException;
    CallableStatement prepareCall(String sql) throws SQLException;

    // Transaction management
    void setAutoCommit(boolean autoCommit) throws SQLException;
    boolean getAutoCommit() throws SQLException;
    void commit() throws SQLException;
    void rollback() throws SQLException;

    // Connection management
    void close() throws SQLException;
    boolean isClosed() throws SQLException;

    // Metadata
    DatabaseMetaData getMetaData() throws SQLException;
}
```

**Example:**
```java
Connection conn = DriverManager.getConnection(url, user, password);

// Disable auto-commit for transaction control
conn.setAutoCommit(false);

try {
    // Execute database operations
    // ...

    conn.commit();  // Commit transaction
} catch (SQLException e) {
    conn.rollback();  // Rollback on error
} finally {
    conn.close();
}
```

### 4. Statement Interface

`Statement` is used to execute static SQL queries without parameters.

```java
public interface Statement extends Wrapper, AutoCloseable {
    // Execute methods
    ResultSet executeQuery(String sql) throws SQLException;  // For SELECT
    int executeUpdate(String sql) throws SQLException;        // For INSERT, UPDATE, DELETE
    boolean execute(String sql) throws SQLException;          // For any SQL

    // Batch execution
    void addBatch(String sql) throws SQLException;
    int[] executeBatch() throws SQLException;
    void clearBatch() throws SQLException;

    // Result handling
    ResultSet getResultSet() throws SQLException;
    int getUpdateCount() throws SQLException;

    // Resource management
    void close() throws SQLException;
}
```

**Example:**
```java
Statement stmt = conn.createStatement();

// SELECT query
ResultSet rs = stmt.executeQuery("SELECT * FROM employees");

// INSERT/UPDATE/DELETE query
int rowsAffected = stmt.executeUpdate("UPDATE employees SET salary = 50000 WHERE id = 1");

stmt.close();
```

### 5. PreparedStatement Interface

`PreparedStatement` extends `Statement` for precompiled SQL with parameters. It's safer and more efficient.

```java
public interface PreparedStatement extends Statement {
    // Execute methods
    ResultSet executeQuery() throws SQLException;
    int executeUpdate() throws SQLException;
    boolean execute() throws SQLException;

    // Parameter setters
    void setString(int parameterIndex, String x) throws SQLException;
    void setInt(int parameterIndex, int x) throws SQLException;
    void setLong(int parameterIndex, long x) throws SQLException;
    void setDouble(int parameterIndex, double x) throws SQLException;
    void setDate(int parameterIndex, Date x) throws SQLException;
    void setTimestamp(int parameterIndex, Timestamp x) throws SQLException;
    void setObject(int parameterIndex, Object x) throws SQLException;
    void setNull(int parameterIndex, int sqlType) throws SQLException;

    // Clear parameters
    void clearParameters() throws SQLException;
}
```

**Example:**
```java
String sql = "INSERT INTO employees (name, salary, department) VALUES (?, ?, ?)";
PreparedStatement pstmt = conn.prepareStatement(sql);

pstmt.setString(1, "John Doe");
pstmt.setDouble(2, 55000.00);
pstmt.setString(3, "Engineering");

int rowsInserted = pstmt.executeUpdate();
pstmt.close();
```

### 6. CallableStatement Interface

`CallableStatement` extends `PreparedStatement` for calling stored procedures.

```java
public interface CallableStatement extends PreparedStatement {
    // Register OUT parameters
    void registerOutParameter(int parameterIndex, int sqlType) throws SQLException;
    void registerOutParameter(String parameterName, int sqlType) throws SQLException;

    // Get OUT parameter values
    String getString(int parameterIndex) throws SQLException;
    int getInt(int parameterIndex) throws SQLException;
    // ... other getters
}
```

**Example:**
```java
// Calling a stored procedure: get_employee_count(dept_name OUT count)
CallableStatement cstmt = conn.prepareCall("{call get_employee_count(?, ?)}");

cstmt.setString(1, "Engineering");
cstmt.registerOutParameter(2, Types.INTEGER);

cstmt.execute();

int count = cstmt.getInt(2);
cstmt.close();
```

### 7. ResultSet Interface

`ResultSet` represents the result of a SELECT query. It provides methods to navigate and retrieve data.

```java
public interface ResultSet extends Wrapper, AutoCloseable {
    // Navigation methods
    boolean next() throws SQLException;
    boolean previous() throws SQLException;
    boolean first() throws SQLException;
    boolean last() throws SQLException;
    boolean absolute(int row) throws SQLException;
    boolean relative(int rows) throws SQLException;

    // Data retrieval by column index
    String getString(int columnIndex) throws SQLException;
    int getInt(int columnIndex) throws SQLException;
    long getLong(int columnIndex) throws SQLException;
    double getDouble(int columnIndex) throws SQLException;
    Date getDate(int columnIndex) throws SQLException;

    // Data retrieval by column name
    String getString(String columnLabel) throws SQLException;
    int getInt(String columnLabel) throws SQLException;
    // ... other getters

    // Metadata
    ResultSetMetaData getMetaData() throws SQLException;

    // Resource management
    void close() throws SQLException;
}
```

**Example:**
```java
Statement stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery("SELECT id, name, salary FROM employees");

while (rs.next()) {
    int id = rs.getInt("id");
    String name = rs.getString("name");
    double salary = rs.getDouble("salary");

    System.out.println(id + ": " + name + " - $" + salary);
}

rs.close();
stmt.close();
```

---

## Interface Hierarchy

```
                      AutoCloseable
                           │
                           ▼
                        Wrapper
                           │
            ┌──────────────┼──────────────┐
            │              │              │
            ▼              ▼              ▼
       Connection      Statement      ResultSet
                           │
              ┌────────────┴────────────┐
              │                         │
              ▼                         ▼
      PreparedStatement          (other subtypes)
              │
              ▼
      CallableStatement
```

---

## Summary Table

| Interface | Purpose | Key Methods |
|-----------|---------|-------------|
| `Driver` | Database-specific connectivity | `connect()`, `acceptsURL()` |
| `Connection` | Database session management | `createStatement()`, `prepareStatement()`, `commit()`, `rollback()` |
| `Statement` | Execute static SQL | `executeQuery()`, `executeUpdate()`, `execute()` |
| `PreparedStatement` | Execute parameterized SQL | `setXxx()` methods, `executeQuery()`, `executeUpdate()` |
| `CallableStatement` | Execute stored procedures | `registerOutParameter()`, `getXxx()` methods |
| `ResultSet` | Navigate query results | `next()`, `getXxx()` methods |

---

## Best Practices

1. **Always close resources**: Use try-with-resources or finally blocks
2. **Use PreparedStatement**: Prevents SQL injection and improves performance
3. **Manage transactions**: Explicitly control commits and rollbacks
4. **Use connection pooling**: In production, use pools like HikariCP or Apache DBCP
5. **Handle exceptions properly**: Log and handle `SQLException` appropriately

---

## Next Topic

Continue to [Driver Types and Registration](./02-driver-types.md) to learn about different JDBC driver types and how to register them.
