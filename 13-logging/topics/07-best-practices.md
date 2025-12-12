# Best Practices for Logging

## Introduction

Effective logging is crucial for maintaining, debugging, and monitoring applications in production. This guide covers comprehensive best practices for logging in enterprise Java applications.

---

## General Logging Principles

### 1. Log With Purpose

Every log message should have a clear purpose and audience.

```java
// GOOD: Clear, informative log messages
logger.info("User {} successfully logged in from IP {}", username, ipAddress);
logger.warn("API response time {}ms exceeds threshold {}ms", responseTime, threshold);
logger.error("Failed to connect to database after {} retries", maxRetries, exception);

// BAD: Vague or useless messages
logger.info("Success");
logger.debug("Here");
logger.error("Error");
```

**Questions to ask:**
- Who will read this log?
- What action should they take?
- What context do they need?

### 2. Choose Appropriate Log Levels

| Level | When to Use | Example |
|-------|-------------|---------|
| **TRACE** | Very detailed flow, loop iterations | `logger.trace("Processing record {} of {}", i, total)` |
| **DEBUG** | Diagnostic information, variable values | `logger.debug("Query returned {} results", count)` |
| **INFO** | Important business events, milestones | `logger.info("Order {} placed by user {}", orderId, userId)` |
| **WARN** | Potential issues, degraded functionality | `logger.warn("Cache unavailable, falling back to database")` |
| **ERROR** | Errors requiring attention | `logger.error("Payment processing failed", exception)` |

```java
public class OrderService {
    private static final Logger logger = LoggerFactory.getLogger(OrderService.class);

    public Order createOrder(OrderRequest request) {
        logger.info("Creating order for user: {}", request.getUserId());

        // Debug: Detailed processing info
        logger.debug("Order items: {}, total: {}", request.getItems().size(), request.getTotal());

        try {
            // Trace: Very detailed flow
            logger.trace("Validating order request");
            validateOrder(request);

            logger.trace("Calculating discounts");
            BigDecimal finalAmount = calculateTotal(request);

            logger.trace("Saving order to database");
            Order order = orderRepository.save(request);

            logger.info("Order created successfully: {}", order.getId());
            return order;

        } catch (ValidationException e) {
            // Warn: Business rule violation
            logger.warn("Order validation failed for user {}: {}",
                       request.getUserId(), e.getMessage());
            throw e;

        } catch (PaymentException e) {
            // Error: Technical failure
            logger.error("Payment processing failed for order", e);
            throw e;
        }
    }
}
```

### 3. Include Relevant Context

```java
// BAD: Insufficient context
logger.error("Update failed");

// GOOD: Clear context
logger.error("Failed to update user {} in database", userId);

// BETTER: Complete context
logger.error("Failed to update user {} with email {} in database after {} attempts",
            userId, email, retryCount, exception);
```

**Essential context elements:**
- Entity identifiers (user ID, order ID, etc.)
- Operation being performed
- Relevant state information
- Timing information (when applicable)

---

## Code-Level Best Practices

### 1. Logger Declaration

```java
// GOOD: Static final logger per class
public class UserService {
    private static final Logger logger = LoggerFactory.getLogger(UserService.class);
}

// ALSO GOOD: Using method reference (Java 8+)
public class UserService {
    private static final Logger logger = LoggerFactory.getLogger(
        MethodHandles.lookup().lookupClass()
    );
}

// BAD: Non-static logger (creates multiple instances)
public class UserService {
    private final Logger logger = LoggerFactory.getLogger(UserService.class);
}

// BAD: Creating logger per method
public void createUser(String username) {
    Logger logger = LoggerFactory.getLogger(UserService.class);
}
```

### 2. Use Parameterized Messages

```java
// GOOD: Parameterized logging (efficient)
logger.info("User {} created with role {} at {}", username, role, timestamp);

// BAD: String concatenation (always executes)
logger.info("User " + username + " created with role " + role + " at " + timestamp);

// GOOD: Variable number of parameters
Object[] params = {id, name, email, phone};
logger.info("User created: id={}, name={}, email={}, phone={}", params);
```

**Performance benefit**: Parameters only evaluated if log level is enabled.

### 3. Guard Expensive Operations

```java
// GOOD: Guard check for expensive operations
if (logger.isDebugEnabled()) {
    logger.debug("User details: {}", user.toDetailedString()); // Expensive
}

// ALSO GOOD: Lambda expressions (Java 8+)
logger.debug(() -> "User details: " + user.toDetailedString());

// GOOD: Guard complex computations
if (logger.isTraceEnabled()) {
    logger.trace("Query results: {}", formatResults(resultSet));
}
```

### 4. Exception Logging

```java
// GOOD: Log exception with context
try {
    processPayment(orderId);
} catch (PaymentException e) {
    logger.error("Payment failed for order {} amount {}", orderId, amount, e);
    throw e;
}

// BAD: Losing stack trace
catch (Exception e) {
    logger.error("Error: " + e.getMessage()); // No stack trace!
}

// BAD: Converting to string
catch (Exception e) {
    logger.error("Error: {}", e.toString()); // Incomplete info
}

// GOOD: Exception always last parameter
logger.error("Context message: {}, {}, {}", param1, param2, param3, exception);

// AVOID: Logging and rethrowing at every level
public void level1() {
    try {
        level2();
    } catch (Exception e) {
        logger.error("Error in level1", e); // Logged here
        throw e;
    }
}

public void level2() {
    try {
        level3();
    } catch (Exception e) {
        logger.error("Error in level2", e); // Logged here too!
        throw e;
    }
}

// BETTER: Log at one level, rethrow at others
public void level1() {
    try {
        level2();
    } catch (Exception e) {
        logger.error("Error processing request", e); // Log only here
        throw e;
    }
}

public void level2() {
    level3(); // Just let it propagate
}
```

### 5. Don't Log Sensitive Information

```java
// BAD: Logging sensitive data
logger.info("User login: username={}, password={}", username, password);
logger.debug("Credit card: {}", creditCard);
logger.info("SSN: {}", ssn);

// GOOD: Exclude sensitive data
logger.info("User login: username={}", username);

// GOOD: Mask sensitive data
logger.debug("Credit card: {}", maskCreditCard(creditCard));
logger.info("Processing payment for user: {}", userId);

// Masking utility
public String maskCreditCard(String cardNumber) {
    if (cardNumber == null || cardNumber.length() < 4) {
        return "****";
    }
    return "**** **** **** " + cardNumber.substring(cardNumber.length() - 4);
}

// Common sensitive data to avoid:
// - Passwords, tokens, API keys
// - Credit card numbers, CVV
// - SSN, passport numbers
// - Personal health information
// - Personally identifiable information (PII) without consent
```

---

## Architectural Best Practices

### 1. Structured Logging

Use consistent structure for machine parsing:

```java
// Structured log messages
logger.info("action=user_login user_id={} ip_address={} result=success", userId, ip);
logger.warn("action=api_call endpoint={} duration_ms={} status=timeout", endpoint, duration);

// JSON-style structured logging
logger.info("{{\"action\":\"user_login\",\"user_id\":\"{}\",\"ip\":\"{}\",\"result\":\"success\"}}",
           userId, ip);

// Or use JSON layout in configuration
<JsonLayout complete="false" compact="true"/>

// MDC for automatic context
MDC.put("requestId", requestId);
MDC.put("userId", userId);
logger.info("Processing payment"); // requestId and userId auto-included
```

### 2. Use MDC for Request Tracking

```java
@Component
public class RequestContextFilter implements Filter {
    private static final Logger logger = LoggerFactory.getLogger(RequestContextFilter.class);

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {

        HttpServletRequest httpRequest = (HttpServletRequest) request;

        try {
            // Set up context
            String requestId = UUID.randomUUID().toString();
            MDC.put("requestId", requestId);
            MDC.put("method", httpRequest.getMethod());
            MDC.put("uri", httpRequest.getRequestURI());
            MDC.put("ip", httpRequest.getRemoteAddr());

            // Set response header for tracing
            ((HttpServletResponse) response).setHeader("X-Request-ID", requestId);

            logger.info("Request started");

            chain.doFilter(request, response);

            logger.info("Request completed");

        } finally {
            // Always clean up
            MDC.clear();
        }
    }
}

// Pattern layout includes MDC
<PatternLayout pattern="%d [%X{requestId}] [%X{method} %X{uri}] %-5p - %m%n"/>

// Output:
// 2025-12-12 14:30:45 [a1b2c3d4] [GET /api/users] INFO  - Request started
// 2025-12-12 14:30:45 [a1b2c3d4] [GET /api/users] INFO  - Fetching user from database
// 2025-12-12 14:30:45 [a1b2c3d4] [GET /api/users] INFO  - Request completed
```

### 3. Separate Concerns with Multiple Loggers

```java
public class UserService {
    // Regular application logs
    private static final Logger logger = LoggerFactory.getLogger(UserService.class);

    // Audit logs
    private static final Logger auditLogger = LoggerFactory.getLogger("AUDIT");

    // Performance logs
    private static final Logger perfLogger = LoggerFactory.getLogger("PERFORMANCE");

    public void createUser(User user) {
        long startTime = System.currentTimeMillis();

        logger.info("Creating user: {}", user.getUsername());

        // Business logic
        User created = userRepository.save(user);

        // Audit trail
        auditLogger.info("action=user_created user_id={} by_user={} timestamp={}",
                        created.getId(), getCurrentUserId(), Instant.now());

        // Performance tracking
        long duration = System.currentTimeMillis() - startTime;
        perfLogger.info("operation=create_user duration_ms={}", duration);

        return created;
    }
}
```

**Configuration:**
```xml
<Loggers>
    <!-- Regular application logs -->
    <Logger name="com.company" level="INFO">
        <AppenderRef ref="ApplicationLog"/>
    </Logger>

    <!-- Audit logs: separate file, longer retention -->
    <Logger name="AUDIT" level="INFO" additivity="false">
        <AppenderRef ref="AuditLog"/>
    </Logger>

    <!-- Performance logs: separate file, structured format -->
    <Logger name="PERFORMANCE" level="INFO" additivity="false">
        <AppenderRef ref="PerformanceLog"/>
    </Logger>
</Loggers>
```

### 4. Markers for Categorization

```java
import org.slf4j.Marker;
import org.slf4j.MarkerFactory;

public class UserService {
    private static final Logger logger = LoggerFactory.getLogger(UserService.class);

    private static final Marker AUDIT = MarkerFactory.getMarker("AUDIT");
    private static final Marker SECURITY = MarkerFactory.getMarker("SECURITY");
    private static final Marker BUSINESS = MarkerFactory.getMarker("BUSINESS");

    public void createUser(User user) {
        logger.info(BUSINESS, "User registration started: {}", user.getEmail());
        logger.info(AUDIT, "New user created: id={} email={}", user.getId(), user.getEmail());
    }

    public void login(String username, String password) {
        logger.info(SECURITY, "Login attempt: username={}", username);

        if (authenticate(username, password)) {
            logger.info(SECURITY, "Login successful: username={}", username);
            logger.info(AUDIT, "User logged in: {}", username);
        } else {
            logger.warn(SECURITY, "Login failed: username={}", username);
        }
    }
}
```

---

## Configuration Best Practices

### 1. Environment-Specific Configuration

```xml
<!-- Development: Verbose logging -->
<Configuration>
    <Properties>
        <Property name="logLevel">${env:LOG_LEVEL:-DEBUG}</Property>
    </Properties>

    <Appenders>
        <Console name="Console">
            <PatternLayout pattern="%d{HH:mm:ss} %highlight{%-5p} [%t] %c{1}.%M:%L - %m%n"/>
        </Console>
    </Appenders>

    <Loggers>
        <Logger name="com.company" level="${logLevel}"/>
        <Root level="DEBUG">
            <AppenderRef ref="Console"/>
        </Root>
    </Loggers>
</Configuration>
```

```xml
<!-- Production: Concise, performance-focused -->
<Configuration monitorInterval="60">
    <Properties>
        <Property name="LOG_DIR">/var/logs/myapp</Property>
        <Property name="logLevel">${env:LOG_LEVEL:-INFO}</Property>
    </Properties>

    <Appenders>
        <!-- Console: Errors only -->
        <Console name="Console">
            <PatternLayout pattern="%d{ISO8601} %-5p - %m%n"/>
            <ThresholdFilter level="ERROR"/>
        </Console>

        <!-- Application logs: Daily rotation, compression -->
        <RollingFile name="AppLog"
                     fileName="${LOG_DIR}/application.log"
                     filePattern="${LOG_DIR}/archive/application-%d{yyyy-MM-dd}.log.gz">
            <PatternLayout pattern="%d{ISO8601} [%t] %-5p %c - %m%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy/>
                <SizeBasedTriggeringPolicy size="100 MB"/>
            </Policies>
            <DefaultRolloverStrategy max="30"/>
        </RollingFile>
    </Appenders>

    <Loggers>
        <Logger name="com.company" level="${logLevel}">
            <AppenderRef ref="AppLog"/>
        </Logger>
        <Root level="WARN">
            <AppenderRef ref="Console"/>
        </Root>
    </Loggers>
</Configuration>
```

### 2. Log Rotation and Retention

```xml
<RollingFile name="AppLog"
             fileName="logs/application.log"
             filePattern="logs/archive/%d{yyyy-MM}/application-%d{yyyy-MM-dd}-%i.log.gz">
    <PatternLayout pattern="%d %-5p - %m%n"/>

    <Policies>
        <!-- Roll daily at midnight -->
        <TimeBasedTriggeringPolicy interval="1" modulate="true"/>
        <!-- Or when file exceeds 100MB -->
        <SizeBasedTriggeringPolicy size="100 MB"/>
        <!-- On application startup -->
        <OnStartupTriggeringPolicy/>
    </Policies>

    <DefaultRolloverStrategy max="90">
        <!-- Delete files older than 90 days -->
        <Delete basePath="logs/archive" maxDepth="2">
            <IfFileName glob="*/application-*.log.gz"/>
            <IfLastModified age="90d"/>
        </Delete>
    </DefaultRolloverStrategy>
</RollingFile>
```

**Retention Recommendations:**
| Log Type | Retention | Reason |
|----------|-----------|--------|
| Application logs | 30-60 days | General troubleshooting |
| Error logs | 90-180 days | Long-term issue tracking |
| Audit logs | 1-7 years | Compliance requirements |
| Performance logs | 7-30 days | Short-term analysis |

### 3. Reduce Framework Noise

```xml
<Loggers>
    <!-- Application code: INFO and above -->
    <Logger name="com.company" level="INFO"/>

    <!-- Reduce framework verbosity -->
    <Logger name="org.springframework" level="WARN"/>
    <Logger name="org.hibernate" level="WARN"/>
    <Logger name="org.apache.http" level="ERROR"/>
    <Logger name="com.amazonaws" level="ERROR"/>

    <!-- SQL logging (only in development) -->
    <Logger name="org.hibernate.SQL" level="${env:SQL_LOG_LEVEL:-ERROR}"/>
    <Logger name="org.hibernate.type.descriptor.sql.BasicBinder" level="ERROR"/>

    <!-- Root logger: catch-all -->
    <Root level="WARN">
        <AppenderRef ref="Console"/>
    </Root>
</Loggers>
```

### 4. Enable Automatic Reload

```xml
<!-- Reload configuration every 60 seconds if changed -->
<Configuration status="WARN" monitorInterval="60">
    <!-- Configuration ... -->
</Configuration>
```

**Benefit**: Change log levels in production without restarting application.

---

## Performance Best Practices

### 1. Use Async Logging

```xml
<Configuration>
    <Appenders>
        <RollingFile name="FileAppender" fileName="logs/app.log">
            <PatternLayout pattern="%d %-5p - %m%n"/>
        </RollingFile>

        <!-- Async wrapper for better performance -->
        <Async name="AsyncAppender" bufferSize="512">
            <AppenderRef ref="FileAppender"/>
        </Async>
    </Appenders>

    <Loggers>
        <Root level="INFO">
            <AppenderRef ref="AsyncAppender"/>
        </Root>
    </Loggers>
</Configuration>
```

**Or use Async Loggers (better performance):**
```properties
# log4j2.component.properties
log4j2.contextSelector=org.apache.logging.log4j.core.async.AsyncLoggerContextSelector
```

### 2. Avoid Location Information in Production

```xml
<!-- Development: Include location -->
<PatternLayout pattern="%d %-5p [%t] %c.%M:%L - %m%n"/>

<!-- Production: Exclude expensive location info -->
<PatternLayout pattern="%d %-5p [%t] %c - %m%n"/>
```

**Location patterns are expensive:**
- `%C` or `%class`: Class name
- `%M` or `%method`: Method name
- `%L` or `%line`: Line number
- `%l`: Complete location

### 3. Enable Garbage-Free Logging

```properties
# System properties or log4j2.component.properties
log4j2.enable.threadlocals=true
log4j2.enable.direct.encoders=true
```

```xml
<!-- Use RandomAccessFile for better garbage-free performance -->
<RollingRandomAccessFile name="File"
                        fileName="logs/app.log"
                        filePattern="logs/app-%d{yyyy-MM-dd}.log">
    <PatternLayout pattern="%d %-5p - %m%n"/>
</RollingRandomAccessFile>
```

### 4. Guard Debug and Trace Logs

```java
// High-volume code paths
for (int i = 0; i < 1_000_000; i++) {
    // BAD: Creates overhead even if DEBUG disabled
    logger.debug("Processing record {} of {}", i, total);

    // GOOD: Guard check
    if (i % 10000 == 0 && logger.isDebugEnabled()) {
        logger.debug("Progress: {} of {} records processed", i, total);
    }
}
```

---

## Testing and Monitoring

### 1. Log Testing

```java
// Test that expected logs are generated
@Test
public void testUserCreationLogging() {
    // Arrange
    ListAppender<ILoggingEvent> listAppender = new ListAppender<>();
    listAppender.start();

    Logger logger = (Logger) LoggerFactory.getLogger(UserService.class);
    logger.addAppender(listAppender);

    // Act
    userService.createUser("testuser");

    // Assert
    List<ILoggingEvent> logs = listAppender.list;
    assertEquals(1, logs.size());
    assertEquals(Level.INFO, logs.get(0).getLevel());
    assertTrue(logs.get(0).getMessage().contains("Creating user"));
}
```

### 2. Log Monitoring

```java
// Expose metrics about logging
@Component
public class LogMetrics {
    private final Counter errorCounter = Counter.builder("log_errors_total")
            .description("Total number of ERROR logs")
            .register(registry);

    // Custom appender that increments counter
    public class MetricsAppender extends AbstractAppender {
        @Override
        public void append(LogEvent event) {
            if (event.getLevel().equals(Level.ERROR)) {
                errorCounter.increment();
            }
        }
    }
}
```

### 3. Log Aggregation

**Send logs to centralized system:**
- ELK Stack (Elasticsearch, Logstash, Kibana)
- Splunk
- Graylog
- CloudWatch (AWS)
- Stackdriver (GCP)

```xml
<!-- JSON format for log aggregation -->
<RollingFile name="JsonLog" fileName="logs/application.json">
    <JsonLayout complete="false" compact="true" eventEol="true">
        <KeyValuePair key="application" value="myapp"/>
        <KeyValuePair key="environment" value="${env:ENVIRONMENT}"/>
    </JsonLayout>
</RollingFile>
```

---

## Common Pitfalls to Avoid

### 1. Logging in Loops

```java
// BAD: Excessive logging
for (User user : users) {
    logger.debug("Processing user: {}", user.getId()); // Could be millions!
}

// GOOD: Log summary
logger.debug("Processing {} users", users.size());
// ... process ...
logger.debug("Processed {} users successfully", successCount);

// ACCEPTABLE: Log sampling
for (int i = 0; i < users.size(); i++) {
    if (i % 1000 == 0) {
        logger.debug("Progress: {} of {} users processed", i, users.size());
    }
}
```

### 2. Duplicate Logging

```java
// BAD: Logging at every layer
public void controller() {
    try {
        service.method();
    } catch (Exception e) {
        logger.error("Error in controller", e); // Logged here
        throw e;
    }
}

public void service() {
    try {
        repository.method();
    } catch (Exception e) {
        logger.error("Error in service", e); // And here!
        throw e;
    }
}

// GOOD: Log at appropriate layer only
public void controller() {
    try {
        service.method();
    } catch (Exception e) {
        logger.error("Failed to process request", e); // Log once
        throw e;
    }
}

public void service() {
    repository.method(); // Just propagate
}
```

### 3. Over-Logging

```java
// BAD: Too much detail at INFO level
logger.info("Entering createUser method");
logger.info("Validating username");
logger.info("Username is valid");
logger.info("Checking if user exists");
logger.info("User does not exist");
logger.info("Creating user object");
logger.info("Saving to database");
logger.info("User saved");
logger.info("Exiting createUser method");

// GOOD: Concise, meaningful logs
logger.info("Creating user: {}", username);
// ... business logic ...
logger.info("User created successfully: {}", userId);
```

### 4. Under-Logging

```java
// BAD: No logging
public void processPayment(Payment payment) {
    // Complex business logic
    // ...
    // No logs at all!
}

// GOOD: Log key events
public void processPayment(Payment payment) {
    logger.info("Processing payment: order={} amount={}", payment.getOrderId(), payment.getAmount());

    try {
        validatePayment(payment);
        chargeCard(payment);
        updateOrder(payment);

        logger.info("Payment processed successfully: order={} transaction={}",
                   payment.getOrderId(), transactionId);
    } catch (PaymentException e) {
        logger.error("Payment failed: order={}", payment.getOrderId(), e);
        throw e;
    }
}
```

---

## Logging Checklist

### Development

- [ ] Use SLF4J API for framework independence
- [ ] Declare static final loggers
- [ ] Use parameterized messages
- [ ] Include relevant context in log messages
- [ ] Guard expensive operations
- [ ] Use appropriate log levels
- [ ] Log exceptions with stack traces
- [ ] Avoid logging sensitive data
- [ ] Test logging in critical paths

### Configuration

- [ ] Separate configuration for dev/test/prod
- [ ] Enable async logging for performance
- [ ] Configure log rotation and retention
- [ ] Compress archived logs
- [ ] Separate error logs
- [ ] Reduce framework noise
- [ ] Enable automatic config reload
- [ ] Use environment variables for flexibility

### Production

- [ ] Monitor log volume and errors
- [ ] Set up log aggregation
- [ ] Configure alerts for error spikes
- [ ] Implement log retention policies
- [ ] Disable debug/trace in production
- [ ] Use structured logging for parsing
- [ ] Track request IDs with MDC
- [ ] Regular log review and cleanup

---

## Summary

| Category | Key Practices |
|----------|---------------|
| **Code** | Static loggers, parameterized messages, appropriate levels, guard expensive ops |
| **Security** | Never log passwords/PII, mask sensitive data, sanitize inputs |
| **Performance** | Async logging, no location info, garbage-free mode, guard debug logs |
| **Architecture** | MDC for context, separate concerns, structured logging, markers |
| **Configuration** | Environment-specific, rotation/retention, reduce noise, async appenders |
| **Operations** | Monitoring, aggregation, alerts, regular reviews |

## Conclusion

Effective logging requires:
1. **Clarity**: Clear, contextual messages
2. **Performance**: Minimal overhead
3. **Security**: Protect sensitive data
4. **Maintainability**: Consistent patterns
5. **Operability**: Easy to monitor and analyze

Follow these best practices to build robust, maintainable, and observable applications.

---

**End of Logging Module**

Continue to the next module in the curriculum to expand your Java Full Stack development skills.
