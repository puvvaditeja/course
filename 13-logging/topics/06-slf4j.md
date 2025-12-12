# SLF4J (Simple Logging Facade for Java)

## What is SLF4J?

SLF4J (Simple Logging Facade for Java) is a logging abstraction layer that provides a common API for various logging frameworks. It acts as a facade or interface, allowing developers to write logging code once while being able to plug in different logging implementations (Log4J 2, Logback, JUL, etc.) without changing application code.

### The Logging Framework Problem

```
Without SLF4J:
Application → Log4J
     ↓ (framework change)
Application → Logback (requires code changes!)

With SLF4J:
Application → SLF4J API → Log4J
     ↓ (framework change)
Application → SLF4J API → Logback (no code changes!)
```

---

## Why Use SLF4J?

### 1. Framework Independence

Your application code doesn't depend on a specific logging implementation.

```java
// Code depends only on SLF4J API
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class UserService {
    private static final Logger logger = LoggerFactory.getLogger(UserService.class);

    public void createUser(String username) {
        logger.info("Creating user: {}", username);
    }
}
```

**Benefit**: Switch from Log4J to Logback or any other framework by changing dependencies, not code.

### 2. Library Compatibility

Third-party libraries can use SLF4J, and your application chooses the implementation.

```
Your Application (uses Logback)
    ↓
Library A (uses SLF4J) ─┐
                        ├──→ SLF4J API ──→ Logback Implementation
Library B (uses SLF4J) ─┘
```

All libraries' logs go through your chosen framework.

### 3. Performance

SLF4J uses parameterized messages efficiently:

```java
// Efficient: String construction only if level enabled
logger.debug("User {} logged in at {}", username, timestamp);

// Less efficient with direct logging framework
log4j.debug("User " + username + " logged in at " + timestamp); // Always constructs
```

### 4. Cleaner Dependencies

```xml
<!-- Application depends on SLF4J API (small, stable) -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>2.0.9</version>
</dependency>

<!-- Implementation chosen at deployment -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j2-impl</artifactId>
    <version>2.20.0</version>
</dependency>
```

---

## SLF4J Architecture

### Component Diagram

```
┌──────────────────────────────────────────┐
│       Application Code                   │
│       (imports org.slf4j.*)              │
└──────────────────────────────────────────┘
                  ↓
┌──────────────────────────────────────────┐
│       SLF4J API                          │
│       (slf4j-api.jar)                    │
│       - Logger interface                 │
│       - LoggerFactory                    │
└──────────────────────────────────────────┘
                  ↓
┌──────────────────────────────────────────┐
│       SLF4J Binding                      │
│       (slf4j-log4j2.jar)                 │
│       Adapts SLF4J API to Implementation │
└──────────────────────────────────────────┘
                  ↓
┌──────────────────────────────────────────┐
│       Logging Implementation             │
│       (log4j-core.jar)                   │
│       Actual logging framework           │
└──────────────────────────────────────────┘
```

### Key Components

1. **SLF4J API**: Common logging interface
2. **SLF4J Binding**: Adapter between API and implementation
3. **Implementation**: Actual logging framework (Log4J 2, Logback, etc.)

---

## Getting Started with SLF4J

### Maven Dependencies

#### SLF4J with Log4J 2

```xml
<!-- SLF4J API -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>2.0.9</version>
</dependency>

<!-- Log4J 2 SLF4J Binding -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j2-impl</artifactId>
    <version>2.20.0</version>
</dependency>

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

#### SLF4J with Logback

```xml
<!-- SLF4J API -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>2.0.9</version>
</dependency>

<!-- Logback (includes SLF4J binding) -->
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.4.11</version>
</dependency>
```

#### SLF4J with Java Util Logging

```xml
<!-- SLF4J API -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>2.0.9</version>
</dependency>

<!-- SLF4J JUL Binding -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-jdk14</artifactId>
    <version>2.0.9</version>
</dependency>
```

---

## Basic SLF4J Usage

### Creating a Logger

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class UserService {
    // Create logger for this class
    private static final Logger logger = LoggerFactory.getLogger(UserService.class);

    // Alternative: Using class name string
    private static final Logger logger2 = LoggerFactory.getLogger("com.company.UserService");
}
```

**Best Practice**: Use `ClassName.class` for automatic refactoring support.

### Log Levels

SLF4J supports standard log levels:

```java
logger.trace("Trace level - very detailed");
logger.debug("Debug level - detailed info");
logger.info("Info level - general info");
logger.warn("Warn level - warnings");
logger.error("Error level - errors");
```

**Note**: SLF4J doesn't have a FATAL level (Log4J 2 specific).

### Parameterized Logging

```java
// GOOD: Parameterized messages
logger.info("User {} logged in from IP {}", username, ipAddress);
logger.debug("Processing {} records in {} ms", count, duration);

// Support up to 2 parameters efficiently
logger.info("User: {}, Status: {}", username, status);

// More than 2 parameters
logger.info("User: {}, Status: {}, Time: {}", username, status, timestamp);

// Variable number of parameters
logger.info("Values: {}, {}, {}, {}", val1, val2, val3, val4);
```

### Exception Logging

```java
try {
    riskyOperation();
} catch (Exception e) {
    // Log exception with message
    logger.error("Failed to process user: {}", userId, e);

    // Exception as last parameter
    logger.error("Operation failed", e);

    // With parameterized message
    logger.error("Failed for user {} at {}", username, timestamp, e);
}
```

---

## Advanced SLF4J Features

### 1. Markers

Markers allow categorization and filtering of log events.

```java
import org.slf4j.Marker;
import org.slf4j.MarkerFactory;

public class UserService {
    private static final Logger logger = LoggerFactory.getLogger(UserService.class);

    // Define markers
    private static final Marker AUDIT = MarkerFactory.getMarker("AUDIT");
    private static final Marker PERFORMANCE = MarkerFactory.getMarker("PERFORMANCE");
    private static final Marker SECURITY = MarkerFactory.getMarker("SECURITY");

    public void createUser(String username) {
        // Audit log
        logger.info(AUDIT, "User created: {}", username);
    }

    public void login(String username) {
        // Security log
        logger.info(SECURITY, "Login attempt: {}", username);
    }

    public void measurePerformance(long duration) {
        // Performance log
        logger.info(PERFORMANCE, "Operation took {} ms", duration);
    }
}
```

**Log4J 2 Configuration for Markers:**
```xml
<Appenders>
    <!-- Audit log: Only AUDIT marker -->
    <RollingFile name="AuditLog" fileName="logs/audit.log">
        <MarkerFilter marker="AUDIT" onMatch="ACCEPT" onMismatch="DENY"/>
        <PatternLayout pattern="%d %-5p - %m%n"/>
    </RollingFile>

    <!-- Security log: Only SECURITY marker -->
    <RollingFile name="SecurityLog" fileName="logs/security.log">
        <MarkerFilter marker="SECURITY" onMatch="ACCEPT" onMismatch="DENY"/>
        <PatternLayout pattern="%d %-5p - %m%n"/>
    </RollingFile>
</Appenders>
```

### 2. MDC (Mapped Diagnostic Context)

MDC allows contextual information to be included in logs.

```java
import org.slf4j.MDC;

public class UserController {
    private static final Logger logger = LoggerFactory.getLogger(UserController.class);

    @GetMapping("/users/{id}")
    public User getUser(@PathVariable Long id, HttpServletRequest request) {
        try {
            // Add context
            MDC.put("userId", id.toString());
            MDC.put("requestId", UUID.randomUUID().toString());
            MDC.put("ipAddress", request.getRemoteAddr());

            logger.info("Fetching user details");
            User user = userService.getUser(id);
            logger.info("User found: {}", user.getUsername());

            return user;

        } finally {
            // Always clear MDC
            MDC.clear();
        }
    }
}
```

**Log4J 2 Pattern:**
```xml
<PatternLayout pattern="%d [%X{requestId}] [%X{userId}] [%X{ipAddress}] %-5p - %m%n"/>
```

**Output:**
```
2025-12-12 14:30:45 [a1b2c3d4-e5f6] [123] [192.168.1.100] INFO  - Fetching user details
2025-12-12 14:30:45 [a1b2c3d4-e5f6] [123] [192.168.1.100] INFO  - User found: john
```

**Spring Boot Filter for MDC:**
```java
@Component
public class MDCFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        try {
            MDC.put("requestId", UUID.randomUUID().toString());
            MDC.put("ipAddress", request.getRemoteAddr());
            chain.doFilter(request, response);
        } finally {
            MDC.clear();
        }
    }
}
```

### 3. Fluent API (SLF4J 2.0+)

Modern fluent logging API:

```java
// Traditional
logger.info("User {} created with role {}", username, role);

// Fluent API
logger.atInfo()
      .addKeyValue("username", username)
      .addKeyValue("role", role)
      .log("User created");

// Conditional logging
logger.atDebug()
      .addKeyValue("query", sql)
      .addKeyValue("duration", duration)
      .log("Query executed");

// With exception
logger.atError()
      .addKeyValue("userId", userId)
      .setCause(exception)
      .log("Failed to create user");
```

---

## Bridging Legacy Logging

SLF4J provides bridges to route legacy logging to SLF4J.

### Common Bridges

| Legacy Framework | Bridge Dependency | Purpose |
|-----------------|-------------------|---------|
| Commons Logging | `jcl-over-slf4j` | Apache Commons Logging → SLF4J |
| Log4J 1.x | `log4j-over-slf4j` | Log4J 1.x → SLF4J |
| JUL | `jul-to-slf4j` | Java Util Logging → SLF4J |

### Bridging Log4J 1.x to SLF4J to Log4J 2

```xml
<!-- SLF4J API -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>2.0.9</version>
</dependency>

<!-- Bridge: Log4J 1.x calls → SLF4J -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>log4j-over-slf4j</artifactId>
    <version>2.0.9</version>
</dependency>

<!-- Binding: SLF4J → Log4J 2 -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j2-impl</artifactId>
    <version>2.20.0</version>
</dependency>

<!-- Log4J 2 Implementation -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.20.0</version>
</dependency>

<!-- IMPORTANT: Exclude Log4J 1.x -->
<dependency>
    <groupId>some-library</groupId>
    <artifactId>uses-log4j1</artifactId>
    <exclusions>
        <exclusion>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

**Flow:**
```
Legacy Code (Log4J 1.x API)
    ↓
log4j-over-slf4j (bridge)
    ↓
SLF4J API
    ↓
log4j-slf4j2-impl (binding)
    ↓
Log4J 2 (implementation)
```

### Bridging Java Util Logging

```xml
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>jul-to-slf4j</artifactId>
    <version>2.0.9</version>
</dependency>
```

**Activate in code:**
```java
import org.slf4j.bridge.SLF4JBridgeHandler;

public class Application {
    public static void main(String[] args) {
        // Remove existing handlers
        SLF4JBridgeHandler.removeHandlersForRootLogger();

        // Add SLF4J bridge handler
        SLF4JBridgeHandler.install();

        // Now JUL logs go through SLF4J
    }
}
```

---

## SLF4J vs Direct Framework Usage

### Code Comparison

**Using SLF4J:**
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class UserService {
    private static final Logger logger = LoggerFactory.getLogger(UserService.class);

    public void createUser(String username) {
        logger.info("Creating user: {}", username);
    }
}
```

**Using Log4J 2 Directly:**
```java
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

public class UserService {
    private static final Logger logger = LogManager.getLogger(UserService.class);

    public void createUser(String username) {
        logger.info("Creating user: {}", username);
    }
}
```

### When to Use SLF4J vs Direct Framework

| Use SLF4J When | Use Direct Framework When |
|----------------|--------------------------|
| Building libraries for others | Building final application |
| Framework independence needed | Need framework-specific features |
| Working with multiple frameworks | Using single framework exclusively |
| Maximum flexibility required | Performance is critical |
| Team uses different frameworks | Framework choice is permanent |

**Recommendation**:
- **Libraries**: Always use SLF4J
- **Applications**: SLF4J is recommended but direct usage is acceptable

---

## Common Patterns

### 1. Guard Expensive Operations

```java
// Check if level is enabled before expensive operation
if (logger.isDebugEnabled()) {
    logger.debug("User data: {}", user.expensiveToString());
}

// Or use lambda (Java 8+)
logger.debug(() -> "User data: " + user.expensiveToString());
```

### 2. Structured Logging

```java
logger.atInfo()
      .addKeyValue("action", "user_created")
      .addKeyValue("username", username)
      .addKeyValue("email", email)
      .addKeyValue("timestamp", System.currentTimeMillis())
      .log("User registration completed");
```

### 3. Request Tracking

```java
@Component
public class RequestLoggingFilter implements Filter {
    private static final Logger logger = LoggerFactory.getLogger(RequestLoggingFilter.class);

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {

        String requestId = UUID.randomUUID().toString();
        MDC.put("requestId", requestId);

        long startTime = System.currentTimeMillis();
        logger.info("Request started: {} {}",
                    ((HttpServletRequest) request).getMethod(),
                    ((HttpServletRequest) request).getRequestURI());

        try {
            chain.doFilter(request, response);
        } finally {
            long duration = System.currentTimeMillis() - startTime;
            logger.info("Request completed in {} ms", duration);
            MDC.clear();
        }
    }
}
```

### 4. Error Handling Pattern

```java
public class UserService {
    private static final Logger logger = LoggerFactory.getLogger(UserService.class);

    public User createUser(UserDTO dto) {
        logger.info("Creating user: {}", dto.getUsername());

        try {
            validateUser(dto);
            User user = saveUser(dto);
            logger.info("User created successfully: {}", user.getId());
            return user;

        } catch (ValidationException e) {
            logger.warn("Validation failed for user: {}, reason: {}",
                       dto.getUsername(), e.getMessage());
            throw e;

        } catch (DataAccessException e) {
            logger.error("Database error while creating user: {}",
                        dto.getUsername(), e);
            throw new ServiceException("Failed to create user", e);

        } catch (Exception e) {
            logger.error("Unexpected error creating user: {}",
                        dto.getUsername(), e);
            throw new ServiceException("Unexpected error", e);
        }
    }
}
```

---

## Best Practices

### 1. Logger Declaration

```java
// GOOD: Static final logger
private static final Logger logger = LoggerFactory.getLogger(UserService.class);

// BAD: Non-static logger
private final Logger logger = LoggerFactory.getLogger(UserService.class);

// BAD: Creating logger per method
public void someMethod() {
    Logger logger = LoggerFactory.getLogger(UserService.class);
}
```

### 2. Use Parameterized Messages

```java
// GOOD: Parameters evaluated only if level enabled
logger.info("User {} created with role {}", username, role);

// BAD: String concatenation always executes
logger.info("User " + username + " created with role " + role);
```

### 3. Log Appropriate Levels

```java
logger.trace("Entering method with params: {}", params);  // Very detailed
logger.debug("Processing record {}", recordId);            // Debug info
logger.info("User {} logged in", username);                // Important events
logger.warn("Cache miss for key {}", key);                 // Potential issues
logger.error("Failed to connect to database", exception);  // Errors
```

### 4. Don't Log Sensitive Data

```java
// BAD: Logging passwords, credit cards, etc.
logger.info("User logged in: {} with password: {}", username, password);

// GOOD: Log safely
logger.info("User logged in: {}", username);

// GOOD: Mask sensitive data
logger.debug("Processing card: {}", maskCardNumber(cardNumber));
```

### 5. Clean Up MDC

```java
// ALWAYS clean up MDC in finally block
try {
    MDC.put("userId", userId);
    // ... business logic ...
} finally {
    MDC.clear(); // or MDC.remove("userId")
}
```

---

## Migration Guide

### From Log4J 1.x to SLF4J

**Before (Log4J 1.x):**
```java
import org.apache.log4j.Logger;

public class UserService {
    private static final Logger logger = Logger.getLogger(UserService.class);

    public void createUser(String username) {
        logger.info("Creating user: " + username);
    }
}
```

**After (SLF4J):**
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class UserService {
    private static final Logger logger = LoggerFactory.getLogger(UserService.class);

    public void createUser(String username) {
        logger.info("Creating user: {}", username);
    }
}
```

**Changes:**
1. Import: `org.apache.log4j.Logger` → `org.slf4j.Logger`
2. Import: Add `org.slf4j.LoggerFactory`
3. Logger creation: `Logger.getLogger()` → `LoggerFactory.getLogger()`
4. Message format: String concatenation → Parameterized messages

### From JUL to SLF4J

**Before (Java Util Logging):**
```java
import java.util.logging.Logger;
import java.util.logging.Level;

public class UserService {
    private static final Logger logger = Logger.getLogger(UserService.class.getName());

    public void createUser(String username) {
        logger.log(Level.INFO, "Creating user: " + username);
    }
}
```

**After (SLF4J):**
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class UserService {
    private static final Logger logger = LoggerFactory.getLogger(UserService.class);

    public void createUser(String username) {
        logger.info("Creating user: {}", username);
    }
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| What is SLF4J | Logging facade providing abstraction over logging frameworks |
| Why Use It | Framework independence, library compatibility, cleaner code |
| Architecture | API → Binding → Implementation (Log4J 2, Logback, etc.) |
| Basic Usage | LoggerFactory.getLogger(), parameterized messages, standard levels |
| Advanced Features | Markers, MDC, fluent API, bridges for legacy frameworks |
| Best Practices | Static loggers, parameterized messages, clean up MDC, appropriate levels |

## Next Topic

Continue to [Best Practices for Logging](./07-best-practices.md) to learn comprehensive logging strategies and patterns for production applications.
