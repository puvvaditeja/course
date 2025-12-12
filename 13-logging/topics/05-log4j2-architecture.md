# Log4J 2 Architecture

## Log4J 2 vs Log4J 1.x

Log4J 2 is a complete rewrite of Log4J 1.x, addressing performance issues and adding modern features.

### Key Differences

| Feature | Log4J 1.x | Log4J 2 |
|---------|-----------|---------|
| **Performance** | Synchronous only | Async appenders, lock-free |
| **API** | Single API | Separated API and implementation |
| **Configuration** | XML, Properties | XML, JSON, YAML, Properties |
| **Plugin System** | Limited | Extensive plugin architecture |
| **Auto-reload** | Not supported | Automatic config reload |
| **Lambda Support** | No | Yes (Java 8+) |
| **Garbage-Free** | No | Yes (optional) |
| **Security** | Multiple vulnerabilities | Better security (after patches) |
| **Status** | End of life (2015) | Active development |

**Performance Comparison:**
- Log4J 2 is 10x faster than Log4J 1.x in multi-threaded scenarios
- Async loggers can achieve 18 million messages/second
- Garbage-free mode reduces GC pressure significantly

---

## Log4J 2 Architecture Overview

### Component Layers

```
┌─────────────────────────────────────────────────┐
│         Application Code                        │
│         (LogManager.getLogger())                │
└─────────────────────────────────────────────────┘
                     ↓
┌─────────────────────────────────────────────────┐
│         Log4J 2 API                             │
│         (Logger interface)                      │
└─────────────────────────────────────────────────┘
                     ↓
┌─────────────────────────────────────────────────┐
│         Log4J 2 Core                            │
│  ┌──────────────────────────────────────────┐  │
│  │  LoggerContext                           │  │
│  │  ├── Configuration                       │  │
│  │  │   ├── Appenders                       │  │
│  │  │   ├── Filters                         │  │
│  │  │   └── Loggers                         │  │
│  │  └── Logger Instances                    │  │
│  └──────────────────────────────────────────┘  │
└─────────────────────────────────────────────────┘
                     ↓
┌─────────────────────────────────────────────────┐
│         Appenders                               │
│  ┌─────────┐ ┌─────────┐ ┌─────────┐           │
│  │ Console │ │  File   │ │  Async  │           │
│  └─────────┘ └─────────┘ └─────────┘           │
└─────────────────────────────────────────────────┘
                     ↓
┌─────────────────────────────────────────────────┐
│         Output Destinations                     │
│         (Console, Files, Network, etc.)         │
└─────────────────────────────────────────────────┘
```

### Core Components

1. **Logger**: Front-end interface for application code
2. **LoggerContext**: Anchor point for logging system
3. **Configuration**: Container for appenders, loggers, filters
4. **LogEvent**: Immutable log event containing all information
5. **Appender**: Publishes log events to destinations
6. **Layout**: Formats log events
7. **Filter**: Controls which events are logged

---

## API and Implementation Separation

Log4J 2 separates the API from the implementation, providing flexibility.

### Module Structure

```
Application
    ↓ (depends on)
log4j-api.jar (interface)
    ↓ (implementation)
log4j-core.jar (concrete classes)
```

**Benefits:**
- Applications depend only on API (lightweight)
- Implementation can be swapped
- Better compatibility with dependency management
- Smaller classpath for API-only modules

### Maven Dependencies

```xml
<!-- API: Required for application code -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.20.0</version>
</dependency>

<!-- Core: Required for actual logging -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.20.0</version>
</dependency>
```

---

## Log Event Flow

### Detailed Flow Diagram

```
1. Application Code
   logger.info("User created: {}", username);
                    ↓
2. Logger (checks level)
   if (isInfoEnabled()) ...
                    ↓
3. Create LogEvent (immutable)
   timestamp, level, logger, message, parameters
                    ↓
4. Logger Filters
   if (filter.filter(event) == ACCEPT) ...
                    ↓
5. Appender Filters
   for each appender's filter ...
                    ↓
6. Layout Formatting
   layout.format(event) → String
                    ↓
7. Write to Destination
   outputStream.write(formattedMessage)
```

### Example Code Flow

```java
// Application code
logger.info("User {} logged in", username);

// Internal flow:
// 1. Check if INFO is enabled (quick check)
// 2. Create LogEvent with timestamp, level, message template, parameters
// 3. Pass to configured appenders
// 4. Each appender applies its filters
// 5. Layout formats the message
// 6. Write to destination (console, file, etc.)
```

---

## Asynchronous Logging

Log4J 2 provides two async logging mechanisms for high-performance applications.

### 1. Async Appender

Wraps synchronous appenders to make them asynchronous.

```xml
<Appenders>
    <!-- Synchronous file appender -->
    <RollingFile name="FileAppender" fileName="logs/app.log">
        <PatternLayout pattern="%d %-5p - %m%n"/>
        <Policies>
            <SizeBasedTriggeringPolicy size="100 MB"/>
        </Policies>
    </RollingFile>

    <!-- Async wrapper -->
    <Async name="AsyncAppender" bufferSize="512" blocking="false">
        <AppenderRef ref="FileAppender"/>
    </Async>
</Appenders>

<Loggers>
    <Root level="INFO">
        <AppenderRef ref="AsyncAppender"/>
    </Root>
</Loggers>
```

**Configuration Options:**
- `bufferSize`: Queue size (default: 128)
- `blocking`: Wait if queue full (true) or discard (false)
- `shutdownTimeout`: Wait time on shutdown (ms)

**Flow:**

```
Application Thread          Background Thread
      ↓                           ↓
logger.info("msg")          (waiting for events)
      ↓                           ↓
Add to queue ─────────────> Dequeue event
      ↓                           ↓
Return immediately          Format and write
      ↓                           ↓
Continue execution          Process next event
```

### 2. Async Logger (Recommended)

Uses LMAX Disruptor for even better performance.

**Add dependency:**
```xml
<dependency>
    <groupId>com.lmax</groupId>
    <artifactId>disruptor</artifactId>
    <version>3.4.4</version>
</dependency>
```

**Configuration:**

```xml
<Configuration>
    <Appenders>
        <RollingFile name="FileAppender" fileName="logs/app.log">
            <PatternLayout pattern="%d %-5p - %m%n"/>
            <Policies>
                <SizeBasedTriggeringPolicy size="100 MB"/>
            </Policies>
        </RollingFile>
    </Appenders>

    <Loggers>
        <!-- Async logger for specific package -->
        <AsyncLogger name="com.company" level="INFO" includeLocation="false">
            <AppenderRef ref="FileAppender"/>
        </AsyncLogger>

        <!-- Synchronous root logger -->
        <Root level="WARN">
            <AppenderRef ref="FileAppender"/>
        </Root>
    </Loggers>
</Configuration>
```

**All Loggers Async:**

Add system property or `log4j2.component.properties`:
```properties
log4j2.contextSelector=org.apache.logging.log4j.core.async.AsyncLoggerContextSelector
```

**Configuration file:**
```xml
<Configuration>
    <Appenders>
        <RollingFile name="FileAppender" fileName="logs/app.log">
            <PatternLayout pattern="%d %-5p - %m%n"/>
        </RollingFile>
    </Appenders>

    <Loggers>
        <!-- All loggers are automatically async -->
        <Root level="INFO">
            <AppenderRef ref="FileAppender"/>
        </Root>
    </Loggers>
</Configuration>
```

### Async Logging Comparison

| Feature | Async Appender | Async Logger |
|---------|----------------|--------------|
| Performance | Fast | Fastest |
| Throughput | ~2M msg/sec | ~18M msg/sec |
| Technology | ArrayBlockingQueue | LMAX Disruptor |
| Configuration | Wrap appenders | Configure loggers |
| Dependency | None | Disruptor library |
| Location Info | Available | Expensive (disabled by default) |
| Use Case | Specific appenders | Entire application |

**Recommendation**: Use Async Logger for best performance.

---

## Garbage-Free Logging

Log4J 2.6+ supports garbage-free logging to reduce GC pressure.

### Enabling Garbage-Free Mode

**System properties:**
```properties
log4j2.enable.threadlocals=true
log4j2.enable.direct.encoders=true
```

**Or in code:**
```java
System.setProperty("log4j2.enable.threadlocals", "true");
System.setProperty("log4j2.enable.direct.encoders", "true");
```

### Benefits

- No temporary objects created during logging
- Reduced GC pressure in high-throughput applications
- Better latency (no GC pauses)

### Requirements

- Log4J 2.6 or later
- All layouts must support garbage-free mode
- Pattern layouts work with most patterns

### Garbage-Free Configuration

```xml
<Configuration>
    <Appenders>
        <RollingRandomAccessFile name="RandomAccessFile"
                                fileName="logs/app.log"
                                filePattern="logs/app-%d{yyyy-MM-dd}.log">
            <PatternLayout pattern="%d %-5p [%t] %c - %m%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy/>
            </Policies>
        </RollingRandomAccessFile>
    </Appenders>

    <Loggers>
        <Root level="INFO">
            <AppenderRef ref="RandomAccessFile"/>
        </Root>
    </Loggers>
</Configuration>
```

**Note**: Use `RollingRandomAccessFile` instead of `RollingFile` for best garbage-free performance.

---

## Filters

Filters provide fine-grained control over log event processing.

### Filter Hierarchy

```
Logger Config
    ↓
Logger Filters ──→ (if DENY, stop)
    ↓ (if ACCEPT or NEUTRAL)
Appender Filters ──→ (if DENY, skip this appender)
    ↓ (if ACCEPT or NEUTRAL)
Write to Appender
```

### Filter Return Values

| Return Value | Meaning |
|--------------|---------|
| ACCEPT | Process event, skip remaining filters |
| DENY | Reject event, stop processing |
| NEUTRAL | Continue to next filter |

### Common Filters

#### 1. Threshold Filter

```xml
<ThresholdFilter level="WARN" onMatch="ACCEPT" onMismatch="DENY"/>
```

#### 2. Time Filter

```xml
<TimeFilter start="06:00:00" end="18:00:00" onMatch="ACCEPT" onMismatch="DENY"/>
```

#### 3. Regex Filter

```xml
<RegexFilter regex=".*password.*" onMatch="DENY" onMismatch="ACCEPT"/>
```

#### 4. Burst Filter

Limits log rate to prevent flooding:

```xml
<BurstFilter level="INFO" rate="10" maxBurst="100"/>
```

**Parameters:**
- `rate`: Average rate (events/second)
- `maxBurst`: Maximum burst size

#### 5. Marker Filter

```xml
<MarkerFilter marker="SENSITIVE" onMatch="DENY" onMismatch="ACCEPT"/>
```

### Composite Filters

Combine multiple filters:

```xml
<Appenders>
    <File name="BusinessLog" fileName="logs/business.log">
        <Filters>
            <!-- Only business-related logs -->
            <MarkerFilter marker="BUSINESS" onMatch="NEUTRAL" onMismatch="DENY"/>
            <!-- INFO and above -->
            <ThresholdFilter level="INFO" onMatch="ACCEPT" onMismatch="DENY"/>
        </Filters>
        <PatternLayout pattern="%d %-5p - %m%n"/>
    </File>
</Appenders>
```

---

## Plugin System

Log4J 2 uses a powerful plugin architecture for extensibility.

### Plugin Types

- **Core Plugins**: Logger, Appender, Layout, Filter
- **Converter Plugins**: Pattern converters
- **Lookup Plugins**: Variable substitution
- **Custom Plugins**: User-defined extensions

### Creating a Custom Appender

```java
import org.apache.logging.log4j.core.*;
import org.apache.logging.log4j.core.appender.AbstractAppender;
import org.apache.logging.log4j.core.config.plugins.*;

@Plugin(name = "CustomAppender", category = Core.CATEGORY_NAME, elementType = Appender.ELEMENT_TYPE)
public class CustomAppender extends AbstractAppender {

    protected CustomAppender(String name, Filter filter, Layout<? extends Serializable> layout) {
        super(name, filter, layout, true, null);
    }

    @Override
    public void append(LogEvent event) {
        // Custom logic to handle log event
        String message = new String(getLayout().toByteArray(event));
        // Send to custom destination (database, queue, API, etc.)
        sendToCustomDestination(message);
    }

    @PluginFactory
    public static CustomAppender createAppender(
            @PluginAttribute("name") String name,
            @PluginElement("Filter") Filter filter,
            @PluginElement("Layout") Layout<? extends Serializable> layout) {

        if (name == null) {
            LOGGER.error("No name provided for CustomAppender");
            return null;
        }
        if (layout == null) {
            layout = PatternLayout.createDefaultLayout();
        }
        return new CustomAppender(name, filter, layout);
    }

    private void sendToCustomDestination(String message) {
        // Implementation
    }
}
```

**Usage in configuration:**
```xml
<Appenders>
    <CustomAppender name="Custom">
        <PatternLayout pattern="%d %-5p - %m%n"/>
    </CustomAppender>
</Appenders>
```

### Creating a Custom Layout

```java
import org.apache.logging.log4j.core.Layout;
import org.apache.logging.log4j.core.LogEvent;
import org.apache.logging.log4j.core.config.plugins.*;

@Plugin(name = "CustomLayout", category = Node.CATEGORY, elementType = Layout.ELEMENT_TYPE)
public class CustomLayout extends AbstractStringLayout {

    protected CustomLayout() {
        super(StandardCharsets.UTF_8);
    }

    @Override
    public String toSerializable(LogEvent event) {
        // Custom formatting
        return String.format("[%s] %s - %s%n",
            event.getLevel(),
            event.getLoggerName(),
            event.getMessage().getFormattedMessage());
    }

    @PluginFactory
    public static CustomLayout createLayout() {
        return new CustomLayout();
    }
}
```

---

## Configuration API

Programmatic configuration for dynamic scenarios.

### ConfigurationBuilder API

```java
import org.apache.logging.log4j.Level;
import org.apache.logging.log4j.core.appender.ConsoleAppender;
import org.apache.logging.log4j.core.config.Configurator;
import org.apache.logging.log4j.core.config.builder.api.*;
import org.apache.logging.log4j.core.config.builder.impl.BuiltConfiguration;

public class ProgrammaticConfig {

    public static void configure() {
        ConfigurationBuilder<BuiltConfiguration> builder =
            ConfigurationBuilderFactory.newConfigurationBuilder();

        builder.setStatusLevel(Level.ERROR);
        builder.setConfigurationName("ProgrammaticConfig");

        // Create pattern layout
        LayoutComponentBuilder layoutBuilder = builder.newLayout("PatternLayout")
            .addAttribute("pattern", "%d [%t] %-5level: %msg%n%throwable");

        // Create console appender
        AppenderComponentBuilder consoleBuilder = builder.newAppender("Stdout", "CONSOLE")
            .addAttribute("target", ConsoleAppender.Target.SYSTEM_OUT)
            .add(layoutBuilder);
        builder.add(consoleBuilder);

        // Create file appender
        AppenderComponentBuilder fileBuilder = builder.newAppender("LogFile", "File")
            .addAttribute("fileName", "logs/app.log")
            .add(layoutBuilder);
        builder.add(fileBuilder);

        // Create loggers
        builder.add(builder.newLogger("com.company", Level.DEBUG)
            .add(builder.newAppenderRef("LogFile"))
            .addAttribute("additivity", false));

        builder.add(builder.newRootLogger(Level.INFO)
            .add(builder.newAppenderRef("Stdout"))
            .add(builder.newAppenderRef("LogFile")));

        // Apply configuration
        Configurator.initialize(builder.build());
    }
}
```

### Dynamic Reconfiguration

```java
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.core.LoggerContext;
import org.apache.logging.log4j.core.config.Configuration;
import org.apache.logging.log4j.core.config.LoggerConfig;

public class DynamicConfig {

    public static void changeLogLevel(String loggerName, Level newLevel) {
        LoggerContext ctx = (LoggerContext) LogManager.getContext(false);
        Configuration config = ctx.getConfiguration();

        LoggerConfig loggerConfig = config.getLoggerConfig(loggerName);
        loggerConfig.setLevel(newLevel);

        ctx.updateLoggers(); // Apply changes
    }

    public static void main(String[] args) {
        // Change log level at runtime
        changeLogLevel("com.company.UserService", Level.DEBUG);
    }
}
```

---

## Lookups and Variable Substitution

Log4J 2 supports various lookup mechanisms for configuration.

### Available Lookups

| Lookup | Syntax | Example |
|--------|--------|---------|
| System Properties | `${sys:property}` | `${sys:user.home}` |
| Environment Variables | `${env:variable}` | `${env:LOG_PATH}` |
| Java | `${java:runtime}` | `${java:version}` |
| Date | `${date:pattern}` | `${date:yyyy-MM-dd}` |
| Context Map (MDC) | `${ctx:key}` | `${ctx:userId}` |
| Main Arguments | `${main:index}` | `${main:0}` |
| Bundle | `${bundle:key}` | `${bundle:app.name}` |

### Usage Examples

```xml
<Configuration>
    <Properties>
        <!-- Default values with fallback -->
        <Property name="logPath">${sys:log.path:-${env:LOG_PATH:-./logs}}</Property>
        <Property name="appName">${env:APP_NAME:-myapp}</Property>
    </Properties>

    <Appenders>
        <RollingFile name="File"
                     fileName="${logPath}/${appName}.log"
                     filePattern="${logPath}/${appName}-%d{yyyy-MM-dd}.log">
            <PatternLayout pattern="%d %-5p - %m%n"/>
        </RollingFile>
    </Appenders>
</Configuration>
```

---

## Performance Best Practices

### 1. Use Async Loggers

```xml
<!-- Enable all async -->
<Configuration>
    <Properties>
        <Property name="log4j2.contextSelector">
            org.apache.logging.log4j.core.async.AsyncLoggerContextSelector
        </Property>
    </Properties>
</Configuration>
```

### 2. Disable Location Information

```xml
<AsyncLogger name="com.company" level="INFO" includeLocation="false">
    <AppenderRef ref="FileAppender"/>
</AsyncLogger>
```

### 3. Use Lambda Expressions (Java 8+)

```java
// Only evaluates if DEBUG is enabled
logger.debug(() -> "Expensive computation: " + computeExpensiveValue());
```

### 4. Enable Garbage-Free Logging

```properties
log4j2.enable.threadlocals=true
log4j2.enable.direct.encoders=true
```

### 5. Use RandomAccessFile Appenders

```xml
<RollingRandomAccessFile name="File" fileName="logs/app.log">
    <!-- Faster than regular RollingFile -->
</RollingRandomAccessFile>
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Architecture | Separated API and Core, plugin-based, flexible |
| Async Logging | Async Appender (good) vs Async Logger (best) |
| Garbage-Free | Reduce GC pressure with threadlocals and direct encoders |
| Filters | ACCEPT/DENY/NEUTRAL, can be combined, multiple types |
| Plugins | Extensible architecture for custom appenders, layouts, filters |
| Performance | Use async loggers, disable location, enable garbage-free mode |

## Next Topic

Continue to [SLF4J (Simple Logging Facade for Java)](./06-slf4j.md) to learn about logging abstraction and framework independence.
