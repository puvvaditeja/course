# Spring ORM

## What is Spring ORM?

Spring ORM is a module in the Spring Framework that provides integration with Object-Relational Mapping (ORM) frameworks like Hibernate, JPA, JDO, and iBatis. It simplifies database access by providing consistent exception handling, transaction management, and resource management.

### Key Characteristics

- **Framework Integration**: Seamless integration with popular ORM frameworks
- **Exception Translation**: Converts database-specific exceptions to Spring's DataAccessException hierarchy
- **Transaction Management**: Declarative and programmatic transaction support
- **Resource Management**: Automatic management of database connections and sessions
- **Template Support**: JdbcTemplate, HibernateTemplate for simplified data access
- **Testing Support**: Easy to test with mock objects

### Benefits

1. **Reduced Boilerplate Code**: Less code for common operations
2. **Consistent Exception Handling**: Unified exception hierarchy
3. **Transaction Management**: Declarative transactions with @Transactional
4. **Resource Safety**: Automatic cleanup of resources
5. **Framework Independence**: Switch ORM frameworks with minimal code changes
6. **Integration**: Works seamlessly with Spring MVC and other Spring modules

---

## ORM Frameworks Supported

Spring ORM provides integration with several ORM frameworks:

```
┌─────────────────────────────────────────────┐
│           Spring ORM Module                  │
├─────────────────────────────────────────────┤
│                                             │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  │
│  │   JPA    │  │ Hibernate│  │   JDO    │  │
│  │(Standard)│  │ (Native) │  │          │  │
│  └──────────┘  └──────────┘  └──────────┘  │
│                                             │
│  ┌──────────┐  ┌──────────┐                │
│  │  iBatis  │  │   JDBC   │                │
│  │ (MyBatis)│  │ Template │                │
│  └──────────┘  └──────────┘                │
│                                             │
└─────────────────────────────────────────────┘
```

---

## Spring Data Access Architecture

### Layered Architecture

```
┌────────────────────────────────────────────┐
│         Presentation Layer                  │
│         (Controllers)                       │
└─────────────────┬──────────────────────────┘
                  │
                  ▼
┌────────────────────────────────────────────┐
│         Service Layer                       │
│    (Business Logic + Transactions)          │
└─────────────────┬──────────────────────────┘
                  │
                  ▼
┌────────────────────────────────────────────┐
│         Repository/DAO Layer                │
│      (Data Access Operations)               │
└─────────────────┬──────────────────────────┘
                  │
                  ▼
┌────────────────────────────────────────────┐
│         Spring ORM                          │
│   (Exception Translation, Transactions)     │
└─────────────────┬──────────────────────────┘
                  │
                  ▼
┌────────────────────────────────────────────┐
│      ORM Framework (Hibernate/JPA)          │
└─────────────────┬──────────────────────────┘
                  │
                  ▼
┌────────────────────────────────────────────┐
│            Database                         │
└────────────────────────────────────────────┘
```

---

## Configuration

### Maven Dependencies

**pom.xml**
```xml
<project>
    <properties>
        <spring.version>5.3.20</spring.version>
        <hibernate.version>5.6.10.Final</hibernate.version>
    </properties>

    <dependencies>
        <!-- Spring ORM -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-orm</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <!-- Spring Transaction -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <!-- Database Driver (MySQL example) -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.29</version>
        </dependency>

        <!-- Connection Pool (HikariCP) -->
        <dependency>
            <groupId>com.zaxxer</groupId>
            <artifactId>HikariCP</artifactId>
            <version>5.0.1</version>
        </dependency>

        <!-- Hibernate (if using Hibernate) -->
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-core</artifactId>
            <version>${hibernate.version}</version>
        </dependency>

        <!-- JPA API (if using JPA) -->
        <dependency>
            <groupId>javax.persistence</groupId>
            <artifactId>javax.persistence-api</artifactId>
            <version>2.2</version>
        </dependency>
    </dependencies>
</project>
```

### Java Configuration

**DataSourceConfig.java**
```java
package com.example.config;

import com.zaxxer.hikari.HikariConfig;
import com.zaxxer.hikari.HikariDataSource;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;

import javax.sql.DataSource;

/**
 * DataSource configuration
 */
@Configuration
@PropertySource("classpath:database.properties")
public class DataSourceConfig {

    @Value("${db.url}")
    private String url;

    @Value("${db.username}")
    private String username;

    @Value("${db.password}")
    private String password;

    @Value("${db.driver}")
    private String driverClassName;

    @Bean
    public DataSource dataSource() {
        HikariConfig config = new HikariConfig();
        config.setJdbcUrl(url);
        config.setUsername(username);
        config.setPassword(password);
        config.setDriverClassName(driverClassName);

        // Connection pool settings
        config.setMaximumPoolSize(10);
        config.setMinimumIdle(5);
        config.setConnectionTimeout(30000);
        config.setIdleTimeout(600000);
        config.setMaxLifetime(1800000);

        return new HikariDataSource(config);
    }
}
```

**database.properties**
```properties
# Database Configuration
db.driver=com.mysql.cj.jdbc.Driver
db.url=jdbc:mysql://localhost:3306/mydb?useSSL=false&serverTimezone=UTC
db.username=root
db.password=password

# Hibernate Properties
hibernate.dialect=org.hibernate.dialect.MySQL8Dialect
hibernate.show_sql=true
hibernate.format_sql=true
hibernate.hbm2ddl.auto=update
hibernate.jdbc.batch_size=20
hibernate.jdbc.fetch_size=50
```

---

## JPA Configuration

### EntityManagerFactory Configuration

**JpaConfig.java**
```java
package com.example.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.orm.jpa.JpaTransactionManager;
import org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean;
import org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.annotation.EnableTransactionManagement;

import javax.persistence.EntityManagerFactory;
import javax.sql.DataSource;
import java.util.Properties;

/**
 * JPA Configuration with Hibernate as provider
 */
@Configuration
@EnableTransactionManagement
public class JpaConfig {

    @Bean
    public LocalContainerEntityManagerFactoryBean entityManagerFactory(DataSource dataSource) {
        LocalContainerEntityManagerFactoryBean emf = new LocalContainerEntityManagerFactoryBean();
        emf.setDataSource(dataSource);
        emf.setPackagesToScan("com.example.model");

        // Use Hibernate as JPA provider
        HibernateJpaVendorAdapter vendorAdapter = new HibernateJpaVendorAdapter();
        vendorAdapter.setGenerateDdl(true);
        vendorAdapter.setShowSql(true);
        emf.setJpaVendorAdapter(vendorAdapter);

        // JPA properties
        emf.setJpaProperties(hibernateProperties());

        return emf;
    }

    private Properties hibernateProperties() {
        Properties properties = new Properties();
        properties.setProperty("hibernate.dialect", "org.hibernate.dialect.MySQL8Dialect");
        properties.setProperty("hibernate.show_sql", "true");
        properties.setProperty("hibernate.format_sql", "true");
        properties.setProperty("hibernate.hbm2ddl.auto", "update");
        properties.setProperty("hibernate.jdbc.batch_size", "20");
        properties.setProperty("hibernate.order_inserts", "true");
        properties.setProperty("hibernate.order_updates", "true");
        properties.setProperty("hibernate.jdbc.batch_versioned_data", "true");
        return properties;
    }

    @Bean
    public PlatformTransactionManager transactionManager(EntityManagerFactory emf) {
        JpaTransactionManager transactionManager = new JpaTransactionManager();
        transactionManager.setEntityManagerFactory(emf);
        return transactionManager;
    }
}
```

---

## Entity Classes

### JPA Entity Example

**User.java**
```java
package com.example.model;

import javax.persistence.*;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;

/**
 * JPA Entity representing a User
 */
@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "username", nullable = false, unique = true, length = 50)
    private String username;

    @Column(name = "email", nullable = false, unique = true, length = 100)
    private String email;

    @Column(name = "password", nullable = false)
    private String password;

    @Column(name = "first_name", length = 50)
    private String firstName;

    @Column(name = "last_name", length = 50)
    private String lastName;

    @Column(name = "created_at", nullable = false, updatable = false)
    private LocalDateTime createdAt;

    @Column(name = "updated_at")
    private LocalDateTime updatedAt;

    @Enumerated(EnumType.STRING)
    @Column(name = "role", nullable = false)
    private UserRole role;

    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Order> orders = new ArrayList<>();

    // Lifecycle callbacks
    @PrePersist
    protected void onCreate() {
        createdAt = LocalDateTime.now();
        updatedAt = LocalDateTime.now();
    }

    @PreUpdate
    protected void onUpdate() {
        updatedAt = LocalDateTime.now();
    }

    // Constructors
    public User() {}

    public User(String username, String email, String password) {
        this.username = username;
        this.email = email;
        this.password = password;
    }

    // Getters and Setters
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }

    public String getUsername() { return username; }
    public void setUsername(String username) { this.username = username; }

    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }

    public String getPassword() { return password; }
    public void setPassword(String password) { this.password = password; }

    public String getFirstName() { return firstName; }
    public void setFirstName(String firstName) { this.firstName = firstName; }

    public String getLastName() { return lastName; }
    public void setLastName(String lastName) { this.lastName = lastName; }

    public LocalDateTime getCreatedAt() { return createdAt; }
    public LocalDateTime getUpdatedAt() { return updatedAt; }

    public UserRole getRole() { return role; }
    public void setRole(UserRole role) { this.role = role; }

    public List<Order> getOrders() { return orders; }
    public void setOrders(List<Order> orders) { this.orders = orders; }

    // Helper methods for bidirectional relationship
    public void addOrder(Order order) {
        orders.add(order);
        order.setUser(this);
    }

    public void removeOrder(Order order) {
        orders.remove(order);
        order.setUser(null);
    }
}
```

**UserRole.java**
```java
package com.example.model;

public enum UserRole {
    USER,
    ADMIN,
    MODERATOR
}
```

**Order.java**
```java
package com.example.model;

import javax.persistence.*;
import java.math.BigDecimal;
import java.time.LocalDateTime;

@Entity
@Table(name = "orders")
public class Order {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "order_number", nullable = false, unique = true)
    private String orderNumber;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id", nullable = false)
    private User user;

    @Column(name = "total_amount", nullable = false, precision = 10, scale = 2)
    private BigDecimal totalAmount;

    @Enumerated(EnumType.STRING)
    @Column(name = "status", nullable = false)
    private OrderStatus status;

    @Column(name = "created_at", nullable = false, updatable = false)
    private LocalDateTime createdAt;

    @PrePersist
    protected void onCreate() {
        createdAt = LocalDateTime.now();
    }

    // Constructors, getters, setters
}
```

---

## Repository Layer

### JPA Repository Interface

**UserRepository.java**
```java
package com.example.repository;

import com.example.model.User;
import java.util.List;
import java.util.Optional;

/**
 * Repository interface for User entity
 */
public interface UserRepository {

    User save(User user);

    Optional<User> findById(Long id);

    List<User> findAll();

    Optional<User> findByUsername(String username);

    Optional<User> findByEmail(String email);

    List<User> findByRole(UserRole role);

    void delete(User user);

    void deleteById(Long id);

    boolean existsById(Long id);

    boolean existsByUsername(String username);

    long count();
}
```

### JPA Repository Implementation

**UserRepositoryImpl.java**
```java
package com.example.repository;

import com.example.model.User;
import com.example.model.UserRole;
import org.springframework.stereotype.Repository;

import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;
import javax.persistence.TypedQuery;
import java.util.List;
import java.util.Optional;

/**
 * JPA implementation of UserRepository
 */
@Repository
public class UserRepositoryImpl implements UserRepository {

    @PersistenceContext
    private EntityManager entityManager;

    @Override
    public User save(User user) {
        if (user.getId() == null) {
            // New entity - persist
            entityManager.persist(user);
            return user;
        } else {
            // Existing entity - merge
            return entityManager.merge(user);
        }
    }

    @Override
    public Optional<User> findById(Long id) {
        User user = entityManager.find(User.class, id);
        return Optional.ofNullable(user);
    }

    @Override
    public List<User> findAll() {
        TypedQuery<User> query = entityManager.createQuery(
            "SELECT u FROM User u ORDER BY u.username", User.class);
        return query.getResultList();
    }

    @Override
    public Optional<User> findByUsername(String username) {
        TypedQuery<User> query = entityManager.createQuery(
            "SELECT u FROM User u WHERE u.username = :username", User.class);
        query.setParameter("username", username);
        try {
            return Optional.of(query.getSingleResult());
        } catch (Exception e) {
            return Optional.empty();
        }
    }

    @Override
    public Optional<User> findByEmail(String email) {
        TypedQuery<User> query = entityManager.createQuery(
            "SELECT u FROM User u WHERE u.email = :email", User.class);
        query.setParameter("email", email);
        try {
            return Optional.of(query.getSingleResult());
        } catch (Exception e) {
            return Optional.empty();
        }
    }

    @Override
    public List<User> findByRole(UserRole role) {
        TypedQuery<User> query = entityManager.createQuery(
            "SELECT u FROM User u WHERE u.role = :role", User.class);
        query.setParameter("role", role);
        return query.getResultList();
    }

    @Override
    public void delete(User user) {
        // Ensure entity is managed before deletion
        if (!entityManager.contains(user)) {
            user = entityManager.merge(user);
        }
        entityManager.remove(user);
    }

    @Override
    public void deleteById(Long id) {
        User user = entityManager.find(User.class, id);
        if (user != null) {
            entityManager.remove(user);
        }
    }

    @Override
    public boolean existsById(Long id) {
        TypedQuery<Long> query = entityManager.createQuery(
            "SELECT COUNT(u) FROM User u WHERE u.id = :id", Long.class);
        query.setParameter("id", id);
        return query.getSingleResult() > 0;
    }

    @Override
    public boolean existsByUsername(String username) {
        TypedQuery<Long> query = entityManager.createQuery(
            "SELECT COUNT(u) FROM User u WHERE u.username = :username", Long.class);
        query.setParameter("username", username);
        return query.getSingleResult() > 0;
    }

    @Override
    public long count() {
        TypedQuery<Long> query = entityManager.createQuery(
            "SELECT COUNT(u) FROM User u", Long.class);
        return query.getSingleResult();
    }
}
```

---

## Service Layer with Transactions

**UserService.java**
```java
package com.example.service;

import com.example.model.User;
import com.example.model.UserRole;
import java.util.List;
import java.util.Optional;

public interface UserService {

    User createUser(User user);

    Optional<User> getUserById(Long id);

    Optional<User> getUserByUsername(String username);

    List<User> getAllUsers();

    List<User> getUsersByRole(UserRole role);

    User updateUser(User user);

    void deleteUser(Long id);

    boolean existsByUsername(String username);

    long getTotalUsers();
}
```

**UserServiceImpl.java**
```java
package com.example.service;

import com.example.model.User;
import com.example.model.UserRole;
import com.example.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;
import java.util.Optional;

/**
 * Service layer with transaction management
 */
@Service
@Transactional(readOnly = true)  // Default for all methods
public class UserServiceImpl implements UserService {

    private final UserRepository userRepository;

    @Autowired
    public UserServiceImpl(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    @Override
    @Transactional  // Read-write transaction
    public User createUser(User user) {
        // Validate user doesn't already exist
        if (userRepository.existsByUsername(user.getUsername())) {
            throw new IllegalArgumentException("Username already exists");
        }

        // Set default role if not specified
        if (user.getRole() == null) {
            user.setRole(UserRole.USER);
        }

        return userRepository.save(user);
    }

    @Override
    public Optional<User> getUserById(Long id) {
        return userRepository.findById(id);
    }

    @Override
    public Optional<User> getUserByUsername(String username) {
        return userRepository.findByUsername(username);
    }

    @Override
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }

    @Override
    public List<User> getUsersByRole(UserRole role) {
        return userRepository.findByRole(role);
    }

    @Override
    @Transactional
    public User updateUser(User user) {
        // Verify user exists
        if (!userRepository.existsById(user.getId())) {
            throw new IllegalArgumentException("User not found");
        }

        return userRepository.save(user);
    }

    @Override
    @Transactional
    public void deleteUser(Long id) {
        if (!userRepository.existsById(id)) {
            throw new IllegalArgumentException("User not found");
        }

        userRepository.deleteById(id);
    }

    @Override
    public boolean existsByUsername(String username) {
        return userRepository.existsByUsername(username);
    }

    @Override
    public long getTotalUsers() {
        return userRepository.count();
    }
}
```

---

## Transaction Management

### Declarative Transactions

**@Transactional Annotation**
```java
@Service
public class OrderService {

    @Autowired
    private OrderRepository orderRepository;

    @Autowired
    private InventoryService inventoryService;

    /**
     * Read-write transaction (default)
     */
    @Transactional
    public Order createOrder(Order order) {
        // Both operations in same transaction
        inventoryService.decreaseStock(order.getItems());
        return orderRepository.save(order);
    }

    /**
     * Read-only transaction (optimization)
     */
    @Transactional(readOnly = true)
    public List<Order> getAllOrders() {
        return orderRepository.findAll();
    }

    /**
     * Custom isolation level
     */
    @Transactional(isolation = Isolation.SERIALIZABLE)
    public void criticalOperation() {
        // High isolation level for critical operations
    }

    /**
     * Custom propagation
     */
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void independentOperation() {
        // Always creates new transaction
    }

    /**
     * Rollback configuration
     */
    @Transactional(rollbackFor = Exception.class,
                   noRollbackFor = CustomException.class)
    public void conditionalRollback() {
        // Rolls back for Exception but not CustomException
    }

    /**
     * Timeout configuration
     */
    @Transactional(timeout = 30)  // 30 seconds
    public void timeoutOperation() {
        // Transaction times out after 30 seconds
    }
}
```

### Programmatic Transactions

```java
@Service
public class PaymentService {

    @Autowired
    private PlatformTransactionManager transactionManager;

    public void processPayment(Payment payment) {
        // Define transaction
        DefaultTransactionDefinition def = new DefaultTransactionDefinition();
        def.setPropagationBehavior(TransactionDefinition.PROPAGATION_REQUIRED);
        def.setIsolationLevel(TransactionDefinition.ISOLATION_READ_COMMITTED);

        // Get transaction status
        TransactionStatus status = transactionManager.getTransaction(def);

        try {
            // Execute operations
            paymentRepository.save(payment);
            accountService.deduct(payment.getAmount());

            // Commit
            transactionManager.commit(status);
        } catch (Exception e) {
            // Rollback on error
            transactionManager.rollback(status);
            throw e;
        }
    }
}
```

---

## Best Practices

### 1. Separate Layers

```java
// Controller - handles HTTP
@Controller
public class UserController {
    @Autowired
    private UserService userService;
}

// Service - business logic + transactions
@Service
@Transactional
public class UserService {
    @Autowired
    private UserRepository userRepository;
}

// Repository - data access
@Repository
public class UserRepository {
    @PersistenceContext
    private EntityManager entityManager;
}
```

### 2. Use Read-Only Transactions

```java
@Transactional(readOnly = true)
public List<User> getAllUsers() {
    return userRepository.findAll();
}
```

### 3. Proper Exception Handling

```java
@Transactional(rollbackFor = Exception.class)
public void criticalOperation() {
    try {
        // Operation
    } catch (SpecificException e) {
        // Handle and rethrow to trigger rollback
        throw new DataAccessException("Operation failed", e);
    }
}
```

### 4. Use Constructor Injection

```java
@Service
public class UserService {
    private final UserRepository userRepository;

    @Autowired
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

### 5. Pagination for Large Results

```java
public Page<User> getUsers(int page, int size) {
    TypedQuery<User> query = entityManager.createQuery(
        "SELECT u FROM User u", User.class);
    query.setFirstResult(page * size);
    query.setMaxResults(size);
    return query.getResultList();
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Spring ORM | Integration with ORM frameworks (Hibernate, JPA) |
| Configuration | DataSource, EntityManagerFactory, TransactionManager |
| Entities | JPA annotations (@Entity, @Table, @Column, etc.) |
| Repository | Data access layer with EntityManager |
| Service Layer | Business logic with transaction management |
| Transactions | @Transactional for declarative transaction management |
| Best Practices | Layered architecture, read-only transactions, proper exception handling |

## Next Topic

Continue to [Hibernate Integration](./07-hibernate-integration.md) to learn about advanced Hibernate features in Spring.
