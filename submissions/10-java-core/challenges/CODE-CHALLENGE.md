# Code Challenges: Java Core

Short coding problems to practice Java fundamentals. Complete each challenge in a separate file.

---

## Challenge 1: Stream Processor
**File:** `StreamProcessor.java`

Write a method that takes a list of integers and returns the average of all even numbers.

```java
public static OptionalDouble averageOfEvens(List<Integer> numbers) {
    // Implementation
}

averageOfEvens(Arrays.asList(1, 2, 3, 4, 5, 6)); // Returns: OptionalDouble[4.0]
averageOfEvens(Arrays.asList(1, 3, 5));          // Returns: OptionalDouble.empty
```

**Constraints:**
- Use Stream API
- Use `filter()` and `mapToInt()`
- Return `OptionalDouble`

---

## Challenge 2: Generic Pair
**File:** `Pair.java`

Create a generic `Pair<K, V>` class that holds two values of different types.

```java
Pair<String, Integer> pair = new Pair<>("age", 25);
pair.getKey();   // Returns: "age"
pair.getValue(); // Returns: 25
pair.swap();     // Returns: Pair<Integer, String>(25, "age")
```

**Requirements:**
- Generic class with two type parameters
- Implement `getKey()`, `getValue()`, `swap()`
- Override `equals()` and `hashCode()`
- Override `toString()`

---

## Challenge 3: Exception Handler
**File:** `SafeExecutor.java`

Create a functional interface and utility method for safe execution with error handling.

```java
@FunctionalInterface
interface ThrowingSupplier<T> {
    T get() throws Exception;
}

public static <T> Optional<T> safeExecute(ThrowingSupplier<T> supplier) {
    // Returns Optional.of(result) on success
    // Returns Optional.empty() on exception
}

safeExecute(() -> Integer.parseInt("123"));  // Optional[123]
safeExecute(() -> Integer.parseInt("abc"));  // Optional.empty
```

**Constraints:**
- Use functional interface
- Use try-catch with Optional

---

## Challenge 4: Collection Analyzer
**File:** `CollectionAnalyzer.java`

Write methods to analyze a collection of strings.

```java
public static Map<Integer, List<String>> groupByLength(List<String> words);
public static Map<Character, Long> charFrequency(List<String> words);

groupByLength(Arrays.asList("hi", "bye", "hello", "ok"));
// Returns: {2=[hi, ok], 3=[bye], 5=[hello]}

charFrequency(Arrays.asList("aab", "bc"));
// Returns: {a=2, b=2, c=1}
```

**Constraints:**
- Use `Collectors.groupingBy()`
- Use Stream API throughout

---

## Submission
Place all challenge files in: `10-java-core/challenges/`

| Challenge | File | Status |
|-----------|------|--------|
| Stream Processor | `StreamProcessor.java` | ⬜ |
| Generic Pair | `Pair.java` | ⬜ |
| Exception Handler | `SafeExecutor.java` | ⬜ |
| Collection Analyzer | `CollectionAnalyzer.java` | ⬜ |
