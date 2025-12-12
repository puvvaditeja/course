# Spring Boot vs Spring Framework

## Introduction

Understanding the difference between Spring Boot and Spring Framework is crucial for making informed decisions about which to use for your projects. While Spring Boot is built on top of the Spring Framework, it provides significant enhancements that change how we develop applications.

---

## Spring Framework Overview

### What is Spring Framework?

The Spring Framework is a comprehensive framework for enterprise Java development, created in 2003 by Rod Johnson. It provides:

- **Dependency Injection (DI)**: Inversion of Control container
- **Aspect-Oriented Programming (AOP)**: Cross-cutting concerns
- **Data Access**: JDBC, ORM, transactions
- **Web Framework**: Spring MVC for web applications
- **Security**: Authentication and authorization
- **Testing**: Comprehensive testing support

### Traditional Spring Application Example

```java
// applicationContext.xml - Traditional Spring Configuration
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- Enable component scanning -->
    <context:component-scan base-package="com.example"/>

    <!-- DataSource configuration -->
    <bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/mydb"/>
        <property name="username" value="root"/>
        <property name="password" value="password"/>
    </bean>

    <!-- EntityManagerFactory -->
    <bean id="entityManagerFactory"
          class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <property name="packagesToScan" value="com.example.model"/>
        <property name="jpaVendorAdapter">
            <bean class="org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter"/>
        </property>
    </bean>

    <!-- Transaction Manager -->
    <bean id="transactionManager"
          class="org.springframework.orm.jpa.JpaTransactionManager">
        <property name="entityManagerFactory" ref="entityManagerFactory"/>
    </bean>

    <!-- Enable transaction annotations -->
    <tx:annotation-driven transaction-manager="transactionManager"/>
</beans>
```

```java
// web.xml - Web application configuration
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
         http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">

    <!-- Spring MVC Dispatcher Servlet -->
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/spring/dispatcher-config.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!-- Spring Root Application Context -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/spring/applicationContext.xml</param-value>
    </context-param>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
</web-app>
```

---

## Spring Boot Overview

### What is Spring Boot?

Spring Boot is an extension of the Spring Framework that eliminates boilerplate configuration. Released in 2014, it provides:

- **Auto-Configuration**: Automatic bean configuration based on classpath
- **Starter Dependencies**: Pre-configured dependency descriptors
- **Embedded Servers**: No external server deployment needed
- **Production Features**: Metrics, health checks, externalized configuration
- **Opinionated Defaults**: Sensible defaults that can be overridden

### Spring Boot Application Example

```java
// application.properties - Simple configuration
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=password
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

```java
// Application.java - Main class
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

```xml
<!-- pom.xml - Minimal dependencies -->
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
</dependencies>
```

---

## Key Differences

### 1. Configuration

| Spring Framework | Spring Boot |
|-----------------|-------------|
| XML or Java-based configuration | Auto-configuration with properties |
| Manual bean definitions | Convention over configuration |
| Verbose setup | Minimal configuration |
| Multiple configuration files | Single application.properties/yml |

**Example Comparison:**

```java
// Spring Framework - Manual Configuration
@Configuration
@EnableTransactionManagement
@EnableJpaRepositories(basePackages = "com.example.repository")
public class DatabaseConfig {

    @Bean
    public DataSource dataSource() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/mydb");
        dataSource.setUsername("root");
        dataSource.setPassword("password");
        return dataSource;
    }

    @Bean
    public LocalContainerEntityManagerFactoryBean entityManagerFactory() {
        LocalContainerEntityManagerFactoryBean em = new LocalContainerEntityManagerFactoryBean();
        em.setDataSource(dataSource());
        em.setPackagesToScan("com.example.model");
        HibernateJpaVendorAdapter vendorAdapter = new HibernateJpaVendorAdapter();
        em.setJpaVendorAdapter(vendorAdapter);
        return em;
    }

    @Bean
    public PlatformTransactionManager transactionManager() {
        JpaTransactionManager transactionManager = new JpaTransactionManager();
        transactionManager.setEntityManagerFactory(entityManagerFactory().getObject());
        return transactionManager;
    }
}
```

```java
// Spring Boot - Auto-Configuration
// Just add properties - Spring Boot handles the rest!
// application.properties:
// spring.datasource.url=jdbc:mysql://localhost:3306/mydb
// spring.datasource.username=root
// spring.datasource.password=password
// spring.jpa.hibernate.ddl-auto=update

// No additional configuration needed!
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
}
```

### 2. Dependency Management

**Spring Framework:**
```xml
<!-- Manual version management -->
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>5.3.20</version>
    </dependency>
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
    <!-- Many more dependencies... -->
</dependencies>
```

**Spring Boot:**
```xml
<!-- Single starter dependency -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.2.0</version>
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <!-- No version needed - inherited from parent -->
    </dependency>
</dependencies>
```

### 3. Application Deployment

**Spring Framework:**
```
Development Process:
1. Develop application
2. Package as WAR file
3. Install application server (Tomcat, JBoss, etc.)
4. Configure server
5. Deploy WAR to server
6. Start server
7. Access application

Deployment Structure:
myapp.war → Tomcat/webapps/ → Tomcat startup
```

**Spring Boot:**
```
Development Process:
1. Develop application
2. Package as JAR file
3. Run: java -jar myapp.jar
4. Access application

Deployment Structure:
myapp.jar (includes embedded Tomcat) → Direct execution
```

### 4. Server Configuration

**Spring Framework:**
```xml
<!-- Separate server installation required -->
<!-- Configure server.xml for Tomcat -->
<Server port="8005" shutdown="SHUTDOWN">
    <Service name="Catalina">
        <Connector port="8080" protocol="HTTP/1.1"
                   connectionTimeout="20000"
                   redirectPort="8443" />
        <Engine name="Catalina" defaultHost="localhost">
            <Host name="localhost" appBase="webapps" />
        </Engine>
    </Service>
</Server>
```

**Spring Boot:**
```properties
# application.properties - Simple server configuration
server.port=8080
server.servlet.context-path=/api
server.compression.enabled=true
server.tomcat.max-threads=200
```

### 5. Testing Support

**Spring Framework:**
```java
// Complex test configuration
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {
    "classpath:applicationContext.xml",
    "classpath:test-context.xml"
})
@WebAppConfiguration
public class UserServiceTest {

    @Autowired
    private WebApplicationContext context;

    private MockMvc mockMvc;

    @Before
    public void setup() {
        mockMvc = MockMvcBuilders
            .webAppContextSetup(context)
            .build();
    }

    @Test
    public void testGetUser() throws Exception {
        // Test code
    }
}
```

**Spring Boot:**
```java
// Simplified test configuration
@SpringBootTest
@AutoConfigureMockMvc
public class UserServiceTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    public void testGetUser() throws Exception {
        mockMvc.perform(get("/users/1"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.name").value("John"));
    }
}
```

---

## Detailed Comparison Table

| Feature | Spring Framework | Spring Boot |
|---------|-----------------|-------------|
| **Configuration** | XML or Java-based, manual | Auto-configuration, properties-based |
| **Dependency Management** | Manual version management | Starter dependencies with versions |
| **Embedded Server** | No (external server required) | Yes (Tomcat, Jetty, Undertow) |
| **Deployment** | WAR file to server | Executable JAR |
| **Setup Time** | Hours to days | Minutes |
| **Production Features** | Manual implementation | Built-in Actuator |
| **Development Speed** | Slower due to configuration | Rapid development |
| **Learning Curve** | Steeper | Easier for beginners |
| **Flexibility** | Highly flexible | Opinionated (still flexible) |
| **XML Configuration** | Common | Optional (rarely needed) |
| **Default Configuration** | Must define everything | Sensible defaults provided |
| **Microservices** | Requires setup | Built for microservices |
| **Cloud Native** | Requires configuration | Cloud-ready out of the box |

---

## When to Use Spring Framework

### Suitable Scenarios

1. **Legacy Applications**: Maintaining existing Spring applications
2. **Full Control Required**: Need complete control over configuration
3. **Specific Architecture**: Custom architecture that doesn't fit Boot's conventions
4. **Gradual Migration**: Migrating from older frameworks

### Example Use Case

```java
// Complex, customized configuration needed
@Configuration
public class CustomSecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // Highly customized security configuration
        // that doesn't fit standard patterns
    }

    @Bean
    public AuthenticationManager customAuthenticationManager() {
        // Custom authentication logic
    }

    // Multiple custom beans with specific configurations
}
```

---

## When to Use Spring Boot

### Suitable Scenarios

1. **New Applications**: Starting fresh projects
2. **Microservices**: Building microservice architecture
3. **REST APIs**: Developing RESTful web services
4. **Rapid Prototyping**: Quick proof-of-concept development
5. **Cloud Deployment**: Applications for cloud platforms
6. **Standard Use Cases**: Common application patterns

### Example Use Cases

```java
// 1. Simple REST API
@RestController
@RequestMapping("/api/products")
public class ProductController {

    @Autowired
    private ProductService productService;

    @GetMapping
    public List<Product> getAllProducts() {
        return productService.findAll();
    }

    @PostMapping
    public Product createProduct(@RequestBody Product product) {
        return productService.save(product);
    }
}

// 2. Microservice with Service Discovery
@SpringBootApplication
@EnableEurekaClient
public class InventoryService {
    public static void main(String[] args) {
        SpringApplication.run(InventoryService.class, args);
    }
}

// 3. Scheduled Background Tasks
@Component
public class DataSyncScheduler {

    @Scheduled(cron = "0 0 2 * * ?") // Run at 2 AM daily
    public void syncData() {
        // Synchronization logic
    }
}
```

---

## Migration Path: Spring to Spring Boot

### Step-by-Step Migration

```java
// 1. Update pom.xml
// Before:
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>5.3.20</version>
    </dependency>
    <!-- Many manual dependencies -->
</dependencies>

// After:
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.2.0</version>
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>

// 2. Create Main Application Class
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}

// 3. Move XML configuration to application.properties
// From applicationContext.xml:
// <property name="url" value="jdbc:mysql://localhost:3306/mydb"/>

// To application.properties:
// spring.datasource.url=jdbc:mysql://localhost:3306/mydb

// 4. Remove web.xml - No longer needed!

// 5. Update Java configurations
// Before:
@Configuration
@ComponentScan("com.example")
@EnableWebMvc
public class WebConfig extends WebMvcConfigurerAdapter { }

// After:
@Configuration
public class WebConfig implements WebMvcConfigurer {
    // Only keep custom configurations
    // Standard configurations handled by Spring Boot
}
```

---

## Code Examples: Same Feature, Different Approaches

### Example 1: Database Configuration

**Spring Framework:**
```java
@Configuration
@EnableTransactionManagement
@PropertySource("classpath:database.properties")
public class DatabaseConfig {

    @Autowired
    private Environment env;

    @Bean
    public DataSource dataSource() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName(env.getProperty("db.driver"));
        dataSource.setUrl(env.getProperty("db.url"));
        dataSource.setUsername(env.getProperty("db.username"));
        dataSource.setPassword(env.getProperty("db.password"));
        return dataSource;
    }

    @Bean
    public JdbcTemplate jdbcTemplate() {
        return new JdbcTemplate(dataSource());
    }
}
```

**Spring Boot:**
```properties
# application.properties
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=password
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```

```java
// No configuration class needed - just use JdbcTemplate
@Repository
public class UserRepository {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    public List<User> findAll() {
        return jdbcTemplate.query(
            "SELECT * FROM users",
            new BeanPropertyRowMapper<>(User.class)
        );
    }
}
```

### Example 2: REST Controller

**Spring Framework:**
```java
@Controller
@RequestMapping("/api/users")
public class UserController {

    @Autowired
    private UserService userService;

    @RequestMapping(method = RequestMethod.GET)
    @ResponseBody
    public ResponseEntity<List<User>> getUsers() {
        List<User> users = userService.findAll();
        return new ResponseEntity<>(users, HttpStatus.OK);
    }

    @RequestMapping(value = "/{id}", method = RequestMethod.GET)
    @ResponseBody
    public ResponseEntity<User> getUser(@PathVariable Long id) {
        User user = userService.findById(id);
        return new ResponseEntity<>(user, HttpStatus.OK);
    }
}
```

**Spring Boot:**
```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping
    public List<User> getUsers() {
        return userService.findAll();
    }

    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id);
    }
}
```

---

## Performance Comparison

| Aspect | Spring Framework | Spring Boot |
|--------|-----------------|-------------|
| Startup Time | Depends on configuration | Slightly slower (auto-config) |
| Runtime Performance | Same | Same (same underlying framework) |
| Memory Footprint | Lower (minimal dependencies) | Slightly higher (more dependencies) |
| Build Size | WAR (smaller) | JAR with embedded server (larger) |
| Development Time | Slower | Faster |

### Performance Optimization Tips for Spring Boot

```java
// 1. Exclude unnecessary auto-configurations
@SpringBootApplication(exclude = {
    DataSourceAutoConfiguration.class,
    HibernateJpaAutoConfiguration.class
})
public class Application { }

// 2. Use lazy initialization (Spring Boot 2.2+)
spring.main.lazy-initialization=true

// 3. Disable unused features
spring.jmx.enabled=false
spring.devtools.restart.enabled=false

// 4. Use native compilation (Spring Native)
// For even faster startup times and lower memory
```

---

## Summary

| Aspect | Spring Framework | Spring Boot |
|--------|-----------------|-------------|
| **Purpose** | Comprehensive framework | Rapid application development |
| **Best For** | Legacy apps, full control | New apps, microservices, APIs |
| **Configuration** | Manual, verbose | Auto-configuration, minimal |
| **Learning Curve** | Steeper | Gentler |
| **Development Speed** | Slower | Faster |
| **Production Ready** | Requires setup | Built-in features |
| **Deployment** | WAR to server | Executable JAR |

### Key Takeaways

1. Spring Boot builds on Spring Framework - it's not a replacement
2. Spring Boot simplifies configuration through auto-configuration
3. Spring Framework offers more control; Spring Boot offers faster development
4. Both are production-ready; Spring Boot adds convenience features
5. Choose based on project needs, team experience, and requirements

## Next Topic

Continue to [Auto-configuration](./03-auto-configuration.md) to learn how Spring Boot automatically configures your application.
