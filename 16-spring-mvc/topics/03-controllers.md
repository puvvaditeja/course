# Controllers

## What are Controllers?

Controllers are the "C" in MVC (Model-View-Controller). They handle incoming HTTP requests, process user input, interact with the model (business logic and data), and return an appropriate response. In Spring MVC, controllers are simple Java classes annotated with `@Controller` or `@RestController`.

### Key Characteristics

- **Request Handling**: Process HTTP requests and parameters
- **Business Logic Coordination**: Delegate to service layer
- **Model Preparation**: Prepare data for the view
- **View Selection**: Determine which view to render
- **RESTful Support**: Return data directly (JSON/XML)
- **Validation**: Validate user input
- **Exception Handling**: Handle errors gracefully

### Controller vs Servlet

| Feature | Spring Controller | Traditional Servlet |
|---------|------------------|---------------------|
| Declaration | `@Controller` annotation | Extends HttpServlet |
| Request Mapping | `@RequestMapping` annotations | URL patterns in web.xml |
| Parameter Binding | Automatic via annotations | Manual via request.getParameter() |
| Return Type | Flexible (String, ModelAndView, etc.) | void or forward/redirect |
| Testing | Easy to unit test | Requires servlet container |
| Configuration | Minimal | Extensive XML configuration |

---

## Creating Controllers

### Basic Controller

```java
package com.example.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

/**
 * Basic controller example
 */
@Controller
public class HomeController {

    /**
     * Handle GET requests to /home
     * Returns view name "home"
     */
    @RequestMapping(value = "/home", method = RequestMethod.GET)
    public String home() {
        return "home";  // Resolved to /WEB-INF/views/home.jsp
    }
}
```

### Modern Controller (Using Composed Annotations)

```java
package com.example.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;

/**
 * Modern controller using composed annotations
 */
@Controller
@RequestMapping("/users")  // Base URL for all handler methods
public class UserController {

    /**
     * Handle GET /users
     * List all users
     */
    @GetMapping
    public String listUsers(Model model) {
        // Add data to model
        model.addAttribute("message", "User List");
        return "users/list";
    }

    /**
     * Handle GET /users/new
     * Show create user form
     */
    @GetMapping("/new")
    public String showCreateForm() {
        return "users/create";
    }

    /**
     * Handle POST /users
     * Create new user
     */
    @PostMapping
    public String createUser() {
        // Process form submission
        return "redirect:/users";
    }
}
```

### REST Controller

```java
package com.example.controller;

import org.springframework.web.bind.annotation.*;
import java.util.List;

/**
 * REST API controller
 * @RestController = @Controller + @ResponseBody
 */
@RestController
@RequestMapping("/api/users")
public class UserRestController {

    /**
     * GET /api/users
     * Returns JSON response
     */
    @GetMapping
    public List<User> getAllUsers() {
        return userService.findAll();
        // Automatically converted to JSON
    }

    /**
     * GET /api/users/{id}
     * Returns single user as JSON
     */
    @GetMapping("/{id}")
    public User getUserById(@PathVariable Long id) {
        return userService.findById(id);
    }

    /**
     * POST /api/users
     * Create user from JSON request body
     */
    @PostMapping
    public User createUser(@RequestBody User user) {
        return userService.save(user);
    }

    /**
     * PUT /api/users/{id}
     * Update user
     */
    @PutMapping("/{id}")
    public User updateUser(@PathVariable Long id, @RequestBody User user) {
        return userService.update(id, user);
    }

    /**
     * DELETE /api/users/{id}
     * Delete user
     */
    @DeleteMapping("/{id}")
    public void deleteUser(@PathVariable Long id) {
        userService.delete(id);
    }
}
```

---

## Request Mapping

### @RequestMapping

The `@RequestMapping` annotation maps HTTP requests to handler methods.

```java
@Controller
public class RequestMappingExamples {

    // Simple mapping
    @RequestMapping("/hello")
    public String hello() {
        return "hello";
    }

    // Specific HTTP method
    @RequestMapping(value = "/submit", method = RequestMethod.POST)
    public String submit() {
        return "success";
    }

    // Multiple paths
    @RequestMapping({"/home", "/", "/index"})
    public String home() {
        return "home";
    }

    // Multiple HTTP methods
    @RequestMapping(value = "/form", method = {RequestMethod.GET, RequestMethod.POST})
    public String form() {
        return "form";
    }

    // Path variables
    @RequestMapping("/users/{id}")
    public String getUser(@PathVariable Long id) {
        return "user-detail";
    }

    // Request parameters
    @RequestMapping(value = "/search", params = "query")
    public String search() {
        return "search-results";
    }

    // Headers
    @RequestMapping(value = "/api", headers = "X-API-VERSION=1")
    public String apiV1() {
        return "api-v1";
    }

    // Content type (consumes)
    @RequestMapping(value = "/upload", consumes = "multipart/form-data")
    public String upload() {
        return "upload-success";
    }

    // Response type (produces)
    @RequestMapping(value = "/data", produces = "application/json")
    @ResponseBody
    public String getData() {
        return "{\"message\": \"Hello\"}";
    }
}
```

### HTTP Method-Specific Annotations

Spring provides convenient annotations for common HTTP methods:

```java
@Controller
@RequestMapping("/products")
public class ProductController {

    // GET request
    @GetMapping
    public String list() {
        return "products/list";
    }

    // POST request
    @PostMapping
    public String create() {
        return "redirect:/products";
    }

    // PUT request
    @PutMapping("/{id}")
    public String update(@PathVariable Long id) {
        return "redirect:/products/" + id;
    }

    // DELETE request
    @DeleteMapping("/{id}")
    public String delete(@PathVariable Long id) {
        return "redirect:/products";
    }

    // PATCH request
    @PatchMapping("/{id}")
    public String partialUpdate(@PathVariable Long id) {
        return "redirect:/products/" + id;
    }
}
```

---

## Handler Method Parameters

Spring MVC automatically resolves and injects various parameter types into handler methods.

### Path Variables

```java
@Controller
@RequestMapping("/books")
public class BookController {

    // Single path variable
    @GetMapping("/{id}")
    public String getBook(@PathVariable Long id, Model model) {
        Book book = bookService.findById(id);
        model.addAttribute("book", book);
        return "book-detail";
    }

    // Multiple path variables
    @GetMapping("/{category}/{id}")
    public String getBookByCategory(@PathVariable String category,
                                   @PathVariable Long id,
                                   Model model) {
        Book book = bookService.findByCategoryAndId(category, id);
        model.addAttribute("book", book);
        return "book-detail";
    }

    // Path variable with different name
    @GetMapping("/author/{authorId}")
    public String getBooksByAuthor(@PathVariable("authorId") Long id, Model model) {
        List<Book> books = bookService.findByAuthorId(id);
        model.addAttribute("books", books);
        return "books-list";
    }

    // Optional path variable (Spring 4.3.3+)
    @GetMapping({"/", "/{id}"})
    public String handleOptional(@PathVariable(required = false) Long id, Model model) {
        if (id != null) {
            model.addAttribute("book", bookService.findById(id));
            return "book-detail";
        }
        return "books-list";
    }
}
```

### Request Parameters

```java
@Controller
@RequestMapping("/search")
public class SearchController {

    // Simple request parameter
    @GetMapping
    public String search(@RequestParam String query, Model model) {
        // URL: /search?query=spring
        model.addAttribute("results", searchService.search(query));
        return "search-results";
    }

    // Multiple request parameters
    @GetMapping("/advanced")
    public String advancedSearch(@RequestParam String query,
                                @RequestParam String category,
                                @RequestParam int page,
                                Model model) {
        // URL: /search/advanced?query=spring&category=books&page=1
        model.addAttribute("results",
            searchService.search(query, category, page));
        return "search-results";
    }

    // Optional parameter with default value
    @GetMapping("/filter")
    public String filter(@RequestParam(required = false, defaultValue = "all") String type,
                        Model model) {
        // URL: /search/filter or /search/filter?type=recent
        model.addAttribute("results", searchService.filterByType(type));
        return "search-results";
    }

    // Parameter name different from variable
    @GetMapping("/sort")
    public String sort(@RequestParam("sort_by") String sortBy, Model model) {
        // URL: /search/sort?sort_by=date
        model.addAttribute("results", searchService.sortBy(sortBy));
        return "search-results";
    }

    // Multiple values for single parameter
    @GetMapping("/multi")
    public String multiSelect(@RequestParam List<String> tags, Model model) {
        // URL: /search/multi?tags=java&tags=spring&tags=mvc
        model.addAttribute("results", searchService.searchByTags(tags));
        return "search-results";
    }

    // All parameters as Map
    @GetMapping("/all")
    public String allParams(@RequestParam Map<String, String> params, Model model) {
        model.addAttribute("params", params);
        return "search-results";
    }
}
```

### Request Body

```java
@RestController
@RequestMapping("/api/users")
public class UserApiController {

    // Single object from JSON
    @PostMapping
    public User createUser(@RequestBody User user) {
        // Automatically deserializes JSON to User object
        return userService.save(user);
    }

    // List of objects from JSON array
    @PostMapping("/batch")
    public List<User> createUsers(@RequestBody List<User> users) {
        return userService.saveAll(users);
    }

    // Validation with @Valid
    @PostMapping("/validated")
    public User createValidatedUser(@Valid @RequestBody User user,
                                   BindingResult result) {
        if (result.hasErrors()) {
            throw new ValidationException("Invalid user data");
        }
        return userService.save(user);
    }
}
```

### Model and ModelMap

```java
@Controller
@RequestMapping("/demo")
public class DemoController {

    // Using Model
    @GetMapping("/model")
    public String useModel(Model model) {
        model.addAttribute("message", "Hello from Model");
        model.addAttribute("user", new User("John", "Doe"));
        return "demo";
    }

    // Using ModelMap
    @GetMapping("/modelmap")
    public String useModelMap(ModelMap modelMap) {
        modelMap.addAttribute("message", "Hello from ModelMap");
        modelMap.put("count", 42);
        return "demo";
    }

    // Using Map
    @GetMapping("/map")
    public String useMap(Map<String, Object> model) {
        model.put("message", "Hello from Map");
        return "demo";
    }
}
```

### HttpServletRequest and HttpServletResponse

```java
@Controller
@RequestMapping("/servlet")
public class ServletController {

    @GetMapping("/request")
    public String handleRequest(HttpServletRequest request, Model model) {
        // Access request details
        String userAgent = request.getHeader("User-Agent");
        String ip = request.getRemoteAddr();
        String method = request.getMethod();

        model.addAttribute("userAgent", userAgent);
        model.addAttribute("ip", ip);
        model.addAttribute("method", method);

        return "request-info";
    }

    @GetMapping("/response")
    public void handleResponse(HttpServletResponse response) throws IOException {
        // Manually write response
        response.setContentType("text/plain");
        response.setCharacterEncoding("UTF-8");
        response.getWriter().write("Custom response");
    }

    @GetMapping("/download")
    public void downloadFile(HttpServletResponse response) throws IOException {
        // Set headers for file download
        response.setContentType("application/pdf");
        response.setHeader("Content-Disposition", "attachment; filename=document.pdf");

        // Write file content
        // ... (file writing logic)
    }
}
```

### Session Attributes

```java
@Controller
@RequestMapping("/session")
@SessionAttributes("cart")  // Store cart in session
public class ShoppingController {

    @ModelAttribute("cart")
    public ShoppingCart initCart() {
        return new ShoppingCart();
    }

    @PostMapping("/add")
    public String addToCart(@RequestParam Long productId,
                          @ModelAttribute("cart") ShoppingCart cart) {
        cart.addProduct(productId);
        return "redirect:/products";
    }

    @GetMapping("/cart")
    public String viewCart(@ModelAttribute("cart") ShoppingCart cart, Model model) {
        model.addAttribute("cart", cart);
        return "cart";
    }

    @PostMapping("/clear")
    public String clearCart(SessionStatus sessionStatus) {
        sessionStatus.setComplete();  // Clear session attributes
        return "redirect:/products";
    }
}
```

### Headers and Cookies

```java
@Controller
@RequestMapping("/headers")
public class HeaderController {

    // Access specific header
    @GetMapping("/user-agent")
    public String getUserAgent(@RequestHeader("User-Agent") String userAgent, Model model) {
        model.addAttribute("userAgent", userAgent);
        return "header-info";
    }

    // Optional header
    @GetMapping("/auth")
    public String checkAuth(@RequestHeader(value = "Authorization", required = false) String auth,
                          Model model) {
        model.addAttribute("authenticated", auth != null);
        return "auth-status";
    }

    // All headers as Map
    @GetMapping("/all")
    public String allHeaders(@RequestHeader Map<String, String> headers, Model model) {
        model.addAttribute("headers", headers);
        return "header-info";
    }

    // Access cookie
    @GetMapping("/cookie")
    public String getCookie(@CookieValue(value = "sessionId", defaultValue = "none") String sessionId,
                          Model model) {
        model.addAttribute("sessionId", sessionId);
        return "cookie-info";
    }
}
```

---

## Return Types

Controller methods can return various types to handle different scenarios.

### String (View Name)

```java
@Controller
public class ViewNameController {

    @GetMapping("/home")
    public String home() {
        return "home";  // Resolved to /WEB-INF/views/home.jsp
    }

    @GetMapping("/redirect")
    public String redirect() {
        return "redirect:/home";  // Redirect to /home
    }

    @GetMapping("/forward")
    public String forward() {
        return "forward:/home";  // Forward to /home
    }
}
```

### ModelAndView

```java
@Controller
public class ModelAndViewController {

    @GetMapping("/mav")
    public ModelAndView handleRequest() {
        ModelAndView mav = new ModelAndView();
        mav.setViewName("user-list");
        mav.addObject("users", userService.findAll());
        mav.addObject("message", "Welcome");
        return mav;
    }

    // Alternative constructor
    @GetMapping("/mav2")
    public ModelAndView handleRequest2() {
        ModelAndView mav = new ModelAndView("user-list");
        mav.addObject("users", userService.findAll());
        return mav;
    }
}
```

### void

```java
@Controller
public class VoidController {

    @GetMapping("/void")
    public void handleVoid(HttpServletRequest request, HttpServletResponse response)
            throws IOException {
        // Manually handle response
        response.getWriter().write("Custom response");
    }

    // View name derived from request URL
    @GetMapping("/auto")
    public void autoView(Model model) {
        // View name: "auto" (derived from URL)
        model.addAttribute("message", "Auto view");
    }
}
```

### ResponseEntity

```java
@RestController
@RequestMapping("/api")
public class ResponseEntityController {

    @GetMapping("/user/{id}")
    public ResponseEntity<User> getUser(@PathVariable Long id) {
        User user = userService.findById(id);
        if (user != null) {
            return ResponseEntity.ok(user);
        }
        return ResponseEntity.notFound().build();
    }

    @PostMapping("/user")
    public ResponseEntity<User> createUser(@RequestBody User user) {
        User created = userService.save(user);
        return ResponseEntity
            .status(HttpStatus.CREATED)
            .header("Location", "/api/user/" + created.getId())
            .body(created);
    }

    @DeleteMapping("/user/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        userService.delete(id);
        return ResponseEntity.noContent().build();
    }
}
```

### Domain Objects (REST)

```java
@RestController
@RequestMapping("/api/products")
public class ProductRestController {

    // Returns single object as JSON
    @GetMapping("/{id}")
    public Product getProduct(@PathVariable Long id) {
        return productService.findById(id);
    }

    // Returns list as JSON array
    @GetMapping
    public List<Product> getAllProducts() {
        return productService.findAll();
    }

    // Returns custom object
    @GetMapping("/summary")
    public ProductSummary getSummary() {
        return new ProductSummary(
            productService.count(),
            productService.totalValue()
        );
    }
}
```

---

## Form Handling

### Simple Form Binding

**Controller**
```java
@Controller
@RequestMapping("/registration")
public class RegistrationController {

    @GetMapping
    public String showForm(Model model) {
        model.addAttribute("user", new User());
        return "registration-form";
    }

    @PostMapping
    public String submitForm(@ModelAttribute User user, Model model) {
        userService.save(user);
        model.addAttribute("message", "Registration successful!");
        return "registration-success";
    }
}
```

**User.java**
```java
package com.example.model;

public class User {
    private String username;
    private String email;
    private String password;

    // Constructors, getters, setters
    public User() {}

    public String getUsername() { return username; }
    public void setUsername(String username) { this.username = username; }

    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }

    public String getPassword() { return password; }
    public void setPassword(String password) { this.password = password; }
}
```

**registration-form.jsp**
```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<!DOCTYPE html>
<html>
<head>
    <title>Registration</title>
</head>
<body>
    <h1>User Registration</h1>

    <form:form method="POST" modelAttribute="user">
        <div>
            <label>Username:</label>
            <form:input path="username" />
        </div>
        <div>
            <label>Email:</label>
            <form:input path="email" type="email" />
        </div>
        <div>
            <label>Password:</label>
            <form:password path="password" />
        </div>
        <button type="submit">Register</button>
    </form:form>
</body>
</html>
```

### Form Validation

**User.java (with validation annotations)**
```java
package com.example.model;

import javax.validation.constraints.*;

public class User {

    @NotBlank(message = "Username is required")
    @Size(min = 3, max = 20, message = "Username must be between 3 and 20 characters")
    private String username;

    @NotBlank(message = "Email is required")
    @Email(message = "Invalid email format")
    private String email;

    @NotBlank(message = "Password is required")
    @Size(min = 6, message = "Password must be at least 6 characters")
    private String password;

    @Min(value = 18, message = "Must be at least 18 years old")
    private int age;

    // Getters and setters
}
```

**Controller with Validation**
```java
@Controller
@RequestMapping("/registration")
public class RegistrationController {

    @GetMapping
    public String showForm(Model model) {
        model.addAttribute("user", new User());
        return "registration-form";
    }

    @PostMapping
    public String submitForm(@Valid @ModelAttribute User user,
                           BindingResult result,
                           Model model) {
        if (result.hasErrors()) {
            // Return to form with error messages
            return "registration-form";
        }

        userService.save(user);
        model.addAttribute("message", "Registration successful!");
        return "registration-success";
    }
}
```

**registration-form.jsp (with error display)**
```jsp
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<!DOCTYPE html>
<html>
<head>
    <title>Registration</title>
    <style>
        .error { color: red; }
    </style>
</head>
<body>
    <h1>User Registration</h1>

    <form:form method="POST" modelAttribute="user">
        <div>
            <label>Username:</label>
            <form:input path="username" />
            <form:errors path="username" cssClass="error" />
        </div>
        <div>
            <label>Email:</label>
            <form:input path="email" type="email" />
            <form:errors path="email" cssClass="error" />
        </div>
        <div>
            <label>Password:</label>
            <form:password path="password" />
            <form:errors path="password" cssClass="error" />
        </div>
        <div>
            <label>Age:</label>
            <form:input path="age" type="number" />
            <form:errors path="age" cssClass="error" />
        </div>
        <button type="submit">Register</button>
    </form:form>
</body>
</html>
```

---

## Best Practices

### 1. Keep Controllers Thin

```java
// BAD: Controller contains business logic
@Controller
public class BadController {
    @PostMapping("/order")
    public String createOrder(@ModelAttribute Order order) {
        // Business logic in controller - BAD!
        if (order.getTotal() > 1000) {
            order.setDiscount(order.getTotal() * 0.1);
        }
        // Database access in controller - BAD!
        entityManager.persist(order);
        return "order-success";
    }
}

// GOOD: Controller delegates to service layer
@Controller
public class GoodController {
    @Autowired
    private OrderService orderService;

    @PostMapping("/order")
    public String createOrder(@ModelAttribute Order order) {
        orderService.createOrder(order);
        return "order-success";
    }
}
```

### 2. Use Proper HTTP Methods

```java
@Controller
@RequestMapping("/users")
public class UserController {

    @GetMapping           // List/retrieve
    public String list() { }

    @GetMapping("/{id}")  // Retrieve single
    public String get(@PathVariable Long id) { }

    @PostMapping          // Create
    public String create() { }

    @PutMapping("/{id}")  // Update (full)
    public String update(@PathVariable Long id) { }

    @PatchMapping("/{id}")// Update (partial)
    public String partialUpdate(@PathVariable Long id) { }

    @DeleteMapping("/{id}")// Delete
    public String delete(@PathVariable Long id) { }
}
```

### 3. Use Redirect After POST

```java
@Controller
public class FormController {

    @PostMapping("/submit")
    public String submit(@ModelAttribute Form form) {
        formService.save(form);
        // Redirect to prevent duplicate submission
        return "redirect:/success";
    }
}
```

### 4. Validate Input

```java
@Controller
public class ValidationController {

    @PostMapping("/user")
    public String createUser(@Valid @ModelAttribute User user,
                            BindingResult result) {
        if (result.hasErrors()) {
            return "user-form";
        }
        userService.save(user);
        return "redirect:/users";
    }
}
```

### 5. Use ResponseEntity for REST APIs

```java
@RestController
@RequestMapping("/api/users")
public class UserRestController {

    @GetMapping("/{id}")
    public ResponseEntity<User> getUser(@PathVariable Long id) {
        return userService.findById(id)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
    }
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Controllers | Handle HTTP requests, coordinate business logic, prepare responses |
| Annotations | @Controller, @RestController, @RequestMapping, @GetMapping, etc. |
| Parameters | @PathVariable, @RequestParam, @RequestBody, Model, etc. |
| Return Types | String, ModelAndView, ResponseEntity, void, domain objects |
| Forms | @ModelAttribute, @Valid, BindingResult |
| Best Practices | Thin controllers, proper HTTP methods, validation, redirect after POST |

## Next Topic

Continue to [ViewResolver](./04-view-resolver.md) to learn about resolving views in Spring MVC.
