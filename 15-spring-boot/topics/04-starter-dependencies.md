# Starter Dependencies

## Introduction

Spring Boot Starter Dependencies are pre-configured dependency descriptors that you can include in your application. They bring in all the libraries you need for a particular functionality, with versions that are tested and known to work together.

### What Are Starter Dependencies?

Starters are a set of convenient dependency descriptors that you can include in your application. You get a one-stop shop for all the Spring and related technologies without having to hunt through sample code and copy-paste loads of dependency descriptors.

**Key Benefits:**
- Simplified dependency management
- Version compatibility guaranteed
- Reduced configuration overhead
- Quick project setup
- Curated set of dependencies

---

## How Starters Work

### Traditional Dependency Management

```xml
<!-- Traditional approach - managing many dependencies manually -->
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
        <version>5.3.20</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.3.20</version>
    </dependency>
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.13.3</version>
    </dependency>
    <dependency>
        <groupId>org.hibernate.validator</groupId>
        <artifactId>hibernate-validator</artifactId>
        <version>6.2.3.Final</version>
    </dependency>
    <dependency>
        <groupId>org.apache.tomcat.embed</groupId>
        <artifactId>tomcat-embed-core</artifactId>
        <version>9.0.62</version>
    </dependency>
    <!-- ... many more dependencies -->
</dependencies>
```

### Spring Boot Starter Approach

```xml
<!-- Spring Boot approach - single starter -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.2.0</version>
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <!-- No version needed - managed by parent -->
        <!-- Includes all dependencies from previous example and more -->
    </dependency>
</dependencies>
```

---

## Common Spring Boot Starters

### Web Application Starters

| Starter | Purpose | Key Dependencies |
|---------|---------|------------------|
| spring-boot-starter-web | Web applications with Spring MVC | Tomcat, Spring MVC, Jackson, Validation |
| spring-boot-starter-webflux | Reactive web applications | Netty, Spring WebFlux, Reactor |
| spring-boot-starter-websocket | WebSocket applications | WebSocket support |
| spring-boot-starter-jersey | JAX-RS with Jersey | Jersey, JAX-RS |
| spring-boot-starter-thymeleaf | Thymeleaf templating | Thymeleaf template engine |
| spring-boot-starter-freemarker | FreeMarker templating | FreeMarker template engine |

#### Example: Web Starter

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

```java
// Immediately use web features
@RestController
@RequestMapping("/api")
public class ApiController {

    @GetMapping("/hello")
    public Map<String, String> hello() {
        return Map.of("message", "Hello, World!");
    }

    @PostMapping("/users")
    public User createUser(@RequestBody User user) {
        // Jackson automatically converts JSON to User object
        return user;
    }
}
```

### Data Access Starters

| Starter | Purpose | Key Dependencies |
|---------|---------|------------------|
| spring-boot-starter-data-jpa | JPA with Hibernate | Hibernate, Spring Data JPA, JDBC |
| spring-boot-starter-jdbc | JDBC with connection pooling | HikariCP, Spring JDBC |
| spring-boot-starter-data-mongodb | MongoDB | Spring Data MongoDB, MongoDB driver |
| spring-boot-starter-data-redis | Redis | Spring Data Redis, Lettuce |
| spring-boot-starter-data-elasticsearch | Elasticsearch | Spring Data Elasticsearch |
| spring-boot-starter-data-cassandra | Cassandra | Spring Data Cassandra |
| spring-boot-starter-data-neo4j | Neo4j graph database | Spring Data Neo4j |

#### Example: JPA Starter

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

```java
// Entity
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String username;
    private String email;

    // Getters and setters
}

// Repository - no implementation needed!
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    List<User> findByUsername(String username);
    Optional<User> findByEmail(String email);
}

// Service
@Service
@Transactional
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public User saveUser(User user) {
        return userRepository.save(user);
    }

    public List<User> findAll() {
        return userRepository.findAll();
    }
}
```

### Security Starter

| Starter | Purpose | Key Dependencies |
|---------|---------|------------------|
| spring-boot-starter-security | Security features | Spring Security |
| spring-boot-starter-oauth2-client | OAuth 2.0 client | Spring Security OAuth2 |
| spring-boot-starter-oauth2-resource-server | OAuth 2.0 resource server | Spring Security OAuth2 Resource Server |

#### Example: Security Starter

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

```java
// Basic security configuration
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/public/**").permitAll()
                .requestMatchers("/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .formLogin(form -> form
                .loginPage("/login")
                .permitAll()
            )
            .logout(logout -> logout.permitAll());

        return http.build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

### Messaging Starters

| Starter | Purpose | Key Dependencies |
|---------|---------|------------------|
| spring-boot-starter-amqp | RabbitMQ | Spring AMQP, RabbitMQ client |
| spring-boot-starter-kafka | Apache Kafka | Spring Kafka |
| spring-boot-starter-artemis | Apache Artemis | Spring JMS, Artemis client |
| spring-boot-starter-activemq | ActiveMQ | Spring JMS, ActiveMQ client |

#### Example: Kafka Starter

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-kafka</artifactId>
</dependency>
```

```java
// Producer
@Service
public class MessageProducer {

    @Autowired
    private KafkaTemplate<String, String> kafkaTemplate;

    public void sendMessage(String topic, String message) {
        kafkaTemplate.send(topic, message);
    }
}

// Consumer
@Component
public class MessageConsumer {

    @KafkaListener(topics = "my-topic", groupId = "my-group")
    public void listen(String message) {
        System.out.println("Received: " + message);
    }
}

// Configuration
spring.kafka.bootstrap-servers=localhost:9092
spring.kafka.consumer.group-id=my-group
spring.kafka.consumer.auto-offset-reset=earliest
```

### Testing Starters

| Starter | Purpose | Key Dependencies |
|---------|---------|------------------|
| spring-boot-starter-test | Testing support | JUnit 5, Mockito, AssertJ, Spring Test |

#### Example: Test Starter

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

```java
// Integration test
@SpringBootTest
@AutoConfigureMockMvc
public class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    public void testGetUsers() throws Exception {
        mockMvc.perform(get("/api/users"))
            .andExpect(status().isOk())
            .andExpect(content().contentType(MediaType.APPLICATION_JSON))
            .andExpect(jsonPath("$").isArray());
    }
}

// Unit test
@ExtendWith(MockitoExtension.class)
public class UserServiceTest {

    @Mock
    private UserRepository userRepository;

    @InjectMocks
    private UserService userService;

    @Test
    public void testSaveUser() {
        User user = new User("john", "john@example.com");
        when(userRepository.save(any(User.class))).thenReturn(user);

        User saved = userService.saveUser(user);

        assertThat(saved).isNotNull();
        assertThat(saved.getUsername()).isEqualTo("john");
        verify(userRepository, times(1)).save(user);
    }
}
```

### Production Starters

| Starter | Purpose | Key Dependencies |
|---------|---------|------------------|
| spring-boot-starter-actuator | Production features | Micrometer, Health checks, Metrics |
| spring-boot-starter-validation | Bean validation | Hibernate Validator |
| spring-boot-starter-cache | Caching abstraction | Spring Cache |
| spring-boot-starter-aop | Aspect-Oriented Programming | Spring AOP, AspectJ |

#### Example: Actuator Starter

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

```properties
# application.properties
management.endpoints.web.exposure.include=health,info,metrics,env
management.endpoint.health.show-details=always
```

```bash
# Access actuator endpoints
curl http://localhost:8080/actuator/health
curl http://localhost:8080/actuator/metrics
curl http://localhost:8080/actuator/info
```

---

## Complete Starters Reference Table

### Core Starters

| Starter | Description |
|---------|-------------|
| spring-boot-starter | Core starter, including auto-configuration support, logging, and YAML |
| spring-boot-starter-logging | Logging using Logback (default) |
| spring-boot-starter-log4j2 | Logging using Log4j2 |
| spring-boot-starter-json | JSON support with Jackson |

### Web Starters

| Starter | Description |
|---------|-------------|
| spring-boot-starter-web | Full-stack web development with Tomcat and Spring MVC |
| spring-boot-starter-webflux | Reactive web applications with Spring WebFlux |
| spring-boot-starter-websocket | WebSocket applications |
| spring-boot-starter-jersey | RESTful web applications using JAX-RS and Jersey |
| spring-boot-starter-hateoas | HATEOAS-based RESTful web services |

### Template Engines

| Starter | Description |
|---------|-------------|
| spring-boot-starter-thymeleaf | Thymeleaf template engine |
| spring-boot-starter-freemarker | FreeMarker template engine |
| spring-boot-starter-mustache | Mustache template engine |
| spring-boot-starter-groovy-templates | Groovy template engine |

### Data Access

| Starter | Description |
|---------|-------------|
| spring-boot-starter-data-jpa | Spring Data JPA with Hibernate |
| spring-boot-starter-jdbc | JDBC with HikariCP connection pool |
| spring-boot-starter-data-mongodb | MongoDB document database |
| spring-boot-starter-data-mongodb-reactive | Reactive MongoDB |
| spring-boot-starter-data-redis | Redis key-value store |
| spring-boot-starter-data-redis-reactive | Reactive Redis |
| spring-boot-starter-data-elasticsearch | Elasticsearch search and analytics |
| spring-boot-starter-data-cassandra | Cassandra distributed database |
| spring-boot-starter-data-cassandra-reactive | Reactive Cassandra |
| spring-boot-starter-data-neo4j | Neo4j graph database |
| spring-boot-starter-data-couchbase | Couchbase document database |
| spring-boot-starter-data-couchbase-reactive | Reactive Couchbase |
| spring-boot-starter-data-ldap | LDAP directory services |
| spring-boot-starter-data-rest | REST repositories over Spring Data |

### Messaging

| Starter | Description |
|---------|-------------|
| spring-boot-starter-amqp | RabbitMQ with Spring AMQP |
| spring-boot-starter-kafka | Apache Kafka |
| spring-boot-starter-artemis | Apache Artemis JMS broker |
| spring-boot-starter-activemq | ActiveMQ JMS broker |

### Cloud and Distributed Systems

| Starter | Description |
|---------|-------------|
| spring-boot-starter-cloud-connectors | Cloud platform connectors |
| spring-boot-starter-batch | Spring Batch for batch processing |
| spring-boot-starter-integration | Spring Integration for enterprise integration patterns |
| spring-boot-starter-mail | Email sending support |
| spring-boot-starter-quartz | Quartz scheduler |

### Testing and Development

| Starter | Description |
|---------|-------------|
| spring-boot-starter-test | Testing with JUnit 5, Mockito, AssertJ |
| spring-boot-starter-test-web | Web testing support |
| spring-boot-starter-test-webflux | WebFlux testing support |

---

## spring-boot-starter-parent

### What is the Parent POM?

The `spring-boot-starter-parent` is a special starter that provides:
- Dependency management
- Plugin management
- Default plugin configuration
- Resource filtering
- Sensible defaults

### Using the Parent POM

```xml
<project>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.0</version>
        <relativePath/>
    </parent>

    <groupId>com.example</groupId>
    <artifactId>myapp</artifactId>
    <version>1.0.0</version>

    <dependencies>
        <!-- No versions needed for Spring Boot starters -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
</project>
```

### Parent POM Features

```xml
<!-- Provided by parent POM -->
<properties>
    <java.version>17</java.version>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>${java.version}</maven.compiler.source>
    <maven.compiler.target>${java.version}</maven.compiler.target>
</properties>

<!-- Dependency versions managed -->
<dependencyManagement>
    <dependencies>
        <!-- Hundreds of managed versions -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>6.1.0</version>
        </dependency>
        <!-- ... -->
    </dependencies>
</dependencyManagement>

<!-- Plugin configuration -->
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

### Without Parent POM

If you can't use the parent POM (e.g., your project already has a corporate parent):

```xml
<project>
    <!-- Your own parent -->
    <parent>
        <groupId>com.example</groupId>
        <artifactId>corporate-parent</artifactId>
        <version>1.0.0</version>
    </parent>

    <!-- Import Spring Boot dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>3.2.0</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
</project>
```

---

## Creating Custom Starters

### When to Create a Custom Starter

Create custom starters when:
- You have reusable functionality across multiple projects
- You want to provide auto-configuration for a library
- You're building internal company frameworks
- You want to simplify dependency management for teams

### Custom Starter Structure

```
my-custom-starter/
├── my-custom-spring-boot-starter/      # Starter module
│   └── pom.xml                         # Dependencies only
└── my-custom-spring-boot-autoconfigure/ # Auto-config module
    ├── pom.xml
    └── src/main/
        ├── java/
        │   └── com/example/autoconfigure/
        │       ├── MyCustomAutoConfiguration.java
        │       ├── MyCustomProperties.java
        │       └── MyCustomService.java
        └── resources/
            └── META-INF/
                └── spring.factories
```

### Step 1: Create Auto-Configure Module

```xml
<!-- my-custom-spring-boot-autoconfigure/pom.xml -->
<project>
    <groupId>com.example</groupId>
    <artifactId>my-custom-spring-boot-autoconfigure</artifactId>
    <version>1.0.0</version>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-autoconfigure</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
    </dependencies>
</project>
```

### Step 2: Create Configuration Class

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

    private final MyCustomProperties properties;

    public MyCustomAutoConfiguration(MyCustomProperties properties) {
        this.properties = properties;
    }

    @Bean
    @ConditionalOnMissingBean
    public MyCustomService myCustomService() {
        return new MyCustomService(properties);
    }
}
```

### Step 3: Create Properties Class

```java
package com.example.autoconfigure;

import org.springframework.boot.context.properties.ConfigurationProperties;

@ConfigurationProperties(prefix = "mycustom")
public class MyCustomProperties {

    private String apiKey;
    private String endpoint = "https://api.example.com";
    private int timeout = 5000;
    private boolean enabled = true;

    // Getters and setters
    public String getApiKey() { return apiKey; }
    public void setApiKey(String apiKey) { this.apiKey = apiKey; }

    public String getEndpoint() { return endpoint; }
    public void setEndpoint(String endpoint) { this.endpoint = endpoint; }

    public int getTimeout() { return timeout; }
    public void setTimeout(int timeout) { this.timeout = timeout; }

    public boolean isEnabled() { return enabled; }
    public void setEnabled(boolean enabled) { this.enabled = enabled; }
}
```

### Step 4: Create Service Class

```java
package com.example.autoconfigure;

public class MyCustomService {

    private final MyCustomProperties properties;

    public MyCustomService(MyCustomProperties properties) {
        this.properties = properties;
    }

    public String callApi(String endpoint) {
        if (!properties.isEnabled()) {
            throw new IllegalStateException("Service is disabled");
        }

        String fullUrl = properties.getEndpoint() + endpoint;
        // Make API call using properties.getApiKey() and properties.getTimeout()
        return "Response from: " + fullUrl;
    }
}
```

### Step 5: Register Auto-Configuration

```properties
# src/main/resources/META-INF/spring.factories
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.example.autoconfigure.MyCustomAutoConfiguration
```

### Step 6: Create Starter Module

```xml
<!-- my-custom-spring-boot-starter/pom.xml -->
<project>
    <groupId>com.example</groupId>
    <artifactId>my-custom-spring-boot-starter</artifactId>
    <version>1.0.0</version>

    <dependencies>
        <!-- Include auto-configure module -->
        <dependency>
            <groupId>com.example</groupId>
            <artifactId>my-custom-spring-boot-autoconfigure</artifactId>
            <version>1.0.0</version>
        </dependency>

        <!-- Include any other required dependencies -->
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpclient</artifactId>
        </dependency>
    </dependencies>
</project>
```

### Step 7: Use Custom Starter

```xml
<!-- User's project pom.xml -->
<dependencies>
    <dependency>
        <groupId>com.example</groupId>
        <artifactId>my-custom-spring-boot-starter</artifactId>
        <version>1.0.0</version>
    </dependency>
</dependencies>
```

```properties
# application.properties
mycustom.api-key=secret-key-123
mycustom.endpoint=https://custom.api.com
mycustom.timeout=10000
mycustom.enabled=true
```

```java
// Use the custom starter
@RestController
public class MyController {

    @Autowired
    private MyCustomService customService;

    @GetMapping("/test")
    public String test() {
        return customService.callApi("/users");
    }
}
```

---

## Dependency Management Best Practices

### 1. Use BOM for Version Management

```xml
<!-- Import BOM (Bill of Materials) -->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>3.2.0</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

### 2. Override Dependency Versions When Needed

```xml
<properties>
    <!-- Override managed version -->
    <jackson.version>2.15.0</jackson.version>
</properties>
```

### 3. Exclude Unwanted Transitive Dependencies

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<!-- Use Jetty instead -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jetty</artifactId>
</dependency>
```

### 4. View Dependency Tree

```bash
# Maven
mvn dependency:tree

# Gradle
gradle dependencies
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| What are Starters | Pre-configured dependency descriptors for specific functionality |
| Benefits | Simplified dependency management, version compatibility, quick setup |
| Common Starters | Web, Data, Security, Messaging, Testing, Actuator |
| Parent POM | Provides dependency and plugin management |
| Custom Starters | Create reusable auto-configuration modules |
| Best Practices | Use BOM, override versions carefully, exclude when needed |

## Next Topic

Continue to [Spring Boot CLI](./05-spring-boot-cli.md) to learn about the command-line interface for Spring Boot.
