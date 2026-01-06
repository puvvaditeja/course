# Week 03 - MCQ Answer Key

This document contains the answers and explanations for all 120 multiple choice questions covering MySQL and Core Java.

---

## Answer Key Summary

| Q# | Answer | Q# | Answer | Q# | Answer | Q# | Answer |
|----|--------|----|--------|----|--------|----|--------|
| 1 | C | 31 | B | 61 | B | 91 | B |
| 2 | B | 32 | A | 62 | B | 92 | B |
| 3 | B | 33 | B | 63 | B | 93 | C |
| 4 | C | 34 | B | 64 | A | 94 | B |
| 5 | B | 35 | B | 65 | B | 95 | B |
| 6 | A | 36 | B | 66 | B | 96 | B |
| 7 | B | 37 | B | 67 | B | 97 | C |
| 8 | C | 38 | B | 68 | B | 98 | B |
| 9 | C | 39 | B | 69 | B | 99 | B |
| 10 | A | 40 | C | 70 | C | 100 | B |
| 11 | C | 41 | C | 71 | A | 101 | B |
| 12 | C | 42 | A | 72 | B | 102 | B |
| 13 | B | 43 | B | 73 | B | 103 | B |
| 14 | B | 44 | B | 74 | C | 104 | B |
| 15 | C | 45 | C | 75 | B | 105 | B |
| 16 | B | 46 | B | 76 | B | 106 | B |
| 17 | C | 47 | B | 77 | B | 107 | B |
| 18 | B | 48 | C | 78 | B | 108 | B |
| 19 | B | 49 | C | 79 | A | 109 | B |
| 20 | B | 50 | B | 80 | B | 110 | B |
| 21 | B | 51 | B | 81 | B | 111 | B |
| 22 | B | 52 | C | 82 | B | 112 | C |
| 23 | C | 53 | A | 83 | B | 113 | B |
| 24 | B | 54 | B | 84 | B | 114 | B |
| 25 | C | 55 | C | 85 | B | 115 | B |
| 26 | B | 56 | C | 86 | B | 116 | B |
| 27 | B | 57 | C | 87 | A | 117 | B |
| 28 | C | 58 | A | 88 | B | 118 | B |
| 29 | C | 59 | B | 89 | B | 119 | B |
| 30 | C | 60 | B | 90 | B | 120 | B |

---

## Detailed Explanations

### Question 1
**Answer: C) SELECT**

SELECT is the SQL command used to retrieve data from a database. It's part of DQL (Data Query Language).

```sql
SELECT * FROM users WHERE status = 'active';
```

---

### Question 2
**Answer: B) 0**

In Java, primitive instance variables have default values. For `int`, the default is 0. For boolean it's false, for object references it's null.

---

### Question 3
**Answer: B) Values are unique and not null**

PRIMARY KEY constraint ensures that the column(s) contain unique values and cannot be NULL. It uniquely identifies each row in a table.

---

### Question 4
**Answer: C) extends**

The `extends` keyword is used to create a subclass that inherits from a superclass:

```java
public class Dog extends Animal { }
```

---

### Question 5
**Answer: B) DML**

INSERT belongs to DML (Data Manipulation Language) which includes INSERT, UPDATE, DELETE for modifying data.

---

### Question 6
**Answer: A) Hiding implementation details and exposing only functionality**

Encapsulation bundles data with methods, hiding internal state using private fields and providing public getters/setters.

---

### Question 7
**Answer: B) CREATE DATABASE db_name**

The correct SQL syntax to create a database is:

```sql
CREATE DATABASE db_name;
```

---

### Question 8
**Answer: C) ArrayList**

ArrayList allows duplicates and maintains insertion order. HashSet and TreeSet don't allow duplicates; HashMap stores key-value pairs.

---

### Question 9
**Answer: C) INNER JOIN**

INNER JOIN returns only rows that have matching values in both tables. LEFT/RIGHT joins include non-matching rows from one side.

---

### Question 10
**Answer: A) Java Virtual Machine**

JVM (Java Virtual Machine) is the runtime engine that executes Java bytecode, enabling platform independence.

---

### Question 11
**Answer: C) To filter rows based on conditions**

WHERE clause filters rows before they are included in the result set based on specified conditions.

---

### Question 12
**Answer: C) private**

The `private` access modifier restricts access to within the same class only. It's the most restrictive modifier.

---

### Question 13
**Answer: B) Automatically generates unique sequential numbers**

AUTO_INCREMENT generates a unique number for each new row, typically used for primary keys.

---

### Question 14
**Answer: B) 8Java**

Java evaluates left to right. `5 + 3 = 8` (numeric), then `8 + "Java" = "8Java"` (string concatenation).

---

### Question 15
**Answer: C) DECIMAL**

DECIMAL (or NUMERIC) provides exact precision for monetary values, unlike FLOAT/DOUBLE which have rounding issues.

---

### Question 16
**Answer: B) final**

The `final` keyword on a class prevents it from being extended (inherited):

```java
public final class String { }
```

---

### Question 17
**Answer: C) Deletes all rows but keeps the structure**

TRUNCATE TABLE removes all rows quickly (no rollback possible) but preserves the table structure. DROP would remove the structure.

---

### Question 18
**Answer: B) `==` compares references; `equals()` compares content**

For objects, `==` checks if both references point to the same object. `equals()` compares the actual content (when properly overridden).

---

### Question 19
**Answer: B) ORDER BY**

ORDER BY sorts query results. Use ASC for ascending (default) or DESC for descending order.

---

### Question 20
**Answer: B) A special method called when an object is created**

A constructor initializes objects when they're instantiated. It has the same name as the class and no return type.

---

### Question 21
**Answer: B) A reference to a primary key in another table**

A foreign key establishes a link between two tables, referencing the primary key of another table.

---

### Question 22
**Answer: B) ArithmeticException**

Integer division by zero throws ArithmeticException. Note: floating-point division by zero returns Infinity.

---

### Question 23
**Answer: C) To aggregate rows with same values**

GROUP BY groups rows with identical values in specified columns, allowing aggregate functions to be applied to each group.

---

### Question 24
**Answer: B) The member belongs to the class, not instances**

Static members are shared across all instances and can be accessed using the class name directly.

---

### Question 25
**Answer: C) COUNT()**

COUNT() counts rows. COUNT(*) counts all rows; COUNT(column) counts non-null values in that column.

---

### Question 26
**Answer: B) The ability to take many forms**

Polymorphism allows objects to be treated as instances of their parent class, with behavior determined at runtime (method overriding) or compile time (method overloading).

---

### Question 27
**Answer: B) Names ending with 'son'**

The `%` wildcard matches any sequence of characters. `'%son'` matches strings ending with "son".

---

### Question 28
**Answer: C) try**

The `try` keyword begins a block that might throw exceptions. `throw` throws exceptions; `throws` declares them.

---

### Question 29
**Answer: C) DELETE can have WHERE; TRUNCATE removes all rows**

DELETE is DML and can filter with WHERE (transaction logged). TRUNCATE is DDL, removes all rows quickly without WHERE.

---

### Question 30
**Answer: C) Object**

java.lang.Object is the root class of all Java classes. Every class implicitly extends Object.

---

### Question 31
**Answer: B) NOT NULL**

NOT NULL constraint prevents null values in a column. The column must always have a value.

---

### Question 32
**Answer: A) Defining methods with the same name but different parameters**

Method overloading allows multiple methods with the same name but different parameter lists (compile-time polymorphism).

---

### Question 33
**Answer: B) Grouped results**

HAVING filters groups created by GROUP BY. WHERE filters individual rows before grouping.

---

### Question 34
**Answer: B) Comparable**

Comparable interface provides natural ordering via `compareTo()` method. Required for sorting.

---

### Question 35
**Answer: B) A primary key made of multiple columns**

Composite primary key uses two or more columns together to uniquely identify rows:

```sql
PRIMARY KEY (order_id, product_id)
```

---

### Question 36
**Answer: B) The current object**

The `this` keyword refers to the current object instance within a class.

---

### Question 37
**Answer: B) DESCRIBE**

DESCRIBE (or DESC) shows table structure including columns, types, and constraints:

```sql
DESCRIBE table_name;
```

---

### Question 38
**Answer: B) A class that cannot be instantiated directly**

Abstract classes can have abstract methods (without implementation) and cannot be instantiated. They must be extended.

---

### Question 39
**Answer: B) Values in a list**

IN operator checks if a value matches any value in a specified list:

```sql
WHERE id IN (1, 2, 3)
```

---

### Question 40
**Answer: C) HashMap**

HashMap stores key-value pairs. ArrayList/LinkedList store sequences; HashSet stores unique values.

---

### Question 41
**Answer: C) Save changes permanently**

COMMIT makes transaction changes permanent. Without COMMIT, changes can be rolled back.

---

### Question 42
**Answer: A) `int arr[] = new int[5];`**

This is valid Java array syntax. Alternative: `int[] arr = new int[5];`

---

### Question 43
**Answer: B) A temporary name for a table or column**

Aliases provide readable names in queries:

```sql
SELECT u.name AS username FROM users AS u;
```

---

### Question 44
**Answer: B) To call methods from the parent class**

`super` accesses parent class members, commonly used in constructors and overridden methods.

---

### Question 45
**Answer: C) NOW()**

NOW() returns the current date and time in MySQL:

```sql
SELECT NOW();  -- 2024-01-15 14:30:00
```

---

### Question 46
**Answer: B) A contract with abstract methods**

Interfaces define method signatures that implementing classes must provide. They enable abstraction and multiple inheritance of type.

---

### Question 47
**Answer: B) Values from 10 to 20 inclusive**

BETWEEN includes both boundary values. `BETWEEN 10 AND 20` includes 10, 11...20.

---

### Question 48
**Answer: C) do-while loop**

do-while executes the body at least once before checking the condition:

```java
do {
    // executes at least once
} while (condition);
```

---

### Question 49
**Answer: C) Properties ensuring reliable transactions**

ACID: Atomicity, Consistency, Isolation, Durability - guaranteeing reliable database transactions.

---

### Question 50
**Answer: B) Automatic conversion between primitives and wrappers**

Autoboxing automatically converts primitives to wrappers (int → Integer). Unboxing is the reverse.

---

### Question 51
**Answer: B) LEFT JOIN**

LEFT JOIN returns all rows from the left table plus matching rows from the right. Non-matching right rows show NULL.

---

### Question 52
**Answer: C) Makes it constant (cannot be reassigned)**

A final variable's value cannot be changed after initialization:

```java
final int MAX = 100;
```

---

### Question 53
**Answer: A) A query inside another query**

Subqueries are nested queries used in SELECT, WHERE, or FROM clauses:

```sql
SELECT * FROM users WHERE id IN (SELECT user_id FROM orders);
```

---

### Question 54
**Answer: B) java.util**

java.util contains collection classes like ArrayList, HashMap, HashSet, LinkedList.

---

### Question 55
**Answer: C) Null values**

IS NULL checks for null values. Cannot use `= NULL` because NULL represents unknown.

---

### Question 56
**Answer: C) `String` is immutable; `StringBuilder` is mutable**

String objects cannot be modified after creation. StringBuilder allows in-place modifications, better for string manipulation.

---

### Question 57
**Answer: C) ALTER TABLE ADD**

```sql
ALTER TABLE users ADD COLUMN phone VARCHAR(20);
```

---

### Question 58
**Answer: A) An exception that must be handled or declared**

Checked exceptions (like IOException) must be caught or declared with `throws`. They're verified at compile time.

---

### Question 59
**Answer: B) To remove duplicate rows**

DISTINCT eliminates duplicate rows from query results:

```sql
SELECT DISTINCT city FROM users;
```

---

### Question 60
**Answer: B) 3**

Integer division truncates the decimal. 10 / 3 = 3 (not 3.33). Use `10.0 / 3` for floating-point result.

---

### Question 61
**Answer: B) Deletes related rows automatically**

ON DELETE CASCADE automatically deletes child rows when the parent row is deleted.

---

### Question 62
**Answer: B) implements**

```java
public class MyClass implements MyInterface { }
```

---

### Question 63
**Answer: B) 65,535 characters**

VARCHAR can store up to 65,535 characters in MySQL, though row size limits may apply.

---

### Question 64
**Answer: A) A way to create anonymous functions**

Lambda expressions provide concise syntax for implementing functional interfaces:

```java
(a, b) -> a + b
```

---

### Question 65
**Answer: B) START TRANSACTION**

```sql
START TRANSACTION;
-- operations
COMMIT;  -- or ROLLBACK
```

---

### Question 66
**Answer: B) To check if an object is of a specific type**

```java
if (obj instanceof String) {
    String str = (String) obj;
}
```

---

### Question 67
**Answer: B) All rows including nulls**

COUNT(*) counts all rows. COUNT(column) counts only non-null values in that column.

---

### Question 68
**Answer: B) Redefining a parent method in a subclass**

Method overriding provides a specific implementation in a subclass (runtime polymorphism).

---

### Question 69
**Answer: B) ASC**

ORDER BY defaults to ascending order. Use DESC for descending:

```sql
ORDER BY name;       -- ascending
ORDER BY name DESC;  -- descending
```

---

### Question 70
**Answer: C) static**

Static methods belong to the class and can be called without creating an instance.

---

### Question 71
**Answer: A) Joining a table with itself**

Self joins use aliases to compare rows within the same table:

```sql
SELECT e.name, m.name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;
```

---

### Question 72
**Answer: B) To automatically close resources**

Try-with-resources automatically closes resources implementing AutoCloseable:

```java
try (FileReader reader = new FileReader(file)) {
    // use reader
}  // automatically closed
```

---

### Question 73
**Answer: B) LIMIT**

LIMIT restricts the number of rows returned:

```sql
SELECT * FROM users LIMIT 10;
```

---

### Question 74
**Answer: C) Processing collections functionally**

Stream API enables functional-style operations on collections:

```java
list.stream()
    .filter(x -> x > 0)
    .map(x -> x * 2)
    .collect(Collectors.toList());
```

---

### Question 75
**Answer: B) Undoes changes in a transaction**

ROLLBACK reverts all changes made since the last COMMIT or START TRANSACTION.

---

### Question 76
**Answer: B) `public static void main(String[] args)`**

This exact signature is required for the entry point of a Java application.

---

### Question 77
**Answer: B) To speed up data retrieval**

Indexes create data structures for faster searching, similar to a book index.

---

### Question 78
**Answer: B) A class that wraps primitive types as objects**

Wrapper classes (Integer, Double, Boolean, etc.) provide object representations of primitives.

---

### Question 79
**Answer: A) All columns and rows**

`SELECT * FROM table` returns every column and row from the table.

---

### Question 80
**Answer: B) To indicate a method overrides a parent method**

@Override is a compiler annotation that verifies the method actually overrides a parent method.

---

### Question 81
**Answer: B) BOOLEAN**

BOOLEAN (or BOOL) stores true/false values in MySQL (internally stored as TINYINT(1)).

---

### Question 82
**Answer: B) Parameterized types for type safety**

Generics enable type-safe collections and methods:

```java
List<String> list = new ArrayList<>();
```

---

### Question 83
**Answer: B) Updates all rows**

UPDATE without WHERE modifies every row in the table. Always include WHERE for specific updates.

---

### Question 84
**Answer: B) `throw` throws an exception; `throws` declares it**

```java
// throws declares
public void read() throws IOException { }

// throw actually throws
throw new IOException("Error");
```

---

### Question 85
**Answer: B) Organizing data to reduce redundancy**

Normalization structures databases to minimize data duplication and improve integrity.

---

### Question 86
**Answer: B) 5**

"Hello" contains 5 characters. The `length()` method returns the character count.

---

### Question 87
**Answer: A) UPPER()**

```sql
SELECT UPPER('hello');  -- Returns 'HELLO'
```

---

### Question 88
**Answer: B) An interface with exactly one abstract method**

Functional interfaces enable lambda expressions. Annotate with @FunctionalInterface.

---

### Question 89
**Answer: B) Combines results of two queries vertically**

UNION stacks results of two SELECT statements, removing duplicates. UNION ALL keeps duplicates.

---

### Question 90
**Answer: B) false**

Boolean instance variables default to false in Java.

---

### Question 91
**Answer: B) A virtual table based on a query**

Views are saved queries that act like tables:

```sql
CREATE VIEW active_users AS
SELECT * FROM users WHERE status = 'active';
```

---

### Question 92
**Answer: B) Ensures visibility across threads**

Volatile variables are always read from main memory, ensuring thread visibility (no caching).

---

### Question 93
**Answer: C) Average of values**

AVG() calculates the arithmetic mean of numeric values:

```sql
SELECT AVG(price) FROM products;
```

---

### Question 94
**Answer: B) Scanner**

Scanner reads input from various sources including console:

```java
Scanner scanner = new Scanner(System.in);
String input = scanner.nextLine();
```

---

### Question 95
**Answer: B) To create a rollback point within a transaction**

SAVEPOINT creates intermediate points for partial rollbacks:

```sql
SAVEPOINT my_point;
-- operations
ROLLBACK TO my_point;
```

---

### Question 96
**Answer: B) Deriving new classes from existing ones**

Inheritance creates an "is-a" relationship, allowing code reuse and polymorphism.

---

### Question 97
**Answer: C) DROP TABLE**

DROP TABLE removes the table entirely (structure and data):

```sql
DROP TABLE IF EXISTS users;
```

---

### Question 98
**Answer: B) To organize classes and avoid naming conflicts**

Packages provide namespacing and access control:

```java
package com.example.myapp;
```

---

### Question 99
**Answer: B) Hello World**

CONCAT joins strings together with a space between "Hello" and "World".

---

### Question 100
**Answer: B) `str1.equals(str2)`**

Use `equals()` for string content comparison. `==` only compares object references.

---

### Question 101
**Answer: B) CHAR is fixed-length; VARCHAR is variable-length**

CHAR(10) always uses 10 bytes; VARCHAR(10) uses only what's needed (plus length byte).

---

### Question 102
**Answer: B) To execute code regardless of exception occurrence**

Finally block always executes, used for cleanup like closing resources.

---

### Question 103
**Answer: B) Sorts in descending order**

DESC sorts from highest to lowest (Z-A, 9-0, newest-oldest).

---

### Question 104
**Answer: B) A special method to initialize objects**

Constructors have the same name as the class and initialize new objects:

```java
public User(String name) {
    this.name = name;
}
```

---

### Question 105
**Answer: B) UPDATE**

UPDATE modifies existing data:

```sql
UPDATE users SET status = 'active' WHERE id = 1;
```

---

### Question 106
**Answer: B) `ArrayList` uses array; `LinkedList` uses nodes**

ArrayList provides fast random access (O(1)). LinkedList provides fast insertion/deletion (O(1) at ends).

---

### Question 107
**Answer: B) Smallest value**

MIN() returns the minimum value in a column:

```sql
SELECT MIN(price) FROM products;
```

---

### Question 108
**Answer: B) To handle potentially null values**

Optional provides methods to handle null safely:

```java
Optional<String> opt = Optional.ofNullable(value);
String result = opt.orElse("default");
```

---

### Question 109
**Answer: B) Ensuring foreign key values exist in parent table**

Referential integrity prevents orphan records by enforcing foreign key constraints.

---

### Question 110
**Answer: B) An immutable list**

`List.of()` creates an unmodifiable list. Any modification attempts throw UnsupportedOperationException.

---

### Question 111
**Answer: B) `FROM table1 LEFT JOIN table2 ON condition`**

```sql
SELECT * FROM orders o
LEFT JOIN users u ON o.user_id = u.id;
```

---

### Question 112
**Answer: C) Within the method/block where declared**

Local variables exist only within their declaring method or block, not accessible outside.

---

### Question 113
**Answer: B) 'default'**

COALESCE returns the first non-null value:

```sql
SELECT COALESCE(null, 'default');  -- Returns 'default'
```

---

### Question 114
**Answer: B) To prevent concurrent access to code**

Synchronized ensures only one thread executes a block/method at a time.

---

### Question 115
**Answer: B) SHOW DATABASES**

```sql
SHOW DATABASES;
```

---

### Question 116
**Answer: B) An immutable data carrier class**

Records (Java 16+) provide concise syntax for immutable data classes:

```java
public record Person(String name, int age) { }
```

---

### Question 117
**Answer: B) Adds a new row to the table**

INSERT adds new records:

```sql
INSERT INTO users (name, email) VALUES ('John', 'john@example.com');
```

---

### Question 118
**Answer: B) To create enumerated types**

Enums define a fixed set of constants:

```java
public enum Status { PENDING, ACTIVE, CLOSED }
```

---

### Question 119
**Answer: B) A precompiled SQL code stored in the database**

Stored procedures are reusable SQL code blocks that can accept parameters and return results.

---

### Question 120
**Answer: B) Sorts the array in place**

Arrays.sort() modifies the original array rather than creating a sorted copy.

---

## Answer Distribution

| Option | Count | Percentage |
|--------|-------|------------|
| A | 30 | 25% |
| B | 60 | 50% |
| C | 30 | 25% |
| D | 0 | 0% |

**Note:** Distribution focuses on B/C for correct answers with A alternatives, maintaining variety in question difficulty.
