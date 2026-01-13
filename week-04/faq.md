# Week 04 - Interview FAQ

This document contains frequently asked interview questions and comprehensive answers for Week 04 topics: Debugging and Reverse Engineering.

---

## Table of Contents

1. [Debugging Fundamentals](#debugging-fundamentals)
2. [IntelliJ IDEA Debugging](#intellij-idea-debugging)
3. [Debugging Techniques](#debugging-techniques)
4. [Reverse Engineering Basics](#reverse-engineering-basics)
5. [Java Decompilation](#java-decompilation)
6. [Analysis Tools](#analysis-tools)

---

## Debugging Fundamentals

### Q1: What is debugging? Why is it important in software development?

**Answer:**

**Debugging** is the systematic process of identifying, analyzing, and removing errors (bugs) from computer programs. It involves finding the root cause of unexpected behavior and fixing it without introducing new bugs.

**Why debugging is important:**

| Aspect | Impact |
|--------|--------|
| **Software Quality** | Ensures reliable, predictable application behavior |
| **User Experience** | Prevents crashes, data loss, and frustration |
| **Development Efficiency** | Reduces time spent on recurring issues |
| **Maintenance Cost** | Early bug detection saves exponential costs later |
| **Professional Growth** | Sharpens problem-solving and analytical skills |

**The debugging process:**
```
Reproduce → Isolate → Identify → Fix → Verify
```

**Key point:** Good debugging skills distinguish senior developers from juniors. It's not just about fixing bugs but understanding why they occur and preventing similar issues in the future.

---

### Q2: What are the different types of bugs in software?

**Answer:**

Bugs are categorized by when they occur and their nature:

**1. Syntax Errors:**
- Detected at compile time
- Examples: Missing semicolons, unclosed brackets, typos
```java
// Syntax error - missing semicolon
int x = 5  // Compile error
```

**2. Runtime Errors:**
- Occur during program execution
- Examples: NullPointerException, ArrayIndexOutOfBoundsException
```java
String s = null;
s.length();  // NullPointerException at runtime
```

**3. Logic Errors:**
- Program runs but produces incorrect results
- Hardest to find - no errors thrown
```java
// Logic error - off-by-one
for (int i = 0; i <= arr.length; i++) {  // Should be < not <=
    System.out.println(arr[i]);
}
```

**4. Semantic Errors:**
- Code is syntactically correct but doesn't do what was intended
```java
// Semantic error - wrong variable used
int total = price * price;  // Should be price * quantity
```

**5. Concurrency Bugs:**
- Race conditions, deadlocks, thread starvation
- Occur in multithreaded applications
- Often intermittent and hard to reproduce

**6. Performance Bugs:**
- Memory leaks, infinite loops, N+1 queries
- Application works but is slow or resource-intensive

---

### Q3: Explain the scientific method for debugging.

**Answer:**

Debugging should follow a systematic approach similar to the scientific method:

**Step 1: Reproduce the Bug**
- Consistently recreate the issue
- Document exact steps, inputs, and environment
- If you can't reproduce it, you can't verify a fix

**Step 2: Gather Information**
- Collect error messages, stack traces, logs
- Note when the bug started occurring
- Identify what changed recently

**Step 3: Form a Hypothesis**
- Based on evidence, guess the likely cause
- "The NullPointerException probably occurs because user data isn't loaded"

**Step 4: Test the Hypothesis**
- Use debugging tools to verify
- Set breakpoints at suspected locations
- Inspect variable values

**Step 5: Fix and Verify**
- Implement a targeted fix
- Verify the fix resolves the issue
- Ensure no new bugs were introduced

**Example:**
```java
// Bug: calculateTotal returns 0

// Step 1: Reproduce
calculateTotal(order);  // Returns 0

// Step 2: Gather info - order has 3 items

// Step 3: Hypothesis - items list might be empty or null

// Step 4: Test - set breakpoint, inspect order.getItems()
// Found: items exists but prices are all 0.0

// Step 5: Fix - prices weren't being set when items added
```

---

### Q4: What is the difference between a breakpoint and a watchpoint?

**Answer:**

| Feature | Breakpoint | Watchpoint |
|---------|------------|------------|
| **Purpose** | Pause at specific line | Pause when variable changes |
| **Trigger** | Execution reaches the line | Variable is read/modified |
| **Location** | Set on code lines | Set on variables/fields |
| **Use Case** | Examine state at specific point | Track unexpected changes |
| **Icon (IntelliJ)** | Red circle | Red eye |

**Breakpoint Example:**
```java
public void processOrder(Order order) {
    validate(order);          // Line breakpoint here
    calculateTotal(order);    // Execution pauses before this line
    save(order);
}
```

**Watchpoint Example:**
```java
public class Account {
    private double balance;   // Field watchpoint here

    // Debugger pauses whenever balance is modified
    // Helps find unexpected balance changes
}
```

**When to use:**
- **Breakpoints**: When you know where to look
- **Watchpoints**: When a value changes unexpectedly and you don't know where

---

### Q5: What are conditional breakpoints? When would you use them?

**Answer:**

**Conditional breakpoints** pause execution only when a specified condition is true. They're essential for debugging issues that occur under specific circumstances.

**Setting a conditional breakpoint:**
1. Set a regular breakpoint
2. Right-click the breakpoint
3. Enter a boolean condition

**Use cases:**

**1. Debugging specific data:**
```java
for (User user : users) {  // Condition: user.getId() == 42
    processUser(user);
}
// Only pauses for user with ID 42
```

**2. Iteration debugging:**
```java
for (int i = 0; i < 10000; i++) {  // Condition: i == 5000
    process(i);
}
// Pauses only at iteration 5000
```

**3. Error conditions:**
```java
public void saveOrder(Order order) {  // Condition: order.getTotal() < 0
    // Pauses only when total is negative (invalid state)
}
```

**4. Null detection:**
```java
public void process(Data data) {  // Condition: data.getValue() == null
    // Pauses when value is null
}
```

**Benefits:**
- Avoid stepping through thousands of irrelevant iterations
- Focus on specific problematic cases
- Much faster than manual stepping

---

## IntelliJ IDEA Debugging

### Q6: Explain the different stepping actions in IntelliJ debugger.

**Answer:**

IntelliJ provides several stepping actions to control execution flow:

| Action | Shortcut | Description |
|--------|----------|-------------|
| **Step Over** | F8 | Execute current line, don't enter methods |
| **Step Into** | F7 | Enter the called method |
| **Step Out** | Shift+F8 | Complete current method, return to caller |
| **Run to Cursor** | Alt+F9 | Continue to cursor position |
| **Force Step Into** | Alt+Shift+F7 | Enter any method, including libraries |
| **Smart Step Into** | Shift+F7 | Choose which method to enter in chained calls |
| **Resume** | F9 | Continue to next breakpoint |

**Practical example:**
```java
public void processOrder(Order order) {
    validate(order);        // F8 - Step Over (skip validation details)

    double total = calculateTotal(order);  // F7 - Step Into (see calculation)

    applyDiscount(order);   // F7 then Shift+F8 - Step Into, then Out

    sendConfirmation(order);  // Alt+F9 - Run to Cursor (skip to here)
}
```

**When to use each:**
- **Step Over**: For trusted/simple code
- **Step Into**: To investigate a method's behavior
- **Step Out**: When you've seen enough inside a method
- **Force Step Into**: To debug library code
- **Smart Step Into**: For `user.getAddress().getCity().toUpperCase()`

---

### Q7: What is the Evaluate Expression feature? How is it useful?

**Answer:**

**Evaluate Expression** (Alt+F8) allows you to execute any Java expression while paused in the debugger. It's one of the most powerful debugging features.

**Capabilities:**

**1. Inspect complex expressions:**
```java
// Evaluate:
order.getItems().stream().mapToDouble(Item::getPrice).sum()
// Result: 150.0
```

**2. Call methods:**
```java
// Evaluate:
userService.findById(42)
// Returns the user object for inspection
```

**3. Test hypotheses:**
```java
// Bug hypothesis: discount calculation is wrong
// Evaluate:
price * 0.15  // Expected: 15.0
price * discountRate  // Actual: 0.0 - discountRate is 0!
```

**4. Modify state (careful!):**
```java
// Evaluate:
order.setStatus("COMPLETED")
// Changes the order's status during debugging
```

**5. Create objects:**
```java
// Evaluate:
new BigDecimal("100.50").multiply(new BigDecimal("1.08"))
// Test calculations
```

**Best practices:**
- Use for read-only inspection when possible
- Be careful with methods that have side effects
- Great for testing potential fixes before coding

---

### Q8: How do you debug multithreaded applications in IntelliJ?

**Answer:**

Debugging multithreaded applications requires special techniques:

**1. Thread-Specific Breakpoints:**
- Right-click breakpoint > Suspend: Thread (not All)
- Only pauses the current thread, others continue
- Helps observe concurrent behavior

**2. Frames Panel:**
```
Thread-1 [RUNNING]
  └─ processOrder() line 45
  └─ main() line 12

Thread-2 [BLOCKED]
  └─ updateInventory() line 78
  └─ run() line 5
```
- Switch between threads to see each call stack
- Identify which thread is blocked and why

**3. Thread Dump:**
- Run > Get Thread Dump
- Shows all threads and their states
- Useful for finding deadlocks

**4. Debugging Race Conditions:**
```java
// Breakpoint with condition:
// Thread.currentThread().getName().equals("Thread-2")

public synchronized void updateCounter() {
    counter++;  // Break only for Thread-2
}
```

**5. Detecting Deadlocks:**
- If application hangs, take thread dump
- Look for threads in BLOCKED state waiting on each other
- IntelliJ highlights deadlock cycles

**Tips:**
- Use logging alongside debugging for timing issues
- Conditional breakpoints help isolate specific threads
- Remember that debugging changes timing and may hide race conditions

---

### Q9: What are the key panels in IntelliJ's Debug window?

**Answer:**

The Debug window has several essential panels:

**1. Frames Panel (Call Stack):**
```
processPayment()      ← Current location
validateCard()
processOrder()
main()
```
- Shows method call hierarchy
- Click any frame to see its variables
- Navigate up/down to understand flow

**2. Variables Panel:**
```
this = PaymentService@1234
order = Order@5678
  ├─ id = 42
  ├─ items = ArrayList (size=3)
  └─ total = 150.0
amount = 150.0
```
- Shows local variables and fields
- Expandable for object inspection
- Right-click to modify values

**3. Watches Panel:**
```
order.getTotal()           = 150.0
items.size()               = 3
customer.getName()         = "John"
```
- Custom expressions to track
- Persist across debug sessions
- Useful for calculated values

**4. Console:**
- Shows program output
- Enter input if program requires

**5. Debug Toolbar:**
- Resume, Pause, Stop
- Step Over, Into, Out
- Evaluate Expression button

**Pro tip:** Arrange panels based on your workflow. You can detach and resize each panel.

---

### Q10: How do you use the Memory View in IntelliJ debugger?

**Answer:**

The Memory View helps analyze object instances and detect memory issues.

**Accessing Memory View:**
- Run > Debug Windows > Memory

**Features:**

**1. Class Instance Count:**
```
Class                    Count
java.lang.String         15,234
com.example.User         500
com.example.Order        150
```
- See how many instances exist
- Identify potential memory leaks

**2. Instance Tracking:**
- Double-click a class to see all instances
- Inspect individual objects
- Find which objects hold references

**3. Comparing States:**
- Take snapshot at time A
- Run code
- Take snapshot at time B
- Compare: which instances were created/removed?

**Use cases:**

**Memory Leak Detection:**
```java
// If User count keeps increasing after each request
// and never decreases, there's likely a leak
```

**Object Creation Analysis:**
```java
// Check if expensive objects are being created
// more often than expected
```

**Best practice:** Use with profiling tools (VisualVM, JProfiler) for comprehensive memory analysis.

---

## Debugging Techniques

### Q11: What is rubber duck debugging?

**Answer:**

**Rubber duck debugging** is a method where you explain your code line-by-line to an inanimate object (traditionally a rubber duck). The act of verbalizing often reveals the bug.

**How it works:**
1. Place a rubber duck (or any object) on your desk
2. Explain what your code is supposed to do
3. Go through the code line by line, explaining each step
4. The bug often becomes apparent during explanation

**Example:**
```java
public String formatName(String firstName, String lastName) {
    // "I'm getting the first character of firstName..."
    String initial = firstName.substring(0, 1);

    // "Then uppercasing it..."
    initial = initial.toUpperCase();

    // "Wait - what if firstName is empty or null?"
    // BUG FOUND through explanation!

    return initial + ". " + lastName;
}
```

**Why it works:**
- Forces you to slow down and think carefully
- Verbal explanation engages different parts of the brain
- Assumptions become explicit when verbalized
- Often faster than random debugging

**Alternatives:**
- Explain to a colleague (pair debugging)
- Write comments explaining each line
- Teach the code to someone else

---

### Q12: Explain the divide and conquer debugging technique.

**Answer:**

**Divide and conquer** systematically narrows down the problem area by testing halves of the code or data.

**Process:**
```
1. Identify the range where bug could exist
2. Test the middle point
3. If bug is before middle → test first half
4. If bug is after middle → test second half
5. Repeat until bug location is isolated
```

**Example - Finding problematic data:**
```java
public void processList(List<Data> items) {
    // Bug: Something fails with 1000 items

    // Test first half (0-500)
    List<Data> firstHalf = items.subList(0, 500);
    process(firstHalf);  // Works!

    // Test second half (500-1000)
    List<Data> secondHalf = items.subList(500, 1000);
    process(secondHalf);  // Fails!

    // Test 500-750
    // Continue narrowing...
}
```

**Example - Git bisect:**
```bash
git bisect start
git bisect bad          # Current commit is broken
git bisect good v1.0    # v1.0 was working

# Git checks out middle commit
# Test and mark as good/bad
# Repeat until problematic commit found
```

**When to use:**
- Large codebases with unclear bug location
- Data-dependent bugs in large datasets
- Regression bugs (worked before, broken now)

---

### Q13: What is print statement debugging? What are its limitations?

**Answer:**

**Print statement debugging** uses output statements to trace program execution and inspect values.

**Basic usage:**
```java
public int calculate(int a, int b) {
    System.out.println("Entering calculate: a=" + a + ", b=" + b);

    int result = a + b;
    System.out.println("After addition: result=" + result);

    result = result * 2;
    System.out.println("After multiplication: result=" + result);

    return result;
}
```

**Advantages:**
- Simple and universally available
- Works in any environment
- No special tools needed
- Can be left in as logging

**Limitations:**

| Limitation | Problem |
|------------|---------|
| **Code clutter** | Many print statements obscure actual code |
| **No interaction** | Can't pause or inspect further |
| **Performance impact** | Slows down execution |
| **Cleanup required** | Must remove before production |
| **Limited view** | Only see what you print |
| **Timing changes** | May hide race conditions |

**Better alternative - Logging:**
```java
private static final Logger log = LoggerFactory.getLogger(MyClass.class);

public int calculate(int a, int b) {
    log.debug("Entering calculate: a={}, b={}", a, b);
    // Can be disabled in production via configuration
}
```

**When print debugging is okay:**
- Quick one-off debugging
- Environments without debugger access
- Understanding execution flow

---

### Q14: How do you read and interpret a stack trace?

**Answer:**

A **stack trace** shows the sequence of method calls that led to an error. Reading it correctly is essential for debugging.

**Stack trace anatomy:**
```
java.lang.NullPointerException: Cannot invoke method on null object
    at com.example.OrderService.calculateTotal(OrderService.java:45)
    at com.example.OrderService.processOrder(OrderService.java:28)
    at com.example.OrderController.submitOrder(OrderController.java:15)
    at sun.reflect.NativeMethodAccessorImpl.invoke(...)
    at org.springframework.web.servlet.FrameworkServlet.service(...)
```

**Reading order:**
1. **First line**: Exception type and message
   - `NullPointerException` - what went wrong
   - Message explains the specific issue

2. **Top of stack**: Where error occurred
   - `OrderService.java:45` - exact file and line
   - This is where to start investigating

3. **Call chain**: How we got there
   - Read top-to-bottom: most recent to oldest
   - `calculateTotal` was called by `processOrder`
   - `processOrder` was called by `submitOrder`

4. **Framework code**: Can usually ignore
   - `sun.reflect.*`, `org.springframework.*`
   - Focus on your code (`com.example.*`)

**Debugging from stack trace:**
```java
// Go to OrderService.java line 45
public double calculateTotal(Order order) {
    double total = 0;
    for (Item item : order.getItems()) {  // Line 45 - order.getItems() returns null!
        total += item.getPrice();
    }
    return total;
}
```

**Common patterns:**
- `NullPointerException`: Something is null that shouldn't be
- `ArrayIndexOutOfBounds`: Accessing invalid array index
- `ClassCastException`: Wrong type cast
- `Caused by`: Look for root cause in chained exceptions

---

## Reverse Engineering Basics

### Q15: What is reverse engineering in software development?

**Answer:**

**Reverse engineering** is the process of analyzing software to understand its structure, functionality, and behavior without access to original source code or documentation.

**Forward vs. Reverse Engineering:**
```
Forward:   Requirements → Design → Code → Executable
Reverse:   Executable → Analysis → Understanding → Documentation
```

**Types of reverse engineering:**

| Type | Description | Example |
|------|-------------|---------|
| **Static Analysis** | Examine code without running | Decompiling JAR files |
| **Dynamic Analysis** | Observe during execution | Debugging, profiling |
| **Protocol Analysis** | Understand communication | Network packet analysis |
| **Data Analysis** | Understand data structures | File format reverse engineering |

**Legitimate uses:**
- Learning how libraries work internally
- Maintaining legacy systems without documentation
- Security research and vulnerability analysis
- Interoperability and integration
- Malware analysis

**Why developers should know it:**
- Debug third-party library issues
- Understand framework internals
- Analyze competitors' public APIs
- Recover lost documentation

---

### Q16: What are the goals and objectives of reverse engineering?

**Answer:**

**Primary Goals:**

| Goal | Description |
|------|-------------|
| **Understanding** | Comprehend how software works internally |
| **Learning** | Study implementation techniques from real software |
| **Maintenance** | Support systems with lost documentation |
| **Integration** | Understand interfaces for compatibility |
| **Security** | Identify vulnerabilities (authorized testing) |
| **Documentation** | Recreate lost technical documentation |

**Specific Objectives:**

**1. Code Understanding:**
- Identify class hierarchies and relationships
- Map package organization
- Discover design patterns used
- Trace data flow through system

**2. API Discovery:**
- Document undocumented APIs
- Understand method signatures and parameters
- Find hidden functionality

**3. Architecture Recovery:**
```
Objective: Create architecture diagram from compiled code

Result:
┌────────────┐     ┌────────────┐     ┌────────────┐
│ Controller │────▶│  Service   │────▶│ Repository │
└────────────┘     └────────────┘     └────────────┘
```

**4. Security Analysis:**
- Find input validation gaps
- Identify authentication mechanisms
- Analyze encryption implementation

**Ethical considerations:**
- Only reverse engineer software you're authorized to analyze
- Respect intellectual property and licensing
- Use for learning, not for copying proprietary code

---

### Q17: What are the steps involved in reverse engineering?

**Answer:**

A systematic approach to reverse engineering:

**Step 1: Planning**
- Define clear objectives
- Identify legal considerations
- Select appropriate tools
- Set up isolated analysis environment

**Step 2: Reconnaissance**
- Gather initial information
- Identify technologies used
- Map file structure
- Find entry points

```
myapp.jar
├── META-INF/MANIFEST.MF  ← Version info, main class
├── com/example/
│   ├── Main.class         ← Entry point
│   ├── service/
│   └── model/
└── resources/
```

**Step 3: Static Analysis**
- Decompile bytecode
- Analyze class structure
- Identify patterns
- Map dependencies

```bash
# Decompile JAR
java -jar cfr.jar myapp.jar --outputdir ./decompiled
```

**Step 4: Dynamic Analysis**
- Run with debugger attached
- Set strategic breakpoints
- Trace execution flow
- Monitor memory and resources

**Step 5: Hypothesis and Testing**
- Form theories about behavior
- Test predictions
- Validate understanding

**Step 6: Documentation**
- Record findings
- Create diagrams
- Document APIs
- Write analysis report

---

## Java Decompilation

### Q18: What tools are used for Java decompilation?

**Answer:**

**Popular Java decompilers:**

| Tool | Type | Best For |
|------|------|----------|
| **JD-GUI** | GUI | Quick visual inspection |
| **CFR** | CLI | Modern Java features, scripting |
| **Procyon** | CLI | Complex code reconstruction |
| **Fernflower** | Library | IntelliJ's built-in decompiler |
| **jadx** | GUI/CLI | Android APK decompilation |

**JD-GUI:**
```
- Standalone graphical application
- Drag and drop JAR files
- Syntax highlighting and navigation
- Export to ZIP
```

**CFR (Class File Reader):**
```bash
# Decompile single class
java -jar cfr.jar MyClass.class

# Decompile entire JAR
java -jar cfr.jar myapp.jar --outputdir ./output

# With options
java -jar cfr.jar myapp.jar --decodelambdas true
```

**IntelliJ IDEA:**
```
- Open any .class file directly
- Automatic decompilation
- Navigate to library source
- Integrated with debugging
```

**javap (built-in):**
```bash
# Show public methods
javap com.example.MyClass

# Show bytecode
javap -c com.example.MyClass

# Show private members
javap -p com.example.MyClass
```

**Choosing a tool:**
- Quick inspection: JD-GUI or IntelliJ
- Batch processing: CFR
- Android apps: jadx
- Bytecode level: javap

---

### Q19: What is bytecode? How does understanding it help in debugging?

**Answer:**

**Bytecode** is the intermediate representation of Java code that runs on the JVM. It's platform-independent and stored in `.class` files.

**Compilation flow:**
```
Source (.java) → Compiler (javac) → Bytecode (.class) → JVM → Machine Code
```

**Example:**
```java
// Java source
public int add(int a, int b) {
    return a + b;
}

// Bytecode (javap -c)
public int add(int, int);
  Code:
     0: iload_1    // Load first parameter
     1: iload_2    // Load second parameter
     2: iadd       // Add integers
     3: ireturn    // Return result
```

**Common bytecode instructions:**

| Instruction | Purpose |
|-------------|---------|
| `iload` | Load int from local variable |
| `aload` | Load object reference |
| `istore` | Store int to local variable |
| `invokevirtual` | Call instance method |
| `invokestatic` | Call static method |
| `new` | Create new object |
| `getfield` | Get instance field |

**Why understanding bytecode helps:**

**1. Debug compiler optimizations:**
```java
// Source looks correct, but compiler optimized it unexpectedly
```

**2. Understand library behavior:**
```java
// When source isn't available, bytecode reveals logic
```

**3. Performance analysis:**
```java
// See what operations actually happen at runtime
```

**4. Verify code coverage:**
```java
// Ensure all bytecode paths are tested
```

---

### Q20: How do you analyze a JAR file structure?

**Answer:**

JAR files are ZIP archives containing compiled classes and resources.

**Examining JAR contents:**
```bash
# List contents
jar -tf myapp.jar

# Extract contents
jar -xf myapp.jar

# View specific file
jar -xf myapp.jar META-INF/MANIFEST.MF
```

**Spring Boot JAR structure:**
```
myapp.jar
├── BOOT-INF/
│   ├── classes/           # Application code
│   │   └── com/example/
│   │       ├── controller/
│   │       ├── service/
│   │       └── model/
│   └── lib/               # Dependencies
│       ├── spring-boot-*.jar
│       └── spring-web-*.jar
├── META-INF/
│   └── MANIFEST.MF        # Main class, version
└── org/springframework/boot/loader/  # Boot loader
```

**MANIFEST.MF analysis:**
```
Manifest-Version: 1.0
Main-Class: org.springframework.boot.loader.JarLauncher
Start-Class: com.example.Application
Spring-Boot-Version: 3.0.0
```

**Key information to extract:**
- Main class (entry point)
- Dependencies (lib folder)
- Version information
- Build metadata
- Package structure

**Tools for analysis:**
- File archiver (extract as ZIP)
- `jar` command (built-in)
- JD-GUI (visual inspection)
- IntelliJ (navigate compiled code)

---

## Analysis Tools

### Q21: What is VisualVM? How is it used for debugging?

**Answer:**

**VisualVM** is a visual tool for monitoring and troubleshooting Java applications.

**Features:**

| Feature | Use Case |
|---------|----------|
| **Monitor** | CPU, memory, threads overview |
| **Sampler** | Find hot methods without overhead |
| **Profiler** | Detailed performance analysis |
| **Heap Dump** | Analyze memory usage |
| **Thread Dump** | Find deadlocks, blocked threads |

**Connecting to application:**
```bash
# Start application with JMX
java -Dcom.sun.management.jmxremote \
     -Dcom.sun.management.jmxremote.port=9010 \
     -jar myapp.jar

# VisualVM connects automatically to local JVMs
```

**CPU Profiling:**
```
Method                          Self Time    Total Time
processOrder()                  45.2%        82.1%
└── calculateTotal()            30.5%        35.2%
    └── getItemPrice()          4.7%         4.7%
```
- Identifies slow methods
- Shows call hierarchy
- Helps optimize performance

**Memory Analysis:**
```
Class                     Instances    Size
byte[]                    15,234       2.5 MB
String                    12,456       1.2 MB
com.example.Order         500          0.5 MB
```
- Find memory-heavy classes
- Detect potential leaks
- Analyze object retention

**When to use VisualVM:**
- Application is slow
- Memory usage keeps growing
- Threads are deadlocked
- Need to profile without code changes

---

### Q22: What is the difference between static and dynamic analysis?

**Answer:**

| Aspect | Static Analysis | Dynamic Analysis |
|--------|-----------------|------------------|
| **When** | Without running code | While code runs |
| **What** | Examines source/bytecode | Observes behavior |
| **Speed** | Fast, automated | Slower, interactive |
| **Coverage** | All code paths | Only executed paths |
| **False positives** | Higher | Lower |
| **Finds** | Potential issues | Actual issues |

**Static Analysis:**
```java
// Tools analyze code without execution

// Example: SpotBugs finding
public void process(String input) {
    if (input.equals("test")) {  // Warning: possible NPE if input is null
        // ...
    }
}
```

**Tools:**
- SpotBugs, PMD, SonarQube (code quality)
- javap, CFR (bytecode inspection)
- IntelliJ inspections

**Dynamic Analysis:**
```java
// Observe actual behavior during execution

// Example: Debugger showing
input = null  // Runtime value
// NullPointerException when equals() called
```

**Tools:**
- Debuggers (IntelliJ, Eclipse)
- Profilers (VisualVM, JProfiler)
- Tracers and loggers

**Combined approach:**
1. Use static analysis to find potential issues
2. Use dynamic analysis to confirm and investigate
3. Static analysis catches issues early
4. Dynamic analysis reveals actual behavior

---

### Q23: How do you debug memory leaks in Java?

**Answer:**

**Signs of memory leak:**
- Heap usage steadily increases
- OutOfMemoryError eventually occurs
- Application slows down over time

**Debugging process:**

**Step 1: Confirm the leak**
```bash
# Monitor heap over time with VisualVM or JConsole
# Look for steadily increasing used heap
```

**Step 2: Generate heap dump**
```bash
# Automatically on OOM
-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/

# Manually
jmap -dump:format=b,file=heap.hprof <pid>
```

**Step 3: Analyze with Eclipse MAT or VisualVM**
```
Leak Suspects:
1. 10,000 instances of com.example.Session
   - Retained: 500 MB
   - GC Root: HashMap in SessionManager

Problem: Sessions not being removed from HashMap
```

**Step 4: Find the root cause**

Common leak patterns:
```java
// 1. Collections growing unbounded
private static List<Object> cache = new ArrayList<>();
public void add(Object obj) {
    cache.add(obj);  // Never removed!
}

// 2. Listeners not unregistered
button.addActionListener(this);
// Never: button.removeActionListener(this)

// 3. Static references
private static final Map<String, Object> cache = new HashMap<>();
// Objects referenced here live forever

// 4. Unclosed resources
Connection conn = dataSource.getConnection();
// conn.close() never called
```

**Step 5: Fix and verify**
- Remove unnecessary references
- Use weak references for caches
- Ensure proper cleanup in finally blocks
- Use try-with-resources

---

### Q24: What are profilers and when should you use them?

**Answer:**

**Profilers** are tools that measure application performance, identifying where time and resources are spent.

**Types of profiling:**

| Type | Measures | Use Case |
|------|----------|----------|
| **CPU Profiling** | Method execution time | Find slow methods |
| **Memory Profiling** | Object allocation | Find memory issues |
| **Thread Profiling** | Thread states, locks | Find concurrency issues |

**Popular Java profilers:**
- VisualVM (free, basic)
- JProfiler (commercial, comprehensive)
- YourKit (commercial, comprehensive)
- Async Profiler (free, low overhead)
- Java Flight Recorder (built into JDK)

**When to use:**

**1. Performance issues:**
```
User complaint: "Page takes 10 seconds to load"

Profiler shows:
processOrder()     - 8.5 seconds
└── loadItems()    - 8.0 seconds (N+1 query problem!)
```

**2. Memory issues:**
```
Application slows down over time

Profiler shows:
Top allocations:
- byte[] - 500MB (image processing not releasing)
```

**3. Concurrency issues:**
```
Application hangs randomly

Thread profiler shows:
Thread-1: BLOCKED waiting for lock held by Thread-2
Thread-2: BLOCKED waiting for lock held by Thread-1
Deadlock detected!
```

**Sampling vs. Instrumentation:**

| Method | Overhead | Accuracy | Use |
|--------|----------|----------|-----|
| Sampling | Low | Approximate | Production |
| Instrumentation | High | Exact | Development |

---

### Q25: How do you debug issues in production systems?

**Answer:**

Production debugging requires techniques that don't impact users.

**Safe debugging approaches:**

**1. Logging:**
```java
log.info("Processing order {} for user {}", orderId, userId);
log.debug("Order details: {}", order);
log.error("Failed to process order: {}", orderId, exception);
```
- Different log levels (ERROR, WARN, INFO, DEBUG, TRACE)
- Can adjust levels without restart
- Include correlation IDs for request tracing

**2. Remote debugging (with caution):**
```bash
# Enable on server (blocks threads when paused!)
java -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5005

# Connect from IDE
# Only use on non-production or isolated instance
```

**3. APM tools (Application Performance Monitoring):**
- New Relic, Datadog, Dynatrace
- Distributed tracing
- Automatic anomaly detection
- No code changes needed

**4. Thread dumps:**
```bash
# Take thread dump without stopping application
kill -3 <pid>  # Outputs to console/log
jstack <pid>   # Outputs to terminal
```

**5. Heap dumps:**
```bash
# Trigger heap dump
jmap -dump:format=b,file=heap.hprof <pid>
# Analyze offline with Eclipse MAT
```

**6. Feature flags:**
```java
if (featureFlags.isEnabled("debug-mode")) {
    log.debug("Detailed debug info: {}", data);
}
```

**Best practices:**
- Never attach debugger to production (blocks threads)
- Use APM tools for continuous monitoring
- Have runbooks for common issues
- Practice incident response procedures

---

## Quick Reference

### Debugging Shortcuts (IntelliJ)

| Action | Shortcut |
|--------|----------|
| Debug | Shift+F9 |
| Toggle Breakpoint | Ctrl+F8 |
| Step Over | F8 |
| Step Into | F7 |
| Step Out | Shift+F8 |
| Resume | F9 |
| Evaluate Expression | Alt+F8 |
| View Breakpoints | Ctrl+Shift+F8 |

### Common Debugging Commands

```bash
# JVM debugging
jps                    # List Java processes
jstack <pid>           # Thread dump
jmap -heap <pid>       # Heap summary
jmap -dump:format=b,file=heap.hprof <pid>  # Heap dump

# Decompilation
javap -c ClassName     # Show bytecode
jar -tf myapp.jar      # List JAR contents
java -jar cfr.jar MyClass.class  # Decompile
```

### Debugging Decision Tree

```
Problem identified
       │
       ▼
Can reproduce? ──No──▶ Add logging, gather more info
       │
      Yes
       │
       ▼
Know the area? ──No──▶ Use search, divide and conquer
       │
      Yes
       │
       ▼
Set breakpoint
       │
       ▼
Inspect variables
       │
       ▼
Root cause found? ──No──▶ Step through, add watches
       │
      Yes
       │
       ▼
Fix and verify
```
