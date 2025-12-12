# Basic Git Commands

## Overview

This topic covers the essential Git commands you'll use daily. Mastering these commands is fundamental to effective version control and collaboration.

---

## git init - Initialize Repository

Creates a new Git repository in the current directory or initializes an existing directory.

### Basic Usage

```bash
# Initialize current directory
git init

# Output:
# Initialized empty Git repository in /path/to/directory/.git/

# Initialize with specific branch name
git init -b main

# Initialize new directory
git init my-project
# Creates directory and initializes Git
```

### What Happens

```
Before:
my-project/
├── src/
└── README.md

After git init:
my-project/
├── .git/           # Git repository created
│   ├── HEAD
│   ├── config
│   ├── objects/
│   └── refs/
├── src/
└── README.md
```

### Reinitializing

```bash
# Safe to run in existing repository
# Doesn't overwrite existing data
git init
# Reinitialized existing Git repository in /path/.git/
```

---

## git add - Stage Changes

Adds file changes to the staging area (index), preparing them for commit.

### Basic Usage

```bash
# Stage specific file
git add filename.txt

# Stage multiple files
git add file1.txt file2.txt file3.txt

# Stage all changes in current directory
git add .

# Stage all changes in repository
git add -A
git add --all

# Stage all tracked files (not new files)
git add -u
git add --update
```

### Interactive Staging

```bash
# Interactive mode - choose what to stage
git add -i

# Patch mode - stage parts of files
git add -p
git add --patch

# Example patch mode session:
# Stage this hunk [y,n,q,a,d,/,s,e,?]?
# y - yes, stage this hunk
# n - no, don't stage this hunk
# q - quit
# a - stage this and all remaining hunks
# d - don't stage this or remaining hunks
# s - split hunk into smaller hunks
# e - manually edit the hunk
# ? - print help
```

### Stage by Pattern

```bash
# Stage all .js files
git add '*.js'

# Stage files in specific directory
git add src/

# Stage all .txt files in subdirectories
git add '**/*.txt'
```

### Examples

```bash
# New project workflow
echo "# My Project" > README.md
git add README.md

# Multiple files
touch app.js style.css index.html
git add app.js style.css index.html

# All changes
git add .

# Only modifications (not new files)
git add -u
```

---

## git status - Check Repository Status

Shows the state of the working directory and staging area.

### Basic Usage

```bash
# Full status
git status

# Short format
git status -s
git status --short

# Branch information
git status -b
git status --branch
```

### Understanding Output

#### Full Status

```bash
$ git status

On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   README.md
        new file:   app.js

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   index.html

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        style.css
```

#### Short Status

```bash
$ git status -s

M  README.md      # Staged modification
A  app.js         # Staged new file
 M index.html     # Unstaged modification
?? style.css      # Untracked file
MM config.js      # Staged and unstaged modifications
```

### Status Flags

| Flag | Meaning |
|------|---------|
| `??` | Untracked file |
| `A ` | Added (new file staged) |
| `M ` | Modified and staged |
| ` M` | Modified but not staged |
| `MM` | Staged, then modified again |
| `D ` | Deleted and staged |
| ` D` | Deleted but not staged |
| `R ` | Renamed |
| `C ` | Copied |
| `U ` | Updated but unmerged |

---

## git commit - Save Changes

Records a snapshot of the staged changes to the repository.

### Basic Usage

```bash
# Commit with inline message
git commit -m "Add user authentication"

# Commit with detailed message in editor
git commit

# Stage all tracked files and commit
git commit -a -m "Update configuration"
git commit -am "Update configuration"  # Short form

# Amend last commit
git commit --amend

# Amend without changing message
git commit --amend --no-edit
```

### Commit Message Format

#### Single-line Commit

```bash
git commit -m "Fix login validation bug"
```

#### Multi-line Commit

```bash
git commit
# Opens editor:

Add user authentication feature

- Implement JWT-based authentication
- Add login and registration endpoints
- Include password hashing with bcrypt
- Add authentication middleware

Fixes #123
```

#### Good Commit Messages

```bash
# ✓ Good - Clear and specific
git commit -m "Add password reset functionality"
git commit -m "Fix null pointer exception in UserService"
git commit -m "Update API documentation for /users endpoint"

# ✗ Bad - Too vague
git commit -m "fix stuff"
git commit -m "updates"
git commit -m "asdfasdf"
```

### Conventional Commits

```bash
# Format: <type>(<scope>): <subject>

# Feature
git commit -m "feat(auth): add OAuth2 login support"

# Bug fix
git commit -m "fix(api): resolve timeout issue in user endpoint"

# Documentation
git commit -m "docs(readme): update installation instructions"

# Refactoring
git commit -m "refactor(users): simplify validation logic"

# Tests
git commit -m "test(auth): add login integration tests"

# Chore
git commit -m "chore(deps): update dependencies to latest versions"
```

Common types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `perf`

### Advanced Commit Options

```bash
# Commit only specific files (even if others are staged)
git commit file1.txt file2.txt -m "Update specific files"

# Allow empty commit (no changes)
git commit --allow-empty -m "Trigger CI build"

# Set commit author
git commit --author="John Doe <john@example.com>" -m "Message"

# Set commit date
git commit --date="2024-01-15T10:00:00" -m "Message"

# Sign commit with GPG
git commit -S -m "Signed commit"

# Verbose mode (show diff in editor)
git commit -v
```

---

## git log - View Commit History

Displays the commit history of the repository.

### Basic Usage

```bash
# Show full commit history
git log

# One line per commit
git log --oneline

# Last N commits
git log -n 5
git log -5

# Show commits by author
git log --author="John Doe"

# Show commits in date range
git log --since="2 weeks ago"
git log --after="2024-01-01" --before="2024-01-31"

# Show commits affecting specific file
git log -- path/to/file.txt
```

### Formatting Log Output

```bash
# One line with graph
git log --oneline --graph --all

# Pretty format
git log --pretty=format:"%h - %an, %ar : %s"
# a1b2c3d - John Doe, 2 days ago : Add feature

# Show stats
git log --stat

# Show patches (actual changes)
git log -p
git log --patch

# Show abbreviated stats
git log --shortstat
```

### Advanced Log Options

```bash
# Search commit messages
git log --grep="fix"
git log --grep="bug" --grep="error" --all-match

# Search code changes (pickaxe)
git log -S "function_name"  # Find when function_name was added/removed

# Search by code pattern
git log -G "regex_pattern"

# Show merge commits only
git log --merges

# Show non-merge commits only
git log --no-merges

# Show commits from all branches
git log --all

# Visualize branches
git log --oneline --graph --all --decorate
```

### Custom Format

```bash
# Custom format with placeholders
git log --pretty=format:"%C(yellow)%h%Creset %C(blue)%ad%Creset %C(green)%an%Creset %s" --date=short

# Placeholders:
# %H  - Commit hash (full)
# %h  - Commit hash (abbreviated)
# %an - Author name
# %ae - Author email
# %ad - Author date
# %ar - Author date (relative)
# %cn - Committer name
# %ce - Committer email
# %cd - Commit date
# %cr - Commit date (relative)
# %s  - Subject (commit message)
# %b  - Body
```

### Useful Log Aliases

```bash
# Set up useful aliases
git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"

git config --global alias.ls "log --pretty=format:'%C(yellow)%h %C(blue)%ad%C(red)%d %C(reset)%s%C(green) [%cn]' --decorate --date=short"

# Use:
git lg
git ls
```

---

## git diff - View Differences

Shows changes between commits, working directory, and staging area.

### Basic Usage

```bash
# Show unstaged changes
git diff

# Show staged changes
git diff --staged
git diff --cached  # Same as --staged

# Show all changes (staged and unstaged)
git diff HEAD
```

### Comparing Commits

```bash
# Compare two commits
git diff commit1 commit2

# Compare with specific commit
git diff a1b2c3d

# Compare HEAD with previous commit
git diff HEAD HEAD~1

# Compare branches
git diff main feature-branch

# Compare current branch with main
git diff main
```

### File-Specific Diffs

```bash
# Show changes in specific file
git diff path/to/file.txt

# Show changes in specific file between commits
git diff commit1 commit2 -- path/to/file.txt

# Show changes in directory
git diff src/
```

### Advanced Diff Options

```bash
# Show only names of changed files
git diff --name-only

# Show names and status (modified, added, deleted)
git diff --name-status

# Show statistics
git diff --stat

# Show word-level diff
git diff --word-diff

# Show color-coded word diff
git diff --word-diff=color

# Ignore whitespace changes
git diff -w
git diff --ignore-all-space

# Ignore blank line changes
git diff --ignore-blank-lines
```

### Understanding Diff Output

```bash
$ git diff

diff --git a/file.txt b/file.txt
index 83db48f..bf269f4 100644
--- a/file.txt              # Original version
+++ b/file.txt              # New version
@@ -1,3 +1,4 @@             # Line numbers (old, new)
 Line 1                     # Unchanged
-Line 2                     # Removed (red)
+Line 2 modified            # Added (green)
+New Line 3                 # Added (green)
 Line 4                     # Unchanged
```

---

## git restore - Restore Files

Modern command to restore working directory files or unstage changes.

### Restore Working Directory

```bash
# Discard changes in file
git restore file.txt

# Discard all changes
git restore .

# Restore file from specific commit
git restore --source=HEAD~2 file.txt

# Restore file from specific branch
git restore --source=main file.txt
```

### Unstage Files

```bash
# Unstage file (keep changes in working directory)
git restore --staged file.txt

# Unstage all files
git restore --staged .

# Unstage and discard changes
git restore --staged --worktree file.txt
```

### Old Syntax (Still Works)

```bash
# Discard changes (old way)
git checkout -- file.txt

# Unstage (old way)
git reset HEAD file.txt
```

---

## git reset - Reset State

Moves HEAD and optionally updates staging area and working directory.

### Reset Modes

```bash
# Soft reset - move HEAD, keep staging and working directory
git reset --soft HEAD~1
# Use case: Undo commit but keep changes staged

# Mixed reset - move HEAD, update staging, keep working directory (default)
git reset HEAD~1
git reset --mixed HEAD~1
# Use case: Undo commit and unstage, but keep changes

# Hard reset - move HEAD, update staging and working directory
git reset --hard HEAD~1
# Use case: Completely undo commit and discard changes
# WARNING: Loses all changes!
```

### Common Reset Operations

```bash
# Unstage file
git reset HEAD file.txt

# Undo last commit, keep changes
git reset HEAD~1

# Undo last 3 commits, keep changes
git reset HEAD~3

# Reset to specific commit
git reset a1b2c3d

# Reset to remote branch state
git reset --hard origin/main

# Reset single file to previous commit
git reset HEAD~1 -- file.txt
```

### Visual Representation

```
Before: A -> B -> C (HEAD)

git reset --soft HEAD~1
After:  A -> B (HEAD)
        Changes from C are staged

git reset HEAD~1
After:  A -> B (HEAD)
        Changes from C are in working directory (unstaged)

git reset --hard HEAD~1
After:  A -> B (HEAD)
        Changes from C are lost
```

---

## git rm - Remove Files

Removes files from working directory and staging area.

### Basic Usage

```bash
# Remove file from Git and filesystem
git rm file.txt

# Remove multiple files
git rm file1.txt file2.txt

# Remove directory recursively
git rm -r directory/

# Remove from Git but keep in filesystem
git rm --cached file.txt

# Force removal (even if modified)
git rm -f file.txt
```

### Common Scenarios

```bash
# Stop tracking file but keep locally
git rm --cached config.env
# Then add to .gitignore

# Remove all .log files
git rm '*.log'

# Remove files matching pattern
git rm 'temp-*'

# Dry run (see what would be removed)
git rm -n '*.log'
```

---

## git mv - Move/Rename Files

Moves or renames files and stages the change.

### Basic Usage

```bash
# Rename file
git mv oldname.txt newname.txt

# Move file to directory
git mv file.txt directory/

# Move and rename
git mv old.txt directory/new.txt

# Rename directory
git mv old-dir/ new-dir/
```

### Equivalent Manual Process

```bash
# These are equivalent:

# Using git mv:
git mv file.txt newfile.txt

# Manual method:
mv file.txt newfile.txt
git rm file.txt
git add newfile.txt
```

---

## Practical Workflows

### Starting a New Project

```bash
# Create project directory
mkdir my-project
cd my-project

# Initialize Git
git init

# Create initial files
echo "# My Project" > README.md
echo "console.log('Hello');" > app.js

# Check status
git status

# Stage files
git add .

# Commit
git commit -m "Initial commit"

# View history
git log
```

### Daily Development Workflow

```bash
# Check current status
git status

# View current changes
git diff

# Stage specific changes
git add modified-file.txt

# Review staged changes
git diff --staged

# Commit
git commit -m "Descriptive message"

# View recent history
git log --oneline -5
```

### Fixing Mistakes

```bash
# Oops, forgot to add a file to last commit
git add forgotten-file.txt
git commit --amend --no-edit

# Oops, staged wrong file
git restore --staged wrong-file.txt

# Oops, made changes I want to discard
git restore file-to-discard.txt

# Oops, last commit was wrong
git reset --soft HEAD~1
# Fix issues, then commit again
```

---

## Summary

| Command | Purpose | Common Usage |
|---------|---------|--------------|
| `git init` | Initialize repository | `git init` |
| `git add` | Stage changes | `git add .` |
| `git status` | Check status | `git status` |
| `git commit` | Save snapshot | `git commit -m "message"` |
| `git log` | View history | `git log --oneline` |
| `git diff` | View changes | `git diff` |
| `git restore` | Restore/unstage | `git restore file.txt` |
| `git reset` | Reset state | `git reset HEAD~1` |
| `git rm` | Remove files | `git rm file.txt` |
| `git mv` | Move/rename | `git mv old.txt new.txt` |

### Essential Command Patterns

```bash
# Start new project
git init

# Daily workflow
git status
git add .
git commit -m "message"
git log

# Review changes
git diff              # Unstaged
git diff --staged     # Staged
git log -p            # History with changes

# Undo operations
git restore file.txt           # Discard changes
git restore --staged file.txt  # Unstage
git reset HEAD~1               # Undo commit
git commit --amend             # Fix last commit
```

## Next Topic

Continue to [Git Branching and Merging](./06-git-branching-merging.md) to learn about branches, one of Git's most powerful features.
