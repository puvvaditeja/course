# Week 04 - MCQ Answers

This document contains answers and explanations for the Week 04 multiple choice questions on Debugging and Reverse Engineering.

---

### Question 1
**Answer: B) The process of finding and fixing errors in code**

Debugging is the systematic process of identifying, analyzing, and removing errors (bugs) from software. It involves reproducing the problem, isolating the cause, and implementing a fix.

---

### Question 2
**Answer: C) Syntax error**

Syntax errors are detected during compilation because they violate the language grammar rules. Examples include missing semicolons, unclosed brackets, and misspelled keywords.

---

### Question 3
**Answer: C) Ctrl+F8**

In IntelliJ IDEA, Ctrl+F8 (Cmd+F8 on Mac) toggles a breakpoint on the current line. You can also click in the gutter next to the line number.

---

### Question 4
**Answer: B) Attempting to use a null reference**

NullPointerException is thrown when code attempts to use a null reference where an object is required, such as calling a method on null or accessing a field of a null object.

---

### Question 5
**Answer: B) Executes current line without entering methods**

Step Over (F8) executes the current line completely. If the line contains method calls, those methods run but the debugger doesn't step inside them.

---

### Question 6
**Answer: B) Analyzing software to understand without source code**

Reverse engineering involves analyzing compiled or deployed software to understand its functionality, structure, and behavior when original source code or documentation is unavailable.

---

### Question 7
**Answer: C) Frames panel**

The Frames panel shows the call stack - the sequence of method calls that led to the current execution point. You can click on any frame to see its local variables.

---

### Question 8
**Answer: C) Shift+F9**

Shift+F9 starts the debugger in IntelliJ IDEA. Shift+F10 runs without debugging. You can also click the bug icon in the toolbar.

---

### Question 9
**Answer: B) A breakpoint that pauses only when a condition is true**

Conditional breakpoints include a boolean expression. Execution pauses only when that condition evaluates to true, which is useful for debugging specific cases in loops or with specific data.

---

### Question 10
**Answer: B) JD-GUI**

JD-GUI is a popular graphical decompiler that converts compiled .class files back to readable Java source code. Other decompilers include CFR, Procyon, and Fernflower.

---

### Question 11
**Answer: B) Enters the method being called**

Step Into (F7) enters the method being called on the current line, allowing you to debug inside that method. Use this when you need to investigate what happens inside a method.

---

### Question 12
**Answer: B) A breakpoint that triggers when a variable is modified**

A watchpoint (or field watchpoint) pauses execution when a specific variable or field is read or modified. This is useful for tracking unexpected changes to values.

---

### Question 13
**Answer: B) Executing Java expressions during debugging**

Evaluate Expression (Alt+F8) lets you type and execute any Java expression while debugging. You can inspect variables, call methods, and test hypotheses about the code.

---

### Question 14
**Answer: C) javap -c**

The `javap -c` command disassembles class files to show the bytecode instructions. The -c flag shows the code (bytecode), while -p shows private members.

---

### Question 15
**Answer: B) The sequence of method calls leading to an error**

A stack trace shows the chain of method calls from the current point back to the program's entry point. It includes class names, method names, file names, and line numbers.

---

### Question 16
**Answer: C) Logic error**

Logic errors produce incorrect results but don't cause crashes or error messages. The program runs normally but doesn't do what was intended, making these bugs harder to find.

---

### Question 17
**Answer: B) Completes current method and returns to caller**

Step Out (Shift+F8) runs the rest of the current method and pauses when returning to the calling method. Use this when you've seen enough inside a method.

---

### Question 18
**Answer: B) Analyzing code without executing it**

Static analysis examines source code or bytecode without running it. Tools like SpotBugs, PMD, and decompilers perform static analysis to find potential issues or understand code structure.

---

### Question 19
**Answer: B) ArrayIndexOutOfBoundsException**

ArrayIndexOutOfBoundsException is thrown when accessing an array with an index that is negative or greater than or equal to the array's length.

---

### Question 20
**Answer: B) Current variable values in scope**

The Variables panel displays all variables visible in the current scope, including local variables, parameters, and object fields. You can expand objects to see their internal state.

---

### Question 21
**Answer: B) Explaining code to find bugs through verbalization**

Rubber duck debugging involves explaining your code line-by-line to an inanimate object (like a rubber duck). The act of verbalization often reveals the bug without needing the "listener" to respond.

---

### Question 22
**Answer: B) Converts bytecode back to source code**

A decompiler reverses the compilation process, converting compiled bytecode (.class files) back into human-readable source code. The result may not match the original exactly but is usually understandable.

---

### Question 23
**Answer: B) Alt+F9**

Run to Cursor (Alt+F9) continues execution until it reaches the line where your cursor is positioned. This is faster than setting a temporary breakpoint.

---

### Question 24
**Answer: C) Reproduce the bug**

The first step is always reproducing the bug consistently. If you can't reproduce it, you can't verify that a fix works. Document exact steps, inputs, and environment needed to trigger the bug.

---

### Question 25
**Answer: B) Continues execution until next breakpoint**

Resume (F9) continues normal program execution until the next breakpoint is hit or the program ends. Use this to quickly skip to the next interesting location.

---

### Question 26
**Answer: B) Disassemble class files**

javap is the Java class file disassembler. It shows the structure of compiled classes including methods, fields, and bytecode instructions. It's built into the JDK.

---

### Question 27
**Answer: B) Objects not released, causing growing memory usage**

A memory leak occurs when objects that are no longer needed aren't released because something still holds a reference to them. This causes heap usage to grow continuously.

---

### Question 28
**Answer: B) Ctrl+Shift+F8**

Ctrl+Shift+F8 opens the Breakpoints dialog showing all breakpoints. You can enable/disable, add conditions, and configure each breakpoint from this dialog.

---

### Question 29
**Answer: B) Systematically narrowing down the problem area**

Divide and conquer debugging splits the problem space in half repeatedly to isolate the bug. Test the middle point, then narrow to the half that contains the problem.

---

### Question 30
**Answer: B) Observing software behavior during execution**

Dynamic analysis observes software while it runs. This includes debugging, profiling, and tracing. It reveals actual behavior rather than potential issues.

---

### Question 31
**Answer: B) Entering even library/framework methods**

Force Step Into (Alt+Shift+F7) enters any method, including JDK classes and third-party libraries that are normally skipped. Use this to debug framework behavior.

---

### Question 32
**Answer: B) Infinite recursion or very deep call stack**

StackOverflowError occurs when the call stack exceeds its limit, usually due to infinite recursion (a method calling itself without a proper base case).

---

### Question 33
**Answer: B) All objects in memory at a point in time**

A heap dump captures all objects on the heap at a specific moment. Analyzing it reveals memory usage patterns, retained objects, and potential memory leaks.

---

### Question 34
**Answer: B) Track custom expressions across debugging**

The Watches panel lets you add expressions that are evaluated at each breakpoint. Unlike Variables (which shows current scope), Watches persist and show whatever you're tracking.

---

### Question 35
**Answer: B) A Java decompiler**

CFR (Class File Reader) is a modern Java decompiler that handles recent Java features well including lambdas and streams. It can be used from command line for batch processing.

---

### Question 36
**Answer: B) Recording application events for debugging**

Logging records events, errors, and diagnostic information during application execution. Different log levels (ERROR, WARN, INFO, DEBUG, TRACE) control verbosity.

---

### Question 37
**Answer: B) Choose which method to enter in chained calls**

Smart Step Into (Shift+F7) lets you choose which method to enter when a line has multiple method calls, like `user.getAddress().getCity()`. A popup shows available methods.

---

### Question 38
**Answer: B) Threads waiting for each other indefinitely**

A deadlock occurs when two or more threads are each waiting for a resource held by another thread in the cycle. No thread can proceed, causing the application to hang.

---

### Question 39
**Answer: B) .class**

Compiled Java classes have the .class extension. These files contain bytecode that runs on the JVM. The .java extension is for source files.

---

### Question 40
**Answer: B) Eclipse MAT**

Eclipse Memory Analyzer Tool (MAT) is used to analyze heap dumps. It identifies memory leaks, shows object retention, and calculates retained sizes.

---

### Question 41
**Answer: B) Alt+F8**

Alt+F8 opens the Evaluate Expression dialog where you can type and execute any Java expression while paused in the debugger.

---

### Question 42
**Answer: B) Application performance (CPU, memory usage)**

Profiling measures how resources are used - which methods consume the most CPU time, where memory is allocated, how threads behave. This identifies performance bottlenecks.

---

### Question 43
**Answer: B) JAR metadata like main class and version**

MANIFEST.MF contains JAR metadata including the main class (entry point), class path, version information, and other attributes. Spring Boot JARs include additional boot metadata.

---

### Question 44
**Answer: B) A breakpoint that triggers when a specific exception is thrown**

Exception breakpoints pause execution when a specific exception type is thrown, before it's caught. This helps find where exceptions originate, not just where they're caught.

---

### Question 45
**Answer: B) Right-click in Variables panel > Set Value**

In the Variables panel, right-click a variable and select "Set Value" to change it during debugging. This lets you test different scenarios without modifying code.

---

### Question 46
**Answer: B) Show all threads and their current state**

A thread dump shows all threads in the JVM, their states (RUNNABLE, BLOCKED, WAITING, etc.), and their stack traces. This helps identify deadlocks and blocked threads.

---

### Question 47
**Answer: B) Intermediate code executed by the JVM**

Bytecode is the platform-independent intermediate representation that Java source code compiles to. The JVM executes bytecode, making Java "write once, run anywhere."

---

### Question 48
**Answer: D) TRACE**

Log levels from least to most verbose: ERROR < WARN < INFO < DEBUG < TRACE. TRACE logs the most detailed information, typically used for fine-grained debugging.

---

### Question 49
**Answer: B) Re-executes the current method from the start**

Drop Frame resets execution to the beginning of the current method (when possible). This lets you re-run a method with different variable values set via Set Value.

---

### Question 50
**Answer: B) Monitoring and profiling Java applications**

VisualVM is a visual tool for monitoring JVM applications. It shows CPU usage, memory, threads, and allows profiling and heap dump analysis.

---

### Question 51
**Answer: B) Modifying a collection while iterating over it**

ConcurrentModificationException is thrown when a collection is modified (add/remove) while being iterated using an Iterator or enhanced for loop. Use Iterator.remove() or CopyOnWriteArrayList instead.

---

### Question 52
**Answer: B) Temporarily disables all breakpoints**

Mute Breakpoints temporarily ignores all breakpoints without removing them. Click again to re-enable. This is useful when you want to run without stopping but keep breakpoints for later.

---

### Question 53
**Answer: B) They pause only when specific conditions are met**

Conditional breakpoints avoid stopping at every iteration of a loop. Instead of pausing 10,000 times, you can pause only when `i == 5000` or `user.getId() == 42`.

---

### Question 54
**Answer: B) Understanding how software works internally**

Legitimate reverse engineering is used to learn from existing software, understand library internals, maintain legacy systems, and analyze APIs for integration purposes.

---

### Question 55
**Answer: B) Adding output statements to trace execution**

Print statement debugging adds System.out.println() or log statements to trace program flow and inspect variable values. Simple but requires cleanup.

---

### Question 56
**Answer: B) Java Stream operations step by step**

The Stream Debugger visualizes Java Stream pipelines, showing intermediate results at each operation (filter, map, etc.). Click "Trace Current Stream Chain" when paused in a stream.

---

### Question 57
**Answer: B) A bug that reappears after being fixed**

A regression bug is functionality that worked before but broke after a change. Regression testing catches these. Git bisect helps find the commit that introduced the regression.

---

### Question 58
**Answer: C) Fernflower**

IntelliJ IDEA uses Fernflower as its built-in decompiler. When you open a .class file or navigate to library code without sources, Fernflower automatically decompiles it.

---

### Question 59
**Answer: B) It clutters code and must be removed**

Print debugging's main disadvantage is adding temporary code that clutters the source and must be removed before committing. It also doesn't allow interaction or inspection like a debugger.

---

### Question 60
**Answer: C) Ctrl+F2**

Ctrl+F2 stops the debugger and terminates the application. The red square stop button in the toolbar does the same thing.

---

## Score Guide

| Score | Level |
|-------|-------|
| 54-60 | Excellent - Ready for advanced debugging |
| 45-53 | Good - Solid understanding |
| 36-44 | Fair - Review weak areas |
| Below 36 | Needs improvement - Study materials again |

## Key Concepts to Remember

### Debugging Shortcuts (IntelliJ)
- **Shift+F9**: Start debugging
- **Ctrl+F8**: Toggle breakpoint
- **F8**: Step Over
- **F7**: Step Into
- **Shift+F8**: Step Out
- **F9**: Resume
- **Alt+F8**: Evaluate Expression
- **Ctrl+F2**: Stop

### Debugging Process
1. Reproduce the bug
2. Isolate the problem area
3. Identify the root cause
4. Fix the bug
5. Verify the fix

### Reverse Engineering Tools
- **JD-GUI, CFR**: Decompilers
- **javap**: Bytecode viewer
- **VisualVM**: Profiler/Monitor
- **Eclipse MAT**: Memory analysis
