# Exercise: Linux Fundamentals - Log Analyzer

## Objective
Create a shell script that analyzes server log files and generates a summary report.

## Requirements

### Required Files
| File | Description |
|------|-------------|
| `log-analyzer.sh` | Main shell script |

### Functional Requirements
1. Accept a log file path as a command-line argument
2. Count total number of log entries
3. Count entries by log level (ERROR, WARN, INFO, DEBUG)
4. Display the top 5 most frequent error messages
5. Output results in a formatted summary

### Technical Requirements
- [ ] Include shebang (`#!/bin/bash`)
- [ ] Include `set -e` for error handling
- [ ] Validate input arguments
- [ ] Use variables (properly quoted)
- [ ] Include helpful comments

## Expected Output
```
=== Log Analysis Report ===
File: /path/to/logfile.log
Total Entries: 1000

Log Level Summary:
  ERROR: 50
  WARN:  150
  INFO:  700
  DEBUG: 100

Top 5 Error Messages:
  1. Connection timeout (15 occurrences)
  2. File not found (12 occurrences)
  ...
```

## Sample Usage
```bash
./log-analyzer.sh /var/log/application.log
./log-analyzer.sh sample.log
```

## Hints
- Use `grep -c` to count occurrences
- Use `awk` or `cut` to extract fields
- Use `sort | uniq -c | sort -rn | head -5` for top 5

## Evaluation Criteria
| Criteria | Points |
|----------|--------|
| Script runs without errors | 20 |
| Correct argument handling | 15 |
| Accurate log level counts | 25 |
| Top errors displayed | 20 |
| Code quality & best practices | 20 |
| **Total** | **100** |

## Submission Checklist
- [ ] File named exactly `log-analyzer.sh`
- [ ] Script is executable (`chmod +x log-analyzer.sh`)
- [ ] Placed in `01-linux/` folder
- [ ] Tested with sample log file
- [ ] Committed and pushed to repository
