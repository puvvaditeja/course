# Exercise 6: Collections Deep Dive

## Learning Objectives
- Master Comparator creation and chaining for complex sorting
- Understand when to use TreeSet/TreeMap vs HashSet/HashMap
- Implement custom objects correctly for use in collections
- Use Deque for stack and queue operations
- Apply Collections utility methods effectively

---

## Exercise 6.1: Comparator Mastery (1 hour)

### Scenario
Build a flexible sorting system for a product catalog.

### Requirements

**1. Create the Product class:**
```java
public class Product {
    private String id;
    private String name;
    private String category;
    private double price;
    private int stockQuantity;
    private LocalDate addedDate;
    private Double rating;  // nullable, 0.0 - 5.0

    // Constructor, getters, setters, equals, hashCode, toString
}
```

**2. Create ProductComparators utility class:**
```java
public class ProductComparators {

    // Sort by price (ascending)
    public static Comparator<Product> byPrice() { }

    // Sort by price (descending)
    public static Comparator<Product> byPriceDescending() { }

    // Sort by name (case-insensitive)
    public static Comparator<Product> byName() { }

    // Sort by category, then by name
    public static Comparator<Product> byCategoryThenName() { }

    // Sort by rating (nulls last), then by price
    public static Comparator<Product> byRatingThenPrice() { }

    // Sort by stock (low stock first - less than 10), then by name
    public static Comparator<Product> byLowStockFirst() { }

    // Sort by date added (newest first)
    public static Comparator<Product> byNewest() { }

    // Custom multi-field sort builder
    public static Comparator<Product> multiSort(List<String> fields,
                                                 List<Boolean> ascending) { }
}
```

**3. Create SortableProductList class:**
```java
public class SortableProductList {
    private List<Product> products;

    public SortableProductList(List<Product> products) { }

    // Sort using provided comparator
    public SortableProductList sortBy(Comparator<Product> comparator) { }

    // Chain multiple sorts (secondary sort, tertiary sort, etc.)
    public SortableProductList thenSortBy(Comparator<Product> comparator) { }

    // Sort by field name using reflection (bonus)
    public SortableProductList sortByField(String fieldName, boolean ascending) { }

    // Get top N products after sorting
    public List<Product> top(int n) { }

    // Get products in pages
    public List<Product> getPage(int pageNumber, int pageSize) { }

    public List<Product> getProducts() { }
}
```

### Test Cases
```java
List<Product> products = loadProducts(); // Load sample data

SortableProductList list = new SortableProductList(products);

// Test 1: Simple price sort
List<Product> byPrice = list.sortBy(ProductComparators.byPrice()).getProducts();

// Test 2: Category then name sort
List<Product> organized = list
    .sortBy(ProductComparators.byCategoryThenName())
    .getProducts();

// Test 3: Handle null ratings
List<Product> byRating = list
    .sortBy(ProductComparators.byRatingThenPrice())
    .getProducts();
// Products with null ratings should be at the end

// Test 4: Chained sorting with pagination
List<Product> page1 = list
    .sortBy(ProductComparators.byCategory())
    .thenSortBy(ProductComparators.byPriceDescending())
    .getPage(1, 10);

// Test 5: Multi-field dynamic sort
Comparator<Product> dynamic = ProductComparators.multiSort(
    List.of("category", "price", "name"),
    List.of(true, false, true)  // category ASC, price DESC, name ASC
);
```

---

## Exercise 6.2: TreeSet and TreeMap (45 minutes)

### Scenario
Build an event scheduling system that maintains sorted order.

### Requirements

**1. Create Event class:**
```java
public class Event implements Comparable<Event> {
    private String id;
    private String title;
    private LocalDateTime startTime;
    private LocalDateTime endTime;
    private int priority;  // 1 (highest) to 5 (lowest)

    // Natural ordering: by startTime, then by priority
    @Override
    public int compareTo(Event other) { }

    // equals and hashCode based on id only
}
```

**2. Create EventScheduler class using TreeSet/TreeMap:**
```java
public class EventScheduler {
    // TreeSet for events sorted by start time
    private TreeSet<Event> eventsByTime;

    // TreeMap for events grouped by date
    private TreeMap<LocalDate, List<Event>> eventsByDate;

    public EventScheduler() { }

    // Add event (prevent overlapping events)
    public boolean addEvent(Event event) { }

    // Remove event by id
    public boolean removeEvent(String eventId) { }

    // Get all events on a specific date
    public List<Event> getEventsOnDate(LocalDate date) { }

    // Get events in time range (inclusive)
    public List<Event> getEventsInRange(LocalDateTime start, LocalDateTime end) { }

    // Get next N upcoming events from now
    public List<Event> getUpcomingEvents(int count) { }

    // Get the event happening right now (if any)
    public Optional<Event> getCurrentEvent() { }

    // Find gaps in schedule (free time slots) for a given date
    public List<TimeSlot> findFreeSlots(LocalDate date,
                                         LocalTime dayStart,
                                         LocalTime dayEnd) { }

    // Get events before a specific event
    public List<Event> getEventsBefore(Event event) { }

    // Get events after a specific event
    public List<Event> getEventsAfter(Event event) { }

    // Check if time slot is available
    public boolean isSlotAvailable(LocalDateTime start, LocalDateTime end) { }
}

public record TimeSlot(LocalDateTime start, LocalDateTime end) {}
```

**3. Use NavigableSet/NavigableMap methods:**
- `headSet()`, `tailSet()`, `subSet()`
- `floor()`, `ceiling()`, `higher()`, `lower()`
- `firstEntry()`, `lastEntry()`, `pollFirstEntry()`
- `descendingSet()`, `descendingMap()`

### Test Cases
```java
EventScheduler scheduler = new EventScheduler();

// Add events
Event meeting = new Event("1", "Team Meeting",
    LocalDateTime.of(2024, 1, 15, 10, 0),
    LocalDateTime.of(2024, 1, 15, 11, 0), 2);
Event lunch = new Event("2", "Lunch Break",
    LocalDateTime.of(2024, 1, 15, 12, 0),
    LocalDateTime.of(2024, 1, 15, 13, 0), 5);

scheduler.addEvent(meeting);
scheduler.addEvent(lunch);

// Test overlap prevention
Event conflicting = new Event("3", "Another Meeting",
    LocalDateTime.of(2024, 1, 15, 10, 30),
    LocalDateTime.of(2024, 1, 15, 11, 30), 1);
assert !scheduler.addEvent(conflicting); // Should fail

// Test range query
List<Event> morningEvents = scheduler.getEventsInRange(
    LocalDateTime.of(2024, 1, 15, 8, 0),
    LocalDateTime.of(2024, 1, 15, 12, 0)
);

// Test free slots
List<TimeSlot> freeSlots = scheduler.findFreeSlots(
    LocalDate.of(2024, 1, 15),
    LocalTime.of(9, 0),
    LocalTime.of(17, 0)
);
// Expected: 9:00-10:00, 11:00-12:00, 13:00-17:00
```

---

## Exercise 6.3: Deque Operations (45 minutes)

### Scenario
Implement common data structure patterns using Deque.

### Requirements

**1. Create BrowserHistory class (Stack pattern):**
```java
public class BrowserHistory {
    private Deque<String> backStack;
    private Deque<String> forwardStack;
    private String currentPage;

    public BrowserHistory(String homepage) { }

    // Navigate to new URL (clears forward history)
    public void visit(String url) { }

    // Go back N steps (return current page after navigation)
    public String back(int steps) { }

    // Go forward N steps (return current page after navigation)
    public String forward(int steps) { }

    // Get current page
    public String getCurrentPage() { }

    // Check if can go back/forward
    public boolean canGoBack() { }
    public boolean canGoForward() { }

    // Get full history (back stack + current + forward stack)
    public List<String> getFullHistory() { }
}
```

**2. Create TaskQueue class (Queue pattern with priority):**
```java
public class TaskQueue {
    private Deque<Task> normalQueue;
    private Deque<Task> priorityQueue;

    public TaskQueue() { }

    // Add task to appropriate queue
    public void addTask(Task task) { }

    // Add task to front (urgent)
    public void addUrgentTask(Task task) { }

    // Get next task (priority queue first)
    public Optional<Task> getNextTask() { }

    // Peek at next task without removing
    public Optional<Task> peekNextTask() { }

    // Get count of pending tasks
    public int getPendingCount() { }

    // Get all tasks in order they will be processed
    public List<Task> getAllPendingTasks() { }

    // Move task to front of its queue
    public boolean prioritizeTask(String taskId) { }
}

public record Task(String id, String description, boolean isPriority) {}
```

**3. Create UndoRedoManager class (Dual stack pattern):**
```java
public class UndoRedoManager<T> {
    private Deque<T> undoStack;
    private Deque<T> redoStack;
    private T currentState;

    public UndoRedoManager(T initialState) { }

    // Save new state (clears redo stack)
    public void saveState(T newState) { }

    // Undo to previous state
    public Optional<T> undo() { }

    // Redo to next state
    public Optional<T> redo() { }

    // Get current state
    public T getCurrentState() { }

    // Check capabilities
    public boolean canUndo() { }
    public boolean canRedo() { }

    // Get undo/redo history sizes
    public int getUndoCount() { }
    public int getRedoCount() { }

    // Clear all history
    public void clearHistory() { }
}
```

### Test Cases
```java
// Test Browser History
BrowserHistory browser = new BrowserHistory("google.com");
browser.visit("youtube.com");
browser.visit("github.com");
browser.visit("stackoverflow.com");

assert browser.back(1).equals("github.com");
assert browser.back(1).equals("youtube.com");
assert browser.forward(1).equals("github.com");
browser.visit("twitter.com");  // Clears forward history
assert !browser.canGoForward();

// Test Task Queue
TaskQueue queue = new TaskQueue();
queue.addTask(new Task("1", "Normal task", false));
queue.addTask(new Task("2", "Priority task", true));
queue.addUrgentTask(new Task("3", "Urgent task", true));

// Order should be: 3 (urgent priority), 2 (priority), 1 (normal)
assert queue.getNextTask().get().id().equals("3");

// Test Undo/Redo
UndoRedoManager<String> editor = new UndoRedoManager<>("Hello");
editor.saveState("Hello World");
editor.saveState("Hello World!");

assert editor.undo().get().equals("Hello World");
assert editor.redo().get().equals("Hello World!");
editor.saveState("Hi");  // Clears redo
assert !editor.canRedo();
```

---

## Exercise 6.4: Collections Utilities (30 minutes)

### Scenario
Use Collections utility methods to solve common problems.

### Requirements

**Create CollectionUtilities class demonstrating these methods:**
```java
public class CollectionUtilities {

    // Shuffle a deck of cards and deal hands
    public static List<List<Card>> shuffleAndDeal(List<Card> deck,
                                                   int numPlayers,
                                                   int cardsPerPlayer) { }

    // Rotate a list by N positions
    public static <T> void rotateList(List<T> list, int positions) { }

    // Find frequency of each element
    public static <T> Map<T, Integer> getFrequencyMap(Collection<T> collection) { }

    // Check if two collections have no common elements
    public static <T> boolean areDisjoint(Collection<T> c1, Collection<T> c2) { }

    // Find the most common element
    public static <T> Optional<T> findMostCommon(Collection<T> collection) { }

    // Swap elements at two positions
    public static <T> void swapElements(List<T> list, int i, int j) { }

    // Fill list with repeated value
    public static <T> List<T> createFilledList(T value, int count) { }

    // Reverse a list (return new list, don't modify original)
    public static <T> List<T> reversed(List<T> original) { }

    // Binary search with custom comparator
    public static <T> int binarySearch(List<T> sortedList, T key,
                                       Comparator<T> comparator) { }

    // Find min and max in one pass
    public static <T> MinMax<T> findMinMax(Collection<T> collection,
                                           Comparator<T> comparator) { }

    // Create synchronized wrapper
    public static <T> List<T> synchronizedCopy(List<T> list) { }

    // Create unmodifiable view
    public static <T> List<T> unmodifiableView(List<T> list) { }
}

public record Card(String suit, String rank) {}
public record MinMax<T>(T min, T max) {}
```

### Test Cases
```java
// Test shuffle and deal
List<Card> deck = createStandardDeck();  // 52 cards
List<List<Card>> hands = CollectionUtilities.shuffleAndDeal(deck, 4, 5);
assert hands.size() == 4;
assert hands.get(0).size() == 5;

// Test frequency
List<String> words = List.of("apple", "banana", "apple", "cherry", "apple");
Map<String, Integer> freq = CollectionUtilities.getFrequencyMap(words);
assert freq.get("apple") == 3;

// Test most common
Optional<String> mostCommon = CollectionUtilities.findMostCommon(words);
assert mostCommon.get().equals("apple");

// Test disjoint
List<Integer> set1 = List.of(1, 2, 3);
List<Integer> set2 = List.of(4, 5, 6);
List<Integer> set3 = List.of(3, 4, 5);
assert CollectionUtilities.areDisjoint(set1, set2);
assert !CollectionUtilities.areDisjoint(set1, set3);

// Test rotate
List<Integer> numbers = new ArrayList<>(List.of(1, 2, 3, 4, 5));
CollectionUtilities.rotateList(numbers, 2);
// Result: [4, 5, 1, 2, 3]

// Test min/max
List<Integer> values = List.of(5, 2, 8, 1, 9, 3);
MinMax<Integer> result = CollectionUtilities.findMinMax(
    values, Comparator.naturalOrder()
);
assert result.min() == 1;
assert result.max() == 9;
```

---

## Exercise 6.5: Custom Collection Implementation (1.5 hours)

### Scenario
Implement a custom collection that combines features of multiple collection types.

### Requirements

**Create LimitedSortedUniqueList class:**
```java
public class LimitedSortedUniqueList<T> implements Iterable<T> {
    private final int maxSize;
    private final Comparator<T> comparator;
    private final List<T> elements;

    // Create with max size and custom comparator
    public LimitedSortedUniqueList(int maxSize, Comparator<T> comparator) { }

    // Create with max size and natural ordering
    public LimitedSortedUniqueList(int maxSize) { }

    // Add element (maintains sorted order, rejects duplicates)
    // If at max size, only adds if element would be in top N
    public boolean add(T element) { }

    // Add all elements from collection
    public int addAll(Collection<T> elements) { }

    // Remove element
    public boolean remove(T element) { }

    // Check if contains element
    public boolean contains(T element) { }

    // Get element at index
    public T get(int index) { }

    // Get first (smallest) element
    public Optional<T> first() { }

    // Get last (largest) element
    public Optional<T> last() { }

    // Get current size
    public int size() { }

    // Check if full
    public boolean isFull() { }

    // Check if empty
    public boolean isEmpty() { }

    // Get as unmodifiable list
    public List<T> toList() { }

    // Clear all elements
    public void clear() { }

    // Iterator (required by Iterable)
    @Override
    public Iterator<T> iterator() { }

    // Stream support
    public Stream<T> stream() { }
}
```

**Use cases:**
- Top N leaderboard (highest scores)
- Recent unique visitors (most recent N)
- Priority task list (top N urgent tasks)

### Test Cases
```java
// Test as leaderboard (top 5 scores, highest first)
LimitedSortedUniqueList<Integer> leaderboard =
    new LimitedSortedUniqueList<>(5, Comparator.reverseOrder());

leaderboard.add(100);
leaderboard.add(85);
leaderboard.add(92);
leaderboard.add(78);
leaderboard.add(95);

assert leaderboard.size() == 5;
assert leaderboard.first().get() == 100;  // Highest
assert leaderboard.last().get() == 78;    // Lowest in top 5

// Adding lower score doesn't make it in
leaderboard.add(70);
assert leaderboard.size() == 5;
assert !leaderboard.contains(70);

// Adding higher score bumps out lowest
leaderboard.add(88);
assert leaderboard.contains(88);
assert !leaderboard.contains(78);  // Bumped out

// Test uniqueness
assert !leaderboard.add(100);  // Duplicate rejected
assert leaderboard.size() == 5;

// Test iteration
for (Integer score : leaderboard) {
    System.out.println(score);
}
// Output: 100, 95, 92, 88, 85 (sorted order)

// Test with custom objects
record Player(String name, int score) {}
LimitedSortedUniqueList<Player> playerBoard = new LimitedSortedUniqueList<>(
    3,
    Comparator.comparingInt(Player::score).reversed()
);

playerBoard.add(new Player("Alice", 100));
playerBoard.add(new Player("Bob", 150));
playerBoard.add(new Player("Charlie", 120));
playerBoard.add(new Player("Dave", 90));   // Won't make it
playerBoard.add(new Player("Eve", 200));   // Bumps out Alice

assert playerBoard.first().get().name().equals("Eve");
```

---

## Submission Checklist

- [ ] All Comparators handle null values appropriately
- [ ] TreeSet/TreeMap maintain correct ordering
- [ ] Deque implementations handle edge cases (empty, single element)
- [ ] Collections utility methods use proper generic types
- [ ] Custom collection maintains invariants (sorted, unique, limited)
- [ ] equals/hashCode implemented correctly for collection elements
- [ ] Iterator implementations are fail-fast where appropriate
- [ ] All methods have appropriate time complexity
- [ ] Unit tests cover edge cases and boundary conditions
- [ ] No concurrent modification exceptions in single-threaded code
