# Fullstack the Big Picture

## Understanding the Complete Application Stack

### What is Full Stack Development?

Full stack development refers to working with both the frontend (client-side) and backend (server-side) portions of a web application. A full stack developer understands the entire technology stack required to build a complete application.

### The Application Stack Layers

```
┌─────────────────────────────────────────────────────────────┐
│                      CLIENT TIER                             │
│  ┌─────────────────────────────────────────────────────┐    │
│  │              Frontend Application                    │    │
│  │         (Angular, React, Vue, HTML/CSS/JS)          │    │
│  └─────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
                              │
                              │ HTTP/HTTPS
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      SERVER TIER                             │
│  ┌─────────────────────────────────────────────────────┐    │
│  │              Web Server (Nginx/Apache)               │    │
│  └─────────────────────────────────────────────────────┘    │
│                              │                               │
│  ┌─────────────────────────────────────────────────────┐    │
│  │           Application Server                         │    │
│  │      (Spring Boot, Node.js, Django, etc.)           │    │
│  └─────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
                              │
                              │ JDBC/ORM
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      DATA TIER                               │
│  ┌─────────────────────────────────────────────────────┐    │
│  │                   Database                           │    │
│  │        (PostgreSQL, MySQL, MongoDB, etc.)           │    │
│  └─────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
```

### Technology Stack Overview

For this training program, we'll be working with:

| Layer | Technology | Purpose |
|-------|------------|---------|
| Frontend | Angular | Single-page application framework |
| Backend | Java + Spring Boot | REST API and business logic |
| Database | PostgreSQL | Relational data storage |
| Web Server | Embedded Tomcat / Nginx | HTTP request handling |
| DevOps | Docker, Jenkins | Containerization & CI/CD |
| Version Control | Git | Source code management |

---

## Role of Linux in the Development Ecosystem

### Development Environment

Linux provides the foundation for modern development workflows:

```
Developer Workstation
├── IDE (VS Code, IntelliJ)
├── Local Development
│   ├── Java JDK
│   ├── Node.js / npm
│   ├── Angular CLI
│   └── Docker Desktop
├── Version Control
│   └── Git
└── Terminal / Shell
    └── Bash / Zsh
```

### Production Environment

Most applications are deployed on Linux servers:

```
Production Server (Linux)
├── Web Server (Nginx)
│   └── Serves static files, reverse proxy
├── Application Container (Docker)
│   ├── Spring Boot Application
│   └── JVM Runtime
├── Database Server
│   └── PostgreSQL
└── Monitoring & Logging
    ├── Log files (/var/log/)
    └── System metrics
```

### Why Linux is Essential

1. **Server Standard**: 96%+ of web servers run Linux
2. **Container Runtime**: Docker runs natively on Linux
3. **Cloud Platforms**: AWS, GCP, Azure default to Linux
4. **DevOps Tools**: Most CI/CD tools are Linux-based
5. **Cost Effective**: No licensing fees

---

## Server-Side Considerations

### Application Deployment Flow

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  Developer   │────▶│   Git Repo   │────▶│   CI/CD      │
│  Commits     │     │   (GitHub)   │     │   (Jenkins)  │
└──────────────┘     └──────────────┘     └──────────────┘
                                                  │
                                                  ▼
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  Production  │◀────│   Container  │◀────│    Build     │
│   Server     │     │   Registry   │     │   & Test     │
└──────────────┘     └──────────────┘     └──────────────┘
```

### Key Server Concepts

#### 1. Process Management

Applications run as processes on Linux:

```bash
# View running Java processes
$ ps aux | grep java

# View process tree
$ pstree

# Monitor system resources
$ top
$ htop

# Check application logs
$ tail -f /var/log/app/application.log
```

#### 2. Service Management

Modern Linux uses systemd for service management:

```bash
# Check service status
$ sudo systemctl status nginx
$ sudo systemctl status postgresql

# Start/Stop services
$ sudo systemctl start myapp
$ sudo systemctl stop myapp

# Enable service at boot
$ sudo systemctl enable myapp

# View service logs
$ journalctl -u myapp -f
```

#### 3. Network Configuration

Understanding networking is crucial for server management:

```bash
# Check open ports
$ netstat -tlnp
$ ss -tlnp

# View network interfaces
$ ip addr
$ ifconfig

# Test connectivity
$ ping google.com
$ curl http://localhost:8080/health

# Check firewall rules
$ sudo ufw status
$ sudo iptables -L
```

#### 4. File System for Applications

Typical application directory structure:

```
/opt/myapp/                 # Application installation
├── bin/                    # Executable scripts
├── config/                 # Configuration files
├── lib/                    # Libraries/JARs
└── logs/                   # Application logs

/var/log/myapp/            # Log files
/etc/myapp/                # System configuration
/var/run/myapp/            # Runtime data (PIDs)
```

### Environment Configuration

Applications typically need environment configuration:

```bash
# Application environment variables
export JAVA_HOME=/usr/lib/jvm/java-17
export APP_ENV=production
export DB_HOST=localhost
export DB_PORT=5432
export DB_NAME=myapp
export DB_USER=appuser
export DB_PASSWORD=secret

# Or use .env files
$ cat /opt/myapp/.env
SPRING_PROFILES_ACTIVE=prod
SERVER_PORT=8080
DATABASE_URL=jdbc:postgresql://localhost:5432/myapp
```

### Security Considerations

#### User and Permission Management

```bash
# Create application user (no login shell)
$ sudo useradd -r -s /bin/false appuser

# Set ownership
$ sudo chown -R appuser:appuser /opt/myapp

# Restrict permissions
$ sudo chmod 750 /opt/myapp
$ sudo chmod 640 /opt/myapp/config/*.properties
```

#### Common Security Practices

1. **Principle of Least Privilege**: Run applications with minimal permissions
2. **Firewall Configuration**: Only open necessary ports
3. **Regular Updates**: Keep system and packages updated
4. **Log Monitoring**: Monitor for suspicious activity
5. **Secure Secrets**: Use environment variables or secret managers

---

## The Full Development Workflow

### Local Development

```bash
# 1. Clone repository
$ git clone https://github.com/company/project.git
$ cd project

# 2. Backend development
$ cd backend
$ ./mvnw spring-boot:run

# 3. Frontend development (new terminal)
$ cd frontend
$ npm install
$ ng serve

# 4. Access application
# Frontend: http://localhost:4200
# Backend API: http://localhost:8080
```

### Building for Production

```bash
# Build backend
$ cd backend
$ ./mvnw clean package -DskipTests
# Output: target/app.jar

# Build frontend
$ cd frontend
$ ng build --configuration=production
# Output: dist/frontend/
```

### Containerization

```dockerfile
# Dockerfile for Spring Boot
FROM eclipse-temurin:17-jre
WORKDIR /app
COPY target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

```bash
# Build and run container
$ docker build -t myapp:latest .
$ docker run -d -p 8080:8080 myapp:latest

# View running containers
$ docker ps

# View container logs
$ docker logs -f <container_id>
```

---

## How It All Connects

### Request Flow Example

When a user accesses your application:

```
1. User opens browser
         │
         ▼
2. DNS resolves domain → IP address
         │
         ▼
3. Request hits Linux server (port 80/443)
         │
         ▼
4. Nginx (reverse proxy) receives request
         │
         ├── Static files → Serve directly
         │
         └── API request → Forward to backend
                  │
                  ▼
5. Spring Boot application processes request
         │
         ├── Authentication/Authorization
         ├── Business Logic
         └── Database Query
                  │
                  ▼
6. PostgreSQL executes query
         │
         ▼
7. Response travels back through the stack
         │
         ▼
8. User sees the result in browser
```

### Components Working Together

| Component | Role | Linux Relevance |
|-----------|------|-----------------|
| Browser | Client interface | N/A |
| Nginx | Reverse proxy, SSL termination | Linux service |
| Angular | Frontend SPA | Built with Node.js on Linux |
| Spring Boot | Backend API | Runs on JVM on Linux |
| PostgreSQL | Data persistence | Linux service |
| Docker | Containerization | Linux native |
| Git | Version control | Command-line tool |
| Jenkins | CI/CD automation | Runs on Linux |

---

## Summary

| Concept | Key Takeaway |
|---------|--------------|
| Full Stack | Frontend + Backend + Database working together |
| Linux Role | Foundation for servers, containers, and DevOps |
| Server Management | Process control, services, networking, security |
| Development Flow | Local dev → Git → CI/CD → Production |
| Containers | Docker abstracts OS differences, runs on Linux |

### What's Next?

As you progress through this training, you'll learn each technology in depth:

1. **Git** - Version control for collaboration
2. **SQL/PostgreSQL** - Database fundamentals
3. **Java** - Backend programming language
4. **Spring Boot** - Enterprise application framework
5. **Angular** - Frontend framework
6. **Docker** - Containerization
7. **Jenkins** - CI/CD pipelines

Each technology builds on your Linux foundation. The command-line skills you've learned will be essential throughout your journey as a full stack developer.

---

## Review Questions

1. What are the three main tiers in a typical web application architecture?
2. Why is Linux the dominant operating system for servers?
3. How do containers (Docker) relate to Linux?
4. What is the role of a reverse proxy like Nginx?
5. How do environment variables help in application configuration?

---

[Return to Linux Module Overview](../README.md)
