# Logging Quick Reference Card

## Log Levels (Priority Order)

```
FATAL > ERROR > WARN > INFO > DEBUG > TRACE
```

| Level | Purpose | Example |
|-------|---------|---------|
| TRACE | Very detailed flow | Method entry/exit |
| DEBUG | Diagnostic info | Variable values |
| INFO | Business events | User logged in |
| WARN | Potential issues | Deprecated API used |
| ERROR | Operation failures | Database connection failed |
| FATAL | System crash | Out of memory |

**Rule**: Setting level to INFO shows INFO, WARN, ERROR, FATAL (hides DEBUG, TRACE)

---

## Quick Setup

### Maven Dependencies (SLF4J + Log4J 2)

```xml
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>2.0.9</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j2-impl</artifactId>
    <version>2.21.1</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.21.1</version>
</dependency>
```

### Basic Logger Usage

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class MyService {
    private static final Logger logger = LoggerFactory.getLogger(MyService.class);

    public void doSomething(String param) {
        logger.info("Processing: {}", param);           // Parameterized
        logger.error("Failed for: {}", param, exception); // With exception
    }
}
```

---

## Essential Patterns

| Pattern | Output | Description |
|---------|--------|-------------|
| `%d{HH:mm:ss}` | 14:30:45 | Time |
| `%d{yyyy-MM-dd}` | 2024-01-15 | Date |
| `%-5level` | INFO  | Level (5 chars) |
| `%logger{36}` | c.e.MyService | Logger name |
| `%msg` | message text | Log message |
| `%n` | newline | Line separator |
| `%t` | main | Thread name |
| `%ex` | stack trace | Exception |

**Standard Pattern:**
```
%d{yyyy-MM-dd HH:mm:ss} [%t] %-5level %logger{36} - %msg%n
```

---

## Minimal Configuration (log4j2.xml)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
    <Appenders>
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss} %-5level %logger{36} - %msg%n"/>
        </Console>
        <RollingFile name="File" fileName="logs/app.log"
                     filePattern="logs/app-%d{yyyy-MM-dd}.log.gz">
            <PatternLayout pattern="%d %-5level %logger - %msg%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy/>
                <SizeBasedTriggeringPolicy size="10MB"/>
            </Policies>
            <DefaultRolloverStrategy max="30"/>
        </RollingFile>
    </Appenders>
    <Loggers>
        <Logger name="com.myapp" level="DEBUG"/>
        <Root level="INFO">
            <AppenderRef ref="Console"/>
            <AppenderRef ref="File"/>
        </Root>
    </Loggers>
</Configuration>
```

---

## MDC (Request Tracking)

```java
import org.slf4j.MDC;

// Set context
MDC.put("requestId", UUID.randomUUID().toString());
MDC.put("userId", userId);

// Use in pattern: %X{requestId} %X{userId}

// Always clean up
MDC.clear();
```

---

## Common Appenders

| Type | Use Case |
|------|----------|
| Console | Development, stdout |
| File | Simple file logging |
| RollingFile | Production (auto-rotation) |
| Async | High-performance apps |

---

## Best Practices Summary

| Do | Don't |
|----|-------|
| Use parameterized messages: `logger.info("User: {}", name)` | String concatenation: `"User: " + name` |
| Static final logger per class | Create logger in methods |
| Log exception as last param | `e.getMessage()` only |
| Use appropriate levels | Everything at INFO |
| Include context (IDs, values) | Vague messages |
| Clean up MDC in finally | Leave MDC values |

**Never log:** Passwords, credit cards, SSN, API keys, PII

---

## Environment Settings

| Environment | Root Level | App Level |
|-------------|------------|-----------|
| Development | DEBUG | DEBUG/TRACE |
| Production | WARN | INFO |

---

## Troubleshooting

- **No logs?** Check log level and appender refs
- **Duplicate logs?** Set `additivity="false"` on logger
- **Config not loading?** Place `log4j2.xml` in `src/main/resources`
- **Debug config:** Set `<Configuration status="TRACE">`
