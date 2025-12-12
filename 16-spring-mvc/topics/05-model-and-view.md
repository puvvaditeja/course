# Model and View

## What is Model and View?

In Spring MVC, the Model represents the data to be displayed, and the View represents how that data should be presented. The framework provides multiple ways to pass data from controllers to views and return view information back to the DispatcherServlet.

### Key Concepts

- **Model**: Container for data to be displayed in the view
- **View**: Presentation layer (JSP, Thymeleaf, JSON, etc.)
- **ModelAndView**: Combines model data and view name in one object
- **ModelMap**: Extension of LinkedHashMap for model data
- **ModelAttribute**: Binds method parameters or return values to named model attributes

### Data Flow

```
┌─────────────────────────────────────────────────────────┐
│                    Controller                            │
│                                                          │
│  1. Process Request                                      │
│  2. Prepare Data (Model)                                 │
│  3. Select View Name                                     │
│                                                          │
└────────────────────┬────────────────────────────────────┘
                     │
                     │ Model + View Name
                     ▼
         ┌───────────────────────┐
         │   DispatcherServlet   │
         └───────────┬───────────┘
                     │
                     │ Resolve View
                     ▼
         ┌───────────────────────┐
         │     ViewResolver      │
         └───────────┬───────────┘
                     │
                     │ View Object
                     ▼
         ┌───────────────────────┐
         │        View           │
         │  (Render with Model)  │
         └───────────┬───────────┘
                     │
                     │ HTML/JSON
                     ▼
         ┌───────────────────────┐
         │       Browser         │
         └───────────────────────┘
```

---

## Working with Model

The Model interface provides methods to add attributes that will be available in the view.

### Using Model Interface

```java
package com.example.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

import java.util.Arrays;
import java.util.List;

@Controller
public class ModelExampleController {

    /**
     * Add single attribute to model
     */
    @GetMapping("/user/{id}")
    public String getUser(@PathVariable Long id, Model model) {
        User user = userService.findById(id);
        model.addAttribute("user", user);
        return "user-detail";
    }

    /**
     * Add multiple attributes
     */
    @GetMapping("/dashboard")
    public String dashboard(Model model) {
        model.addAttribute("userName", "John Doe");
        model.addAttribute("userRole", "ADMIN");
        model.addAttribute("loginCount", 42);
        model.addAttribute("lastLogin", new Date());
        return "dashboard";
    }

    /**
     * Add collection to model
     */
    @GetMapping("/users")
    public String listUsers(Model model) {
        List<User> users = userService.findAll();
        model.addAttribute("users", users);
        model.addAttribute("totalUsers", users.size());
        return "user-list";
    }

    /**
     * Add multiple objects
     */
    @GetMapping("/report")
    public String report(Model model) {
        model.addAttribute("sales", salesService.getTodaySales());
        model.addAttribute("orders", orderService.getPendingOrders());
        model.addAttribute("customers", customerService.getNewCustomers());
        return "report";
    }

    /**
     * Attribute name inferred from type
     */
    @GetMapping("/product/{id}")
    public String getProduct(@PathVariable Long id, Model model) {
        Product product = productService.findById(id);
        // Attribute name will be "product" (lowercase class name)
        model.addAttribute(product);
        return "product-detail";
    }
}
```

**Accessing in JSP**
```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<!DOCTYPE html>
<html>
<head>
    <title>User Details</title>
</head>
<body>
    <h1>User Information</h1>
    <p>Name: ${user.name}</p>
    <p>Email: ${user.email}</p>
    <p>Role: ${userRole}</p>

    <h2>All Users</h2>
    <ul>
        <c:forEach items="${users}" var="user">
            <li>${user.name} - ${user.email}</li>
        </c:forEach>
    </ul>
</body>
</html>
```

### Using ModelMap

```java
package com.example.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.GetMapping;

import java.util.HashMap;
import java.util.Map;

@Controller
public class ModelMapController {

    /**
     * Using ModelMap (extends LinkedHashMap)
     */
    @GetMapping("/settings")
    public String settings(ModelMap modelMap) {
        modelMap.addAttribute("appName", "My Application");
        modelMap.addAttribute("version", "1.0.0");
        modelMap.put("theme", "dark");  // Can also use put()
        return "settings";
    }

    /**
     * Add all entries from a Map
     */
    @GetMapping("/config")
    public String config(ModelMap modelMap) {
        Map<String, Object> configMap = new HashMap<>();
        configMap.put("maxUploadSize", "10MB");
        configMap.put("allowedFileTypes", Arrays.asList("pdf", "doc", "txt"));
        configMap.put("enableNotifications", true);

        modelMap.addAllAttributes(configMap);
        return "config";
    }

    /**
     * Merge attributes
     */
    @GetMapping("/merged")
    public String merged(ModelMap modelMap) {
        modelMap.addAttribute("initial", "value");

        Map<String, Object> additionalAttrs = new HashMap<>();
        additionalAttrs.put("extra1", "value1");
        additionalAttrs.put("extra2", "value2");

        modelMap.mergeAttributes(additionalAttrs);
        return "merged";
    }
}
```

### Using Map

```java
@Controller
public class MapController {

    /**
     * Using plain Map as model
     */
    @GetMapping("/plain-map")
    public String plainMap(Map<String, Object> model) {
        model.put("message", "Hello, World!");
        model.put("timestamp", System.currentTimeMillis());
        return "plain-map";
    }
}
```

---

## ModelAndView

ModelAndView is a holder for both Model and View, providing a convenient way to return both from a controller method.

### Basic Usage

```java
package com.example.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class ModelAndViewController {

    /**
     * Create ModelAndView with view name
     */
    @GetMapping("/home")
    public ModelAndView home() {
        ModelAndView mav = new ModelAndView("home");
        mav.addObject("message", "Welcome to Spring MVC!");
        mav.addObject("currentYear", 2024);
        return mav;
    }

    /**
     * Create ModelAndView using constructor
     */
    @GetMapping("/about")
    public ModelAndView about() {
        ModelAndView mav = new ModelAndView("about", "company", "Acme Corp");
        return mav;
    }

    /**
     * Set view name later
     */
    @GetMapping("/contact")
    public ModelAndView contact() {
        ModelAndView mav = new ModelAndView();
        mav.setViewName("contact");
        mav.addObject("email", "contact@example.com");
        mav.addObject("phone", "555-1234");
        return mav;
    }

    /**
     * Add multiple objects
     */
    @GetMapping("/users/{id}")
    public ModelAndView userDetail(@PathVariable Long id) {
        User user = userService.findById(id);
        List<Order> orders = orderService.findByUserId(id);

        ModelAndView mav = new ModelAndView("user-detail");
        mav.addObject("user", user);
        mav.addObject("orders", orders);
        mav.addObject("orderCount", orders.size());
        return mav;
    }

    /**
     * Add map of objects
     */
    @GetMapping("/dashboard")
    public ModelAndView dashboard() {
        Map<String, Object> model = new HashMap<>();
        model.put("totalUsers", userService.count());
        model.put("totalOrders", orderService.count());
        model.put("revenue", salesService.getTotalRevenue());

        ModelAndView mav = new ModelAndView("dashboard", model);
        return mav;
    }
}
```

### Redirect and Forward with ModelAndView

```java
@Controller
public class NavigationController {

    /**
     * Redirect using ModelAndView
     */
    @PostMapping("/save")
    public ModelAndView save(User user) {
        userService.save(user);
        ModelAndView mav = new ModelAndView("redirect:/users");
        return mav;
    }

    /**
     * Redirect with path variable
     */
    @PostMapping("/create")
    public ModelAndView create(Product product) {
        Product saved = productService.save(product);
        ModelAndView mav = new ModelAndView("redirect:/products/" + saved.getId());
        return mav;
    }

    /**
     * Forward using ModelAndView
     */
    @GetMapping("/legacy")
    public ModelAndView legacy() {
        ModelAndView mav = new ModelAndView("forward:/modern");
        return mav;
    }

    /**
     * Conditional redirect
     */
    @PostMapping("/submit")
    public ModelAndView submit(Form form) {
        if (form.isValid()) {
            return new ModelAndView("redirect:/success");
        } else {
            ModelAndView mav = new ModelAndView("form");
            mav.addObject("errors", form.getErrors());
            return mav;
        }
    }
}
```

---

## @ModelAttribute

The `@ModelAttribute` annotation is used to bind request parameters to model objects and to add attributes to the model.

### Method-Level @ModelAttribute

```java
package com.example.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class GlobalAttributeController {

    /**
     * Add attribute to ALL handler methods in this controller
     * This method is called BEFORE any handler method
     */
    @ModelAttribute("appName")
    public String appName() {
        return "My Application";
    }

    /**
     * Add multiple attributes
     */
    @ModelAttribute
    public void addCommonAttributes(Model model) {
        model.addAttribute("version", "1.0.0");
        model.addAttribute("currentYear", 2024);
        model.addAttribute("environment", "production");
    }

    /**
     * Add object as attribute
     */
    @ModelAttribute("categories")
    public List<Category> categories() {
        return categoryService.findAll();
    }

    /**
     * Handler method - all @ModelAttribute methods are called first
     */
    @GetMapping("/page")
    public String page(Model model) {
        // appName, version, currentYear, environment, categories
        // are already in the model
        model.addAttribute("pageTitle", "My Page");
        return "page";
    }
}
```

### Parameter-Level @ModelAttribute

```java
@Controller
@RequestMapping("/users")
public class UserFormController {

    /**
     * Bind form data to User object
     */
    @GetMapping("/new")
    public String showForm(Model model) {
        model.addAttribute("user", new User());
        return "user-form";
    }

    /**
     * Form submission - automatic binding
     */
    @PostMapping
    public String createUser(@ModelAttribute User user, Model model) {
        // User object is automatically populated from form fields
        userService.save(user);
        model.addAttribute("message", "User created successfully!");
        return "redirect:/users";
    }

    /**
     * Custom attribute name
     */
    @PostMapping("/register")
    public String register(@ModelAttribute("registrationForm") User user) {
        userService.register(user);
        return "redirect:/login";
    }

    /**
     * Edit form - pre-populate with existing data
     */
    @GetMapping("/{id}/edit")
    public String editForm(@PathVariable Long id, Model model) {
        User user = userService.findById(id);
        model.addAttribute("user", user);
        return "user-form";
    }

    /**
     * Update - merge form data with existing object
     */
    @PostMapping("/{id}")
    public String update(@PathVariable Long id,
                        @ModelAttribute User user) {
        user.setId(id);  // Ensure ID is set
        userService.update(user);
        return "redirect:/users/" + id;
    }
}
```

**user-form.jsp**
```jsp
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<!DOCTYPE html>
<html>
<head>
    <title>User Form</title>
</head>
<body>
    <h1>Create User</h1>

    <!-- modelAttribute must match controller's model attribute name -->
    <form:form method="POST" action="/users" modelAttribute="user">
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
        <div>
            <label>Role:</label>
            <form:select path="role">
                <form:option value="USER">User</form:option>
                <form:option value="ADMIN">Admin</form:option>
            </form:select>
        </div>
        <button type="submit">Create</button>
    </form:form>
</body>
</html>
```

### @ModelAttribute with Validation

```java
@Controller
@RequestMapping("/registration")
public class RegistrationController {

    @GetMapping
    public String showForm(Model model) {
        model.addAttribute("user", new User());
        return "registration-form";
    }

    /**
     * Validate model attribute
     */
    @PostMapping
    public String register(@Valid @ModelAttribute User user,
                          BindingResult result,
                          Model model) {
        // Check for validation errors
        if (result.hasErrors()) {
            // Return to form with errors
            return "registration-form";
        }

        // No errors - proceed with registration
        userService.register(user);
        model.addAttribute("message", "Registration successful!");
        return "redirect:/login";
    }
}
```

**User.java with Validation**
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

    @NotNull(message = "Age is required")
    @Min(value = 18, message = "Must be at least 18 years old")
    private Integer age;

    // Constructors, getters, setters
}
```

---

## Session Attributes

Store model attributes in HTTP session across multiple requests.

### Using @SessionAttributes

```java
package com.example.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.bind.support.SessionStatus;

/**
 * Store "cart" attribute in session
 */
@Controller
@RequestMapping("/shopping")
@SessionAttributes("cart")
public class ShoppingController {

    /**
     * Initialize session attribute
     * This method is called when "cart" is first accessed
     */
    @ModelAttribute("cart")
    public ShoppingCart initCart() {
        return new ShoppingCart();
    }

    /**
     * Add product to cart (stored in session)
     */
    @PostMapping("/add")
    public String addToCart(@RequestParam Long productId,
                          @ModelAttribute("cart") ShoppingCart cart,
                          Model model) {
        Product product = productService.findById(productId);
        cart.addProduct(product);
        model.addAttribute("message", "Product added to cart");
        return "redirect:/products";
    }

    /**
     * View cart
     */
    @GetMapping("/cart")
    public String viewCart(@ModelAttribute("cart") ShoppingCart cart,
                          Model model) {
        model.addAttribute("total", cart.getTotal());
        return "shopping-cart";
    }

    /**
     * Update cart item quantity
     */
    @PostMapping("/cart/update")
    public String updateCart(@RequestParam Long productId,
                           @RequestParam int quantity,
                           @ModelAttribute("cart") ShoppingCart cart) {
        cart.updateQuantity(productId, quantity);
        return "redirect:/shopping/cart";
    }

    /**
     * Checkout and clear session
     */
    @PostMapping("/checkout")
    public String checkout(@ModelAttribute("cart") ShoppingCart cart,
                          SessionStatus sessionStatus) {
        // Process checkout
        orderService.createOrder(cart);

        // Clear session attributes
        sessionStatus.setComplete();

        return "redirect:/order/confirmation";
    }

    /**
     * Clear cart
     */
    @PostMapping("/cart/clear")
    public String clearCart(SessionStatus sessionStatus) {
        sessionStatus.setComplete();
        return "redirect:/products";
    }
}
```

### Multiple Session Attributes

```java
@Controller
@SessionAttributes({"user", "preferences", "cart"})
public class MultiSessionController {

    @ModelAttribute("user")
    public User initUser() {
        return new User();
    }

    @ModelAttribute("preferences")
    public UserPreferences initPreferences() {
        return new UserPreferences();
    }

    @ModelAttribute("cart")
    public ShoppingCart initCart() {
        return new ShoppingCart();
    }

    @GetMapping("/profile")
    public String profile(@ModelAttribute("user") User user,
                         @ModelAttribute("preferences") UserPreferences prefs,
                         Model model) {
        // All session attributes are available
        return "profile";
    }
}
```

---

## Flash Attributes

Flash attributes survive a redirect and are automatically removed after being used once.

### Using RedirectAttributes

```java
@Controller
public class FlashAttributeController {

    /**
     * Add flash attributes on redirect
     */
    @PostMapping("/save")
    public String save(User user, RedirectAttributes redirectAttributes) {
        userService.save(user);

        // Add flash attribute (available after redirect, then removed)
        redirectAttributes.addFlashAttribute("message", "User saved successfully!");
        redirectAttributes.addFlashAttribute("user", user);

        // Redirect
        return "redirect:/users";
    }

    /**
     * Receive flash attributes
     */
    @GetMapping("/users")
    public String list(Model model) {
        // Flash attributes are automatically added to model
        // Access: ${message} and ${user} in view
        // After this request, flash attributes are removed
        return "user-list";
    }

    /**
     * Flash vs regular attributes
     */
    @PostMapping("/submit")
    public String submit(Form form, RedirectAttributes redirectAttributes) {
        // Flash attribute - available in next request only
        redirectAttributes.addFlashAttribute("formData", form);

        // URL parameter - visible in URL
        redirectAttributes.addAttribute("id", form.getId());

        // Redirects to: /result?id=123
        // formData available in model, but not in URL
        return "redirect:/result";
    }

    /**
     * Conditional flash attributes
     */
    @PostMapping("/process")
    public String process(Data data, RedirectAttributes redirectAttributes) {
        try {
            dataService.process(data);
            redirectAttributes.addFlashAttribute("success", "Processing completed!");
            return "redirect:/dashboard";
        } catch (Exception e) {
            redirectAttributes.addFlashAttribute("error", e.getMessage());
            redirectAttributes.addFlashAttribute("data", data);
            return "redirect:/form";
        }
    }
}
```

**Accessing Flash Attributes in JSP**
```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<!DOCTYPE html>
<html>
<head>
    <title>Result</title>
</head>
<body>
    <!-- Display flash message if present -->
    <c:if test="${not empty message}">
        <div class="alert alert-success">
            ${message}
        </div>
    </c:if>

    <c:if test="${not empty error}">
        <div class="alert alert-danger">
            ${error}
        </div>
    </c:if>

    <!-- Display other content -->
    <h1>User List</h1>
    <!-- ... -->
</body>
</html>
```

---

## Request and Session Scope

### Understanding Scopes

```java
@Controller
public class ScopeController {

    /**
     * Request scope - available for single request only
     */
    @GetMapping("/request-scope")
    public String requestScope(Model model) {
        model.addAttribute("data", "Request scope data");
        // Available in this request only
        return "scope-demo";
    }

    /**
     * Session scope - available across multiple requests
     */
    @GetMapping("/session-scope")
    public String sessionScope(HttpSession session) {
        session.setAttribute("data", "Session scope data");
        // Available across multiple requests until session expires
        return "scope-demo";
    }

    /**
     * Application scope - available to all users
     */
    @GetMapping("/application-scope")
    public String applicationScope(ServletContext context) {
        context.setAttribute("data", "Application scope data");
        // Available to all users, all sessions
        return "scope-demo";
    }
}
```

**Scope Lifecycle**
```
Request Scope
├── Created: When request arrives
├── Destroyed: After response is sent
└── Use case: Data for single page

Session Scope
├── Created: First request from user
├── Destroyed: Session timeout or invalidation
└── Use case: User-specific data across pages

Application Scope
├── Created: Application startup
├── Destroyed: Application shutdown
└── Use case: Shared data for all users
```

---

## Best Practices

### 1. Choose the Right Approach

```java
// Use Model for simple cases
@GetMapping("/simple")
public String simple(Model model) {
    model.addAttribute("data", data);
    return "view";
}

// Use ModelAndView when returning multiple attributes
@GetMapping("/complex")
public ModelAndView complex() {
    ModelAndView mav = new ModelAndView("view");
    mav.addObject("data1", data1);
    mav.addObject("data2", data2);
    mav.addObject("data3", data3);
    return mav;
}
```

### 2. Use @ModelAttribute for Forms

```java
// Always use @ModelAttribute for form binding
@PostMapping("/user")
public String createUser(@ModelAttribute User user) {
    userService.save(user);
    return "redirect:/users";
}
```

### 3. Redirect After POST (PRG Pattern)

```java
// Always redirect after successful POST to prevent duplicate submission
@PostMapping("/submit")
public String submit(Form form, RedirectAttributes attrs) {
    service.process(form);
    attrs.addFlashAttribute("message", "Success!");
    return "redirect:/result";  // PRG pattern
}
```

### 4. Use Session Attributes Sparingly

```java
// Only use session for data that needs to persist across requests
@SessionAttributes("cart")  // Shopping cart across pages
@SessionAttributes("wizard") // Multi-step wizard form
```

### 5. Clear Session When Done

```java
@PostMapping("/complete")
public String complete(SessionStatus status) {
    // Process completion
    status.setComplete();  // Clear session attributes
    return "redirect:/home";
}
```

### 6. Validate Model Attributes

```java
@PostMapping("/save")
public String save(@Valid @ModelAttribute User user,
                  BindingResult result) {
    if (result.hasErrors()) {
        return "form";
    }
    userService.save(user);
    return "redirect:/success";
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Model | Container for data passed to view; simple interface |
| ModelAndView | Combines model data and view name in single object |
| @ModelAttribute | Binds parameters to objects, adds common attributes |
| Session Attributes | Persist data across multiple requests |
| Flash Attributes | Survive redirect, automatically removed after use |
| Scopes | Request (single request), Session (user), Application (all users) |
| Best Practices | Use appropriate approach, validate input, redirect after POST |

## Next Topic

Continue to [Spring ORM](./06-spring-orm.md) to learn about database integration in Spring.
