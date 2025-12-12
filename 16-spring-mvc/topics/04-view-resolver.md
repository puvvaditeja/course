# ViewResolver

## What is a ViewResolver?

A ViewResolver is a Spring MVC component responsible for resolving logical view names returned by controllers into actual view implementations (JSP, Thymeleaf, FreeMarker, etc.). It acts as a bridge between the controller's view name and the actual view technology.

### Key Characteristics

- **View Name Resolution**: Maps logical names to physical view files
- **Multiple View Technologies**: Supports JSP, Thymeleaf, FreeMarker, Velocity, etc.
- **Chain of Responsibility**: Multiple resolvers can be configured with priority
- **Flexible Configuration**: Prefix, suffix, and other customization options
- **Content Negotiation**: Can return different views based on requested content type
- **Internationalization**: Supports locale-based view resolution

### Purpose

```
Controller                ViewResolver              View
┌──────────┐             ┌─────────────┐         ┌─────────┐
│ return   │────────────→│  Resolves   │────────→│  JSP    │
│ "home"   │  View Name  │  "home" to  │  View   │  File   │
│          │             │  /WEB-INF/  │ Object  │         │
└──────────┘             │  views/     │         └─────────┘
                         │  home.jsp   │
                         └─────────────┘
```

---

## Common ViewResolver Implementations

Spring MVC provides several built-in ViewResolver implementations.

### 1. InternalResourceViewResolver (JSP)

Most commonly used for JSP-based applications.

**Configuration (Java)**
```java
package com.example.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.ViewResolver;
import org.springframework.web.servlet.view.InternalResourceViewResolver;
import org.springframework.web.servlet.view.JstlView;

@Configuration
public class WebMvcConfig {

    @Bean
    public ViewResolver viewResolver() {
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setPrefix("/WEB-INF/views/");
        resolver.setSuffix(".jsp");
        resolver.setViewClass(JstlView.class);
        resolver.setOrder(1);
        return resolver;
    }
}
```

**Configuration (XML)**
```xml
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/views/"/>
    <property name="suffix" value=".jsp"/>
    <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
    <property name="order" value="1"/>
</bean>
```

**Example Usage**
```java
@Controller
public class HomeController {

    @GetMapping("/home")
    public String home() {
        return "home";  // Resolved to /WEB-INF/views/home.jsp
    }

    @GetMapping("/users/{id}")
    public String userDetail() {
        return "users/detail";  // Resolved to /WEB-INF/views/users/detail.jsp
    }
}
```

**How It Works**
```
View Name: "home"
Prefix: "/WEB-INF/views/"
Suffix: ".jsp"
Result: /WEB-INF/views/home.jsp

View Name: "users/detail"
Prefix: "/WEB-INF/views/"
Suffix: ".jsp"
Result: /WEB-INF/views/users/detail.jsp
```

### 2. ThymeleafViewResolver

Used for Thymeleaf template engine.

**Maven Dependency**
```xml
<dependency>
    <groupId>org.thymeleaf</groupId>
    <artifactId>thymeleaf-spring5</artifactId>
    <version>3.0.15.RELEASE</version>
</dependency>
```

**Configuration**
```java
package com.example.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.thymeleaf.spring5.SpringTemplateEngine;
import org.thymeleaf.spring5.view.ThymeleafViewResolver;
import org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver;
import org.thymeleaf.templatemode.TemplateMode;

@Configuration
public class ThymeleafConfig {

    @Bean
    public SpringResourceTemplateResolver templateResolver() {
        SpringResourceTemplateResolver resolver = new SpringResourceTemplateResolver();
        resolver.setPrefix("/WEB-INF/templates/");
        resolver.setSuffix(".html");
        resolver.setTemplateMode(TemplateMode.HTML);
        resolver.setCharacterEncoding("UTF-8");
        resolver.setCacheable(false); // Set to true in production
        return resolver;
    }

    @Bean
    public SpringTemplateEngine templateEngine() {
        SpringTemplateEngine engine = new SpringTemplateEngine();
        engine.setTemplateResolver(templateResolver());
        engine.setEnableSpringELCompiler(true);
        return engine;
    }

    @Bean
    public ThymeleafViewResolver viewResolver() {
        ThymeleafViewResolver resolver = new ThymeleafViewResolver();
        resolver.setTemplateEngine(templateEngine());
        resolver.setCharacterEncoding("UTF-8");
        resolver.setOrder(1);
        return resolver;
    }
}
```

**Controller**
```java
@Controller
public class ProductController {

    @GetMapping("/products")
    public String listProducts(Model model) {
        model.addAttribute("products", productService.findAll());
        return "products/list";  // Resolved to /WEB-INF/templates/products/list.html
    }
}
```

**Thymeleaf Template (list.html)**
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Product List</title>
</head>
<body>
    <h1>Products</h1>
    <table>
        <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Price</th>
        </tr>
        <tr th:each="product : ${products}">
            <td th:text="${product.id}">1</td>
            <td th:text="${product.name}">Product Name</td>
            <td th:text="${product.price}">$0.00</td>
        </tr>
    </table>
</body>
</html>
```

### 3. FreeMarkerViewResolver

Used for FreeMarker template engine.

**Maven Dependency**
```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context-support</artifactId>
    <version>5.3.20</version>
</dependency>
<dependency>
    <groupId>org.freemarker</groupId>
    <artifactId>freemarker</artifactId>
    <version>2.3.31</version>
</dependency>
```

**Configuration**
```java
package com.example.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.ViewResolver;
import org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer;
import org.springframework.web.servlet.view.freemarker.FreeMarkerViewResolver;

@Configuration
public class FreeMarkerConfig {

    @Bean
    public FreeMarkerConfigurer freeMarkerConfigurer() {
        FreeMarkerConfigurer configurer = new FreeMarkerConfigurer();
        configurer.setTemplateLoaderPath("/WEB-INF/freemarker/");
        configurer.setDefaultEncoding("UTF-8");
        return configurer;
    }

    @Bean
    public ViewResolver freeMarkerViewResolver() {
        FreeMarkerViewResolver resolver = new FreeMarkerViewResolver();
        resolver.setPrefix("");
        resolver.setSuffix(".ftl");
        resolver.setContentType("text/html;charset=UTF-8");
        resolver.setOrder(1);
        return resolver;
    }
}
```

**FreeMarker Template (products.ftl)**
```ftl
<!DOCTYPE html>
<html>
<head>
    <title>Product List</title>
</head>
<body>
    <h1>Products</h1>
    <table>
        <#list products as product>
        <tr>
            <td>${product.id}</td>
            <td>${product.name}</td>
            <td>${product.price}</td>
        </tr>
        </#list>
    </table>
</body>
</html>
```

### 4. BeanNameViewResolver

Resolves view names to beans defined in the application context.

**Configuration**
```java
@Configuration
public class ViewResolverConfig {

    @Bean
    public ViewResolver beanNameViewResolver() {
        BeanNameViewResolver resolver = new BeanNameViewResolver();
        resolver.setOrder(0); // Higher priority
        return resolver;
    }

    @Bean
    public View jsonView() {
        MappingJackson2JsonView view = new MappingJackson2JsonView();
        view.setPrettyPrint(true);
        return view;
    }

    @Bean
    public View xmlView() {
        MappingJackson2XmlView view = new MappingJackson2XmlView();
        return view;
    }
}
```

**Controller**
```java
@Controller
public class ApiController {

    @GetMapping("/api/data")
    public String getData(Model model) {
        model.addAttribute("message", "Hello, World!");
        return "jsonView";  // Resolves to bean named "jsonView"
    }
}
```

### 5. ContentNegotiatingViewResolver

Selects view based on requested media type.

**Configuration**
```java
@Configuration
public class ContentNegotiationConfig {

    @Bean
    public ViewResolver contentNegotiatingViewResolver() {
        ContentNegotiatingViewResolver resolver = new ContentNegotiatingViewResolver();

        List<ViewResolver> viewResolvers = new ArrayList<>();
        viewResolvers.add(internalResourceViewResolver());
        viewResolvers.add(jsonViewResolver());

        resolver.setViewResolvers(viewResolvers);
        return resolver;
    }

    @Bean
    public ViewResolver internalResourceViewResolver() {
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setPrefix("/WEB-INF/views/");
        resolver.setSuffix(".jsp");
        return resolver;
    }

    @Bean
    public ViewResolver jsonViewResolver() {
        return new ViewResolver() {
            @Override
            public View resolveViewName(String viewName, Locale locale) {
                MappingJackson2JsonView view = new MappingJackson2JsonView();
                view.setPrettyPrint(true);
                return view;
            }
        };
    }
}
```

**Controller**
```java
@Controller
public class MultiFormatController {

    @GetMapping("/data")
    public String getData(Model model) {
        model.addAttribute("users", userService.findAll());
        // Returns JSON if Accept: application/json
        // Returns JSP if Accept: text/html
        return "users";
    }
}
```

---

## ViewResolver Chain

Multiple ViewResolvers can be configured with different priorities using the `order` property.

### Configuration

```java
@Configuration
public class MultipleViewResolverConfig {

    /**
     * First resolver - handles bean-based views
     * Order: 0 (highest priority)
     */
    @Bean
    public ViewResolver beanNameViewResolver() {
        BeanNameViewResolver resolver = new BeanNameViewResolver();
        resolver.setOrder(0);
        return resolver;
    }

    /**
     * Second resolver - handles Thymeleaf templates
     * Order: 1
     */
    @Bean
    public ViewResolver thymeleafViewResolver() {
        ThymeleafViewResolver resolver = new ThymeleafViewResolver();
        resolver.setTemplateEngine(templateEngine());
        resolver.setOrder(1);
        return resolver;
    }

    /**
     * Third resolver - handles JSP pages
     * Order: 2 (lowest priority)
     */
    @Bean
    public ViewResolver jspViewResolver() {
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setPrefix("/WEB-INF/views/");
        resolver.setSuffix(".jsp");
        resolver.setOrder(2);
        return resolver;
    }
}
```

### Resolution Flow

```
Controller returns "home"
        │
        ▼
┌─────────────────────┐
│ BeanNameViewResolver│ Order: 0
│ Look for bean "home"│
└────────┬────────────┘
         │ Not Found
         ▼
┌─────────────────────────┐
│ ThymeleafViewResolver   │ Order: 1
│ Look for home.html      │
└────────┬────────────────┘
         │ Found!
         ▼
┌─────────────────────────┐
│ Return View Object      │
└─────────────────────────┘

(JspViewResolver not reached)
```

---

## Custom ViewResolver

You can create custom ViewResolver implementations for specific needs.

### Simple Custom ViewResolver

```java
package com.example.config;

import org.springframework.web.servlet.View;
import org.springframework.web.servlet.ViewResolver;

import java.util.Locale;

/**
 * Custom ViewResolver that prefixes all view names
 */
public class CustomViewResolver implements ViewResolver {

    private final ViewResolver delegate;
    private final String prefix;

    public CustomViewResolver(ViewResolver delegate, String prefix) {
        this.delegate = delegate;
        this.prefix = prefix;
    }

    @Override
    public View resolveViewName(String viewName, Locale locale) throws Exception {
        // Add custom prefix to view name
        String modifiedViewName = prefix + viewName;
        return delegate.resolveViewName(modifiedViewName, locale);
    }
}
```

**Configuration**
```java
@Configuration
public class CustomViewResolverConfig {

    @Bean
    public ViewResolver customViewResolver() {
        InternalResourceViewResolver internalResolver = new InternalResourceViewResolver();
        internalResolver.setPrefix("/WEB-INF/views/");
        internalResolver.setSuffix(".jsp");

        return new CustomViewResolver(internalResolver, "custom-");
    }
}
```

### Advanced Custom ViewResolver

```java
package com.example.config;

import org.springframework.web.servlet.View;
import org.springframework.web.servlet.ViewResolver;
import org.springframework.web.servlet.view.InternalResourceView;

import java.util.Locale;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

/**
 * Custom ViewResolver with caching and special handling
 */
public class CachingViewResolver implements ViewResolver {

    private final Map<String, View> viewCache = new ConcurrentHashMap<>();
    private final String prefix;
    private final String suffix;
    private final boolean cache;

    public CachingViewResolver(String prefix, String suffix, boolean cache) {
        this.prefix = prefix;
        this.suffix = suffix;
        this.cache = cache;
    }

    @Override
    public View resolveViewName(String viewName, Locale locale) throws Exception {
        // Check cache first
        if (cache && viewCache.containsKey(viewName)) {
            return viewCache.get(viewName);
        }

        // Special handling for certain view names
        if (viewName.startsWith("redirect:") || viewName.startsWith("forward:")) {
            return null; // Let another resolver handle it
        }

        // Construct full path
        String fullPath = prefix + viewName + suffix;

        // Create view
        InternalResourceView view = new InternalResourceView(fullPath);

        // Cache if enabled
        if (cache) {
            viewCache.put(viewName, view);
        }

        return view;
    }

    public void clearCache() {
        viewCache.clear();
    }
}
```

---

## View Resolution Patterns

### Redirect and Forward

```java
@Controller
public class RedirectController {

    /**
     * Redirect to another URL
     * Causes browser to make new request
     */
    @PostMapping("/save")
    public String save() {
        // Process save
        return "redirect:/success";  // Redirects to /success
    }

    /**
     * Redirect with path variables
     */
    @PostMapping("/user")
    public String createUser() {
        User user = userService.save();
        return "redirect:/user/" + user.getId();
    }

    /**
     * Redirect to external URL
     */
    @GetMapping("/external")
    public String external() {
        return "redirect:https://www.example.com";
    }

    /**
     * Forward to another handler
     * Server-side forward, no new browser request
     */
    @GetMapping("/old-url")
    public String forward() {
        return "forward:/new-url";
    }

    /**
     * Redirect with flash attributes
     */
    @PostMapping("/form")
    public String submitForm(RedirectAttributes redirectAttributes) {
        redirectAttributes.addFlashAttribute("message", "Success!");
        return "redirect:/result";
    }
}
```

### Multiple View Technologies

```java
@Configuration
public class MixedViewConfig {

    /**
     * Thymeleaf for HTML pages
     */
    @Bean
    public ViewResolver thymeleafViewResolver() {
        ThymeleafViewResolver resolver = new ThymeleafViewResolver();
        resolver.setTemplateEngine(templateEngine());
        resolver.setViewNames(new String[] {"thymeleaf/*"}); // Only for specific patterns
        resolver.setOrder(1);
        return resolver;
    }

    /**
     * JSP for legacy pages
     */
    @Bean
    public ViewResolver jspViewResolver() {
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setPrefix("/WEB-INF/jsp/");
        resolver.setSuffix(".jsp");
        resolver.setViewNames(new String[] {"jsp/*"}); // Only for specific patterns
        resolver.setOrder(2);
        return resolver;
    }
}
```

**Controller**
```java
@Controller
public class MixedViewController {

    @GetMapping("/modern")
    public String modernPage() {
        return "thymeleaf/modern";  // Uses Thymeleaf
    }

    @GetMapping("/legacy")
    public String legacyPage() {
        return "jsp/legacy";  // Uses JSP
    }
}
```

---

## Locale-Based View Resolution

```java
@Configuration
public class LocaleConfig {

    @Bean
    public LocaleResolver localeResolver() {
        SessionLocaleResolver resolver = new SessionLocaleResolver();
        resolver.setDefaultLocale(Locale.US);
        return resolver;
    }

    @Bean
    public LocaleChangeInterceptor localeChangeInterceptor() {
        LocaleChangeInterceptor interceptor = new LocaleChangeInterceptor();
        interceptor.setParamName("lang");
        return interceptor;
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(localeChangeInterceptor());
    }
}
```

**View Resolution by Locale**
```
Request: /home?lang=en
View: /WEB-INF/views/home_en.jsp

Request: /home?lang=es
View: /WEB-INF/views/home_es.jsp

Request: /home (default)
View: /WEB-INF/views/home.jsp
```

---

## Best Practices

### 1. Order ViewResolvers Correctly

```java
// Specific resolvers first, general ones last
@Bean
public ViewResolver beanNameViewResolver() {
    BeanNameViewResolver resolver = new BeanNameViewResolver();
    resolver.setOrder(0);  // Highest priority
    return resolver;
}

@Bean
public ViewResolver jspViewResolver() {
    InternalResourceViewResolver resolver = new InternalResourceViewResolver();
    resolver.setOrder(Integer.MAX_VALUE);  // Lowest priority (last resort)
    return resolver;
}
```

### 2. Use Prefixes to Organize Views

```java
@Bean
public ViewResolver viewResolver() {
    InternalResourceViewResolver resolver = new InternalResourceViewResolver();
    resolver.setPrefix("/WEB-INF/views/");
    resolver.setSuffix(".jsp");
    return resolver;
}
```

**Directory Structure**
```
/WEB-INF/views/
├── home.jsp
├── users/
│   ├── list.jsp
│   ├── detail.jsp
│   └── form.jsp
├── products/
│   ├── list.jsp
│   └── detail.jsp
└── common/
    ├── header.jsp
    └── footer.jsp
```

### 3. Disable Caching in Development

```java
@Bean
public ViewResolver viewResolver() {
    InternalResourceViewResolver resolver = new InternalResourceViewResolver();
    resolver.setPrefix("/WEB-INF/views/");
    resolver.setSuffix(".jsp");
    resolver.setCache(false);  // Disable caching during development
    return resolver;
}
```

### 4. Set Correct Content Type

```java
@Bean
public ViewResolver viewResolver() {
    InternalResourceViewResolver resolver = new InternalResourceViewResolver();
    resolver.setPrefix("/WEB-INF/views/");
    resolver.setSuffix(".jsp");
    resolver.setContentType("text/html;charset=UTF-8");
    return resolver;
}
```

### 5. Use View Names Consistently

```java
// GOOD: Consistent naming
return "users/list";      // /WEB-INF/views/users/list.jsp
return "users/detail";    // /WEB-INF/views/users/detail.jsp
return "users/form";      // /WEB-INF/views/users/form.jsp

// BAD: Inconsistent naming
return "userList";
return "user-detail";
return "userForm";
```

---

## Common Configuration Examples

### Spring Boot with Thymeleaf

**application.properties**
```properties
# Thymeleaf configuration
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
spring.thymeleaf.mode=HTML
spring.thymeleaf.encoding=UTF-8
spring.thymeleaf.cache=false

# Disable Thymeleaf cache in development
spring.thymeleaf.cache=false
```

### Spring Boot with JSP

**application.properties**
```properties
# JSP configuration
spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp

# Enable JSTL
spring.mvc.view.view-class=org.springframework.web.servlet.view.JstlView
```

**pom.xml**
```xml
<!-- Required for JSP in Spring Boot -->
<dependency>
    <groupId>org.apache.tomcat.embed</groupId>
    <artifactId>tomcat-embed-jasper</artifactId>
    <scope>provided</scope>
</dependency>

<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
</dependency>
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Purpose | Resolve logical view names to actual view implementations |
| Common Types | InternalResourceViewResolver, ThymeleafViewResolver, FreeMarkerViewResolver |
| Configuration | Prefix, suffix, order, caching |
| Chain | Multiple resolvers with different priorities |
| Patterns | Redirect, forward, locale-based resolution |
| Best Practices | Correct ordering, organized structure, consistent naming |

## Next Topic

Continue to [Model and View](./05-model-and-view.md) to learn about passing data to views in Spring MVC.
