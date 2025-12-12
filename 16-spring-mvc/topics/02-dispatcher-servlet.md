# DispatcherServlet

## What is DispatcherServlet?

The DispatcherServlet is the front controller in Spring MVC framework. It acts as the central entry point for all HTTP requests in a Spring MVC application, routing them to appropriate handlers and coordinating the request-response lifecycle.

### Key Characteristics

- **Front Controller Pattern**: Single servlet handles all incoming requests
- **Request Delegation**: Delegates work to specialized components
- **Pluggable**: Allows customization of all MVC components
- **Thread-Safe**: Handles multiple concurrent requests
- **Context Hierarchy**: Manages application and servlet contexts
- **Exception Handling**: Provides centralized exception management

### Front Controller Pattern

```
Multiple Requests                    Front Controller
┌──────────────┐                   ┌─────────────────┐
│   Request 1  │─────────────────→ │                 │
└──────────────┘                   │                 │
┌──────────────┐                   │  Dispatcher     │
│   Request 2  │─────────────────→ │   Servlet       │
└──────────────┘                   │                 │
┌──────────────┐                   │                 │
│   Request 3  │─────────────────→ │                 │
└──────────────┘                   └────────┬────────┘
                                            │
                          ┌─────────────────┼─────────────────┐
                          │                 │                 │
                          ▼                 ▼                 ▼
                   ┌──────────┐      ┌──────────┐     ┌──────────┐
                   │Controller│      │Controller│     │Controller│
                   │    1     │      │    2     │     │    3     │
                   └──────────┘      └──────────┘     └──────────┘
```

### Benefits of DispatcherServlet

1. **Centralized Request Handling**: Single point of entry for all requests
2. **Simplified Configuration**: One servlet configuration instead of many
3. **Consistent Processing**: Uniform request processing pipeline
4. **Flexible Routing**: Dynamic request routing based on various criteria
5. **Easy Maintenance**: Changes to request processing in one place
6. **Better Testing**: Easier to test and mock

---

## DispatcherServlet Lifecycle

The DispatcherServlet follows the standard servlet lifecycle with additional Spring-specific initialization.

### Lifecycle Phases

```
┌─────────────────────────────────────────────────────────────┐
│                    Container Startup                         │
└───────────────────────────┬─────────────────────────────────┘
                            │
                            ▼
                 ┌──────────────────────┐
                 │   1. Constructor     │  Create DispatcherServlet instance
                 └──────────┬───────────┘
                            │
                            ▼
                 ┌──────────────────────┐
                 │   2. init()          │  Initialize servlet
                 │                      │  - Load configuration
                 │                      │  - Initialize WebApplicationContext
                 │                      │  - Initialize strategies
                 └──────────┬───────────┘
                            │
                            ▼
                 ┌──────────────────────┐
                 │   3. service()       │  Handle requests
                 │   (doGet/doPost)     │  - Process HTTP requests
                 │                      │  - Delegate to handlers
                 │   [Called multiple   │
                 │    times]            │
                 └──────────┬───────────┘
                            │
                            ▼
                 ┌──────────────────────┐
                 │   4. destroy()       │  Cleanup resources
                 │                      │  - Close context
                 │                      │  - Release resources
                 └──────────────────────┘
```

### Initialization Process

```java
public class DispatcherServlet extends FrameworkServlet {

    @Override
    protected void onRefresh(ApplicationContext context) {
        initStrategies(context);
    }

    protected void initStrategies(ApplicationContext context) {
        initMultipartResolver(context);      // File upload support
        initLocaleResolver(context);         // Internationalization
        initThemeResolver(context);          // Theme support
        initHandlerMappings(context);        // URL to handler mapping
        initHandlerAdapters(context);        // Handler execution
        initHandlerExceptionResolvers(context); // Exception handling
        initRequestToViewNameTranslator(context); // Default view names
        initViewResolvers(context);          // View resolution
        initFlashMapManager(context);        // Flash attributes
    }
}
```

---

## Configuration

DispatcherServlet can be configured using XML (web.xml), Java configuration, or Spring Boot.

### XML Configuration (Traditional)

**web.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
         http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">

    <!-- DispatcherServlet Definition -->
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>

        <!-- Context Configuration Location -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/spring/dispatcher-config.xml</param-value>
        </init-param>

        <!-- Load on Startup -->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <!-- DispatcherServlet Mapping -->
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!-- Encoding Filter (Optional but recommended) -->
    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>

    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
</web-app>
```

**dispatcher-config.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="
           http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd
           http://www.springframework.org/schema/mvc
           http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <context:component-scan base-package="com.example.controller"/>
    <mvc:annotation-driven/>

    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/views/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
</beans>
```

### Java Configuration (Modern)

**WebAppInitializer.java**
```java
package com.example.config;

import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;

/**
 * Replaces web.xml configuration
 * Automatically registers and configures DispatcherServlet
 */
public class WebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    /**
     * Root application context configuration classes
     * Loaded by ContextLoaderListener
     */
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[] { RootConfig.class };
    }

    /**
     * Servlet application context configuration classes
     * Loaded by DispatcherServlet
     */
    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[] { WebMvcConfig.class };
    }

    /**
     * URL patterns to map to DispatcherServlet
     */
    @Override
    protected String[] getServletMappings() {
        return new String[] { "/" };
    }

    /**
     * Customize DispatcherServlet registration
     */
    @Override
    protected void customizeRegistration(ServletRegistration.Dynamic registration) {
        registration.setLoadOnStartup(1);
        registration.setInitParameter("throwExceptionIfNoHandlerFound", "true");
    }
}
```

**Alternative: WebApplicationInitializer**
```java
package com.example.config;

import org.springframework.web.WebApplicationInitializer;
import org.springframework.web.context.support.AnnotationConfigWebApplicationContext;
import org.springframework.web.servlet.DispatcherServlet;

import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.ServletRegistration;

/**
 * Manual DispatcherServlet configuration
 * Provides more control over servlet initialization
 */
public class ManualWebAppInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext servletContext) throws ServletException {

        // Create the Spring application context
        AnnotationConfigWebApplicationContext context =
            new AnnotationConfigWebApplicationContext();
        context.register(WebMvcConfig.class);

        // Create and configure DispatcherServlet
        DispatcherServlet dispatcherServlet = new DispatcherServlet(context);
        dispatcherServlet.setThrowExceptionIfNoHandlerFound(true);

        // Register DispatcherServlet
        ServletRegistration.Dynamic registration =
            servletContext.addServlet("dispatcher", dispatcherServlet);

        registration.setLoadOnStartup(1);
        registration.addMapping("/");
        registration.setMultipartConfig(
            new MultipartConfigElement("/tmp", 5242880, 20971520, 0)
        );
    }
}
```

### Spring Boot Configuration (Simplest)

Spring Boot auto-configures DispatcherServlet with sensible defaults.

**Application.java**
```java
package com.example;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * Spring Boot automatically configures DispatcherServlet
 * No manual configuration needed!
 */
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

**application.properties**
```properties
# Customize DispatcherServlet configuration
spring.mvc.servlet.path=/
spring.mvc.throw-exception-if-no-handler-found=true
spring.web.resources.add-mappings=false

# Multipart configuration
spring.servlet.multipart.max-file-size=5MB
spring.servlet.multipart.max-request-size=20MB
```

---

## Request Processing Flow

The DispatcherServlet processes requests through a well-defined sequence of steps.

### Detailed Flow Diagram

```
┌────────────────────────────────────────────────────────────────┐
│                        HTTP Request                             │
└───────────────────────────┬────────────────────────────────────┘
                            │
                            ▼
        ┌───────────────────────────────────────┐
        │  1. DispatcherServlet.doService()    │
        │     - Set context attributes          │
        │     - Prepare request                 │
        └───────────────────┬───────────────────┘
                            │
                            ▼
        ┌───────────────────────────────────────┐
        │  2. DispatcherServlet.doDispatch()   │
        │     - Main request processing         │
        └───────────────────┬───────────────────┘
                            │
                            ▼
        ┌───────────────────────────────────────┐
        │  3. Get Handler                       │
        │     - HandlerMapping.getHandler()     │
        │     - Returns HandlerExecutionChain   │
        └───────────────────┬───────────────────┘
                            │
                            ▼
        ┌───────────────────────────────────────┐
        │  4. Get HandlerAdapter                │
        │     - Find adapter for handler        │
        └───────────────────┬───────────────────┘
                            │
                            ▼
        ┌───────────────────────────────────────┐
        │  5. Execute Interceptors (Pre)        │
        │     - preHandle() methods             │
        └───────────────────┬───────────────────┘
                            │
                            ▼
        ┌───────────────────────────────────────┐
        │  6. Invoke Handler                    │
        │     - Execute controller method       │
        │     - Returns ModelAndView            │
        └───────────────────┬───────────────────┘
                            │
                            ▼
        ┌───────────────────────────────────────┐
        │  7. Execute Interceptors (Post)       │
        │     - postHandle() methods            │
        └───────────────────┬───────────────────┘
                            │
                            ▼
        ┌───────────────────────────────────────┐
        │  8. Process Dispatch Result           │
        │     - Resolve view                    │
        │     - Render view                     │
        └───────────────────┬───────────────────┘
                            │
                            ▼
        ┌───────────────────────────────────────┐
        │  9. Execute Interceptors (After)      │
        │     - afterCompletion() methods       │
        └───────────────────┬───────────────────┘
                            │
                            ▼
        ┌───────────────────────────────────────┐
        │              HTTP Response             │
        └────────────────────────────────────────┘
```

### Code Flow

**Simplified DispatcherServlet.doDispatch()**
```java
protected void doDispatch(HttpServletRequest request, HttpServletResponse response)
        throws Exception {

    HttpServletRequest processedRequest = request;
    HandlerExecutionChain mappedHandler = null;
    ModelAndView mv = null;
    Exception dispatchException = null;

    try {
        // 1. Check for multipart request (file upload)
        processedRequest = checkMultipart(request);

        // 2. Determine handler for the current request
        mappedHandler = getHandler(processedRequest);
        if (mappedHandler == null) {
            noHandlerFound(processedRequest, response);
            return;
        }

        // 3. Determine handler adapter
        HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());

        // 4. Process last-modified header (for GET, HEAD requests)
        String method = request.getMethod();
        boolean isGet = "GET".equals(method);
        if (isGet || "HEAD".equals(method)) {
            long lastModified = ha.getLastModified(request, mappedHandler.getHandler());
            if (lastModified > 0 && new ServletWebRequest(request).checkNotModified(lastModified)) {
                return;
            }
        }

        // 5. Apply preHandle methods of interceptors
        if (!mappedHandler.applyPreHandle(processedRequest, response)) {
            return;
        }

        // 6. Actually invoke the handler (controller method)
        mv = ha.handle(processedRequest, response, mappedHandler.getHandler());

        // 7. Apply default view name if needed
        if (mv != null && !mv.hasView()) {
            mv.setViewName(getDefaultViewName(request));
        }

        // 8. Apply postHandle methods of interceptors
        mappedHandler.applyPostHandle(processedRequest, response, mv);
    }
    catch (Exception ex) {
        dispatchException = ex;
    }

    // 9. Process dispatch result (render view or handle exception)
    processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
}
```

---

## Context Hierarchy

DispatcherServlet creates and manages a WebApplicationContext, which can have a parent context.

### Context Structure

```
┌──────────────────────────────────────────────────┐
│         Root WebApplicationContext               │
│                                                  │
│  - Business Services                             │
│  - Data Access Objects (DAOs)                    │
│  - Data Sources                                  │
│  - Transaction Managers                          │
│  - Infrastructure Beans                          │
│                                                  │
│  Loaded by: ContextLoaderListener                │
│  Configuration: RootConfig.class                 │
└────────────────────┬─────────────────────────────┘
                     │ Parent Context
                     ▼
┌──────────────────────────────────────────────────┐
│      Servlet WebApplicationContext               │
│                                                  │
│  - Controllers                                   │
│  - ViewResolvers                                 │
│  - HandlerMappings                               │
│  - Interceptors                                  │
│  - Web-specific Beans                            │
│                                                  │
│  Loaded by: DispatcherServlet                    │
│  Configuration: WebMvcConfig.class               │
└──────────────────────────────────────────────────┘
```

### Configuration Example

**WebAppInitializer.java**
```java
public class WebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        // Root context - shared by all servlets and filters
        return new Class[] { RootConfig.class };
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        // Servlet context - specific to DispatcherServlet
        return new Class[] { WebMvcConfig.class };
    }

    @Override
    protected String[] getServletMappings() {
        return new String[] { "/" };
    }
}
```

**RootConfig.java**
```java
@Configuration
@ComponentScan(basePackages = "com.example",
    excludeFilters = {
        @ComponentScan.Filter(type = FilterType.ANNOTATION, value = EnableWebMvc.class)
    })
public class RootConfig {
    // Service, Repository, and infrastructure beans
}
```

**WebMvcConfig.java**
```java
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = "com.example.controller")
public class WebMvcConfig implements WebMvcConfigurer {
    // Web-specific beans (controllers, view resolvers, etc.)
}
```

---

## Multiple DispatcherServlets

You can configure multiple DispatcherServlets for different URL patterns.

### Configuration

```java
public class MultipleDispatcherInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext servletContext) throws ServletException {

        // First DispatcherServlet for main application
        AnnotationConfigWebApplicationContext mainContext =
            new AnnotationConfigWebApplicationContext();
        mainContext.register(MainWebConfig.class);

        DispatcherServlet mainServlet = new DispatcherServlet(mainContext);
        ServletRegistration.Dynamic main =
            servletContext.addServlet("mainServlet", mainServlet);
        main.setLoadOnStartup(1);
        main.addMapping("/app/*");

        // Second DispatcherServlet for API
        AnnotationConfigWebApplicationContext apiContext =
            new AnnotationConfigWebApplicationContext();
        apiContext.register(ApiWebConfig.class);

        DispatcherServlet apiServlet = new DispatcherServlet(apiContext);
        ServletRegistration.Dynamic api =
            servletContext.addServlet("apiServlet", apiServlet);
        api.setLoadOnStartup(2);
        api.addMapping("/api/*");
    }
}
```

### Use Cases

- Separate web UI from REST API
- Different configuration for different modules
- Version-specific API endpoints
- Multi-tenant applications

---

## Customization and Extension

### Custom DispatcherServlet

```java
package com.example.config;

import org.springframework.web.servlet.DispatcherServlet;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Custom DispatcherServlet with additional functionality
 */
public class CustomDispatcherServlet extends DispatcherServlet {

    @Override
    protected void doService(HttpServletRequest request, HttpServletResponse response)
            throws Exception {

        // Add custom request attributes
        request.setAttribute("requestStartTime", System.currentTimeMillis());

        // Log request details
        logger.info("Processing request: " + request.getRequestURI());

        try {
            // Call parent implementation
            super.doService(request, response);
        } finally {
            // Calculate processing time
            long startTime = (Long) request.getAttribute("requestStartTime");
            long duration = System.currentTimeMillis() - startTime;
            logger.info("Request processed in " + duration + "ms");
        }
    }

    @Override
    protected void noHandlerFound(HttpServletRequest request, HttpServletResponse response)
            throws Exception {

        // Custom 404 handling
        logger.warn("No handler found for: " + request.getRequestURI());
        response.sendError(HttpServletResponse.SC_NOT_FOUND,
            "The requested resource was not found");
    }
}
```

**Register Custom DispatcherServlet**
```java
public class CustomWebAppInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext servletContext) throws ServletException {

        AnnotationConfigWebApplicationContext context =
            new AnnotationConfigWebApplicationContext();
        context.register(WebMvcConfig.class);

        // Use custom DispatcherServlet
        CustomDispatcherServlet servlet = new CustomDispatcherServlet(context);

        ServletRegistration.Dynamic registration =
            servletContext.addServlet("dispatcher", servlet);
        registration.setLoadOnStartup(1);
        registration.addMapping("/");
    }
}
```

---

## Best Practices

### 1. URL Mapping Patterns

```xml
<!-- Map all requests (recommended for Spring MVC) -->
<url-pattern>/</url-pattern>

<!-- Map specific extensions (older style, not recommended) -->
<url-pattern>*.do</url-pattern>
<url-pattern>*.action</url-pattern>

<!-- Map specific path (for multiple servlets) -->
<url-pattern>/api/*</url-pattern>
```

### 2. Load on Startup

```xml
<!-- Load DispatcherServlet on startup -->
<!-- Lower number = higher priority -->
<load-on-startup>1</load-on-startup>
```

### 3. Character Encoding

Always configure character encoding filter:

```java
@Override
protected Filter[] getServletFilters() {
    CharacterEncodingFilter encodingFilter = new CharacterEncodingFilter();
    encodingFilter.setEncoding("UTF-8");
    encodingFilter.setForceEncoding(true);
    return new Filter[] { encodingFilter };
}
```

### 4. Exception Handling

Enable throwing exception for no handler found:

```java
@Override
protected void customizeRegistration(ServletRegistration.Dynamic registration) {
    registration.setInitParameter("throwExceptionIfNoHandlerFound", "true");
}
```

### 5. Context Organization

- Root context: Services, repositories, infrastructure
- Servlet context: Controllers, web components
- Avoid duplicating beans in both contexts

---

## Common Issues and Solutions

### Issue 1: 404 Error for All Requests

**Cause**: Incorrect URL mapping or missing component scan

**Solution**:
```java
// Ensure correct servlet mapping
@Override
protected String[] getServletMappings() {
    return new String[] { "/" };  // Not "/*"
}

// Ensure component scanning includes controllers
@ComponentScan(basePackages = "com.example.controller")
```

### Issue 2: Static Resources Not Loading

**Cause**: DispatcherServlet intercepts static resource requests

**Solution**:
```java
@Configuration
@EnableWebMvc
public class WebMvcConfig implements WebMvcConfigurer {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/resources/**")
                .addResourceLocations("/resources/");
        registry.addResourceHandler("/static/**")
                .addResourceLocations("/static/");
    }
}
```

### Issue 3: Context Not Loading

**Cause**: Missing or incorrect configuration class

**Solution**:
```java
// Verify configuration classes are specified
@Override
protected Class<?>[] getServletConfigClasses() {
    return new Class[] { WebMvcConfig.class };  // Must not be null
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Purpose | Front controller that handles all HTTP requests |
| Lifecycle | init() → service() → destroy() |
| Configuration | XML (web.xml), Java Config, Spring Boot |
| Context | Creates WebApplicationContext with optional parent |
| Request Flow | Receive → Map → Execute → Resolve View → Render → Response |
| Customization | Extend DispatcherServlet for custom behavior |

## Next Topic

Continue to [Controllers](./03-controllers.md) to learn about handling requests in Spring MVC.
