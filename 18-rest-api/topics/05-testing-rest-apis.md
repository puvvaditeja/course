# Testing REST APIs

## Overview

Testing is crucial for ensuring REST APIs work correctly and handle edge cases properly. This topic covers testing strategies using Spring Boot Test, MockMvc, and @WebMvcTest for comprehensive API testing.

---

## Testing Strategy

### Testing Pyramid

REST API testing typically follows a testing pyramid:

```
       /\
      /  \     E2E Tests (few)
     /----\    Integration Tests (some)
    /------\   Unit Tests (many)
   /--------\
```

**Test Types:**

1. **Unit Tests**: Test individual components (services, mappers) in isolation
2. **Integration Tests**: Test controllers with Spring context
3. **End-to-End Tests**: Test complete API flows with real database

### What to Test

**Controller Tests:**
- HTTP method routing
- Request parameter binding
- Request body deserialization
- Response serialization
- HTTP status codes
- Response headers
- Error handling
- Validation

**Service Tests:**
- Business logic
- Data validation
- Exception handling
- Transaction behavior

---

## Spring Boot Test Dependencies

### Maven Dependencies

```xml
<dependencies>
    <!-- Spring Boot Test Starter -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>

    <!-- REST Assured (optional - for more readable tests) -->
    <dependency>
        <groupId>io.rest-assured</groupId>
        <artifactId>rest-assured</artifactId>
        <scope>test</scope>
    </dependency>

    <!-- H2 Database for testing -->
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### Test Dependencies Included

Spring Boot Starter Test includes:

- **JUnit 5**: Testing framework
- **Spring Test**: Spring testing support
- **AssertJ**: Fluent assertion library
- **Hamcrest**: Matcher library
- **Mockito**: Mocking framework
- **JSONassert**: JSON assertion library
- **JsonPath**: JSON path expressions

---

## MockMvc

MockMvc is a powerful tool for testing Spring MVC controllers without starting a full HTTP server.

### What is MockMvc?

MockMvc allows you to:
- Send HTTP requests to controllers
- Verify responses (status, headers, body)
- Test request/response processing
- Perform tests quickly (no server startup)

### Basic Setup

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.web.servlet.MockMvc;

@SpringBootTest
@AutoConfigureMockMvc
class ProductControllerIntegrationTest {

    @Autowired
    private MockMvc mockMvc;

    // Tests go here
}
```

### MockMvc Methods

```java
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;
import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.*;

// Perform GET request
mockMvc.perform(get("/api/products"))
    .andExpect(status().isOk())
    .andDo(print());

// Perform POST request
mockMvc.perform(post("/api/products")
    .contentType(MediaType.APPLICATION_JSON)
    .content("{\"name\":\"Test Product\"}"))
    .andExpect(status().isCreated());

// Perform PUT request
mockMvc.perform(put("/api/products/1")
    .contentType(MediaType.APPLICATION_JSON)
    .content("{\"name\":\"Updated Product\"}"))
    .andExpect(status().isOk());

// Perform PATCH request
mockMvc.perform(patch("/api/products/1")
    .contentType(MediaType.APPLICATION_JSON)
    .content("{\"price\":99.99}"))
    .andExpect(status().isOk());

// Perform DELETE request
mockMvc.perform(delete("/api/products/1"))
    .andExpect(status().isNoContent());
```

---

## @WebMvcTest

@WebMvcTest is a specialized test annotation for testing only the web layer (controllers).

### Key Features

- **Focused Testing**: Only loads web layer components
- **Fast**: Doesn't load full application context
- **Auto-Configuration**: Automatically configures MockMvc
- **Mocking**: Requires mocking service layer dependencies

### Basic Example

```java
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;

@WebMvcTest(ProductController.class)
class ProductControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private ProductService productService;

    @MockBean
    private ProductMapper productMapper;

    // Tests go here
}
```

### @WebMvcTest vs @SpringBootTest

| Feature | @WebMvcTest | @SpringBootTest |
|---------|-------------|-----------------|
| **Scope** | Web layer only | Full application context |
| **Speed** | Fast | Slower |
| **Components Loaded** | Controllers, filters, advice | All beans |
| **Service Layer** | Must mock | Can be real or mocked |
| **Database** | Not available | Can use real or embedded |
| **Use Case** | Controller unit tests | Integration tests |

---

## Testing CRUD Operations

### Test CREATE (POST)

```java
import com.fasterxml.jackson.databind.ObjectMapper;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;

import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.when;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;
import static org.hamcrest.Matchers.*;

@WebMvcTest(ProductController.class)
class ProductControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private ObjectMapper objectMapper;

    @MockBean
    private ProductService productService;

    @Test
    void createProduct_ShouldReturnCreated() throws Exception {
        // Given
        CreateProductRequest request = new CreateProductRequest();
        request.setName("Test Product");
        request.setPrice(new BigDecimal("99.99"));
        request.setStockQuantity(10);
        request.setCategory("Electronics");

        ProductResponse response = new ProductResponse();
        response.setId(1L);
        response.setName("Test Product");
        response.setPrice(new BigDecimal("99.99"));
        response.setStockQuantity(10);
        response.setCategory("Electronics");

        when(productService.createProduct(any(CreateProductRequest.class)))
            .thenReturn(response);

        // When & Then
        mockMvc.perform(post("/api/products")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request)))
            .andExpect(status().isCreated())
            .andExpect(header().exists("Location"))
            .andExpect(header().string("Location", containsString("/api/products/1")))
            .andExpect(jsonPath("$.id").value(1))
            .andExpect(jsonPath("$.name").value("Test Product"))
            .andExpect(jsonPath("$.price").value(99.99))
            .andExpect(jsonPath("$.category").value("Electronics"));
    }

    @Test
    void createProduct_WithInvalidData_ShouldReturnBadRequest() throws Exception {
        // Given - invalid request (empty name)
        CreateProductRequest request = new CreateProductRequest();
        request.setName("");  // Invalid
        request.setPrice(new BigDecimal("99.99"));

        // When & Then
        mockMvc.perform(post("/api/products")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request)))
            .andExpect(status().isBadRequest())
            .andExpect(jsonPath("$.errors.name").exists());
    }

    @Test
    void createProduct_WithDuplicateName_ShouldReturnConflict() throws Exception {
        // Given
        CreateProductRequest request = new CreateProductRequest();
        request.setName("Existing Product");
        request.setPrice(new BigDecimal("99.99"));
        request.setStockQuantity(10);
        request.setCategory("Electronics");

        when(productService.createProduct(any(CreateProductRequest.class)))
            .thenThrow(new DuplicateResourceException("Product already exists"));

        // When & Then
        mockMvc.perform(post("/api/products")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request)))
            .andExpect(status().isConflict())
            .andExpect(jsonPath("$.message").value("Product already exists"));
    }
}
```

### Test READ (GET)

```java
@Test
void getAllProducts_ShouldReturnProductList() throws Exception {
    // Given
    List<ProductListResponse> products = Arrays.asList(
        new ProductListResponse(1L, "Product 1", new BigDecimal("99.99"), "Electronics", true),
        new ProductListResponse(2L, "Product 2", new BigDecimal("149.99"), "Books", true)
    );

    Page<ProductListResponse> page = new PageImpl<>(products);

    when(productService.getAllProducts(any(Pageable.class)))
        .thenReturn(page);

    // When & Then
    mockMvc.perform(get("/api/products"))
        .andExpect(status().isOk())
        .andExpect(jsonPath("$.content", hasSize(2)))
        .andExpect(jsonPath("$.content[0].id").value(1))
        .andExpect(jsonPath("$.content[0].name").value("Product 1"))
        .andExpect(jsonPath("$.content[1].id").value(2))
        .andExpect(jsonPath("$.totalElements").value(2));
}

@Test
void getProductById_WhenExists_ShouldReturnProduct() throws Exception {
    // Given
    ProductResponse product = new ProductResponse();
    product.setId(1L);
    product.setName("Test Product");
    product.setPrice(new BigDecimal("99.99"));

    when(productService.getProductById(1L))
        .thenReturn(product);

    // When & Then
    mockMvc.perform(get("/api/products/{id}", 1))
        .andExpect(status().isOk())
        .andExpect(jsonPath("$.id").value(1))
        .andExpect(jsonPath("$.name").value("Test Product"))
        .andExpect(jsonPath("$.price").value(99.99));
}

@Test
void getProductById_WhenNotExists_ShouldReturnNotFound() throws Exception {
    // Given
    when(productService.getProductById(999L))
        .thenThrow(new ResourceNotFoundException("Product not found"));

    // When & Then
    mockMvc.perform(get("/api/products/{id}", 999))
        .andExpect(status().isNotFound())
        .andExpect(jsonPath("$.message").value("Product not found"));
}

@Test
void getProductsByCategory_ShouldReturnFilteredList() throws Exception {
    // Given
    List<ProductListResponse> products = Arrays.asList(
        new ProductListResponse(1L, "Laptop", new BigDecimal("999.99"), "Electronics", true)
    );

    when(productService.getProductsByCategory("Electronics"))
        .thenReturn(products);

    // When & Then
    mockMvc.perform(get("/api/products")
            .param("category", "Electronics"))
        .andExpect(status().isOk())
        .andExpect(jsonPath("$", hasSize(1)))
        .andExpect(jsonPath("$[0].category").value("Electronics"));
}
```

### Test UPDATE (PUT/PATCH)

```java
@Test
void updateProduct_WhenExists_ShouldReturnUpdated() throws Exception {
    // Given
    UpdateProductRequest request = new UpdateProductRequest();
    request.setPrice(new BigDecimal("149.99"));

    ProductResponse response = new ProductResponse();
    response.setId(1L);
    response.setName("Test Product");
    response.setPrice(new BigDecimal("149.99"));

    when(productService.updateProduct(eq(1L), any(UpdateProductRequest.class)))
        .thenReturn(response);

    // When & Then
    mockMvc.perform(patch("/api/products/{id}", 1)
            .contentType(MediaType.APPLICATION_JSON)
            .content(objectMapper.writeValueAsString(request)))
        .andExpect(status().isOk())
        .andExpect(jsonPath("$.id").value(1))
        .andExpect(jsonPath("$.price").value(149.99));
}

@Test
void updateProduct_WhenNotExists_ShouldReturnNotFound() throws Exception {
    // Given
    UpdateProductRequest request = new UpdateProductRequest();
    request.setPrice(new BigDecimal("149.99"));

    when(productService.updateProduct(eq(999L), any(UpdateProductRequest.class)))
        .thenThrow(new ResourceNotFoundException("Product not found"));

    // When & Then
    mockMvc.perform(patch("/api/products/{id}", 999)
            .contentType(MediaType.APPLICATION_JSON)
            .content(objectMapper.writeValueAsString(request)))
        .andExpect(status().isNotFound());
}

@Test
void replaceProduct_ShouldReturnUpdated() throws Exception {
    // Given
    CreateProductRequest request = new CreateProductRequest();
    request.setName("Updated Product");
    request.setPrice(new BigDecimal("199.99"));
    request.setStockQuantity(20);
    request.setCategory("Electronics");

    ProductResponse response = new ProductResponse();
    response.setId(1L);
    response.setName("Updated Product");
    response.setPrice(new BigDecimal("199.99"));

    when(productService.replaceProduct(eq(1L), any(CreateProductRequest.class)))
        .thenReturn(response);

    // When & Then
    mockMvc.perform(put("/api/products/{id}", 1)
            .contentType(MediaType.APPLICATION_JSON)
            .content(objectMapper.writeValueAsString(request)))
        .andExpect(status().isOk())
        .andExpect(jsonPath("$.name").value("Updated Product"))
        .andExpect(jsonPath("$.price").value(199.99));
}
```

### Test DELETE

```java
@Test
void deleteProduct_WhenExists_ShouldReturnNoContent() throws Exception {
    // Given
    // productService.deleteProduct(1L) returns void - no mocking needed

    // When & Then
    mockMvc.perform(delete("/api/products/{id}", 1))
        .andExpect(status().isNoContent());

    verify(productService).deleteProduct(1L);
}

@Test
void deleteProduct_WhenNotExists_ShouldReturnNotFound() throws Exception {
    // Given
    doThrow(new ResourceNotFoundException("Product not found"))
        .when(productService).deleteProduct(999L);

    // When & Then
    mockMvc.perform(delete("/api/products/{id}", 999))
        .andExpect(status().isNotFound());
}
```

---

## Testing with Real Database

### Integration Test with TestContainers

```java
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.autoconfigure.jdbc.AutoConfigureTestDatabase;
import org.springframework.test.context.DynamicPropertyRegistry;
import org.springframework.test.context.DynamicPropertySource;
import org.testcontainers.containers.PostgreSQLContainer;
import org.testcontainers.junit.jupiter.Container;
import org.testcontainers.junit.jupiter.Testcontainers;

@SpringBootTest
@AutoConfigureMockMvc
@Testcontainers
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
class ProductControllerIntegrationTest {

    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:15")
        .withDatabaseName("testdb")
        .withUsername("test")
        .withPassword("test");

    @DynamicPropertySource
    static void configureProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", postgres::getJdbcUrl);
        registry.add("spring.datasource.username", postgres::getUsername);
        registry.add("spring.datasource.password", postgres::getPassword);
    }

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private ProductRepository productRepository;

    @BeforeEach
    void setUp() {
        productRepository.deleteAll();
    }

    @Test
    void createAndRetrieveProduct() throws Exception {
        // Create product
        String createRequest = """
            {
                "name": "Test Product",
                "price": 99.99,
                "stockQuantity": 10,
                "category": "Electronics"
            }
            """;

        String response = mockMvc.perform(post("/api/products")
                .contentType(MediaType.APPLICATION_JSON)
                .content(createRequest))
            .andExpect(status().isCreated())
            .andReturn()
            .getResponse()
            .getContentAsString();

        // Extract ID from response
        JsonNode jsonNode = new ObjectMapper().readTree(response);
        Long id = jsonNode.get("id").asLong();

        // Retrieve product
        mockMvc.perform(get("/api/products/{id}", id))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.id").value(id))
            .andExpect(jsonPath("$.name").value("Test Product"));
    }
}
```

### Integration Test with H2

```java
@SpringBootTest
@AutoConfigureMockMvc
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.ANY)
class ProductControllerH2Test {

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private ProductRepository productRepository;

    @BeforeEach
    void setUp() {
        productRepository.deleteAll();
    }

    @Test
    void fullCrudFlow() throws Exception {
        // CREATE
        String createRequest = """
            {
                "name": "Test Product",
                "price": 99.99,
                "stockQuantity": 10,
                "category": "Electronics"
            }
            """;

        String createResponse = mockMvc.perform(post("/api/products")
                .contentType(MediaType.APPLICATION_JSON)
                .content(createRequest))
            .andExpect(status().isCreated())
            .andReturn()
            .getResponse()
            .getContentAsString();

        Long id = new ObjectMapper().readTree(createResponse).get("id").asLong();

        // READ
        mockMvc.perform(get("/api/products/{id}", id))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.name").value("Test Product"));

        // UPDATE
        String updateRequest = """
            {
                "price": 149.99
            }
            """;

        mockMvc.perform(patch("/api/products/{id}", id)
                .contentType(MediaType.APPLICATION_JSON)
                .content(updateRequest))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.price").value(149.99));

        // DELETE
        mockMvc.perform(delete("/api/products/{id}", id))
            .andExpect(status().isNoContent());

        // Verify deletion
        mockMvc.perform(get("/api/products/{id}", id))
            .andExpect(status().isNotFound());
    }
}
```

---

## Advanced Testing Techniques

### Testing Pagination

```java
@Test
void getAllProducts_WithPagination_ShouldReturnCorrectPage() throws Exception {
    mockMvc.perform(get("/api/products")
            .param("page", "0")
            .param("size", "10")
            .param("sort", "name,asc"))
        .andExpect(status().isOk())
        .andExpect(jsonPath("$.size").value(10))
        .andExpect(jsonPath("$.number").value(0))
        .andExpect(jsonPath("$.sort.sorted").value(true));
}
```

### Testing Headers

```java
@Test
void createProduct_ShouldSetLocationHeader() throws Exception {
    ProductResponse response = new ProductResponse();
    response.setId(1L);

    when(productService.createProduct(any()))
        .thenReturn(response);

    mockMvc.perform(post("/api/products")
            .contentType(MediaType.APPLICATION_JSON)
            .content("{}"))
        .andExpect(status().isCreated())
        .andExpect(header().exists("Location"))
        .andExpect(header().string("Location", endsWith("/api/products/1")));
}
```

### Testing CORS

```java
@Test
void preflightRequest_ShouldAllowCors() throws Exception {
    mockMvc.perform(options("/api/products")
            .header("Access-Control-Request-Method", "POST")
            .header("Origin", "http://localhost:4200"))
        .andExpect(status().isOk())
        .andExpect(header().exists("Access-Control-Allow-Origin"))
        .andExpect(header().exists("Access-Control-Allow-Methods"));
}
```

### Testing Content Negotiation

```java
@Test
void getProduct_WithJsonAccept_ShouldReturnJson() throws Exception {
    ProductResponse product = new ProductResponse();
    product.setId(1L);

    when(productService.getProductById(1L))
        .thenReturn(product);

    mockMvc.perform(get("/api/products/1")
            .accept(MediaType.APPLICATION_JSON))
        .andExpect(status().isOk())
        .andExpect(content().contentType(MediaType.APPLICATION_JSON));
}
```

### Testing Security

```java
import org.springframework.security.test.context.support.WithMockUser;

@Test
@WithMockUser(roles = "ADMIN")
void deleteProduct_AsAdmin_ShouldSucceed() throws Exception {
    mockMvc.perform(delete("/api/products/1"))
        .andExpect(status().isNoContent());
}

@Test
@WithMockUser(roles = "USER")
void deleteProduct_AsUser_ShouldFail() throws Exception {
    mockMvc.perform(delete("/api/products/1"))
        .andExpect(status().isForbidden());
}

@Test
void deleteProduct_Unauthenticated_ShouldFail() throws Exception {
    mockMvc.perform(delete("/api/products/1"))
        .andExpect(status().isUnauthorized());
}
```

---

## Test Organization

### Nested Tests

Organize related tests using `@Nested`:

```java
@WebMvcTest(ProductController.class)
class ProductControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private ProductService productService;

    @Nested
    @DisplayName("POST /api/products")
    class CreateProduct {

        @Test
        @DisplayName("Should create product successfully")
        void shouldCreateProduct() throws Exception {
            // Test implementation
        }

        @Test
        @DisplayName("Should return 400 for invalid data")
        void shouldReturnBadRequestForInvalidData() throws Exception {
            // Test implementation
        }

        @Test
        @DisplayName("Should return 409 for duplicate name")
        void shouldReturnConflictForDuplicate() throws Exception {
            // Test implementation
        }
    }

    @Nested
    @DisplayName("GET /api/products/{id}")
    class GetProduct {

        @Test
        @DisplayName("Should return product when exists")
        void shouldReturnProductWhenExists() throws Exception {
            // Test implementation
        }

        @Test
        @DisplayName("Should return 404 when not exists")
        void shouldReturn404WhenNotExists() throws Exception {
            // Test implementation
        }
    }
}
```

### Parameterized Tests

Test multiple scenarios with `@ParameterizedTest`:

```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.ValueSource;
import org.junit.jupiter.params.provider.CsvSource;

@ParameterizedTest
@ValueSource(strings = {"", "  ", "ab"})
void createProduct_WithInvalidName_ShouldReturnBadRequest(String name) throws Exception {
    CreateProductRequest request = new CreateProductRequest();
    request.setName(name);

    mockMvc.perform(post("/api/products")
            .contentType(MediaType.APPLICATION_JSON)
            .content(objectMapper.writeValueAsString(request)))
        .andExpect(status().isBadRequest());
}

@ParameterizedTest
@CsvSource({
    "-1, Price must be positive",
    "0, Price must be positive",
    "999999999, Invalid price format"
})
void createProduct_WithInvalidPrice_ShouldReturnBadRequest(
        String price, String expectedMessage) throws Exception {
    // Test implementation
}
```

---

## Best Practices

### 1. Use Descriptive Test Names

```java
// Good
@Test
void getProductById_WhenProductExists_ShouldReturnProductWith200() { }

// Bad
@Test
void test1() { }
```

### 2. Follow AAA Pattern

```java
@Test
void testName() {
    // Arrange - Set up test data and mocks
    Product product = new Product();
    when(productService.getProductById(1L)).thenReturn(product);

    // Act - Perform the action
    mockMvc.perform(get("/api/products/1"));

    // Assert - Verify the results
    .andExpect(status().isOk());
}
```

### 3. Test Edge Cases

```java
@Test
void getAllProducts_WhenEmpty_ShouldReturnEmptyList() { }

@Test
void getProductById_WithNegativeId_ShouldReturnBadRequest() { }

@Test
void createProduct_WithNullValues_ShouldReturnBadRequest() { }
```

### 4. Use Test Fixtures

```java
class ProductControllerTest {

    private CreateProductRequest validRequest;
    private ProductResponse validResponse;

    @BeforeEach
    void setUp() {
        validRequest = new CreateProductRequest();
        validRequest.setName("Test Product");
        validRequest.setPrice(new BigDecimal("99.99"));

        validResponse = new ProductResponse();
        validResponse.setId(1L);
        validResponse.setName("Test Product");
    }

    @Test
    void createProduct_ShouldReturnCreated() throws Exception {
        when(productService.createProduct(any()))
            .thenReturn(validResponse);
        // Test with validRequest
    }
}
```

### 5. Verify Interactions

```java
@Test
void deleteProduct_ShouldCallServiceDelete() throws Exception {
    mockMvc.perform(delete("/api/products/1"))
        .andExpect(status().isNoContent());

    verify(productService).deleteProduct(1L);
    verify(productService, times(1)).deleteProduct(1L);
}
```

---

## Summary

Testing REST APIs ensures reliability and correctness:

**Key Tools:**

1. **MockMvc**: Test controllers without HTTP server
2. **@WebMvcTest**: Fast controller unit tests
3. **@SpringBootTest**: Full integration tests
4. **Mockito**: Mock dependencies
5. **AssertJ/Hamcrest**: Fluent assertions

**What to Test:**

- HTTP methods and routing
- Request/response serialization
- Status codes
- Validation
- Error handling
- Security
- Pagination and filtering

**Best Practices:**

- Use descriptive test names
- Follow AAA pattern (Arrange, Act, Assert)
- Test both happy and error paths
- Use @WebMvcTest for controller tests
- Use @SpringBootTest for integration tests
- Organize tests with @Nested
- Verify service interactions

**Next Steps:**

In the next topic, we'll explore API documentation using SpringDoc and OpenAPI specifications.
