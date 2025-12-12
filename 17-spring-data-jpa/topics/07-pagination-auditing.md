# Pagination and Auditing

## Pagination Overview

Pagination is essential for handling large datasets efficiently. Instead of loading all records at once, pagination loads data in manageable chunks (pages), improving performance and user experience.

### Why Pagination Matters

- **Performance**: Reduces memory consumption and database load
- **User Experience**: Faster page loads with smaller data sets
- **Network Efficiency**: Transfers less data per request
- **Scalability**: Handles growing datasets without degradation

---

## Pageable Interface

The `Pageable` interface is Spring Data's abstraction for pagination information.

### Creating Pageable Instances

```java
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;

@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    // Simple pagination (page 0, size 10)
    public Page<Product> getFirstPage() {
        Pageable pageable = PageRequest.of(0, 10);
        return productRepository.findAll(pageable);
    }

    // Pagination with specific page and size
    public Page<Product> getProducts(int page, int size) {
        Pageable pageable = PageRequest.of(page, size);
        return productRepository.findAll(pageable);
    }

    // Pagination with sorting
    public Page<Product> getProductsSorted(int page, int size) {
        Pageable pageable = PageRequest.of(page, size,
            Sort.by("name").ascending());
        return productRepository.findAll(pageable);
    }

    // Unpaged (all results)
    public Page<Product> getAllProducts() {
        return productRepository.findAll(Pageable.unpaged());
    }
}
```

### Pageable Parameters

```java
// Page number (0-indexed)
int page = 0;

// Number of items per page
int size = 20;

// Create Pageable
Pageable pageable = PageRequest.of(page, size);

// With sorting
Pageable pageableWithSort = PageRequest.of(page, size,
    Sort.by("createdAt").descending());

// Multiple sort fields
Pageable pageableMultiSort = PageRequest.of(page, size,
    Sort.by("category").ascending()
        .and(Sort.by("price").descending()));
```

### Using Pageable in Repositories

```java
public interface ProductRepository extends JpaRepository<Product, Long> {

    // Built-in pagination support
    Page<Product> findAll(Pageable pageable);

    // Custom query with pagination
    Page<Product> findByCategory(String category, Pageable pageable);

    // Multiple criteria with pagination
    Page<Product> findByCategoryAndPriceGreaterThan(
        String category, Double price, Pageable pageable);

    // @Query with pagination
    @Query("SELECT p FROM Product p WHERE p.active = :active")
    Page<Product> findActiveProducts(
        @Param("active") boolean active,
        Pageable pageable);

    // Native query with pagination
    @Query(value = "SELECT * FROM products WHERE category = :category",
           countQuery = "SELECT COUNT(*) FROM products WHERE category = :category",
           nativeQuery = true)
    Page<Product> findByCategoryNative(
        @Param("category") String category,
        Pageable pageable);
}
```

### Pageable in REST Controllers

```java
@RestController
@RequestMapping("/api/products")
public class ProductController {

    @Autowired
    private ProductService productService;

    // Spring automatically creates Pageable from request parameters
    // Example: GET /api/products?page=0&size=20&sort=name,asc
    @GetMapping
    public ResponseEntity<Page<Product>> getProducts(Pageable pageable) {
        Page<Product> products = productService.getProducts(pageable);
        return ResponseEntity.ok(products);
    }

    // With custom parameters
    @GetMapping("/search")
    public ResponseEntity<Page<Product>> searchProducts(
            @RequestParam String category,
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "20") int size,
            @RequestParam(defaultValue = "name") String sortBy,
            @RequestParam(defaultValue = "ASC") String direction) {

        Sort.Direction sortDirection = Sort.Direction.fromString(direction);
        Pageable pageable = PageRequest.of(page, size,
            Sort.by(sortDirection, sortBy));

        Page<Product> products = productService.searchByCategory(
            category, pageable);

        return ResponseEntity.ok(products);
    }

    // With @PageableDefault annotation
    @GetMapping("/featured")
    public ResponseEntity<Page<Product>> getFeaturedProducts(
            @PageableDefault(size = 10, sort = "createdAt",
                           direction = Sort.Direction.DESC)
            Pageable pageable) {

        Page<Product> products = productService.getFeaturedProducts(pageable);
        return ResponseEntity.ok(products);
    }
}
```

---

## Sort Object

The `Sort` object defines how results should be ordered.

### Creating Sort Objects

```java
import org.springframework.data.domain.Sort;

// Single field ascending
Sort sortByNameAsc = Sort.by("name").ascending();

// Single field descending
Sort sortByPriceDesc = Sort.by("price").descending();

// Multiple fields
Sort multiSort = Sort.by("category").ascending()
                     .and(Sort.by("price").descending());

// Multiple fields (alternative)
Sort multiSort2 = Sort.by(
    Sort.Order.asc("category"),
    Sort.Order.desc("price")
);

// Case-insensitive sorting
Sort sortIgnoreCase = Sort.by(Sort.Order.asc("name").ignoreCase());

// Null handling
Sort sortWithNulls = Sort.by(
    Sort.Order.asc("name").nullsFirst()
);

Sort sortWithNullsLast = Sort.by(
    Sort.Order.desc("price").nullsLast()
);

// Unsafe sorting (for dynamic property names)
Sort unsafeSort = Sort.by(Sort.Order.asc("customField"));
```

### Using Sort with Repositories

```java
public interface ProductRepository extends JpaRepository<Product, Long> {

    // Using Sort parameter
    List<Product> findByCategory(String category, Sort sort);

    List<Product> findByActive(boolean active, Sort sort);

    // Built-in sorted findAll
    List<Product> findAll(Sort sort);
}

@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    public List<Product> getProductsSorted() {
        Sort sort = Sort.by("name").ascending();
        return productRepository.findAll(sort);
    }

    public List<Product> getProductsByCategory(String category) {
        Sort sort = Sort.by("price").descending()
                        .and(Sort.by("name").ascending());
        return productRepository.findByCategory(category, sort);
    }

    // Dynamic sorting
    public List<Product> getProductsSortedDynamically(
            String sortField, String direction) {

        Sort.Direction sortDirection = direction.equalsIgnoreCase("DESC")
            ? Sort.Direction.DESC : Sort.Direction.ASC;

        Sort sort = Sort.by(sortDirection, sortField);
        return productRepository.findAll(sort);
    }
}
```

### Sort in REST APIs

```java
@RestController
@RequestMapping("/api/products")
public class ProductController {

    @Autowired
    private ProductService productService;

    // Spring creates Sort from request parameters
    // Example: GET /api/products?sort=name,asc&sort=price,desc
    @GetMapping
    public ResponseEntity<List<Product>> getProducts(Sort sort) {
        List<Product> products = productService.getProducts(sort);
        return ResponseEntity.ok(products);
    }

    // With @SortDefault annotation
    @GetMapping("/active")
    public ResponseEntity<List<Product>> getActiveProducts(
            @SortDefault(sort = "createdAt", direction = Sort.Direction.DESC)
            Sort sort) {

        List<Product> products = productService.getActiveProducts(sort);
        return ResponseEntity.ok(products);
    }
}
```

---

## Page vs Slice

Spring Data provides two interfaces for paginated results: `Page` and `Slice`.

### Page Interface

`Page` provides complete information about the result set, including total count.

```java
public interface ProductRepository extends JpaRepository<Product, Long> {
    Page<Product> findByCategory(String category, Pageable pageable);
}

@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    public void demonstratePageUsage() {
        Pageable pageable = PageRequest.of(0, 10);
        Page<Product> page = productRepository.findByCategory("Electronics", pageable);

        // Content
        List<Product> products = page.getContent();

        // Pagination info
        int currentPage = page.getNumber();              // Current page number (0-indexed)
        int pageSize = page.getSize();                   // Page size
        long totalElements = page.getTotalElements();    // Total number of elements
        int totalPages = page.getTotalPages();           // Total number of pages
        int numberOfElements = page.getNumberOfElements(); // Elements in current page

        // Navigation
        boolean hasNext = page.hasNext();
        boolean hasPrevious = page.hasPrevious();
        boolean isFirst = page.isFirst();
        boolean isLast = page.isLast();

        // Sorting
        Sort sort = page.getSort();

        // Pageable for next/previous
        Pageable nextPageable = page.nextPageable();
        Pageable previousPageable = page.previousPageable();
    }

    // REST API response
    public Map<String, Object> getProductsForAPI(String category, int page, int size) {
        Page<Product> productPage = productRepository.findByCategory(
            category, PageRequest.of(page, size));

        Map<String, Object> response = new HashMap<>();
        response.put("products", productPage.getContent());
        response.put("currentPage", productPage.getNumber());
        response.put("totalItems", productPage.getTotalElements());
        response.put("totalPages", productPage.getTotalPages());

        return response;
    }
}
```

### Slice Interface

`Slice` is a lighter alternative that doesn't count total elements (no count query).

```java
public interface ProductRepository extends JpaRepository<Product, Long> {
    Slice<Product> findByCategory(String category, Pageable pageable);
}

@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    public void demonstrateSliceUsage() {
        Pageable pageable = PageRequest.of(0, 10);
        Slice<Product> slice = productRepository.findByCategory("Electronics", pageable);

        // Content
        List<Product> products = slice.getContent();

        // Slice info
        int currentPage = slice.getNumber();
        int pageSize = slice.getSize();
        int numberOfElements = slice.getNumberOfElements();

        // Navigation (no total count)
        boolean hasNext = slice.hasNext();
        boolean hasPrevious = slice.hasPrevious();
        boolean isFirst = slice.isFirst();
        boolean isLast = slice.isLast();

        // Note: slice.getTotalElements() and slice.getTotalPages()
        // are NOT available in Slice
    }
}
```

### Page vs Slice Comparison

| Feature | Page | Slice |
|---------|------|-------|
| Total count | Yes (executes count query) | No |
| Total pages | Yes | No |
| Performance | Slower (additional query) | Faster |
| Use case | Need total count, page numbers | Infinite scroll, "Load more" |
| hasNext() | Accurate | Checks if more results exist |

**When to use Page:**
- Displaying page numbers in UI
- Showing "Page X of Y"
- Need total count for business logic
- Traditional pagination UI

**When to use Slice:**
- Infinite scroll implementations
- "Load more" buttons
- Large datasets where count query is expensive
- Mobile apps with scroll-based loading

---

## Auditing with Spring Data JPA

Auditing automatically tracks who created/modified entities and when, without writing boilerplate code.

### Setting Up Auditing

**Step 1: Enable JPA Auditing**

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.data.jpa.repository.config.EnableJpaAuditing;

@Configuration
@EnableJpaAuditing
public class JpaConfig {
    // Configuration class
}

// Or in main application class
@SpringBootApplication
@EnableJpaAuditing
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

**Step 2: Create Auditable Base Entity**

```java
import jakarta.persistence.*;
import org.springframework.data.annotation.CreatedBy;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedBy;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import java.time.LocalDateTime;

@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public abstract class Auditable {

    @CreatedDate
    @Column(nullable = false, updatable = false)
    private LocalDateTime createdDate;

    @LastModifiedDate
    @Column(nullable = false)
    private LocalDateTime lastModifiedDate;

    @CreatedBy
    @Column(nullable = false, updatable = false)
    private String createdBy;

    @LastModifiedBy
    @Column(nullable = false)
    private String lastModifiedBy;

    // Getters and setters
    public LocalDateTime getCreatedDate() {
        return createdDate;
    }

    public void setCreatedDate(LocalDateTime createdDate) {
        this.createdDate = createdDate;
    }

    public LocalDateTime getLastModifiedDate() {
        return lastModifiedDate;
    }

    public void setLastModifiedDate(LocalDateTime lastModifiedDate) {
        this.lastModifiedDate = lastModifiedDate;
    }

    public String getCreatedBy() {
        return createdBy;
    }

    public void setCreatedBy(String createdBy) {
        this.createdBy = createdBy;
    }

    public String getLastModifiedBy() {
        return lastModifiedBy;
    }

    public void setLastModifiedBy(String lastModifiedBy) {
        this.lastModifiedBy = lastModifiedBy;
    }
}
```

**Step 3: Extend Auditable in Entities**

```java
@Entity
public class Product extends Auditable {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private Double price;

    // Audit fields inherited from Auditable
    // createdDate, lastModifiedDate, createdBy, lastModifiedBy

    // Other fields, getters, setters
}
```

### @CreatedDate and @LastModifiedDate

These annotations automatically populate timestamp fields.

```java
@Entity
@EntityListeners(AuditingEntityListener.class)
public class Order {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String orderNumber;

    @CreatedDate
    @Column(nullable = false, updatable = false)
    private LocalDateTime createdAt;

    @LastModifiedDate
    @Column(nullable = false)
    private LocalDateTime updatedAt;

    // Automatically set on creation and updates
}
```

**Supported Date Types:**
- `java.util.Date`
- `java.time.LocalDateTime`
- `java.time.LocalDate`
- `java.time.Instant`
- `java.time.ZonedDateTime`
- `long` (timestamp)
- `Long` (timestamp)

### @CreatedBy and @LastModifiedBy

These annotations track who created or modified an entity.

**Step 1: Implement AuditorAware**

```java
import org.springframework.data.domain.AuditorAware;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.stereotype.Component;

import java.util.Optional;

@Component
public class AuditorAwareImpl implements AuditorAware<String> {

    @Override
    public Optional<String> getCurrentAuditor() {
        // Get current user from Spring Security
        Authentication authentication =
            SecurityContextHolder.getContext().getAuthentication();

        if (authentication == null || !authentication.isAuthenticated()) {
            return Optional.of("system");
        }

        return Optional.of(authentication.getName());
    }
}
```

**Step 2: Configure Auditing with AuditorAware**

```java
@Configuration
@EnableJpaAuditing(auditorAwareRef = "auditorAwareImpl")
public class JpaConfig {
    // AuditorAware bean will be automatically detected
}
```

**Alternative: Without Spring Security**

```java
@Component
public class AuditorAwareImpl implements AuditorAware<String> {

    @Override
    public Optional<String> getCurrentAuditor() {
        // Get user from session, request, or custom context
        // For demo purposes, returning a fixed value
        return Optional.of("admin");
    }
}
```

### Complete Auditing Example

```java
// Base auditable entity
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public abstract class BaseEntity {

    @CreatedDate
    @Column(name = "created_date", nullable = false, updatable = false)
    private LocalDateTime createdDate;

    @LastModifiedDate
    @Column(name = "last_modified_date")
    private LocalDateTime lastModifiedDate;

    @CreatedBy
    @Column(name = "created_by", updatable = false)
    private String createdBy;

    @LastModifiedBy
    @Column(name = "last_modified_by")
    private String lastModifiedBy;

    @Version
    private Long version;  // Optimistic locking

    // Getters and setters
}

// Entity
@Entity
@Table(name = "products")
public class Product extends BaseEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private Double price;

    // Inherits audit fields from BaseEntity
}

// Usage
@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    @Transactional
    public Product createProduct(String name, Double price) {
        Product product = new Product();
        product.setName(name);
        product.setPrice(price);

        // Audit fields populated automatically
        return productRepository.save(product);
        // createdDate, createdBy set on insert
        // lastModifiedDate, lastModifiedBy set on insert
    }

    @Transactional
    public Product updateProduct(Long id, Double newPrice) {
        Product product = productRepository.findById(id)
            .orElseThrow(() -> new ProductNotFoundException(id));

        product.setPrice(newPrice);

        // Only lastModifiedDate and lastModifiedBy updated
        return productRepository.save(product);
    }
}
```

### Customizing Audit Fields

```java
@Entity
@EntityListeners(AuditingEntityListener.class)
public class Document {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;

    @CreatedDate
    private Instant createdTimestamp;

    @LastModifiedDate
    private Instant modifiedTimestamp;

    @CreatedBy
    private Long createdByUserId;  // Store user ID instead of username

    @LastModifiedBy
    private Long modifiedByUserId;

    // Custom audit field
    @Column(name = "deletion_date")
    private LocalDateTime deletionDate;

    @Column(name = "deleted_by")
    private String deletedBy;

    // Soft delete
    public void softDelete(String deletedBy) {
        this.deletionDate = LocalDateTime.now();
        this.deletedBy = deletedBy;
    }
}
```

### Querying Audit Fields

```java
public interface ProductRepository extends JpaRepository<Product, Long> {

    // Find by creation date range
    List<Product> findByCreatedDateBetween(
        LocalDateTime start, LocalDateTime end);

    // Find by creator
    List<Product> findByCreatedBy(String username);

    // Find recently modified
    @Query("SELECT p FROM Product p WHERE p.lastModifiedDate >= :date " +
           "ORDER BY p.lastModifiedDate DESC")
    List<Product> findRecentlyModified(@Param("date") LocalDateTime date);

    // Find by modifier
    List<Product> findByLastModifiedBy(String username);
}
```

---

## Summary

| Feature | Purpose | Key Points |
|---------|---------|------------|
| Pageable | Pagination parameters | Page number, size, sorting |
| Sort | Ordering results | Single/multiple fields, direction, null handling |
| Page | Complete pagination | Total count, total pages, navigation |
| Slice | Lightweight pagination | No count query, faster, for infinite scroll |
| Auditing | Track changes | @CreatedDate, @LastModifiedDate, @CreatedBy, @LastModifiedBy |

## Module Complete

You have completed the Spring Data JPA module! You should now understand:
- JPA fundamentals and Spring Data JPA benefits
- Setup and configuration
- Entity mapping and relationships
- Repository interfaces and custom queries
- CRUD operations
- Query methods (derived, @Query, native)
- Pagination and sorting
- Auditing features

Continue practicing with the [exercises](../exercises/) to reinforce your learning.
