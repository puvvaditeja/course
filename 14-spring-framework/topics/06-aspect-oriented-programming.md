# Aspect-Oriented Programming (AOP)

## What is Aspect-Oriented Programming?

Aspect-Oriented Programming (AOP) is a programming paradigm that aims to increase modularity by allowing the separation of cross-cutting concerns. It does so by adding additional behavior to existing code without modifying the code itself.

### The Problem: Cross-Cutting Concerns

In traditional object-oriented programming, certain concerns cut across multiple classes and modules:

```java
public class UserService {
    public User getUser(Long id) {
        // Logging - cross-cutting concern
        logger.info("Getting user with id: " + id);

        // Security - cross-cutting concern
        if (!securityContext.hasPermission("USER_READ")) {
            throw new AccessDeniedException();
        }

        // Transaction - cross-cutting concern
        transaction.begin();
        try {
            User user = userRepository.findById(id);

            // Logging - cross-cutting concern
            logger.info("Found user: " + user.getName());

            transaction.commit();
            return user;
        } catch (Exception e) {
            transaction.rollback();
            throw e;
        }
    }
}
```

**Problems:**
- Business logic mixed with infrastructure code
- Code duplication across methods and classes
- Hard to maintain and modify
- Violates Single Responsibility Principle

### The Solution: AOP

With AOP, cross-cutting concerns are extracted into separate modules called "aspects":

```java
@Service
public class UserService {

    @Transactional
    @Secured("USER_READ")
    @Logged
    public User getUser(Long id) {
        // Pure business logic
        return userRepository.findById(id);
    }
}

// Aspect handles logging
@Aspect
@Component
public class LoggingAspect {

    @Around("@annotation(Logged)")
    public Object logMethod(ProceedingJoinPoint joinPoint) throws Throwable {
        logger.info("Calling method: " + joinPoint.getSignature());
        Object result = joinPoint.proceed();
        logger.info("Method returned: " + result);
        return result;
    }
}
```

**Benefits:**
- Clean separation of concerns
- Reusable across application
- Easy to maintain
- Business logic remains focused

---

## Core AOP Concepts

### AOP Terminology

```
┌─────────────────────────────────────────────┐
│           AOP Core Concepts                 │
├─────────────────────────────────────────────┤
│                                             │
│  Aspect      → Module for cross-cutting     │
│                concern (logging, security)  │
│                                             │
│  Join Point  → Point in program execution   │
│                (method call, exception)     │
│                                             │
│  Advice      → Action taken at join point   │
│                (before, after, around)      │
│                                             │
│  Pointcut    → Expression matching          │
│                join points                  │
│                                             │
│  Target      → Object being advised         │
│                                             │
│  Weaving     → Linking aspects with         │
│                target objects               │
│                                             │
└─────────────────────────────────────────────┘
```

### Visual Representation

```
Target Object: UserService
┌──────────────────────────────────────┐
│                                      │
│  ┌────────────────────────────┐     │
│  │  getUser(Long id)          │     │
│  │                            │     │
│  │  // Business logic         │◄────┼─── Join Point
│  │  return repository...      │     │
│  │                            │     │
│  └────────────────────────────┘     │
│                                      │
└──────────────────────────────────────┘
              ▲
              │
              │ Advice Applied
              │
┌─────────────┴────────────────────────┐
│         Aspect: LoggingAspect        │
│                                      │
│  Pointcut: @annotation(Logged)       │
│                                      │
│  Advice: @Around                     │
│  - Log before method                 │
│  - Execute method                    │
│  - Log after method                  │
│                                      │
└──────────────────────────────────────┘
```

---

## Cross-Cutting Concerns

Common examples of cross-cutting concerns that AOP addresses:

### 1. Logging

```java
@Aspect
@Component
public class LoggingAspect {

    private static final Logger logger = LoggerFactory.getLogger(LoggingAspect.class);

    @Before("execution(* com.example.service.*.*(..))")
    public void logBefore(JoinPoint joinPoint) {
        logger.info("Executing: " + joinPoint.getSignature().getName());
    }

    @AfterReturning(pointcut = "execution(* com.example.service.*.*(..))", returning = "result")
    public void logAfterReturning(JoinPoint joinPoint, Object result) {
        logger.info("Method {} returned: {}", joinPoint.getSignature().getName(), result);
    }
}
```

### 2. Security

```java
@Aspect
@Component
public class SecurityAspect {

    @Before("@annotation(secured)")
    public void checkSecurity(JoinPoint joinPoint, Secured secured) {
        String role = secured.value();
        if (!SecurityContext.hasRole(role)) {
            throw new AccessDeniedException("User does not have role: " + role);
        }
    }
}

// Usage
@Service
public class UserService {

    @Secured("ADMIN")
    public void deleteUser(Long id) {
        userRepository.deleteById(id);
    }
}
```

### 3. Performance Monitoring

```java
@Aspect
@Component
public class PerformanceAspect {

    @Around("execution(* com.example.service.*.*(..))")
    public Object measureExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
        long startTime = System.currentTimeMillis();

        Object result = joinPoint.proceed();

        long executionTime = System.currentTimeMillis() - startTime;
        logger.info("{} executed in {} ms",
            joinPoint.getSignature().getName(), executionTime);

        return result;
    }
}
```

### 4. Transaction Management

```java
@Aspect
@Component
public class TransactionAspect {

    @Around("@annotation(org.springframework.transaction.annotation.Transactional)")
    public Object manageTransaction(ProceedingJoinPoint joinPoint) throws Throwable {
        Transaction transaction = transactionManager.beginTransaction();

        try {
            Object result = joinPoint.proceed();
            transaction.commit();
            return result;
        } catch (Exception e) {
            transaction.rollback();
            throw e;
        }
    }
}
```

### 5. Caching

```java
@Aspect
@Component
public class CachingAspect {

    private Map<String, Object> cache = new ConcurrentHashMap<>();

    @Around("@annotation(Cacheable)")
    public Object cache(ProceedingJoinPoint joinPoint) throws Throwable {
        String key = generateKey(joinPoint);

        if (cache.containsKey(key)) {
            logger.info("Cache hit for: " + key);
            return cache.get(key);
        }

        Object result = joinPoint.proceed();
        cache.put(key, result);
        return result;
    }
}
```

---

## AOP Components in Detail

### 1. Aspect

A module that encapsulates a cross-cutting concern.

```java
@Aspect
@Component
public class LoggingAspect {

    private static final Logger logger = LoggerFactory.getLogger(LoggingAspect.class);

    // Pointcuts and advice methods go here
}
```

### 2. Join Point

A point during the execution of a program (method execution, exception handling, etc.).

```java
@Before("execution(* com.example.service.*.*(..))")
public void beforeAdvice(JoinPoint joinPoint) {
    // Access join point information
    String methodName = joinPoint.getSignature().getName();
    String className = joinPoint.getTarget().getClass().getName();
    Object[] args = joinPoint.getArgs();

    logger.info("Executing: {}.{}() with args: {}",
        className, methodName, Arrays.toString(args));
}
```

### 3. Pointcut

An expression that selects one or more join points.

```java
@Aspect
@Component
public class PointcutExamples {

    // Execution pointcut - matches method execution
    @Pointcut("execution(* com.example.service.*.*(..))")
    public void serviceMethods() {}

    // Within pointcut - matches all methods within a type
    @Pointcut("within(com.example.service.*)")
    public void withinService() {}

    // Annotation pointcut - matches methods with annotation
    @Pointcut("@annotation(org.springframework.transaction.annotation.Transactional)")
    public void transactionalMethods() {}

    // Bean pointcut - matches beans by name
    @Pointcut("bean(userService)")
    public void userServiceBean() {}

    // Combining pointcuts
    @Pointcut("serviceMethods() && transactionalMethods()")
    public void transactionalServiceMethods() {}

    @Before("transactionalServiceMethods()")
    public void beforeTransactionalService() {
        // Advice implementation
    }
}
```

### 4. Advice

Action taken at a join point.

**Types of Advice:**

```
┌─────────────────────────────────────┐
│                                     │
│   @Before                           │
│   ────────────►                     │
│                                     │
│   @AfterReturning                   │
│   ◄────────────                     │
│                                     │
│   @AfterThrowing                    │
│   ◄────────────                     │
│                                     │
│   @After                            │
│   ◄────────────                     │
│                                     │
│   @Around                           │
│   ────────────►◄────────────        │
│                                     │
└─────────────────────────────────────┘
```

---

## Advice Types

### 1. @Before Advice

Executes before the join point.

```java
@Aspect
@Component
public class BeforeAdviceExample {

    @Before("execution(* com.example.service.UserService.getUser(..))")
    public void beforeGetUser(JoinPoint joinPoint) {
        logger.info("Before getting user");

        Object[] args = joinPoint.getArgs();
        logger.info("Method arguments: " + Arrays.toString(args));
    }
}
```

**Execution Flow:**
```
1. Before Advice executes
2. Target method executes
3. Return to caller
```

### 2. @After Advice

Executes after the join point (finally behavior - runs regardless of outcome).

```java
@Aspect
@Component
public class AfterAdviceExample {

    @After("execution(* com.example.service.UserService.*(..))")
    public void afterUserServiceMethod(JoinPoint joinPoint) {
        logger.info("After method: " + joinPoint.getSignature().getName());
        // Always executes, even if exception is thrown
    }
}
```

**Execution Flow:**
```
1. Target method executes
2. After Advice executes (regardless of success/failure)
3. Return to caller or throw exception
```

### 3. @AfterReturning Advice

Executes after the join point returns successfully.

```java
@Aspect
@Component
public class AfterReturningAdviceExample {

    @AfterReturning(
        pointcut = "execution(* com.example.service.UserService.getUser(..))",
        returning = "user"
    )
    public void afterReturningUser(JoinPoint joinPoint, User user) {
        logger.info("Method returned user: " + user.getName());
        logger.info("User email: " + user.getEmail());

        // Can access and log the return value
        // Cannot modify the return value
    }
}
```

**Execution Flow:**
```
1. Target method executes successfully
2. AfterReturning Advice executes
3. Return value passed to caller
```

### 4. @AfterThrowing Advice

Executes if the join point throws an exception.

```java
@Aspect
@Component
public class AfterThrowingAdviceExample {

    @AfterThrowing(
        pointcut = "execution(* com.example.service.UserService.*(..))",
        throwing = "exception"
    )
    public void afterThrowingException(JoinPoint joinPoint, Exception exception) {
        logger.error("Method {} threw exception: {}",
            joinPoint.getSignature().getName(),
            exception.getMessage());

        // Can log, send alerts, etc.
        // Cannot suppress the exception
    }
}
```

**Execution Flow:**
```
1. Target method executes
2. Exception is thrown
3. AfterThrowing Advice executes
4. Exception propagates to caller
```

### 5. @Around Advice

Most powerful advice - wraps the join point.

```java
@Aspect
@Component
public class AroundAdviceExample {

    @Around("execution(* com.example.service.UserService.getUser(..))")
    public Object aroundGetUser(ProceedingJoinPoint joinPoint) throws Throwable {
        // Before the method execution
        logger.info("Before method execution");
        long startTime = System.currentTimeMillis();

        Object result = null;
        try {
            // Execute the actual method
            result = joinPoint.proceed();

            // After successful execution
            logger.info("Method executed successfully");

        } catch (Exception e) {
            // Handle exception
            logger.error("Method threw exception: " + e.getMessage());
            throw e;

        } finally {
            // Always executed
            long executionTime = System.currentTimeMillis() - startTime;
            logger.info("Execution time: " + executionTime + " ms");
        }

        // Can modify the return value
        return result;
    }
}
```

**Execution Flow:**
```
1. Around Advice starts
2. Before logic executes
3. joinPoint.proceed() calls target method
4. Target method executes
5. Control returns to Around Advice
6. After logic executes
7. Return to caller
```

---

## Pointcut Expressions

### Execution Pointcut

Matches method execution join points.

```java
// Syntax: execution([modifiers] return-type [class].method([params]) [throws])

// Any public method
@Pointcut("execution(public * *(..))")

// Methods in service package
@Pointcut("execution(* com.example.service.*.*(..))")

// Methods returning User
@Pointcut("execution(com.example.model.User *.*(..))")

// Methods with specific name
@Pointcut("execution(* getUser(..))")

// Methods with specific parameters
@Pointcut("execution(* findById(Long))")

// Methods with any parameters starting with 'find'
@Pointcut("execution(* find*(..))")

// Methods in UserService class
@Pointcut("execution(* com.example.service.UserService.*(..))")

// Methods in any class ending with 'Service'
@Pointcut("execution(* com.example.service.*Service.*(..))")
```

### Within Pointcut

Matches all join points within certain types.

```java
// All methods in service package
@Pointcut("within(com.example.service.*)")

// All methods in service and sub-packages
@Pointcut("within(com.example.service..*)")

// All methods in UserService
@Pointcut("within(com.example.service.UserService)")

// All methods in classes ending with Service
@Pointcut("within(*Service)")
```

### Annotation Pointcut

Matches join points with specific annotations.

```java
// Methods annotated with @Transactional
@Pointcut("@annotation(org.springframework.transaction.annotation.Transactional)")

// Classes annotated with @Service
@Pointcut("@within(org.springframework.stereotype.Service)")

// Methods with any annotation
@Pointcut("@annotation(*)")
```

### Bean Pointcut

Matches Spring beans by name.

```java
// Specific bean
@Pointcut("bean(userService)")

// Beans ending with 'Service'
@Pointcut("bean(*Service)")

// All beans except userService
@Pointcut("bean(*) && !bean(userService)")
```

### Combining Pointcuts

```java
@Aspect
@Component
public class CombinedPointcuts {

    // Define named pointcuts
    @Pointcut("execution(* com.example.service.*.*(..))")
    public void serviceMethods() {}

    @Pointcut("execution(public * *(..))")
    public void publicMethods() {}

    @Pointcut("within(com.example.service.*)")
    public void inServicePackage() {}

    // AND combination
    @Before("serviceMethods() && publicMethods()")
    public void beforePublicServiceMethods() {
        // Executes for public methods in service package
    }

    // OR combination
    @Before("serviceMethods() || inServicePackage()")
    public void beforeServiceRelated() {
        // Executes for service methods or anything in service package
    }

    // NOT combination
    @Before("serviceMethods() && !bean(userService)")
    public void beforeServiceMethodsExceptUser() {
        // Executes for all service methods except userService
    }
}
```

---

## @Aspect Annotation

Marks a class as an aspect containing advice and pointcuts.

### Setting Up AOP

**1. Add Dependencies (Maven):**

```xml
<dependencies>
    <!-- Spring AOP -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aop</artifactId>
        <version>6.1.0</version>
    </dependency>

    <!-- AspectJ Weaver -->
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.9.20</version>
    </dependency>
</dependencies>
```

**2. Enable AspectJ Support:**

```java
// Java Configuration
@Configuration
@EnableAspectJAutoProxy
public class AopConfig {
}

// Or in XML
<aop:aspectj-autoproxy/>
```

**3. Create Aspect:**

```java
@Aspect
@Component
public class MyAspect {

    @Before("execution(* com.example.service.*.*(..))")
    public void beforeAdvice() {
        System.out.println("Before method execution");
    }
}
```

### Complete Aspect Example

```java
@Aspect
@Component
public class LoggingAspect {

    private static final Logger logger = LoggerFactory.getLogger(LoggingAspect.class);

    // Define reusable pointcut
    @Pointcut("execution(* com.example.service.*.*(..))")
    public void serviceMethods() {}

    @Before("serviceMethods()")
    public void logBefore(JoinPoint joinPoint) {
        logger.info("Calling: {}", joinPoint.getSignature().toShortString());
    }

    @AfterReturning(pointcut = "serviceMethods()", returning = "result")
    public void logAfterReturning(JoinPoint joinPoint, Object result) {
        logger.info("{} returned: {}",
            joinPoint.getSignature().toShortString(), result);
    }

    @AfterThrowing(pointcut = "serviceMethods()", throwing = "exception")
    public void logAfterThrowing(JoinPoint joinPoint, Throwable exception) {
        logger.error("{} threw exception: {}",
            joinPoint.getSignature().toShortString(), exception.getMessage());
    }

    @Around("serviceMethods()")
    public Object logAround(ProceedingJoinPoint joinPoint) throws Throwable {
        long startTime = System.currentTimeMillis();

        Object result = joinPoint.proceed();

        long executionTime = System.currentTimeMillis() - startTime;
        logger.info("{} executed in {} ms",
            joinPoint.getSignature().toShortString(), executionTime);

        return result;
    }
}
```

---

## AOP Proxies

Spring AOP uses proxies to implement aspects.

### Proxy Types

```
┌─────────────────────────────────────────┐
│         Spring AOP Proxies              │
├─────────────────────────────────────────┤
│                                         │
│  1. JDK Dynamic Proxy                   │
│     └─ Interface-based                  │
│     └─ Uses java.lang.reflect.Proxy     │
│                                         │
│  2. CGLIB Proxy                         │
│     └─ Class-based                      │
│     └─ Creates subclass                 │
│                                         │
└─────────────────────────────────────────┘
```

### JDK Dynamic Proxy

Used when target implements an interface.

```java
public interface UserService {
    User getUser(Long id);
}

@Service
public class UserServiceImpl implements UserService {
    @Override
    public User getUser(Long id) {
        return new User(id, "John Doe");
    }
}

// Spring creates JDK dynamic proxy
// Proxy implements UserService interface
// Adds aspect behavior
```

**How it Works:**
```
Client
  │
  ▼
┌─────────────────────┐
│   Proxy (JDK)       │ implements UserService
│   ┌─────────────┐   │
│   │ Before      │   │
│   └─────────────┘   │
│   ┌─────────────┐   │
│   │Target Call  │───┼───► UserServiceImpl
│   └─────────────┘   │
│   ┌─────────────┐   │
│   │ After       │   │
│   └─────────────┘   │
└─────────────────────┘
```

### CGLIB Proxy

Used when target doesn't implement an interface.

```java
@Service
public class UserService {  // No interface
    public User getUser(Long id) {
        return new User(id, "John Doe");
    }
}

// Spring creates CGLIB proxy
// Proxy extends UserService class
// Adds aspect behavior
```

**How it Works:**
```
Client
  │
  ▼
┌─────────────────────┐
│ Proxy (CGLIB)       │ extends UserService
│   ┌─────────────┐   │
│   │ Before      │   │
│   └─────────────┘   │
│   ┌─────────────┐   │
│   │super.method()───┼───► UserService.method()
│   └─────────────┘   │
│   ┌─────────────┐   │
│   │ After       │   │
│   └─────────────┘   │
└─────────────────────┘
```

### Forcing CGLIB Proxies

```java
@Configuration
@EnableAspectJAutoProxy(proxyTargetClass = true)  // Force CGLIB
public class AopConfig {
}
```

### Proxy Limitations

**1. Internal Method Calls:**

```java
@Service
public class UserService {

    @Transactional
    public void publicMethod() {
        internalMethod();  // @Transactional won't work!
    }

    @Transactional
    private void internalMethod() {
        // This method is called directly, not through proxy
    }
}
```

**Solution:**

```java
@Service
public class UserService {

    @Autowired
    private UserService self;  // Inject self (proxy)

    @Transactional
    public void publicMethod() {
        self.internalMethod();  // Now goes through proxy
    }

    @Transactional
    public void internalMethod() {
        // Now @Transactional works
    }
}
```

**2. Final Methods/Classes:**

```java
// CGLIB cannot proxy final classes
public final class UserService {  // Won't work with CGLIB
}

// CGLIB cannot override final methods
public class UserService {
    public final void getUser() {  // Aspect won't apply
    }
}
```

---

## Practical Examples

### Example 1: Performance Monitoring

```java
@Aspect
@Component
public class PerformanceMonitoringAspect {

    private static final Logger logger = LoggerFactory.getLogger(PerformanceMonitoringAspect.class);

    @Around("@annotation(Monitor)")
    public Object monitorPerformance(ProceedingJoinPoint joinPoint) throws Throwable {
        long startTime = System.currentTimeMillis();

        Object result = joinPoint.proceed();

        long executionTime = System.currentTimeMillis() - startTime;

        if (executionTime > 1000) {
            logger.warn("SLOW METHOD: {} took {} ms",
                joinPoint.getSignature().toShortString(), executionTime);
        } else {
            logger.info("{} took {} ms",
                joinPoint.getSignature().toShortString(), executionTime);
        }

        return result;
    }
}

// Custom annotation
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Monitor {
}

// Usage
@Service
public class UserService {

    @Monitor
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }
}
```

### Example 2: Exception Handling

```java
@Aspect
@Component
public class ExceptionHandlingAspect {

    @AfterThrowing(
        pointcut = "execution(* com.example.service.*.*(..))",
        throwing = "exception"
    )
    public void handleException(JoinPoint joinPoint, Exception exception) {
        String methodName = joinPoint.getSignature().toShortString();
        Object[] args = joinPoint.getArgs();

        logger.error("Exception in {}: {}", methodName, exception.getMessage());
        logger.error("Method arguments: {}", Arrays.toString(args));

        // Send alert, create ticket, etc.
        alertService.sendAlert("Exception in " + methodName, exception);
    }
}
```

### Example 3: Audit Logging

```java
@Aspect
@Component
public class AuditAspect {

    @Autowired
    private AuditRepository auditRepository;

    @Around("@annotation(Auditable)")
    public Object auditMethod(ProceedingJoinPoint joinPoint) throws Throwable {
        String username = SecurityContext.getCurrentUser();
        String action = joinPoint.getSignature().getName();
        String timestamp = LocalDateTime.now().toString();

        AuditLog log = new AuditLog();
        log.setUsername(username);
        log.setAction(action);
        log.setTimestamp(timestamp);

        try {
            Object result = joinPoint.proceed();
            log.setStatus("SUCCESS");
            return result;
        } catch (Exception e) {
            log.setStatus("FAILURE");
            log.setError(e.getMessage());
            throw e;
        } finally {
            auditRepository.save(log);
        }
    }
}

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Auditable {
}

// Usage
@Service
public class UserService {

    @Auditable
    public void deleteUser(Long id) {
        userRepository.deleteById(id);
    }
}
```

---

## Best Practices

### 1. Keep Aspects Focused

```java
// Good - Single responsibility
@Aspect
@Component
public class LoggingAspect {
    // Only logging logic
}

@Aspect
@Component
public class SecurityAspect {
    // Only security logic
}

// Avoid - Multiple concerns
@Aspect
@Component
public class MixedAspect {
    // Logging, security, caching mixed together - hard to maintain
}
```

### 2. Use Specific Pointcuts

```java
// Good - Specific
@Before("execution(* com.example.service.UserService.deleteUser(..))")

// Avoid - Too broad
@Before("execution(* *(..))")  // Matches everything!
```

### 3. Define Reusable Pointcuts

```java
@Aspect
@Component
public class CommonPointcuts {

    @Pointcut("execution(* com.example.service.*.*(..))")
    public void serviceMethods() {}

    @Pointcut("execution(public * *(..))")
    public void publicMethods() {}

    @Pointcut("@annotation(org.springframework.transaction.annotation.Transactional)")
    public void transactionalMethods() {}
}

// Use in other aspects
@Before("CommonPointcuts.serviceMethods()")
public void beforeService() {
}
```

### 4. Avoid Expensive Operations in Advice

```java
// Avoid
@Before("serviceMethods()")
public void expensiveAdvice() {
    // Expensive database call before every service method!
    List<User> allUsers = userRepository.findAll();
}

// Better
@Before("serviceMethods()")
public void lightweightAdvice(JoinPoint joinPoint) {
    // Just log, don't do expensive operations
    logger.info("Method called: {}", joinPoint.getSignature());
}
```

### 5. Use Appropriate Advice Type

```java
// Use @Before for simple pre-processing
@Before("serviceMethods()")
public void logBefore() {
    logger.info("Method starting");
}

// Use @Around when you need full control
@Around("serviceMethods()")
public Object measureTime(ProceedingJoinPoint joinPoint) throws Throwable {
    long start = System.currentTimeMillis();
    Object result = joinPoint.proceed();
    long time = System.currentTimeMillis() - start;
    logger.info("Time: {} ms", time);
    return result;
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| AOP | Separates cross-cutting concerns from business logic |
| Aspect | Module containing advice and pointcuts |
| Join Point | Point in program execution (method call) |
| Pointcut | Expression selecting join points |
| Advice | Action at join point (Before, After, Around) |
| Proxy | JDK dynamic (interface) or CGLIB (class) |
| Use Cases | Logging, security, transactions, caching, monitoring |

## Next Steps

You've completed the Spring Framework Core topics! You now understand:
- Spring Framework overview and ecosystem
- Maven for project management
- Inversion of Control and containers
- Bean configuration (XML, Annotations, Java)
- Dependency Injection patterns
- Aspect-Oriented Programming

Continue to the next module to learn about Spring Boot, Spring MVC, or Spring Data JPA.

---

**Related Topics:**
- [Spring Overview](./01-spring-overview.md)
- [Maven Basics](./02-maven-basics.md)
- [Inversion of Control](./03-inversion-of-control.md)
- [Bean Configuration](./04-bean-configuration.md)
- [Dependency Injection](./05-dependency-injection.md)
