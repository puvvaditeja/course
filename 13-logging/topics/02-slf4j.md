# SLF4J (Simple Logging Facade for Java)

## What is SLF4J?

SLF4J is a logging abstraction layer that provides a common API for various logging frameworks. It allows you to write logging code once and plug in different implementations (Log4J 2, Logback, etc.) without changing application code.

### Why Use SLF4J?

```
Without SLF4J:
Application → Log4J (code change needed to switch)

With SLF4J:
Application → SLF4J API → Log4J 2 (or Logback, etc.)
                          ↑ Switch by changing dependency only
```

**Benefits:**
- Framework independence
- Library compatibility (all libraries use same API)
- Switch implementations without code changes

---

## SLF4J API

### Maven Dependencies (SLF4J with Log4J 2)

```xml
<!-- SLF4J API -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>2.0.9</version>
</dependency>

<!-- SLF4J to Log4J 2 Binding -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j2-impl</artifactId>
    <version>2.21.1</version>
</dependency>

<!-- Log4J 2 Core -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.21.1</version>
</dependency>
```

### Architecture

```
Application Code (SLF4J API)
         ↓
    slf4j-api.jar (interface)
         ↓
    log4j-slf4j2-impl.jar (binding)
         ↓
    log4j-core.jar (implementation)
```

---

## Logger Factory

### Creating Loggers

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class UserService {
    // Create logger using LoggerFactory
    private static final Logger logger = LoggerFactory.getLogger(UserService.class);

    public void createUser(String username) {
        logger.info("Creating user: {}", username);
    }
}
```

### Log Levels in SLF4J

```java
logger.trace("Trace level - very detailed");
logger.debug("Debug level - diagnostic info");
logger.info("Info level - business events");
logger.warn("Warn level - potential issues");
logger.error("Error level - errors");
```

**Note**: SLF4J doesn't have FATAL level (use ERROR for critical errors).

### Parameterized Logging

```java
// GOOD: Parameterized (efficient)
logger.info("User {} logged in from {}", username, ipAddress);

// BAD: String concatenation (always executes)
logger.info("User " + username + " logged in from " + ipAddress);
```

### Exception Logging

```java
try {
    processOrder(orderId);
} catch (Exception e) {
    // Exception as last parameter
    logger.error("Failed to process order: {}", orderId, e);
}
```

---

## Best Practices for Logging

### 1. Logger Declaration

```java
// GOOD: Static final logger per class
private static final Logger logger = LoggerFactory.getLogger(UserService.class);

// BAD: Creating logger in methods
public void method() {
    Logger logger = LoggerFactory.getLogger(UserService.class); // Wasteful
}
```

### 2. Use Appropriate Levels

```java
// TRACE - Very detailed (loop iterations)
logger.trace("Processing item {} of {}", i, total);

// DEBUG - Diagnostic info
logger.debug("Query returned {} results", count);

// INFO - Business events
logger.info("Order {} placed by user {}", orderId, userId);

// WARN - Potential issues
logger.warn("Cache miss, falling back to database");

// ERROR - Failures
logger.error("Payment failed for order {}", orderId, exception);
```

### 3. Include Context

```java
// BAD: Vague
logger.error("Error occurred");

// GOOD: Context included
logger.error("Failed to create user: username={}, email={}", username, email, exception);
```

### 4. Don't Log Sensitive Data

```java
// BAD: Logging passwords
logger.info("User login: {}, password: {}", username, password);

// GOOD: Only safe data
logger.info("User login: {}", username);
```

### 5. Avoid Logging in Loops

```java
// BAD: Logs every iteration
for (User user : users) {
    logger.debug("Processing user: {}", user.getId()); // 1000s of logs!
}

// GOOD: Log summary
logger.info("Processing {} users", users.size());
for (User user : users) {
    processUser(user);
}
logger.info("Processed {} users successfully", successCount);
```

### 6. Guard Expensive Operations

```java
// Check if level is enabled before expensive operation
if (logger.isDebugEnabled()) {
    logger.debug("Data: {}", expensiveToString());
}
```

---

## MDC (Mapped Diagnostic Context)

MDC adds contextual information to all logs in a thread:

```java
import org.slf4j.MDC;

public void processRequest(String requestId, String userId) {
    try {
        // Add context
        MDC.put("requestId", requestId);
        MDC.put("userId", userId);

        logger.info("Processing request");  // Includes requestId, userId
        doWork();

    } finally {
        MDC.clear();  // Always clean up
    }
}
```

**Log4J Pattern with MDC:**
```xml
<PatternLayout pattern="%d [%X{requestId}] [%X{userId}] %-5level - %msg%n"/>
<!-- Output: 2024-01-15 10:30:45 [req-123] [user-456] INFO  - Processing request -->
```

---

## SLF4J vs Log4J Direct

| Aspect | SLF4J | Log4J Direct |
|--------|-------|--------------|
| Import | `org.slf4j.Logger` | `org.apache.logging.log4j.Logger` |
| Factory | `LoggerFactory.getLogger()` | `LogManager.getLogger()` |
| Framework Lock-in | No | Yes |
| Recommended For | Applications, Libraries | Log4J-specific features |

**Recommendation**: Use SLF4J API for application code. It provides flexibility and is the industry standard.

---

## Complete Example

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.slf4j.MDC;

public class OrderService {
    private static final Logger logger = LoggerFactory.getLogger(OrderService.class);

    public Order createOrder(String userId, List<Item> items) {
        MDC.put("userId", userId);

        try {
            logger.info("Creating order with {} items", items.size());

            // Validation
            logger.debug("Validating order items");
            validateItems(items);

            // Process
            Order order = processOrder(items);
            logger.info("Order created: orderId={}", order.getId());

            return order;

        } catch (ValidationException e) {
            logger.warn("Order validation failed: {}", e.getMessage());
            throw e;

        } catch (Exception e) {
            logger.error("Failed to create order", e);
            throw new ServiceException("Order creation failed", e);

        } finally {
            MDC.clear();
        }
    }
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| SLF4J | Logging facade for framework independence |
| LoggerFactory | `LoggerFactory.getLogger(ClassName.class)` |
| Parameterized | `logger.info("User: {}", name)` - efficient |
| Exception | `logger.error("Failed", exception)` - exception last |
| MDC | Thread-local context for request tracking |
| Best Practices | Static logger, appropriate levels, no sensitive data |

---

## Quick Reference

```java
// Logger creation
private static final Logger logger = LoggerFactory.getLogger(MyClass.class);

// Logging levels
logger.trace("...");
logger.debug("...");
logger.info("User {} created", username);
logger.warn("...");
logger.error("Failed: {}", id, exception);

// MDC
MDC.put("key", "value");
MDC.clear();
```
