# Auto-configuration

## Introduction

Auto-configuration is one of Spring Boot's most powerful features. It automatically configures your Spring application based on the dependencies present in your classpath, eliminating the need for extensive manual configuration.

### What is Auto-Configuration?

Auto-configuration is Spring Boot's way of automatically setting up beans and configurations based on:
- Dependencies in your classpath
- Properties you've defined
- Beans you've already configured

**Key Principle**: Convention over Configuration

---

## How Auto-Configuration Works

### The Magic Behind the Scenes

```
Application Startup
       ↓
@SpringBootApplication
       ↓
@EnableAutoConfiguration
       ↓
Scans spring.factories
       ↓
Loads Auto-Configuration Classes
       ↓
Evaluates @Conditional Annotations
       ↓
Configures Beans (if conditions met)
       ↓
Application Ready
```

### The @SpringBootApplication Annotation

```java
@SpringBootApplication
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}

// @SpringBootApplication is equivalent to:
@Configuration        // Allows bean definitions
@EnableAutoConfiguration  // Enables auto-configuration
@ComponentScan       // Scans for components
```

### @EnableAutoConfiguration Explained

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
    // This annotation triggers the auto-configuration process
}
```

---

## Auto-Configuration Classes

### Location and Structure

Spring Boot's auto-configuration classes are located in:
```
spring-boot-autoconfigure-{version}.jar
└── META-INF/
    └── spring.factories
        # Lists all auto-configuration classes
```

### Example: spring.factories

```properties
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration,\
org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration,\
# ... hundreds more
```

---

## Conditional Annotations

Auto-configuration uses conditional annotations to determine whether to apply configurations.

### Common @Conditional Annotations

| Annotation | Condition | Example |
|------------|-----------|---------|
| @ConditionalOnClass | Class is present in classpath | @ConditionalOnClass(DataSource.class) |
| @ConditionalOnMissingClass | Class is not in classpath | @ConditionalOnMissingClass("org.thymeleaf.spring5.SpringTemplateEngine") |
| @ConditionalOnBean | Bean exists in context | @ConditionalOnBean(DataSource.class) |
| @ConditionalOnMissingBean | Bean doesn't exist in context | @ConditionalOnMissingBean(DataSource.class) |
| @ConditionalOnProperty | Property has specific value | @ConditionalOnProperty("spring.jpa.enabled") |
| @ConditionalOnResource | Resource is present | @ConditionalOnResource("classpath:schema.sql") |
| @ConditionalOnWebApplication | Application is a web app | @ConditionalOnWebApplication |
| @ConditionalOnNotWebApplication | Application is not a web app | @ConditionalOnNotWebApplication |

### Examples of Conditional Configuration

```java
// Example 1: DataSource Auto-Configuration
@Configuration
@ConditionalOnClass({ DataSource.class, EmbeddedDatabaseType.class })
@EnableConfigurationProperties(DataSourceProperties.class)
public class DataSourceAutoConfiguration {

    @Configuration
    @Conditional(EmbeddedDatabaseCondition.class)
    @ConditionalOnMissingBean({ DataSource.class, XADataSource.class })
    protected static class EmbeddedDatabaseConfiguration {

        @Bean
        public DataSource dataSource() {
            // Create embedded database if no DataSource configured
            return new EmbeddedDatabaseBuilder()
                .setType(EmbeddedDatabaseType.H2)
                .build();
        }
    }
}

// Example 2: JPA Auto-Configuration
@Configuration
@ConditionalOnClass({ LocalContainerEntityManagerFactoryBean.class, EntityManager.class })
@ConditionalOnBean(DataSource.class)
public class HibernateJpaAutoConfiguration {

    @Bean
    @ConditionalOnMissingBean
    public LocalContainerEntityManagerFactoryBean entityManagerFactory() {
        // Configure EntityManagerFactory automatically
    }
}

// Example 3: Web MVC Auto-Configuration
@Configuration
@ConditionalOnWebApplication(type = Type.SERVLET)
@ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class })
@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
public class WebMvcAutoConfiguration {

    @Bean
    @ConditionalOnMissingBean
    public InternalResourceViewResolver defaultViewResolver() {
        // Configure view resolver
    }
}
```

---

## Real-World Auto-Configuration Examples

### Example 1: Database Configuration

```java
// When you add this dependency:
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
</dependency>

// Spring Boot automatically configures:
// 1. DataSource (H2 in-memory database)
// 2. EntityManagerFactory
// 3. TransactionManager
// 4. JPA repositories

// You can immediately use:
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    List<User> findByLastName(String lastName);
}

@Service
@Transactional
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public User saveUser(User user) {
        return userRepository.save(user);
    }
}
```

### Example 2: Web Application

```java
// When you add this dependency:
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

// Spring Boot automatically configures:
// 1. Embedded Tomcat server
// 2. DispatcherServlet
// 3. Jackson for JSON conversion
// 4. Error handling
// 5. Static resource handling

// You can immediately create REST endpoints:
@RestController
@RequestMapping("/api/users")
public class UserController {

    @GetMapping
    public List<User> getUsers() {
        return Arrays.asList(
            new User("John", "Doe"),
            new User("Jane", "Smith")
        );
    }
}

// No need to configure:
// - Jackson ObjectMapper
// - Message converters
// - View resolvers
// - Exception handlers
```

### Example 3: Security

```java
// When you add this dependency:
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>

// Spring Boot automatically configures:
// 1. Basic authentication
// 2. Default user (username: user, password: logged in console)
// 3. CSRF protection
// 4. Security headers
// 5. Login/logout pages

// Default security without any code!
// On startup, you'll see:
// Using generated security password: 8e557245-73e2-4286-969a-ff57fe326336

// Override with properties:
spring.security.user.name=admin
spring.security.user.password=secret
```

---

## Viewing Auto-Configuration Report

### Enable Debug Logging

```properties
# application.properties
debug=true

# or
logging.level.org.springframework.boot.autoconfigure=DEBUG
```

### Run Application

```bash
mvn spring-boot:run
```

### Auto-Configuration Report Output

```
============================
CONDITIONS EVALUATION REPORT
============================

Positive matches:
-----------------

   DataSourceAutoConfiguration matched:
      - @ConditionalOnClass found required classes 'javax.sql.DataSource', 'org.springframework.jdbc.datasource.embedded.EmbeddedDatabaseType'

   HibernateJpaAutoConfiguration matched:
      - @ConditionalOnClass found required classes 'org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean', 'javax.persistence.EntityManager'

   JpaRepositoriesAutoConfiguration matched:
      - @ConditionalOnClass found required class 'org.springframework.data.jpa.repository.JpaRepository'

Negative matches:
-----------------

   ActiveMQAutoConfiguration:
      Did not match:
         - @ConditionalOnClass did not find required class 'javax.jms.ConnectionFactory'

   MongoAutoConfiguration:
      Did not match:
         - @ConditionalOnClass did not find required class 'com.mongodb.client.MongoClient'
```

### Programmatic Access to Report

```java
@RestController
public class AutoConfigReportController {

    @Autowired
    private ConditionEvaluationReport report;

    @GetMapping("/autoconfig")
    public Map<String, ConditionEvaluationReport.ConditionAndOutcomes> getReport() {
        return report.getConditionAndOutcomesBySource();
    }
}
```

---

## Customizing Auto-Configuration

### 1. Using Properties

```properties
# application.properties

# Database configuration
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=secret

# JPA configuration
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update

# Server configuration
server.port=9090
server.servlet.context-path=/api

# Logging
logging.level.org.springframework.web=DEBUG
```

### 2. Providing Your Own Beans

```java
// Override auto-configured beans by defining your own

@Configuration
public class CustomDataSourceConfig {

    // This bean will be used instead of auto-configured DataSource
    @Bean
    public DataSource dataSource() {
        HikariConfig config = new HikariConfig();
        config.setJdbcUrl("jdbc:mysql://localhost:3306/mydb");
        config.setUsername("root");
        config.setPassword("secret");
        config.setMaximumPoolSize(20);
        config.setMinimumIdle(5);
        return new HikariDataSource(config);
    }
}
```

### 3. Excluding Auto-Configuration Classes

```java
// Method 1: Using @SpringBootApplication
@SpringBootApplication(exclude = {
    DataSourceAutoConfiguration.class,
    HibernateJpaAutoConfiguration.class
})
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}

// Method 2: Using properties
// application.properties
spring.autoconfigure.exclude=\
  org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration,\
  org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration
```

### 4. Using @ConditionalOnProperty

```java
@Configuration
public class FeatureConfiguration {

    @Bean
    @ConditionalOnProperty(
        name = "feature.advanced.enabled",
        havingValue = "true",
        matchIfMissing = false
    )
    public AdvancedFeature advancedFeature() {
        return new AdvancedFeature();
    }

    @Bean
    @ConditionalOnProperty(
        name = "feature.advanced.enabled",
        havingValue = "false",
        matchIfMissing = true
    )
    public BasicFeature basicFeature() {
        return new BasicFeature();
    }
}

// application.properties
feature.advanced.enabled=true
```

---

## Creating Custom Auto-Configuration

### Step 1: Create Configuration Class

```java
package com.example.autoconfigure;

import org.springframework.boot.autoconfigure.condition.ConditionalOnClass;
import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
@ConditionalOnClass(MyCustomService.class)
@EnableConfigurationProperties(MyCustomProperties.class)
public class MyCustomAutoConfiguration {

    @Bean
    @ConditionalOnMissingBean
    public MyCustomService myCustomService(MyCustomProperties properties) {
        return new MyCustomService(properties);
    }
}
```

### Step 2: Create Properties Class

```java
package com.example.autoconfigure;

import org.springframework.boot.context.properties.ConfigurationProperties;

@ConfigurationProperties(prefix = "mycustom")
public class MyCustomProperties {

    private String name = "default-name";
    private int timeout = 5000;
    private boolean enabled = true;

    // Getters and setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public int getTimeout() { return timeout; }
    public void setTimeout(int timeout) { this.timeout = timeout; }

    public boolean isEnabled() { return enabled; }
    public void setEnabled(boolean enabled) { this.enabled = enabled; }
}
```

### Step 3: Create Service Class

```java
package com.example.autoconfigure;

public class MyCustomService {

    private final MyCustomProperties properties;

    public MyCustomService(MyCustomProperties properties) {
        this.properties = properties;
    }

    public void doSomething() {
        if (properties.isEnabled()) {
            System.out.println("Service " + properties.getName() +
                             " executing with timeout: " + properties.getTimeout());
        }
    }
}
```

### Step 4: Register Auto-Configuration

Create `src/main/resources/META-INF/spring.factories`:

```properties
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.example.autoconfigure.MyCustomAutoConfiguration
```

### Step 5: Use Custom Auto-Configuration

```java
// In your application
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}

@RestController
public class TestController {

    @Autowired
    private MyCustomService myCustomService;

    @GetMapping("/test")
    public String test() {
        myCustomService.doSomething();
        return "Test completed";
    }
}

// Configure via application.properties
mycustom.name=my-service
mycustom.timeout=10000
mycustom.enabled=true
```

---

## Auto-Configuration Order

### Controlling Configuration Order

```java
// Execute before another configuration
@AutoConfigureBefore(DataSourceAutoConfiguration.class)
@Configuration
public class MyDatabaseConfig {
    // This runs before DataSourceAutoConfiguration
}

// Execute after another configuration
@AutoConfigureAfter(DataSourceAutoConfiguration.class)
@Configuration
public class MyRepositoryConfig {
    // This runs after DataSourceAutoConfiguration
}

// Specify order explicitly
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE)
@Configuration
public class HighPriorityConfig {
    // This runs first
}
```

---

## Common Auto-Configuration Scenarios

### Scenario 1: Multiple DataSources

```java
@Configuration
public class MultipleDataSourceConfig {

    @Primary
    @Bean(name = "primaryDataSource")
    @ConfigurationProperties(prefix = "spring.datasource.primary")
    public DataSource primaryDataSource() {
        return DataSourceBuilder.create().build();
    }

    @Bean(name = "secondaryDataSource")
    @ConfigurationProperties(prefix = "spring.datasource.secondary")
    public DataSource secondaryDataSource() {
        return DataSourceBuilder.create().build();
    }
}

// application.properties
spring.datasource.primary.url=jdbc:mysql://localhost:3306/db1
spring.datasource.primary.username=user1
spring.datasource.primary.password=pass1

spring.datasource.secondary.url=jdbc:mysql://localhost:3306/db2
spring.datasource.secondary.username=user2
spring.datasource.secondary.password=pass2
```

### Scenario 2: Environment-Specific Configuration

```java
@Configuration
@Profile("dev")
public class DevAutoConfiguration {

    @Bean
    public DataSource dataSource() {
        // H2 in-memory database for development
        return new EmbeddedDatabaseBuilder()
            .setType(EmbeddedDatabaseType.H2)
            .build();
    }
}

@Configuration
@Profile("prod")
public class ProdAutoConfiguration {

    @Bean
    public DataSource dataSource() {
        // Production database configuration
        HikariConfig config = new HikariConfig();
        config.setJdbcUrl(System.getenv("DB_URL"));
        config.setUsername(System.getenv("DB_USER"));
        config.setPassword(System.getenv("DB_PASS"));
        return new HikariDataSource(config);
    }
}
```

### Scenario 3: Feature Flags

```java
@Configuration
public class FeatureFlagConfiguration {

    @Bean
    @ConditionalOnProperty(name = "feature.cache.enabled", havingValue = "true")
    public CacheManager cacheManager() {
        return new ConcurrentMapCacheManager("users", "products");
    }

    @Bean
    @ConditionalOnProperty(name = "feature.async.enabled", havingValue = "true")
    @EnableAsync
    public Executor asyncExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(2);
        executor.setMaxPoolSize(5);
        executor.setQueueCapacity(500);
        executor.setThreadNamePrefix("async-");
        executor.initialize();
        return executor;
    }
}

// application.properties
feature.cache.enabled=true
feature.async.enabled=false
```

---

## Debugging Auto-Configuration Issues

### Common Issues and Solutions

#### Issue 1: Bean Not Found

```java
// Problem: Required bean not auto-configured
// Error: Field userRepository required a bean of type 'UserRepository' that could not be found

// Solution 1: Check dependencies
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

// Solution 2: Check component scanning
@SpringBootApplication  // Scans current package and sub-packages
public class Application {
    // Make sure your repositories are in or under this package
}

// Solution 3: Enable JPA repositories explicitly
@EnableJpaRepositories(basePackages = "com.example.repository")
@SpringBootApplication
public class Application { }
```

#### Issue 2: Multiple Beans of Same Type

```java
// Problem: Multiple DataSource beans cause conflict

// Solution: Mark one as @Primary
@Primary
@Bean
public DataSource primaryDataSource() {
    return DataSourceBuilder.create().build();
}

@Bean
public DataSource secondaryDataSource() {
    return DataSourceBuilder.create().build();
}
```

#### Issue 3: Auto-Configuration Not Applied

```java
// Problem: Expected auto-configuration doesn't work

// Check 1: Verify dependency is in classpath
// Check 2: Verify conditions are met
// Check 3: Check if excluded

@SpringBootApplication(exclude = {
    // Make sure not excluding needed auto-configuration
})
public class Application { }

// Check 4: Review auto-configuration report
debug=true  // in application.properties
```

---

## Best Practices

### 1. Let Auto-Configuration Do Its Job

```java
// Good: Use auto-configuration
@SpringBootApplication
public class Application { }

// Bad: Over-configuring manually
@Configuration
public class UnnecessaryConfig {
    @Bean
    public DataSource dataSource() { }  // Already auto-configured!
    @Bean
    public EntityManagerFactory emf() { }  // Already auto-configured!
}
```

### 2. Override Only When Necessary

```java
// Good: Override specific settings via properties
spring.datasource.hikari.maximum-pool-size=20

// Okay: Override when you need custom behavior
@Bean
public DataSource dataSource() {
    // Custom connection pool configuration
}

// Bad: Recreating what's already auto-configured
@Bean
public JacksonObjectMapper objectMapper() {
    return new ObjectMapper();  // Already auto-configured!
}
```

### 3. Use @ConditionalOnProperty for Features

```java
@Configuration
public class OptionalFeaturesConfig {

    @Bean
    @ConditionalOnProperty("app.feature.email.enabled")
    public EmailService emailService() {
        return new EmailService();
    }

    @Bean
    @ConditionalOnProperty("app.feature.sms.enabled")
    public SmsService smsService() {
        return new SmsService();
    }
}
```

### 4. Document Your Customizations

```java
/**
 * Custom DataSource configuration.
 *
 * Overrides default auto-configuration to use HikariCP
 * with specific connection pool settings required for
 * our high-traffic production environment.
 *
 * Auto-configuration is disabled via:
 * spring.autoconfigure.exclude=DataSourceAutoConfiguration
 */
@Configuration
public class CustomDataSourceConfig {
    @Bean
    public DataSource dataSource() {
        // Custom configuration
    }
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| What is Auto-Configuration | Automatic bean configuration based on classpath and properties |
| How It Works | @EnableAutoConfiguration + Conditional annotations + spring.factories |
| Conditional Annotations | @ConditionalOnClass, @ConditionalOnBean, @ConditionalOnProperty |
| Customization | Properties, custom beans, exclusions |
| Debugging | Debug logging, auto-configuration report |
| Best Practices | Let auto-config work, override only when needed, use properties |

## Next Topic

Continue to [Starter Dependencies](./04-starter-dependencies.md) to learn about Spring Boot's dependency management system.
