# Dependency Injection

## What is Dependency Injection?

Dependency Injection (DI) is a design pattern where objects define their dependencies (the other objects they work with) through constructor arguments, factory method arguments, or properties set on the object instance. The IoC container then injects those dependencies when it creates the bean.

### The Problem DI Solves

**Without Dependency Injection:**
```java
public class UserService {
    private UserRepository repository;

    public UserService() {
        // Tight coupling - hard to test, inflexible
        this.repository = new MySQLUserRepository();
    }

    public User getUser(Long id) {
        return repository.findById(id);
    }
}
```

**Problems:**
- Tight coupling to MySQLUserRepository
- Cannot swap implementations
- Hard to test (cannot mock repository)
- Violates Dependency Inversion Principle

**With Dependency Injection:**
```java
public class UserService {
    private UserRepository repository;

    // Dependency is injected from outside
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
- Easy to swap implementations
- Testable with mocks
- Follows SOLID principles

---

## Dependency Injection Types

Spring supports three types of dependency injection:

```
┌─────────────────────────────────────────┐
│     Dependency Injection Types          │
├─────────────────────────────────────────┤
│                                         │
│  1. Constructor Injection               │
│     └─ Recommended for required deps   │
│                                         │
│  2. Setter Injection                    │
│     └─ Recommended for optional deps   │
│                                         │
│  3. Field Injection                     │
│     └─ Convenient but not recommended  │
│                                         │
└─────────────────────────────────────────┘
```

---

## 1. Constructor Injection

Dependencies are provided through class constructor. This is the recommended approach.

### Basic Constructor Injection

```java
@Service
public class UserService {
    private final UserRepository userRepository;

    @Autowired  // Optional in Spring 4.3+ if only one constructor
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public User getUser(Long id) {
        return userRepository.findById(id);
    }
}
```

### Multiple Dependencies

```java
@Service
public class OrderService {
    private final OrderRepository orderRepository;
    private final UserService userService;
    private final EmailService emailService;

    @Autowired
    public OrderService(
            OrderRepository orderRepository,
            UserService userService,
            EmailService emailService) {
        this.orderRepository = orderRepository;
        this.userService = userService;
        this.emailService = emailService;
    }

    public void createOrder(Order order) {
        orderRepository.save(order);
        User user = userService.getUser(order.getUserId());
        emailService.sendOrderConfirmation(user, order);
    }
}
```

### Constructor Injection in XML

```xml
<bean id="userRepository" class="com.example.repository.UserRepositoryImpl"/>

<bean id="userService" class="com.example.service.UserService">
    <constructor-arg ref="userRepository"/>
</bean>

<!-- Multiple constructor arguments -->
<bean id="orderService" class="com.example.service.OrderService">
    <constructor-arg ref="orderRepository"/>
    <constructor-arg ref="userService"/>
    <constructor-arg ref="emailService"/>
</bean>

<!-- Constructor arguments with index -->
<bean id="productService" class="com.example.service.ProductService">
    <constructor-arg index="0" ref="productRepository"/>
    <constructor-arg index="1" value="Standard"/>
</bean>

<!-- Constructor arguments with type -->
<bean id="paymentService" class="com.example.service.PaymentService">
    <constructor-arg type="com.example.repository.PaymentRepository" ref="paymentRepository"/>
    <constructor-arg type="java.lang.String" value="PayPal"/>
</bean>
```

### Constructor Injection in Java Config

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

    // Alternative: Spring auto-wires parameters
    @Bean
    public UserService userService(UserRepository userRepository) {
        return new UserService(userRepository);
    }
}
```

### Advantages of Constructor Injection

1. **Immutability**: Dependencies can be final
2. **Required Dependencies**: Constructor enforces required dependencies
3. **Testability**: Easy to instantiate in tests
4. **Null Safety**: Cannot create object without dependencies
5. **Thread Safety**: Immutable objects are thread-safe

```java
// Testing with constructor injection
@Test
public void testUserService() {
    UserRepository mockRepo = mock(UserRepository.class);
    UserService service = new UserService(mockRepo);

    when(mockRepo.findById(1L)).thenReturn(new User(1L, "John"));

    User user = service.getUser(1L);
    assertEquals("John", user.getName());
}
```

---

## 2. Setter Injection

Dependencies are provided through setter methods. Use for optional dependencies.

### Basic Setter Injection

```java
@Service
public class EmailService {
    private String smtpHost;
    private int smtpPort;
    private EmailTemplate template;  // Optional dependency

    @Autowired(required = false)  // Optional dependency
    public void setTemplate(EmailTemplate template) {
        this.template = template;
    }

    public void setSmtpHost(String smtpHost) {
        this.smtpHost = smtpHost;
    }

    public void setSmtpPort(int smtpPort) {
        this.smtpPort = smtpPort;
    }

    public void sendEmail(String to, String message) {
        // Send email logic
    }
}
```

### Setter Injection in XML

```xml
<bean id="emailService" class="com.example.service.EmailService">
    <property name="smtpHost" value="smtp.gmail.com"/>
    <property name="smtpPort" value="587"/>
    <property name="template" ref="emailTemplate"/>
</bean>

<!-- Using p-namespace (more concise) -->
<beans xmlns:p="http://www.springframework.org/schema/p">
    <bean id="emailService" class="com.example.service.EmailService"
          p:smtpHost="smtp.gmail.com"
          p:smtpPort="587"
          p:template-ref="emailTemplate"/>
</beans>
```

### Setter Injection in Java Config

```java
@Configuration
public class AppConfig {

    @Bean
    public EmailService emailService() {
        EmailService service = new EmailService();
        service.setSmtpHost("smtp.gmail.com");
        service.setSmtpPort(587);
        service.setTemplate(emailTemplate());
        return service;
    }

    @Bean
    public EmailTemplate emailTemplate() {
        return new EmailTemplate();
    }
}
```

### When to Use Setter Injection

```java
@Service
public class ReportService {
    private final ReportRepository repository;  // Required
    private ReportFormatter formatter;          // Optional, has default

    // Constructor for required dependencies
    @Autowired
    public ReportService(ReportRepository repository) {
        this.repository = repository;
        this.formatter = new DefaultReportFormatter();  // Default
    }

    // Setter for optional dependencies
    @Autowired(required = false)
    public void setFormatter(ReportFormatter formatter) {
        this.formatter = formatter;
    }

    public Report generateReport() {
        Report report = repository.getData();
        return formatter.format(report);
    }
}
```

---

## 3. Field Injection

Dependencies are injected directly into fields using @Autowired.

### Basic Field Injection

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    @Autowired
    private EmailService emailService;

    public User getUser(Long id) {
        return userRepository.findById(id);
    }

    public void notifyUser(User user, String message) {
        emailService.sendEmail(user.getEmail(), message);
    }
}
```

### Why Field Injection is Not Recommended

**Problems:**
1. **Cannot create immutable objects**: Fields cannot be final
2. **Hard to test**: Requires reflection to inject dependencies
3. **Hidden dependencies**: Not obvious from constructor/setters
4. **Circular dependency risk**: Easier to create circular dependencies
5. **Framework coupling**: Tight coupling to Spring framework

**Better Alternative - Constructor Injection:**
```java
@Service
public class UserService {
    private final UserRepository userRepository;
    private final EmailService emailService;

    @Autowired
    public UserService(UserRepository userRepository, EmailService emailService) {
        this.userRepository = userRepository;
        this.emailService = emailService;
    }
}
```

### When Field Injection is Acceptable

- Quick prototypes and demos
- Test classes (but prefer @MockBean or manual injection)
- Configuration classes (for framework beans like ApplicationContext)

```java
@Configuration
public class AppConfig {

    @Autowired
    private Environment environment;  // Acceptable in config classes

    @Bean
    public DataSource dataSource() {
        String url = environment.getProperty("db.url");
        // Configure datasource
    }
}
```

---

## @Autowired Annotation

The @Autowired annotation marks a dependency for automatic injection by Spring.

### @Autowired Behavior

```java
// Constructor autowiring (recommended)
@Service
public class UserService {
    private final UserRepository repository;

    @Autowired  // Optional if only one constructor
    public UserService(UserRepository repository) {
        this.repository = repository;
    }
}

// Setter autowiring
@Service
public class EmailService {
    private EmailTemplate template;

    @Autowired
    public void setTemplate(EmailTemplate template) {
        this.template = template;
    }
}

// Field autowiring (not recommended)
@Service
public class NotificationService {
    @Autowired
    private EmailService emailService;
}
```

### Required vs Optional Dependencies

```java
@Service
public class ReportService {

    // Required dependency (default)
    @Autowired
    private ReportRepository repository;

    // Optional dependency
    @Autowired(required = false)
    private ReportCache cache;

    public Report getReport(Long id) {
        if (cache != null) {
            Report cached = cache.get(id);
            if (cached != null) return cached;
        }
        return repository.findById(id);
    }
}
```

### Autowiring Collections

```java
@Service
public class NotificationService {

    // Inject all beans of type MessageHandler
    @Autowired
    private List<MessageHandler> handlers;

    // Inject beans as a map (key = bean name, value = bean)
    @Autowired
    private Map<String, MessageHandler> handlerMap;

    public void notify(Message message) {
        for (MessageHandler handler : handlers) {
            handler.handle(message);
        }
    }
}
```

### @Autowired with Generics

```java
// Generic repository
public interface Repository<T> {
    T findById(Long id);
}

@Repository
public class UserRepository implements Repository<User> {
    public User findById(Long id) {
        // Implementation
    }
}

@Service
public class UserService {

    @Autowired
    private Repository<User> userRepository;  // Spring resolves generic type

    public User getUser(Long id) {
        return userRepository.findById(id);
    }
}
```

---

## Qualifier Annotation

Use @Qualifier when multiple beans of the same type exist.

### The Problem

```java
public interface MessageService {
    void sendMessage(String message);
}

@Service
public class EmailMessageService implements MessageService {
    public void sendMessage(String message) {
        // Send via email
    }
}

@Service
public class SmsMessageService implements MessageService {
    public void sendMessage(String message) {
        // Send via SMS
    }
}

@Service
public class NotificationService {

    @Autowired
    private MessageService messageService;  // Error! Which one?
}
```

### Solution: Using @Qualifier

```java
@Service
@Qualifier("email")
public class EmailMessageService implements MessageService {
    public void sendMessage(String message) {
        System.out.println("Sending email: " + message);
    }
}

@Service
@Qualifier("sms")
public class SmsMessageService implements MessageService {
    public void sendMessage(String message) {
        System.out.println("Sending SMS: " + message);
    }
}

@Service
public class NotificationService {

    private final MessageService emailService;
    private final MessageService smsService;

    @Autowired
    public NotificationService(
            @Qualifier("email") MessageService emailService,
            @Qualifier("sms") MessageService smsService) {
        this.emailService = emailService;
        this.smsService = smsService;
    }

    public void sendNotification(String message, String type) {
        if ("email".equals(type)) {
            emailService.sendMessage(message);
        } else if ("sms".equals(type)) {
            smsService.sendMessage(message);
        }
    }
}
```

### Using Bean Names as Qualifiers

```java
@Service("emailService")
public class EmailMessageService implements MessageService {
}

@Service("smsService")
public class SmsMessageService implements MessageService {
}

@Service
public class NotificationService {

    @Autowired
    @Qualifier("emailService")
    private MessageService emailService;

    @Autowired
    @Qualifier("smsService")
    private MessageService smsService;
}
```

### Custom Qualifier Annotations

```java
// Define custom qualifier annotation
@Target({ElementType.FIELD, ElementType.PARAMETER, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Email {
}

@Target({ElementType.FIELD, ElementType.PARAMETER, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Sms {
}

// Use custom qualifiers
@Service
@Email
public class EmailMessageService implements MessageService {
}

@Service
@Sms
public class SmsMessageService implements MessageService {
}

@Service
public class NotificationService {

    @Autowired
    @Email
    private MessageService emailService;

    @Autowired
    @Sms
    private MessageService smsService;
}
```

---

## Autowiring Modes

Spring supports different autowiring strategies.

### Autowiring Strategies

| Mode | Description | Use Case |
|------|-------------|----------|
| **no** | No autowiring (default in XML) | Manual wiring |
| **byName** | Autowire by property name | Property name matches bean name |
| **byType** | Autowire by property type | One bean of type exists |
| **constructor** | Autowire via constructor | Constructor injection |

### Autowiring in XML

```xml
<!-- No autowiring (default) -->
<bean id="userService" class="com.example.UserService">
    <property name="userRepository" ref="userRepository"/>
</bean>

<!-- Autowire by name -->
<bean id="userService" class="com.example.UserService" autowire="byName"/>
<bean id="userRepository" class="com.example.UserRepositoryImpl"/>

<!-- Autowire by type -->
<bean id="userService" class="com.example.UserService" autowire="byType"/>
<bean class="com.example.UserRepositoryImpl"/>

<!-- Autowire by constructor -->
<bean id="userService" class="com.example.UserService" autowire="constructor"/>
```

### Autowiring with Annotations

```java
// Autowire by type (default)
@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;
}

// Autowire by name using @Qualifier
@Service
public class UserService {
    @Autowired
    @Qualifier("userRepositoryImpl")
    private UserRepository userRepository;
}
```

---

## @Primary Annotation

Mark a bean as the primary candidate when multiple beans of the same type exist.

### Using @Primary

```java
public interface MessageService {
    void sendMessage(String message);
}

@Service
@Primary  // This will be injected by default
public class EmailMessageService implements MessageService {
    public void sendMessage(String message) {
        System.out.println("Sending email: " + message);
    }
}

@Service
public class SmsMessageService implements MessageService {
    public void sendMessage(String message) {
        System.out.println("Sending SMS: " + message);
    }
}

@Service
public class NotificationService {

    @Autowired
    private MessageService messageService;  // EmailMessageService injected

    public void notify(String message) {
        messageService.sendMessage(message);  // Uses email
    }
}
```

### @Primary in Java Config

```java
@Configuration
public class AppConfig {

    @Bean
    @Primary
    public MessageService emailMessageService() {
        return new EmailMessageService();
    }

    @Bean
    public MessageService smsMessageService() {
        return new SmsMessageService();
    }
}
```

### @Primary vs @Qualifier

```java
@Service
@Primary
public class DefaultPaymentService implements PaymentService {
}

@Service
@Qualifier("premium")
public class PremiumPaymentService implements PaymentService {
}

@Service
public class OrderService {

    // Gets DefaultPaymentService (Primary)
    @Autowired
    private PaymentService defaultPayment;

    // Gets PremiumPaymentService (Qualifier overrides Primary)
    @Autowired
    @Qualifier("premium")
    private PaymentService premiumPayment;
}
```

---

## @Resource and @Inject

Alternative injection annotations from Java EE.

### @Resource (JSR-250)

```java
import javax.annotation.Resource;

@Service
public class UserService {

    // Autowire by name
    @Resource(name = "userRepository")
    private UserRepository repository;

    // If no name specified, uses field name
    @Resource
    private EmailService emailService;  // Looks for bean named "emailService"
}
```

### @Inject (JSR-330)

```java
import javax.inject.Inject;
import javax.inject.Named;

@Service
public class UserService {

    @Inject
    private UserRepository repository;

    @Inject
    @Named("smsService")
    private MessageService messageService;
}
```

### Comparison

| Feature | @Autowired | @Resource | @Inject |
|---------|------------|-----------|---------|
| **Source** | Spring | Java EE (JSR-250) | Java EE (JSR-330) |
| **Required** | Supports required attribute | No | No |
| **By Type** | Default | No | Default |
| **By Name** | With @Qualifier | Default | With @Named |
| **Collections** | Yes | No | Yes |

**Recommendation:** Use @Autowired for Spring applications (more features and better Spring integration).

---

## Circular Dependencies

When two beans depend on each other.

### The Problem

```java
@Service
public class ServiceA {
    @Autowired
    private ServiceB serviceB;
}

@Service
public class ServiceB {
    @Autowired
    private ServiceA serviceA;  // Circular dependency!
}
```

### Solutions

#### 1. Redesign (Best Solution)

```java
// Extract common logic to a third service
@Service
public class SharedService {
    public void doSomething() {
        // Shared logic
    }
}

@Service
public class ServiceA {
    @Autowired
    private SharedService sharedService;
}

@Service
public class ServiceB {
    @Autowired
    private SharedService sharedService;
}
```

#### 2. Setter Injection

```java
@Service
public class ServiceA {
    private ServiceB serviceB;

    @Autowired
    public void setServiceB(ServiceB serviceB) {
        this.serviceB = serviceB;
    }
}

@Service
public class ServiceB {
    private ServiceA serviceA;

    @Autowired
    public void setServiceA(ServiceA serviceA) {
        this.serviceA = serviceA;
    }
}
```

#### 3. @Lazy Annotation

```java
@Service
public class ServiceA {
    @Autowired
    @Lazy
    private ServiceB serviceB;
}

@Service
public class ServiceB {
    @Autowired
    private ServiceA serviceA;
}
```

#### 4. ApplicationContextAware

```java
@Service
public class ServiceA implements ApplicationContextAware {
    private ApplicationContext context;

    @Override
    public void setApplicationContext(ApplicationContext context) {
        this.context = context;
    }

    public void useServiceB() {
        ServiceB serviceB = context.getBean(ServiceB.class);
        serviceB.doSomething();
    }
}
```

---

## Best Practices

### 1. Prefer Constructor Injection

```java
// Good
@Service
public class UserService {
    private final UserRepository repository;

    @Autowired
    public UserService(UserRepository repository) {
        this.repository = repository;
    }
}

// Avoid
@Service
public class UserService {
    @Autowired
    private UserRepository repository;
}
```

### 2. Use Final Fields

```java
@Service
public class UserService {
    private final UserRepository repository;  // Immutable

    @Autowired
    public UserService(UserRepository repository) {
        this.repository = repository;
    }
}
```

### 3. Avoid Circular Dependencies

```java
// Bad - Circular dependency
@Service
public class ServiceA {
    @Autowired
    private ServiceB serviceB;
}

// Better - Redesign
@Service
public class ServiceA {
    @Autowired
    private SharedService sharedService;
}
```

### 4. Use @Qualifier for Multiple Implementations

```java
@Service
public class NotificationService {
    @Autowired
    @Qualifier("email")
    private MessageService emailService;
}
```

### 5. Make Dependencies Explicit

```java
// Good - Clear dependencies
@Service
public class OrderService {
    private final OrderRepository orderRepository;
    private final UserService userService;
    private final EmailService emailService;

    @Autowired
    public OrderService(
            OrderRepository orderRepository,
            UserService userService,
            EmailService emailService) {
        this.orderRepository = orderRepository;
        this.userService = userService;
        this.emailService = emailService;
    }
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Dependency Injection | Pattern where dependencies are provided externally |
| Constructor Injection | Recommended, immutable, testable, required dependencies |
| Setter Injection | Optional dependencies, can change at runtime |
| Field Injection | Convenient but not recommended, testing issues |
| @Autowired | Spring's annotation for dependency injection |
| @Qualifier | Disambiguate when multiple beans of same type exist |
| @Primary | Mark default bean when multiple candidates exist |
| Best Practice | Use constructor injection with final fields |

## Next Topic

Continue to [Aspect-Oriented Programming](./06-aspect-oriented-programming.md) to learn about AOP in Spring.
