# Appenders (Console, File, Rolling File)

## What are Appenders?

Appenders are components in Log4J that control where log messages are written. They define the destination for log output, such as console, files, databases, email servers, or remote logging services. Each appender can have its own configuration, layout, and filters.

### Appender Hierarchy

```
LogEvent
    |
    v
Logger --> Appender --> Layout --> Destination
              |           |
              v           v
           Filter     Formatter
```

**Flow:**
1. Logger receives log event
2. Appender processes event (applies filters)
3. Layout formats the message
4. Output written to destination

---

## Common Appender Types

| Appender Type | Purpose | Use Case |
|--------------|---------|----------|
| **ConsoleAppender** | Writes to console (stdout/stderr) | Development, debugging |
| **FileAppender** | Writes to a file | Simple logging needs |
| **RollingFileAppender** | Writes to files with rotation | Production applications |
| **AsyncAppender** | Asynchronous logging | High-performance applications |
| **JDBCAppender** | Writes to database | Centralized log storage |
| **SMTPAppender** | Sends email | Critical error notifications |
| **SyslogAppender** | Sends to syslog server | Unix/Linux system integration |

---

## Console Appender

The Console Appender writes log messages to the console (standard output or standard error).

### Basic Console Configuration

```xml
<Console name="Console" target="SYSTEM_OUT">
    <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
</Console>
```

**Attributes:**
- `name`: Unique identifier for the appender
- `target`: `SYSTEM_OUT` (stdout) or `SYSTEM_ERR` (stderr)

### Console Appender with Colors

```xml
<Console name="ColorConsole" target="SYSTEM_OUT">
    <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %highlight{%-5level}{FATAL=red, ERROR=red, WARN=yellow, INFO=green, DEBUG=cyan, TRACE=blue} %logger{36} - %msg%n"/>
</Console>
```

**Color Patterns:**
- `%highlight{pattern}`: Automatically colors based on log level
- `%style{pattern}{color}`: Custom color for specific parts

### Advanced Console Example

```xml
<Console name="Console" target="SYSTEM_OUT" follow="true">
    <PatternLayout>
        <Pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %highlight{%-5level}{FATAL=bright red, ERROR=red, WARN=yellow, INFO=green, DEBUG=cyan, TRACE=blue} %style{%logger{36}}{bright black} - %msg%n</Pattern>
    </PatternLayout>
    <ThresholdFilter level="DEBUG" onMatch="ACCEPT" onMismatch="DENY"/>
</Console>
```

**Features:**
- `follow="true"`: Recreates output stream if System.out is reassigned
- Threshold filter: Only logs DEBUG and above
- Color highlighting: Different colors for different levels
- Styled logger name: Bright black for logger name

### Separate Error Console

```xml
<Appenders>
    <!-- Standard output for INFO and below -->
    <Console name="ConsoleOut" target="SYSTEM_OUT">
        <PatternLayout pattern="%d %-5p - %m%n"/>
        <ThresholdFilter level="WARN" onMatch="DENY" onMismatch="ACCEPT"/>
    </Console>

    <!-- Standard error for WARN and above -->
    <Console name="ConsoleErr" target="SYSTEM_ERR">
        <PatternLayout pattern="%d %-5p [%t] %c - %m%n"/>
        <ThresholdFilter level="WARN" onMatch="ACCEPT" onMismatch="DENY"/>
    </Console>
</Appenders>

<Loggers>
    <Root level="INFO">
        <AppenderRef ref="ConsoleOut"/>
        <AppenderRef ref="ConsoleErr"/>
    </Root>
</Loggers>
```

---

## File Appender

The File Appender writes log messages to a file. It's simple but doesn't support automatic rotation.

### Basic File Configuration

```xml
<File name="FileAppender" fileName="logs/application.log">
    <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
</File>
```

**Attributes:**
- `name`: Unique identifier
- `fileName`: Path to log file (can use variables)
- `append`: true (default) or false (overwrite on each startup)
- `immediateFlush`: true (default, slower but safer) or false (buffer writes)

### File Appender with Properties

```xml
<Configuration>
    <Properties>
        <Property name="logPath">/var/logs/myapp</Property>
        <Property name="filename">application.log</Property>
    </Properties>

    <Appenders>
        <File name="File" fileName="${logPath}/${filename}" append="true" immediateFlush="true">
            <PatternLayout pattern="%d{ISO8601} [%t] %-5level %logger{36} - %msg%n"/>
        </File>
    </Appenders>
</Configuration>
```

### Multiple File Appenders

```xml
<Appenders>
    <!-- General application logs -->
    <File name="AppLog" fileName="logs/application.log">
        <PatternLayout pattern="%d %-5p - %m%n"/>
    </File>

    <!-- Error logs only -->
    <File name="ErrorLog" fileName="logs/error.log">
        <PatternLayout pattern="%d %-5p [%t] %C{1.} - %m%n%throwable"/>
        <ThresholdFilter level="ERROR" onMatch="ACCEPT" onMismatch="DENY"/>
    </File>

    <!-- SQL queries -->
    <File name="SqlLog" fileName="logs/sql.log">
        <PatternLayout pattern="%d %-5p - %m%n"/>
    </File>
</Appenders>

<Loggers>
    <Logger name="com.company" level="INFO">
        <AppenderRef ref="AppLog"/>
    </Logger>

    <Logger name="org.hibernate.SQL" level="DEBUG" additivity="false">
        <AppenderRef ref="SqlLog"/>
    </Logger>

    <Root level="ERROR">
        <AppenderRef ref="ErrorLog"/>
    </Root>
</Loggers>
```

**When to Use File Appender:**
- Simple applications with predictable log volume
- Logs managed by external rotation tools
- Development/testing environments
- When file size won't grow excessively

**Limitations:**
- No automatic rotation (file grows indefinitely)
- Manual management required in production
- Can run out of disk space

---

## Rolling File Appender

The Rolling File Appender automatically creates new log files based on size, time, or other criteria. This is essential for production applications.

### Rolling File Architecture

```
Current Log File: application.log
    |
    v (trigger condition met)
Rollover Strategy
    |
    +-- Rename current file to: application-2025-12-12-1.log
    +-- Compress: application-2025-12-12-1.log.gz
    +-- Create new: application.log
    +-- Delete old files (per retention policy)
```

### Time-Based Rolling

Create a new file every day:

```xml
<RollingFile name="RollingFile"
             fileName="logs/application.log"
             filePattern="logs/application-%d{yyyy-MM-dd}.log">
    <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
    <Policies>
        <TimeBasedTriggeringPolicy interval="1" modulate="true"/>
    </Policies>
    <DefaultRolloverStrategy max="30"/>
</RollingFile>
```

**Explanation:**
- `fileName`: Current active log file
- `filePattern`: Pattern for rolled files (includes date)
- `TimeBasedTriggeringPolicy interval="1"`: Roll daily
- `modulate="true"`: Roll at midnight (not 24 hours from start)
- `max="30"`: Keep 30 days of logs

### Size-Based Rolling

Create a new file when size exceeds limit:

```xml
<RollingFile name="RollingFile"
             fileName="logs/application.log"
             filePattern="logs/application-%i.log.gz">
    <PatternLayout pattern="%d %-5p [%t] %c - %m%n"/>
    <Policies>
        <SizeBasedTriggeringPolicy size="10 MB"/>
    </Policies>
    <DefaultRolloverStrategy max="20"/>
</RollingFile>
```

**Explanation:**
- `%i`: Index number for rolled files
- `size="10 MB"`: Roll when file reaches 10MB
- `.gz`: Compress rolled files
- `max="20"`: Keep maximum 20 rolled files

### Combined Time and Size Rolling

Most common production configuration:

```xml
<RollingFile name="RollingFile"
             fileName="logs/application.log"
             filePattern="logs/application-%d{yyyy-MM-dd}-%i.log.gz">
    <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
    <Policies>
        <TimeBasedTriggeringPolicy interval="1" modulate="true"/>
        <SizeBasedTriggeringPolicy size="100 MB"/>
    </Policies>
    <DefaultRolloverStrategy max="30">
        <Delete basePath="logs" maxDepth="1">
            <IfFileName glob="application-*.log.gz"/>
            <IfLastModified age="30d"/>
        </Delete>
    </DefaultRolloverStrategy>
</RollingFile>
```

**Behavior:**
- Creates new file daily at midnight
- Also creates new file if any file exceeds 100MB
- Compresses rolled files (.gz)
- Keeps 30 rolled files
- Deletes files older than 30 days

**File Pattern Examples:**
```
application.log                 (current)
application-2025-12-12-1.log.gz (first rollover today)
application-2025-12-12-2.log.gz (second rollover today)
application-2025-12-11-1.log.gz (yesterday's log)
```

### Advanced Rolling Configuration

```xml
<RollingFile name="AdvancedRolling"
             fileName="logs/app.log"
             filePattern="logs/$${date:yyyy-MM}/app-%d{yyyy-MM-dd}-%i.log.gz">
    <PatternLayout>
        <Pattern>%d{ISO8601} [%t] %-5level %logger{36} - %msg%n</Pattern>
    </PatternLayout>

    <Policies>
        <!-- Roll every hour -->
        <TimeBasedTriggeringPolicy interval="1" modulate="true"/>
        <!-- Or when file exceeds 50MB -->
        <SizeBasedTriggeringPolicy size="50 MB"/>
        <!-- Or on application startup -->
        <OnStartupTriggeringPolicy/>
    </Policies>

    <DefaultRolloverStrategy max="100">
        <!-- Delete files older than 90 days -->
        <Delete basePath="logs" maxDepth="2">
            <IfFileName glob="*/app-*.log.gz"/>
            <IfLastModified age="90d"/>
        </Delete>
    </DefaultRolloverStrategy>
</RollingFile>
```

**Features:**
- `$${date:yyyy-MM}`: Creates monthly subdirectories
- `OnStartupTriggeringPolicy`: Rolls on application restart
- `maxDepth="2"`: Searches in subdirectories
- Custom retention: 90 days

### Hourly Rolling

```xml
<RollingFile name="HourlyRolling"
             fileName="logs/application.log"
             filePattern="logs/application-%d{yyyy-MM-dd-HH}.log.gz">
    <PatternLayout pattern="%d %-5p - %m%n"/>
    <Policies>
        <TimeBasedTriggeringPolicy interval="1" modulate="true"/>
    </Policies>
    <DefaultRolloverStrategy max="168"/> <!-- 7 days of hourly logs -->
</RollingFile>
```

---

## Complete Production Example

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN" monitorInterval="60">
    <Properties>
        <Property name="LOG_DIR">/var/logs/myapp</Property>
        <Property name="PATTERN">%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n</Property>
    </Properties>

    <Appenders>
        <!-- Console: Errors only in production -->
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="${PATTERN}"/>
            <ThresholdFilter level="ERROR" onMatch="ACCEPT" onMismatch="DENY"/>
        </Console>

        <!-- Application logs: Daily rotation with size limit -->
        <RollingFile name="AppLog"
                     fileName="${LOG_DIR}/application.log"
                     filePattern="${LOG_DIR}/archive/application-%d{yyyy-MM-dd}-%i.log.gz">
            <PatternLayout pattern="${PATTERN}"/>
            <Policies>
                <TimeBasedTriggeringPolicy interval="1" modulate="true"/>
                <SizeBasedTriggeringPolicy size="100 MB"/>
            </Policies>
            <DefaultRolloverStrategy max="30">
                <Delete basePath="${LOG_DIR}/archive" maxDepth="1">
                    <IfFileName glob="application-*.log.gz"/>
                    <IfLastModified age="30d"/>
                </Delete>
            </DefaultRolloverStrategy>
        </RollingFile>

        <!-- Error logs: Separate file, longer retention -->
        <RollingFile name="ErrorLog"
                     fileName="${LOG_DIR}/error.log"
                     filePattern="${LOG_DIR}/archive/error-%d{yyyy-MM-dd}.log.gz">
            <PatternLayout pattern="${PATTERN}"/>
            <ThresholdFilter level="ERROR" onMatch="ACCEPT" onMismatch="DENY"/>
            <Policies>
                <TimeBasedTriggeringPolicy interval="1" modulate="true"/>
            </Policies>
            <DefaultRolloverStrategy max="90"/>
        </RollingFile>

        <!-- Performance logs: High-volume, shorter retention -->
        <RollingFile name="PerfLog"
                     fileName="${LOG_DIR}/performance.log"
                     filePattern="${LOG_DIR}/archive/performance-%d{yyyy-MM-dd-HH}-%i.log.gz">
            <PatternLayout pattern="%d{ISO8601}|%m%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy interval="1" modulate="true"/>
                <SizeBasedTriggeringPolicy size="50 MB"/>
            </Policies>
            <DefaultRolloverStrategy max="48"/> <!-- 2 days of hourly logs -->
        </RollingFile>

        <!-- Audit logs: Long-term retention, no deletion -->
        <RollingFile name="AuditLog"
                     fileName="${LOG_DIR}/audit.log"
                     filePattern="${LOG_DIR}/audit/audit-%d{yyyy-MM-dd}.log.gz">
            <PatternLayout pattern="%d{ISO8601}|%m%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy interval="1" modulate="true"/>
            </Policies>
            <!-- No deletion policy - keep forever -->
        </RollingFile>
    </Appenders>

    <Loggers>
        <!-- Application logger -->
        <Logger name="com.company" level="INFO">
            <AppenderRef ref="AppLog"/>
        </Logger>

        <!-- Performance logger -->
        <Logger name="com.company.monitoring" level="INFO" additivity="false">
            <AppenderRef ref="PerfLog"/>
        </Logger>

        <!-- Audit logger -->
        <Logger name="com.company.audit" level="INFO" additivity="false">
            <AppenderRef ref="AuditLog"/>
        </Logger>

        <!-- Root logger -->
        <Root level="WARN">
            <AppenderRef ref="Console"/>
            <AppenderRef ref="ErrorLog"/>
        </Root>
    </Loggers>
</Configuration>
```

---

## Appender Performance Considerations

### Immediate Flush

```xml
<!-- Safer but slower: flush after every write -->
<File name="SafeLog" fileName="logs/app.log" immediateFlush="true">
    <PatternLayout pattern="%d %-5p - %m%n"/>
</File>

<!-- Faster but risky: buffer writes -->
<File name="FastLog" fileName="logs/app.log" immediateFlush="false">
    <PatternLayout pattern="%d %-5p - %m%n"/>
</File>
```

**Trade-off:**
- `immediateFlush="true"`: Slower but safer (no data loss if app crashes)
- `immediateFlush="false"`: Faster but may lose buffered logs on crash

### Buffered IO

```xml
<File name="BufferedLog" fileName="logs/app.log" bufferedIO="true" bufferSize="8192">
    <PatternLayout pattern="%d %-5p - %m%n"/>
</File>
```

**Performance Tip:** Use buffered IO for better performance in high-volume logging.

### Async Appenders

For high-performance needs (covered in detail in Log4J 2 Architecture topic):

```xml
<Appenders>
    <!-- Actual file appender -->
    <RollingFile name="FileAppender" fileName="logs/app.log">
        <PatternLayout pattern="%d %-5p - %m%n"/>
        <Policies>
            <SizeBasedTriggeringPolicy size="100 MB"/>
        </Policies>
    </RollingFile>

    <!-- Async wrapper -->
    <Async name="AsyncAppender">
        <AppenderRef ref="FileAppender"/>
    </Async>
</Appenders>

<Loggers>
    <Root level="INFO">
        <AppenderRef ref="AsyncAppender"/>
    </Root>
</Loggers>
```

---

## Appender Best Practices

### 1. Use Rolling Files in Production

```xml
<!-- GOOD: Automatic rotation prevents disk space issues -->
<RollingFile name="AppLog" fileName="logs/app.log"
             filePattern="logs/app-%d{yyyy-MM-dd}.log.gz">
    <Policies>
        <TimeBasedTriggeringPolicy/>
    </Policies>
    <DefaultRolloverStrategy max="30"/>
</RollingFile>

<!-- BAD: File grows indefinitely -->
<File name="AppLog" fileName="logs/app.log">
    <PatternLayout pattern="%d %-5p - %m%n"/>
</File>
```

### 2. Compress Archived Logs

```xml
<!-- Use .gz extension to enable compression -->
filePattern="logs/app-%d{yyyy-MM-dd}.log.gz"
```

**Benefits:**
- Saves disk space (typically 90% reduction)
- Faster to transfer/backup
- Minimal CPU overhead

### 3. Separate Error Logs

```xml
<!-- Makes troubleshooting easier -->
<RollingFile name="ErrorLog" fileName="logs/error.log">
    <ThresholdFilter level="ERROR" onMatch="ACCEPT" onMismatch="DENY"/>
</RollingFile>
```

### 4. Implement Retention Policies

```xml
<DefaultRolloverStrategy max="30">
    <Delete basePath="logs" maxDepth="1">
        <IfFileName glob="app-*.log.gz"/>
        <IfLastModified age="30d"/>
    </Delete>
</DefaultRolloverStrategy>
```

### 5. Use Variables for Paths

```xml
<Properties>
    <Property name="LOG_DIR">${sys:log.dir:-${env:LOG_DIR:-./logs}}</Property>
</Properties>

<RollingFile fileName="${LOG_DIR}/app.log">
```

### 6. Consider Different Retention by Type

```xml
<!-- Error logs: long retention -->
<RollingFile name="ErrorLog" filePattern="logs/error-%d{yyyy-MM-dd}.log.gz">
    <DefaultRolloverStrategy max="90"/>
</RollingFile>

<!-- Debug logs: short retention -->
<RollingFile name="DebugLog" filePattern="logs/debug-%d{yyyy-MM-dd}.log.gz">
    <DefaultRolloverStrategy max="7"/>
</RollingFile>

<!-- Audit logs: permanent retention -->
<RollingFile name="AuditLog" filePattern="audit/audit-%d{yyyy-MM-dd}.log.gz">
    <!-- No delete policy -->
</RollingFile>
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Console Appender | Writes to stdout/stderr, good for development, supports colors |
| File Appender | Simple file writing, no rotation, suitable for low-volume logs |
| Rolling File | Automatic rotation by time/size, essential for production |
| Rolling Policies | TimeBasedTriggeringPolicy, SizeBasedTriggeringPolicy, OnStartupTriggeringPolicy |
| Retention | Use DefaultRolloverStrategy with Delete policies to manage disk space |
| Best Practices | Use rolling files in production, compress archives, separate error logs |

## Next Topic

Continue to [Layouts (Pattern Layout)](./04-layouts.md) to learn how to format log messages effectively.
