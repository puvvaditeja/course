# Exercise 5: File Processing

## Learning Objectives
- Read and write CSV files using Java NIO
- Parse structured text data with proper error handling
- Work with Properties files for configuration
- Perform directory operations and file filtering
- Handle file encoding and malformed data gracefully

---

## Exercise 5.1: CSV File Processor (1 hour)

### Scenario
Build a utility to process employee data from CSV files and generate reports.

### Requirements

**1. Create the Employee record:**
```java
public record Employee(
    int id,
    String firstName,
    String lastName,
    String department,
    double salary,
    LocalDate hireDate
) {}
```

**2. Create CsvFileProcessor class with these methods:**

```java
public class CsvFileProcessor {

    // Read employees from CSV file (skip header row)
    // Handle: missing fields, invalid numbers, invalid dates
    // Return list of valid employees, log errors for invalid rows
    public List<Employee> readEmployees(Path filePath) { }

    // Write employees to CSV file with header
    public void writeEmployees(Path filePath, List<Employee> employees) { }

    // Filter employees and write to new file
    public void filterAndWrite(Path source, Path destination,
                               Predicate<Employee> filter) { }

    // Generate salary report grouped by department
    // Format: Department, EmployeeCount, TotalSalary, AverageSalary
    public void generateDepartmentReport(Path source, Path reportPath) { }
}
```

**3. Sample CSV format (employees.csv):**
```csv
id,firstName,lastName,department,salary,hireDate
1,John,Doe,Engineering,75000.00,2020-01-15
2,Jane,Smith,Marketing,65000.00,2019-06-20
3,Bob,Johnson,Engineering,82000.00,2018-03-10
4,Alice,Williams,HR,58000.00,2021-09-01
5,Charlie,Brown,Marketing,70000.00,2020-11-30
```

**4. Handle these error cases:**
- Missing columns (skip row, log error)
- Invalid salary format (skip row, log error)
- Invalid date format (skip row, log error)
- Empty file (return empty list)
- File not found (throw custom exception)

### Test Cases
```java
// Test 1: Read valid CSV
List<Employee> employees = processor.readEmployees(Path.of("employees.csv"));
assert employees.size() == 5;

// Test 2: Filter high earners (salary > 70000)
processor.filterAndWrite(
    Path.of("employees.csv"),
    Path.of("high-earners.csv"),
    emp -> emp.salary() > 70000
);

// Test 3: Generate department report
processor.generateDepartmentReport(
    Path.of("employees.csv"),
    Path.of("department-report.csv")
);
// Expected output:
// Department,EmployeeCount,TotalSalary,AverageSalary
// Engineering,2,157000.00,78500.00
// Marketing,2,135000.00,67500.00
// HR,1,58000.00,58000.00
```

---

## Exercise 5.2: Properties Configuration Manager (45 minutes)

### Scenario
Create a configuration management system using Properties files.

### Requirements

**1. Create AppConfig class:**
```java
public class AppConfig {
    private Properties properties;
    private Path configPath;

    // Load properties from file, create with defaults if not exists
    public AppConfig(Path configPath) { }

    // Get property with type conversion
    public String getString(String key, String defaultValue) { }
    public int getInt(String key, int defaultValue) { }
    public boolean getBoolean(String key, boolean defaultValue) { }
    public double getDouble(String key, double defaultValue) { }

    // Set and save property
    public void setProperty(String key, String value) { }

    // Save all properties to file
    public void save() { }

    // Reload properties from file
    public void reload() { }

    // Get all properties with a prefix (e.g., "db." returns db.host, db.port)
    public Map<String, String> getPropertiesWithPrefix(String prefix) { }
}
```

**2. Default configuration file (app.properties):**
```properties
# Application Settings
app.name=MyApplication
app.version=1.0.0
app.debug=false

# Database Settings
db.host=localhost
db.port=5432
db.name=mydb
db.pool.size=10

# Feature Flags
feature.darkMode=true
feature.analytics=false
```

**3. Implementation requirements:**
- Use `try-with-resources` for all file operations
- Handle missing file by creating with default values
- Support comments in properties file
- Thread-safe read/write operations

### Test Cases
```java
AppConfig config = new AppConfig(Path.of("app.properties"));

// Test type conversions
assert config.getString("app.name", "").equals("MyApplication");
assert config.getInt("db.port", 0) == 5432;
assert config.getBoolean("app.debug", true) == false;

// Test prefix filtering
Map<String, String> dbProps = config.getPropertiesWithPrefix("db.");
assert dbProps.size() == 4;
assert dbProps.containsKey("db.host");

// Test update and save
config.setProperty("app.version", "1.1.0");
config.save();
config.reload();
assert config.getString("app.version", "").equals("1.1.0");
```

---

## Exercise 5.3: Directory Scanner (45 minutes)

### Scenario
Build a utility to scan directories and find files matching specific criteria.

### Requirements

**1. Create DirectoryScanner class:**
```java
public class DirectoryScanner {

    // Find all files matching glob pattern (e.g., "*.java", "**/*.txt")
    public List<Path> findByPattern(Path directory, String globPattern) { }

    // Find files modified within last N days
    public List<Path> findRecentFiles(Path directory, int days) { }

    // Find files larger than specified size (in bytes)
    public List<Path> findLargeFiles(Path directory, long minSize) { }

    // Find duplicate files (same size and content hash)
    public Map<String, List<Path>> findDuplicates(Path directory) { }

    // Get directory statistics
    public DirectoryStats getStats(Path directory) { }

    // Copy files matching pattern to destination
    public int copyMatchingFiles(Path source, Path destination,
                                  String globPattern) { }
}

public record DirectoryStats(
    int totalFiles,
    int totalDirectories,
    long totalSize,
    Map<String, Integer> filesByExtension
) {}
```

**2. Implementation requirements:**
- Use `Files.walk()` or `Files.walkFileTree()` for traversal
- Use `PathMatcher` for glob pattern matching
- Calculate MD5 or SHA-256 hash for duplicate detection
- Handle symbolic links appropriately (don't follow by default)
- Handle access denied errors gracefully

### Test Cases
```java
DirectoryScanner scanner = new DirectoryScanner();

// Test pattern matching
List<Path> javaFiles = scanner.findByPattern(
    Path.of("src"), "**/*.java"
);

// Test recent files (last 7 days)
List<Path> recentFiles = scanner.findRecentFiles(
    Path.of("logs"), 7
);

// Test directory stats
DirectoryStats stats = scanner.getStats(Path.of("project"));
System.out.println("Total files: " + stats.totalFiles());
System.out.println("Total size: " + stats.totalSize() + " bytes");
System.out.println("Java files: " + stats.filesByExtension().get("java"));

// Test find duplicates
Map<String, List<Path>> duplicates = scanner.findDuplicates(Path.of("documents"));
duplicates.forEach((hash, paths) -> {
    if (paths.size() > 1) {
        System.out.println("Duplicate files: " + paths);
    }
});
```

---

## Exercise 5.4: Text File Transformer (1 hour)

### Scenario
Create a pipeline-based text file transformer that can apply multiple transformations.

### Requirements

**1. Create transformation interfaces and classes:**
```java
@FunctionalInterface
public interface LineTransformer {
    String transform(String line);
}

public class TextFileTransformer {
    private List<LineTransformer> transformers = new ArrayList<>();

    // Add transformer to pipeline
    public TextFileTransformer addTransformer(LineTransformer transformer) { }

    // Clear all transformers
    public TextFileTransformer clearTransformers() { }

    // Transform file and write to destination
    public void transform(Path source, Path destination) { }

    // Transform file in place (read all, transform, write back)
    public void transformInPlace(Path file) { }

    // Transform with line filtering (skip lines that don't match)
    public void transformWithFilter(Path source, Path destination,
                                    Predicate<String> filter) { }
}
```

**2. Built-in transformers to implement:**
```java
public class Transformers {
    // Convert to uppercase
    public static LineTransformer toUpperCase() { }

    // Convert to lowercase
    public static LineTransformer toLowerCase() { }

    // Trim whitespace
    public static LineTransformer trim() { }

    // Add line numbers (format: "001: original line")
    public static LineTransformer addLineNumbers() { }

    // Replace pattern with replacement
    public static LineTransformer replace(String pattern, String replacement) { }

    // Remove empty lines
    public static LineTransformer removeEmptyLines() { }

    // Indent lines by N spaces
    public static LineTransformer indent(int spaces) { }

    // Wrap lines at N characters
    public static LineTransformer wrapAt(int maxWidth) { }
}
```

**3. Implementation requirements:**
- Use method chaining (fluent API)
- Handle different line endings (Unix/Windows)
- Preserve or normalize line endings based on configuration
- Support UTF-8 encoding

### Test Cases
```java
TextFileTransformer transformer = new TextFileTransformer();

// Test pipeline transformation
transformer
    .addTransformer(Transformers.trim())
    .addTransformer(Transformers.toUpperCase())
    .addTransformer(Transformers.addLineNumbers())
    .transform(Path.of("input.txt"), Path.of("output.txt"));

// Input:
//   hello world
//   foo bar
// Output:
// 001: HELLO WORLD
// 002: FOO BAR

// Test replace transformer
new TextFileTransformer()
    .addTransformer(Transformers.replace("TODO", "DONE"))
    .transformInPlace(Path.of("notes.txt"));

// Test with filter (only transform non-empty lines)
new TextFileTransformer()
    .addTransformer(Transformers.indent(4))
    .transformWithFilter(
        Path.of("code.txt"),
        Path.of("indented-code.txt"),
        line -> !line.isBlank()
    );
```

---

## Exercise 5.5: Log File Aggregator (1 hour)

### Scenario
Build a system to aggregate and analyze log files from multiple sources.

### Requirements

**1. Create LogEntry record:**
```java
public record LogEntry(
    LocalDateTime timestamp,
    String level,      // INFO, WARN, ERROR, DEBUG
    String source,     // File name or service name
    String message
) implements Comparable<LogEntry> {
    // Compare by timestamp
}
```

**2. Create LogAggregator class:**
```java
public class LogAggregator {

    // Parse log file (format: "2024-01-15 10:30:45 [INFO] Message here")
    public List<LogEntry> parseLogFile(Path logFile) { }

    // Aggregate logs from multiple files
    public List<LogEntry> aggregateLogs(List<Path> logFiles) { }

    // Aggregate all logs from directory matching pattern
    public List<LogEntry> aggregateFromDirectory(Path directory,
                                                  String pattern) { }

    // Filter by time range
    public List<LogEntry> filterByTimeRange(List<LogEntry> logs,
                                            LocalDateTime start,
                                            LocalDateTime end) { }

    // Filter by log level
    public List<LogEntry> filterByLevel(List<LogEntry> logs,
                                        String... levels) { }

    // Search logs by message pattern (regex)
    public List<LogEntry> searchByPattern(List<LogEntry> logs,
                                          String regex) { }

    // Generate summary report
    public LogSummary generateSummary(List<LogEntry> logs) { }

    // Write merged logs to file (sorted by timestamp)
    public void writeMergedLogs(Path destination, List<LogEntry> logs) { }
}

public record LogSummary(
    int totalEntries,
    Map<String, Integer> countByLevel,
    Map<String, Integer> countBySource,
    LocalDateTime earliestEntry,
    LocalDateTime latestEntry,
    List<LogEntry> errors  // All ERROR level entries
) {}
```

**3. Sample log format:**
```
2024-01-15 10:30:45 [INFO] Application started
2024-01-15 10:30:46 [DEBUG] Loading configuration
2024-01-15 10:30:47 [WARN] Config file not found, using defaults
2024-01-15 10:31:00 [ERROR] Database connection failed: timeout
2024-01-15 10:31:05 [INFO] Retrying database connection
```

### Test Cases
```java
LogAggregator aggregator = new LogAggregator();

// Aggregate from multiple files
List<LogEntry> allLogs = aggregator.aggregateFromDirectory(
    Path.of("logs"), "*.log"
);

// Filter errors from last hour
LocalDateTime oneHourAgo = LocalDateTime.now().minusHours(1);
List<LogEntry> recentErrors = aggregator.filterByLevel(
    aggregator.filterByTimeRange(allLogs, oneHourAgo, LocalDateTime.now()),
    "ERROR", "WARN"
);

// Search for specific pattern
List<LogEntry> dbErrors = aggregator.searchByPattern(
    allLogs, ".*[Dd]atabase.*"
);

// Generate summary
LogSummary summary = aggregator.generateSummary(allLogs);
System.out.println("Total entries: " + summary.totalEntries());
System.out.println("Errors: " + summary.countByLevel().get("ERROR"));

// Merge and write sorted logs
aggregator.writeMergedLogs(Path.of("merged.log"), allLogs);
```

---

## Submission Checklist

- [ ] All classes compile without errors
- [ ] CSV processor handles malformed data gracefully
- [ ] Properties manager supports all required type conversions
- [ ] Directory scanner uses NIO APIs correctly
- [ ] Text transformer supports method chaining
- [ ] Log aggregator merges files in correct time order
- [ ] All file operations use try-with-resources
- [ ] UTF-8 encoding is handled correctly
- [ ] Meaningful error messages for file operation failures
- [ ] Unit tests for edge cases (empty files, missing files, invalid data)
