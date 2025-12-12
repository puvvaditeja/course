# Setup and Configuration

## Setting Up Spring Boot with JPA

Spring Boot makes it incredibly easy to set up a JPA-enabled application with minimal configuration through its auto-configuration capabilities.

### Creating a Spring Boot JPA Project

#### Option 1: Spring Initializr (Recommended)

1. Visit [https://start.spring.io](https://start.spring.io)
2. Configure project metadata:
   - **Project**: Maven or Gradle
   - **Language**: Java
   - **Spring Boot Version**: Latest stable version
   - **Group**: com.example
   - **Artifact**: demo
   - **Packaging**: Jar
   - **Java Version**: 17 or 21

3. Add dependencies:
   - Spring Web
   - Spring Data JPA
   - Database driver (H2, MySQL, PostgreSQL)
   - Lombok (optional, but recommended)

4. Click "Generate" to download the project

#### Option 2: Using Spring CLI

```bash
spring init --dependencies=web,data-jpa,h2,lombok \
    --build=maven \
    --language=java \
    my-jpa-app

cd my-jpa-app
```

#### Option 3: Using IDE

**IntelliJ IDEA:**
- File > New > Project
- Select "Spring Initializr"
- Follow the wizard to add dependencies

**Eclipse with Spring Tools:**
- File > New > Spring Starter Project
- Select dependencies and generate

### Project Structure

```
my-jpa-app/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/
│   │   │       └── example/
│   │   │           └── demo/
│   │   │               ├── DemoApplication.java
│   │   │               ├── entity/
│   │   │               ├── repository/
│   │   │               ├── service/
│   │   │               └── controller/
│   │   └── resources/
│   │       ├── application.properties
│   │       └── data.sql (optional)
│   └── test/
│       └── java/
├── pom.xml (or build.gradle)
└── README.md
```

---

## Dependencies Configuration

Understanding and configuring dependencies is crucial for a functioning JPA application.

### Maven Dependencies (pom.xml)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.0</version>
        <relativePath/>
    </parent>

    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>
    <description>Demo project for Spring Boot JPA</description>

    <properties>
        <java.version>17</java.version>
    </properties>

    <dependencies>
        <!-- Spring Boot Starter Data JPA -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>

        <!-- Spring Boot Starter Web (for REST APIs) -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- Database Driver - H2 (in-memory, for development) -->
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>runtime</scope>
        </dependency>

        <!-- Database Driver - MySQL -->
        <dependency>
            <groupId>com.mysql</groupId>
            <artifactId>mysql-connector-j</artifactId>
            <scope>runtime</scope>
        </dependency>

        <!-- Database Driver - PostgreSQL -->
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <scope>runtime</scope>
        </dependency>

        <!-- Lombok (optional, reduces boilerplate) -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>

        <!-- Spring Boot Starter Test -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <!-- Spring Boot Starter Validation -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

### Gradle Dependencies (build.gradle)

```gradle
plugins {
    id 'java'
    id 'org.springframework.boot' version '3.2.0'
    id 'io.spring.dependency-management' version '1.1.4'
}

group = 'com.example'
version = '0.0.1-SNAPSHOT'

java {
    sourceCompatibility = '17'
}

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
}

repositories {
    mavenCentral()
}

dependencies {
    // Spring Boot Starter Data JPA
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'

    // Spring Boot Starter Web
    implementation 'org.springframework.boot:spring-boot-starter-web'

    // Spring Boot Starter Validation
    implementation 'org.springframework.boot:spring-boot-starter-validation'

    // Database Drivers
    runtimeOnly 'com.h2database:h2'
    runtimeOnly 'com.mysql:mysql-connector-j'
    runtimeOnly 'org.postgresql:postgresql'

    // Lombok
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'

    // Testing
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.named('test') {
    useJUnitPlatform()
}
```

### What Each Dependency Does

| Dependency | Purpose |
|------------|---------|
| spring-boot-starter-data-jpa | Core JPA functionality, includes Hibernate |
| spring-boot-starter-web | REST API support, embedded Tomcat |
| Database driver | JDBC driver for specific database |
| lombok | Reduces boilerplate (getters, setters, constructors) |
| spring-boot-starter-validation | Bean validation (JSR-303) |
| spring-boot-starter-test | Testing framework (JUnit, Mockito) |

---

## DataSource Configuration

The DataSource is the connection between your application and the database. Spring Boot auto-configures the DataSource based on properties.

### H2 Database (In-Memory, Development)

**application.properties:**
```properties
# H2 Database Configuration
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

# H2 Console (optional, for debugging)
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

# JPA Properties
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.jpa.hibernate.ddl-auto=create-drop
```

**application.yml:**
```yaml
spring:
  datasource:
    url: jdbc:h2:mem:testdb
    driverClassName: org.h2.Driver
    username: sa
    password:

  h2:
    console:
      enabled: true
      path: /h2-console

  jpa:
    database-platform: org.hibernate.dialect.H2Dialect
    hibernate:
      ddl-auto: create-drop
```

### MySQL Configuration

**application.properties:**
```properties
# MySQL Configuration
spring.datasource.url=jdbc:mysql://localhost:3306/mydb?useSSL=false&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=yourpassword
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# JPA Properties
spring.jpa.database-platform=org.hibernate.dialect.MySQLDialect
spring.jpa.hibernate.ddl-auto=update
```

**application.yml:**
```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mydb?useSSL=false&serverTimezone=UTC
    username: root
    password: yourpassword
    driver-class-name: com.mysql.cj.jdbc.Driver

  jpa:
    database-platform: org.hibernate.dialect.MySQLDialect
    hibernate:
      ddl-auto: update
```

### PostgreSQL Configuration

**application.properties:**
```properties
# PostgreSQL Configuration
spring.datasource.url=jdbc:postgresql://localhost:5432/mydb
spring.datasource.username=postgres
spring.datasource.password=yourpassword
spring.datasource.driver-class-name=org.postgresql.Driver

# JPA Properties
spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect
spring.jpa.hibernate.ddl-auto=update
```

**application.yml:**
```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/mydb
    username: postgres
    password: yourpassword
    driver-class-name: org.postgresql.Driver

  jpa:
    database-platform: org.hibernate.dialect.PostgreSQLDialect
    hibernate:
      ddl-auto: update
```

### Connection Pool Configuration

Spring Boot uses HikariCP as the default connection pool. You can configure it:

```properties
# HikariCP Configuration
spring.datasource.hikari.maximum-pool-size=10
spring.datasource.hikari.minimum-idle=5
spring.datasource.hikari.idle-timeout=300000
spring.datasource.hikari.max-lifetime=1200000
spring.datasource.hikari.connection-timeout=20000
```

### Multiple DataSource Configuration

For applications requiring multiple databases:

```java
@Configuration
public class DataSourceConfig {

    @Primary
    @Bean(name = "primaryDataSource")
    @ConfigurationProperties(prefix = "spring.datasource.primary")
    public DataSource primaryDataSource() {
        return DataSourceBuilder.create().build();
    }

    @Bean(name = "secondaryDataSource")
    @ConfigurationProperties(prefix = "spring.datasource.secondary")
    public DataSource secondaryDataSource() {
        return DataSourceBuilder.create().build();
    }
}
```

---

## JPA Properties

JPA properties control how Hibernate (or other JPA providers) behaves.

### Essential JPA Properties

```properties
# Hibernate DDL auto (create, create-drop, update, validate, none)
spring.jpa.hibernate.ddl-auto=update

# Show SQL queries in console
spring.jpa.show-sql=true

# Format SQL queries for readability
spring.jpa.properties.hibernate.format_sql=true

# Database dialect
spring.jpa.database-platform=org.hibernate.dialect.MySQLDialect

# Naming strategy
spring.jpa.hibernate.naming.physical-strategy=org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
spring.jpa.hibernate.naming.implicit-strategy=org.hibernate.boot.model.naming.ImplicitNamingStrategyLegacyJpaImpl
```

### DDL Auto Options

| Value | Description | Use Case |
|-------|-------------|----------|
| create | Drop and recreate schema on startup | Development (fresh start) |
| create-drop | Create on startup, drop on shutdown | Testing |
| update | Update schema if needed | Development |
| validate | Validate schema, make no changes | Production (verify only) |
| none | No action | Production (manual migrations) |

### Advanced JPA Properties

```properties
# Second-level cache
spring.jpa.properties.hibernate.cache.use_second_level_cache=true
spring.jpa.properties.hibernate.cache.region.factory_class=org.hibernate.cache.jcache.JCacheRegionFactory

# Query cache
spring.jpa.properties.hibernate.cache.use_query_cache=true

# Batch processing
spring.jpa.properties.hibernate.jdbc.batch_size=20
spring.jpa.properties.hibernate.order_inserts=true
spring.jpa.properties.hibernate.order_updates=true

# Statistics (for performance monitoring)
spring.jpa.properties.hibernate.generate_statistics=true

# Lazy loading outside transaction
spring.jpa.properties.hibernate.enable_lazy_load_no_trans=false

# SQL Comments
spring.jpa.properties.hibernate.use_sql_comments=true

# Logging
logging.level.org.hibernate.SQL=DEBUG
logging.level.org.hibernate.type.descriptor.sql.BasicBinder=TRACE
```

### Environment-Specific Configuration

**application-dev.properties:**
```properties
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

**application-prod.properties:**
```properties
spring.jpa.hibernate.ddl-auto=validate
spring.jpa.show-sql=false
spring.jpa.properties.hibernate.format_sql=false
```

**Activate profile:**
```properties
# In application.properties
spring.profiles.active=dev
```

Or via command line:
```bash
java -jar myapp.jar --spring.profiles.active=prod
```

### Configuration Class Approach

For programmatic configuration:

```java
@Configuration
@EnableJpaRepositories(basePackages = "com.example.repository")
@EnableTransactionManagement
public class JpaConfig {

    @Bean
    public LocalContainerEntityManagerFactoryBean entityManagerFactory(
            DataSource dataSource) {
        LocalContainerEntityManagerFactoryBean em =
            new LocalContainerEntityManagerFactoryBean();
        em.setDataSource(dataSource);
        em.setPackagesToScan("com.example.entity");

        JpaVendorAdapter vendorAdapter = new HibernateJpaVendorAdapter();
        em.setJpaVendorAdapter(vendorAdapter);

        Properties properties = new Properties();
        properties.setProperty("hibernate.hbm2ddl.auto", "update");
        properties.setProperty("hibernate.dialect",
            "org.hibernate.dialect.MySQLDialect");
        em.setJpaProperties(properties);

        return em;
    }

    @Bean
    public PlatformTransactionManager transactionManager(
            EntityManagerFactory entityManagerFactory) {
        JpaTransactionManager transactionManager =
            new JpaTransactionManager();
        transactionManager.setEntityManagerFactory(entityManagerFactory);
        return transactionManager;
    }
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Setup | Spring Initializr, Maven/Gradle dependencies, proper structure |
| Dependencies | spring-boot-starter-data-jpa, database drivers, optional utilities |
| DataSource | Connection configuration, database-specific settings, connection pooling |
| JPA Properties | DDL auto, show SQL, dialect, naming strategies, performance tuning |

## Next Topic

Continue to [Entity Mapping](./03-entity-mapping.md) to learn how to map Java classes to database tables.
