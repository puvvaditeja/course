# Log4J Framework

## What is Log4J?

Log4J is a Java-based logging framework that provides a flexible way to log application events, errors, and debugging information. Log4J 2 is the current version, offering better performance and features than Log4J 1.x.

### Why Use Logging Frameworks?

| Aspect | System.out.println() | Logging Framework |
|--------|---------------------|-------------------|
| Control | No control over output | Configurable levels and targets |
| Performance | Always executes | Can be disabled by level |
| Output Target | Console only | Console, files, databases, etc. |
| Production Use | Hard to disable | Easy to adjust via configuration |
| Log Rotation | No support | Built-in file rotation |

---

## Log Levels

Log levels categorize messages by severity:

```
FATAL > ERROR > WARN > INFO > DEBUG > TRACE
```

| Level | Purpose | Example |
|-------|---------|---------|
| **TRACE** | Very detailed flow | Method entry/exit |
| **DEBUG** | Diagnostic information | Variable values, query results |
| **INFO** | Important business events | User logged in, order placed |
| **WARN** | Potential issues | API timeout, deprecated API used |
| **ERROR** | Errors requiring attention | Database connection failed |
| **FATAL** | Critical system failure | Out of memory |

**Rule**: Setting level to INFO shows INFO, WARN, ERROR, FATAL (hides DEBUG, TRACE).

### Basic Usage

```java
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

public class UserService {
    private static final Logger logger = LogManager.getLogger(UserService.class);

    public void createUser(String username) {
        logger.info("Creating user: {}", username);

        try {
            // Business logic
            logger.debug("Validating username: {}", username);
            logger.info("User created successfully: {}", username);
        } catch (Exception e) {
            logger.error("Failed to create user: {}", username, e);
        }
    }
}
```

---

## Log4J Configuration

### Maven Dependencies

```xml
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.21.1</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.21.1</version>
</dependency>
```

### Configuration File (log4j2.xml)

Place `log4j2.xml` in `src/main/resources/`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
    <Appenders>
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Root level="INFO">
            <AppenderRef ref="Console"/>
        </Root>
    </Loggers>
</Configuration>
```

### Configuration Elements

| Element | Purpose |
|---------|---------|
| `Configuration` | Root element, `status` sets internal logging level |
| `Appenders` | Defines where logs go (console, files, etc.) |
| `Loggers` | Configures logging levels per package/class |
| `Root` | Default logger for all classes |

---

## Appenders

Appenders control where log messages are written.

### Console Appender

Writes to standard output (development):

```xml
<Console name="Console" target="SYSTEM_OUT">
    <PatternLayout pattern="%d{HH:mm:ss.SSS} %-5level %logger{36} - %msg%n"/>
</Console>
```

### File Appender

Writes to a single file:

```xml
<File name="FileAppender" fileName="logs/application.log">
    <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} %-5level %logger{36} - %msg%n"/>
</File>
```

### Rolling File Appender

Rotates files by time or size (production):

```xml
<RollingFile name="RollingFile"
             fileName="logs/app.log"
             filePattern="logs/app-%d{yyyy-MM-dd}-%i.log.gz">
    <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
    <Policies>
        <TimeBasedTriggeringPolicy interval="1" modulate="true"/>
        <SizeBasedTriggeringPolicy size="10MB"/>
    </Policies>
    <DefaultRolloverStrategy max="30"/>
</RollingFile>
```

**Policies:**
- `TimeBasedTriggeringPolicy`: Roll daily at midnight
- `SizeBasedTriggeringPolicy`: Roll when file exceeds size
- `DefaultRolloverStrategy max="30"`: Keep 30 archived files

---

## Layouts (Pattern Layout)

Layouts format log messages using conversion patterns.

### Common Patterns

| Pattern | Output | Description |
|---------|--------|-------------|
| `%d{HH:mm:ss}` | 14:30:45 | Time |
| `%d{yyyy-MM-dd HH:mm:ss}` | 2024-01-15 14:30:45 | Date and time |
| `%-5level` | INFO  | Level (5 chars, left-aligned) |
| `%logger{36}` | c.e.UserService | Logger name (max 36 chars) |
| `%msg` | message text | Log message |
| `%n` | newline | Line separator |
| `%t` | main | Thread name |
| `%ex` | stack trace | Exception |

### Pattern Examples

```xml
<!-- Simple (development) -->
<PatternLayout pattern="%d{HH:mm:ss} %-5level - %msg%n"/>
<!-- Output: 14:30:45 INFO  - User logged in -->

<!-- Standard (production) -->
<PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
<!-- Output: 2024-01-15 14:30:45 [main] INFO  c.e.UserService - User logged in -->
```

---

## Log4J 2 Architecture

### Component Flow

```
Application Code
       ↓
    Logger (checks level)
       ↓
    Appender (where to write)
       ↓
    Layout (how to format)
       ↓
    Output (console, file, etc.)
```

### Key Components

| Component | Purpose |
|-----------|---------|
| **Logger** | Entry point, created per class |
| **LoggerContext** | Container for configuration |
| **Appender** | Output destination |
| **Layout** | Message formatter |
| **Filter** | Controls which events are logged |

### Logger Hierarchy

Loggers follow package hierarchy:

```xml
<Loggers>
    <!-- Root: applies to all -->
    <Root level="WARN">
        <AppenderRef ref="Console"/>
    </Root>

    <!-- Package level -->
    <Logger name="com.myapp" level="INFO">
        <AppenderRef ref="File"/>
    </Logger>

    <!-- Class level -->
    <Logger name="com.myapp.service.UserService" level="DEBUG"/>
</Loggers>
```

---

## Complete Configuration Example

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
    <Properties>
        <Property name="LOG_PATTERN">%d{yyyy-MM-dd HH:mm:ss} [%t] %-5level %logger{36} - %msg%n</Property>
    </Properties>

    <Appenders>
        <!-- Console for development -->
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="${LOG_PATTERN}"/>
        </Console>

        <!-- Rolling file for production -->
        <RollingFile name="File"
                     fileName="logs/application.log"
                     filePattern="logs/application-%d{yyyy-MM-dd}.log.gz">
            <PatternLayout pattern="${LOG_PATTERN}"/>
            <Policies>
                <TimeBasedTriggeringPolicy/>
                <SizeBasedTriggeringPolicy size="10MB"/>
            </Policies>
            <DefaultRolloverStrategy max="30"/>
        </RollingFile>
    </Appenders>

    <Loggers>
        <!-- Application logging -->
        <Logger name="com.myapp" level="DEBUG" additivity="false">
            <AppenderRef ref="Console"/>
            <AppenderRef ref="File"/>
        </Logger>

        <!-- Reduce framework noise -->
        <Logger name="org.springframework" level="WARN"/>
        <Logger name="org.hibernate" level="WARN"/>

        <!-- Root logger -->
        <Root level="INFO">
            <AppenderRef ref="Console"/>
        </Root>
    </Loggers>
</Configuration>
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Log Levels | FATAL > ERROR > WARN > INFO > DEBUG > TRACE |
| Configuration | XML file in `src/main/resources/log4j2.xml` |
| Console Appender | Development output |
| Rolling File | Production with auto-rotation |
| Pattern Layout | `%d`, `%-5level`, `%logger`, `%msg`, `%n` |
| Logger Hierarchy | Package-based, inherits from Root |

## Next Topic

Continue to [SLF4J](./02-slf4j.md) to learn about the logging facade for framework independence.
