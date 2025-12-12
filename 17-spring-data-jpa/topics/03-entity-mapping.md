# Entity Mapping

## What is Entity Mapping?

Entity mapping is the process of defining how Java classes (entities) correspond to database tables and how their fields map to table columns. JPA uses annotations to establish these mappings, allowing you to work with database records as objects.

### Why Entity Mapping Matters

- Defines the structure of your database schema
- Establishes relationships between tables
- Configures data types and constraints
- Enables object-oriented database interactions
- Provides metadata for query generation

---

## @Entity, @Table, @Column

### @Entity Annotation

The `@Entity` annotation marks a class as a JPA entity, making it eligible for database persistence.

```java
import jakarta.persistence.*;

@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String username;
    private String email;

    // Constructors, getters, setters
}
```

**Key Points:**
- Must have a no-argument constructor (can be protected or public)
- Should not be final
- Fields should not be final (except for constants)
- Must have a primary key field annotated with @Id

### @Table Annotation

The `@Table` annotation customizes the table mapping. If omitted, the table name defaults to the entity class name.

```java
@Entity
@Table(name = "users", schema = "myapp", catalog = "production")
public class User {
    // fields
}
```

**@Table Attributes:**

| Attribute | Description | Example |
|-----------|-------------|---------|
| name | Table name | `@Table(name = "app_users")` |
| schema | Database schema | `@Table(schema = "public")` |
| catalog | Database catalog | `@Table(catalog = "mydb")` |
| uniqueConstraints | Unique constraints | `@Table(uniqueConstraints = @UniqueConstraint(columnNames = {"email"}))` |
| indexes | Table indexes | `@Table(indexes = @Index(columnList = "email"))` |

**Advanced Example:**

```java
@Entity
@Table(
    name = "users",
    uniqueConstraints = {
        @UniqueConstraint(
            name = "uk_email",
            columnNames = {"email"}
        )
    },
    indexes = {
        @Index(name = "idx_username", columnList = "username"),
        @Index(name = "idx_email", columnList = "email")
    }
)
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String username;
    private String email;
}
```

### @Column Annotation

The `@Column` annotation configures column mapping. If omitted, the column name defaults to the field name.

```java
@Entity
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "product_name", nullable = false, length = 100)
    private String name;

    @Column(name = "description", columnDefinition = "TEXT")
    private String description;

    @Column(precision = 10, scale = 2)
    private BigDecimal price;

    @Column(unique = true)
    private String sku;

    @Column(updatable = false)
    private LocalDateTime createdAt;
}
```

**@Column Attributes:**

| Attribute | Description | Example |
|-----------|-------------|---------|
| name | Column name | `@Column(name = "first_name")` |
| nullable | Allow null values | `@Column(nullable = false)` |
| unique | Unique constraint | `@Column(unique = true)` |
| length | String column length | `@Column(length = 255)` |
| precision | Decimal precision | `@Column(precision = 10)` |
| scale | Decimal scale | `@Column(scale = 2)` |
| insertable | Include in INSERT | `@Column(insertable = false)` |
| updatable | Include in UPDATE | `@Column(updatable = false)` |
| columnDefinition | Custom SQL type | `@Column(columnDefinition = "TEXT")` |

---

## Primary Keys

The primary key uniquely identifies each entity instance. JPA requires every entity to have a primary key.

### Simple Primary Key

```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String username;
}
```

### Generation Strategies

#### AUTO (Default)

Lets the JPA provider choose the strategy:

```java
@Id
@GeneratedValue(strategy = GenerationType.AUTO)
private Long id;
```

#### IDENTITY

Uses database auto-increment:

```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```

Best for MySQL, PostgreSQL with SERIAL.

#### SEQUENCE

Uses database sequence:

```java
@Id
@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "user_seq")
@SequenceGenerator(name = "user_seq", sequenceName = "user_sequence",
                   allocationSize = 1)
private Long id;
```

Best for Oracle, PostgreSQL.

#### TABLE

Uses a database table to generate keys:

```java
@Id
@GeneratedValue(strategy = GenerationType.TABLE, generator = "user_gen")
@TableGenerator(name = "user_gen", table = "id_generator",
                pkColumnName = "gen_name", valueColumnName = "gen_value",
                pkColumnValue = "user_id", allocationSize = 1)
private Long id;
```

Portable but slower.

### Composite Primary Key

When multiple fields form the primary key:

**Using @IdClass:**

```java
// Composite key class
public class OrderItemId implements Serializable {
    private Long orderId;
    private Long productId;

    // Default constructor
    public OrderItemId() {}

    // Constructor, equals, hashCode
}

// Entity
@Entity
@IdClass(OrderItemId.class)
public class OrderItem {
    @Id
    private Long orderId;

    @Id
    private Long productId;

    private Integer quantity;
    private BigDecimal price;
}
```

**Using @EmbeddedId:**

```java
// Embeddable composite key
@Embeddable
public class OrderItemId implements Serializable {
    private Long orderId;
    private Long productId;

    // Constructor, equals, hashCode
}

// Entity
@Entity
public class OrderItem {
    @EmbeddedId
    private OrderItemId id;

    private Integer quantity;
    private BigDecimal price;
}
```

---

## Relationships

JPA supports four types of relationships between entities.

### @OneToOne

One entity instance relates to exactly one instance of another entity.

**Example: User and UserProfile**

```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String username;

    @OneToOne(cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    @JoinColumn(name = "profile_id", referencedColumnName = "id")
    private UserProfile profile;
}

@Entity
public class UserProfile {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String bio;
    private String avatarUrl;

    @OneToOne(mappedBy = "profile")
    private User user;
}
```

**Key Attributes:**
- `cascade`: Operations to cascade (PERSIST, MERGE, REMOVE, etc.)
- `fetch`: Eager or lazy loading
- `mappedBy`: Indicates the owning side (bidirectional)
- `@JoinColumn`: Specifies the foreign key column

### @OneToMany and @ManyToOne

One entity relates to multiple instances, and vice versa.

**Example: Department and Employees**

```java
@Entity
public class Department {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @OneToMany(mappedBy = "department", cascade = CascadeType.ALL,
               orphanRemoval = true)
    private List<Employee> employees = new ArrayList<>();

    // Helper methods
    public void addEmployee(Employee employee) {
        employees.add(employee);
        employee.setDepartment(this);
    }

    public void removeEmployee(Employee employee) {
        employees.remove(employee);
        employee.setDepartment(null);
    }
}

@Entity
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "department_id")
    private Department department;
}
```

**Best Practices:**
- Always use `mappedBy` on the @OneToMany side
- Put @JoinColumn on the @ManyToOne side (owning side)
- Use helper methods for bidirectional relationships
- Consider orphanRemoval for dependent entities

### @ManyToMany

Multiple instances of one entity relate to multiple instances of another.

**Example: Students and Courses**

```java
@Entity
public class Student {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @ManyToMany(cascade = {CascadeType.PERSIST, CascadeType.MERGE})
    @JoinTable(
        name = "student_course",
        joinColumns = @JoinColumn(name = "student_id"),
        inverseJoinColumns = @JoinColumn(name = "course_id")
    )
    private Set<Course> courses = new HashSet<>();

    // Helper methods
    public void enrollInCourse(Course course) {
        courses.add(course);
        course.getStudents().add(this);
    }

    public void dropCourse(Course course) {
        courses.remove(course);
        course.getStudents().remove(this);
    }
}

@Entity
public class Course {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;

    @ManyToMany(mappedBy = "courses")
    private Set<Student> students = new HashSet<>();
}
```

**With Join Entity (Recommended for Additional Attributes):**

```java
@Entity
public class Student {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @OneToMany(mappedBy = "student", cascade = CascadeType.ALL,
               orphanRemoval = true)
    private List<Enrollment> enrollments = new ArrayList<>();
}

@Entity
public class Course {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;

    @OneToMany(mappedBy = "course", cascade = CascadeType.ALL,
               orphanRemoval = true)
    private List<Enrollment> enrollments = new ArrayList<>();
}

@Entity
public class Enrollment {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "student_id")
    private Student student;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "course_id")
    private Course course;

    private LocalDate enrollmentDate;
    private String grade;
}
```

### Cascade Types

| Cascade Type | Description |
|-------------|-------------|
| ALL | All operations cascade |
| PERSIST | Persist operations cascade |
| MERGE | Merge operations cascade |
| REMOVE | Remove operations cascade |
| REFRESH | Refresh operations cascade |
| DETACH | Detach operations cascade |

### Fetch Types

| Fetch Type | Description | When to Use |
|-----------|-------------|-------------|
| EAGER | Load immediately | Small, frequently needed data |
| LAZY | Load on demand | Large collections, optional data |

**Default Fetch Types:**
- @OneToOne: EAGER
- @ManyToOne: EAGER
- @OneToMany: LAZY
- @ManyToMany: LAZY

---

## Embedded Objects

Embedded objects allow you to group related fields into a separate class without creating a separate table.

### @Embeddable and @Embedded

```java
@Embeddable
public class Address {
    private String street;
    private String city;
    private String state;
    private String zipCode;
    private String country;

    // Constructors, getters, setters
}

@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String username;

    @Embedded
    private Address address;

    @Embedded
    @AttributeOverrides({
        @AttributeOverride(name = "street",
                          column = @Column(name = "billing_street")),
        @AttributeOverride(name = "city",
                          column = @Column(name = "billing_city")),
        @AttributeOverride(name = "state",
                          column = @Column(name = "billing_state")),
        @AttributeOverride(name = "zipCode",
                          column = @Column(name = "billing_zip")),
        @AttributeOverride(name = "country",
                          column = @Column(name = "billing_country"))
    })
    private Address billingAddress;
}
```

### Multiple Embedded Objects

Use `@AttributeOverrides` when embedding the same type multiple times:

```java
@Entity
public class Company {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @Embedded
    @AttributeOverrides({
        @AttributeOverride(name = "street",
                          column = @Column(name = "hq_street")),
        @AttributeOverride(name = "city",
                          column = @Column(name = "hq_city"))
    })
    private Address headquarters;

    @Embedded
    @AttributeOverrides({
        @AttributeOverride(name = "street",
                          column = @Column(name = "branch_street")),
        @AttributeOverride(name = "city",
                          column = @Column(name = "branch_city"))
    })
    private Address branch;
}
```

### Benefits of Embedded Objects

- **Code Reusability**: Use the same class in multiple entities
- **Better Organization**: Group related fields logically
- **No Additional Table**: All fields stored in the same table
- **Type Safety**: Encapsulate validation and business logic

---

## Summary

| Concept | Key Points |
|---------|------------|
| @Entity/@Table/@Column | Basic mapping annotations, customize names and constraints |
| Primary Keys | @Id with generation strategies (IDENTITY, SEQUENCE, TABLE) |
| @OneToOne | One-to-one relationships, use @JoinColumn and mappedBy |
| @OneToMany/@ManyToOne | Parent-child relationships, owning side has @JoinColumn |
| @ManyToMany | Many-to-many with join table or join entity |
| Embedded Objects | Group related fields, no separate table, use @Embeddable |

## Next Topic

Continue to [Repositories](./04-repositories.md) to learn about Spring Data JPA repository interfaces.
