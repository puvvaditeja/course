# Spring Boot REST APIs

## Overview

Spring Boot provides excellent support for building RESTful web services with minimal configuration. This topic covers Spring Boot 3 features and how to build production-ready REST APIs.

---

## Spring Boot 3 Features for REST APIs

### What's New in Spring Boot 3

Spring Boot 3 (released November 2022) brings significant improvements for building REST APIs:

**Key Updates:**

1. **Java 17 Baseline**: Minimum Java version is 17 (LTS)
2. **Jakarta EE 9+**: Migration from `javax.*` to `jakarta.*` packages
3. **Spring Framework 6**: Built on Spring Framework 6
4. **Native Compilation**: GraalVM native image support for faster startup
5. **Improved Observability**: Built-in support for metrics and tracing
6. **HTTP Interface Clients**: Declarative HTTP clients similar to Feign

### Jakarta EE Migration

Spring Boot 3 uses Jakarta EE 9+ specifications, requiring package changes:

**Before (Spring Boot 2.x):**
```java
import javax.persistence.Entity;
import javax.servlet.http.HttpServletRequest;
import javax.validation.constraints.NotNull;
```

**After (Spring Boot 3.x):**
```java
import jakarta.persistence.Entity;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.validation.constraints.NotNull;
```

### Modern Java Features

Spring Boot 3 leverages Java 17+ features:

```java
// Records for DTOs
public record UserDTO(
    Long id,
    String name,
    String email
) {}

// Text blocks for readable strings
String json = """
    {
        "name": "John Doe",
        "email": "john@example.com"
    }
    """;

// Pattern matching for instanceof
if (obj instanceof UserDTO dto) {
    System.out.println(dto.name());
}

// Switch expressions
String role = switch (user.getRole()) {
    case ADMIN -> "Administrator";
    case USER -> "Regular User";
    case GUEST -> "Guest";
};
```

---

## Creating a REST Controller

### Basic REST Controller

A REST controller handles HTTP requests and returns responses.

```java
package com.example.demo.controller;

import org.springframework.web.bind.annotation.*;
import java.util.List;

@RestController
@RequestMapping("/api/users")
public class UserController {

    // GET /api/users
    @GetMapping
    public List<User> getAllUsers() {
        return userService.findAll();
    }

    // GET /api/users/123
    @GetMapping("/{id}")
    public User getUserById(@PathVariable Long id) {
        return userService.findById(id);
    }

    // POST /api/users
    @PostMapping
    public User createUser(@RequestBody User user) {
        return userService.save(user);
    }

    // PUT /api/users/123
    @PutMapping("/{id}")
    public User updateUser(@PathVariable Long id, @RequestBody User user) {
        return userService.update(id, user);
    }

    // DELETE /api/users/123
    @DeleteMapping("/{id}")
    public void deleteUser(@PathVariable Long id) {
        userService.delete(id);
    }
}
```

### Key Annotations

#### @RestController

Combines `@Controller` and `@ResponseBody`. All methods automatically serialize return values to JSON/XML.

```java
@RestController
public class UserController {
    // All methods return data serialized to JSON
}

// Equivalent to:
@Controller
@ResponseBody
public class UserController {
    // Same functionality
}
```

#### @RequestMapping

Maps HTTP requests to handler methods.

```java
// Class-level: Base path for all methods
@RequestMapping("/api/users")
public class UserController {

    // Method-level: Specific endpoint
    @RequestMapping(value = "/{id}", method = RequestMethod.GET)
    public User getUser(@PathVariable Long id) {
        return userService.findById(id);
    }
}
```

#### HTTP Method Annotations

Specialized shortcuts for common HTTP methods:

```java
@GetMapping("/users")           // GET requests
@PostMapping("/users")          // POST requests
@PutMapping("/users/{id}")      // PUT requests
@PatchMapping("/users/{id}")    // PATCH requests
@DeleteMapping("/users/{id}")   // DELETE requests

// All are shortcuts for @RequestMapping with method specified
@GetMapping("/users")
// equals
@RequestMapping(value = "/users", method = RequestMethod.GET)
```

### Request Parameters and Path Variables

#### @PathVariable

Extracts values from URI path:

```java
// GET /api/users/123
@GetMapping("/users/{id}")
public User getUser(@PathVariable Long id) {
    return userService.findById(id);
}

// Multiple path variables
// GET /api/users/123/orders/456
@GetMapping("/users/{userId}/orders/{orderId}")
public Order getUserOrder(
    @PathVariable Long userId,
    @PathVariable Long orderId
) {
    return orderService.findByUserAndOrder(userId, orderId);
}

// Custom variable name
@GetMapping("/users/{id}")
public User getUser(@PathVariable("id") Long userId) {
    return userService.findById(userId);
}

// Optional path variable (Spring Boot 3)
@GetMapping({"/users", "/users/{id}"})
public ResponseEntity<?> getUsers(@PathVariable(required = false) Long id) {
    if (id == null) {
        return ResponseEntity.ok(userService.findAll());
    }
    return ResponseEntity.ok(userService.findById(id));
}
```

#### @RequestParam

Extracts query parameters from URL:

```java
// GET /api/users?role=admin&status=active
@GetMapping("/users")
public List<User> getUsers(
    @RequestParam String role,
    @RequestParam String status
) {
    return userService.findByRoleAndStatus(role, status);
}

// Optional parameters with defaults
@GetMapping("/users")
public List<User> getUsers(
    @RequestParam(required = false, defaultValue = "user") String role,
    @RequestParam(required = false, defaultValue = "active") String status
) {
    return userService.findByRoleAndStatus(role, status);
}

// Pagination parameters
@GetMapping("/users")
public List<User> getUsers(
    @RequestParam(defaultValue = "0") int page,
    @RequestParam(defaultValue = "20") int size,
    @RequestParam(defaultValue = "id") String sortBy
) {
    return userService.findAll(page, size, sortBy);
}

// Map all parameters
@GetMapping("/users")
public List<User> getUsers(@RequestParam Map<String, String> filters) {
    return userService.findByFilters(filters);
}
```

#### @RequestBody

Binds HTTP request body to method parameter:

```java
// POST /api/users
@PostMapping("/users")
public User createUser(@RequestBody User user) {
    return userService.save(user);
}

// With validation
@PostMapping("/users")
public User createUser(@Valid @RequestBody User user) {
    return userService.save(user);
}
```

### Response Entity

`ResponseEntity` provides fine-grained control over HTTP response:

```java
import org.springframework.http.ResponseEntity;
import org.springframework.http.HttpStatus;

@RestController
@RequestMapping("/api/users")
public class UserController {

    // Return with status code
    @GetMapping("/{id}")
    public ResponseEntity<User> getUser(@PathVariable Long id) {
        User user = userService.findById(id);
        if (user == null) {
            return ResponseEntity.notFound().build();
        }
        return ResponseEntity.ok(user);
    }

    // POST with 201 Created and Location header
    @PostMapping
    public ResponseEntity<User> createUser(@RequestBody User user) {
        User saved = userService.save(user);
        return ResponseEntity
            .created(URI.create("/api/users/" + saved.getId()))
            .body(saved);
    }

    // PUT with custom status
    @PutMapping("/{id}")
    public ResponseEntity<User> updateUser(
        @PathVariable Long id,
        @RequestBody User user
    ) {
        User updated = userService.update(id, user);
        return ResponseEntity
            .status(HttpStatus.OK)
            .header("X-Updated-By", "system")
            .body(updated);
    }

    // DELETE with no content
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        userService.delete(id);
        return ResponseEntity.noContent().build();
    }

    // Custom headers and status
    @GetMapping("/{id}/download")
    public ResponseEntity<byte[]> downloadUserData(@PathVariable Long id) {
        byte[] data = userService.generateReport(id);
        return ResponseEntity.ok()
            .header("Content-Disposition", "attachment; filename=user-" + id + ".pdf")
            .contentType(MediaType.APPLICATION_PDF)
            .body(data);
    }
}
```

**Common ResponseEntity Methods:**

```java
// 200 OK
ResponseEntity.ok(body)
ResponseEntity.ok().build()

// 201 Created
ResponseEntity.created(location).body(body)

// 204 No Content
ResponseEntity.noContent().build()

// 400 Bad Request
ResponseEntity.badRequest().body(errorDetails)

// 404 Not Found
ResponseEntity.notFound().build()

// 500 Internal Server Error
ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build()

// Custom status
ResponseEntity.status(HttpStatus.ACCEPTED).body(body)
```

---

## RESTful Resource Representation

### JSON Serialization with Jackson

Spring Boot uses Jackson for JSON serialization/deserialization by default.

#### Basic Serialization

```java
public class User {
    private Long id;
    private String name;
    private String email;
    private LocalDateTime createdAt;

    // Getters and setters
}

// Automatically serialized to:
{
    "id": 123,
    "name": "John Doe",
    "email": "john@example.com",
    "createdAt": "2023-12-12T10:30:00"
}
```

#### Jackson Annotations

Control JSON serialization with Jackson annotations:

```java
import com.fasterxml.jackson.annotation.*;
import java.time.LocalDateTime;

public class User {

    private Long id;

    @JsonProperty("full_name")  // Custom field name in JSON
    private String name;

    @JsonIgnore  // Exclude from JSON
    private String password;

    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private LocalDateTime createdAt;

    @JsonInclude(JsonInclude.Include.NON_NULL)  // Only include if not null
    private String phoneNumber;

    @JsonProperty(access = JsonProperty.Access.READ_ONLY)
    private LocalDateTime lastModified;  // Only in response, not request

    @JsonProperty(access = JsonProperty.Access.WRITE_ONLY)
    private String passwordReset;  // Only in request, not response

    // Getters and setters
}
```

**Result:**
```json
{
    "id": 123,
    "full_name": "John Doe",
    "createdAt": "2023-12-12 10:30:00",
    "lastModified": "2023-12-12 15:45:00"
}
```

#### Custom Serialization

```java
import com.fasterxml.jackson.core.JsonGenerator;
import com.fasterxml.jackson.databind.JsonSerializer;
import com.fasterxml.jackson.databind.SerializerProvider;
import com.fasterxml.jackson.databind.annotation.JsonSerialize;

public class MoneySerializer extends JsonSerializer<BigDecimal> {
    @Override
    public void serialize(BigDecimal value, JsonGenerator gen, SerializerProvider serializers)
            throws IOException {
        gen.writeString("$" + value.setScale(2, RoundingMode.HALF_UP));
    }
}

public class Product {
    private Long id;
    private String name;

    @JsonSerialize(using = MoneySerializer.class)
    private BigDecimal price;
}

// Result:
{
    "id": 1,
    "name": "Laptop",
    "price": "$999.99"
}
```

### Content Negotiation

Spring Boot supports multiple content types through content negotiation.

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    // Produces JSON (default)
    @GetMapping(value = "/{id}", produces = MediaType.APPLICATION_JSON_VALUE)
    public User getUserAsJson(@PathVariable Long id) {
        return userService.findById(id);
    }

    // Produces XML
    @GetMapping(value = "/{id}", produces = MediaType.APPLICATION_XML_VALUE)
    public User getUserAsXml(@PathVariable Long id) {
        return userService.findById(id);
    }

    // Supports both
    @GetMapping(value = "/{id}",
                produces = {MediaType.APPLICATION_JSON_VALUE, MediaType.APPLICATION_XML_VALUE})
    public User getUser(@PathVariable Long id) {
        return userService.findById(id);
    }

    // Consumes JSON
    @PostMapping(consumes = MediaType.APPLICATION_JSON_VALUE)
    public User createUser(@RequestBody User user) {
        return userService.save(user);
    }
}
```

**Client Request:**
```http
GET /api/users/123
Accept: application/json

# or

GET /api/users/123
Accept: application/xml
```

### HATEOAS (Hypermedia)

HATEOAS (Hypermedia as the Engine of Application State) adds links to responses.

#### Add Dependency

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-hateoas</artifactId>
</dependency>
```

#### Implementation

```java
import org.springframework.hateoas.*;
import static org.springframework.hateoas.server.mvc.WebMvcLinkBuilder.*;

@RestController
@RequestMapping("/api/users")
public class UserController {

    @GetMapping("/{id}")
    public EntityModel<User> getUser(@PathVariable Long id) {
        User user = userService.findById(id);

        // Add links
        EntityModel<User> resource = EntityModel.of(user);
        resource.add(linkTo(methodOn(UserController.class).getUser(id)).withSelfRel());
        resource.add(linkTo(methodOn(UserController.class).getAllUsers()).withRel("users"));
        resource.add(linkTo(methodOn(OrderController.class).getUserOrders(id)).withRel("orders"));

        return resource;
    }

    @GetMapping
    public CollectionModel<EntityModel<User>> getAllUsers() {
        List<EntityModel<User>> users = userService.findAll().stream()
            .map(user -> EntityModel.of(user,
                linkTo(methodOn(UserController.class).getUser(user.getId())).withSelfRel()))
            .collect(Collectors.toList());

        return CollectionModel.of(users,
            linkTo(methodOn(UserController.class).getAllUsers()).withSelfRel());
    }
}
```

**Response:**
```json
{
    "id": 123,
    "name": "John Doe",
    "email": "john@example.com",
    "_links": {
        "self": {
            "href": "http://localhost:8080/api/users/123"
        },
        "users": {
            "href": "http://localhost:8080/api/users"
        },
        "orders": {
            "href": "http://localhost:8080/api/users/123/orders"
        }
    }
}
```

---

## Exception Handling

### @ExceptionHandler

Handle exceptions at controller level:

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id);
    }

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleUserNotFound(UserNotFoundException ex) {
        ErrorResponse error = new ErrorResponse(
            HttpStatus.NOT_FOUND.value(),
            ex.getMessage(),
            System.currentTimeMillis()
        );
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }
}
```

### @ControllerAdvice

Global exception handling for all controllers:

```java
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.http.ResponseEntity;
import org.springframework.http.HttpStatus;

@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleUserNotFound(UserNotFoundException ex) {
        ErrorResponse error = new ErrorResponse(
            HttpStatus.NOT_FOUND.value(),
            ex.getMessage(),
            System.currentTimeMillis()
        );
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }

    @ExceptionHandler(ValidationException.class)
    public ResponseEntity<ErrorResponse> handleValidation(ValidationException ex) {
        ErrorResponse error = new ErrorResponse(
            HttpStatus.BAD_REQUEST.value(),
            ex.getMessage(),
            System.currentTimeMillis()
        );
        return ResponseEntity.badRequest().body(error);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGeneric(Exception ex) {
        ErrorResponse error = new ErrorResponse(
            HttpStatus.INTERNAL_SERVER_ERROR.value(),
            "An unexpected error occurred",
            System.currentTimeMillis()
        );
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(error);
    }
}
```

### Error Response Model

```java
public class ErrorResponse {
    private int status;
    private String message;
    private long timestamp;
    private List<String> errors;

    // Constructors, getters, setters
}
```

### Validation Error Handling

```java
import org.springframework.validation.FieldError;
import org.springframework.web.bind.MethodArgumentNotValidException;

@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ValidationErrorResponse> handleValidation(
            MethodArgumentNotValidException ex) {

        List<FieldError> fieldErrors = ex.getBindingResult().getFieldErrors();
        List<String> errors = fieldErrors.stream()
            .map(error -> error.getField() + ": " + error.getDefaultMessage())
            .collect(Collectors.toList());

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

## Cross-Origin Resource Sharing (CORS)

### @CrossOrigin Annotation

Enable CORS for specific controllers or methods:

```java
// Controller-level
@RestController
@RequestMapping("/api/users")
@CrossOrigin(origins = "http://localhost:4200")
public class UserController {
    // All methods allow CORS from localhost:4200
}

// Method-level
@RestController
@RequestMapping("/api/users")
public class UserController {

    @CrossOrigin(origins = {"http://localhost:4200", "https://app.example.com"})
    @GetMapping
    public List<User> getAllUsers() {
        return userService.findAll();
    }
}

// Allow all origins (not recommended for production)
@CrossOrigin(origins = "*")
@GetMapping
public List<User> getAllUsers() {
    return userService.findAll();
}
```

### Global CORS Configuration

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class CorsConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/**")
            .allowedOrigins("http://localhost:4200", "https://app.example.com")
            .allowedMethods("GET", "POST", "PUT", "DELETE", "PATCH")
            .allowedHeaders("*")
            .allowCredentials(true)
            .maxAge(3600);
    }
}
```

---

## Summary

Spring Boot 3 provides powerful features for building REST APIs:

**Key Features:**
- `@RestController` for automatic JSON serialization
- HTTP method annotations (`@GetMapping`, `@PostMapping`, etc.)
- `ResponseEntity` for fine-grained response control
- Jackson for JSON serialization with rich annotation support
- Global exception handling with `@ControllerAdvice`
- Built-in CORS support

**Best Practices:**
1. Use appropriate HTTP methods and status codes
2. Implement proper exception handling
3. Configure CORS for security
4. Use DTOs for clean API contracts (covered in next topic)
5. Validate input data
6. Document APIs with OpenAPI/Swagger

**Next Steps:**

In the next topic, we'll explore Data Transfer Objects (DTOs) and mapping strategies to create clean separation between domain models and API representations.
