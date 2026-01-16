# Logging Frameworks

## Overview
Learn application logging using SLF4J and Log4J 2 for debugging, monitoring, and maintaining production applications.

## Learning Objectives
By the end of this module, you will be able to:
- Understand why logging frameworks are essential over System.out.println
- Use SLF4J as a logging abstraction for framework independence
- Configure Log4J 2 for different environments
- Implement proper log levels, appenders, and layouts
- Apply logging best practices in enterprise applications

## Topics Covered

### 1. [Logging Fundamentals](./topics/01-logging-fundamentals.md)
- Why logging matters (debugging, monitoring, auditing)
- Logging vs System.out.println
- Log levels and their hierarchy (TRACE → FATAL)
- Basic usage patterns and logger best practices

### 2. [SLF4J - Simple Logging Facade](./topics/02-slf4j.md)
- What is SLF4J and why use it
- Framework independence and library compatibility
- Basic and advanced usage (parameterized messages)
- MDC (Mapped Diagnostic Context) for request tracking

### 3. [Log4J 2 Architecture](./topics/03-log4j2-architecture.md)
- Log4J 2 vs Log4J 1.x improvements
- Component layers: Logger → Appender → Layout
- Log event flow and processing
- Async logging and performance features

### 4. [Log4J Configuration](./topics/04-log4j-configuration.md)
- Configuration file formats (XML, Properties)
- Basic and production configuration examples
- Environment-specific settings
- Filters and dynamic configuration

### 5. [Appenders](./topics/05-appenders.md)
- Console Appender for development
- File Appender basics
- Rolling File Appender for production
- Time and size-based rotation strategies

### 6. [Layouts and Patterns](./topics/06-layouts.md)
- Pattern Layout syntax
- Essential conversion patterns (%d, %p, %c, %m, %n)
- Date/time formatting
- Color output for console

### 7. [Best Practices](./topics/07-best-practices.md)
- Code-level best practices
- Security considerations (sensitive data)
- Performance optimization
- Common pitfalls to avoid

## Key Concepts
See [key-concepts.md](./key-concepts.md) for a quick reference card.

## Exercises
See the [exercises](../submissions/13-logging/) directory for hands-on practice.

## Prerequisites
- Core Java fundamentals
- Maven dependency management
- Basic understanding of exceptions

---
**Time Estimate:** 2 days | **Difficulty:** Intermediate
