# Layouts (Pattern Layout)

## What are Layouts?

Layouts in Log4J are responsible for formatting log events into strings before they are written by appenders. They transform structured log event data (timestamp, level, logger name, message, exception) into a readable format according to a specified pattern or structure.

### Layout Flow

```
LogEvent (structured data)
    |
    v
Layout (formatter)
    |
    v
Formatted String
    |
    v
Appender (writes to destination)
```

---

## Layout Types

Log4J 2 provides several layout types:

| Layout Type | Purpose | Format |
|------------|---------|---------|
| **PatternLayout** | Flexible pattern-based formatting | Customizable text |
| **JsonLayout** | JSON-formatted logs | JSON |
| **XmlLayout** | XML-formatted logs | XML |
| **CsvLayout** | Comma-separated values | CSV |
| **HtmlLayout** | HTML table format | HTML |
| **RFC5424Layout** | Syslog format | RFC 5424 |
| **SerializedLayout** | Java serialization | Binary |

**Most Common**: PatternLayout is used in 95% of cases due to its flexibility and readability.

---

## Pattern Layout

Pattern Layout uses conversion patterns to format log messages. Patterns are strings containing literal text and conversion specifiers.

### Basic Pattern Syntax

```
%[conversion character]{pattern}{style}
```

**Example:**
```
%d{yyyy-MM-dd HH:mm:ss} %-5level %logger{36} - %msg%n
```

### Essential Conversion Patterns

| Pattern | Description | Example Output |
|---------|-------------|----------------|
| `%d` | Date/time | `2025-12-12 14:30:45,123` |
| `%p` or `%level` | Log level | `INFO` |
| `%c` or `%logger` | Logger name | `com.company.UserService` |
| `%C` or `%class` | Class name | `UserService` |
| `%M` or `%method` | Method name | `createUser` |
| `%L` or `%line` | Line number | `42` |
| `%m` or `%msg` | Log message | `User created successfully` |
| `%n` | Line separator | (newline) |
| `%t` or `%thread` | Thread name | `main` or `http-nio-8080-exec-1` |
| `%throwable` or `%ex` | Exception stack trace | Full stack trace |

### Common Pattern Formats

#### 1. Simple Development Format

```xml
<PatternLayout pattern="%d{HH:mm:ss.SSS} %-5level - %msg%n"/>
```

**Output:**
```
14:30:45.123 INFO  - User logged in
14:30:45.456 ERROR - Database connection failed
```

#### 2. Standard Production Format

```xml
<PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
```

**Output:**
```
2025-12-12 14:30:45.123 [main] INFO  com.company.UserService - User logged in
2025-12-12 14:30:45.456 [http-nio-8080-exec-1] ERROR com.company.DatabaseService - Connection failed
```

#### 3. Detailed Debug Format

```xml
<PatternLayout pattern="%d{ISO8601} [%t] %-5level %logger{36}.%M:%L - %msg%n%throwable"/>
```

**Output:**
```
2025-12-12T14:30:45,123 [main] DEBUG com.company.UserService.createUser:42 - Validating email
2025-12-12T14:30:45,456 [main] ERROR com.company.UserService.createUser:45 - Validation failed
java.lang.IllegalArgumentException: Invalid email format
    at com.company.UserService.validateEmail(UserService.java:78)
    at com.company.UserService.createUser(UserService.java:45)
```

#### 4. Compact Production Format

```xml
<PatternLayout pattern="%d{ISO8601}|%-5p|%c{1}|%m%n"/>
```

**Output:**
```
2025-12-12T14:30:45,123|INFO |UserService|User logged in
2025-12-12T14:30:45,456|ERROR|DatabaseService|Connection failed
```

---

## Date/Time Formatting

The `%d` pattern supports various date formats:

### Date Format Examples

| Pattern | Example Output | Use Case |
|---------|----------------|----------|
| `%d` | `2025-12-12 14:30:45,123` | Default format |
| `%d{ISO8601}` | `2025-12-12T14:30:45,123` | ISO standard |
| `%d{yyyy-MM-dd HH:mm:ss}` | `2025-12-12 14:30:45` | Custom format |
| `%d{HH:mm:ss.SSS}` | `14:30:45.123` | Time only |
| `%d{yyyy-MM-dd}` | `2025-12-12` | Date only |
| `%d{UNIX}` | `1734007845` | Unix timestamp |
| `%d{UNIX_MILLIS}` | `1734007845123` | Unix timestamp (ms) |

### Common Date Patterns

```xml
<!-- ISO 8601 format (recommended for international applications) -->
<PatternLayout pattern="%d{ISO8601} %-5p - %m%n"/>

<!-- Human-readable format -->
<PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} %-5p - %m%n"/>

<!-- Compact format (time only for development) -->
<PatternLayout pattern="%d{HH:mm:ss} %-5p - %m%n"/>

<!-- Unix timestamp (for log analysis tools) -->
<PatternLayout pattern="%d{UNIX_MILLIS}|%-5p|%m%n"/>
```

---

## Pattern Modifiers

Modifiers control the formatting and alignment of conversion patterns.

### Width and Alignment

| Pattern | Description | Example Input | Example Output |
|---------|-------------|---------------|----------------|
| `%20logger` | Right-align, min 20 chars | `UserService` | `        UserService` |
| `%-20logger` | Left-align, min 20 chars | `UserService` | `UserService        ` |
| `%.20logger` | Truncate from beginning | `com.company.service.UserService` | `vice.UserService` |
| `%20.30logger` | Min 20, max 30 chars | `UserService` | `        UserService` |
| `%-5level` | Left-align level (5 chars) | `INFO` | `INFO ` |

### Examples

```xml
<!-- Fixed-width columns for better readability -->
<PatternLayout pattern="%d{HH:mm:ss} %-5level %-30logger{30} - %msg%n"/>
```

**Output:**
```
14:30:45 INFO  com.company.UserService        - User created
14:30:45 DEBUG com.company.DatabaseService    - Query executed
14:30:45 ERROR com.c.v.l.n.DeepNestedClass    - Error occurred
```

### Logger Name Abbreviation

The `%logger{n}` pattern can abbreviate package names:

| Pattern | Logger Name | Output |
|---------|-------------|--------|
| `%logger` | `com.company.service.UserService` | `com.company.service.UserService` |
| `%logger{0}` | `com.company.service.UserService` | `UserService` |
| `%logger{1}` | `com.company.service.UserService` | `c.c.s.UserService` |
| `%logger{2}` | `com.company.service.UserService` | `c.c.service.UserService` |
| `%logger{36}` | `com.company.service.UserService` | `com.company.service.UserService` (max 36 chars) |
| `%c{1}` | `com.company.service.UserService` | `UserService` |

**Common Usage:**
```xml
<!-- Show only class name -->
<PatternLayout pattern="%d %-5p %c{1} - %m%n"/>

<!-- Show package + class (abbreviated) -->
<PatternLayout pattern="%d %-5p %c{2} - %m%n"/>
```

---

## Color and Styling

Log4J 2 supports ANSI color codes for console output.

### Highlight Pattern

```xml
<Console name="Console">
    <PatternLayout pattern="%d %highlight{%-5level} %logger{36} - %msg%n"/>
</Console>
```

**Default Colors:**
- FATAL: Bright Red
- ERROR: Red
- WARN: Yellow
- INFO: Green
- DEBUG: Cyan
- TRACE: Blue

### Custom Highlight Colors

```xml
<PatternLayout pattern="%d %highlight{%-5level}{FATAL=bright red, ERROR=red, WARN=yellow, INFO=bright green, DEBUG=cyan, TRACE=blue} - %msg%n"/>
```

### Style Pattern

```xml
<PatternLayout pattern="%d{HH:mm:ss} %highlight{%-5level} %style{%logger{36}}{cyan} - %msg%n"/>
```

**Available Colors:**
- black, red, green, yellow, blue, magenta, cyan, white
- bright black, bright red, bright green, etc.

### Complete Colored Example

```xml
<Console name="ColorConsole">
    <PatternLayout>
        <Pattern>%style{%d{ISO8601}}{bright black} %highlight{%-5level}{FATAL=bright red blink, ERROR=red, WARN=yellow bold, INFO=green, DEBUG=cyan, TRACE=blue} %style{[%t]}{bright black} %style{%c{1.}}{bright cyan} - %msg%n%throwable</Pattern>
    </PatternLayout>
</Console>
```

**Note**: Colors only work in console appenders, not file appenders.

---

## Advanced Pattern Features

### 1. Exception Handling

| Pattern | Description | Output |
|---------|-------------|--------|
| `%throwable` | Full stack trace | Complete exception info |
| `%throwable{short}` | First line only | Exception message only |
| `%throwable{10}` | First 10 lines | Partial stack trace |
| `%throwable{none}` | No stack trace | (nothing) |
| `%throwable{separator(...)}` | Custom separator | Modified format |

**Examples:**

```xml
<!-- Full stack trace -->
<PatternLayout pattern="%d %-5p - %m%n%throwable"/>

<!-- Exception message only -->
<PatternLayout pattern="%d %-5p - %m%n%throwable{short}"/>

<!-- First 5 lines of stack trace -->
<PatternLayout pattern="%d %-5p - %m%n%throwable{5}"/>
```

### 2. Thread Information

```xml
<!-- Thread name -->
<PatternLayout pattern="%d [%t] %-5p - %m%n"/>

<!-- Thread ID -->
<PatternLayout pattern="%d [%T] %-5p - %m%n"/>

<!-- Thread priority -->
<PatternLayout pattern="%d [%t|%tp] %-5p - %m%n"/>
```

### 3. Location Information

**Warning**: Location information (%C, %M, %L) is expensive to compute.

```xml
<!-- Class name -->
%C or %class

<!-- Method name -->
%M or %method

<!-- Line number -->
%L or %line

<!-- Complete location -->
%l (lowercase L) - shows Class.method(File.java:line)
```

**Example:**
```xml
<PatternLayout pattern="%d %-5p [%t] %C.%M:%L - %m%n"/>
```

**Output:**
```
2025-12-12 14:30:45 INFO  [main] com.company.UserService.createUser:42 - User created
```

**Performance Note**: Only use in development. Disable in production:

```xml
<!-- Production: No location info -->
<PatternLayout pattern="%d %-5p [%t] %c - %m%n"/>

<!-- Development: Include location -->
<PatternLayout pattern="%d %-5p [%t] %c.%M:%L - %m%n"/>
```

### 4. Markers

Markers provide additional categorization:

```java
import org.apache.logging.log4j.Marker;
import org.apache.logging.log4j.MarkerManager;

public class UserService {
    private static final Logger logger = LogManager.getLogger(UserService.class);
    private static final Marker AUDIT = MarkerManager.getMarker("AUDIT");
    private static final Marker PERFORMANCE = MarkerManager.getMarker("PERFORMANCE");

    public void createUser(String username) {
        logger.info(AUDIT, "User created: {}", username);
    }

    public void logPerformance(long duration) {
        logger.info(PERFORMANCE, "Operation took {} ms", duration);
    }
}
```

**Pattern:**
```xml
<PatternLayout pattern="%d %-5p [%marker] - %m%n"/>
```

**Output:**
```
2025-12-12 14:30:45 INFO  [AUDIT] - User created: john
2025-12-12 14:30:45 INFO  [PERFORMANCE] - Operation took 250 ms
```

### 5. Map Data Context (MDC)

MDC allows contextual data in logs:

```java
import org.apache.logging.log4j.ThreadContext;

public class UserController {
    @PostMapping("/users")
    public User createUser(@RequestBody UserDTO dto) {
        // Add context
        ThreadContext.put("userId", dto.getUsername());
        ThreadContext.put("requestId", UUID.randomUUID().toString());

        try {
            User user = userService.createUser(dto);
            return user;
        } finally {
            // Clean up
            ThreadContext.clearAll();
        }
    }
}
```

**Pattern:**
```xml
<PatternLayout pattern="%d %-5p [%X{requestId}] [%X{userId}] - %m%n"/>
```

**Output:**
```
2025-12-12 14:30:45 INFO  [a1b2c3d4-e5f6-7890] [john] - User created successfully
```

---

## Complete Pattern Examples

### 1. Development Environment

```xml
<Console name="Console">
    <PatternLayout>
        <Pattern>%d{HH:mm:ss.SSS} %highlight{%-5level}{FATAL=red blink, ERROR=red, WARN=yellow, INFO=green, DEBUG=cyan, TRACE=blue} [%t] %style{%c{1.}}{cyan} - %msg%n%throwable</Pattern>
    </PatternLayout>
</Console>
```

**Features:**
- Time only (no date needed in dev)
- Color-coded levels
- Thread name
- Short class name
- Full exception stack traces

### 2. Production Application Logs

```xml
<RollingFile name="AppFile" fileName="logs/application.log">
    <PatternLayout>
        <Pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n</Pattern>
    </PatternLayout>
</RollingFile>
```

**Features:**
- Full timestamp
- Thread name
- Standard log level
- Full logger name
- No expensive location info

### 3. Production Error Logs

```xml
<RollingFile name="ErrorFile" fileName="logs/error.log">
    <PatternLayout>
        <Pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36}.%M:%L - %msg%n%throwable</Pattern>
    </PatternLayout>
    <ThresholdFilter level="ERROR" onMatch="ACCEPT" onMismatch="DENY"/>
</RollingFile>
```

**Features:**
- Full timestamp
- Thread name
- Method and line number (acceptable for errors only)
- Full stack trace

### 4. Structured Logging (Parsing-Friendly)

```xml
<RollingFile name="StructuredLog" fileName="logs/structured.log">
    <PatternLayout>
        <Pattern>%d{ISO8601}|%p|%t|%c|%M|%L|%msg|%replace{%throwable}{[\r\n]+}{ }%n</Pattern>
    </PatternLayout>
</RollingFile>
```

**Output:**
```
2025-12-12T14:30:45,123|INFO|main|com.company.UserService|createUser|42|User created|
2025-12-12T14:30:45,456|ERROR|main|com.company.UserService|createUser|45|Error occurred|java.lang.Exception: test at com.company.UserService.createUser(UserService.java:45)
```

**Benefits:**
- Easy to parse with scripts
- Each field separated by delimiter
- Exception on same line (replace newlines with spaces)

### 5. JSON Layout (Alternative to Pattern)

```xml
<RollingFile name="JsonLog" fileName="logs/application.json">
    <JsonLayout complete="false" compact="true" eventEol="true"/>
</RollingFile>
```

**Output:**
```json
{"timeMillis":1734007845123,"thread":"main","level":"INFO","loggerName":"com.company.UserService","message":"User created","endOfBatch":false}
{"timeMillis":1734007845456,"thread":"main","level":"ERROR","loggerName":"com.company.UserService","message":"Error occurred","thrown":{"commonElementCount":0,"message":"test","name":"java.lang.Exception","extendedStackTrace":"java.lang.Exception: test\n\tat com.company.UserService.createUser(UserService.java:45)\n"},"endOfBatch":false}
```

**Use Cases:**
- Log aggregation systems (ELK, Splunk)
- Machine parsing
- Cloud logging services

---

## Pattern Layout Best Practices

### 1. Choose Appropriate Detail Level

```xml
<!-- Development: Detailed with colors -->
<PatternLayout pattern="%d{HH:mm:ss} %highlight{%-5p} [%t] %c{1}.%M:%L - %m%n%throwable"/>

<!-- Production: Standard detail -->
<PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5p %c - %m%n"/>
```

### 2. Avoid Expensive Patterns in Production

```xml
<!-- BAD: Slow performance -->
<PatternLayout pattern="%d %-5p %C.%M:%L - %m%n"/>

<!-- GOOD: Better performance -->
<PatternLayout pattern="%d %-5p %c - %m%n"/>
```

**Expensive Patterns:**
- `%C` (class name)
- `%M` (method name)
- `%L` (line number)
- `%l` (location)

**Only use these in development or error-only appenders.**

### 3. Use Consistent Patterns

Define patterns in properties for consistency:

```xml
<Properties>
    <Property name="CONSOLE_PATTERN">%d{HH:mm:ss} %highlight{%-5p} - %m%n</Property>
    <Property name="FILE_PATTERN">%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5p %c - %m%n</Property>
</Properties>

<Appenders>
    <Console name="Console">
        <PatternLayout pattern="${CONSOLE_PATTERN}"/>
    </Console>
    <RollingFile name="File" fileName="logs/app.log">
        <PatternLayout pattern="${FILE_PATTERN}"/>
    </RollingFile>
</Appenders>
```

### 4. Include Essential Information

**Minimum recommended fields:**
- Timestamp (`%d`)
- Log level (`%-5p`)
- Logger name (`%c`)
- Message (`%m`)
- Line separator (`%n`)

**Additional for production:**
- Thread name (`%t`)
- Exception stack trace (`%throwable`)

### 5. Consider Log Parsing

If logs will be parsed:
```xml
<!-- Use consistent delimiter -->
<PatternLayout pattern="%d{ISO8601}|%p|%t|%c|%m%n"/>

<!-- Or use JSON layout -->
<JsonLayout/>
```

---

## Performance Considerations

### Pattern Complexity Impact

| Pattern Complexity | Example | Performance Impact |
|-------------------|---------|-------------------|
| Simple | `%m%n` | Minimal |
| Standard | `%d %-5p - %m%n` | Low |
| Detailed | `%d [%t] %-5p %c - %m%n` | Moderate |
| Location Info | `%d %-5p %C.%M:%L - %m%n` | High |

### Optimization Tips

1. **Avoid location patterns in high-volume logging**
2. **Use async appenders for complex patterns**
3. **Consider buffered IO for file appenders**
4. **Use JSON layout for structured logs instead of complex parsing patterns**

---

## Summary

| Concept | Key Points |
|---------|------------|
| Pattern Layout | Most flexible layout using conversion patterns |
| Essential Patterns | %d (date), %-5p (level), %c (logger), %m (message), %n (newline) |
| Date Formats | ISO8601, custom formats, Unix timestamps |
| Colors | %highlight for levels, %style for custom styling (console only) |
| Advanced Features | MDC (%X), markers, exception formatting, thread info |
| Performance | Avoid %C, %M, %L in production; use async appenders for complex patterns |

## Next Topic

Continue to [Best Practices](./07-best-practices.md) to learn comprehensive logging strategies for production applications.
