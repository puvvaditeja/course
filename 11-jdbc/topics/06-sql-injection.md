# SQL Injection

## Overview

SQL Injection is one of the most critical web application security vulnerabilities. It occurs when untrusted data is sent to an interpreter as part of a command or query, allowing attackers to execute unintended commands or access unauthorized data.

---

## What is SQL Injection?

SQL Injection happens when user input is incorrectly filtered or not strongly typed and unexpectedly executed as SQL code.

### Basic Example

```java
// Vulnerable code
String username = request.getParameter("username");
String password = request.getParameter("password");

String sql = "SELECT * FROM users WHERE username = '" + username +
             "' AND password = '" + password + "'";

Statement stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery(sql);
```

**Normal input:**
- Username: `john`
- Password: `secret123`
- Generated SQL: `SELECT * FROM users WHERE username = 'john' AND password = 'secret123'`

**Malicious input:**
- Username: `' OR '1'='1`
- Password: `' OR '1'='1`
- Generated SQL: `SELECT * FROM users WHERE username = '' OR '1'='1' AND password = '' OR '1'='1'`

This query returns ALL users because `'1'='1'` is always true!

---

## Types of SQL Injection

### 1. Classic SQL Injection (In-band)

The attacker uses the same communication channel to launch attacks and gather results.

```sql
-- Original query
SELECT * FROM users WHERE id = [user_input]

-- Attack input: 1 OR 1=1
SELECT * FROM users WHERE id = 1 OR 1=1
-- Returns all users

-- Attack input: 1; DROP TABLE users; --
SELECT * FROM users WHERE id = 1; DROP TABLE users; --
-- Deletes the entire table!
```

### 2. Blind SQL Injection

The attacker doesn't see the results directly but can infer information based on application behavior.

```sql
-- Boolean-based blind injection
SELECT * FROM users WHERE id = 1 AND 1=1  -- True, page loads normally
SELECT * FROM users WHERE id = 1 AND 1=2  -- False, page behaves differently

-- Time-based blind injection
SELECT * FROM users WHERE id = 1; WAITFOR DELAY '0:0:5'--
-- If page takes 5 seconds, the database is vulnerable
```

### 3. Union-Based SQL Injection

Combining results from multiple queries.

```sql
-- Original: SELECT name, email FROM users WHERE id = [input]

-- Attack input: 1 UNION SELECT username, password FROM admin_users--
SELECT name, email FROM users WHERE id = 1
UNION SELECT username, password FROM admin_users--
-- Returns admin credentials!
```

### 4. Second-Order SQL Injection

Malicious input is stored in the database and executed later.

```java
// Step 1: User registers with malicious username
String username = "admin'--";
// Stored in database

// Step 2: Later, username is used in another query
String sql = "SELECT * FROM orders WHERE user = '" + storedUsername + "'";
// Becomes: SELECT * FROM orders WHERE user = 'admin'--'
// The -- comments out the rest, showing admin's orders
```

---

## SQL Injection Attack Examples

### Authentication Bypass

```java
// Vulnerable login query
String sql = "SELECT * FROM users WHERE username = '" + user +
             "' AND password = '" + pass + "'";

// Attack: username = admin'--
// Result: SELECT * FROM users WHERE username = 'admin'--' AND password = ''
// The -- comments out password check, logging in as admin!

// Attack: username = ' OR 1=1--
// Result: SELECT * FROM users WHERE username = '' OR 1=1--' AND password = ''
// Returns all users, often logging in as first user (admin)
```

### Data Theft

```java
// Vulnerable product search
String sql = "SELECT name, price FROM products WHERE category = '" + category + "'";

// Attack: category = ' UNION SELECT username, password FROM users--
// Result: SELECT name, price FROM products WHERE category = ''
//         UNION SELECT username, password FROM users--
// Exposes all usernames and passwords!
```

### Data Manipulation

```java
// Vulnerable update
String sql = "UPDATE users SET email = '" + newEmail + "' WHERE id = " + userId;

// Attack: newEmail = test@test.com', admin = 1 WHERE username = 'victim'--
// Result: UPDATE users SET email = 'test@test.com', admin = 1
//         WHERE username = 'victim'--' WHERE id = 123
// Escalates victim to admin!
```

### Database Destruction

```java
// Vulnerable delete
String sql = "DELETE FROM posts WHERE id = " + postId;

// Attack: postId = 1; DROP TABLE users; --
// Result: DELETE FROM posts WHERE id = 1; DROP TABLE users; --
// Deletes the entire users table!
```

---

## Prevention Techniques

### 1. Use PreparedStatement (Primary Defense)

```java
// SECURE: Using PreparedStatement
String sql = "SELECT * FROM users WHERE username = ? AND password = ?";

try (PreparedStatement pstmt = conn.prepareStatement(sql)) {
    pstmt.setString(1, username);
    pstmt.setString(2, password);

    ResultSet rs = pstmt.executeQuery();
    // Parameters are properly escaped
}
```

**Why it works:** PreparedStatement separates SQL code from data. The database treats parameters as literal values, not executable SQL.

### 2. Use Parameterized Stored Procedures

```sql
-- Create stored procedure
CREATE PROCEDURE GetUser(IN p_username VARCHAR(50), IN p_password VARCHAR(50))
BEGIN
    SELECT * FROM users WHERE username = p_username AND password = p_password;
END
```

```java
// Call stored procedure
String sql = "{call GetUser(?, ?)}";
try (CallableStatement cstmt = conn.prepareCall(sql)) {
    cstmt.setString(1, username);
    cstmt.setString(2, password);
    ResultSet rs = cstmt.executeQuery();
}
```

### 3. Input Validation (Defense in Depth)

```java
public class InputValidator {

    // Validate numeric input
    public static boolean isValidId(String input) {
        try {
            Integer.parseInt(input);
            return true;
        } catch (NumberFormatException e) {
            return false;
        }
    }

    // Validate email format
    public static boolean isValidEmail(String email) {
        String regex = "^[A-Za-z0-9+_.-]+@(.+)$";
        return email != null && email.matches(regex);
    }

    // Whitelist allowed characters
    public static boolean isValidUsername(String username) {
        String regex = "^[a-zA-Z0-9_]{3,20}$";
        return username != null && username.matches(regex);
    }

    // Sanitize string (remove dangerous characters)
    public static String sanitize(String input) {
        if (input == null) return null;
        return input.replaceAll("[^a-zA-Z0-9\\s]", "");
    }
}
```

```java
// Usage
String userId = request.getParameter("id");
if (!InputValidator.isValidId(userId)) {
    throw new IllegalArgumentException("Invalid user ID");
}

String sql = "SELECT * FROM users WHERE id = ?";
PreparedStatement pstmt = conn.prepareStatement(sql);
pstmt.setInt(1, Integer.parseInt(userId));
```

### 4. Whitelist Input Validation

```java
public List<Product> getProductsByCategory(String category) throws SQLException {
    // Whitelist allowed categories
    Set<String> allowedCategories = Set.of("electronics", "clothing", "books", "home");

    if (!allowedCategories.contains(category.toLowerCase())) {
        throw new IllegalArgumentException("Invalid category");
    }

    String sql = "SELECT * FROM products WHERE category = ?";
    // ... execute query
}
```

### 5. Escape Special Characters (Last Resort)

```java
// Only use if you CANNOT use PreparedStatement
public static String escapeForSQL(String input) {
    if (input == null) return null;
    return input.replace("'", "''")
                .replace("\\", "\\\\")
                .replace("\0", "\\0")
                .replace("\n", "\\n")
                .replace("\r", "\\r");
}

// Note: This is NOT recommended - use PreparedStatement instead!
```

### 6. Principle of Least Privilege

```sql
-- Create application-specific database user with limited permissions
CREATE USER 'app_user'@'localhost' IDENTIFIED BY 'secure_password';

-- Grant only necessary permissions
GRANT SELECT, INSERT, UPDATE ON myapp.products TO 'app_user'@'localhost';
GRANT SELECT, INSERT, UPDATE, DELETE ON myapp.orders TO 'app_user'@'localhost';

-- Deny dangerous permissions
-- No DROP, ALTER, CREATE, or access to system tables
```

---

## Secure Code Examples

### Secure Login Implementation

```java
public User authenticateUser(String username, String password) {
    // Input validation
    if (username == null || username.length() < 3 || username.length() > 50) {
        throw new IllegalArgumentException("Invalid username");
    }

    String sql = "SELECT id, username, password_hash, salt FROM users WHERE username = ?";

    try (Connection conn = DatabaseUtil.getConnection();
         PreparedStatement pstmt = conn.prepareStatement(sql)) {

        pstmt.setString(1, username);

        try (ResultSet rs = pstmt.executeQuery()) {
            if (rs.next()) {
                String storedHash = rs.getString("password_hash");
                String salt = rs.getString("salt");

                // Verify password using secure hashing
                if (PasswordUtil.verifyPassword(password, storedHash, salt)) {
                    User user = new User();
                    user.setId(rs.getInt("id"));
                    user.setUsername(rs.getString("username"));
                    return user;
                }
            }
        }
    } catch (SQLException e) {
        // Log error but don't expose details
        logger.error("Authentication error", e);
    }

    return null; // Authentication failed
}
```

### Secure Search Implementation

```java
public List<Product> searchProducts(String searchTerm, String category,
                                     Double minPrice, Double maxPrice) {
    List<Product> products = new ArrayList<>();

    // Build query dynamically but safely
    StringBuilder sql = new StringBuilder("SELECT * FROM products WHERE 1=1");
    List<Object> params = new ArrayList<>();

    if (searchTerm != null && !searchTerm.trim().isEmpty()) {
        sql.append(" AND (name LIKE ? OR description LIKE ?)");
        String searchPattern = "%" + searchTerm + "%";
        params.add(searchPattern);
        params.add(searchPattern);
    }

    if (category != null) {
        sql.append(" AND category = ?");
        params.add(category);
    }

    if (minPrice != null) {
        sql.append(" AND price >= ?");
        params.add(minPrice);
    }

    if (maxPrice != null) {
        sql.append(" AND price <= ?");
        params.add(maxPrice);
    }

    try (Connection conn = DatabaseUtil.getConnection();
         PreparedStatement pstmt = conn.prepareStatement(sql.toString())) {

        // Set parameters
        for (int i = 0; i < params.size(); i++) {
            pstmt.setObject(i + 1, params.get(i));
        }

        try (ResultSet rs = pstmt.executeQuery()) {
            while (rs.next()) {
                products.add(mapResultSetToProduct(rs));
            }
        }
    } catch (SQLException e) {
        logger.error("Search error", e);
    }

    return products;
}
```

---

## Testing for SQL Injection

### Manual Testing Inputs

```
' OR '1'='1
' OR '1'='1'--
' OR '1'='1'/*
admin'--
1 OR 1=1
1; DROP TABLE users--
' UNION SELECT null,null,null--
' AND 1=2 UNION SELECT username, password FROM users--
```

### Automated Tools

- **SQLMap**: Automatic SQL injection detection and exploitation
- **Burp Suite**: Web vulnerability scanner
- **OWASP ZAP**: Open-source security scanner

---

## Summary

| Prevention Method | Effectiveness | Implementation |
|-------------------|---------------|----------------|
| PreparedStatement | Excellent | Primary defense |
| Stored Procedures | Excellent | Database-side security |
| Input Validation | Good | Defense in depth |
| Whitelist Validation | Good | For known values |
| Least Privilege | Good | Limit damage |
| Escaping | Poor | Last resort only |

**Key Takeaway:** Always use `PreparedStatement` with parameterized queries. It's the most effective and reliable protection against SQL injection.

---

## Next Topic

Continue to [Callable Statements](./07-callable-statements.md) to learn about executing stored procedures.
