# Java Core - Practice Exercises

## Overview

This module covers Core Java concepts through structured hands-on exercises. Each section focuses on specific concepts with practical implementations.

## Exercise Topics

| # | Focus Area | Topics |
|---|------------|--------|
| 1 | OOP Foundations | Classes, Static Members, Inheritance, Polymorphism |
| 2 | OOP Advanced + Exceptions | Interfaces, Generics, Annotations, Exception Handling |
| 3 | Collections + Functional | Collections, Lambdas, Optional, Stream API |
| 4 | I/O + Concurrency | File I/O, Serialization, Multithreading, CompletableFuture |
| 5 | File Processing | CSV Processing, Properties Files, Directory Operations, Text Transformation |
| 6 | Collections Deep Dive | Comparators, TreeSet/TreeMap, Deque Patterns, Collections Utilities |

## Exercise Files

- [OOP Foundations](exercises/exercise-01-oop-foundations.md)
- [OOP Advanced + Exceptions](exercises/exercise-02-oop-advanced.md)
- [Collections + Functional + Streams](exercises/exercise-03-collections-streams.md)
- [I/O + Concurrency](exercises/exercise-04-io-concurrency.md)
- [File Processing](exercises/exercise-05-file-processing.md)
- [Collections Deep Dive](exercises/exercise-06-collections-deep-dive.md)

## Final Project

After completing the exercises, build the **Library Management System** as a capstone project.

### Project: Library Management System

Build a console-based Library Management System that demonstrates Core Java concepts including OOP, Collections, Streams, Exception Handling, and File I/O.

### Domain Classes

#### Book
- ISBN (unique identifier)
- Title
- Author
- Genre (enum: FICTION, NON_FICTION, SCIENCE, HISTORY, TECHNOLOGY)
- PublishedYear
- Available (boolean)

#### Member
- MemberId (auto-generated)
- Name
- Email
- MembershipDate
- BorrowedBooks (List of Book)

#### BorrowRecord
- RecordId
- Member
- Book
- BorrowDate
- DueDate
- ReturnDate (nullable)

### Functionality

1. **Book Management**
   - Add new books
   - Search books by title, author, or genre
   - List all available books
   - Remove books from library

2. **Member Management**
   - Register new members
   - Search members by name or email
   - View member's borrowed books

3. **Borrowing System**
   - Borrow a book (max 3 books per member)
   - Return a book
   - Check overdue books
   - Calculate late fees ($0.50 per day)

4. **Reports (Using Streams)**
   - Most popular books (most borrowed)
   - Members with overdue books
   - Books by genre statistics
   - Revenue from late fees

### Project Structure

```
src/
├── model/
│   ├── Book.java
│   ├── Member.java
│   ├── BorrowRecord.java
│   └── Genre.java (enum)
├── repository/
│   ├── Repository.java (generic interface)
│   ├── BookRepository.java
│   └── MemberRepository.java
├── service/
│   ├── LibraryService.java
│   └── ReportService.java
├── exception/
│   ├── LibraryException.java
│   ├── BookNotAvailableException.java
│   └── MemberLimitExceededException.java
└── Main.java
```

### Java Concepts to Demonstrate

- **OOP**: Encapsulation, Inheritance, Polymorphism
- **Collections**: ArrayList, HashMap, HashSet
- **Generics**: Custom generic repository class
- **Streams**: filter, map, collect, groupingBy, reduce
- **Exceptions**: Custom LibraryException hierarchy
- **Optional**: For nullable return values
- **File I/O**: Save/load data to JSON or serialization
- **Java 21 Features**: Records, Pattern Matching, Text Blocks

## Submission Checklist

| Exercise | Status |
|----------|--------|
| OOP Foundations | ⬜ |
| OOP Advanced + Exceptions | ⬜ |
| Collections + Streams | ⬜ |
| I/O + Concurrency | ⬜ |
| File Processing | ⬜ |
| Collections Deep Dive | ⬜ |
| Library Management System | ⬜ |

## Submission Instructions

1. Create a folder named `java-core-exercises`
2. Add subfolders for each topic: `oop-foundations`, `oop-advanced`, `collections-streams`, `io-concurrency`, `file-processing`, `collections-deep-dive`
3. Add a `library-system` folder for the final project
4. Submit the complete folder as a zip file or GitHub repository link
