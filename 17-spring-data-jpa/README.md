# Spring Data JPA

## Overview
Spring Data JPA simplifies database access by providing repository abstractions and query methods.

## Learning Objectives
By the end of this module, you will understand and be able to apply the key concepts and practices of Spring Data JPA.

## Topics Covered

### 1. [JPA Overview](./topics/01-jpa-overview.md)
- What is JPA?
- JPA architecture and core concepts
- Spring Data JPA benefits
- Repository Pattern and design principles

### 2. [Setup and Configuration](./topics/02-setup-configuration.md)
- Setting up Spring Boot with JPA
- Maven and Gradle dependencies
- DataSource configuration (H2, MySQL, PostgreSQL)
- JPA properties and environment-specific configuration

### 3. [Entity Mapping](./topics/03-entity-mapping.md)
- @Entity, @Table, @Column annotations
- Primary keys and generation strategies
- Relationships: @OneToOne, @OneToMany, @ManyToOne, @ManyToMany
- Embedded objects with @Embeddable
- Cascade types and fetch strategies

### 4. [Repositories](./topics/04-repositories.md)
- CrudRepository interface
- PagingAndSortingRepository interface
- JpaRepository interface
- Custom repository implementations
- Query by Example and Specifications

### 5. [CRUD Operations](./topics/05-crud-operations.md)
- Save and update operations
- Find methods (findById, findAll, derived queries)
- Delete operations (single, batch, soft delete)
- Performance considerations

### 6. [Queries](./topics/06-queries.md)
- Query derivation from method names
- @Query annotation with JPQL
- Named queries
- Native SQL queries
- DTO projections and query optimization

### 7. [Pagination and Auditing](./topics/07-pagination-auditing.md)
- Pageable interface and PageRequest
- Sort object for ordering results
- Page vs Slice comparison
- Auditing with @CreatedDate, @LastModifiedDate
- @CreatedBy, @LastModifiedBy with AuditorAware


## Key Concepts
Refer to the curriculum and lecture notes for detailed explanations of each topic.

## Exercises
See the [exercises](./exercises/) directory for hands-on practice problems and solutions.

## Code Examples
Check the module materials and exercises for practical code examples.

## Additional Resources
- Official documentation
- Online tutorials and courses
- Community forums and discussions

## Assessment
Make sure you are comfortable with all topics listed above before proceeding to the next module.

## Next Steps
Continue to the next module in the curriculum sequence.

---
**Time Estimate:** 5 days | **Difficulty:** Intermediate | **Prerequisites:** Previous modules
