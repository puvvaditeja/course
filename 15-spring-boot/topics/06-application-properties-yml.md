# Application Properties and YAML Configuration

## Introduction

Spring Boot provides a flexible and powerful configuration system through external configuration files. The two main formats are `application.properties` and `application.yml` (YAML), allowing you to customize application behavior without changing code.

### What is External Configuration?

External configuration allows you to:
- Separate configuration from code
- Change application behavior without recompilation
- Use different configurations for different environments
- Override default auto-configuration settings
- Inject values into Spring beans

**Key Principles:**
- Convention over configuration
- Environment-specific configurations
- Property source precedence
- Type-safe configuration binding

---

## Configuration File Formats

### application.properties

Traditional Java properties file format:

```properties
# Server configuration
server.port=8080
server.servlet.context-path=/api

# Database configuration
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=secret

# JPA configuration
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

### application.yml (YAML)

More structured, hierarchical format:

```yaml
# Server configuration
server:
  port: 8080
  servlet:
    context-path: /api

# Database configuration
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mydb
    username: root
    password: secret

  # JPA configuration
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
```

### Choosing Between Properties and YAML

| Feature | .properties | .yml |
|---------|-------------|------|
| Readability | Good | Excellent |
| Hierarchy | Flat (dots) | Nested |
| Lists | Limited | Native support |
| Multi-document | No | Yes |
| IDE Support | Universal | Good |
| Learning Curve | Easy | Moderate |
| File Size | Larger | Smaller |

---

## File Locations

### Search Order (Highest to Lowest Priority)

```
1. /config subdirectory of current directory
   ./config/application.properties

2. Current directory
   ./application.properties

3. classpath:/config/ package
   src/main/resources/config/application.properties

4. classpath root
   src/main/resources/application.properties
```

### Project Structure

```
my-app/
├── config/                           # Priority 1
│   └── application.properties
├── application.properties            # Priority 2
└── src/
    └── main/
        └── resources/
            ├── config/              # Priority 3
            │   └── application.properties
            └── application.properties  # Priority 4 (most common)
```

---

## Common Configuration Properties

### Server Configuration

```properties
# Basic server settings
server.port=8080
server.address=localhost
server.servlet.context-path=/api

# Tomcat-specific
server.tomcat.max-threads=200
server.tomcat.min-spare-threads=10
server.tomcat.max-connections=10000

# Compression
server.compression.enabled=true
server.compression.mime-types=application/json,application/xml,text/html,text/xml,text/plain

# SSL/TLS
server.ssl.enabled=true
server.ssl.key-store=classpath:keystore.p12
server.ssl.key-store-password=secret
server.ssl.key-store-type=PKCS12
```

```yaml
# YAML equivalent
server:
  port: 8080
  address: localhost
  servlet:
    context-path: /api

  tomcat:
    max-threads: 200
    min-spare-threads: 10
    max-connections: 10000

  compression:
    enabled: true
    mime-types:
      - application/json
      - application/xml
      - text/html

  ssl:
    enabled: true
    key-store: classpath:keystore.p12
    key-store-password: secret
    key-store-type: PKCS12
```

### Database Configuration

```properties
# DataSource
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=secret
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# Connection Pool (HikariCP)
spring.datasource.hikari.maximum-pool-size=20
spring.datasource.hikari.minimum-idle=5
spring.datasource.hikari.connection-timeout=30000
spring.datasource.hikari.idle-timeout=600000
spring.datasource.hikari.max-lifetime=1800000

# JPA/Hibernate
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect
```

```yaml
# YAML equivalent
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mydb
    username: root
    password: secret
    driver-class-name: com.mysql.cj.jdbc.Driver

    hikari:
      maximum-pool-size: 20
      minimum-idle: 5
      connection-timeout: 30000
      idle-timeout: 600000
      max-lifetime: 1800000

  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
    properties:
      hibernate:
        format_sql: true
        dialect: org.hibernate.dialect.MySQL8Dialect
```

### Logging Configuration

```properties
# Root logging level
logging.level.root=INFO

# Package-specific levels
logging.level.org.springframework.web=DEBUG
logging.level.org.hibernate=ERROR
logging.level.com.example.myapp=DEBUG

# Log file
logging.file.name=app.log
logging.file.path=/var/logs

# Log pattern
logging.pattern.console=%d{yyyy-MM-dd HH:mm:ss} - %msg%n
logging.pattern.file=%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n

# Rolling
logging.logback.rollingpolicy.max-file-size=10MB
logging.logback.rollingpolicy.max-history=30
```

```yaml
# YAML equivalent
logging:
  level:
    root: INFO
    org.springframework.web: DEBUG
    org.hibernate: ERROR
    com.example.myapp: DEBUG

  file:
    name: app.log
    path: /var/logs

  pattern:
    console: "%d{yyyy-MM-dd HH:mm:ss} - %msg%n"
    file: "%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n"

  logback:
    rollingpolicy:
      max-file-size: 10MB
      max-history: 30
```

### Jackson (JSON) Configuration

```properties
# Date format
spring.jackson.date-format=yyyy-MM-dd HH:mm:ss
spring.jackson.time-zone=UTC

# Serialization
spring.jackson.serialization.write-dates-as-timestamps=false
spring.jackson.serialization.indent-output=true

# Deserialization
spring.jackson.deserialization.fail-on-unknown-properties=false
```

```yaml
# YAML equivalent
spring:
  jackson:
    date-format: yyyy-MM-dd HH:mm:ss
    time-zone: UTC
    serialization:
      write-dates-as-timestamps: false
      indent-output: true
    deserialization:
      fail-on-unknown-properties: false
```

---

## Profile-Specific Configuration

### Understanding Profiles

Profiles allow different configurations for different environments (dev, test, prod).

### Creating Profile-Specific Files

```
src/main/resources/
├── application.properties           # Default
├── application-dev.properties       # Development
├── application-test.properties      # Testing
└── application-prod.properties      # Production
```

### application.properties (default)

```properties
# Default configuration
app.name=My Application
logging.level.root=INFO
```

### application-dev.properties

```properties
# Development configuration
server.port=8080
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.username=sa
spring.datasource.password=
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=create-drop
logging.level.com.example=DEBUG
```

### application-prod.properties

```properties
# Production configuration
server.port=80
spring.datasource.url=jdbc:mysql://prod-server:3306/proddb
spring.datasource.username=${DB_USER}
spring.datasource.password=${DB_PASSWORD}
spring.jpa.show-sql=false
spring.jpa.hibernate.ddl-auto=validate
logging.level.com.example=WARN
```

### Activating Profiles

```bash
# Method 1: Command line
java -jar myapp.jar --spring.profiles.active=dev

# Method 2: Environment variable
export SPRING_PROFILES_ACTIVE=prod
java -jar myapp.jar

# Method 3: In application.properties
spring.profiles.active=dev

# Method 4: Programmatically
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication app = new SpringApplication(Application.class);
        app.setAdditionalProfiles("dev");
        app.run(args);
    }
}

# Multiple profiles
java -jar myapp.jar --spring.profiles.active=dev,debug
```

### Profile-Specific Beans

```java
@Configuration
@Profile("dev")
public class DevConfiguration {

    @Bean
    public DataSource dataSource() {
        return new EmbeddedDatabaseBuilder()
            .setType(EmbeddedDatabaseType.H2)
            .build();
    }
}

@Configuration
@Profile("prod")
public class ProdConfiguration {

    @Bean
    public DataSource dataSource() {
        HikariConfig config = new HikariConfig();
        config.setJdbcUrl(System.getenv("DB_URL"));
        config.setUsername(System.getenv("DB_USER"));
        config.setPassword(System.getenv("DB_PASS"));
        return new HikariDataSource(config);
    }
}
```

---

## YAML Advanced Features

### Multi-Document YAML

```yaml
# Default configuration
spring:
  application:
    name: My Application

---
# Development profile
spring:
  config:
    activate:
      on-profile: dev

  datasource:
    url: jdbc:h2:mem:testdb
    username: sa
    password:

---
# Production profile
spring:
  config:
    activate:
      on-profile: prod

  datasource:
    url: jdbc:mysql://prod-server:3306/proddb
    username: ${DB_USER}
    password: ${DB_PASSWORD}
```

### Lists in YAML

```yaml
# List of strings
spring:
  security:
    user:
      roles:
        - USER
        - ADMIN
        - MANAGER

# List of objects
app:
  servers:
    - name: server1
      host: 192.168.1.10
      port: 8080
    - name: server2
      host: 192.168.1.11
      port: 8080
```

```properties
# Properties equivalent (more verbose)
spring.security.user.roles[0]=USER
spring.security.user.roles[1]=ADMIN
spring.security.user.roles[2]=MANAGER

app.servers[0].name=server1
app.servers[0].host=192.168.1.10
app.servers[0].port=8080
app.servers[1].name=server2
app.servers[1].host=192.168.1.11
app.servers[1].port=8080
```

### Maps in YAML

```yaml
app:
  config:
    settings:
      theme: dark
      language: en
      timezone: UTC
      notifications: true
```

---

## Custom Properties

### Define Custom Properties

```properties
# application.properties
app.name=My Application
app.version=1.0.0
app.description=A sample application
app.max-upload-size=10MB
app.allowed-origins=http://localhost:3000,http://localhost:4200
```

### Inject Using @Value

```java
@RestController
public class AppController {

    @Value("${app.name}")
    private String appName;

    @Value("${app.version}")
    private String appVersion;

    @Value("${app.description}")
    private String description;

    @Value("${app.max-upload-size}")
    private String maxUploadSize;

    @Value("${app.allowed-origins}")
    private List<String> allowedOrigins;

    @GetMapping("/info")
    public Map<String, Object> getInfo() {
        return Map.of(
            "name", appName,
            "version", appVersion,
            "description", description,
            "maxUploadSize", maxUploadSize,
            "allowedOrigins", allowedOrigins
        );
    }
}
```

### Type-Safe Configuration Properties

```java
// Create configuration class
@Component
@ConfigurationProperties(prefix = "app")
public class AppProperties {

    private String name;
    private String version;
    private String description;
    private String maxUploadSize;
    private List<String> allowedOrigins;

    // Getters and setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public String getVersion() { return version; }
    public void setVersion(String version) { this.version = version; }

    public String getDescription() { return description; }
    public void setDescription(String description) { this.description = description; }

    public String getMaxUploadSize() { return maxUploadSize; }
    public void setMaxUploadSize(String maxUploadSize) { this.maxUploadSize = maxUploadSize; }

    public List<String> getAllowedOrigins() { return allowedOrigins; }
    public void setAllowedOrigins(List<String> allowedOrigins) { this.allowedOrigins = allowedOrigins; }
}

// Enable configuration properties
@SpringBootApplication
@EnableConfigurationProperties(AppProperties.class)
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}

// Use in controller
@RestController
public class AppController {

    private final AppProperties appProperties;

    @Autowired
    public AppController(AppProperties appProperties) {
        this.appProperties = appProperties;
    }

    @GetMapping("/info")
    public AppProperties getInfo() {
        return appProperties;
    }
}
```

### Nested Properties

```yaml
app:
  security:
    jwt:
      secret: mySecretKey
      expiration: 86400000
      header: Authorization
    cors:
      allowed-origins:
        - http://localhost:3000
        - http://localhost:4200
      allowed-methods:
        - GET
        - POST
        - PUT
        - DELETE
```

```java
@Component
@ConfigurationProperties(prefix = "app.security")
public class SecurityProperties {

    private Jwt jwt = new Jwt();
    private Cors cors = new Cors();

    public static class Jwt {
        private String secret;
        private long expiration;
        private String header;

        // Getters and setters
        public String getSecret() { return secret; }
        public void setSecret(String secret) { this.secret = secret; }

        public long getExpiration() { return expiration; }
        public void setExpiration(long expiration) { this.expiration = expiration; }

        public String getHeader() { return header; }
        public void setHeader(String header) { this.header = header; }
    }

    public static class Cors {
        private List<String> allowedOrigins;
        private List<String> allowedMethods;

        // Getters and setters
        public List<String> getAllowedOrigins() { return allowedOrigins; }
        public void setAllowedOrigins(List<String> allowedOrigins) { this.allowedOrigins = allowedOrigins; }

        public List<String> getAllowedMethods() { return allowedMethods; }
        public void setAllowedMethods(List<String> allowedMethods) { this.allowedMethods = allowedMethods; }
    }

    // Getters and setters for nested classes
    public Jwt getJwt() { return jwt; }
    public void setJwt(Jwt jwt) { this.jwt = jwt; }

    public Cors getCors() { return cors; }
    public void setCors(Cors cors) { this.cors = cors; }
}
```

---

## Property Placeholders

### Default Values

```properties
# With default value
app.timeout=${APP_TIMEOUT:5000}
app.name=${APP_NAME:My Application}
```

```java
@Value("${app.timeout:5000}")
private int timeout;

@Value("${app.name:Default Name}")
private String appName;
```

### Property References

```properties
app.name=My Application
app.description=Welcome to ${app.name}
app.url=http://localhost:8080
app.api-url=${app.url}/api
```

### Environment Variables

```properties
# Use environment variables
database.url=${DB_URL}
database.username=${DB_USER}
database.password=${DB_PASS}

# With defaults
database.url=${DB_URL:jdbc:h2:mem:testdb}
database.username=${DB_USER:sa}
database.password=${DB_PASS:}
```

### System Properties

```bash
# Set system property
java -Dapp.environment=production -jar myapp.jar
```

```properties
# Use in configuration
app.environment=${app.environment}
```

---

## Property Source Precedence

### Order (Highest to Lowest Priority)

1. Command line arguments
2. Java System properties (System.getProperties())
3. OS environment variables
4. application-{profile}.properties outside packaged jar
5. application-{profile}.properties inside packaged jar
6. application.properties outside packaged jar
7. application.properties inside packaged jar
8. @PropertySource annotations
9. Default properties (SpringApplication.setDefaultProperties)

### Example

```properties
# application.properties
server.port=8080
```

```properties
# application-dev.properties
server.port=8081
```

```bash
# Command line (highest priority)
java -jar myapp.jar --server.port=9090 --spring.profiles.active=dev

# Result: server.port=9090 (command line wins)
```

---

## Configuration Validation

### Using @Validated

```java
@Component
@ConfigurationProperties(prefix = "app")
@Validated
public class AppProperties {

    @NotNull
    @Size(min = 3, max = 50)
    private String name;

    @Min(1)
    @Max(65535)
    private int port;

    @Email
    private String adminEmail;

    @Pattern(regexp = "^(dev|test|prod)$")
    private String environment;

    @NotEmpty
    private List<String> allowedOrigins;

    // Getters and setters
}
```

### Custom Validation

```java
@Component
@ConfigurationProperties(prefix = "app.security")
@Validated
public class SecurityProperties {

    @NotNull
    @ValidJwtSecret
    private String jwtSecret;

    // Custom validator annotation
    @Target({ElementType.FIELD})
    @Retention(RetentionPolicy.RUNTIME)
    @Constraint(validatedBy = JwtSecretValidator.class)
    public @interface ValidJwtSecret {
        String message() default "JWT secret must be at least 256 bits";
        Class<?>[] groups() default {};
        Class<? extends Payload>[] payload() default {};
    }

    // Validator implementation
    public static class JwtSecretValidator
            implements ConstraintValidator<ValidJwtSecret, String> {

        @Override
        public boolean isValid(String value, ConstraintValidatorContext context) {
            return value != null && value.length() >= 32;
        }
    }
}
```

---

## Externalized Configuration Patterns

### Configuration Server Pattern

```yaml
# application.yml
spring:
  config:
    import: optional:configserver:http://config-server:8888
  cloud:
    config:
      uri: http://config-server:8888
      name: myapp
      profile: ${spring.profiles.active}
```

### Secrets Management

```properties
# Using environment variables for secrets
spring.datasource.password=${DB_PASSWORD}
app.api.key=${API_KEY}
app.jwt.secret=${JWT_SECRET}
```

```bash
# Set environment variables
export DB_PASSWORD=secret123
export API_KEY=abc123xyz
export JWT_SECRET=myJwtSecretKey

# Run application
java -jar myapp.jar
```

### Docker Secrets

```yaml
# docker-compose.yml
version: '3.8'
services:
  app:
    image: myapp:latest
    environment:
      - SPRING_DATASOURCE_PASSWORD_FILE=/run/secrets/db_password
    secrets:
      - db_password

secrets:
  db_password:
    external: true
```

---

## Best Practices

### 1. Use Profiles for Environments

```yaml
# application.yml
spring:
  profiles:
    active: ${ENV:dev}

---
spring:
  config:
    activate:
      on-profile: dev
  datasource:
    url: jdbc:h2:mem:testdb

---
spring:
  config:
    activate:
      on-profile: prod
  datasource:
    url: ${DB_URL}
```

### 2. Never Commit Secrets

```properties
# BAD - Don't do this
spring.datasource.password=hardcodedpassword

# GOOD - Use environment variables
spring.datasource.password=${DB_PASSWORD}
```

```gitignore
# .gitignore
application-local.properties
application-secrets.properties
*.env
```

### 3. Use Type-Safe Configuration

```java
// GOOD - Type-safe
@ConfigurationProperties(prefix = "app")
public class AppProperties {
    private String name;
    private int timeout;
}

// AVOID - Error-prone
@Value("${app.name}")
private String name;

@Value("${app.timeout}")
private String timeout; // Wrong type!
```

### 4. Provide Sensible Defaults

```properties
# Always provide defaults
app.max-connections=${MAX_CONNECTIONS:100}
app.timeout=${TIMEOUT:5000}
app.cache-enabled=${CACHE_ENABLED:true}
```

### 5. Document Custom Properties

```properties
# Application name displayed in UI
app.name=My Application

# Maximum file upload size in MB
app.max-upload-size=10

# Comma-separated list of allowed CORS origins
app.allowed-origins=http://localhost:3000,http://localhost:4200
```

---

## Troubleshooting

### Common Issues

#### Issue 1: Property Not Found

```java
// Error: Could not resolve placeholder 'app.name'

// Solution 1: Check spelling
@Value("${app.name}") // Must match exactly

// Solution 2: Provide default
@Value("${app.name:Default Name}")

// Solution 3: Verify file location
// application.properties must be in src/main/resources/
```

#### Issue 2: Profile Not Loading

```bash
# Check active profile
java -jar myapp.jar --spring.profiles.active=dev

# Verify file exists
ls src/main/resources/application-dev.properties

# Check logs
# Spring Boot will log: "The following profiles are active: dev"
```

#### Issue 3: Properties Override Not Working

```properties
# Lower priority file
server.port=8080

# Higher priority file (should win)
server.port=9090

# If not working, check:
# 1. File locations (external > internal)
# 2. Profile precedence
# 3. Command line arguments (highest priority)
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| **Formats** | application.properties (flat) or application.yml (hierarchical) |
| **Profiles** | Environment-specific configurations (dev, test, prod) |
| **Custom Properties** | Use @Value or @ConfigurationProperties |
| **Precedence** | Command line > Environment > Profile-specific > Default |
| **Best Practices** | Use profiles, externalize secrets, type-safe configuration |

### Key Takeaways

1. Spring Boot supports both .properties and .yml formats
2. Use profiles for environment-specific configurations
3. @ConfigurationProperties is preferred over @Value for complex configuration
4. Never commit secrets to version control
5. Property precedence: command line arguments have highest priority
6. YAML supports lists and nested structures natively
7. Always provide sensible defaults with placeholders

---

**Congratulations!** You've completed all Spring Boot topics. You should now have a solid understanding of:
- What Spring Boot is and why it's useful
- How it differs from Spring Framework
- Auto-configuration mechanisms
- Starter dependencies and dependency management
- Spring Boot CLI for rapid prototyping
- Configuration management with properties and YAML

Continue practicing these concepts through hands-on exercises and building real applications!
