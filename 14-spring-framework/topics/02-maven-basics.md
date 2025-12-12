# Maven Basics

## Introduction to Maven

Apache Maven is a powerful build automation and project management tool primarily used for Java projects. Maven addresses two key aspects of building software: how software is built and its dependencies.

### What is Maven?

Maven is based on the concept of a Project Object Model (POM). It can manage a project's build, reporting, and documentation from a central piece of information defined in an XML file called `pom.xml`.

### Key Features

- **Dependency Management**: Automatically downloads and manages project dependencies
- **Standardized Build Process**: Uniform build system across projects
- **Project Information**: Centralized project metadata and documentation
- **Plugin Architecture**: Extensible through plugins for various tasks
- **Repository System**: Central and local repositories for artifact management
- **Multi-module Projects**: Support for complex project hierarchies

### Why Maven for Spring Projects?

1. **Simplified Dependency Management**: Spring has many dependencies; Maven handles them
2. **Transitive Dependencies**: Automatically resolves dependencies of dependencies
3. **Version Management**: Easy to manage Spring version across all modules
4. **Build Lifecycle**: Standardized compilation, testing, packaging, deployment
5. **Integration**: Works seamlessly with IDEs and CI/CD tools

---

## Maven Installation and Setup

### Prerequisites

- Java Development Kit (JDK) 8 or higher installed
- JAVA_HOME environment variable set

### Installation

**macOS (using Homebrew):**
```bash
brew install maven
```

**Linux (Ubuntu/Debian):**
```bash
sudo apt update
sudo apt install maven
```

**Windows:**
1. Download Maven from https://maven.apache.org/download.cgi
2. Extract to C:\Program Files\Apache\maven
3. Add bin directory to PATH environment variable

### Verify Installation

```bash
mvn --version
```

Expected output:
```
Apache Maven 3.9.x
Maven home: /usr/local/Cellar/maven/3.9.x
Java version: 17.0.x, vendor: Oracle Corporation
```

### Maven Configuration

Maven uses `settings.xml` for configuration:

**Global settings**: `$MAVEN_HOME/conf/settings.xml`
**User settings**: `~/.m2/settings.xml`

Basic `settings.xml`:
```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0">
    <localRepository>${user.home}/.m2/repository</localRepository>

    <mirrors>
        <!-- Optional: Add mirrors for faster downloads -->
    </mirrors>

    <profiles>
        <!-- Optional: Add profiles for different environments -->
    </profiles>
</settings>
```

---

## Maven Project Structure

Maven enforces a standard directory layout that makes projects consistent and easy to navigate.

### Standard Directory Layout

```
my-spring-app/
│
├── pom.xml                     # Project Object Model file
│
├── src/
│   ├── main/
│   │   ├── java/              # Application source code
│   │   │   └── com/
│   │   │       └── example/
│   │   │           └── App.java
│   │   │
│   │   ├── resources/         # Application resources
│   │   │   ├── application.properties
│   │   │   ├── log4j2.xml
│   │   │   └── spring/
│   │   │       └── applicationContext.xml
│   │   │
│   │   └── webapp/            # Web application resources (if web app)
│   │       ├── WEB-INF/
│   │       │   └── web.xml
│   │       └── index.html
│   │
│   └── test/
│       ├── java/              # Test source code
│       │   └── com/
│       │       └── example/
│       │           └── AppTest.java
│       │
│       └── resources/         # Test resources
│           └── test.properties
│
└── target/                    # Compiled code and packages (generated)
    ├── classes/
    ├── test-classes/
    └── my-spring-app-1.0.jar
```

### Directory Purposes

| Directory | Purpose |
|-----------|---------|
| `src/main/java` | Application Java source code |
| `src/main/resources` | Application resources (config files, properties) |
| `src/main/webapp` | Web application files (JSP, HTML, CSS, JS) |
| `src/test/java` | Test Java source code |
| `src/test/resources` | Test resources |
| `target` | Compiled bytecode and packaged artifacts |

### Benefits of Standard Structure

- **Consistency**: All Maven projects follow same structure
- **Convention over Configuration**: No need to configure source directories
- **Tool Integration**: IDEs automatically recognize Maven structure
- **Ease of Navigation**: Developers know where to find files

---

## Project Object Model (POM)

The `pom.xml` file is the core of a Maven project, containing all project configuration.

### Basic POM Structure

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <!-- Project Coordinates -->
    <groupId>com.example</groupId>
    <artifactId>my-spring-app</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <!-- Project Information -->
    <name>My Spring Application</name>
    <description>A sample Spring application</description>

    <!-- Properties -->
    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <spring.version>6.1.0</spring.version>
    </properties>

    <!-- Dependencies -->
    <dependencies>
        <!-- Spring dependencies will be added here -->
    </dependencies>

    <!-- Build Configuration -->
    <build>
        <plugins>
            <!-- Build plugins will be added here -->
        </plugins>
    </build>
</project>
```

### Project Coordinates (GAV)

Every Maven project is identified by coordinates:

- **groupId**: Organization or group (e.g., `com.example`, `org.springframework`)
- **artifactId**: Project name (e.g., `my-spring-app`)
- **version**: Project version (e.g., `1.0.0-SNAPSHOT`)

Together, these form the unique identifier: `groupId:artifactId:version`

### Spring Project POM Example

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>spring-demo</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <spring.version>6.1.0</spring.version>
    </properties>

    <dependencies>
        <!-- Spring Core -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <!-- Spring AOP -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <!-- AspectJ for AOP -->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.20</version>
        </dependency>

        <!-- Testing -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${spring.version}</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <version>5.10.0</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.11.0</version>
            </plugin>

            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.1.2</version>
            </plugin>
        </plugins>
    </build>
</project>
```

### Dependency Scopes

Maven dependencies can have different scopes that control when they're available:

```xml
<dependencies>
    <!-- Compile scope (default): available in all classpaths -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>6.1.0</version>
        <scope>compile</scope>
    </dependency>

    <!-- Test scope: only available during testing -->
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>5.10.0</version>
        <scope>test</scope>
    </dependency>

    <!-- Provided scope: available at compile time, expected at runtime -->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>4.0.1</version>
        <scope>provided</scope>
    </dependency>

    <!-- Runtime scope: not needed for compilation, needed at runtime -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.33</version>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

---

## Maven Build Lifecycle

Maven follows a well-defined build lifecycle consisting of phases. Each phase represents a stage in the lifecycle.

### Three Built-in Lifecycles

1. **default**: Main build lifecycle (compile, test, package, deploy)
2. **clean**: Cleans the project (removes target directory)
3. **site**: Generates project documentation

### Default Lifecycle Phases

```
validate → compile → test → package → verify → install → deploy
```

#### Phase Details

| Phase | Description |
|-------|-------------|
| `validate` | Validate project structure and information |
| `compile` | Compile source code |
| `test` | Run unit tests |
| `package` | Package compiled code (JAR/WAR) |
| `verify` | Run integration tests |
| `install` | Install package to local repository |
| `deploy` | Deploy package to remote repository |

### Phase Execution

When you execute a phase, all preceding phases are executed first:

```bash
mvn package
```

This executes: validate → compile → test → package

### Lifecycle Visualization

```
┌──────────────────────────────────────────────┐
│          Maven Build Lifecycle               │
├──────────────────────────────────────────────┤
│                                              │
│  1. validate   ─┐                           │
│                 │                            │
│  2. compile    ←┘                           │
│                 │                            │
│  3. test       ←┘                           │
│                 │                            │
│  4. package    ←┘                           │
│                 │                            │
│  5. verify     ←┘                           │
│                 │                            │
│  6. install    ←┘                           │
│                 │                            │
│  7. deploy     ←┘                           │
│                                              │
└──────────────────────────────────────────────┘
```

---

## Essential Maven Commands

### Project Creation

Create a new Maven project from archetype:

```bash
mvn archetype:generate \
    -DgroupId=com.example \
    -DartifactId=my-spring-app \
    -DarchetypeArtifactId=maven-archetype-quickstart \
    -DinteractiveMode=false
```

### Build Commands

```bash
# Clean the project (delete target directory)
mvn clean

# Compile the source code
mvn compile

# Compile and run tests
mvn test

# Package the application (JAR/WAR)
mvn package

# Clean and package in one command
mvn clean package

# Install to local repository
mvn install

# Deploy to remote repository
mvn deploy
```

### Dependency Commands

```bash
# Download dependencies
mvn dependency:resolve

# Display dependency tree
mvn dependency:tree

# Copy dependencies to target/dependency
mvn dependency:copy-dependencies

# Analyze dependencies for issues
mvn dependency:analyze
```

### Running the Application

```bash
# Execute a main class
mvn exec:java -Dexec.mainClass="com.example.Application"

# Run with Spring Boot
mvn spring-boot:run
```

### Skipping Tests

```bash
# Skip test execution
mvn package -DskipTests

# Skip test compilation and execution
mvn package -Dmaven.test.skip=true
```

### Verbose Output

```bash
# Debug mode with detailed output
mvn -X package

# Show error stacktraces
mvn -e package
```

### Multi-module Commands

```bash
# Build all modules
mvn clean install

# Build specific module
mvn clean install -pl module-name

# Build module and its dependencies
mvn clean install -pl module-name -am

# Build without dependencies
mvn clean install -pl module-name -DskipTests
```

---

## Dependency Management

### Transitive Dependencies

Maven automatically resolves dependencies of your dependencies:

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>6.1.0</version>
</dependency>
```

This automatically includes:
- spring-core
- spring-beans
- spring-aop
- spring-expression
- And their dependencies

### Dependency Tree

View all dependencies:

```bash
mvn dependency:tree
```

Output:
```
[INFO] com.example:my-spring-app:jar:1.0.0
[INFO] +- org.springframework:spring-context:jar:6.1.0:compile
[INFO] |  +- org.springframework:spring-aop:jar:6.1.0:compile
[INFO] |  +- org.springframework:spring-beans:jar:6.1.0:compile
[INFO] |  +- org.springframework:spring-core:jar:6.1.0:compile
[INFO] |  |  \- org.springframework:spring-jcl:jar:6.1.0:compile
[INFO] |  \- org.springframework:spring-expression:jar:6.1.0:compile
```

### Dependency Management Section

Centralize version management:

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-framework-bom</artifactId>
            <version>6.1.0</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

<dependencies>
    <!-- No version needed - inherited from BOM -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
    </dependency>
</dependencies>
```

### Excluding Transitive Dependencies

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>6.1.0</version>
    <exclusions>
        <exclusion>
            <groupId>commons-logging</groupId>
            <artifactId>commons-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

---

## Maven Repositories

### Repository Types

```
┌─────────────────────────────────────────────┐
│                                             │
│              Your Project                   │
│                                             │
└──────────────┬──────────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────────┐
│         Local Repository                    │
│         (~/.m2/repository)                  │
└──────────────┬──────────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────────┐
│         Central Repository                  │
│      (https://repo.maven.apache.org)        │
└─────────────────────────────────────────────┘
```

### Local Repository

Located at `~/.m2/repository` by default. Maven searches here first before downloading.

### Central Repository

Maven's default remote repository (https://repo.maven.apache.org/maven2)

### Custom Repositories

```xml
<repositories>
    <repository>
        <id>spring-milestones</id>
        <name>Spring Milestones</name>
        <url>https://repo.spring.io/milestone</url>
    </repository>
</repositories>
```

---

## Best Practices

### 1. Use Properties for Versions

```xml
<properties>
    <spring.version>6.1.0</spring.version>
    <junit.version>5.10.0</junit.version>
</properties>

<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>${spring.version}</version>
    </dependency>
</dependencies>
```

### 2. Use Dependency Management

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-framework-bom</artifactId>
            <version>${spring.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

### 3. Keep Dependencies Updated

```bash
# Check for updates
mvn versions:display-dependency-updates

# Update versions
mvn versions:use-latest-versions
```

### 4. Minimal Dependencies

Only include dependencies you actually use. Review with:

```bash
mvn dependency:analyze
```

### 5. Use Appropriate Scopes

- `compile`: Default, needed everywhere
- `test`: Only for testing
- `provided`: Supplied by container
- `runtime`: Not needed for compilation

---

## Summary

| Concept | Key Points |
|---------|------------|
| Maven | Build automation and dependency management tool |
| Structure | Standard directory layout (src/main/java, src/test/java) |
| POM | Project Object Model in pom.xml, defines project configuration |
| Lifecycle | validate → compile → test → package → install → deploy |
| Commands | clean, compile, test, package, install, deploy |
| Dependencies | Automatic download and transitive dependency resolution |
| Repositories | Local, Central, and custom repositories |

## Next Topic

Continue to [Inversion of Control](./03-inversion-of-control.md) to learn about Spring's core principle of IoC.
