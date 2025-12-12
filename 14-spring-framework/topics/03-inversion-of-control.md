# Inversion of Control (IoC)

## What is Inversion of Control?

Inversion of Control (IoC) is a design principle in which the control flow of a program is inverted: instead of the programmer controlling the flow of a program, the framework or runtime controls it. In simpler terms, "Don't call us, we'll call you."

### Traditional Control Flow

In traditional programming, your code explicitly creates and manages its dependencies:

```java
public class UserService {
    private UserRepository repository;

    public UserService() {
        // UserService controls the creation of its dependency
        this.repository = new UserRepositoryImpl();
    }

    public User getUser(Long id) {
        return repository.findById(id);
    }
}
```

**Problems:**
- Tight coupling between UserService and UserRepositoryImpl
- Difficult to test (can't easily mock the repository)
- Hard to change implementation
- UserService has too many responsibilities

### Inverted Control Flow

With IoC, the framework provides the dependencies:

```java
public class UserService {
    private UserRepository repository;

    // Dependency is provided from outside
    public UserService(UserRepository repository) {
        this.repository = repository;
    }

    public User getUser(Long id) {
        return repository.findById(id);
    }
}
```

**Benefits:**
- Loose coupling
- Easy to test with mocks
- Flexible implementation switching
- Single Responsibility Principle

---

## IoC Principle

### Control Flow Comparison

```
Traditional Approach:
┌─────────────────────┐
│   Your Code         │
│                     │
│  ┌──────────────┐   │
│  │ new Object() │   │
│  └──────────────┘   │
│         │           │
│         ▼           │
│  ┌──────────────┐   │
│  │   Object     │   │
│  └──────────────┘   │
└─────────────────────┘

IoC Approach:
┌─────────────────────┐
│   IoC Container     │
│                     │
│  ┌──────────────┐   │
│  │Create Objects│   │
│  └──────────────┘   │
│         │           │
│         ▼           │
│  ┌──────────────┐   │
│  │ Inject Deps  │   │
│  └──────────────┘   │
└─────────┬───────────┘
          │
          ▼
    ┌──────────┐
    │Your Code │
    └──────────┘
```

### The Hollywood Principle

"Don't call us, we'll call you" - the framework calls your code, not the other way around.

**Example:**

```java
// Without IoC - You call the framework
public class Application {
    public static void main(String[] args) {
        Database db = new Database();
        db.connect();

        UserRepository repo = new UserRepositoryImpl(db);
        UserService service = new UserService(repo);

        User user = service.getUser(1L);
        System.out.println(user);
    }
}

// With IoC - Framework calls your code
@Configuration
@ComponentScan
public class AppConfig {
    // Configuration only
}

public class Application {
    public static void main(String[] args) {
        ApplicationContext context =
            new AnnotationConfigApplicationContext(AppConfig.class);

        // Framework created and wired everything
        UserService service = context.getBean(UserService.class);
        User user = service.getUser(1L);
        System.out.println(user);
    }
}
```

---

## Spring IoC Container

The Spring IoC container is responsible for instantiating, configuring, and assembling objects called beans. It manages the entire lifecycle of beans.

### Container Responsibilities

1. **Instantiate**: Create bean instances
2. **Configure**: Set properties and dependencies
3. **Assemble**: Wire beans together
4. **Manage Lifecycle**: Initialize and destroy beans
5. **Provide**: Serve beans to application code

### Container Architecture

```
┌────────────────────────────────────────────────┐
│         Spring IoC Container                   │
├────────────────────────────────────────────────┤
│                                                │
│  ┌──────────────────────────────────────┐    │
│  │     Configuration Metadata           │    │
│  │  (XML, Annotations, Java Config)     │    │
│  └──────────────┬───────────────────────┘    │
│                 │                             │
│                 ▼                             │
│  ┌──────────────────────────────────────┐    │
│  │      Bean Definition Registry        │    │
│  └──────────────┬───────────────────────┘    │
│                 │                             │
│                 ▼                             │
│  ┌──────────────────────────────────────┐    │
│  │      Bean Factory / Container        │    │
│  │    - Instantiation                   │    │
│  │    - Dependency Injection            │    │
│  │    - Lifecycle Management            │    │
│  └──────────────┬───────────────────────┘    │
│                 │                             │
└─────────────────┼─────────────────────────────┘
                  │
                  ▼
            ┌──────────┐
            │  Beans   │
            └──────────┘
```

---

## IoC Container Types

Spring provides two types of IoC containers, both implementing different interfaces.

### 1. BeanFactory

The most basic IoC container providing basic functionality for managing beans.

**Characteristics:**
- Lightweight container
- Lazy initialization (beans created when requested)
- Basic dependency injection
- Minimal resource consumption
- Rarely used directly in modern applications

**Example:**

```java
import org.springframework.beans.factory.BeanFactory;
import org.springframework.beans.factory.xml.XmlBeanFactory;
import org.springframework.core.io.ClassPathResource;

public class BeanFactoryExample {
    public static void main(String[] args) {
        // Create BeanFactory from XML configuration
        BeanFactory factory = new XmlBeanFactory(
            new ClassPathResource("beans.xml")
        );

        // Get bean (created on demand)
        UserService service = factory.getBean("userService", UserService.class);

        User user = service.getUser(1L);
        System.out.println(user);
    }
}
```

### 2. ApplicationContext

Advanced container that extends BeanFactory with enterprise-specific functionality.

**Characteristics:**
- Eager initialization (beans created at startup)
- Automatic BeanPostProcessor registration
- Automatic BeanFactoryPostProcessor registration
- Internationalization (i18n) support
- Event publication mechanism
- Resource loading capabilities
- Preferred for most applications

**Common Implementations:**

1. **AnnotationConfigApplicationContext** - Java-based configuration
2. **ClassPathXmlApplicationContext** - XML configuration from classpath
3. **FileSystemXmlApplicationContext** - XML configuration from file system
4. **WebApplicationContext** - For web applications

---

## ApplicationContext in Detail

### Creating ApplicationContext

#### 1. XML-based Configuration

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class XmlConfigExample {
    public static void main(String[] args) {
        ApplicationContext context =
            new ClassPathXmlApplicationContext("applicationContext.xml");

        UserService service = context.getBean(UserService.class);
        // Use service
    }
}
```

**applicationContext.xml:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="userRepository" class="com.example.UserRepositoryImpl"/>

    <bean id="userService" class="com.example.UserService">
        <constructor-arg ref="userRepository"/>
    </bean>
</beans>
```

#### 2. Annotation-based Configuration

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class AnnotationConfigExample {
    public static void main(String[] args) {
        ApplicationContext context =
            new AnnotationConfigApplicationContext(AppConfig.class);

        UserService service = context.getBean(UserService.class);
        // Use service
    }
}
```

**AppConfig.java:**
```java
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackages = "com.example")
public class AppConfig {
    // Configuration class
}
```

#### 3. Java-based Configuration

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
}

public class JavaConfigExample {
    public static void main(String[] args) {
        ApplicationContext context =
            new AnnotationConfigApplicationContext(AppConfig.class);

        UserService service = context.getBean(UserService.class);
    }
}
```

### ApplicationContext Features

#### 1. Bean Retrieval

```java
// By type
UserService service = context.getBean(UserService.class);

// By name
UserService service = (UserService) context.getBean("userService");

// By name and type
UserService service = context.getBean("userService", UserService.class);

// Check if bean exists
boolean exists = context.containsBean("userService");

// Get all beans of a type
Map<String, UserService> beans =
    context.getBeansOfType(UserService.class);
```

#### 2. Event Publishing

```java
// Define custom event
public class UserRegisteredEvent extends ApplicationEvent {
    private User user;

    public UserRegisteredEvent(Object source, User user) {
        super(source);
        this.user = user;
    }

    public User getUser() {
        return user;
    }
}

// Event listener
@Component
public class UserEventListener implements ApplicationListener<UserRegisteredEvent> {

    @Override
    public void onApplicationEvent(UserRegisteredEvent event) {
        System.out.println("User registered: " + event.getUser().getName());
    }
}

// Publish event
@Service
public class UserService {

    @Autowired
    private ApplicationEventPublisher publisher;

    public void registerUser(User user) {
        // Register user logic
        publisher.publishEvent(new UserRegisteredEvent(this, user));
    }
}
```

#### 3. Resource Loading

```java
@Service
public class ConfigService {

    @Autowired
    private ApplicationContext context;

    public void loadConfig() throws IOException {
        Resource resource = context.getResource("classpath:config.properties");
        InputStream is = resource.getInputStream();
        // Read properties
    }
}
```

#### 4. Internationalization (i18n)

```java
@Service
public class MessageService {

    @Autowired
    private ApplicationContext context;

    public String getMessage(String code, Locale locale) {
        return context.getMessage(code, null, locale);
    }
}
```

---

## BeanFactory vs ApplicationContext

### Detailed Comparison

| Feature | BeanFactory | ApplicationContext |
|---------|-------------|-------------------|
| **Initialization** | Lazy (on-demand) | Eager (at startup) |
| **Memory** | Lightweight | More features, more memory |
| **Configuration** | Programmatic | Multiple options (XML, annotations, Java) |
| **BeanPostProcessor** | Manual registration | Automatic registration |
| **Event Publication** | Not supported | Built-in support |
| **i18n** | Not supported | Built-in support |
| **AOP** | Manual configuration | Automatic processing |
| **Usage** | Rare, legacy apps | Standard, modern apps |

### When to Use What?

**Use BeanFactory:**
- Resource-constrained environments (mobile, IoT)
- Need complete control over initialization
- Legacy application maintenance

**Use ApplicationContext:**
- All modern applications (99% of cases)
- Need enterprise features
- Spring Boot applications
- Web applications
- AOP and event-driven designs

---

## Practical Examples

### Example 1: Complete Application with ApplicationContext

**Domain Model:**
```java
public class User {
    private Long id;
    private String name;
    private String email;

    // Constructors, getters, setters
    public User(Long id, String name, String email) {
        this.id = id;
        this.name = name;
        this.email = email;
    }

    // Getters and setters
}
```

**Repository:**
```java
public interface UserRepository {
    User findById(Long id);
    void save(User user);
}

@Repository
public class InMemoryUserRepository implements UserRepository {
    private Map<Long, User> users = new HashMap<>();

    public InMemoryUserRepository() {
        // Sample data
        users.put(1L, new User(1L, "John Doe", "john@example.com"));
        users.put(2L, new User(2L, "Jane Smith", "jane@example.com"));
    }

    @Override
    public User findById(Long id) {
        return users.get(id);
    }

    @Override
    public void save(User user) {
        users.put(user.getId(), user);
    }
}
```

**Service:**
```java
@Service
public class UserService {
    private final UserRepository repository;

    @Autowired
    public UserService(UserRepository repository) {
        this.repository = repository;
    }

    public User getUser(Long id) {
        return repository.findById(id);
    }

    public void createUser(User user) {
        repository.save(user);
        System.out.println("User created: " + user.getName());
    }
}
```

**Configuration:**
```java
@Configuration
@ComponentScan(basePackages = "com.example")
public class AppConfig {
    // Spring will scan and create beans automatically
}
```

**Application:**
```java
public class Application {
    public static void main(String[] args) {
        // Create ApplicationContext
        ApplicationContext context =
            new AnnotationConfigApplicationContext(AppConfig.class);

        // Get bean from container
        UserService userService = context.getBean(UserService.class);

        // Use the service
        User user = userService.getUser(1L);
        System.out.println("Found user: " + user.getName());

        // Create new user
        User newUser = new User(3L, "Bob Johnson", "bob@example.com");
        userService.createUser(newUser);

        // Close context (important in non-web applications)
        ((AnnotationConfigApplicationContext) context).close();
    }
}
```

### Example 2: Multiple Configuration Files

```java
// Database configuration
@Configuration
public class DatabaseConfig {

    @Bean
    public DataSource dataSource() {
        HikariDataSource dataSource = new HikariDataSource();
        dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/mydb");
        dataSource.setUsername("user");
        dataSource.setPassword("password");
        return dataSource;
    }
}

// Service configuration
@Configuration
@ComponentScan(basePackages = "com.example.service")
public class ServiceConfig {
    // Services will be auto-scanned
}

// Main configuration
@Configuration
@Import({DatabaseConfig.class, ServiceConfig.class})
public class AppConfig {
    // Imports other configurations
}

// Application
public class Application {
    public static void main(String[] args) {
        ApplicationContext context =
            new AnnotationConfigApplicationContext(AppConfig.class);

        // All beans from all configurations are available
        DataSource ds = context.getBean(DataSource.class);
        UserService service = context.getBean(UserService.class);
    }
}
```

---

## Container Lifecycle

### ApplicationContext Lifecycle

```
1. Load Configuration
        ↓
2. Create Bean Definitions
        ↓
3. Process BeanFactoryPostProcessors
        ↓
4. Instantiate Beans
        ↓
5. Inject Dependencies
        ↓
6. Process BeanPostProcessors (before initialization)
        ↓
7. Initialize Beans (@PostConstruct, InitializingBean)
        ↓
8. Process BeanPostProcessors (after initialization)
        ↓
9. Beans Ready for Use
        ↓
10. Container Shutdown
        ↓
11. Destroy Beans (@PreDestroy, DisposableBean)
```

### Lifecycle Hooks Example

```java
@Component
public class LifecycleBean implements InitializingBean, DisposableBean {

    public LifecycleBean() {
        System.out.println("1. Constructor called");
    }

    @PostConstruct
    public void postConstruct() {
        System.out.println("2. @PostConstruct called");
    }

    @Override
    public void afterPropertiesSet() {
        System.out.println("3. afterPropertiesSet() called");
    }

    @PreDestroy
    public void preDestroy() {
        System.out.println("4. @PreDestroy called");
    }

    @Override
    public void destroy() {
        System.out.println("5. destroy() called");
    }
}
```

---

## Best Practices

### 1. Prefer ApplicationContext over BeanFactory

```java
// Good
ApplicationContext context =
    new AnnotationConfigApplicationContext(AppConfig.class);

// Avoid (unless you have specific reasons)
BeanFactory factory = new XmlBeanFactory(
    new ClassPathResource("beans.xml")
);
```

### 2. Use Constructor Injection

```java
// Good - immutable, testable
@Service
public class UserService {
    private final UserRepository repository;

    @Autowired
    public UserService(UserRepository repository) {
        this.repository = repository;
    }
}
```

### 3. Close the Context

```java
public class Application {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context =
            new AnnotationConfigApplicationContext(AppConfig.class);

        try {
            // Use context
        } finally {
            context.close();  // Important!
        }

        // Or use try-with-resources
        try (AnnotationConfigApplicationContext ctx =
                 new AnnotationConfigApplicationContext(AppConfig.class)) {
            // Use context
        }
    }
}
```

### 4. Organize Configuration

```java
// Separate concerns into different configuration classes
@Configuration
@Import({
    DatabaseConfig.class,
    SecurityConfig.class,
    WebConfig.class
})
public class AppConfig {
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| IoC | Framework controls object creation and lifecycle |
| Container | Manages beans: instantiation, configuration, assembly |
| BeanFactory | Lightweight, lazy initialization, basic features |
| ApplicationContext | Enterprise features, eager initialization, preferred |
| Benefits | Loose coupling, testability, flexibility |

## Next Topic

Continue to [Bean Configuration](./04-bean-configuration.md) to learn how to configure beans in Spring.
