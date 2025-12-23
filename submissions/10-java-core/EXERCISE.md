# Exercise: Java Core - Library Management System

## Objective
Build a library management system demonstrating core Java concepts: OOP, Collections, Exceptions, and File I/O.

## Requirements

### Required Files
| File | Description |
|------|-------------|
| `Book.java` | Book entity class |
| `Member.java` | Library member class |
| `Genre.java` | Enum for book genres |
| `LibraryService.java` | Business logic |
| `LibraryException.java` | Custom exception |
| `Main.java` | Entry point (optional) |

### Class Specifications

#### Book.java
```java
public class Book {
    private String isbn;
    private String title;
    private String author;
    private Genre genre;
    private boolean available;

    // Constructor, getters, setters
    // equals() and hashCode() based on isbn
    // toString()
}
```

#### Member.java
```java
public class Member {
    private String memberId;
    private String name;
    private List<Book> borrowedBooks;
    private static final int MAX_BOOKS = 5;

    // Constructor, getters
    // borrowBook(Book book) throws LibraryException
    // returnBook(Book book)
}
```

#### Genre.java (Enum)
```java
public enum Genre {
    FICTION, NON_FICTION, SCIENCE, HISTORY, BIOGRAPHY, TECHNOLOGY
}
```

#### LibraryService.java
```java
public class LibraryService {
    private Map<String, Book> books;      // isbn -> Book
    private Map<String, Member> members;  // memberId -> Member

    // addBook(Book book)
    // removeBook(String isbn)
    // registerMember(Member member)
    // borrowBook(String memberId, String isbn) throws LibraryException
    // returnBook(String memberId, String isbn)
    // searchByTitle(String title) -> List<Book>
    // searchByAuthor(String author) -> List<Book>
    // getAvailableBooks() -> List<Book>
}
```

### Technical Requirements
- [ ] Proper encapsulation (private fields, public methods)
- [ ] Use appropriate Collections (List, Map, Set)
- [ ] Custom exception with meaningful messages
- [ ] Input validation
- [ ] JavaDoc comments on public methods
- [ ] Proper use of `@Override` annotation
- [ ] Follow Java naming conventions

## Evaluation Criteria
| Criteria | Points |
|----------|--------|
| Correct class structure & OOP | 25 |
| Collections used appropriately | 20 |
| Exception handling | 15 |
| Business logic correctness | 20 |
| Code quality & conventions | 20 |
| **Total** | **100** |

## Submission Checklist
- [ ] All required `.java` files present
- [ ] Placed in `10-java-core/` folder
- [ ] Code compiles without errors
- [ ] Classes follow specifications
- [ ] Committed and pushed to repository
