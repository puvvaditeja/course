# Bean Configuration

## What is a Spring Bean?

A Spring bean is an object that is instantiated, assembled, and managed by the Spring IoC container. Beans are the backbone of a Spring application and represent the components that make up your application.

### Bean Characteristics

- **Container-managed**: Created and managed by Spring IoC container
- **Configurable**: Can be configured in multiple ways (XML, annotations, Java)
- **Named**: Each bean has a unique identifier
- **Scoped**: Defines lifecycle and visibility
- **Injectable**: Can be injected into other beans

### Bean Definition

A bean definition contains metadata that tells the container:
- How to create the bean
- Bean's lifecycle details
- Bean's dependencies
- Configuration settings

---

## Bean Configuration Methods

Spring supports three primary ways to configure beans:

```
┌──────────────────────────────────────────┐
│      Bean Configuration Methods          │
├──────────────────────────────────────────┤
│                                          │
│  1. XML-based Configuration              │
│     └─ beans.xml                         │
│                                          │
│  2. Annotation-based Configuration       │
│     └─ @Component, @Service, etc.        │
│                                          │
│  3. Java-based Configuration             │
│     └─ @Configuration + @Bean            │
│                                          │
└──────────────────────────────────────────┘
```

---

## 1. XML Configuration

The traditional way to configure Spring beans using XML files.

### Basic XML Configuration

**applicationContext.xml:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
           http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- Simple bean definition -->
    <bean id="userRepository" class="com.example.repository.UserRepositoryImpl"/>

    <!-- Bean with constructor injection -->
    <bean id="userService" class="com.example.service.UserService">
        <constructor-arg ref="userRepository"/>
    </bean>

    <!-- Bean with property injection -->
    <bean id="emailService" class="com.example.service.EmailService">
        <property name="host" value="smtp.gmail.com"/>
        <property name="port" value="587"/>
    </bean>

</beans>
```

### Loading XML Configuration

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class XmlConfigApp {
    public static void main(String[] args) {
        ApplicationContext context =
            new ClassPathXmlApplicationContext("applicationContext.xml");

        UserService userService = context.getBean("userService", UserService.class);
        userService.doSomething();
    }
}
```

### XML Configuration Examples

#### Constructor Injection

```xml
<!-- Single constructor argument -->
<bean id="userService" class="com.example.UserService">
    <constructor-arg ref="userRepository"/>
</bean>

<!-- Multiple constructor arguments -->
<bean id="orderService" class="com.example.OrderService">
    <constructor-arg ref="orderRepository"/>
    <constructor-arg ref="emailService"/>
    <constructor-arg value="Premium"/>
</bean>

<!-- Constructor argument with index -->
<bean id="productService" class="com.example.ProductService">
    <constructor-arg index="0" ref="productRepository"/>
    <constructor-arg index="1" value="100"/>
</bean>

<!-- Constructor argument with type -->
<bean id="paymentService" class="com.example.PaymentService">
    <constructor-arg type="java.lang.String" value="PayPal"/>
    <constructor-arg type="double" value="2.5"/>
</bean>
```

#### Property Injection

```xml
<!-- Simple property -->
<bean id="dataSource" class="com.example.config.DataSource">
    <property name="url" value="jdbc:mysql://localhost:3306/mydb"/>
    <property name="username" value="root"/>
    <property name="password" value="secret"/>
</bean>

<!-- Reference to another bean -->
<bean id="userService" class="com.example.UserService">
    <property name="userRepository" ref="userRepository"/>
</bean>

<!-- Collection properties -->
<bean id="emailService" class="com.example.EmailService">
    <property name="recipients">
        <list>
            <value>admin@example.com</value>
            <value>support@example.com</value>
        </list>
    </property>

    <property name="configuration">
        <map>
            <entry key="host" value="smtp.gmail.com"/>
            <entry key="port" value="587"/>
        </map>
    </property>
</bean>
```

#### Namespace Configuration

```xml
<!-- Using p-namespace for properties -->
<beans xmlns:p="http://www.springframework.org/schema/p">

    <bean id="dataSource" class="com.example.DataSource"
          p:url="jdbc:mysql://localhost:3306/mydb"
          p:username="root"
          p:password="secret"/>

</beans>

<!-- Using c-namespace for constructor -->
<beans xmlns:c="http://www.springframework.org/schema/c">

    <bean id="userService" class="com.example.UserService"
          c:userRepository-ref="userRepository"/>

</beans>
```

---

## 2. Annotation-based Configuration

Modern approach using annotations to define beans and their dependencies.

### Enabling Annotation Configuration

**In XML:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
           http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- Enable annotation-based configuration -->
    <context:annotation-config/>

    <!-- Enable component scanning -->
    <context:component-scan base-package="com.example"/>

</beans>
```

**In Java:**
```java
@Configuration
@ComponentScan(basePackages = "com.example")
public class AppConfig {
    // Configuration
}
```

### Stereotype Annotations

Spring provides several stereotype annotations for marking classes as beans:

```java
// Generic component
@Component
public class MyComponent {
}

// Service layer
@Service
public class UserService {
}

// Data access layer
@Repository
public class UserRepository {
}

// Web layer (Spring MVC)
@Controller
public class UserController {
}

// RESTful web services
@RestController
public class UserRestController {
}
```

### Component Scanning Example

**Project Structure:**
```
com.example
├── Application.java
├── config
│   └── AppConfig.java
├── repository
│   ├── UserRepository.java
│   └── UserRepositoryImpl.java
├── service
│   └── UserService.java
└── model
    └── User.java
```

**Configuration:**
```java
@Configuration
@ComponentScan(basePackages = "com.example")
public class AppConfig {
    // Spring will scan com.example and sub-packages
}
```

**Repository:**
```java
package com.example.repository;

@Repository
public class UserRepositoryImpl implements UserRepository {

    @Override
    public User findById(Long id) {
        // Implementation
        return new User(id, "John Doe", "john@example.com");
    }
}
```

**Service:**
```java
package com.example.service;

@Service
public class UserService {
    private final UserRepository userRepository;

    @Autowired  // Constructor injection
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public User getUser(Long id) {
        return userRepository.findById(id);
    }
}
```

### Custom Component Names

```java
// Default name: userService (camelCase of class name)
@Service
public class UserService {
}

// Custom name
@Service("customUserService")
public class UserService {
}

// Using value attribute
@Service(value = "myUserService")
public class UserService {
}
```

### Filtering Components

```java
@Configuration
@ComponentScan(
    basePackages = "com.example",
    includeFilters = @ComponentScan.Filter(
        type = FilterType.ANNOTATION,
        classes = Service.class
    ),
    excludeFilters = @ComponentScan.Filter(
        type = FilterType.REGEX,
        pattern = "com.example.exclude.*"
    )
)
public class AppConfig {
}
```

---

## 3. Java-based Configuration

Type-safe configuration using Java classes.

### Configuration Class

```java
@Configuration
public class AppConfig {

    @Bean
    public UserRepository userRepository() {
        return new UserRepositoryImpl();
    }

    @Bean
    public UserService userService() {
        return new UserService(userRepository());
    }

    @Bean
    public EmailService emailService() {
        EmailService service = new EmailService();
        service.setHost("smtp.gmail.com");
        service.setPort(587);
        return service;
    }
}
```

### Bean Definition Methods

```java
@Configuration
public class DatabaseConfig {

    // Simple bean
    @Bean
    public DataSource dataSource() {
        HikariDataSource dataSource = new HikariDataSource();
        dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/mydb");
        dataSource.setUsername("root");
        dataSource.setPassword("secret");
        return dataSource;
    }

    // Bean with custom name
    @Bean(name = "mainDataSource")
    public DataSource primaryDataSource() {
        return new HikariDataSource();
    }

    // Bean with multiple names (aliases)
    @Bean(name = {"ds", "dataSource", "primaryDS"})
    public DataSource dataSource2() {
        return new HikariDataSource();
    }

    // Bean with initialization and destruction methods
    @Bean(initMethod = "init", destroyMethod = "cleanup")
    public CacheManager cacheManager() {
        return new CacheManager();
    }

    // Bean depending on other beans
    @Bean
    public UserRepository userRepository(DataSource dataSource) {
        return new UserRepositoryImpl(dataSource);
    }
}
```

### Importing Configurations

```java
// Configuration 1
@Configuration
public class DatabaseConfig {
    @Bean
    public DataSource dataSource() {
        return new HikariDataSource();
    }
}

// Configuration 2
@Configuration
public class ServiceConfig {
    @Bean
    public UserService userService(UserRepository repository) {
        return new UserService(repository);
    }
}

// Main configuration
@Configuration
@Import({DatabaseConfig.class, ServiceConfig.class})
public class AppConfig {
    // Combines multiple configurations
}
```

### Using @Value for Properties

```java
@Configuration
@PropertySource("classpath:application.properties")
public class AppConfig {

    @Value("${db.url}")
    private String dbUrl;

    @Value("${db.username}")
    private String dbUsername;

    @Value("${db.password}")
    private String dbPassword;

    @Bean
    public DataSource dataSource() {
        HikariDataSource dataSource = new HikariDataSource();
        dataSource.setJdbcUrl(dbUrl);
        dataSource.setUsername(dbUsername);
        dataSource.setPassword(dbPassword);
        return dataSource;
    }
}
```

**application.properties:**
```properties
db.url=jdbc:mysql://localhost:3306/mydb
db.username=root
db.password=secret
```

---

## Bean Scopes

Bean scope defines the lifecycle and visibility of a bean within the container.

### Available Scopes

| Scope | Description | Use Case |
|-------|-------------|----------|
| **singleton** | One instance per Spring container (default) | Stateless services, repositories |
| **prototype** | New instance every time bean is requested | Stateful objects, non-shared instances |
| **request** | One instance per HTTP request (web only) | Request-specific data |
| **session** | One instance per HTTP session (web only) | User session data |
| **application** | One instance per ServletContext (web only) | Application-wide shared data |
| **websocket** | One instance per WebSocket session | WebSocket handlers |

### Singleton Scope (Default)

```java
@Component
@Scope("singleton")  // Can be omitted as it's default
public class UserService {
    // One instance for entire application
}

// Java config
@Configuration
public class AppConfig {

    @Bean
    @Scope("singleton")
    public UserService userService() {
        return new UserService();
    }
}

// XML config
<bean id="userService" class="com.example.UserService" scope="singleton"/>
```

**Singleton Behavior:**
```java
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

UserService service1 = context.getBean(UserService.class);
UserService service2 = context.getBean(UserService.class);

System.out.println(service1 == service2);  // true - same instance
```

### Prototype Scope

```java
@Component
@Scope("prototype")
public class ShoppingCart {
    // New instance every time
}

// Java config
@Configuration
public class AppConfig {

    @Bean
    @Scope("prototype")
    public ShoppingCart shoppingCart() {
        return new ShoppingCart();
    }
}

// XML config
<bean id="shoppingCart" class="com.example.ShoppingCart" scope="prototype"/>
```

**Prototype Behavior:**
```java
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

ShoppingCart cart1 = context.getBean(ShoppingCart.class);
ShoppingCart cart2 = context.getBean(ShoppingCart.class);

System.out.println(cart1 == cart2);  // false - different instances
```

### Web Scopes

```java
// Request scope
@Component
@Scope(value = WebApplicationContext.SCOPE_REQUEST, proxyMode = ScopedProxyMode.TARGET_CLASS)
public class RequestScopedBean {
    // New instance per HTTP request
}

// Session scope
@Component
@Scope(value = WebApplicationContext.SCOPE_SESSION, proxyMode = ScopedProxyMode.TARGET_CLASS)
public class SessionScopedBean {
    // New instance per HTTP session
}

// Application scope
@Component
@Scope(value = WebApplicationContext.SCOPE_APPLICATION, proxyMode = ScopedProxyMode.TARGET_CLASS)
public class ApplicationScopedBean {
    // One instance per ServletContext
}
```

### Scoped Proxy

When injecting a shorter-lived bean into a longer-lived bean, use scoped proxy:

```java
@Component
@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class RequestBean {
}

@Component
@Scope("singleton")
public class SingletonBean {

    @Autowired
    private RequestBean requestBean;  // Proxy injected

    public void doSomething() {
        // Proxy delegates to actual request-scoped instance
        requestBean.process();
    }
}
```

---

## Bean Lifecycle

### Lifecycle Phases

```
1. Instantiation
        ↓
2. Populate Properties
        ↓
3. BeanNameAware.setBeanName()
        ↓
4. BeanFactoryAware.setBeanFactory()
        ↓
5. ApplicationContextAware.setApplicationContext()
        ↓
6. BeanPostProcessor.postProcessBeforeInitialization()
        ↓
7. @PostConstruct
        ↓
8. InitializingBean.afterPropertiesSet()
        ↓
9. Custom init-method
        ↓
10. BeanPostProcessor.postProcessAfterInitialization()
        ↓
11. Bean Ready to Use
        ↓
12. Container Shutdown
        ↓
13. @PreDestroy
        ↓
14. DisposableBean.destroy()
        ↓
15. Custom destroy-method
```

### Initialization Callbacks

#### 1. @PostConstruct Annotation

```java
@Component
public class UserService {

    @PostConstruct
    public void init() {
        System.out.println("UserService initialized with @PostConstruct");
        // Initialization logic
    }
}
```

#### 2. InitializingBean Interface

```java
@Component
public class UserService implements InitializingBean {

    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("UserService initialized via InitializingBean");
        // Initialization logic
    }
}
```

#### 3. Custom Init Method

**Annotation:**
```java
@Configuration
public class AppConfig {

    @Bean(initMethod = "customInit")
    public UserService userService() {
        return new UserService();
    }
}

public class UserService {
    public void customInit() {
        System.out.println("UserService initialized via custom init method");
    }
}
```

**XML:**
```xml
<bean id="userService" class="com.example.UserService" init-method="customInit"/>
```

### Destruction Callbacks

#### 1. @PreDestroy Annotation

```java
@Component
public class UserService {

    @PreDestroy
    public void cleanup() {
        System.out.println("UserService cleanup with @PreDestroy");
        // Cleanup logic
    }
}
```

#### 2. DisposableBean Interface

```java
@Component
public class UserService implements DisposableBean {

    @Override
    public void destroy() throws Exception {
        System.out.println("UserService cleanup via DisposableBean");
        // Cleanup logic
    }
}
```

#### 3. Custom Destroy Method

**Annotation:**
```java
@Configuration
public class AppConfig {

    @Bean(destroyMethod = "customCleanup")
    public UserService userService() {
        return new UserService();
    }
}

public class UserService {
    public void customCleanup() {
        System.out.println("UserService cleanup via custom destroy method");
    }
}
```

**XML:**
```xml
<bean id="userService" class="com.example.UserService" destroy-method="customCleanup"/>
```

### Complete Lifecycle Example

```java
@Component
public class LifecycleDemoBean implements InitializingBean, DisposableBean {

    private String name;

    public LifecycleDemoBean() {
        System.out.println("1. Constructor called");
    }

    public void setName(String name) {
        this.name = name;
        System.out.println("2. Property 'name' set to: " + name);
    }

    @PostConstruct
    public void postConstruct() {
        System.out.println("3. @PostConstruct called");
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("4. afterPropertiesSet() called");
    }

    public void customInit() {
        System.out.println("5. Custom init method called");
    }

    // Bean is now ready to use

    @PreDestroy
    public void preDestroy() {
        System.out.println("6. @PreDestroy called");
    }

    @Override
    public void destroy() throws Exception {
        System.out.println("7. destroy() called");
    }

    public void customDestroy() {
        System.out.println("8. Custom destroy method called");
    }
}

@Configuration
public class AppConfig {

    @Bean(initMethod = "customInit", destroyMethod = "customDestroy")
    public LifecycleDemoBean lifecycleBean() {
        LifecycleDemoBean bean = new LifecycleDemoBean();
        bean.setName("Demo Bean");
        return bean;
    }
}
```

### Aware Interfaces

Spring provides several "Aware" interfaces to give beans access to container infrastructure:

```java
@Component
public class AwareBean implements
        BeanNameAware,
        BeanFactoryAware,
        ApplicationContextAware {

    @Override
    public void setBeanName(String name) {
        System.out.println("Bean name: " + name);
    }

    @Override
    public void setBeanFactory(BeanFactory beanFactory) {
        System.out.println("BeanFactory set");
    }

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) {
        System.out.println("ApplicationContext set");
        // Can now use ApplicationContext
    }
}
```

---

## Configuration Comparison

### XML vs Annotations vs Java Config

| Aspect | XML | Annotations | Java Config |
|--------|-----|-------------|-------------|
| **Type Safety** | No | Limited | Yes |
| **Refactoring** | Manual | Automatic | Automatic |
| **Centralized** | Yes | No | Yes |
| **Verbosity** | High | Low | Medium |
| **External Config** | Easy | Hard | Medium |
| **Learning Curve** | Steep | Gentle | Moderate |
| **Modern Apps** | Rare | Common | Preferred |

### When to Use What?

**Use XML when:**
- Working with legacy applications
- Need external configuration without recompilation
- Third-party libraries without annotations

**Use Annotations when:**
- Rapid development
- Components you own
- Simple applications

**Use Java Config when:**
- Type safety is important
- Complex bean construction
- Conditional bean creation
- Modern applications

---

## Best Practices

### 1. Prefer Constructor Injection

```java
// Good - immutable, testable
@Service
public class UserService {
    private final UserRepository repository;

    @Autowired  // Optional in Spring 4.3+
    public UserService(UserRepository repository) {
        this.repository = repository;
    }
}
```

### 2. Use Appropriate Scopes

```java
// Singleton for stateless services
@Service
@Scope("singleton")
public class UserService {
}

// Prototype for stateful objects
@Component
@Scope("prototype")
public class ShoppingCart {
}
```

### 3. Organize Configuration

```java
@Configuration
@Import({
    DatabaseConfig.class,
    SecurityConfig.class,
    WebConfig.class
})
public class AppConfig {
}
```

### 4. Use Meaningful Bean Names

```java
@Service("userManagementService")
public class UserService {
}
```

### 5. Prefer Java Config for Complex Beans

```java
@Configuration
public class ComplexConfig {

    @Bean
    public DataSource dataSource() {
        HikariDataSource ds = new HikariDataSource();
        // Complex configuration
        ds.setMaximumPoolSize(10);
        ds.setMinimumIdle(5);
        ds.setConnectionTimeout(30000);
        return ds;
    }
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Bean | Container-managed object with lifecycle |
| XML Config | Traditional, external, verbose |
| Annotation Config | Modern, in-code, concise |
| Java Config | Type-safe, flexible, preferred |
| Scopes | singleton, prototype, request, session, application |
| Lifecycle | init → use → destroy with multiple callback options |

## Next Topic

Continue to [Dependency Injection](./05-dependency-injection.md) to learn how to inject dependencies into beans.
