# Spring MVC Architecture

## What is Spring MVC?

Spring MVC (Model-View-Controller) is a web framework built on the Servlet API and part of the Spring Framework ecosystem. It provides a comprehensive solution for building web applications using the Model-View-Controller design pattern, offering a clean separation of concerns and a flexible architecture for handling HTTP requests and responses.

### Key Characteristics

- **Flexible and Loosely Coupled**: Components are loosely coupled, making it easy to test and maintain
- **Annotation-Based Configuration**: Uses annotations like `@Controller`, `@RequestMapping` for streamlined development
- **Multiple View Technologies**: Supports JSP, Thymeleaf, FreeMarker, and other view technologies
- **Built on Servlet API**: Leverages the standard Java Servlet API
- **RESTful Support**: Excellent support for building RESTful web services
- **Integration with Spring**: Seamless integration with Spring IoC, AOP, and other Spring modules

### Spring MVC vs Traditional Servlets

| Feature | Spring MVC | Traditional Servlets |
|---------|-----------|---------------------|
| Configuration | Annotation-based, minimal XML | Extensive XML configuration |
| Routing | Flexible, annotation-driven | URL patterns in web.xml |
| Data Binding | Automatic form binding | Manual parameter extraction |
| Validation | Built-in validation framework | Manual validation |
| View Resolution | Flexible view resolver | Forward/Redirect manually |
| Testing | Easy to unit test | Difficult to test |

---

## The MVC Design Pattern

The Model-View-Controller pattern separates an application into three interconnected components, each with distinct responsibilities.

### Components

#### Model
- Represents the application data and business logic
- Encapsulates the state of the application
- Responds to requests for information about its state
- Responds to instructions to change state
- Independent of the user interface

#### View
- Renders the model data for presentation to the user
- Generates HTML, JSON, XML, or other formats
- Multiple views can exist for a single model
- Passive - doesn't contain business logic

#### Controller
- Handles user requests and interactions
- Manipulates the model based on user input
- Selects the appropriate view for response
- Acts as an intermediary between model and view

### MVC Flow

```
┌─────────────┐
│   Browser   │
└──────┬──────┘
       │ 1. HTTP Request
       ▼
┌─────────────────────┐
│    Controller       │
│  (Process Request)  │
└──────┬──────┬───────┘
       │      │
       │      └──────────────┐
       │ 2. Update           │ 4. Select View
       ▼                     ▼
┌─────────────┐      ┌──────────────┐
│    Model    │      │     View     │
│  (Data +    │──3──→│  (Render)    │
│   Logic)    │      │              │
└─────────────┘      └──────┬───────┘
                            │ 5. HTTP Response
                            ▼
                     ┌──────────────┐
                     │   Browser    │
                     └──────────────┘
```

---

## Spring MVC Architecture

Spring MVC implements the MVC pattern with additional components that handle the entire request-response lifecycle.

### Core Components

#### 1. DispatcherServlet
- Front Controller that receives all requests
- Delegates request processing to other components
- Handles exceptions and view resolution

#### 2. HandlerMapping
- Maps incoming requests to appropriate handlers (controllers)
- Determines which controller method should process the request
- Based on URL patterns, HTTP methods, headers, etc.

#### 3. Controller
- Contains the application logic
- Processes requests and prepares model data
- Returns view name or model and view

#### 4. ModelAndView
- Container for model data and view name
- Returned by controller methods
- Passed to ViewResolver

#### 5. ViewResolver
- Resolves view names to actual view implementations
- Locates the view template (JSP, Thymeleaf, etc.)
- Configured with prefix and suffix

#### 6. View
- Renders the model data
- Generates the response (HTML, JSON, XML)
- Sends the final output to the client

### Request Processing Flow

```
┌────────────────────────────────────────────────────────────┐
│                        Client/Browser                       │
└────────────────────┬───────────────────────────────────────┘
                     │ HTTP Request
                     ▼
         ┌───────────────────────────┐
         │   DispatcherServlet       │ (Front Controller)
         │   1. Receives request     │
         └───────────┬───────────────┘
                     │
                     ▼
         ┌───────────────────────────┐
         │    HandlerMapping         │
         │   2. Find handler for     │
         │      request URL          │
         └───────────┬───────────────┘
                     │ Returns Controller
                     ▼
         ┌───────────────────────────┐
         │      Controller           │
         │   3. Process request      │
         │   4. Prepare model        │
         │   5. Return view name     │
         └───────────┬───────────────┘
                     │ ModelAndView
                     ▼
         ┌───────────────────────────┐
         │     ViewResolver          │
         │   6. Resolve view name    │
         │      to actual view       │
         └───────────┬───────────────┘
                     │ View Object
                     ▼
         ┌───────────────────────────┐
         │         View              │
         │   7. Render model         │
         │   8. Generate response    │
         └───────────┬───────────────┘
                     │ HTTP Response
                     ▼
         ┌───────────────────────────┐
         │      Client/Browser       │
         └───────────────────────────┘
```

### Detailed Request Flow

1. **Client Request**: User sends HTTP request to the application
2. **DispatcherServlet**: Front controller receives the request
3. **HandlerMapping**: Finds the appropriate controller based on URL mapping
4. **Controller Execution**:
   - Controller method is invoked
   - Business logic is executed
   - Model data is prepared
5. **ModelAndView Return**: Controller returns ModelAndView object
6. **ViewResolver**: Resolves the logical view name to actual view
7. **View Rendering**: View renders the model data
8. **Response**: Final HTML/JSON is sent back to the client

---

## Configuration Approaches

Spring MVC can be configured using XML, Java Configuration, or a combination of both.

### XML Configuration

**web.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
         http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">

    <!-- DispatcherServlet Configuration -->
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/spring-mvc-config.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

**spring-mvc-config.xml**
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

    <!-- Enable component scanning -->
    <context:component-scan base-package="com.example.controller"/>

    <!-- Enable annotation-driven MVC -->
    <mvc:annotation-driven/>

    <!-- ViewResolver -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/views/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!-- Static resource handling -->
    <mvc:resources mapping="/resources/**" location="/resources/"/>
</beans>
```

### Java Configuration (Modern Approach)

**WebAppInitializer.java**
```java
package com.example.config;

import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;

/**
 * Replaces web.xml configuration
 * Automatically configures DispatcherServlet
 */
public class WebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        // Root application context configuration
        return new Class[] { RootConfig.class };
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        // Web application context configuration
        return new Class[] { WebMvcConfig.class };
    }

    @Override
    protected String[] getServletMappings() {
        // URL patterns to map to DispatcherServlet
        return new String[] { "/" };
    }
}
```

**WebMvcConfig.java**
```java
package com.example.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.ViewResolver;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
import org.springframework.web.servlet.view.InternalResourceViewResolver;
import org.springframework.web.servlet.view.JstlView;

/**
 * Spring MVC Configuration
 */
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = "com.example.controller")
public class WebMvcConfig implements WebMvcConfigurer {

    /**
     * Configure ViewResolver for JSP pages
     */
    @Bean
    public ViewResolver viewResolver() {
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setViewClass(JstlView.class);
        resolver.setPrefix("/WEB-INF/views/");
        resolver.setSuffix(".jsp");
        return resolver;
    }

    /**
     * Configure static resource handling
     */
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/resources/**")
                .addResourceLocations("/resources/");
    }
}
```

**RootConfig.java**
```java
package com.example.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;

/**
 * Root application context configuration
 * Contains non-web components (services, repositories, etc.)
 */
@Configuration
@ComponentScan(basePackages = "com.example",
    excludeFilters = {
        @ComponentScan.Filter(type = FilterType.ANNOTATION, value = EnableWebMvc.class)
    })
public class RootConfig {
    // Configuration for business logic layer
}
```

---

## Project Structure

A typical Spring MVC project follows this structure:

```
my-spring-mvc-app/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/
│   │   │       └── example/
│   │   │           ├── config/
│   │   │           │   ├── WebAppInitializer.java
│   │   │           │   ├── WebMvcConfig.java
│   │   │           │   └── RootConfig.java
│   │   │           ├── controller/
│   │   │           │   ├── HomeController.java
│   │   │           │   └── UserController.java
│   │   │           ├── model/
│   │   │           │   └── User.java
│   │   │           ├── service/
│   │   │           │   ├── UserService.java
│   │   │           │   └── UserServiceImpl.java
│   │   │           └── repository/
│   │   │               ├── UserRepository.java
│   │   │               └── UserRepositoryImpl.java
│   │   ├── resources/
│   │   │   ├── application.properties
│   │   │   └── logback.xml
│   │   └── webapp/
│   │       ├── WEB-INF/
│   │       │   ├── views/
│   │       │   │   ├── home.jsp
│   │       │   │   └── user/
│   │       │   │       ├── list.jsp
│   │       │   │       └── detail.jsp
│   │       │   └── web.xml (if using XML config)
│   │       └── resources/
│   │           ├── css/
│   │           ├── js/
│   │           └── images/
│   └── test/
│       └── java/
│           └── com/
│               └── example/
│                   └── controller/
│                       └── HomeControllerTest.java
└── pom.xml
```

---

## Maven Dependencies

**pom.xml**
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>spring-mvc-app</artifactId>
    <version>1.0.0</version>
    <packaging>war</packaging>

    <properties>
        <spring.version>5.3.20</spring.version>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
    </properties>

    <dependencies>
        <!-- Spring MVC -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <!-- Servlet API -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.1</version>
            <scope>provided</scope>
        </dependency>

        <!-- JSP and JSTL -->
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>javax.servlet.jsp-api</artifactId>
            <version>2.3.3</version>
            <scope>provided</scope>
        </dependency>

        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>

        <!-- Logging -->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.2.11</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
                <version>3.3.2</version>
            </plugin>
        </plugins>
    </build>
</project>
```

---

## Simple Example

**HomeController.java**
```java
package com.example.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;

/**
 * Simple controller to demonstrate Spring MVC basics
 */
@Controller
public class HomeController {

    /**
     * Handle requests to home page
     * URL: http://localhost:8080/
     */
    @GetMapping("/")
    public String home() {
        return "home";  // Returns view name "home"
                       // ViewResolver resolves to /WEB-INF/views/home.jsp
    }

    /**
     * Handle requests with parameters
     * URL: http://localhost:8080/greet?name=John
     */
    @GetMapping("/greet")
    public String greet(@RequestParam(name = "name", defaultValue = "Guest") String name,
                       Model model) {
        // Add data to model
        model.addAttribute("message", "Hello, " + name + "!");
        return "greeting";
    }
}
```

**home.jsp**
```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html>
<head>
    <title>Home Page</title>
</head>
<body>
    <h1>Welcome to Spring MVC</h1>
    <p>This is a simple Spring MVC application.</p>
    <a href="/greet?name=Developer">Greet Developer</a>
</body>
</html>
```

**greeting.jsp**
```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html>
<head>
    <title>Greeting</title>
</head>
<body>
    <h1>${message}</h1>
    <a href="/">Back to Home</a>
</body>
</html>
```

---

## Best Practices

### 1. Separation of Concerns
- Keep controllers thin - delegate business logic to services
- Use service layer for business logic
- Use repository/DAO layer for data access

### 2. Use Annotations
- Prefer `@Controller` over XML configuration
- Use `@GetMapping`, `@PostMapping` instead of generic `@RequestMapping`
- Leverage `@RequestParam`, `@PathVariable` for parameter binding

### 3. RESTful URLs
- Use meaningful, resource-oriented URLs
- Follow REST conventions: GET, POST, PUT, DELETE
- Example: `/users/{id}` instead of `/getUser?id=1`

### 4. Exception Handling
- Use `@ExceptionHandler` for controller-specific exceptions
- Use `@ControllerAdvice` for global exception handling
- Return appropriate HTTP status codes

### 5. Validation
- Use Bean Validation (JSR-303) annotations
- Validate input at controller level
- Return meaningful error messages

### 6. Testing
- Write unit tests for controllers
- Use MockMvc for integration testing
- Test business logic separately from web layer

---

## Summary

| Concept | Key Points |
|---------|------------|
| Spring MVC | Web framework implementing MVC pattern on Servlet API |
| Architecture | DispatcherServlet, HandlerMapping, Controller, ViewResolver, View |
| Configuration | XML, Java Config, or Annotation-based |
| Request Flow | Client → DispatcherServlet → Controller → ViewResolver → View → Client |
| Best Practices | Separation of concerns, annotations, RESTful design, testing |

## Next Topic

Continue to [DispatcherServlet](./02-dispatcher-servlet.md) to learn about the front controller in Spring MVC.
