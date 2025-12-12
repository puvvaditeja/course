# Spring Boot CLI

## Introduction

The Spring Boot Command Line Interface (CLI) is a command-line tool that allows you to quickly prototype Spring applications. You can run Groovy scripts, which means you can write Spring applications with minimal boilerplate code.

### What is Spring Boot CLI?

The Spring Boot CLI is a tool for:
- Rapid prototyping
- Writing Spring applications in Groovy
- Testing Spring concepts quickly
- Creating projects from the command line
- Running Spring Boot applications without build tools

**Key Features:**
- No build file needed (no pom.xml or build.gradle)
- Automatic dependency resolution
- Groovy scripting support
- Quick application scaffolding
- Built-in commands for common tasks

---

## Installation

### macOS Installation

#### Using Homebrew

```bash
# Install
brew tap spring-io/tap
brew install spring-boot

# Verify installation
spring --version
```

#### Using SDKMAN!

```bash
# Install SDKMAN! first
curl -s "https://get.sdkman.io" | bash
source "$HOME/.sdkman/bin/sdkman-init.sh"

# Install Spring Boot CLI
sdk install springboot

# Verify installation
spring --version
```

### Linux Installation

#### Using SDKMAN!

```bash
# Install SDKMAN!
curl -s "https://get.sdkman.io" | bash
source "$HOME/.sdkman/bin/sdkman-init.sh"

# Install Spring Boot CLI
sdk install springboot

# Verify
spring --version
```

#### Manual Installation

```bash
# Download
wget https://repo.spring.io/release/org/springframework/boot/spring-boot-cli/3.2.0/spring-boot-cli-3.2.0-bin.tar.gz

# Extract
tar -xzf spring-boot-cli-3.2.0-bin.tar.gz

# Add to PATH
export PATH=$PATH:/path/to/spring-3.2.0/bin

# Verify
spring --version
```

### Windows Installation

#### Using Chocolatey

```powershell
# Install Chocolatey package manager first, then:
choco install springbootcli

# Verify
spring --version
```

#### Using SDKMAN! on Windows (Git Bash)

```bash
# In Git Bash
curl -s "https://get.sdkman.io" | bash
source "$HOME/.sdkman/bin/sdkman-init.sh"
sdk install springboot
spring --version
```

#### Manual Installation

1. Download ZIP from https://repo.spring.io/release/org/springframework/boot/spring-boot-cli/
2. Extract to a directory
3. Add `bin` directory to PATH
4. Verify with `spring --version`

---

## CLI Commands

### Basic Commands

```bash
# Show version
spring --version

# Show help
spring --help
spring help

# Show help for specific command
spring help run
spring help init
```

### Available Commands

| Command | Description |
|---------|-------------|
| run | Run a Spring Boot application |
| test | Run tests for a Spring Boot application |
| grab | Download dependencies |
| jar | Create executable JAR file |
| war | Create deployable WAR file |
| install | Install dependencies to local repository |
| uninstall | Uninstall dependencies from local repository |
| init | Initialize a new project using start.spring.io |
| encodepassword | Encode a password for Spring Security |
| shell | Start interactive shell |
| help | Display help information |

---

## Running Groovy Scripts

### Simple Hello World

```groovy
// hello.groovy
@RestController
class HelloController {

    @GetMapping("/")
    String home() {
        "Hello, Spring Boot CLI!"
    }
}
```

```bash
# Run the script
spring run hello.groovy

# Access the application
curl http://localhost:8080/
# Output: Hello, Spring Boot CLI!
```

### REST API Example

```groovy
// app.groovy
@RestController
class UserController {

    @GetMapping("/users")
    List<Map<String, String>> getUsers() {
        [
            [name: "John Doe", email: "john@example.com"],
            [name: "Jane Smith", email: "jane@example.com"]
        ]
    }

    @GetMapping("/users/{id}")
    Map<String, String> getUser(@PathVariable String id) {
        [id: id, name: "User ${id}", email: "user${id}@example.com"]
    }

    @PostMapping("/users")
    Map<String, String> createUser(@RequestBody Map<String, String> user) {
        user.id = UUID.randomUUID().toString()
        user
    }
}
```

```bash
# Run the application
spring run app.groovy

# Test endpoints
curl http://localhost:8080/users
curl http://localhost:8080/users/1
curl -X POST http://localhost:8080/users \
  -H "Content-Type: application/json" \
  -d '{"name":"New User","email":"new@example.com"}'
```

### Database Example with JPA

```groovy
// database.groovy
@Grab("spring-boot-starter-data-jpa")
@Grab("h2")

@Entity
class User {
    @Id
    @GeneratedValue
    Long id
    String name
    String email
}

@Repository
interface UserRepository extends CrudRepository<User, Long> {
    List<User> findByName(String name)
}

@RestController
class UserController {

    @Autowired
    UserRepository userRepository

    @GetMapping("/users")
    Iterable<User> getAllUsers() {
        userRepository.findAll()
    }

    @PostMapping("/users")
    User createUser(@RequestBody User user) {
        userRepository.save(user)
    }

    @GetMapping("/users/search")
    List<User> searchByName(@RequestParam String name) {
        userRepository.findByName(name)
    }
}

@Configuration
class DataLoader implements CommandLineRunner {

    @Autowired
    UserRepository userRepository

    void run(String... args) {
        userRepository.save(new User(name: "John Doe", email: "john@example.com"))
        userRepository.save(new User(name: "Jane Smith", email: "jane@example.com"))
    }
}
```

```bash
# Run with JPA
spring run database.groovy

# Test
curl http://localhost:8080/users
curl -X POST http://localhost:8080/users \
  -H "Content-Type: application/json" \
  -d '{"name":"Bob","email":"bob@example.com"}'
```

---

## Using @Grab for Dependencies

### What is @Grab?

`@Grab` is a Groovy annotation that downloads and includes dependencies automatically.

### Common Dependencies

```groovy
// Web application
@Grab("spring-boot-starter-web")

// Data JPA
@Grab("spring-boot-starter-data-jpa")

// Database drivers
@Grab("h2")
@Grab("mysql:mysql-connector-java:8.0.33")
@Grab("postgresql:postgresql:42.6.0")

// Security
@Grab("spring-boot-starter-security")

// Actuator
@Grab("spring-boot-starter-actuator")

// Testing
@Grab("spring-boot-starter-test")
```

### Example with Multiple Dependencies

```groovy
// full-app.groovy
@Grab("spring-boot-starter-web")
@Grab("spring-boot-starter-data-jpa")
@Grab("spring-boot-starter-actuator")
@Grab("h2")

@Entity
class Product {
    @Id
    @GeneratedValue
    Long id
    String name
    BigDecimal price
}

@Repository
interface ProductRepository extends JpaRepository<Product, Long> {
    List<Product> findByNameContaining(String name)
}

@RestController
@RequestMapping("/api/products")
class ProductController {

    @Autowired
    ProductRepository productRepository

    @GetMapping
    List<Product> getAll() {
        productRepository.findAll()
    }

    @GetMapping("/{id}")
    Product getOne(@PathVariable Long id) {
        productRepository.findById(id).orElseThrow()
    }

    @PostMapping
    Product create(@RequestBody Product product) {
        productRepository.save(product)
    }

    @PutMapping("/{id}")
    Product update(@PathVariable Long id, @RequestBody Product product) {
        product.id = id
        productRepository.save(product)
    }

    @DeleteMapping("/{id}")
    void delete(@PathVariable Long id) {
        productRepository.deleteById(id)
    }

    @GetMapping("/search")
    List<Product> search(@RequestParam String name) {
        productRepository.findByNameContaining(name)
    }
}
```

---

## Project Initialization

### Using spring init

```bash
# Initialize a new project
spring init my-project

# Initialize with specific dependencies
spring init --dependencies=web,data-jpa,h2 my-project

# Specify build tool
spring init --build=gradle my-project

# Specify Java version
spring init --java-version=17 my-project

# Specify Spring Boot version
spring init --boot-version=3.2.0 my-project

# Full example
spring init \
  --dependencies=web,data-jpa,security,actuator \
  --build=maven \
  --java-version=17 \
  --boot-version=3.2.0 \
  --packaging=jar \
  --group=com.example \
  --artifact=myapp \
  --name=MyApplication \
  --description="My Spring Boot App" \
  --package-name=com.example.myapp \
  myapp
```

### List Available Options

```bash
# List all dependencies
spring init --list

# Output shows:
# Capabilities:
# - dependencies
# - boot-versions
# - java-versions
# - packaging
# - build-tools
# - languages

# View specific capability
spring init --list dependencies
```

### Initialize Different Project Types

```bash
# REST API
spring init --dependencies=web,data-jpa,mysql rest-api

# Reactive Web Application
spring init --dependencies=webflux,data-mongodb-reactive reactive-app

# Microservice
spring init --dependencies=web,actuator,cloud-eureka microservice

# Batch Processing
spring init --dependencies=batch,data-jpa,h2 batch-app

# Message-Driven Application
spring init --dependencies=kafka,data-jpa messaging-app
```

---

## Running Scripts with Options

### Specify Port

```bash
# Method 1: Command line argument
spring run app.groovy -- --server.port=9090

# Method 2: In the Groovy script
// app.groovy
@Component
@ConfigurationProperties(prefix = "server")
class ServerConfig {
    int port = 9090
}
```

### Watch for Changes

```bash
# Auto-reload on file changes
spring run app.groovy --watch app.groovy
```

### Enable Debug Logging

```bash
# Run with debug
spring run app.groovy -- --debug

# Or with logging level
spring run app.groovy -- --logging.level.root=DEBUG
```

### Set Active Profile

```bash
# Run with specific profile
spring run app.groovy -- --spring.profiles.active=dev
```

---

## Creating Executable JARs and WARs

### Create JAR

```bash
# Create executable JAR from Groovy script
spring jar app.jar app.groovy

# Run the JAR
java -jar app.jar
```

### Create WAR

```bash
# Create deployable WAR
spring war app.war app.groovy

# Deploy to servlet container (Tomcat, etc.)
cp app.war /path/to/tomcat/webapps/
```

### JAR with Dependencies

```groovy
// app.groovy with dependencies
@Grab("spring-boot-starter-web")
@Grab("spring-boot-starter-data-jpa")
@Grab("h2")

@RestController
class HelloController {
    @GetMapping("/")
    String home() {
        "Hello from JAR!"
    }
}
```

```bash
# Create JAR with all dependencies
spring jar myapp.jar app.groovy

# Run
java -jar myapp.jar
```

---

## Interactive Shell

### Start Shell

```bash
# Start interactive shell
spring shell
```

### Shell Commands

```groovy
// In the shell, you can run commands interactively

spring:> run hello.groovy
spring:> jar myapp.jar app.groovy
spring:> init --dependencies=web,data-jpa myproject
spring:> help
spring:> exit
```

---

## Advanced Examples

### Scheduled Tasks

```groovy
// scheduler.groovy
@Grab("spring-boot-starter")

import org.springframework.scheduling.annotation.EnableScheduling
import org.springframework.scheduling.annotation.Scheduled

@Configuration
@EnableScheduling
class SchedulerConfig { }

@Component
class ScheduledTasks {

    @Scheduled(fixedRate = 5000)
    void reportCurrentTime() {
        println "Current time: ${new Date()}"
    }

    @Scheduled(cron = "0 0 9 * * ?")
    void scheduledTask() {
        println "Scheduled task executed at 9 AM"
    }
}
```

### Configuration Properties

```groovy
// config-app.groovy
@Component
@ConfigurationProperties(prefix = "app")
class AppConfig {
    String name
    String description
    Map<String, String> settings
}

@RestController
class ConfigController {

    @Autowired
    AppConfig appConfig

    @GetMapping("/config")
    Map<String, Object> getConfig() {
        [
            name: appConfig.name,
            description: appConfig.description,
            settings: appConfig.settings
        ]
    }
}
```

```properties
# application.properties
app.name=My Application
app.description=A demo application
app.settings.color=blue
app.settings.theme=dark
```

```bash
# Run with custom properties
spring run config-app.groovy
```

### Multiple Files

```groovy
// model.groovy
@Entity
class Task {
    @Id
    @GeneratedValue
    Long id
    String title
    String description
    boolean completed = false
}

// repository.groovy
@Repository
interface TaskRepository extends JpaRepository<Task, Long> {
    List<Task> findByCompleted(boolean completed)
}

// controller.groovy
@RestController
@RequestMapping("/tasks")
class TaskController {

    @Autowired
    TaskRepository taskRepository

    @GetMapping
    List<Task> getAll() {
        taskRepository.findAll()
    }

    @PostMapping
    Task create(@RequestBody Task task) {
        taskRepository.save(task)
    }

    @GetMapping("/pending")
    List<Task> getPending() {
        taskRepository.findByCompleted(false)
    }
}
```

```bash
# Run multiple files
spring run model.groovy repository.groovy controller.groovy
```

---

## Testing with Spring Boot CLI

### Create Test Script

```groovy
// app.groovy
@RestController
class HelloController {
    @GetMapping("/hello")
    String hello(@RequestParam(defaultValue = "World") String name) {
        "Hello, ${name}!"
    }
}

// app-test.groovy
@Grab("spring-boot-starter-test")

@WebMvcTest
class HelloControllerTest {

    @Autowired
    MockMvc mockMvc

    @Test
    void testHello() {
        mockMvc.perform(get("/hello"))
            .andExpect(status().isOk())
            .andExpect(content().string("Hello, World!"))
    }

    @Test
    void testHelloWithName() {
        mockMvc.perform(get("/hello").param("name", "Spring"))
            .andExpect(status().isOk())
            .andExpect(content().string("Hello, Spring!"))
    }
}
```

```bash
# Run tests
spring test app.groovy app-test.groovy
```

---

## CLI Configuration

### Custom Settings

Create `~/.spring/settings.xml`:

```xml
<settings>
    <repositories>
        <repository>
            <id>my-repo</id>
            <url>https://repo.example.com/maven2</url>
        </repository>
    </repositories>
</settings>
```

### Environment Variables

```bash
# Set SPRING_HOME
export SPRING_HOME=/path/to/spring-cli

# Set Java options
export JAVA_OPTS="-Xmx1024m -XX:MaxPermSize=256m"

# Run with custom settings
spring run app.groovy
```

---

## Comparison: CLI vs Traditional

### CLI Approach (Groovy)

```groovy
// app.groovy - 10 lines
@RestController
class HelloController {
    @GetMapping("/")
    String home() {
        "Hello, World!"
    }
}
```

```bash
spring run app.groovy
```

### Traditional Approach (Java)

```xml
<!-- pom.xml - 50+ lines -->
<project>
    <!-- ... extensive configuration ... -->
</project>
```

```java
// Application.java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}

// HelloController.java
@RestController
public class HelloController {
    @GetMapping("/")
    public String home() {
        return "Hello, World!";
    }
}
```

```bash
mvn clean package
java -jar target/app.jar
```

---

## Use Cases for Spring Boot CLI

### When to Use CLI

1. **Quick Prototyping**: Test ideas rapidly
2. **Learning**: Explore Spring features without setup overhead
3. **Demos**: Create quick demonstrations
4. **Scripting**: Write automation scripts
5. **Microservices**: Quickly spin up small services

### When NOT to Use CLI

1. **Production Applications**: Use full Maven/Gradle projects
2. **Large Applications**: Better code organization needed
3. **Team Projects**: Standard build tools are better for collaboration
4. **Complex Builds**: Need custom build configurations

---

## Best Practices

### 1. Organize Scripts

```bash
# Keep related scripts together
my-app/
├── model/
│   └── entities.groovy
├── repository/
│   └── repositories.groovy
├── controller/
│   └── controllers.groovy
└── app.groovy
```

### 2. Use Configuration Files

```groovy
// Load external configuration
@PropertySource("classpath:application.properties")
@Component
class AppConfig { }
```

### 3. Version Control

```bash
# Add to .gitignore
.spring/
*.class
target/
```

### 4. Document Dependencies

```groovy
// app.groovy
// Dependencies:
// - spring-boot-starter-web
// - spring-boot-starter-data-jpa
// - h2

@Grab("spring-boot-starter-web")
@Grab("spring-boot-starter-data-jpa")
@Grab("h2")

// Your code here
```

---

## Troubleshooting

### Common Issues

#### Issue 1: Dependencies Not Downloading

```bash
# Clear Grape cache
rm -rf ~/.groovy/grapes/*
rm -rf ~/.m2/repository/org/springframework/boot/

# Try again
spring run app.groovy
```

#### Issue 2: Port Already in Use

```bash
# Specify different port
spring run app.groovy -- --server.port=9090
```

#### Issue 3: Out of Memory

```bash
# Increase heap size
export JAVA_OPTS="-Xmx2048m"
spring run app.groovy
```

#### Issue 4: Class Not Found

```groovy
// Ensure all imports are explicit
import org.springframework.web.bind.annotation.*
import org.springframework.boot.autoconfigure.*

@RestController
class MyController {
    // Your code
}
```

---

## Summary

| Aspect | Details |
|--------|---------|
| **Purpose** | Rapid prototyping and quick Spring Boot application development |
| **Language** | Groovy scripts |
| **Key Commands** | run, init, jar, war, test, shell |
| **Dependencies** | @Grab annotation for automatic resolution |
| **Use Cases** | Prototyping, learning, demos, scripting |
| **Limitations** | Not recommended for production or large applications |

### Key Takeaways

1. Spring Boot CLI enables rapid development with minimal code
2. Groovy scripts eliminate boilerplate configuration
3. @Grab simplifies dependency management
4. Perfect for prototyping and learning
5. Use traditional approach for production applications

## Next Topic

Continue to [application.properties / .yml](./06-application-properties-yml.md) to learn about configuration management in Spring Boot.
