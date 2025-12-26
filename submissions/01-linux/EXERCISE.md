# Exercise: Log File Analyzer Script

| Property | Value |
|----------|-------|
| **Topic** | 01-linux |
| **Type** | Exercise |
| **Difficulty** | Beginner |

## Objective
Create a shell script that analyzes a log file and generates a summary report.

## Sample Log File

A sample log file is provided for testing: **[sample-log.txt](./sample-log.txt)**

You can use this file to test your script:
```bash
./log-analyzer.sh sample-log.txt
```

## Requirements

Create a script called `log-analyzer.sh` that:

1. Takes a log file path as a command-line argument
2. Handles the case when the specified file doesn't exist (show error message)
3. Counts the total number of lines in the log file
4. Counts how many lines contain "ERROR", "WARNING", and "INFO"
5. Extracts and displays unique IP addresses (if any) from the log
6. Outputs a formatted summary report

## Sample Log Format
```
2024-01-15 10:23:45 INFO User login successful from 192.168.1.100
2024-01-15 10:24:12 ERROR Database connection failed
2024-01-15 10:25:33 WARNING High memory usage detected
2024-01-15 10:26:01 INFO Request processed from 10.0.0.50
```

## Expected Output
```
========== LOG ANALYSIS REPORT ==========
File: /path/to/logfile.log
Total Lines: 100
-----------------------------------------
INFO:    45
WARNING: 30
ERROR:   25
-----------------------------------------
Unique IP Addresses Found:
  - 192.168.1.100
  - 10.0.0.50
=========================================
```

## Skills Tested
- Shell script basics (shebang, variables)
- Command-line arguments (`$1`, `$#`)
- Conditional statements (`if`, `else`)
- Text processing (`grep`, `wc`, `sort`, `uniq`)
- Regular expressions basics
- File operations

## Bonus Challenge
- Add a flag `-v` for verbose output that shows each log entry categorized
- Calculate and display the percentage of each log level

---

## Submission

### Required Files
| File | Description |
|------|-------------|
| `log-analyzer.sh` | Your shell script solution |

### Folder Structure
Submit your solution in your repository under:
```
your-repo/
└── 01-linux/
    └── log-analyzer.sh
```

### Technical Checklist
- [ ] File named exactly `log-analyzer.sh`
- [ ] Includes shebang (`#!/bin/bash`)
- [ ] Script is executable (`chmod +x log-analyzer.sh`)
- [ ] Handles missing file argument
- [ ] Tested with provided sample-log.txt

### Evaluation Criteria
| Criteria | Points |
|----------|--------|
| Script runs without errors | 20 |
| Correct argument handling | 15 |
| Accurate line/level counts | 25 |
| IP extraction works | 20 |
| Code quality (comments, structure) | 20 |
| **Total** | **100** |

### How to Submit
```bash
# In your repository
mkdir -p 01-linux
cp log-analyzer.sh 01-linux/
git add 01-linux/log-analyzer.sh
git commit -m "Complete 01-linux exercise: log analyzer"
git push
```
