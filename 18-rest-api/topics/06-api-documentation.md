# API Documentation

## Overview

Good API documentation is essential for API adoption and usability. This topic covers using SpringDoc to automatically generate OpenAPI documentation and best practices for documenting REST APIs.

---

## Why Document APIs?

### Benefits of API Documentation

**For API Consumers:**
- Understand available endpoints and operations
- Learn request/response formats
- Discover error codes and handling
- Try API calls interactively
- Reduce integration time

**For API Providers:**
- Reduce support burden
- Improve API adoption
- Maintain consistency
- Enable API testing
- Support versioning

### Documentation Requirements

Good API documentation should include:

1. **Endpoint Information**: URL, HTTP method, description
2. **Request Details**: Parameters, headers, body format
3. **Response Details**: Status codes, body format, examples
4. **Authentication**: Required credentials and methods
5. **Error Handling**: Error codes and messages
6. **Examples**: Real request/response samples
7. **Try-It-Out**: Interactive testing capability

---

## OpenAPI Specification

### What is OpenAPI?

OpenAPI (formerly Swagger) is a standard specification for describing REST APIs. It provides:

- **Machine-readable format**: JSON or YAML
- **Language-agnostic**: Works with any programming language
- **Tool support**: Code generation, testing, documentation
- **Industry standard**: Widely adopted

### OpenAPI Structure

```yaml
openapi: 3.0.0
info:
  title: Product API
  version: 1.0.0
  description: API for managing products

servers:
  - url: http://localhost:8080
    description: Development server

paths:
  /api/products:
    get:
      summary: Get all products
      responses:
        '200':
          description: Successful response
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Product'

    post:
      summary: Create a product
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateProductRequest'
      responses:
        '201':
          description: Product created

components:
  schemas:
    Product:
      type: object
      properties:
        id:
          type: integer
        name:
          type: string
        price:
          type: number
```

---

## SpringDoc OpenAPI

SpringDoc is a library that automatically generates OpenAPI documentation from Spring Boot applications.

### Setup

#### Maven Dependency

```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.3.0</version>
</dependency>
```

#### Gradle Dependency

```gradle
implementation 'org.springdoc:springdoc-openapi-starter-webmvc-ui:2.3.0'
```

### Auto-Configuration

SpringDoc auto-configures with Spring Boot. Once added, documentation is available at:

- **Swagger UI**: `http://localhost:8080/swagger-ui.html`
- **OpenAPI JSON**: `http://localhost:8080/v3/api-docs`
- **OpenAPI YAML**: `http://localhost:8080/v3/api-docs.yaml`

### Basic Configuration

```yaml
# application.yml
springdoc:
  api-docs:
    path: /v3/api-docs
    enabled: true
  swagger-ui:
    path: /swagger-ui.html
    enabled: true
    try-it-out-enabled: true
    operations-sorter: method
    tags-sorter: alpha
  show-actuator: false
```

---

## Documenting with Annotations

### API Information

Configure general API information:

```java
import io.swagger.v3.oas.annotations.OpenAPIDefinition;
import io.swagger.v3.oas.annotations.info.Info;
import io.swagger.v3.oas.annotations.info.Contact;
import io.swagger.v3.oas.annotations.info.License;
import io.swagger.v3.oas.annotations.servers.Server;

@SpringBootApplication
@OpenAPIDefinition(
    info = @Info(
        title = "Product Management API",
        version = "1.0.0",
        description = "REST API for managing products in an e-commerce system",
        contact = @Contact(
            name = "API Support",
            email = "support@example.com",
            url = "https://example.com/support"
        ),
        license = @License(
            name = "Apache 2.0",
            url = "https://www.apache.org/licenses/LICENSE-2.0.html"
        )
    ),
    servers = {
        @Server(
            url = "http://localhost:8080",
            description = "Development Server"
        ),
        @Server(
            url = "https://api.example.com",
            description = "Production Server"
        )
    }
)
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

### Controller Documentation

```java
import io.swagger.v3.oas.annotations.Operation;
import io.swagger.v3.oas.annotations.Parameter;
import io.swagger.v3.oas.annotations.responses.ApiResponse;
import io.swagger.v3.oas.annotations.responses.ApiResponses;
import io.swagger.v3.oas.annotations.tags.Tag;
import io.swagger.v3.oas.annotations.media.Content;
import io.swagger.v3.oas.annotations.media.Schema;

@RestController
@RequestMapping("/api/products")
@Tag(name = "Products", description = "Product management endpoints")
public class ProductController {

    @Operation(
        summary = "Get all products",
        description = "Retrieves a paginated list of all products. Supports filtering by category and searching by name."
    )
    @ApiResponses(value = {
        @ApiResponse(
            responseCode = "200",
            description = "Successfully retrieved list",
            content = @Content(
                mediaType = "application/json",
                schema = @Schema(implementation = Page.class)
            )
        )
    })
    @GetMapping
    public ResponseEntity<Page<ProductListResponse>> getAllProducts(
            @Parameter(description = "Page number (0-based)")
            @RequestParam(defaultValue = "0") int page,

            @Parameter(description = "Number of items per page")
            @RequestParam(defaultValue = "20") int size,

            @Parameter(description = "Sort field and direction (e.g., 'name,asc')")
            @RequestParam(defaultValue = "id") String sort) {

        // Implementation
    }

    @Operation(
        summary = "Get product by ID",
        description = "Retrieves a single product by its unique identifier"
    )
    @ApiResponses(value = {
        @ApiResponse(
            responseCode = "200",
            description = "Product found",
            content = @Content(
                mediaType = "application/json",
                schema = @Schema(implementation = ProductResponse.class)
            )
        ),
        @ApiResponse(
            responseCode = "404",
            description = "Product not found",
            content = @Content(
                mediaType = "application/json",
                schema = @Schema(implementation = ErrorResponse.class)
            )
        )
    })
    @GetMapping("/{id}")
    public ResponseEntity<ProductResponse> getProduct(
            @Parameter(description = "Product ID", required = true)
            @PathVariable Long id) {

        // Implementation
    }

    @Operation(
        summary = "Create a new product",
        description = "Creates a new product with the provided information"
    )
    @ApiResponses(value = {
        @ApiResponse(
            responseCode = "201",
            description = "Product created successfully",
            content = @Content(
                mediaType = "application/json",
                schema = @Schema(implementation = ProductResponse.class)
            )
        ),
        @ApiResponse(
            responseCode = "400",
            description = "Invalid input",
            content = @Content(
                mediaType = "application/json",
                schema = @Schema(implementation = ValidationErrorResponse.class)
            )
        ),
        @ApiResponse(
            responseCode = "409",
            description = "Product already exists",
            content = @Content(
                mediaType = "application/json",
                schema = @Schema(implementation = ErrorResponse.class)
            )
        )
    })
    @PostMapping
    public ResponseEntity<ProductResponse> createProduct(
            @io.swagger.v3.oas.annotations.parameters.RequestBody(
                description = "Product creation request",
                required = true,
                content = @Content(
                    schema = @Schema(implementation = CreateProductRequest.class)
                )
            )
            @Valid @RequestBody CreateProductRequest request) {

        // Implementation
    }

    @Operation(
        summary = "Update a product",
        description = "Partially updates an existing product"
    )
    @ApiResponses(value = {
        @ApiResponse(
            responseCode = "200",
            description = "Product updated successfully"
        ),
        @ApiResponse(
            responseCode = "404",
            description = "Product not found"
        ),
        @ApiResponse(
            responseCode = "400",
            description = "Invalid input"
        )
    })
    @PatchMapping("/{id}")
    public ResponseEntity<ProductResponse> updateProduct(
            @PathVariable Long id,
            @Valid @RequestBody UpdateProductRequest request) {

        // Implementation
    }

    @Operation(
        summary = "Delete a product",
        description = "Permanently deletes a product by ID"
    )
    @ApiResponses(value = {
        @ApiResponse(
            responseCode = "204",
            description = "Product deleted successfully"
        ),
        @ApiResponse(
            responseCode = "404",
            description = "Product not found"
        )
    })
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteProduct(@PathVariable Long id) {
        // Implementation
    }
}
```

### Schema Documentation

Document DTOs and models:

```java
import io.swagger.v3.oas.annotations.media.Schema;

@Schema(description = "Request object for creating a new product")
public class CreateProductRequest {

    @Schema(
        description = "Product name",
        example = "Laptop Pro 15",
        required = true,
        minLength = 3,
        maxLength = 100
    )
    @NotBlank
    private String name;

    @Schema(
        description = "Product description",
        example = "High-performance laptop with 16GB RAM and 512GB SSD",
        maxLength = 500
    )
    private String description;

    @Schema(
        description = "Product price in USD",
        example = "1299.99",
        required = true,
        minimum = "0.01"
    )
    @NotNull
    @DecimalMin("0.01")
    private BigDecimal price;

    @Schema(
        description = "Available stock quantity",
        example = "50",
        required = true,
        minimum = "0"
    )
    @NotNull
    @Min(0)
    private Integer stockQuantity;

    @Schema(
        description = "Product category",
        example = "Electronics",
        required = true,
        allowableValues = {"Electronics", "Books", "Clothing", "Home & Garden"}
    )
    @NotBlank
    private String category;

    // Getters and setters
}

@Schema(description = "Product response object")
public class ProductResponse {

    @Schema(description = "Unique product identifier", example = "1")
    private Long id;

    @Schema(description = "Product name", example = "Laptop Pro 15")
    private String name;

    @Schema(description = "Product description")
    private String description;

    @Schema(description = "Product price", example = "1299.99")
    private BigDecimal price;

    @Schema(description = "Available stock", example = "50")
    private Integer stockQuantity;

    @Schema(description = "Product category", example = "Electronics")
    private String category;

    @Schema(description = "Whether product is active", example = "true")
    private Boolean active;

    @Schema(description = "Product creation timestamp", example = "2023-12-12T10:30:00")
    private LocalDateTime createdAt;

    @Schema(description = "Last update timestamp", example = "2023-12-12T15:45:00")
    private LocalDateTime updatedAt;

    // Getters and setters
}
```

### Enum Documentation

```java
@Schema(description = "Product category enumeration")
public enum ProductCategory {
    @Schema(description = "Electronic devices and accessories")
    ELECTRONICS,

    @Schema(description = "Books and publications")
    BOOKS,

    @Schema(description = "Clothing and apparel")
    CLOTHING,

    @Schema(description = "Home and garden items")
    HOME_GARDEN
}
```

---

## Advanced Documentation

### Security Documentation

Document authentication requirements:

```java
import io.swagger.v3.oas.annotations.security.SecurityRequirement;
import io.swagger.v3.oas.annotations.enums.SecuritySchemeType;
import io.swagger.v3.oas.annotations.security.SecurityScheme;

@Configuration
@SecurityScheme(
    name = "bearerAuth",
    type = SecuritySchemeType.HTTP,
    scheme = "bearer",
    bearerFormat = "JWT",
    description = "JWT token authentication"
)
public class OpenApiConfig {
}

// Use in controllers
@RestController
@RequestMapping("/api/products")
@SecurityRequirement(name = "bearerAuth")
public class ProductController {
    // Endpoints require authentication
}

// Or on specific endpoints
@PostMapping
@SecurityRequirement(name = "bearerAuth")
public ResponseEntity<ProductResponse> createProduct(...) {
    // Only this endpoint requires authentication
}
```

### Grouping Endpoints

```java
@RestController
@RequestMapping("/api/products")
@Tag(name = "Products", description = "Product management operations")
public class ProductController {
}

@RestController
@RequestMapping("/api/categories")
@Tag(name = "Categories", description = "Product category operations")
public class CategoryController {
}

@RestController
@RequestMapping("/api/orders")
@Tag(name = "Orders", description = "Order management operations")
public class OrderController {
}
```

### Hidden Endpoints

Hide internal endpoints from documentation:

```java
import io.swagger.v3.oas.annotations.Hidden;

@Hidden
@GetMapping("/internal/health")
public ResponseEntity<String> healthCheck() {
    return ResponseEntity.ok("OK");
}

// Or hide entire controller
@Hidden
@RestController
@RequestMapping("/internal")
public class InternalController {
}
```

### Example Responses

Provide example responses:

```java
import io.swagger.v3.oas.annotations.media.ExampleObject;

@Operation(summary = "Get product by ID")
@ApiResponse(
    responseCode = "200",
    description = "Product found",
    content = @Content(
        mediaType = "application/json",
        schema = @Schema(implementation = ProductResponse.class),
        examples = @ExampleObject(
            name = "Laptop Example",
            value = """
                {
                    "id": 1,
                    "name": "Laptop Pro 15",
                    "description": "High-performance laptop",
                    "price": 1299.99,
                    "stockQuantity": 50,
                    "category": "Electronics",
                    "active": true,
                    "createdAt": "2023-12-12T10:30:00",
                    "updatedAt": "2023-12-12T10:30:00"
                }
                """
        )
    )
)
@GetMapping("/{id}")
public ResponseEntity<ProductResponse> getProduct(@PathVariable Long id) {
    // Implementation
}
```

### Custom OpenAPI Configuration

```java
import io.swagger.v3.oas.models.OpenAPI;
import io.swagger.v3.oas.models.info.Info;
import io.swagger.v3.oas.models.info.Contact;
import io.swagger.v3.oas.models.info.License;
import io.swagger.v3.oas.models.servers.Server;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.List;

@Configuration
public class OpenApiConfig {

    @Bean
    public OpenAPI customOpenAPI() {
        return new OpenAPI()
            .info(new Info()
                .title("Product Management API")
                .version("1.0.0")
                .description("Comprehensive API for managing products")
                .contact(new Contact()
                    .name("Development Team")
                    .email("dev@example.com")
                    .url("https://example.com"))
                .license(new License()
                    .name("Apache 2.0")
                    .url("https://www.apache.org/licenses/LICENSE-2.0.html")))
            .servers(List.of(
                new Server()
                    .url("http://localhost:8080")
                    .description("Development Server"),
                new Server()
                    .url("https://api.example.com")
                    .description("Production Server")
            ));
    }

    @Bean
    public GroupedOpenApi publicApi() {
        return GroupedOpenApi.builder()
            .group("public")
            .pathsToMatch("/api/**")
            .pathsToExclude("/api/internal/**")
            .build();
    }

    @Bean
    public GroupedOpenApi adminApi() {
        return GroupedOpenApi.builder()
            .group("admin")
            .pathsToMatch("/api/admin/**")
            .build();
    }
}
```

---

## Customizing Swagger UI

### Custom CSS and JavaScript

```yaml
# application.yml
springdoc:
  swagger-ui:
    path: /swagger-ui.html
    enabled: true
    disable-swagger-default-url: true
    display-request-duration: true
    operations-sorter: method
    tags-sorter: alpha
    doc-expansion: none
    default-models-expand-depth: 1
    default-model-expand-depth: 1
    display-operation-id: false
    filter: true
    show-extensions: true
    show-common-extensions: true
```

### Custom Branding

Create `src/main/resources/static/swagger-ui/swagger-ui.css`:

```css
/* Custom theme colors */
.swagger-ui .topbar {
    background-color: #2c3e50;
}

.swagger-ui .info .title {
    color: #2c3e50;
}

/* Custom button colors */
.swagger-ui .btn.execute {
    background-color: #27ae60;
    border-color: #27ae60;
}
```

---

## API Documentation Best Practices

### 1. Provide Clear Descriptions

```java
@Operation(
    summary = "Create a new product",  // Short, action-oriented
    description = """
        Creates a new product in the catalog with the provided information.
        The product name must be unique across the entire catalog.
        Stock quantity can be set to 0 for out-of-stock items.
        """  // Detailed explanation
)
```

### 2. Document All Response Codes

```java
@ApiResponses(value = {
    @ApiResponse(responseCode = "200", description = "Success"),
    @ApiResponse(responseCode = "400", description = "Invalid input"),
    @ApiResponse(responseCode = "401", description = "Unauthorized"),
    @ApiResponse(responseCode = "403", description = "Forbidden"),
    @ApiResponse(responseCode = "404", description = "Not found"),
    @ApiResponse(responseCode = "409", description = "Conflict"),
    @ApiResponse(responseCode = "500", description = "Server error")
})
```

### 3. Provide Examples

```java
@Schema(
    description = "Product price in USD",
    example = "1299.99",  // Clear example
    required = true
)
private BigDecimal price;
```

### 4. Document Validation Rules

```java
@Schema(
    description = "Product name",
    required = true,
    minLength = 3,
    maxLength = 100,
    pattern = "^[A-Za-z0-9 ]+$"
)
@NotBlank
@Size(min = 3, max = 100)
private String name;
```

### 5. Use Meaningful Tags

```java
@Tag(
    name = "Products",
    description = "Endpoints for managing product catalog including CRUD operations, search, and filtering"
)
```

### 6. Document Deprecation

```java
@Operation(
    summary = "Get product (deprecated)",
    deprecated = true,
    description = "This endpoint is deprecated. Use GET /api/v2/products/{id} instead."
)
@Deprecated
@GetMapping("/v1/products/{id}")
public ResponseEntity<ProductResponse> getProductV1(@PathVariable Long id) {
    // Old implementation
}
```

### 7. Version Your API

```java
@RestController
@RequestMapping("/api/v1/products")
@Tag(name = "Products V1", description = "Product API version 1 (deprecated)")
@Deprecated
public class ProductControllerV1 {
}

@RestController
@RequestMapping("/api/v2/products")
@Tag(name = "Products V2", description = "Product API version 2 (current)")
public class ProductControllerV2 {
}
```

---

## Generating Client Code

OpenAPI documentation can generate client code for various languages.

### Using OpenAPI Generator

```bash
# Install OpenAPI Generator
npm install -g @openapitools/openapi-generator-cli

# Generate Java client
openapi-generator-cli generate \
  -i http://localhost:8080/v3/api-docs \
  -g java \
  -o ./generated-client/java

# Generate TypeScript/Angular client
openapi-generator-cli generate \
  -i http://localhost:8080/v3/api-docs \
  -g typescript-angular \
  -o ./generated-client/typescript

# Generate Python client
openapi-generator-cli generate \
  -i http://localhost:8080/v3/api-docs \
  -g python \
  -o ./generated-client/python
```

### Maven Plugin

```xml
<plugin>
    <groupId>org.openapitools</groupId>
    <artifactId>openapi-generator-maven-plugin</artifactId>
    <version>7.2.0</version>
    <executions>
        <execution>
            <goals>
                <goal>generate</goal>
            </goals>
            <configuration>
                <inputSpec>${project.basedir}/src/main/resources/openapi.yaml</inputSpec>
                <generatorName>java</generatorName>
                <configOptions>
                    <sourceFolder>src/gen/java/main</sourceFolder>
                </configOptions>
            </configuration>
        </execution>
    </executions>
</plugin>
```

---

## Testing Documentation

### Verify OpenAPI Schema

```java
@SpringBootTest
@AutoConfigureMockMvc
class OpenApiDocumentationTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void shouldProvideOpenApiDocumentation() throws Exception {
        mockMvc.perform(get("/v3/api-docs"))
            .andExpect(status().isOk())
            .andExpect(content().contentType(MediaType.APPLICATION_JSON))
            .andExpect(jsonPath("$.openapi").value("3.0.1"))
            .andExpect(jsonPath("$.info.title").exists())
            .andExpect(jsonPath("$.paths").exists());
    }

    @Test
    void shouldProvideSwaggerUI() throws Exception {
        mockMvc.perform(get("/swagger-ui.html"))
            .andExpect(status().isOk());
    }

    @Test
    void shouldDocumentProductEndpoints() throws Exception {
        mockMvc.perform(get("/v3/api-docs"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.paths.['/api/products']").exists())
            .andExpect(jsonPath("$.paths.['/api/products'].get").exists())
            .andExpect(jsonPath("$.paths.['/api/products'].post").exists())
            .andExpect(jsonPath("$.paths.['/api/products/{id}']").exists())
            .andExpect(jsonPath("$.paths.['/api/products/{id}'].get").exists())
            .andExpect(jsonPath("$.paths.['/api/products/{id}'].put").exists())
            .andExpect(jsonPath("$.paths.['/api/products/{id}'].delete").exists());
    }
}
```

---

## Documentation Checklist

When documenting your REST API, ensure you have:

- [ ] Clear API title and description
- [ ] Version information
- [ ] Contact and license information
- [ ] Server URLs (dev, staging, production)
- [ ] All endpoints documented
- [ ] HTTP methods clearly indicated
- [ ] Request parameters documented
- [ ] Request body schemas defined
- [ ] Response codes documented
- [ ] Response schemas defined
- [ ] Authentication requirements
- [ ] Example requests and responses
- [ ] Error response formats
- [ ] Validation rules documented
- [ ] Deprecation notices for old endpoints
- [ ] API versioning strategy
- [ ] Rate limiting information (if applicable)

---

## Summary

API documentation is crucial for API success:

**Key Tools:**

1. **SpringDoc**: Automatic OpenAPI generation
2. **Swagger UI**: Interactive documentation
3. **OpenAPI Spec**: Standard API description format
4. **Code Generation**: Client code from spec

**Documentation Elements:**

- API information (title, version, contact)
- Endpoint descriptions and examples
- Request/response schemas
- Status codes and error handling
- Authentication requirements
- Validation rules

**Best Practices:**

1. Document all endpoints and operations
2. Provide clear descriptions and examples
3. Document all response codes
4. Include validation rules
5. Show example requests/responses
6. Document authentication
7. Keep documentation up-to-date
8. Use tags to organize endpoints
9. Version your API properly
10. Test your documentation

**Benefits:**

- Faster API adoption
- Reduced support burden
- Better developer experience
- Automated client generation
- Interactive testing
- Consistent API design

Well-documented APIs are easier to use, maintain, and evolve!
