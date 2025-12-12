# Configuration Management

## Introduction

In a microservices architecture, managing configuration across multiple services becomes challenging. Changes require updating and redeploying services, and environment-specific configurations multiply the complexity.

### Configuration Challenges

1. **Multiple Environments**: Dev, staging, production configurations
2. **Multiple Services**: Each service has its own configuration
3. **Configuration Changes**: Require rebuild and redeploy
4. **Secrets Management**: Storing sensitive data securely
5. **Consistency**: Ensuring consistent configuration across instances

### Solution: Centralized Configuration

```
Traditional:
Service A → application.yml (in JAR)
Service B → application.yml (in JAR)
Service C → application.yml (in JAR)

Centralized:
Service A ─┐
Service B ─┼→ Config Server → Git Repository
Service C ─┘
```

---

## Spring Cloud Config

### What is Spring Cloud Config?

Spring Cloud Config provides server-side and client-side support for externalized configuration in a distributed system. Configuration is stored in a version-controlled repository and served to applications at runtime.

### Architecture

```
┌─────────────────────────────────────────────────┐
│          Git Repository                         │
│  ├── application.yml         (common)           │
│  ├── order-service.yml       (service-specific) │
│  ├── payment-service.yml                        │
│  └── application-prod.yml    (environment)      │
└────────────────┬────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────┐
│          Config Server                          │
│  ┌─────────────────────────────────────────┐   │
│  │  Serves configuration via REST API      │   │
│  │  /{application}/{profile}/{label}       │   │
│  └─────────────────────────────────────────┘   │
└────────┬────────────────────────────────────────┘
         │
    ┌────┴─────┬──────────┬──────────┐
    ▼          ▼          ▼          ▼
┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐
│Service │ │Service │ │Service │ │Service │
│   A    │ │   B    │ │   C    │ │   D    │
└────────┘ └────────┘ └────────┘ └────────┘
```

### Benefits

1. **Centralized Management**: Single source of truth
2. **Version Control**: Configuration changes tracked in Git
3. **Environment Isolation**: Separate configs per environment
4. **Runtime Updates**: Update config without rebuild
5. **Security**: Encrypted properties support
6. **Audit Trail**: Git history tracks all changes

---

## Config Server

### Setup Config Server

#### Dependencies

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-config-server</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
</dependencies>
```

#### Enable Config Server

```java
package com.example.configserver;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.config.server.EnableConfigServer;

@SpringBootApplication
@EnableConfigServer  // Enable Config Server
public class ConfigServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConfigServerApplication.class, args);
    }
}
```

#### Configuration

**application.yml:**
```yaml
spring:
  application:
    name: config-server
  cloud:
    config:
      server:
        git:
          uri: https://github.com/your-org/config-repo
          # Or use local file system for development
          # uri: file://${user.home}/config-repo
          default-label: main
          search-paths:
            - '{application}'
          clone-on-start: true
          # Authentication for private repos
          username: ${GIT_USERNAME}
          password: ${GIT_PASSWORD}
          # Or use SSH
          # uri: git@github.com:your-org/config-repo.git
          # ignore-local-ssh-settings: false
          # private-key: |
          #   -----BEGIN RSA PRIVATE KEY-----
          #   ...
          #   -----END RSA PRIVATE KEY-----

server:
  port: 8888

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```

### Configuration Repository Structure

```
config-repo/
├── application.yml                    # Default for all applications
├── application-dev.yml                # Default for dev profile
├── application-prod.yml               # Default for prod profile
├── order-service.yml                  # Order service defaults
├── order-service-dev.yml              # Order service dev
├── order-service-prod.yml             # Order service prod
├── payment-service.yml
├── payment-service-dev.yml
└── payment-service-prod.yml
```

### Configuration Files

**application.yml (Common Configuration):**
```yaml
# Common configuration for all services
management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics

logging:
  level:
    root: INFO

spring:
  jackson:
    serialization:
      indent-output: true
```

**application-dev.yml:**
```yaml
# Development environment
logging:
  level:
    root: DEBUG
    com.example: TRACE
```

**application-prod.yml:**
```yaml
# Production environment
logging:
  level:
    root: WARN
    com.example: INFO
```

**order-service.yml:**
```yaml
# Order service specific configuration
spring:
  application:
    name: order-service

server:
  port: 8081

order:
  processing:
    timeout: 30
  payment:
    retries: 3
```

**order-service-prod.yml:**
```yaml
# Order service production overrides
order:
  processing:
    timeout: 60
  payment:
    retries: 5
```

### Accessing Configuration

Config Server exposes REST endpoints:

```
# Get configuration for application
GET /{application}/{profile}
GET /{application}/{profile}/{label}

# Examples:
http://localhost:8888/order-service/default
http://localhost:8888/order-service/dev
http://localhost:8888/order-service/prod/main

# Response format:
{
  "name": "order-service",
  "profiles": ["prod"],
  "label": "main",
  "propertySources": [
    {
      "name": "order-service-prod.yml",
      "source": {
        "order.processing.timeout": 60,
        "order.payment.retries": 5
      }
    }
  ]
}
```

### Securing Config Server

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(authorize -> authorize
                .requestMatchers("/actuator/**").permitAll()
                .anyRequest().authenticated()
            )
            .httpBasic(Customizer.withDefaults());
        return http.build();
    }

    @Bean
    public UserDetailsService userDetailsService() {
        UserDetails user = User.builder()
            .username("configuser")
            .password("{noop}configpass")
            .roles("USER")
            .build();
        return new InMemoryUserDetailsManager(user);
    }
}
```

### Encrypting Properties

**Install Java Cryptography Extension (JCE):**

**Generate encryption key:**
```bash
keytool -genkeypair -alias config-server-key \
  -keyalg RSA -keysize 2048 \
  -dname "CN=Config Server,OU=Unit,O=Organization,L=City,S=State,C=US" \
  -keypass password -keystore config-server.jks \
  -storepass password
```

**Configure encryption:**
```yaml
encrypt:
  key-store:
    location: classpath:/config-server.jks
    password: password
    alias: config-server-key
    secret: password
```

**Encrypt values:**
```bash
# Encrypt
curl -X POST http://localhost:8888/encrypt -d "mysecretpassword"
# Returns: 682bc583f4641835fa2db009355293665d2647dade3375c0ee201de2a49f7bda

# Decrypt
curl -X POST http://localhost:8888/decrypt -d "682bc..."
# Returns: mysecretpassword
```

**Use encrypted values in config:**
```yaml
spring:
  datasource:
    password: '{cipher}682bc583f4641835fa2db009355293665d2647dade3375c0ee201de2a49f7bda'
```

---

## Config Client

### Setup Config Client

#### Dependencies

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-config</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

#### Configuration

**application.yml:**
```yaml
spring:
  application:
    name: order-service
  profiles:
    active: dev
  config:
    import: "optional:configserver:http://localhost:8888"
    # Or with service discovery
    # import: "optional:configserver:"
  cloud:
    config:
      # If using service discovery
      # discovery:
      #   enabled: true
      #   service-id: config-server
      fail-fast: true  # Fail startup if config server unavailable
      retry:
        initial-interval: 1000
        max-attempts: 6
        max-interval: 2000
        multiplier: 1.1

management:
  endpoints:
    web:
      exposure:
        include: refresh,health,info
```

**With authentication:**
```yaml
spring:
  cloud:
    config:
      username: configuser
      password: configpass
```

### Using Configuration

```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties(prefix = "order")
public class OrderProperties {

    private Processing processing = new Processing();
    private Payment payment = new Payment();

    public static class Processing {
        private int timeout;

        public int getTimeout() { return timeout; }
        public void setTimeout(int timeout) { this.timeout = timeout; }
    }

    public static class Payment {
        private int retries;

        public int getRetries() { return retries; }
        public void setRetries(int retries) { this.retries = retries; }
    }

    // Getters and setters
    public Processing getProcessing() { return processing; }
    public void setProcessing(Processing processing) { this.processing = processing; }
    public Payment getPayment() { return payment; }
    public void setPayment(Payment payment) { this.payment = payment; }
}
```

**Usage:**
```java
@Service
public class OrderService {

    private final OrderProperties orderProperties;

    public OrderService(OrderProperties orderProperties) {
        this.orderProperties = orderProperties;
    }

    public Order processOrder(OrderRequest request) {
        int timeout = orderProperties.getProcessing().getTimeout();
        int retries = orderProperties.getPayment().getRetries();

        log.info("Processing order with timeout: {} and retries: {}", timeout, retries);

        // Process order
        return createOrder(request);
    }
}
```

**Using @Value:**
```java
@RestController
public class OrderController {

    @Value("${order.processing.timeout}")
    private int processingTimeout;

    @Value("${order.payment.retries:3}")  // Default value: 3
    private int paymentRetries;

    @GetMapping("/config")
    public Map<String, Object> getConfig() {
        return Map.of(
            "timeout", processingTimeout,
            "retries", paymentRetries
        );
    }
}
```

---

## Refresh Scope

### What is Refresh Scope?

Refresh Scope allows beans to reload their configuration without restarting the application.

### Enable Refresh

**Add dependency:**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

**Enable refresh endpoint:**
```yaml
management:
  endpoints:
    web:
      exposure:
        include: refresh
```

### Using @RefreshScope

```java
import org.springframework.cloud.context.config.annotation.RefreshScope;

@RestController
@RefreshScope  // Enable refresh for this bean
public class ConfigController {

    @Value("${order.processing.timeout}")
    private int processingTimeout;

    @Value("${order.payment.retries}")
    private int paymentRetries;

    @GetMapping("/api/config")
    public Map<String, Object> getConfig() {
        return Map.of(
            "timeout", processingTimeout,
            "retries", paymentRetries
        );
    }
}
```

**With ConfigurationProperties:**
```java
@Component
@RefreshScope
@ConfigurationProperties(prefix = "order")
public class OrderProperties {
    // Properties will be refreshed
}
```

### Refreshing Configuration

**1. Update configuration in Git repository**

**2. Trigger refresh:**
```bash
# Refresh single service
curl -X POST http://localhost:8081/actuator/refresh

# Response shows changed properties:
[
  "order.processing.timeout",
  "order.payment.retries"
]
```

**3. Verify changes:**
```bash
curl http://localhost:8081/api/config
```

### Refresh All Services

#### Spring Cloud Bus

**Dependencies:**
```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>
```

**Configuration:**
```yaml
spring:
  rabbitmq:
    host: localhost
    port: 5672
    username: guest
    password: guest

management:
  endpoints:
    web:
      exposure:
        include: busrefresh
```

**Refresh all services:**
```bash
# Refresh all instances of all services
curl -X POST http://localhost:8888/actuator/busrefresh

# Refresh all instances of specific service
curl -X POST http://localhost:8888/actuator/busrefresh?destination=order-service:**

# Refresh specific instance
curl -X POST http://localhost:8888/actuator/busrefresh?destination=order-service:8081
```

### Automated Refresh with Webhooks

**GitHub Webhook:**

**1. Configure webhook in GitHub repository:**
```
URL: http://your-config-server/monitor
Content type: application/json
Events: Just the push event
```

**2. Add monitor dependency:**
```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-monitor</artifactId>
</dependency>
```

**3. Configuration:**
```yaml
management:
  endpoints:
    web:
      exposure:
        include: busrefresh
```

**When you push to Git → GitHub webhook triggers → Config Server → Bus Refresh → All services updated**

---

## Advanced Configuration

### Multiple Configuration Sources

```yaml
spring:
  cloud:
    config:
      server:
        composite:
          - type: git
            uri: https://github.com/your-org/common-config
            search-paths: common
          - type: git
            uri: https://github.com/your-org/service-config
            search-paths: '{application}'
          - type: native
            search-locations: classpath:/config
```

### Profile-Specific Configuration

```yaml
spring:
  profiles:
    active: dev

# In Git repository: order-service-dev.yml
order:
  processing:
    timeout: 30

# order-service-prod.yml
order:
  processing:
    timeout: 60
```

### Property Overrides (Server-Side)

```yaml
spring:
  cloud:
    config:
      server:
        overrides:
          # These cannot be overridden by clients
          eureka.client.serviceUrl.defaultZone: http://eureka:8761/eureka/
          management.endpoints.web.exposure.include: health,info
```

### Vault Integration

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-vault-config</artifactId>
</dependency>
```

```yaml
spring:
  cloud:
    vault:
      host: localhost
      port: 8200
      scheme: http
      authentication: TOKEN
      token: ${VAULT_TOKEN}
      kv:
        enabled: true
        backend: secret
        application-name: order-service
```

### Native File System

```yaml
spring:
  profiles:
    active: native
  cloud:
    config:
      server:
        native:
          search-locations: file:///config-repo
```

---

## Configuration Best Practices

### 1. Separate Concerns

```
config-repo/
├── common/
│   ├── application.yml           # Common to all
│   ├── logging.yml              # Logging config
│   └── security.yml             # Security config
├── services/
│   ├── order-service/
│   │   ├── order-service.yml
│   │   ├── order-service-dev.yml
│   │   └── order-service-prod.yml
│   └── payment-service/
└── environments/
    ├── dev.yml
    ├── staging.yml
    └── prod.yml
```

### 2. Use Property Hierarchy

```
Priority (highest to lowest):
1. Command line args
2. Environment variables
3. Config server properties
4. application.yml (in JAR)
```

### 3. Encrypt Sensitive Data

```yaml
# Always encrypt passwords, keys, secrets
spring:
  datasource:
    password: '{cipher}AQA...'
  security:
    oauth2:
      client:
        registration:
          app:
            client-secret: '{cipher}AQB...'
```

### 4. Use Meaningful Names

```yaml
# Good
order:
  processing:
    timeout-seconds: 30
    max-retries: 3
  notification:
    email:
      enabled: true
      from: noreply@example.com

# Bad
ord.proc.t: 30
ord.proc.r: 3
```

### 5. Document Configuration

```yaml
# Order Service Configuration

order:
  processing:
    # Maximum time (in seconds) to wait for order processing
    timeout-seconds: 30

    # Number of retry attempts for failed orders
    max-retries: 3
```

### 6. Environment-Specific Overrides

```yaml
# application.yml (defaults)
database:
  pool:
    size: 10
    timeout: 5000

# application-prod.yml (production overrides)
database:
  pool:
    size: 50
    timeout: 10000
```

### 7. Health Checks for Config Server

```yaml
management:
  health:
    config:
      enabled: true
```

### 8. Fail Fast in Production

```yaml
spring:
  cloud:
    config:
      fail-fast: true  # Fail startup if config unavailable
```

### 9. Retry Configuration

```yaml
spring:
  cloud:
    config:
      retry:
        initial-interval: 1000
        max-attempts: 6
        max-interval: 2000
        multiplier: 1.1
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| **Config Server** | Centralized configuration management |
| **Git Repository** | Version-controlled configuration storage |
| **Config Client** | Retrieves configuration from server |
| **Refresh Scope** | Update configuration without restart |
| **Spring Cloud Bus** | Broadcast refresh to all services |
| **Encryption** | Secure sensitive configuration data |
| **Profiles** | Environment-specific configuration |

## Configuration Checklist

- [ ] Set up Config Server with Git repository
- [ ] Organize configuration files logically
- [ ] Use profiles for environments (dev, staging, prod)
- [ ] Encrypt sensitive properties
- [ ] Implement @RefreshScope for dynamic updates
- [ ] Configure Spring Cloud Bus for distributed refresh
- [ ] Set up webhooks for automated refresh
- [ ] Secure Config Server with authentication
- [ ] Enable health checks
- [ ] Configure retry and fail-fast
- [ ] Document configuration properties
- [ ] Test configuration changes before production
- [ ] Monitor configuration access

## Next Topic

Continue to [Monitoring and Observability](./08-monitoring-observability.md) to learn about distributed tracing, logging aggregation, and metrics collection in microservices.
