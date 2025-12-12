# Shell Scripting Fundamentals

## Introduction to Bash Shell

### What is Shell Scripting?

Shell scripting is writing a series of commands in a file that the shell can execute. It allows you to automate repetitive tasks, manage system operations, and create powerful workflows.

### Why Learn Shell Scripting?

- **Automation**: Automate repetitive tasks and save time
- **DevOps**: Essential for CI/CD pipelines, deployment scripts
- **System Administration**: Manage servers, users, and backups
- **Development Workflow**: Build scripts, testing automation
- **Universal**: Available on all Unix-like systems

### The Bash Shell

**Bash** (Bourne Again Shell) is the default shell on most Linux distributions. It's an enhanced version of the original Bourne shell (sh) with additional features.

```bash
# Check your current shell
$ echo $SHELL
/bin/bash

# Check Bash version
$ bash --version
GNU bash, version 5.1.16
```

---

## Creating and Executing Shell Scripts

### Creating Your First Script

1. Create a file with `.sh` extension
2. Add the shebang line
3. Write your commands
4. Make it executable
5. Run the script

```bash
#!/bin/bash
# My first shell script
# This is a comment

echo "Hello, World!"
echo "Today is $(date)"
echo "You are logged in as: $USER"
```

### The Shebang Line

The shebang (`#!`) tells the system which interpreter to use:

```bash
#!/bin/bash          # Use Bash
#!/bin/sh            # Use POSIX shell
#!/usr/bin/env bash  # Find bash in PATH (more portable)
```

### Making Scripts Executable

```bash
# Method 1: Add execute permission
$ chmod +x myscript.sh
$ ./myscript.sh

# Method 2: Run with bash directly
$ bash myscript.sh

# Method 3: Source the script (runs in current shell)
$ source myscript.sh
$ . myscript.sh    # Shorthand for source
```

### Script Best Practices

```bash
#!/bin/bash
#
# Script Name: deploy.sh
# Description: Deploys application to server
# Author: Your Name
# Date: 2024-01-15
# Version: 1.0
#

set -e          # Exit on error
set -u          # Exit on undefined variable
set -o pipefail # Catch errors in pipes

# Your code here
```

---

## Variables and Data Types

### Declaring Variables

```bash
# Simple assignment (no spaces around =)
name="John"
age=25
path="/home/user"

# Using variables
echo "Name: $name"
echo "Age: $age"
echo "Path: ${path}/documents"  # Braces for clarity

# Read-only variables
readonly PI=3.14159
```

### Variable Types

```bash
# String
greeting="Hello World"

# Number (treated as string, but can do arithmetic)
count=10

# Array
fruits=("apple" "banana" "orange")
echo ${fruits[0]}      # apple
echo ${fruits[@]}      # All elements
echo ${#fruits[@]}     # Array length

# Associative array (Bash 4+)
declare -A user
user[name]="John"
user[age]=25
echo ${user[name]}
```

### Special Variables

| Variable | Description |
|----------|-------------|
| `$0` | Script name |
| `$1, $2...` | Positional parameters |
| `$#` | Number of arguments |
| `$@` | All arguments as separate words |
| `$*` | All arguments as single word |
| `$?` | Exit status of last command |
| `$$` | Current process ID |
| `$!` | Last background process ID |

```bash
#!/bin/bash
echo "Script name: $0"
echo "First argument: $1"
echo "Second argument: $2"
echo "Number of arguments: $#"
echo "All arguments: $@"
```

### Environment Variables

```bash
# View all environment variables
$ env
$ printenv

# Common environment variables
echo $HOME      # Home directory
echo $USER      # Current username
echo $PATH      # Executable search path
echo $PWD       # Current working directory
echo $SHELL     # Current shell

# Export variable to child processes
export MY_VAR="value"

# Set variable for single command
MY_VAR="test" ./myscript.sh
```

### Command Substitution

```bash
# Using $() - preferred
current_date=$(date)
file_count=$(ls | wc -l)

# Using backticks - older style
current_date=`date`

# Nested substitution
files_in_home=$(ls $(echo $HOME) | wc -l)
```

---

## Basic Control Structures

### Conditional Statements

#### if-else Statements

```bash
#!/bin/bash

age=18

if [ $age -ge 18 ]; then
    echo "You are an adult"
elif [ $age -ge 13 ]; then
    echo "You are a teenager"
else
    echo "You are a child"
fi
```

#### Test Operators

**Numeric Comparisons:**

| Operator | Description |
|----------|-------------|
| `-eq` | Equal |
| `-ne` | Not equal |
| `-gt` | Greater than |
| `-ge` | Greater than or equal |
| `-lt` | Less than |
| `-le` | Less than or equal |

```bash
if [ $a -eq $b ]; then
    echo "a equals b"
fi
```

**String Comparisons:**

| Operator | Description |
|----------|-------------|
| `=` or `==` | Equal |
| `!=` | Not equal |
| `-z` | String is empty |
| `-n` | String is not empty |

```bash
name="John"
if [ "$name" = "John" ]; then
    echo "Hello John!"
fi

if [ -z "$variable" ]; then
    echo "Variable is empty"
fi
```

**File Tests:**

| Operator | Description |
|----------|-------------|
| `-f` | Is a regular file |
| `-d` | Is a directory |
| `-e` | File exists |
| `-r` | Is readable |
| `-w` | Is writable |
| `-x` | Is executable |
| `-s` | File is not empty |

```bash
if [ -f "/etc/passwd" ]; then
    echo "File exists"
fi

if [ -d "$HOME/projects" ]; then
    echo "Directory exists"
fi
```

#### Using [[ ]] - Extended Test

```bash
# Supports pattern matching and regex
if [[ $name == J* ]]; then
    echo "Name starts with J"
fi

# Logical operators
if [[ $age -ge 18 && $age -le 65 ]]; then
    echo "Working age"
fi
```

#### Case Statements

```bash
#!/bin/bash

fruit="apple"

case $fruit in
    "apple")
        echo "It's an apple"
        ;;
    "banana" | "plantain")
        echo "It's yellow"
        ;;
    *)
        echo "Unknown fruit"
        ;;
esac
```

### Loops

#### for Loop

```bash
# Loop through list
for item in apple banana orange; do
    echo "Fruit: $item"
done

# Loop through range
for i in {1..5}; do
    echo "Number: $i"
done

# C-style for loop
for ((i=0; i<5; i++)); do
    echo "Index: $i"
done

# Loop through files
for file in *.txt; do
    echo "Processing: $file"
done

# Loop through array
fruits=("apple" "banana" "orange")
for fruit in "${fruits[@]}"; do
    echo "$fruit"
done
```

#### while Loop

```bash
#!/bin/bash

count=1
while [ $count -le 5 ]; do
    echo "Count: $count"
    ((count++))
done

# Read file line by line
while IFS= read -r line; do
    echo "Line: $line"
done < input.txt
```

#### until Loop

```bash
#!/bin/bash

count=1
until [ $count -gt 5 ]; do
    echo "Count: $count"
    ((count++))
done
```

#### Loop Control

```bash
# break - exit loop
for i in {1..10}; do
    if [ $i -eq 5 ]; then
        break
    fi
    echo $i
done

# continue - skip iteration
for i in {1..5}; do
    if [ $i -eq 3 ]; then
        continue
    fi
    echo $i
done
```

---

## Input/Output Operations

### Reading User Input

```bash
#!/bin/bash

# Basic input
echo "Enter your name:"
read name
echo "Hello, $name!"

# Input with prompt
read -p "Enter your age: " age

# Silent input (for passwords)
read -sp "Enter password: " password
echo  # New line after silent input

# Read with timeout
read -t 5 -p "Quick! Enter something: " response

# Read into array
read -a colors -p "Enter colors (space-separated): "
echo "First color: ${colors[0]}"
```

### Output Commands

```bash
# echo - print text
echo "Hello World"
echo -n "No newline"    # No trailing newline
echo -e "Tab:\tNewline:\n"  # Enable escape sequences

# printf - formatted output
printf "Name: %s, Age: %d\n" "John" 25
printf "%-10s %5d\n" "Item" 100    # Aligned columns

# Colored output
echo -e "\e[31mRed text\e[0m"
echo -e "\e[32mGreen text\e[0m"
echo -e "\e[1mBold text\e[0m"
```

### Redirection

```bash
# Output redirection
echo "Hello" > file.txt      # Overwrite
echo "World" >> file.txt     # Append

# Input redirection
while read line; do
    echo "$line"
done < input.txt

# Error redirection
command 2> errors.log        # Redirect stderr
command 2>&1                 # Redirect stderr to stdout
command &> all_output.log    # Redirect both stdout and stderr

# Here document
cat << EOF > config.txt
server=localhost
port=8080
debug=true
EOF

# Here string
grep "pattern" <<< "search in this string"
```

### Pipes

```bash
# Chain commands
ls -la | grep ".txt"
cat file.txt | sort | uniq
ps aux | grep "python" | awk '{print $2}'

# Pipe to while loop
ls *.txt | while read file; do
    echo "Found: $file"
done
```

---

## Functions

### Defining Functions

```bash
#!/bin/bash

# Method 1
function greet {
    echo "Hello, $1!"
}

# Method 2 (POSIX compatible)
greet() {
    echo "Hello, $1!"
}

# Calling function
greet "World"
greet "Developer"
```

### Function Parameters and Return Values

```bash
#!/bin/bash

add_numbers() {
    local num1=$1
    local num2=$2
    local sum=$((num1 + num2))
    echo $sum  # Return via stdout
}

# Capture return value
result=$(add_numbers 5 3)
echo "Sum: $result"

# Using return for exit status
is_even() {
    if [ $(($1 % 2)) -eq 0 ]; then
        return 0  # True (success)
    else
        return 1  # False (failure)
    fi
}

if is_even 4; then
    echo "4 is even"
fi
```

### Local Variables

```bash
#!/bin/bash

global_var="I'm global"

my_function() {
    local local_var="I'm local"
    global_var="Modified global"
    echo "Inside: $local_var"
}

my_function
echo "Outside: $global_var"
# echo $local_var  # Would be empty
```

---

## Practical Examples

### Example 1: Backup Script

```bash
#!/bin/bash

# Backup script for project files
BACKUP_DIR="/backup"
SOURCE_DIR="/home/developer/projects"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="backup_$DATE.tar.gz"

# Create backup directory if not exists
mkdir -p "$BACKUP_DIR"

# Create compressed backup
tar -czf "$BACKUP_DIR/$BACKUP_FILE" "$SOURCE_DIR"

# Check if backup was successful
if [ $? -eq 0 ]; then
    echo "Backup created: $BACKUP_FILE"
else
    echo "Backup failed!"
    exit 1
fi

# Remove backups older than 7 days
find "$BACKUP_DIR" -name "backup_*.tar.gz" -mtime +7 -delete

echo "Backup completed successfully"
```

### Example 2: Log File Analyzer

```bash
#!/bin/bash

# Analyze log file for errors
LOG_FILE=${1:-"/var/log/syslog"}

if [ ! -f "$LOG_FILE" ]; then
    echo "Error: Log file not found: $LOG_FILE"
    exit 1
fi

echo "=== Log Analysis Report ==="
echo "File: $LOG_FILE"
echo ""

echo "Error count: $(grep -c -i 'error' "$LOG_FILE")"
echo "Warning count: $(grep -c -i 'warning' "$LOG_FILE")"
echo ""

echo "=== Recent Errors ==="
grep -i 'error' "$LOG_FILE" | tail -5
```

### Example 3: System Health Check

```bash
#!/bin/bash

echo "=== System Health Check ==="
echo "Date: $(date)"
echo ""

# Disk usage
echo "=== Disk Usage ==="
df -h | grep -E '^/dev/'
echo ""

# Memory usage
echo "=== Memory Usage ==="
free -h
echo ""

# CPU load
echo "=== CPU Load ==="
uptime
echo ""

# Top processes
echo "=== Top 5 Processes (by CPU) ==="
ps aux --sort=-%cpu | head -6
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Scripts | Start with shebang, make executable with chmod +x |
| Variables | No spaces around `=`, use `$var` or `${var}` |
| Conditionals | Use `[ ]` or `[[ ]]` for tests |
| Loops | for, while, until - use `break` and `continue` |
| I/O | `read` for input, `echo`/`printf` for output, use redirections |
| Functions | Define with `name() {}`, use `local` for local variables |

---

## Next Topic

Continue to [Fullstack the Big Picture](./04-fullstack-big-picture.md) to understand how Linux fits into the complete development ecosystem.
