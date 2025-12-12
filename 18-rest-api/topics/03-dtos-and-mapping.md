# DTOs and Mapping

## Overview

Data Transfer Objects (DTOs) are a design pattern used to transfer data between different layers of an application. This topic covers why DTOs are important, how to implement them, and strategies for mapping between DTOs and domain entities.

---

## Data Transfer Objects (DTOs)

### What are DTOs?

A Data Transfer Object (DTO) is a simple object designed to carry data between processes or layers. DTOs encapsulate data and send it from one subsystem of an application to another.

**Key Characteristics:**

- No business logic (only getters and setters)
- Used for data transfer, not business operations
- Can aggregate data from multiple sources
- Often have a different structure than domain entities

### Why Use DTOs?

#### 1. Decoupling

DTOs decouple the API contract from the domain model.

```java
// Domain Entity (internal representation)
@Entity
public class User {
    @Id
    private Long id;
    private String username;
    private String passwordHash;  // Should not be exposed
    private String email;
    private LocalDateTime createdAt;
    private LocalDateTime lastLogin;
    private boolean enabled;

    @OneToMany(mappedBy = "user")
    private List<Order> orders;  // May cause circular references
}

// DTO (external representation)
public class UserDTO {
    private Long id;
    private String username;
    private String email;
    // No sensitive or complex fields
}
```

#### 2. Security

Prevent exposing sensitive or internal data.

```java
public class UserResponseDTO {
    private Long id;
    private String username;
    private String email;
    // passwordHash is NOT included
    // Internal IDs and timestamps not exposed
}
```

#### 3. Customization

Tailor responses to specific use cases.

```java
// Minimal DTO for list views
public class UserListDTO {
    private Long id;
    private String username;
}

// Detailed DTO for single user view
public class UserDetailDTO {
    private Long id;
    private String username;
    private String email;
    private String fullName;
    private LocalDateTime memberSince;
    private List<OrderSummaryDTO> recentOrders;
}
```

#### 4. API Versioning

Different DTO versions support API evolution.

```java
// Version 1
public class UserDTOV1 {
    private Long id;
    private String name;
}

// Version 2 - split name field
public class UserDTOV2 {
    private Long id;
    private String firstName;
    private String lastName;
}
```

#### 5. Performance

Reduce payload size by including only needed fields.

```java
// Heavy entity with relationships
@Entity
public class Product {
    // ... many fields
    @ManyToOne
    private Category category;
    @OneToMany
    private List<Review> reviews;
    @ManyToMany
    private List<Tag> tags;
}

// Lightweight DTO
public class ProductListDTO {
    private Long id;
    private String name;
    private BigDecimal price;
    private String categoryName;  // Just the name, not full object
}
```

---

## DTO Pattern

### Common DTO Types

#### Request DTOs

Used for incoming data from clients.

```java
public class CreateUserRequest {
    @NotBlank(message = "Username is required")
    private String username;

    @Email(message = "Invalid email format")
    @NotBlank(message = "Email is required")
    private String email;

    @Size(min = 8, message = "Password must be at least 8 characters")
    private String password;

    @NotBlank(message = "Full name is required")
    private String fullName;

    // Getters and setters
}

public class UpdateUserRequest {
    @Email(message = "Invalid email format")
    private String email;  // Optional for updates

    private String fullName;  // Optional for updates

    // Only fields that can be updated
    // No id or password
}
```

#### Response DTOs

Used for outgoing data to clients.

```java
public class UserResponse {
    private Long id;
    private String username;
    private String email;
    private String fullName;
    private LocalDateTime createdAt;

    // Only safe, public data
}

public class UserDetailResponse extends UserResponse {
    private String role;
    private boolean emailVerified;
    private LocalDateTime lastLogin;
    private List<AddressDTO> addresses;
}
```

#### Nested DTOs

DTOs can contain other DTOs for complex structures.

```java
public class OrderResponse {
    private Long id;
    private LocalDateTime orderDate;
    private BigDecimal totalAmount;
    private String status;

    private UserSummaryDTO customer;  // Nested DTO
    private List<OrderItemDTO> items;  // List of nested DTOs
    private AddressDTO shippingAddress;  // Nested DTO
}

public class UserSummaryDTO {
    private Long id;
    private String name;
    // Minimal user info for nested contexts
}

public class OrderItemDTO {
    private Long productId;
    private String productName;
    private Integer quantity;
    private BigDecimal price;
}
```

### Using Records (Java 14+)

Java records provide a concise way to create immutable DTOs.

```java
// Traditional DTO
public class UserDTO {
    private Long id;
    private String username;
    private String email;

    public UserDTO() {}

    public UserDTO(Long id, String username, String email) {
        this.id = id;
        this.username = username;
        this.email = email;
    }

    // Getters, setters, equals, hashCode, toString
}

// Record (much shorter!)
public record UserDTO(
    Long id,
    String username,
    String email
) {}

// With validation
public record CreateUserRequest(
    @NotBlank String username,
    @Email String email,
    @Size(min = 8) String password
) {}
```

**Benefits of Records:**
- Immutable by default
- Automatic getters, equals, hashCode, toString
- Compact syntax
- Clear intent (data carrier)

### DTO Best Practices

#### 1. Separate Request and Response DTOs

```java
// Request DTO - for creating
public class CreateProductRequest {
    private String name;
    private BigDecimal price;
    private String description;
    // No id - it's generated
}

// Response DTO - for reading
public class ProductResponse {
    private Long id;  // Includes generated id
    private String name;
    private BigDecimal price;
    private String description;
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
}
```

#### 2. Use Validation Annotations

```java
import jakarta.validation.constraints.*;

public class CreateUserRequest {
    @NotBlank(message = "Username is required")
    @Size(min = 3, max = 50, message = "Username must be 3-50 characters")
    private String username;

    @Email(message = "Invalid email format")
    @NotBlank(message = "Email is required")
    private String email;

    @NotBlank(message = "Password is required")
    @Pattern(
        regexp = "^(?=.*[0-9])(?=.*[a-z])(?=.*[A-Z])(?=.*[@#$%^&+=]).*$",
        message = "Password must contain uppercase, lowercase, digit, and special character"
    )
    private String password;

    @Min(value = 18, message = "Must be at least 18 years old")
    private Integer age;
}
```

#### 3. Name DTOs Clearly

```java
// Good naming
CreateUserRequest
UpdateUserRequest
UserResponse
UserListResponse
UserDetailResponse

// Bad naming
UserDTO  // Too generic
User2    // Unclear purpose
NewUser  // Ambiguous
```

#### 4. Keep DTOs Flat When Possible

```java
// Bad - deep nesting makes it hard to use
public class OrderDTO {
    private CustomerDTO customer;
    // CustomerDTO contains AddressDTO
    // AddressDTO contains CountryDTO, etc.
}

// Better - flatten for simpler consumption
public class OrderDTO {
    private Long customerId;
    private String customerName;
    private String shippingAddress;
    private String shippingCity;
    private String shippingCountry;
}
```

---

## Mapping Strategies

### Manual Mapping

The simplest approach - write mapping code manually.

```java
@Service
public class UserService {

    public UserResponse createUser(CreateUserRequest request) {
        // Map request DTO to entity
        User user = new User();
        user.setUsername(request.getUsername());
        user.setEmail(request.getEmail());
        user.setPasswordHash(passwordEncoder.encode(request.getPassword()));
        user.setFullName(request.getFullName());

        // Save entity
        User saved = userRepository.save(user);

        // Map entity to response DTO
        UserResponse response = new UserResponse();
        response.setId(saved.getId());
        response.setUsername(saved.getUsername());
        response.setEmail(saved.getEmail());
        response.setFullName(saved.getFullName());
        response.setCreatedAt(saved.getCreatedAt());

        return response;
    }
}
```

**Pros:**
- Simple and explicit
- No additional dependencies
- Easy to debug
- Full control

**Cons:**
- Verbose and repetitive
- Error-prone for complex mappings
- Tedious for many fields

### Mapper Classes

Organize mapping logic in dedicated mapper classes.

```java
@Component
public class UserMapper {

    @Autowired
    private PasswordEncoder passwordEncoder;

    public User toEntity(CreateUserRequest request) {
        User user = new User();
        user.setUsername(request.getUsername());
        user.setEmail(request.getEmail());
        user.setPasswordHash(passwordEncoder.encode(request.getPassword()));
        user.setFullName(request.getFullName());
        return user;
    }

    public UserResponse toResponse(User user) {
        UserResponse response = new UserResponse();
        response.setId(user.getId());
        response.setUsername(user.getUsername());
        response.setEmail(user.getEmail());
        response.setFullName(user.getFullName());
        response.setCreatedAt(user.getCreatedAt());
        return response;
    }

    public List<UserResponse> toResponseList(List<User> users) {
        return users.stream()
            .map(this::toResponse)
            .collect(Collectors.toList());
    }

    public void updateEntity(User user, UpdateUserRequest request) {
        if (request.getEmail() != null) {
            user.setEmail(request.getEmail());
        }
        if (request.getFullName() != null) {
            user.setFullName(request.getFullName());
        }
    }
}
```

**Usage:**
```java
@Service
public class UserService {

    @Autowired
    private UserMapper userMapper;

    @Autowired
    private UserRepository userRepository;

    public UserResponse createUser(CreateUserRequest request) {
        User user = userMapper.toEntity(request);
        User saved = userRepository.save(user);
        return userMapper.toResponse(saved);
    }

    public UserResponse updateUser(Long id, UpdateUserRequest request) {
        User user = userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException(id));
        userMapper.updateEntity(user, request);
        User updated = userRepository.save(user);
        return userMapper.toResponse(updated);
    }
}
```

---

## ModelMapper

ModelMapper is a library that automatically maps objects based on conventions.

### Setup

**Maven Dependency:**
```xml
<dependency>
    <groupId>org.modelmapper</groupId>
    <artifactId>modelmapper</artifactId>
    <version>3.2.0</version>
</dependency>
```

**Configuration:**
```java
import org.modelmapper.ModelMapper;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ModelMapperConfig {

    @Bean
    public ModelMapper modelMapper() {
        return new ModelMapper();
    }
}
```

### Basic Usage

```java
@Service
public class UserService {

    @Autowired
    private ModelMapper modelMapper;

    @Autowired
    private UserRepository userRepository;

    public UserResponse createUser(CreateUserRequest request) {
        // Map DTO to entity
        User user = modelMapper.map(request, User.class);

        // Additional manual mapping if needed
        user.setPasswordHash(passwordEncoder.encode(request.getPassword()));

        User saved = userRepository.save(user);

        // Map entity to response DTO
        return modelMapper.map(saved, UserResponse.class);
    }

    public List<UserResponse> getAllUsers() {
        List<User> users = userRepository.findAll();

        // Map list
        return users.stream()
            .map(user -> modelMapper.map(user, UserResponse.class))
            .collect(Collectors.toList());
    }
}
```

### Custom Mappings

Configure custom mapping rules for complex scenarios.

```java
@Configuration
public class ModelMapperConfig {

    @Bean
    public ModelMapper modelMapper() {
        ModelMapper mapper = new ModelMapper();

        // Configure mapping from User to UserResponse
        mapper.typeMap(User.class, UserResponse.class)
            .addMapping(User::getId, UserResponse::setUserId)
            .addMapping(src -> src.getProfile().getFullName(), UserResponse::setFullName);

        // Skip certain fields
        mapper.typeMap(CreateUserRequest.class, User.class)
            .addMappings(m -> m.skip(User::setId));

        return mapper;
    }
}
```

### Pros and Cons

**Pros:**
- Reduces boilerplate code
- Convention-based mapping
- Handles nested objects
- Easy for simple mappings

**Cons:**
- Magic behavior can be confusing
- Runtime reflection has performance overhead
- Debugging is harder
- Complex mappings still need configuration

---

## MapStruct

MapStruct is a code generation tool that creates type-safe mappers at compile time.

### Setup

**Maven Dependency:**
```xml
<dependency>
    <groupId>org.mapstruct</groupId>
    <artifactId>mapstruct</artifactId>
    <version>1.5.5.Final</version>
</dependency>

<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.11.0</version>
            <configuration>
                <annotationProcessorPaths>
                    <path>
                        <groupId>org.mapstruct</groupId>
                        <artifactId>mapstruct-processor</artifactId>
                        <version>1.5.5.Final</version>
                    </path>
                    <path>
                        <groupId>org.projectlombok</groupId>
                        <artifactId>lombok</artifactId>
                        <version>1.18.30</version>
                    </path>
                    <path>
                        <groupId>org.projectlombok</groupId>
                        <artifactId>lombok-mapstruct-binding</artifactId>
                        <version>0.2.0</version>
                    </path>
                </annotationProcessorPaths>
            </configuration>
        </plugin>
    </plugins>
</build>
```

### Basic Mapper Interface

```java
import org.mapstruct.Mapper;
import org.mapstruct.MappingConstants;

@Mapper(componentModel = MappingConstants.ComponentModel.SPRING)
public interface UserMapper {

    UserResponse toResponse(User user);

    User toEntity(CreateUserRequest request);

    List<UserResponse> toResponseList(List<User> users);
}
```

MapStruct generates the implementation at compile time:

```java
// Generated code (in target/generated-sources)
@Component
public class UserMapperImpl implements UserMapper {

    @Override
    public UserResponse toResponse(User user) {
        if (user == null) {
            return null;
        }

        UserResponse response = new UserResponse();
        response.setId(user.getId());
        response.setUsername(user.getUsername());
        response.setEmail(user.getEmail());
        response.setFullName(user.getFullName());
        response.setCreatedAt(user.getCreatedAt());

        return response;
    }

    // Other methods...
}
```

**Usage:**
```java
@Service
public class UserService {

    @Autowired
    private UserMapper userMapper;  // Inject MapStruct mapper

    @Autowired
    private UserRepository userRepository;

    public UserResponse createUser(CreateUserRequest request) {
        User user = userMapper.toEntity(request);
        User saved = userRepository.save(user);
        return userMapper.toResponse(saved);
    }
}
```

### Custom Mappings

#### Field Mapping

```java
import org.mapstruct.*;

@Mapper(componentModel = MappingConstants.ComponentModel.SPRING)
public interface UserMapper {

    @Mapping(source = "fullName", target = "name")
    @Mapping(source = "email", target = "emailAddress")
    @Mapping(target = "id", ignore = true)  // Don't map id
    User toEntity(CreateUserRequest request);

    @Mapping(source = "name", target = "fullName")
    @Mapping(source = "emailAddress", target = "email")
    UserResponse toResponse(User user);
}
```

#### Nested Mapping

```java
@Mapper(componentModel = MappingConstants.ComponentModel.SPRING)
public interface OrderMapper {

    @Mapping(source = "user.id", target = "customerId")
    @Mapping(source = "user.username", target = "customerName")
    @Mapping(source = "shippingAddress.street", target = "shippingStreet")
    @Mapping(source = "shippingAddress.city", target = "shippingCity")
    OrderResponse toResponse(Order order);
}
```

#### Using Other Mappers

```java
@Mapper(
    componentModel = MappingConstants.ComponentModel.SPRING,
    uses = {UserMapper.class, ProductMapper.class}
)
public interface OrderMapper {

    @Mapping(source = "user", target = "customer")
    @Mapping(source = "items", target = "orderItems")
    OrderResponse toResponse(Order order);

    // MapStruct will use UserMapper to map user -> customer
    // and ProductMapper to map items
}
```

#### Custom Mapping Methods

```java
@Mapper(componentModel = MappingConstants.ComponentModel.SPRING)
public interface UserMapper {

    @Mapping(source = "password", target = "passwordHash", qualifiedByName = "encodePassword")
    User toEntity(CreateUserRequest request);

    @Named("encodePassword")
    default String encodePassword(String password) {
        return new BCryptPasswordEncoder().encode(password);
    }
}
```

#### Update Existing Entities

```java
@Mapper(componentModel = MappingConstants.ComponentModel.SPRING)
public interface UserMapper {

    @Mapping(target = "id", ignore = true)
    @Mapping(target = "createdAt", ignore = true)
    @BeanMapping(nullValuePropertyMappingStrategy = NullValuePropertyMappingStrategy.IGNORE)
    void updateEntity(UpdateUserRequest request, @MappingTarget User user);
}
```

**Usage:**
```java
public UserResponse updateUser(Long id, UpdateUserRequest request) {
    User user = userRepository.findById(id)
        .orElseThrow(() -> new UserNotFoundException(id));

    userMapper.updateEntity(request, user);  // Updates existing user

    User updated = userRepository.save(user);
    return userMapper.toResponse(updated);
}
```

### MapStruct vs ModelMapper

| Feature | MapStruct | ModelMapper |
|---------|-----------|-------------|
| **Approach** | Compile-time code generation | Runtime reflection |
| **Performance** | Fast (no reflection) | Slower (reflection overhead) |
| **Type Safety** | Compile-time errors | Runtime errors |
| **Debugging** | Easy (generated code) | Harder (reflection) |
| **Configuration** | Annotations on interfaces | Java code or fluent API |
| **Learning Curve** | Steeper | Gentler |
| **Flexibility** | Very flexible | Very flexible |
| **Best For** | Production applications | Quick prototypes |

### Best Practices

#### 1. Use MapStruct for Production

```java
// Recommended for production
@Mapper(componentModel = MappingConstants.ComponentModel.SPRING)
public interface UserMapper {
    UserResponse toResponse(User user);
}
```

#### 2. Organize Mappers by Domain

```java
// Separate mapper for each domain entity
UserMapper
ProductMapper
OrderMapper
CategoryMapper
```

#### 3. Test Your Mappers

```java
@SpringBootTest
class UserMapperTest {

    @Autowired
    private UserMapper userMapper;

    @Test
    void shouldMapUserToResponse() {
        User user = new User();
        user.setId(1L);
        user.setUsername("john");
        user.setEmail("john@example.com");

        UserResponse response = userMapper.toResponse(user);

        assertThat(response.getId()).isEqualTo(1L);
        assertThat(response.getUsername()).isEqualTo("john");
        assertThat(response.getEmail()).isEqualTo("john@example.com");
    }
}
```

---

## Summary

DTOs and mapping are essential for building clean REST APIs:

**Key Concepts:**

1. **DTOs** decouple API contracts from domain models
2. **Security**: Don't expose sensitive or internal data
3. **Customization**: Different DTOs for different use cases
4. **Mapping Strategies**:
   - Manual: Simple, explicit, verbose
   - ModelMapper: Quick, convention-based, runtime overhead
   - MapStruct: Fast, type-safe, compile-time generation (recommended)

**Best Practices:**

- Separate request and response DTOs
- Use validation annotations on request DTOs
- Name DTOs clearly (CreateUserRequest, UserResponse)
- Keep DTOs simple and focused
- Use MapStruct for production applications
- Test your mappers

**Next Steps:**

With DTOs and mapping in place, we'll explore implementing CRUD operations with proper request/response handling in the next topic.
