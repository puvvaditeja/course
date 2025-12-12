# RESTful CRUD Operations

## Overview

CRUD (Create, Read, Update, Delete) operations form the foundation of most REST APIs. This topic covers implementing complete CRUD functionality with proper HTTP methods, status codes, validation, and error handling.

---

## CRUD Mapping to HTTP Methods

| Operation | HTTP Method | URI Pattern | Success Status | Description |
|-----------|-------------|-------------|----------------|-------------|
| **Create** | POST | `/api/resources` | 201 Created | Create new resource |
| **Read All** | GET | `/api/resources` | 200 OK | Retrieve all resources |
| **Read One** | GET | `/api/resources/{id}` | 200 OK | Retrieve single resource |
| **Update** | PUT | `/api/resources/{id}` | 200 OK | Replace entire resource |
| **Partial Update** | PATCH | `/api/resources/{id}` | 200 OK | Update specific fields |
| **Delete** | DELETE | `/api/resources/{id}` | 204 No Content | Remove resource |

---

## Complete CRUD Example

Let's build a complete Product REST API with all CRUD operations.

### Domain Entity

```java
package com.example.demo.entity;

import jakarta.persistence.*;
import lombok.Data;
import java.math.BigDecimal;
import java.time.LocalDateTime;

@Entity
@Table(name = "products")
@Data
public class Product {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

    @Column(columnDefinition = "TEXT")
    private String description;

    @Column(nullable = false, precision = 10, scale = 2)
    private BigDecimal price;

    @Column(nullable = false)
    private Integer stockQuantity;

    @Column(nullable = false)
    private String category;

    @Column(nullable = false)
    private Boolean active = true;

    @Column(nullable = false, updatable = false)
    private LocalDateTime createdAt;

    @Column(nullable = false)
    private LocalDateTime updatedAt;

    @PrePersist
    protected void onCreate() {
        createdAt = LocalDateTime.now();
        updatedAt = LocalDateTime.now();
    }

    @PreUpdate
    protected void onUpdate() {
        updatedAt = LocalDateTime.now();
    }
}
```

### DTOs

```java
// Create Request DTO
package com.example.demo.dto;

import jakarta.validation.constraints.*;
import lombok.Data;
import java.math.BigDecimal;

@Data
public class CreateProductRequest {

    @NotBlank(message = "Product name is required")
    @Size(min = 3, max = 100, message = "Name must be between 3 and 100 characters")
    private String name;

    @Size(max = 500, message = "Description cannot exceed 500 characters")
    private String description;

    @NotNull(message = "Price is required")
    @DecimalMin(value = "0.01", message = "Price must be greater than 0")
    @Digits(integer = 8, fraction = 2, message = "Invalid price format")
    private BigDecimal price;

    @NotNull(message = "Stock quantity is required")
    @Min(value = 0, message = "Stock quantity cannot be negative")
    private Integer stockQuantity;

    @NotBlank(message = "Category is required")
    private String category;
}

// Update Request DTO
@Data
public class UpdateProductRequest {

    @Size(min = 3, max = 100, message = "Name must be between 3 and 100 characters")
    private String name;

    @Size(max = 500, message = "Description cannot exceed 500 characters")
    private String description;

    @DecimalMin(value = "0.01", message = "Price must be greater than 0")
    @Digits(integer = 8, fraction = 2, message = "Invalid price format")
    private BigDecimal price;

    @Min(value = 0, message = "Stock quantity cannot be negative")
    private Integer stockQuantity;

    private String category;

    private Boolean active;
}

// Response DTO
@Data
public class ProductResponse {
    private Long id;
    private String name;
    private String description;
    private BigDecimal price;
    private Integer stockQuantity;
    private String category;
    private Boolean active;
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
}

// List Response DTO (minimal for list views)
@Data
public class ProductListResponse {
    private Long id;
    private String name;
    private BigDecimal price;
    private String category;
    private Boolean active;
}
```

### Repository

```java
package com.example.demo.repository;

import com.example.demo.entity.Product;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;
import java.util.List;

@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {

    List<Product> findByCategory(String category);

    List<Product> findByActiveTrue();

    List<Product> findByNameContainingIgnoreCase(String name);

    boolean existsByName(String name);
}
```

### Mapper

```java
package com.example.demo.mapper;

import com.example.demo.dto.*;
import com.example.demo.entity.Product;
import org.mapstruct.*;

import java.util.List;

@Mapper(componentModel = MappingConstants.ComponentModel.SPRING)
public interface ProductMapper {

    Product toEntity(CreateProductRequest request);

    ProductResponse toResponse(Product product);

    ProductListResponse toListResponse(Product product);

    List<ProductListResponse> toListResponseList(List<Product> products);

    @Mapping(target = "id", ignore = true)
    @Mapping(target = "createdAt", ignore = true)
    @Mapping(target = "updatedAt", ignore = true)
    @BeanMapping(nullValuePropertyMappingStrategy = NullValuePropertyMappingStrategy.IGNORE)
    void updateEntity(UpdateProductRequest request, @MappingTarget Product product);
}
```

---

## Create Resources (POST)

### Service Layer

```java
package com.example.demo.service;

import com.example.demo.dto.*;
import com.example.demo.entity.Product;
import com.example.demo.exception.DuplicateResourceException;
import com.example.demo.mapper.ProductMapper;
import com.example.demo.repository.ProductRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
@RequiredArgsConstructor
public class ProductService {

    private final ProductRepository productRepository;
    private final ProductMapper productMapper;

    @Transactional
    public ProductResponse createProduct(CreateProductRequest request) {
        // Check for duplicates
        if (productRepository.existsByName(request.getName())) {
            throw new DuplicateResourceException(
                "Product with name '" + request.getName() + "' already exists"
            );
        }

        // Map DTO to entity
        Product product = productMapper.toEntity(request);

        // Save
        Product saved = productRepository.save(product);

        // Map to response
        return productMapper.toResponse(saved);
    }
}
```

### Controller

```java
package com.example.demo.controller;

import com.example.demo.dto.*;
import com.example.demo.service.ProductService;
import jakarta.validation.Valid;
import lombok.RequiredArgsConstructor;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.servlet.support.ServletUriComponentsBuilder;

import java.net.URI;

@RestController
@RequestMapping("/api/products")
@RequiredArgsConstructor
public class ProductController {

    private final ProductService productService;

    @PostMapping
    public ResponseEntity<ProductResponse> createProduct(
            @Valid @RequestBody CreateProductRequest request) {

        ProductResponse created = productService.createProduct(request);

        // Build URI for the created resource
        URI location = ServletUriComponentsBuilder
            .fromCurrentRequest()
            .path("/{id}")
            .buildAndExpand(created.getId())
            .toUri();

        return ResponseEntity.created(location).body(created);
    }
}
```

### Request Example

```http
POST /api/products
Content-Type: application/json

{
    "name": "Laptop Pro 15",
    "description": "High-performance laptop with 16GB RAM",
    "price": 1299.99,
    "stockQuantity": 50,
    "category": "Electronics"
}
```

### Response Example

```http
HTTP/1.1 201 Created
Location: /api/products/1
Content-Type: application/json

{
    "id": 1,
    "name": "Laptop Pro 15",
    "description": "High-performance laptop with 16GB RAM",
    "price": 1299.99,
    "stockQuantity": 50,
    "category": "Electronics",
    "active": true,
    "createdAt": "2023-12-12T10:30:00",
    "updatedAt": "2023-12-12T10:30:00"
}
```

---

## Read Resources (GET)

### Service Layer

```java
@Service
@RequiredArgsConstructor
public class ProductService {

    private final ProductRepository productRepository;
    private final ProductMapper productMapper;

    // Get all products
    public List<ProductListResponse> getAllProducts() {
        List<Product> products = productRepository.findAll();
        return productMapper.toListResponseList(products);
    }

    // Get single product
    public ProductResponse getProductById(Long id) {
        Product product = productRepository.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException(
                "Product not found with id: " + id
            ));
        return productMapper.toResponse(product);
    }

    // Get products by category
    public List<ProductListResponse> getProductsByCategory(String category) {
        List<Product> products = productRepository.findByCategory(category);
        return productMapper.toListResponseList(products);
    }

    // Search products
    public List<ProductListResponse> searchProducts(String name) {
        List<Product> products = productRepository.findByNameContainingIgnoreCase(name);
        return productMapper.toListResponseList(products);
    }
}
```

### Controller

```java
@RestController
@RequestMapping("/api/products")
@RequiredArgsConstructor
public class ProductController {

    private final ProductService productService;

    // GET all products
    @GetMapping
    public ResponseEntity<List<ProductListResponse>> getAllProducts() {
        List<ProductListResponse> products = productService.getAllProducts();
        return ResponseEntity.ok(products);
    }

    // GET single product
    @GetMapping("/{id}")
    public ResponseEntity<ProductResponse> getProduct(@PathVariable Long id) {
        ProductResponse product = productService.getProductById(id);
        return ResponseEntity.ok(product);
    }

    // GET with query parameters
    @GetMapping(params = "category")
    public ResponseEntity<List<ProductListResponse>> getProductsByCategory(
            @RequestParam String category) {
        List<ProductListResponse> products = productService.getProductsByCategory(category);
        return ResponseEntity.ok(products);
    }

    // Search endpoint
    @GetMapping("/search")
    public ResponseEntity<List<ProductListResponse>> searchProducts(
            @RequestParam String q) {
        List<ProductListResponse> products = productService.searchProducts(q);
        return ResponseEntity.ok(products);
    }
}
```

### Request/Response Examples

```http
# Get all products
GET /api/products
HTTP/1.1 200 OK
Content-Type: application/json

[
    {
        "id": 1,
        "name": "Laptop Pro 15",
        "price": 1299.99,
        "category": "Electronics",
        "active": true
    },
    {
        "id": 2,
        "name": "Wireless Mouse",
        "price": 29.99,
        "category": "Accessories",
        "active": true
    }
]

# Get single product
GET /api/products/1
HTTP/1.1 200 OK
Content-Type: application/json

{
    "id": 1,
    "name": "Laptop Pro 15",
    "description": "High-performance laptop with 16GB RAM",
    "price": 1299.99,
    "stockQuantity": 50,
    "category": "Electronics",
    "active": true,
    "createdAt": "2023-12-12T10:30:00",
    "updatedAt": "2023-12-12T10:30:00"
}

# Filter by category
GET /api/products?category=Electronics
HTTP/1.1 200 OK
[...]

# Search
GET /api/products/search?q=laptop
HTTP/1.1 200 OK
[...]
```

---

## Update Resources (PUT/PATCH)

### PUT - Full Update

Replaces the entire resource.

```java
@Service
@RequiredArgsConstructor
public class ProductService {

    @Transactional
    public ProductResponse replaceProduct(Long id, CreateProductRequest request) {
        // Check if exists
        if (!productRepository.existsById(id)) {
            throw new ResourceNotFoundException("Product not found with id: " + id);
        }

        // Create new entity with provided data
        Product product = productMapper.toEntity(request);
        product.setId(id);  // Keep the same ID

        // Save (replaces existing)
        Product updated = productRepository.save(product);

        return productMapper.toResponse(updated);
    }
}
```

```java
@PutMapping("/{id}")
public ResponseEntity<ProductResponse> replaceProduct(
        @PathVariable Long id,
        @Valid @RequestBody CreateProductRequest request) {

    ProductResponse updated = productService.replaceProduct(id, request);
    return ResponseEntity.ok(updated);
}
```

**Request:**
```http
PUT /api/products/1
Content-Type: application/json

{
    "name": "Laptop Pro 15 Updated",
    "description": "Updated description",
    "price": 1399.99,
    "stockQuantity": 45,
    "category": "Electronics"
}
```

### PATCH - Partial Update

Updates only provided fields.

```java
@Service
@RequiredArgsConstructor
public class ProductService {

    @Transactional
    public ProductResponse updateProduct(Long id, UpdateProductRequest request) {
        // Find existing product
        Product product = productRepository.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException(
                "Product not found with id: " + id
            ));

        // Update only provided fields
        productMapper.updateEntity(request, product);

        // Save changes
        Product updated = productRepository.save(product);

        return productMapper.toResponse(updated);
    }
}
```

```java
@PatchMapping("/{id}")
public ResponseEntity<ProductResponse> updateProduct(
        @PathVariable Long id,
        @Valid @RequestBody UpdateProductRequest request) {

    ProductResponse updated = productService.updateProduct(id, request);
    return ResponseEntity.ok(updated);
}
```

**Request:**
```http
PATCH /api/products/1
Content-Type: application/json

{
    "price": 1399.99,
    "stockQuantity": 45
}
```

**Response:**
```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "id": 1,
    "name": "Laptop Pro 15",  // Unchanged
    "description": "High-performance laptop with 16GB RAM",  // Unchanged
    "price": 1399.99,  // Updated
    "stockQuantity": 45,  // Updated
    "category": "Electronics",  // Unchanged
    "active": true,
    "createdAt": "2023-12-12T10:30:00",
    "updatedAt": "2023-12-12T11:45:00"  // Updated timestamp
}
```

### PUT vs PATCH

| Aspect | PUT | PATCH |
|--------|-----|-------|
| **Purpose** | Replace entire resource | Update specific fields |
| **Idempotent** | Yes | Yes (usually) |
| **Request Body** | Complete representation | Partial representation |
| **Missing Fields** | Set to null/default | Unchanged |
| **Use Case** | Full updates | Partial updates |

---

## Delete Resources (DELETE)

### Service Layer

```java
@Service
@RequiredArgsConstructor
public class ProductService {

    @Transactional
    public void deleteProduct(Long id) {
        // Verify product exists
        if (!productRepository.existsById(id)) {
            throw new ResourceNotFoundException("Product not found with id: " + id);
        }

        productRepository.deleteById(id);
    }

    // Soft delete (set active = false)
    @Transactional
    public void deactivateProduct(Long id) {
        Product product = productRepository.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException(
                "Product not found with id: " + id
            ));

        product.setActive(false);
        productRepository.save(product);
    }
}
```

### Controller

```java
@RestController
@RequestMapping("/api/products")
@RequiredArgsConstructor
public class ProductController {

    // Hard delete
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteProduct(@PathVariable Long id) {
        productService.deleteProduct(id);
        return ResponseEntity.noContent().build();
    }

    // Soft delete
    @DeleteMapping("/{id}/deactivate")
    public ResponseEntity<Void> deactivateProduct(@PathVariable Long id) {
        productService.deactivateProduct(id);
        return ResponseEntity.noContent().build();
    }
}
```

### Request/Response Examples

```http
# Hard delete
DELETE /api/products/1
HTTP/1.1 204 No Content

# Soft delete
DELETE /api/products/1/deactivate
HTTP/1.1 204 No Content
```

---

## Pagination and Sorting

For large datasets, implement pagination and sorting.

### Using Spring Data Pageable

```java
@Service
@RequiredArgsConstructor
public class ProductService {

    public Page<ProductListResponse> getAllProducts(Pageable pageable) {
        Page<Product> products = productRepository.findAll(pageable);
        return products.map(productMapper::toListResponse);
    }

    public Page<ProductListResponse> getProductsByCategory(
            String category, Pageable pageable) {
        Page<Product> products = productRepository.findByCategory(category, pageable);
        return products.map(productMapper::toListResponse);
    }
}
```

### Controller

```java
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.web.PageableDefault;

@RestController
@RequestMapping("/api/products")
@RequiredArgsConstructor
public class ProductController {

    @GetMapping
    public ResponseEntity<Page<ProductListResponse>> getAllProducts(
            @PageableDefault(size = 20, sort = "name") Pageable pageable) {

        Page<ProductListResponse> products = productService.getAllProducts(pageable);
        return ResponseEntity.ok(products);
    }
}
```

### Request Examples

```http
# Default pagination
GET /api/products

# Custom page and size
GET /api/products?page=0&size=10

# Sorting
GET /api/products?sort=price,desc

# Multiple sort fields
GET /api/products?sort=category,asc&sort=price,desc

# Combination
GET /api/products?page=1&size=20&sort=name,asc
```

### Response Example

```json
{
    "content": [
        {
            "id": 1,
            "name": "Laptop Pro 15",
            "price": 1299.99,
            "category": "Electronics",
            "active": true
        }
    ],
    "pageable": {
        "sort": {
            "sorted": true,
            "unsorted": false,
            "empty": false
        },
        "pageNumber": 0,
        "pageSize": 20,
        "offset": 0,
        "paged": true,
        "unpaged": false
    },
    "totalPages": 5,
    "totalElements": 95,
    "last": false,
    "first": true,
    "size": 20,
    "number": 0,
    "numberOfElements": 20,
    "empty": false
}
```

---

## Filtering and Searching

### Query Parameters

```java
@Service
@RequiredArgsConstructor
public class ProductService {

    public List<ProductListResponse> searchProducts(
            String name,
            String category,
            BigDecimal minPrice,
            BigDecimal maxPrice,
            Boolean active) {

        // Build dynamic query
        List<Product> products = productRepository.findAll((root, query, cb) -> {
            List<Predicate> predicates = new ArrayList<>();

            if (name != null) {
                predicates.add(cb.like(cb.lower(root.get("name")),
                    "%" + name.toLowerCase() + "%"));
            }
            if (category != null) {
                predicates.add(cb.equal(root.get("category"), category));
            }
            if (minPrice != null) {
                predicates.add(cb.greaterThanOrEqualTo(root.get("price"), minPrice));
            }
            if (maxPrice != null) {
                predicates.add(cb.lessThanOrEqualTo(root.get("price"), maxPrice));
            }
            if (active != null) {
                predicates.add(cb.equal(root.get("active"), active));
            }

            return cb.and(predicates.toArray(new Predicate[0]));
        });

        return productMapper.toListResponseList(products);
    }
}
```

```java
@GetMapping("/filter")
public ResponseEntity<List<ProductListResponse>> filterProducts(
        @RequestParam(required = false) String name,
        @RequestParam(required = false) String category,
        @RequestParam(required = false) BigDecimal minPrice,
        @RequestParam(required = false) BigDecimal maxPrice,
        @RequestParam(required = false) Boolean active) {

    List<ProductListResponse> products = productService.searchProducts(
        name, category, minPrice, maxPrice, active
    );

    return ResponseEntity.ok(products);
}
```

### Request Examples

```http
# Filter by category
GET /api/products/filter?category=Electronics

# Filter by price range
GET /api/products/filter?minPrice=100&maxPrice=500

# Multiple filters
GET /api/products/filter?category=Electronics&minPrice=1000&active=true

# Search by name
GET /api/products/filter?name=laptop
```

---

## Validation

### Bean Validation

Use Jakarta Bean Validation annotations on DTOs.

```java
import jakarta.validation.constraints.*;

@Data
public class CreateProductRequest {

    @NotBlank(message = "Product name is required")
    @Size(min = 3, max = 100)
    private String name;

    @NotNull
    @DecimalMin(value = "0.01", message = "Price must be positive")
    private BigDecimal price;

    @Min(0)
    @Max(10000)
    private Integer stockQuantity;

    @Pattern(regexp = "^[A-Za-z ]+$", message = "Category must contain only letters")
    private String category;
}
```

### Custom Validators

```java
import jakarta.validation.Constraint;
import jakarta.validation.Payload;
import java.lang.annotation.*;

@Documented
@Constraint(validatedBy = UniqueProductNameValidator.class)
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface UniqueProductName {
    String message() default "Product name already exists";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}

@Component
public class UniqueProductNameValidator
        implements ConstraintValidator<UniqueProductName, String> {

    @Autowired
    private ProductRepository productRepository;

    @Override
    public boolean isValid(String name, ConstraintValidatorContext context) {
        if (name == null) return true;
        return !productRepository.existsByName(name);
    }
}

// Usage
@Data
public class CreateProductRequest {
    @UniqueProductName
    private String name;
}
```

---

## Error Handling

### Custom Exceptions

```java
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}

public class DuplicateResourceException extends RuntimeException {
    public DuplicateResourceException(String message) {
        super(message);
    }
}
```

### Global Exception Handler

```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.FieldError;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(ResourceNotFoundException ex) {
        ErrorResponse error = new ErrorResponse(
            HttpStatus.NOT_FOUND.value(),
            ex.getMessage(),
            System.currentTimeMillis()
        );
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }

    @ExceptionHandler(DuplicateResourceException.class)
    public ResponseEntity<ErrorResponse> handleDuplicate(DuplicateResourceException ex) {
        ErrorResponse error = new ErrorResponse(
            HttpStatus.CONFLICT.value(),
            ex.getMessage(),
            System.currentTimeMillis()
        );
        return ResponseEntity.status(HttpStatus.CONFLICT).body(error);
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ValidationErrorResponse> handleValidation(
            MethodArgumentNotValidException ex) {

        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getAllErrors().forEach(error -> {
            String fieldName = ((FieldError) error).getField();
            String errorMessage = error.getDefaultMessage();
            errors.put(fieldName, errorMessage);
        });

        ValidationErrorResponse response = new ValidationErrorResponse(
            HttpStatus.BAD_REQUEST.value(),
            "Validation failed",
            errors,
            System.currentTimeMillis()
        );

        return ResponseEntity.badRequest().body(response);
    }
}
```

---

## Complete Controller Example

```java
package com.example.demo.controller;

import com.example.demo.dto.*;
import com.example.demo.service.ProductService;
import jakarta.validation.Valid;
import lombok.RequiredArgsConstructor;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.web.PageableDefault;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.servlet.support.ServletUriComponentsBuilder;

import java.net.URI;
import java.util.List;

@RestController
@RequestMapping("/api/products")
@RequiredArgsConstructor
public class ProductController {

    private final ProductService productService;

    // CREATE
    @PostMapping
    public ResponseEntity<ProductResponse> createProduct(
            @Valid @RequestBody CreateProductRequest request) {

        ProductResponse created = productService.createProduct(request);

        URI location = ServletUriComponentsBuilder
            .fromCurrentRequest()
            .path("/{id}")
            .buildAndExpand(created.getId())
            .toUri();

        return ResponseEntity.created(location).body(created);
    }

    // READ ALL
    @GetMapping
    public ResponseEntity<Page<ProductListResponse>> getAllProducts(
            @PageableDefault(size = 20, sort = "name") Pageable pageable) {

        Page<ProductListResponse> products = productService.getAllProducts(pageable);
        return ResponseEntity.ok(products);
    }

    // READ ONE
    @GetMapping("/{id}")
    public ResponseEntity<ProductResponse> getProduct(@PathVariable Long id) {
        ProductResponse product = productService.getProductById(id);
        return ResponseEntity.ok(product);
    }

    // UPDATE (PUT)
    @PutMapping("/{id}")
    public ResponseEntity<ProductResponse> replaceProduct(
            @PathVariable Long id,
            @Valid @RequestBody CreateProductRequest request) {

        ProductResponse updated = productService.replaceProduct(id, request);
        return ResponseEntity.ok(updated);
    }

    // UPDATE (PATCH)
    @PatchMapping("/{id}")
    public ResponseEntity<ProductResponse> updateProduct(
            @PathVariable Long id,
            @Valid @RequestBody UpdateProductRequest request) {

        ProductResponse updated = productService.updateProduct(id, request);
        return ResponseEntity.ok(updated);
    }

    // DELETE
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteProduct(@PathVariable Long id) {
        productService.deleteProduct(id);
        return ResponseEntity.noContent().build();
    }

    // SEARCH/FILTER
    @GetMapping("/search")
    public ResponseEntity<List<ProductListResponse>> searchProducts(
            @RequestParam String q) {
        List<ProductListResponse> products = productService.searchProducts(q);
        return ResponseEntity.ok(products);
    }
}
```

---

## Summary

RESTful CRUD operations follow consistent patterns:

**Key Points:**

1. **HTTP Methods**: Use appropriate methods (GET, POST, PUT, PATCH, DELETE)
2. **Status Codes**: Return correct status codes (200, 201, 204, 404, etc.)
3. **DTOs**: Separate request and response DTOs
4. **Validation**: Validate input with Bean Validation
5. **Error Handling**: Provide meaningful error messages
6. **Pagination**: Implement pagination for large datasets
7. **Filtering**: Support filtering and searching

**Best Practices:**

- Use service layer for business logic
- Use repository layer for data access
- Map between entities and DTOs
- Implement proper exception handling
- Validate all input data
- Return appropriate status codes
- Include Location header for created resources
- Use pagination for list endpoints

**Next Steps:**

In the next topic, we'll explore testing REST APIs using MockMvc and Spring Boot Test.
