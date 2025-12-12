# Log4J Configuration

## Configuration Overview

Log4J 2 is designed to be highly configurable without requiring code changes. Configuration can be specified in multiple formats, and Log4J will automatically detect and load the configuration file at application startup.

### Configuration File Formats

Log4J 2 supports multiple configuration formats (in order of precedence):

| Format | File Name | Pros | Cons |
|--------|-----------|------|------|
| **JSON** | log4j2.json | Structured, easy to parse programmatically | More verbose than XML |
| **YAML** | log4j2.yaml, log4j2.yml | Human-readable, concise | Requires additional dependency |
| **XML** | log4j2.xml | Most commonly used, well-documented | Can be verbose |
| **Properties** | log4j2.properties | Simple, easy for basic configs | Limited for complex scenarios |

**Recommended**: Use **XML** for most projects as it provides the best balance of readability, features, and community support.

### Configuration File Location

Log4J searches for configuration files in this order:

1. System property: `-Dlog4j.configurationFile=path/to/log4j2.xml`
2. Classpath root (typically `src/main/resources/`)
3. Default configuration if no file is found

**Best Practice**: Place `log4j2.xml` in `src/main/resources/` for Maven/Gradle projects.

---

## Basic XML Configuration

### Minimal Configuration

The simplest Log4J 2 configuration:

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

**Explanation:**
- `Configuration status="WARN"`: Log4J's internal logging level (for debugging configuration issues)
- `Appenders`: Defines where logs go (console, files, etc.)
- `Console`: An appender that writes to console
- `PatternLayout`: Defines the log message format
- `Root`: The root logger that applies to all classes
- `AppenderRef`: Links logger to appender

---

## Configuration Elements

### 1. Configuration Root Element

```xml
<Configuration status="WARN" monitorInterval="30">
```

**Attributes:**
- `status`: Internal Log4J logging level (ERROR, WARN, INFO, DEBUG, TRACE)
- `monitorInterval`: Seconds between checking for configuration changes (0 = disabled)

### 2. Properties

Define reusable values:

```xml
<Configuration>
    <Properties>
        <Property name="logPath">/var/logs/myapp</Property>
        <Property name="pattern">%d{yyyy-MM-dd HH:mm:ss} %-5p %c{1}:%L - %m%n</Property>
    </Properties>

    <Appenders>
        <File name="FileAppender" fileName="${logPath}/application.log">
            <PatternLayout pattern="${pattern}"/>
        </File>
    </Appenders>
</Configuration>
```

**Benefits:**
- Centralize common values
- Easy to modify across multiple appenders
- Support environment-specific configurations

### 3. Appenders

Define output destinations (covered in detail in next topic):

```xml
<Appenders>
    <!-- Console output -->
    <Console name="Console" target="SYSTEM_OUT">
        <PatternLayout pattern="%d{HH:mm:ss.SSS} %-5level - %msg%n"/>
    </Console>

    <!-- File output -->
    <File name="File" fileName="logs/app.log">
        <PatternLayout pattern="%d %-5p [%t] %C{2} - %m%n"/>
    </File>
</Appenders>
```

### 4. Loggers

Configure logging behavior for specific packages or classes:

```xml
<Loggers>
    <!-- Root logger: default for all classes -->
    <Root level="WARN">
        <AppenderRef ref="Console"/>
    </Root>

    <!-- Package-specific logger -->
    <Logger name="com.company.service" level="DEBUG">
        <AppenderRef ref="File"/>
    </Logger>

    <!-- Class-specific logger -->
    <Logger name="com.company.service.UserService" level="TRACE" additivity="false">
        <AppenderRef ref="Console"/>
        <AppenderRef ref="File"/>
    </Logger>
</Loggers>
```

**Logger Attributes:**
- `name`: Package or class name
- `level`: Minimum log level (TRACE, DEBUG, INFO, WARN, ERROR, FATAL)
- `additivity`: If true, also uses parent logger's appenders (default: true)

---

## Complete Configuration Examples

### Development Environment

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
    <Properties>
        <Property name="LOG_PATTERN">
            %d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n
        </Property>
    </Properties>

    <Appenders>
        <!-- Colored console output for development -->
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="%highlight{${LOG_PATTERN}}{FATAL=red, ERROR=red, WARN=yellow, INFO=green, DEBUG=cyan, TRACE=blue}"/>
        </Console>

        <!-- Debug file for detailed logs -->
        <File name="DebugFile" fileName="logs/debug.log">
            <PatternLayout pattern="${LOG_PATTERN}"/>
        </File>
    </Appenders>

    <Loggers>
        <!-- Debug level for application code -->
        <Logger name="com.company" level="DEBUG">
            <AppenderRef ref="DebugFile"/>
        </Logger>

        <!-- Reduce noise from frameworks -->
        <Logger name="org.springframework" level="INFO"/>
        <Logger name="org.hibernate" level="WARN"/>

        <!-- Root logger -->
        <Root level="DEBUG">
            <AppenderRef ref="Console"/>
        </Root>
    </Loggers>
</Configuration>
```

### Production Environment

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="ERROR" monitorInterval="60">
    <Properties>
        <Property name="LOG_DIR">/var/logs/myapp</Property>
        <Property name="LOG_PATTERN">
            %d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n
        </Property>
    </Properties>

    <Appenders>
        <!-- Console: Only errors and above -->
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="${LOG_PATTERN}"/>
            <ThresholdFilter level="ERROR" onMatch="ACCEPT" onMismatch="DENY"/>
        </Console>

        <!-- Application logs with daily rotation -->
        <RollingFile name="AppFile"
                     fileName="${LOG_DIR}/application.log"
                     filePattern="${LOG_DIR}/application-%d{yyyy-MM-dd}-%i.log.gz">
            <PatternLayout pattern="${LOG_PATTERN}"/>
            <Policies>
                <TimeBasedTriggeringPolicy interval="1"/>
                <SizeBasedTriggeringPolicy size="100 MB"/>
            </Policies>
            <DefaultRolloverStrategy max="30"/>
        </RollingFile>

        <!-- Error logs separate file -->
        <RollingFile name="ErrorFile"
                     fileName="${LOG_DIR}/error.log"
                     filePattern="${LOG_DIR}/error-%d{yyyy-MM-dd}.log.gz">
            <PatternLayout pattern="${LOG_PATTERN}"/>
            <ThresholdFilter level="ERROR" onMatch="ACCEPT" onMismatch="DENY"/>
            <Policies>
                <TimeBasedTriggeringPolicy interval="1"/>
            </Policies>
            <DefaultRolloverStrategy max="90"/>
        </RollingFile>
    </Appenders>

    <Loggers>
        <!-- Application code: INFO and above -->
        <Logger name="com.company" level="INFO">
            <AppenderRef ref="AppFile"/>
        </Logger>

        <!-- Root logger -->
        <Root level="WARN">
            <AppenderRef ref="Console"/>
            <AppenderRef ref="ErrorFile"/>
        </Root>
    </Loggers>
</Configuration>
```

---

## Properties File Configuration

For simpler scenarios, you can use properties format:

```properties
# Root logger
status = warn
name = PropertiesConfig

# Console appender
appender.console.type = Console
appender.console.name = STDOUT
appender.console.layout.type = PatternLayout
appender.console.layout.pattern = %d{yyyy-MM-dd HH:mm:ss} %-5p %c{1}:%L - %m%n

# File appender
appender.file.type = File
appender.file.name = LOGFILE
appender.file.fileName = logs/app.log
appender.file.layout.type = PatternLayout
appender.file.layout.pattern = %d{yyyy-MM-dd HH:mm:ss} %-5p %c{1}:%L - %m%n

# Root logger level and appenders
rootLogger.level = info
rootLogger.appenderRefs = stdout, logfile
rootLogger.appenderRef.stdout.ref = STDOUT
rootLogger.appenderRef.logfile.ref = LOGFILE

# Package-specific logger
logger.service.name = com.company.service
logger.service.level = debug
logger.service.additivity = false
logger.service.appenderRefs = stdout, logfile
logger.service.appenderRef.stdout.ref = STDOUT
logger.service.appenderRef.logfile.ref = LOGFILE
```

**Note**: Properties format is less flexible than XML for complex configurations.

---

## Environment-Specific Configuration

### Approach 1: Maven Profiles

**pom.xml:**
```xml
<profiles>
    <profile>
        <id>dev</id>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
        <build>
            <resources>
                <resource>
                    <directory>src/main/resources/config/dev</directory>
                </resource>
            </resources>
        </build>
    </profile>
    <profile>
        <id>prod</id>
        <build>
            <resources>
                <resource>
                    <directory>src/main/resources/config/prod</directory>
                </resource>
            </resources>
        </build>
    </profile>
</profiles>
```

**Directory Structure:**
```
src/main/resources/
├── config/
│   ├── dev/
│   │   └── log4j2.xml
│   └── prod/
│       └── log4j2.xml
```

### Approach 2: System Properties

Use system properties for dynamic configuration:

```xml
<Configuration>
    <Properties>
        <Property name="logLevel">${sys:log.level:-INFO}</Property>
        <Property name="logPath">${sys:log.path:-logs}</Property>
    </Properties>

    <Appenders>
        <File name="File" fileName="${logPath}/application.log">
            <PatternLayout pattern="%d %-5p - %m%n"/>
        </File>
    </Appenders>

    <Loggers>
        <Root level="${logLevel}">
            <AppenderRef ref="File"/>
        </Root>
    </Loggers>
</Configuration>
```

**Run with:**
```bash
java -Dlog.level=DEBUG -Dlog.path=/var/logs/myapp -jar myapp.jar
```

### Approach 3: Environment Variables

```xml
<Configuration>
    <Properties>
        <Property name="logLevel">${env:LOG_LEVEL:-INFO}</Property>
        <Property name="logPath">${env:LOG_PATH:-logs}</Property>
    </Properties>

    <Appenders>
        <File name="File" fileName="${logPath}/application.log">
            <PatternLayout pattern="%d %-5p - %m%n"/>
        </File>
    </Appenders>

    <Loggers>
        <Root level="${logLevel}">
            <AppenderRef ref="File"/>
        </Root>
    </Loggers>
</Configuration>
```

**Run with:**
```bash
export LOG_LEVEL=DEBUG
export LOG_PATH=/var/logs/myapp
java -jar myapp.jar
```

---

## Filters

Filters provide fine-grained control over which log events are processed.

### Threshold Filter

Only logs at or above a specific level:

```xml
<Console name="Console">
    <PatternLayout pattern="%d %-5p - %m%n"/>
    <ThresholdFilter level="WARN" onMatch="ACCEPT" onMismatch="DENY"/>
</Console>
```

### Regex Filter

Filter based on message content:

```xml
<Console name="Console">
    <PatternLayout pattern="%d %-5p - %m%n"/>
    <RegexFilter regex=".*password.*" onMatch="DENY" onMismatch="ACCEPT"/>
</Console>
```

### Time Filter

Only log during specific times:

```xml
<Console name="Console">
    <PatternLayout pattern="%d %-5p - %m%n"/>
    <TimeFilter start="05:00:00" end="05:30:00" onMatch="ACCEPT" onMismatch="DENY"/>
</Console>
```

### Composite Filter

Combine multiple filters:

```xml
<Console name="Console">
    <PatternLayout pattern="%d %-5p - %m%n"/>
    <Filters>
        <ThresholdFilter level="ERROR" onMatch="ACCEPT"/>
        <RegexFilter regex=".*test.*" onMatch="DENY"/>
        <MarkerFilter marker="SENSITIVE" onMatch="DENY" onMismatch="ACCEPT"/>
    </Filters>
</Console>
```

---

## Programmatic Configuration

While external configuration is preferred, you can configure Log4J programmatically:

```java
import org.apache.logging.log4j.Level;
import org.apache.logging.log4j.core.appender.ConsoleAppender;
import org.apache.logging.log4j.core.config.Configurator;
import org.apache.logging.log4j.core.config.builder.api.*;
import org.apache.logging.log4j.core.config.builder.impl.BuiltConfiguration;

public class Log4jConfiguration {
    public static void configure() {
        ConfigurationBuilder<BuiltConfiguration> builder =
            ConfigurationBuilderFactory.newConfigurationBuilder();

        // Set configuration name
        builder.setStatusLevel(Level.ERROR);
        builder.setConfigurationName("ProgrammaticConfig");

        // Create pattern layout
        LayoutComponentBuilder layoutBuilder = builder.newLayout("PatternLayout")
            .addAttribute("pattern", "%d [%t] %-5level: %msg%n%throwable");

        // Create console appender
        AppenderComponentBuilder appenderBuilder = builder.newAppender("Stdout", "CONSOLE")
            .addAttribute("target", ConsoleAppender.Target.SYSTEM_OUT)
            .add(layoutBuilder);
        builder.add(appenderBuilder);

        // Create root logger
        RootLoggerComponentBuilder rootLogger = builder.newRootLogger(Level.DEBUG);
        rootLogger.add(builder.newAppenderRef("Stdout"));
        builder.add(rootLogger);

        // Apply configuration
        Configurator.initialize(builder.build());
    }
}
```

**Use Case**: Dynamic configuration based on runtime conditions, testing scenarios, or when config files are not available.

---

## Configuration Best Practices

### 1. Use Appropriate Log Levels

```xml
<Loggers>
    <!-- Application code: detailed in dev, concise in prod -->
    <Logger name="com.company" level="${env:APP_LOG_LEVEL:-INFO}"/>

    <!-- Framework libraries: reduce noise -->
    <Logger name="org.springframework" level="WARN"/>
    <Logger name="org.hibernate.SQL" level="${env:SQL_LOG_LEVEL:-ERROR}"/>
    <Logger name="org.apache.http" level="WARN"/>

    <!-- Third-party libraries -->
    <Logger name="com.amazonaws" level="ERROR"/>

    <!-- Root catches everything else -->
    <Root level="WARN">
        <AppenderRef ref="Console"/>
    </Root>
</Loggers>
```

### 2. Externalize Configuration Values

```xml
<Properties>
    <!-- Can be overridden via system properties or environment variables -->
    <Property name="logPath">${sys:log.path:-${env:LOG_PATH:-./logs}}</Property>
    <Property name="maxFileSize">${sys:log.maxSize:-100MB}</Property>
    <Property name="maxHistory">${sys:log.maxHistory:-30}</Property>
</Properties>
```

### 3. Enable Automatic Reload

```xml
<Configuration status="WARN" monitorInterval="60">
    <!-- Configuration will be reloaded every 60 seconds if changed -->
</Configuration>
```

This allows changing log levels in production without restarting the application.

### 4. Separate Error Logs

```xml
<!-- Separate file for errors makes troubleshooting easier -->
<RollingFile name="ErrorFile" fileName="logs/error.log">
    <ThresholdFilter level="ERROR" onMatch="ACCEPT" onMismatch="DENY"/>
    <PatternLayout pattern="%d %-5p [%t] %c - %m%n"/>
    <Policies>
        <TimeBasedTriggeringPolicy/>
    </Policies>
</RollingFile>
```

### 5. Use Additivity Wisely

```xml
<!-- Prevent duplicate logging -->
<Logger name="com.company.service" level="DEBUG" additivity="false">
    <AppenderRef ref="ServiceFile"/>
</Logger>
```

Set `additivity="false"` when you want logs only in specific appender, not in parent loggers' appenders.

---

## Troubleshooting Configuration

### Check Configuration Status

Enable internal Log4J logging:

```xml
<Configuration status="TRACE">
```

This shows how Log4J loads and processes the configuration.

### Common Issues

| Problem | Solution |
|---------|----------|
| Configuration not found | Check file location in classpath, verify file name |
| Logs not appearing | Check log levels, verify appender refs match appender names |
| Duplicate logs | Set `additivity="false"` on logger |
| Performance issues | Use async appenders, reduce log level in production |
| Configuration not reloading | Set `monitorInterval` attribute |

### Validate Configuration

```java
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.core.LoggerContext;

public class ConfigValidator {
    public static void main(String[] args) {
        LoggerContext context = (LoggerContext) LogManager.getContext(false);
        System.out.println("Configuration: " + context.getConfiguration().getName());
        System.out.println("Config file: " + context.getConfigLocation());

        // Test logging
        Logger logger = LogManager.getLogger(ConfigValidator.class);
        logger.trace("TRACE level");
        logger.debug("DEBUG level");
        logger.info("INFO level");
        logger.warn("WARN level");
        logger.error("ERROR level");
    }
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Configuration Formats | XML (recommended), JSON, YAML, Properties |
| Main Elements | Configuration, Properties, Appenders, Loggers |
| Environment Config | Use system properties, environment variables, or Maven profiles |
| Filters | ThresholdFilter, RegexFilter, TimeFilter for fine-grained control |
| Best Practices | Externalize values, use appropriate levels, enable auto-reload, separate error logs |

## Next Topic

Continue to [Appenders (Console, File, Rolling File)](./03-appenders.md) to learn about different output destinations for your logs.
