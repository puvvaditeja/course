# Hibernate Integration

## What is Hibernate Integration?

Hibernate Integration in Spring refers to combining the power of Spring Framework with Hibernate ORM to create robust, scalable data access applications. Spring provides comprehensive support for Hibernate through dependency injection, exception translation, transaction management, and resource management.

### Key Benefits

- **Simplified Configuration**: Spring manages Hibernate SessionFactory
- **Exception Translation**: Database exceptions converted to Spring's DataAccessException
- **Transaction Management**: Declarative transactions with @Transactional
- **Resource Management**: Automatic session and connection cleanup
- **Testing Support**: Easy to mock and test
- **Integration**: Seamless integration with Spring MVC and other modules

### Spring vs Native Hibernate

| Aspect | Native Hibernate | Spring + Hibernate |
|--------|-----------------|-------------------|
| Configuration | hibernate.cfg.xml | Java/Spring configuration |
| Session Management | Manual SessionFactory creation | Spring manages SessionFactory |
| Transaction Management | Manual begin/commit/rollback | Declarative @Transactional |
| Exception Handling | Hibernate exceptions | Spring DataAccessException |
| Resource Cleanup | Manual close() calls | Automatic cleanup |
| Testing | Complex setup | Easy with Spring test support |

---

## Architecture

### Integration Overview

```
┌─────────────────────────────────────────────────────┐
│              Spring MVC Layer                        │
│                (Controllers)                         │
└────────────────────┬────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────┐
│              Service Layer                           │
│        (@Transactional annotations)                  │
└────────────────────┬────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────┐
│              DAO/Repository Layer                    │
│         (SessionFactory injection)                   │
└────────────────────┬────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────┐
│          Spring Hibernate Integration                │
│  - SessionFactory Bean                               │
│  - Transaction Manager                               │
│  - Exception Translation                             │
└────────────────────┬────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────┐
│              Hibernate ORM                           │
│         (Session, Criteria, HQL)                     │
└────────────────────┬────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────┐
│              Database                                │
└─────────────────────────────────────────────────────┘
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
        <!-- Spring Framework -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <!-- Spring ORM (includes transaction support) -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-orm</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <!-- Hibernate Core -->
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-core</artifactId>
            <version>${hibernate.version}</version>
        </dependency>

        <!-- Hibernate Validator (optional) -->
        <dependency>
            <groupId>org.hibernate.validator</groupId>
            <artifactId>hibernate-validator</artifactId>
            <version>6.2.3.Final</version>
        </dependency>

        <!-- Database Driver -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.29</version>
        </dependency>

        <!-- Connection Pool -->
        <dependency>
            <groupId>com.zaxxer</groupId>
            <artifactId>HikariCP</artifactId>
            <version>5.0.1</version>
        </dependency>

        <!-- Logging -->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.36</version>
        </dependency>
    </dependencies>
</project>
```

### Java Configuration

**HibernateConfig.java**
```java
package com.example.config;

import com.zaxxer.hikari.HikariConfig;
import com.zaxxer.hikari.HikariDataSource;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
import org.springframework.orm.hibernate5.HibernateTransactionManager;
import org.springframework.orm.hibernate5.LocalSessionFactoryBean;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.annotation.EnableTransactionManagement;

import javax.sql.DataSource;
import java.util.Properties;

/**
 * Hibernate configuration with Spring
 */
@Configuration
@EnableTransactionManagement
@ComponentScan(basePackages = "com.example")
@PropertySource("classpath:database.properties")
public class HibernateConfig {

    @Value("${db.driver}")
    private String driver;

    @Value("${db.url}")
    private String url;

    @Value("${db.username}")
    private String username;

    @Value("${db.password}")
    private String password;

    @Value("${hibernate.dialect}")
    private String dialect;

    @Value("${hibernate.show_sql}")
    private String showSql;

    @Value("${hibernate.hbm2ddl.auto}")
    private String hbm2ddlAuto;

    /**
     * DataSource configuration with HikariCP
     */
    @Bean
    public DataSource dataSource() {
        HikariConfig config = new HikariConfig();
        config.setDriverClassName(driver);
        config.setJdbcUrl(url);
        config.setUsername(username);
        config.setPassword(password);

        // Connection pool settings
        config.setMaximumPoolSize(10);
        config.setMinimumIdle(5);
        config.setConnectionTimeout(30000);
        config.setIdleTimeout(600000);
        config.setMaxLifetime(1800000);
        config.setConnectionTestQuery("SELECT 1");

        return new HikariDataSource(config);
    }

    /**
     * SessionFactory configuration
     */
    @Bean
    public LocalSessionFactoryBean sessionFactory() {
        LocalSessionFactoryBean sessionFactory = new LocalSessionFactoryBean();
        sessionFactory.setDataSource(dataSource());
        sessionFactory.setPackagesToScan("com.example.model");
        sessionFactory.setHibernateProperties(hibernateProperties());
        return sessionFactory;
    }

    /**
     * Hibernate properties
     */
    private Properties hibernateProperties() {
        Properties properties = new Properties();
        properties.setProperty("hibernate.dialect", dialect);
        properties.setProperty("hibernate.show_sql", showSql);
        properties.setProperty("hibernate.format_sql", "true");
        properties.setProperty("hibernate.hbm2ddl.auto", hbm2ddlAuto);

        // Performance settings
        properties.setProperty("hibernate.jdbc.batch_size", "20");
        properties.setProperty("hibernate.order_inserts", "true");
        properties.setProperty("hibernate.order_updates", "true");
        properties.setProperty("hibernate.jdbc.batch_versioned_data", "true");

        // Second-level cache (optional)
        properties.setProperty("hibernate.cache.use_second_level_cache", "false");
        properties.setProperty("hibernate.cache.use_query_cache", "false");

        // Connection pool settings
        properties.setProperty("hibernate.connection.pool_size", "10");

        return properties;
    }

    /**
     * Transaction Manager
     */
    @Bean
    public PlatformTransactionManager transactionManager() {
        HibernateTransactionManager transactionManager = new HibernateTransactionManager();
        transactionManager.setSessionFactory(sessionFactory().getObject());
        return transactionManager;
    }
}
```

**database.properties**
```properties
# Database Configuration
db.driver=com.mysql.cj.jdbc.Driver
db.url=jdbc:mysql://localhost:3306/springdb?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
db.username=root
db.password=password

# Hibernate Configuration
hibernate.dialect=org.hibernate.dialect.MySQL8Dialect
hibernate.show_sql=true
hibernate.format_sql=true
hibernate.hbm2ddl.auto=update
```

---

## Entity Classes

### Hibernate Entity with Annotations

**Product.java**
```java
package com.example.model;

import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import javax.persistence.*;
import java.math.BigDecimal;
import java.time.LocalDateTime;
import java.util.HashSet;
import java.util.Set;

/**
 * Product entity with Hibernate annotations
 */
@Entity
@Table(name = "products",
       indexes = {
           @Index(name = "idx_product_name", columnList = "name"),
           @Index(name = "idx_product_category", columnList = "category_id")
       })
public class Product {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    private Long id;

    @Column(name = "name", nullable = false, length = 100)
    private String name;

    @Column(name = "description", columnDefinition = "TEXT")
    private String description;

    @Column(name = "price", nullable = false, precision = 10, scale = 2)
    private BigDecimal price;

    @Column(name = "quantity_in_stock", nullable = false)
    private Integer quantityInStock;

    @Column(name = "sku", unique = true, length = 50)
    private String sku;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "category_id", nullable = false)
    private Category category;

    @ManyToMany
    @JoinTable(
        name = "product_tags",
        joinColumns = @JoinColumn(name = "product_id"),
        inverseJoinColumns = @JoinColumn(name = "tag_id")
    )
    private Set<Tag> tags = new HashSet<>();

    @CreationTimestamp
    @Column(name = "created_at", nullable = false, updatable = false)
    private LocalDateTime createdAt;

    @UpdateTimestamp
    @Column(name = "updated_at")
    private LocalDateTime updatedAt;

    @Version
    @Column(name = "version")
    private Long version;

    // Constructors
    public Product() {}

    public Product(String name, BigDecimal price, Integer quantityInStock) {
        this.name = name;
        this.price = price;
        this.quantityInStock = quantityInStock;
    }

    // Getters and Setters
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public String getDescription() { return description; }
    public void setDescription(String description) { this.description = description; }

    public BigDecimal getPrice() { return price; }
    public void setPrice(BigDecimal price) { this.price = price; }

    public Integer getQuantityInStock() { return quantityInStock; }
    public void setQuantityInStock(Integer quantityInStock) {
        this.quantityInStock = quantityInStock;
    }

    public String getSku() { return sku; }
    public void setSku(String sku) { this.sku = sku; }

    public Category getCategory() { return category; }
    public void setCategory(Category category) { this.category = category; }

    public Set<Tag> getTags() { return tags; }
    public void setTags(Set<Tag> tags) { this.tags = tags; }

    public LocalDateTime getCreatedAt() { return createdAt; }
    public LocalDateTime getUpdatedAt() { return updatedAt; }

    public Long getVersion() { return version; }

    // Helper methods
    public void addTag(Tag tag) {
        tags.add(tag);
        tag.getProducts().add(this);
    }

    public void removeTag(Tag tag) {
        tags.remove(tag);
        tag.getProducts().remove(this);
    }
}
```

**Category.java**
```java
package com.example.model;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Entity
@Table(name = "categories")
public class Category {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "name", nullable = false, unique = true, length = 50)
    private String name;

    @Column(name = "description")
    private String description;

    @OneToMany(mappedBy = "category", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Product> products = new ArrayList<>();

    // Constructors, getters, setters
}
```

---

## DAO Layer with Hibernate

### Generic DAO Interface

**GenericDao.java**
```java
package com.example.dao;

import java.io.Serializable;
import java.util.List;
import java.util.Optional;

/**
 * Generic DAO interface for common CRUD operations
 */
public interface GenericDao<T, ID extends Serializable> {

    T save(T entity);

    Optional<T> findById(ID id);

    List<T> findAll();

    void update(T entity);

    void delete(T entity);

    void deleteById(ID id);

    boolean exists(ID id);

    long count();
}
```

### Generic DAO Implementation

**GenericDaoImpl.java**
```java
package com.example.dao;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.springframework.beans.factory.annotation.Autowired;

import javax.persistence.criteria.CriteriaBuilder;
import javax.persistence.criteria.CriteriaQuery;
import java.io.Serializable;
import java.lang.reflect.ParameterizedType;
import java.util.List;
import java.util.Optional;

/**
 * Generic DAO implementation using Hibernate
 */
public abstract class GenericDaoImpl<T, ID extends Serializable> implements GenericDao<T, ID> {

    @Autowired
    protected SessionFactory sessionFactory;

    private final Class<T> entityClass;

    @SuppressWarnings("unchecked")
    public GenericDaoImpl() {
        this.entityClass = (Class<T>) ((ParameterizedType) getClass()
            .getGenericSuperclass()).getActualTypeArguments()[0];
    }

    protected Session getCurrentSession() {
        return sessionFactory.getCurrentSession();
    }

    @Override
    public T save(T entity) {
        getCurrentSession().save(entity);
        return entity;
    }

    @Override
    public Optional<T> findById(ID id) {
        T entity = getCurrentSession().get(entityClass, id);
        return Optional.ofNullable(entity);
    }

    @Override
    public List<T> findAll() {
        CriteriaBuilder builder = getCurrentSession().getCriteriaBuilder();
        CriteriaQuery<T> criteria = builder.createQuery(entityClass);
        criteria.from(entityClass);
        return getCurrentSession().createQuery(criteria).getResultList();
    }

    @Override
    public void update(T entity) {
        getCurrentSession().update(entity);
    }

    @Override
    public void delete(T entity) {
        getCurrentSession().delete(entity);
    }

    @Override
    public void deleteById(ID id) {
        Optional<T> entity = findById(id);
        entity.ifPresent(this::delete);
    }

    @Override
    public boolean exists(ID id) {
        return findById(id).isPresent();
    }

    @Override
    public long count() {
        CriteriaBuilder builder = getCurrentSession().getCriteriaBuilder();
        CriteriaQuery<Long> criteria = builder.createQuery(Long.class);
        criteria.select(builder.count(criteria.from(entityClass)));
        return getCurrentSession().createQuery(criteria).getSingleResult();
    }
}
```

### Product DAO

**ProductDao.java**
```java
package com.example.dao;

import com.example.model.Product;
import java.math.BigDecimal;
import java.util.List;
import java.util.Optional;

public interface ProductDao extends GenericDao<Product, Long> {

    Optional<Product> findBySku(String sku);

    List<Product> findByCategory(Long categoryId);

    List<Product> findByPriceRange(BigDecimal minPrice, BigDecimal maxPrice);

    List<Product> findByNameContaining(String keyword);

    List<Product> findLowStock(int threshold);
}
```

**ProductDaoImpl.java**
```java
package com.example.dao;

import com.example.model.Product;
import org.hibernate.query.Query;
import org.springframework.stereotype.Repository;

import javax.persistence.criteria.CriteriaBuilder;
import javax.persistence.criteria.CriteriaQuery;
import javax.persistence.criteria.Root;
import java.math.BigDecimal;
import java.util.List;
import java.util.Optional;

/**
 * Product DAO implementation with Hibernate
 */
@Repository
public class ProductDaoImpl extends GenericDaoImpl<Product, Long> implements ProductDao {

    @Override
    public Optional<Product> findBySku(String sku) {
        Query<Product> query = getCurrentSession().createQuery(
            "FROM Product p WHERE p.sku = :sku", Product.class);
        query.setParameter("sku", sku);
        return query.uniqueResultOptional();
    }

    @Override
    public List<Product> findByCategory(Long categoryId) {
        Query<Product> query = getCurrentSession().createQuery(
            "FROM Product p WHERE p.category.id = :categoryId ORDER BY p.name", Product.class);
        query.setParameter("categoryId", categoryId);
        return query.list();
    }

    @Override
    public List<Product> findByPriceRange(BigDecimal minPrice, BigDecimal maxPrice) {
        // Using Criteria API
        CriteriaBuilder builder = getCurrentSession().getCriteriaBuilder();
        CriteriaQuery<Product> criteria = builder.createQuery(Product.class);
        Root<Product> root = criteria.from(Product.class);

        criteria.select(root)
                .where(builder.between(root.get("price"), minPrice, maxPrice))
                .orderBy(builder.asc(root.get("price")));

        return getCurrentSession().createQuery(criteria).getResultList();
    }

    @Override
    public List<Product> findByNameContaining(String keyword) {
        Query<Product> query = getCurrentSession().createQuery(
            "FROM Product p WHERE LOWER(p.name) LIKE LOWER(:keyword) ORDER BY p.name",
            Product.class);
        query.setParameter("keyword", "%" + keyword + "%");
        return query.list();
    }

    @Override
    public List<Product> findLowStock(int threshold) {
        Query<Product> query = getCurrentSession().createQuery(
            "FROM Product p WHERE p.quantityInStock <= :threshold ORDER BY p.quantityInStock",
            Product.class);
        query.setParameter("threshold", threshold);
        return query.list();
    }
}
```

---

## Advanced Hibernate Features

### Named Queries

**Product.java (with named queries)**
```java
@Entity
@Table(name = "products")
@NamedQueries({
    @NamedQuery(
        name = "Product.findAll",
        query = "SELECT p FROM Product p ORDER BY p.name"
    ),
    @NamedQuery(
        name = "Product.findByCategory",
        query = "SELECT p FROM Product p WHERE p.category.id = :categoryId"
    ),
    @NamedQuery(
        name = "Product.findExpensive",
        query = "SELECT p FROM Product p WHERE p.price > :threshold ORDER BY p.price DESC"
    )
})
public class Product {
    // Entity definition
}
```

**Using Named Queries**
```java
@Repository
public class ProductDaoImpl extends GenericDaoImpl<Product, Long> {

    public List<Product> findAllProducts() {
        return getCurrentSession()
            .createNamedQuery("Product.findAll", Product.class)
            .list();
    }

    public List<Product> findExpensiveProducts(BigDecimal threshold) {
        return getCurrentSession()
            .createNamedQuery("Product.findExpensive", Product.class)
            .setParameter("threshold", threshold)
            .list();
    }
}
```

### Criteria API

```java
@Repository
public class ProductDaoImpl extends GenericDaoImpl<Product, Long> {

    /**
     * Complex search using Criteria API
     */
    public List<Product> search(String name, Long categoryId,
                               BigDecimal minPrice, BigDecimal maxPrice) {
        CriteriaBuilder builder = getCurrentSession().getCriteriaBuilder();
        CriteriaQuery<Product> criteria = builder.createQuery(Product.class);
        Root<Product> root = criteria.from(Product.class);

        List<Predicate> predicates = new ArrayList<>();

        // Name filter
        if (name != null && !name.isEmpty()) {
            predicates.add(builder.like(
                builder.lower(root.get("name")),
                "%" + name.toLowerCase() + "%"
            ));
        }

        // Category filter
        if (categoryId != null) {
            predicates.add(builder.equal(root.get("category").get("id"), categoryId));
        }

        // Price range filter
        if (minPrice != null) {
            predicates.add(builder.greaterThanOrEqualTo(root.get("price"), minPrice));
        }
        if (maxPrice != null) {
            predicates.add(builder.lessThanOrEqualTo(root.get("price"), maxPrice));
        }

        // Combine predicates
        criteria.select(root)
                .where(predicates.toArray(new Predicate[0]))
                .orderBy(builder.asc(root.get("name")));

        return getCurrentSession().createQuery(criteria).getResultList();
    }
}
```

### Batch Operations

```java
@Repository
public class ProductDaoImpl extends GenericDaoImpl<Product, Long> {

    /**
     * Batch insert products
     */
    public void batchSave(List<Product> products) {
        Session session = getCurrentSession();
        int batchSize = 20;

        for (int i = 0; i < products.size(); i++) {
            session.save(products.get(i));

            if (i % batchSize == 0 && i > 0) {
                // Flush and clear every batch
                session.flush();
                session.clear();
            }
        }
    }

    /**
     * Batch update using HQL
     */
    public int updatePriceByCategory(Long categoryId, BigDecimal percentage) {
        Query query = getCurrentSession().createQuery(
            "UPDATE Product p SET p.price = p.price * :percentage " +
            "WHERE p.category.id = :categoryId");
        query.setParameter("percentage", percentage);
        query.setParameter("categoryId", categoryId);
        return query.executeUpdate();
    }

    /**
     * Batch delete
     */
    public int deleteOutOfStock() {
        Query query = getCurrentSession().createQuery(
            "DELETE FROM Product p WHERE p.quantityInStock = 0");
        return query.executeUpdate();
    }
}
```

### Lazy Loading and Fetching

```java
@Repository
public class ProductDaoImpl extends GenericDaoImpl<Product, Long> {

    /**
     * Fetch product with category (eager)
     */
    public Optional<Product> findByIdWithCategory(Long id) {
        Query<Product> query = getCurrentSession().createQuery(
            "SELECT p FROM Product p " +
            "LEFT JOIN FETCH p.category " +
            "WHERE p.id = :id", Product.class);
        query.setParameter("id", id);
        return query.uniqueResultOptional();
    }

    /**
     * Fetch products with all associations
     */
    public List<Product> findAllWithAssociations() {
        Query<Product> query = getCurrentSession().createQuery(
            "SELECT DISTINCT p FROM Product p " +
            "LEFT JOIN FETCH p.category " +
            "LEFT JOIN FETCH p.tags", Product.class);
        return query.list();
    }

    /**
     * Pagination with lazy loading
     */
    public List<Product> findPaginated(int page, int size) {
        Query<Product> query = getCurrentSession().createQuery(
            "FROM Product p ORDER BY p.name", Product.class);
        query.setFirstResult(page * size);
        query.setMaxResults(size);
        return query.list();
    }
}
```

---

## Service Layer with Transactions

**ProductService.java**
```java
package com.example.service;

import com.example.model.Product;
import java.math.BigDecimal;
import java.util.List;
import java.util.Optional;

public interface ProductService {

    Product createProduct(Product product);

    Optional<Product> getProductById(Long id);

    List<Product> getAllProducts();

    List<Product> searchProducts(String keyword);

    Product updateProduct(Product product);

    void deleteProduct(Long id);

    List<Product> getLowStockProducts(int threshold);

    void updatePrices(Long categoryId, BigDecimal percentage);
}
```

**ProductServiceImpl.java**
```java
package com.example.service;

import com.example.dao.ProductDao;
import com.example.model.Product;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Isolation;
import org.springframework.transaction.annotation.Propagation;
import org.springframework.transaction.annotation.Transactional;

import java.math.BigDecimal;
import java.util.List;
import java.util.Optional;

/**
 * Product service with transaction management
 */
@Service
@Transactional(readOnly = true)
public class ProductServiceImpl implements ProductService {

    private final ProductDao productDao;

    @Autowired
    public ProductServiceImpl(ProductDao productDao) {
        this.productDao = productDao;
    }

    @Override
    @Transactional
    public Product createProduct(Product product) {
        // Validate SKU uniqueness
        productDao.findBySku(product.getSku()).ifPresent(p -> {
            throw new IllegalArgumentException("SKU already exists: " + product.getSku());
        });

        return productDao.save(product);
    }

    @Override
    public Optional<Product> getProductById(Long id) {
        return productDao.findById(id);
    }

    @Override
    public List<Product> getAllProducts() {
        return productDao.findAll();
    }

    @Override
    public List<Product> searchProducts(String keyword) {
        return productDao.findByNameContaining(keyword);
    }

    @Override
    @Transactional
    public Product updateProduct(Product product) {
        if (!productDao.exists(product.getId())) {
            throw new IllegalArgumentException("Product not found: " + product.getId());
        }

        productDao.update(product);
        return product;
    }

    @Override
    @Transactional
    public void deleteProduct(Long id) {
        productDao.deleteById(id);
    }

    @Override
    public List<Product> getLowStockProducts(int threshold) {
        return productDao.findLowStock(threshold);
    }

    @Override
    @Transactional(isolation = Isolation.SERIALIZABLE)
    public void updatePrices(Long categoryId, BigDecimal percentage) {
        productDao.updatePriceByCategory(categoryId, percentage);
    }
}
```

---

## Controller Integration

**ProductController.java**
```java
package com.example.controller;

import com.example.model.Product;
import com.example.service.ProductService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.servlet.mvc.support.RedirectAttributes;

import java.util.List;

/**
 * Product controller using Hibernate-backed service
 */
@Controller
@RequestMapping("/products")
public class ProductController {

    private final ProductService productService;

    @Autowired
    public ProductController(ProductService productService) {
        this.productService = productService;
    }

    @GetMapping
    public String listProducts(Model model) {
        List<Product> products = productService.getAllProducts();
        model.addAttribute("products", products);
        return "products/list";
    }

    @GetMapping("/{id}")
    public String viewProduct(@PathVariable Long id, Model model) {
        Product product = productService.getProductById(id)
            .orElseThrow(() -> new ResourceNotFoundException("Product not found"));
        model.addAttribute("product", product);
        return "products/detail";
    }

    @GetMapping("/new")
    public String showCreateForm(Model model) {
        model.addAttribute("product", new Product());
        return "products/form";
    }

    @PostMapping
    public String createProduct(@ModelAttribute Product product,
                               RedirectAttributes redirectAttributes) {
        Product saved = productService.createProduct(product);
        redirectAttributes.addFlashAttribute("message", "Product created successfully!");
        return "redirect:/products/" + saved.getId();
    }

    @GetMapping("/{id}/edit")
    public String showEditForm(@PathVariable Long id, Model model) {
        Product product = productService.getProductById(id)
            .orElseThrow(() -> new ResourceNotFoundException("Product not found"));
        model.addAttribute("product", product);
        return "products/form";
    }

    @PostMapping("/{id}")
    public String updateProduct(@PathVariable Long id,
                               @ModelAttribute Product product,
                               RedirectAttributes redirectAttributes) {
        product.setId(id);
        productService.updateProduct(product);
        redirectAttributes.addFlashAttribute("message", "Product updated successfully!");
        return "redirect:/products/" + id;
    }

    @PostMapping("/{id}/delete")
    public String deleteProduct(@PathVariable Long id,
                               RedirectAttributes redirectAttributes) {
        productService.deleteProduct(id);
        redirectAttributes.addFlashAttribute("message", "Product deleted successfully!");
        return "redirect:/products";
    }

    @GetMapping("/search")
    public String searchProducts(@RequestParam String keyword, Model model) {
        List<Product> products = productService.searchProducts(keyword);
        model.addAttribute("products", products);
        model.addAttribute("keyword", keyword);
        return "products/list";
    }

    @GetMapping("/low-stock")
    public String lowStockProducts(Model model) {
        List<Product> products = productService.getLowStockProducts(10);
        model.addAttribute("products", products);
        return "products/low-stock";
    }
}
```

---

## Best Practices

### 1. Use Constructor Injection

```java
@Service
public class ProductService {
    private final ProductDao productDao;

    @Autowired
    public ProductService(ProductDao productDao) {
        this.productDao = productDao;
    }
}
```

### 2. Proper Transaction Boundaries

```java
// Service layer handles transactions
@Service
@Transactional(readOnly = true)
public class ProductService {

    @Transactional  // Write transaction
    public Product save(Product product) {
        return productDao.save(product);
    }

    // Read-only transaction (optimization)
    public List<Product> findAll() {
        return productDao.findAll();
    }
}
```

### 3. Handle LazyInitializationException

```java
// Fetch associations when needed
@Transactional(readOnly = true)
public Product getProductWithCategory(Long id) {
    return productDao.findByIdWithCategory(id)
        .orElseThrow(() -> new ResourceNotFoundException("Product not found"));
}
```

### 4. Use Pagination for Large Results

```java
public Page<Product> getProducts(int page, int size) {
    return productDao.findPaginated(page, size);
}
```

### 5. Optimize Batch Operations

```java
@Transactional
public void batchImport(List<Product> products) {
    productDao.batchSave(products);
}
```

### 6. Use Proper Exception Handling

```java
@Transactional
public Product createProduct(Product product) {
    try {
        return productDao.save(product);
    } catch (DataAccessException e) {
        throw new ServiceException("Failed to create product", e);
    }
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Integration | Spring manages Hibernate SessionFactory and transactions |
| Configuration | Java-based config with SessionFactoryBean |
| Entity Mapping | JPA/Hibernate annotations for database mapping |
| DAO Layer | Generic DAO pattern with SessionFactory injection |
| HQL/Criteria | Query languages for database operations |
| Transactions | @Transactional for declarative transaction management |
| Best Practices | Constructor injection, proper transaction boundaries, fetch strategies |

## Next Steps

Now that you've completed all Spring MVC topics, you should:

1. Practice building a complete Spring MVC application
2. Integrate with front-end technologies (Thymeleaf, JSP)
3. Explore Spring Boot for simplified configuration
4. Learn Spring Data JPA for even simpler data access
5. Study Spring Security for authentication and authorization

---

**Congratulations!** You've completed the Spring MVC module.
