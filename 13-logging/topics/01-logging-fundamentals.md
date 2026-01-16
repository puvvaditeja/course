# Logging Fundamentals

## What is Application Logging?

Application logging is the practice of recording events, errors, and diagnostic information during program execution. Logs provide visibility into what your application is doing, making them essential for debugging, monitoring, and maintaining software in production.

### The Logging Ecosystem

Java has several logging frameworks and APIs:

| Framework | Type | Description |
|-----------|------|-------------|
| **SLF4J** | API/Facade | Abstraction layer (recommended for application code) |
| **Log4J 2** | Implementation | High-performance logging framework |
| **Logback** | Implementation | Native SLF4J implementation |
| **JUL** | Implementation | Java's built-in java.util.logging |

**Recommended approach**: Use SLF4J API in your code with Log4J 2 as the implementation. This provides framework independence while leveraging Log4J 2's performance.

---

## Why Use Logging?

Logging is essential for application development and maintenance for several reasons:

### 1. Debugging and Troubleshooting

- Track application flow and execution path
- Identify where errors occur in the code
- Understand variable states and data values at specific points
- Reproduce and diagnose bugs in production

### 2. Monitoring and Auditing

- Monitor application health and performance
- Track user activities and transactions
- Maintain audit trails for compliance
- Detect security incidents and unauthorized access

### 3. Performance Analysis

- Measure method execution times
- Identify performance bottlenecks
- Track resource usage patterns
- Optimize critical code paths

### 4. Production Support

- Diagnose issues without debugging tools
- Understand production behavior without reproducing locally
- Correlate events across distributed systems
- Provide evidence for incident reports

---

## Logging vs System.out.println()

Many beginners use `System.out.println()` for debugging, but this approach has significant limitations:

| Aspect | System.out.println() | Logging Frameworks |
|--------|---------------------|-------|
| Control | No control over output | Configurable levels and targets |
| Performance | Always executes | Can be disabled by level |
| Output Format | Fixed format | Customizable layouts |
| Output Target | Console only | Console, files, databases, etc. |
| Production Use | Hard to disable | Easy to adjust via configuration |
| Thread Safety | Thread-safe but slow | Optimized for multi-threading |
| Filtering | No filtering | Filters by level, class, pattern |
| Log Rotation | No support | Built-in file rotation |

**Example Problem with println:**

```java
public class UserService {
    public void createUser(String username) {
        System.out.println("Creating user: " + username);
        // Business logic
        System.out.println("User created successfully");
    }
}
```

**Issues:**
- Output always goes to console (can't redirect to files)
- No timestamp or context information
- Can't disable in production without code changes
- Clutters production output
- No severity indication (error vs info)

---

## Log Levels

Log4J uses hierarchical log levels to categorize messages by severity. Understanding these levels is crucial for effective logging.

### Standard Log Levels (Highest to Lowest Priority)

```
FATAL > ERROR > WARN > INFO > DEBUG > TRACE
```

| Level | Purpose | When to Use | Example |
|-------|---------|-------------|---------|
| **FATAL** | Application crash | Unrecoverable errors that cause shutdown | Database connection completely lost |
| **ERROR** | Serious problems | Errors that prevent specific operations | Failed to process payment transaction |
| **WARN** | Potential problems | Issues that don't stop the flow but need attention | API response time exceeds threshold |
| **INFO** | Informational | Important business events | User logged in, order placed |
| **DEBUG** | Debug information | Detailed flow for debugging | Method parameters, query results |
| **TRACE** | Very detailed debug | Fine-grained diagnostic information | Loop iterations, variable changes |

### Log Level Hierarchy

When you set a log level, all messages at that level and higher are logged:

```
If level = INFO:
  FATAL ✓ Logged
  ERROR ✓ Logged
  WARN  ✓ Logged
  INFO  ✓ Logged
  DEBUG ✗ Not logged
  TRACE ✗ Not logged
```

### Typical Environment Settings

| Environment | Recommended Level | Reason |
|-------------|------------------|---------|
| Development | DEBUG or TRACE | Maximum visibility for debugging |
| Testing/QA | DEBUG or INFO | Balance between detail and noise |
| Staging | INFO | Monitor key events without excessive detail |
| Production | WARN or ERROR | Minimize overhead, focus on problems |

---

## Getting Started with Log4J

### Maven Dependencies

For Log4J 2.x, add these dependencies to your `pom.xml`:

```xml
<!-- Log4J 2 Core -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.20.0</version>
</dependency>

<!-- Log4J 2 API -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.20.0</version>
</dependency>
```

### Gradle Dependencies

```gradle
implementation 'org.apache.logging.log4j:log4j-core:2.20.0'
implementation 'org.apache.logging.log4j:log4j-api:2.20.0'
```

---

## Basic Usage Example

### Simple Console Logging

```java
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

public class UserService {
    // Create logger instance for this class
    private static final Logger logger = LogManager.getLogger(UserService.class);

    public void createUser(String username, String email) {
        logger.info("Starting user creation for username: {}", username);

        try {
            // Validate input
            if (username == null || username.isEmpty()) {
                logger.error("Username cannot be null or empty");
                throw new IllegalArgumentException("Invalid username");
            }

            // Business logic
            logger.debug("Validating email: {}", email);
            validateEmail(email);

            logger.debug("Saving user to database");
            saveToDatabase(username, email);

            logger.info("User created successfully: {}", username);

        } catch (Exception e) {
            logger.error("Failed to create user: {}", username, e);
            throw e;
        }
    }

    private void validateEmail(String email) {
        logger.trace("Entering validateEmail method");
        // Validation logic
        logger.trace("Email validation completed");
    }

    private void saveToDatabase(String username, String email) {
        logger.debug("Database connection established");
        // Database logic
        logger.debug("User record inserted");
    }
}
```

### Logger Best Practices

#### 1. Logger Declaration

```java
// GOOD: Static final logger, one per class
private static final Logger logger = LogManager.getLogger(ClassName.class);

// BAD: Creating logger in methods
public void someMethod() {
    Logger logger = LogManager.getLogger(ClassName.class); // Wasteful
}

// BAD: Using string name instead of class
private static final Logger logger = LogManager.getLogger("MyLogger");
```

#### 2. Parameterized Messages

```java
// GOOD: Parameterized logging (efficient)
logger.info("User {} logged in at {}", username, timestamp);

// BAD: String concatenation (always executes)
logger.info("User " + username + " logged in at " + timestamp);
```

The parameterized approach only constructs the message string if the log level is enabled, improving performance.

#### 3. Exception Logging

```java
// GOOD: Include exception as last parameter
try {
    riskyOperation();
} catch (Exception e) {
    logger.error("Operation failed for user: {}", userId, e);
}

// BAD: Converting exception to string
catch (Exception e) {
    logger.error("Error: " + e.getMessage()); // Loses stack trace
}
```

---

## Logger Naming and Hierarchy

Log4J uses a hierarchical naming system based on Java package structure:

```
com.company
├── com.company.service
│   ├── com.company.service.UserService
│   └── com.company.service.OrderService
└── com.company.repository
    ├── com.company.repository.UserRepository
    └── com.company.repository.OrderRepository
```

### Hierarchical Configuration

You can configure logging levels at different hierarchy levels:

```xml
<!-- Root logger: applies to all loggers -->
<Root level="WARN">

<!-- Package level: applies to all classes in package -->
<Logger name="com.company.service" level="INFO">

<!-- Class level: applies to specific class -->
<Logger name="com.company.service.UserService" level="DEBUG">
```

This hierarchy allows fine-grained control:
- Set most of application to WARN
- Enable INFO for service layer
- Enable DEBUG for specific troublesome class

---

## Performance Considerations

### 1. Guard Expensive Operations

```java
// GOOD: Guard check for expensive operations
if (logger.isDebugEnabled()) {
    logger.debug("User data: {}", expensiveToStringMethod());
}

// BAD: Always executes even if DEBUG is disabled
logger.debug("User data: {}", expensiveToStringMethod());
```

### 2. Use Parameterized Messages

```java
// GOOD: Parameters only evaluated if level is enabled
logger.debug("Processing record {} of {}", current, total);

// BAD: String concatenation always executes
logger.debug("Processing record " + current + " of " + total);
```

### 3. Async Logging

For high-throughput applications, use async appenders (covered in Log4J 2 Architecture topic).

---

## Common Use Cases

### 1. Application Startup/Shutdown

```java
public class Application {
    private static final Logger logger = LogManager.getLogger(Application.class);

    public static void main(String[] args) {
        logger.info("Application starting...");
        logger.info("Java version: {}", System.getProperty("java.version"));
        logger.info("Configuration loaded from: {}", configPath);

        try {
            startApplication();
            logger.info("Application started successfully");
        } catch (Exception e) {
            logger.fatal("Failed to start application", e);
            System.exit(1);
        }
    }
}
```

### 2. Database Operations

```java
public class UserRepository {
    private static final Logger logger = LogManager.getLogger(UserRepository.class);

    public User findById(Long id) {
        logger.debug("Fetching user with id: {}", id);

        String sql = "SELECT * FROM users WHERE id = ?";
        logger.trace("Executing SQL: {}", sql);

        try {
            User user = jdbcTemplate.queryForObject(sql, new Object[]{id}, userMapper);
            logger.debug("User found: {}", user.getUsername());
            return user;
        } catch (EmptyResultDataAccessException e) {
            logger.warn("User not found with id: {}", id);
            return null;
        } catch (Exception e) {
            logger.error("Database error while fetching user: {}", id, e);
            throw new DataAccessException("Failed to fetch user", e);
        }
    }
}
```

### 3. API Endpoints

```java
@RestController
public class UserController {
    private static final Logger logger = LogManager.getLogger(UserController.class);

    @PostMapping("/users")
    public ResponseEntity<User> createUser(@RequestBody UserDTO userDTO) {
        logger.info("Received create user request: {}", userDTO.getUsername());

        try {
            User user = userService.createUser(userDTO);
            logger.info("User created successfully: {}", user.getId());
            return ResponseEntity.ok(user);
        } catch (ValidationException e) {
            logger.warn("Validation failed for user creation: {}", e.getMessage());
            return ResponseEntity.badRequest().build();
        } catch (Exception e) {
            logger.error("Failed to create user", e);
            return ResponseEntity.status(500).build();
        }
    }
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Application Logging | Recording events during execution for visibility and debugging |
| Why Use Logging | Debugging, monitoring, performance analysis, production support |
| Log Levels | FATAL > ERROR > WARN > INFO > DEBUG > TRACE (hierarchical) |
| Basic Usage | Get logger, use parameterized messages, log exceptions properly |
| Best Practices | Static logger per class, parameterized messages, guard expensive operations |

## Next Topic

Continue to [SLF4J](./02-slf4j.md) to learn about the logging facade that provides framework independence.
