# Basic Linux Commands

## Overview

The command-line interface (CLI) is the primary way to interact with Linux systems. Mastering basic commands is essential for developers working with servers, containers, and development environments.

---

## File System Navigation

### Understanding the File System

Linux uses a hierarchical file system starting from the root directory `/`. Everything in Linux is a file, including directories and devices.

### Essential Navigation Commands

#### `pwd` - Print Working Directory

Displays your current location in the file system.

```bash
$ pwd
/home/developer/projects
```

#### `ls` - List Directory Contents

Lists files and directories in the current or specified directory.

```bash
# Basic listing
$ ls
file1.txt  file2.txt  projects  documents

# Long format with details
$ ls -l
-rw-r--r-- 1 user group 1024 Dec 10 10:00 file1.txt
drwxr-xr-x 2 user group 4096 Dec 10 09:00 projects

# Show hidden files (starting with .)
$ ls -a
.  ..  .bashrc  .config  file1.txt

# Combined flags - all files with details, human-readable sizes
$ ls -lah
total 24K
drwxr-xr-x 4 user group 4.0K Dec 10 10:00 .
drwxr-xr-x 3 user group 4.0K Dec  9 15:00 ..
-rw-r--r-- 1 user group  220 Dec  9 15:00 .bashrc
-rw-r--r-- 1 user group 1.0K Dec 10 10:00 file1.txt
```

**Common `ls` Options:**

| Option | Description |
|--------|-------------|
| `-l` | Long format (permissions, owner, size, date) |
| `-a` | Show hidden files |
| `-h` | Human-readable file sizes |
| `-R` | Recursive listing |
| `-t` | Sort by modification time |
| `-S` | Sort by file size |

#### `cd` - Change Directory

Navigate between directories.

```bash
# Go to a specific directory
$ cd /var/log

# Go to home directory
$ cd ~
$ cd        # Also goes to home

# Go to parent directory
$ cd ..

# Go to previous directory
$ cd -

# Relative path navigation
$ cd projects/webapp

# Absolute path navigation
$ cd /home/developer/projects
```

**Path Types:**
- **Absolute path**: Starts from root `/` (e.g., `/home/user/docs`)
- **Relative path**: Starts from current directory (e.g., `./docs` or `docs`)

---

## File Operations

### Creating Files

#### `touch` - Create Empty Files

```bash
# Create a single file
$ touch newfile.txt

# Create multiple files
$ touch file1.txt file2.txt file3.txt

# Update timestamp of existing file
$ touch existingfile.txt
```

#### Using Redirection to Create Files

```bash
# Create file with content
$ echo "Hello World" > hello.txt

# Append to file
$ echo "New line" >> hello.txt
```

### Copying Files

#### `cp` - Copy Files and Directories

```bash
# Copy a file
$ cp source.txt destination.txt

# Copy to a directory
$ cp file.txt /path/to/directory/

# Copy multiple files to a directory
$ cp file1.txt file2.txt /path/to/directory/

# Copy directory recursively
$ cp -r source_dir/ destination_dir/

# Copy with preservation of attributes
$ cp -p source.txt destination.txt

# Interactive mode (prompt before overwrite)
$ cp -i source.txt destination.txt
```

### Moving and Renaming Files

#### `mv` - Move or Rename Files

```bash
# Rename a file
$ mv oldname.txt newname.txt

# Move file to directory
$ mv file.txt /path/to/directory/

# Move and rename
$ mv file.txt /path/to/directory/newname.txt

# Move multiple files
$ mv file1.txt file2.txt /path/to/directory/

# Move directory
$ mv source_dir/ /path/to/destination/
```

### Deleting Files

#### `rm` - Remove Files

```bash
# Remove a file
$ rm file.txt

# Remove with confirmation
$ rm -i file.txt

# Force removal (no confirmation)
$ rm -f file.txt

# Remove directory and contents recursively
$ rm -r directory/

# Force remove directory (use with caution!)
$ rm -rf directory/
```

**Warning**: `rm` permanently deletes files. There is no recycle bin!

---

## Directory Operations

### Creating Directories

#### `mkdir` - Make Directory

```bash
# Create a directory
$ mkdir new_directory

# Create nested directories
$ mkdir -p parent/child/grandchild

# Create multiple directories
$ mkdir dir1 dir2 dir3
```

### Removing Directories

#### `rmdir` - Remove Empty Directory

```bash
# Remove empty directory
$ rmdir empty_directory

# Remove nested empty directories
$ rmdir -p parent/child/grandchild
```

For non-empty directories, use `rm -r`.

---

## Viewing File Contents

### `cat` - Concatenate and Display

```bash
# Display entire file
$ cat file.txt

# Display with line numbers
$ cat -n file.txt

# Display multiple files
$ cat file1.txt file2.txt

# Concatenate files into new file
$ cat file1.txt file2.txt > combined.txt
```

### `less` and `more` - Paginated Viewing

For viewing large files page by page.

```bash
# View file with pagination
$ less largefile.txt

# Navigation in less:
# - Space/PageDown: Next page
# - b/PageUp: Previous page
# - /pattern: Search forward
# - ?pattern: Search backward
# - n: Next search result
# - q: Quit

# View with more (simpler than less)
$ more largefile.txt
```

### `head` and `tail` - View Start/End of Files

```bash
# View first 10 lines (default)
$ head file.txt

# View first N lines
$ head -n 20 file.txt

# View last 10 lines (default)
$ tail file.txt

# View last N lines
$ tail -n 20 file.txt

# Follow file in real-time (great for logs!)
$ tail -f /var/log/syslog
```

---

## File Permissions and Ownership

### Understanding Permissions

Linux file permissions consist of three sets of permissions for three types of users:

```
-rwxr-xr-- 1 owner group size date filename
│├─┤├─┤├─┤
│ │  │  └── Others permissions (r--)
│ │  └───── Group permissions (r-x)
│ └──────── Owner permissions (rwx)
└────────── File type (- = file, d = directory)
```

**Permission Types:**
- `r` (read) = 4
- `w` (write) = 2
- `x` (execute) = 1

### `chmod` - Change Permissions

```bash
# Symbolic mode
$ chmod u+x file.sh      # Add execute for owner
$ chmod g-w file.txt     # Remove write for group
$ chmod o=r file.txt     # Set others to read only
$ chmod a+r file.txt     # Add read for all

# Numeric mode
$ chmod 755 script.sh    # rwxr-xr-x
$ chmod 644 file.txt     # rw-r--r--
$ chmod 700 private.txt  # rwx------

# Recursive
$ chmod -R 755 directory/
```

**Common Permission Values:**

| Value | Permissions | Use Case |
|-------|-------------|----------|
| 755 | rwxr-xr-x | Executable scripts, directories |
| 644 | rw-r--r-- | Regular files |
| 700 | rwx------ | Private scripts |
| 600 | rw------- | Private files |

### `chown` - Change Ownership

```bash
# Change owner
$ sudo chown newowner file.txt

# Change owner and group
$ sudo chown newowner:newgroup file.txt

# Change only group
$ sudo chown :newgroup file.txt

# Recursive
$ sudo chown -R user:group directory/
```

---

## Text Searching and Processing

### `grep` - Search Text Patterns

```bash
# Search for pattern in file
$ grep "error" logfile.txt

# Case-insensitive search
$ grep -i "error" logfile.txt

# Show line numbers
$ grep -n "error" logfile.txt

# Recursive search in directory
$ grep -r "TODO" ./src/

# Invert match (show lines NOT matching)
$ grep -v "debug" logfile.txt

# Count matches
$ grep -c "error" logfile.txt

# Show context lines
$ grep -B 2 -A 2 "error" logfile.txt  # 2 lines before and after
```

### `find` - Search for Files

```bash
# Find by name
$ find /home -name "*.txt"

# Find by name (case-insensitive)
$ find /home -iname "*.TXT"

# Find by type
$ find /var -type f    # Files only
$ find /var -type d    # Directories only

# Find by size
$ find /home -size +100M    # Larger than 100MB
$ find /home -size -1k      # Smaller than 1KB

# Find by modification time
$ find /home -mtime -7      # Modified in last 7 days

# Execute command on found files
$ find . -name "*.log" -exec rm {} \;
```

---

## Summary of Essential Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `pwd` | Print current directory | `pwd` |
| `ls` | List directory contents | `ls -la` |
| `cd` | Change directory | `cd /home/user` |
| `touch` | Create empty file | `touch file.txt` |
| `cp` | Copy files/directories | `cp -r src/ dest/` |
| `mv` | Move/rename | `mv old.txt new.txt` |
| `rm` | Remove files | `rm -rf directory/` |
| `mkdir` | Create directory | `mkdir -p a/b/c` |
| `cat` | Display file contents | `cat file.txt` |
| `less` | Paginated view | `less largefile.txt` |
| `head` | View first lines | `head -n 20 file.txt` |
| `tail` | View last lines | `tail -f logfile.txt` |
| `chmod` | Change permissions | `chmod 755 script.sh` |
| `chown` | Change ownership | `chown user:group file` |
| `grep` | Search text | `grep -r "pattern" ./` |
| `find` | Search files | `find . -name "*.js"` |

---

## Next Topic

Continue to [Shell Scripting Fundamentals](./03-shell-scripting.md) to learn how to automate tasks with bash scripts.
