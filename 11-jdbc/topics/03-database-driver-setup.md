# Setting Up the Database Driver

## Overview

Before connecting to a database, you need to add the appropriate JDBC driver to your project. This guide covers setting up drivers using build tools and manual configuration.

---

## Adding Drivers with Maven

### MySQL Connector/J

```xml
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <version>8.0.33</version>
</dependency>
```

### PostgreSQL Driver

```xml
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
    <version>42.6.0</version>
</dependency>
```

### Oracle JDBC Driver

```xml
<dependency>
    <groupId>com.oracle.database.jdbc</groupId>
    <artifactId>ojdbc8</artifactId>
    <version>21.9.0.0</version>
</dependency>
```

### H2 Database (For Testing)

```xml
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <version>2.2.220</version>
</dependency>
```

---

## Adding Drivers with Gradle

### Groovy DSL (build.gradle)

```groovy
dependencies {
    // MySQL
    implementation 'com.mysql:mysql-connector-j:8.0.33'

    // PostgreSQL
    implementation 'org.postgresql:postgresql:42.6.0'

    // H2
    implementation 'com.h2database:h2:2.2.220'
}
```

### Kotlin DSL (build.gradle.kts)

```kotlin
dependencies {
    implementation("com.mysql:mysql-connector-j:8.0.33")
    implementation("org.postgresql:postgresql:42.6.0")
}
```

---

## Manual JAR Installation

If not using a build tool, download the JAR file and add it to your classpath.

### Download Locations

| Database | Download URL |
|----------|--------------|
| MySQL | https://dev.mysql.com/downloads/connector/j/ |
| PostgreSQL | https://jdbc.postgresql.org/download/ |
| Oracle | https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html |

### Adding to Classpath

**Command Line:**
```bash
# Compile
javac -cp .:mysql-connector-j-8.0.33.jar MyApp.java

# Run
java -cp .:mysql-connector-j-8.0.33.jar MyApp
```

**IDE Configuration:**
1. Right-click on project
2. Go to Build Path / Libraries
3. Add External JARs
4. Select the downloaded driver JAR

---

## Complete Maven Project Setup

### pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>jdbc-demo</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <!-- MySQL Driver -->
        <dependency>
            <groupId>com.mysql</groupId>
            <artifactId>mysql-connector-j</artifactId>
            <version>8.0.33</version>
        </dependency>

        <!-- PostgreSQL Driver (optional) -->
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <version>42.6.0</version>
        </dependency>

        <!-- H2 for testing (optional) -->
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <version>2.2.220</version>
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
        </plugins>
    </build>

</project>
```

---

## Database Configuration File

Create a properties file to store connection details (keep separate from code).

### db.properties

```properties
# Database Configuration
db.driver=com.mysql.cj.jdbc.Driver
db.url=jdbc:mysql://localhost:3306/mydb?useSSL=false&serverTimezone=UTC
db.username=root
db.password=password

# Connection Pool Settings (optional)
db.pool.maxSize=10
db.pool.minIdle=2
```

### Loading Properties in Java

```java
import java.io.IOException;
import java.io.InputStream;
import java.util.Properties;

public class DatabaseConfig {

    private static Properties properties = new Properties();

    static {
        try (InputStream input = DatabaseConfig.class
                .getClassLoader()
                .getResourceAsStream("db.properties")) {

            if (input == null) {
                throw new RuntimeException("Unable to find db.properties");
            }

            properties.load(input);
        } catch (IOException e) {
            throw new RuntimeException("Error loading db.properties", e);
        }
    }

    public static String getUrl() {
        return properties.getProperty("db.url");
    }

    public static String getUsername() {
        return properties.getProperty("db.username");
    }

    public static String getPassword() {
        return properties.getProperty("db.password");
    }

    public static String getDriver() {
        return properties.getProperty("db.driver");
    }
}
```

---

## Verifying Driver Installation

### Test Connection

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class ConnectionTest {

    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/testdb";
        String user = "root";
        String password = "password";

        System.out.println("Testing database connection...");

        try (Connection conn = DriverManager.getConnection(url, user, password)) {
            if (conn != null) {
                System.out.println("Connection successful!");
                System.out.println("Database: " + conn.getCatalog());
                System.out.println("Driver: " + conn.getMetaData().getDriverName());
                System.out.println("Driver Version: " + conn.getMetaData().getDriverVersion());
            }
        } catch (SQLException e) {
            System.err.println("Connection failed!");
            System.err.println("Error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

---

## Common Connection Issues

### Issue 1: Driver Not Found

```
java.sql.SQLException: No suitable driver found for jdbc:mysql://...
```

**Solutions:**
- Verify driver JAR is in classpath
- Check Maven/Gradle dependency is downloaded
- Ensure URL format is correct

### Issue 2: Access Denied

```
java.sql.SQLException: Access denied for user 'user'@'localhost'
```

**Solutions:**
- Verify username and password
- Check user has database privileges
- Verify host access permissions

### Issue 3: Connection Refused

```
com.mysql.cj.jdbc.exceptions.CommunicationsException: Communications link failure
```

**Solutions:**
- Verify database server is running
- Check host and port are correct
- Verify firewall allows connection

### Issue 4: SSL Errors

```
javax.net.ssl.SSLHandshakeException: No appropriate protocol
```

**Solutions:**
- Add `?useSSL=false` to URL (for development only)
- Configure proper SSL certificates for production

---

## Environment-Specific Configuration

### Development

```properties
# db-dev.properties
db.url=jdbc:mysql://localhost:3306/dev_db
db.username=dev_user
db.password=dev_password
```

### Testing

```properties
# db-test.properties
db.url=jdbc:h2:mem:testdb
db.username=sa
db.password=
```

### Production

```properties
# db-prod.properties
db.url=jdbc:mysql://prod-server:3306/prod_db?useSSL=true
db.username=${DB_USER}  # From environment variable
db.password=${DB_PASS}  # From environment variable
```

---

## Security Best Practices

1. **Never hardcode credentials** in source code
2. **Use environment variables** or secret management
3. **Enable SSL/TLS** for production connections
4. **Use least privilege** database users
5. **Rotate passwords** regularly
6. **Don't commit** properties files with real credentials

### Using Environment Variables

```java
String url = System.getenv("DB_URL");
String user = System.getenv("DB_USER");
String password = System.getenv("DB_PASSWORD");

Connection conn = DriverManager.getConnection(url, user, password);
```

---

## Next Topic

Continue to [Setting Up the Utility Class](./04-utility-class.md) to learn how to create a reusable database connection utility.
