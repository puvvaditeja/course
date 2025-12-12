# Git Fundamentals

## Overview

Understanding Git fundamentals is essential for effective version control. This topic covers the core concepts that make Git work, including repository structure, the three states of files, and the basic Git workflow.

---

## Repository Initialization

A Git repository (or "repo") is a directory that Git tracks. It contains all your project files and the complete version history.

### Creating a New Repository

#### Method 1: Initialize in Existing Directory

```bash
# Navigate to your project directory
cd my-project

# Initialize Git repository
git init

# Output:
# Initialized empty Git repository in /path/to/my-project/.git/
```

This creates a hidden `.git` directory containing all Git metadata:

```
my-project/
├── .git/              # Git repository data
│   ├── HEAD           # Points to current branch
│   ├── config         # Repository configuration
│   ├── description    # Repository description
│   ├── hooks/         # Scripts for Git events
│   ├── objects/       # All committed data
│   └── refs/          # Branch and tag pointers
├── src/               # Your project files
└── README.md
```

#### Method 2: Clone Existing Repository

```bash
# Clone from remote repository
git clone https://github.com/username/repository.git

# Clone with custom directory name
git clone https://github.com/username/repository.git my-project

# Clone specific branch
git clone -b develop https://github.com/username/repository.git
```

### Understanding .git Directory

The `.git` directory is where Git stores everything:

```
.git/
├── HEAD                 # Current branch reference
├── config               # Repository-specific config
├── description          # Repository description
├── index                # Staging area (index)
├── hooks/               # Client/server hooks
│   ├── pre-commit       # Runs before commit
│   └── post-commit      # Runs after commit
├── objects/             # Git object database
│   ├── 0a/              # Object files (by hash)
│   ├── 1b/
│   └── pack/            # Packed objects
├── refs/                # References
│   ├── heads/           # Branch references
│   │   └── main
│   ├── tags/            # Tag references
│   └── remotes/         # Remote references
│       └── origin/
└── logs/                # Reference logs
```

Never modify `.git` directly unless you know what you're doing!

---

## The Staging Area Concept

Git has a unique three-stage workflow that gives you fine-grained control over what goes into each commit.

### The Three Areas

```
┌─────────────────────────────────────────────────────────────┐
│                    Git Workflow Areas                       │
└─────────────────────────────────────────────────────────────┘

┌──────────────────────┐
│  Working Directory   │  ← Where you edit files
│                      │
│  file1.txt (modified)│
│  file2.txt (new)     │
└──────────┬───────────┘
           │
           │  git add file1.txt
           │
           ▼
┌──────────────────────┐
│   Staging Area       │  ← Preparing next commit
│   (Index)            │
│                      │
│  file1.txt (staged)  │
└──────────┬───────────┘
           │
           │  git commit -m "Message"
           │
           ▼
┌──────────────────────┐
│  Repository          │  ← Permanent history
│  (.git directory)    │
│                      │
│  Commit abc123       │
│  ├─ file1.txt        │
│  └─ ...              │
└──────────────────────┘
```

### Why Use a Staging Area?

#### 1. Selective Commits

Commit only specific changes, even from the same file:

```bash
# Make changes to multiple files
echo "Feature A" >> file1.txt
echo "Feature B" >> file2.txt
echo "Bug fix" >> file3.txt

# Stage only feature-related files
git add file1.txt file2.txt
git commit -m "Add Feature A and B"

# Later, commit bug fix separately
git add file3.txt
git commit -m "Fix bug in file3"
```

#### 2. Review Before Commit

```bash
# Stage changes
git add .

# Review what's staged
git diff --staged

# Unstage if needed
git reset HEAD file.txt

# Commit when ready
git commit -m "Commit message"
```

#### 3. Partial File Staging

Stage only portions of a file:

```bash
# Interactive staging
git add -p file.txt

# Git will show each change and ask:
# Stage this hunk [y,n,q,a,d,/,s,e,?]?
# y - yes, stage this hunk
# n - no, don't stage
# s - split into smaller hunks
# e - manually edit the hunk
```

### Staging Area States

Files in your repository can be in different states:

```
┌─────────────────────────────────────────────────┐
│             File States in Git                  │
└─────────────────────────────────────────────────┘

Untracked → Not in version control
    │
    │ git add
    ▼
Staged (New) → Prepared for first commit
    │
    │ git commit
    ▼
Tracked → Git knows about this file
    │
    │ (modify file)
    ▼
Modified → Changed but not staged
    │
    │ git add
    ▼
Staged (Modified) → Ready to commit
    │
    │ git commit
    ▼
Committed → Saved in repository
```

---

## Committing Changes

Commits are snapshots of your project at a specific point in time. Each commit contains:

- **Snapshot**: Complete state of all tracked files
- **Metadata**: Author, date, message
- **Parent**: Reference to previous commit(s)
- **Hash**: Unique SHA-1 identifier

### Creating Commits

#### Basic Commit

```bash
# Stage files
git add file1.txt file2.txt

# Commit with message
git commit -m "Add user authentication feature"
```

#### Commit All Tracked Files

```bash
# Stage and commit all modified tracked files
git commit -am "Update configuration files"

# Note: -a flag doesn't include new (untracked) files
```

#### Detailed Commit Message

```bash
# Open editor for multi-line message
git commit

# In editor:
# Subject line (50 chars or less)
#
# Detailed explanation of what changed and why.
# Wrap at 72 characters.
# - Bullet points are fine
# - Reference issues: Fixes #123
```

### Commit Message Best Practices

#### Good Commit Messages

```bash
# ✓ GOOD - Imperative mood, clear
git commit -m "Add password reset functionality"

# ✓ GOOD - Specific and descriptive
git commit -m "Fix null pointer exception in user service"

# ✓ GOOD - Reference related work
git commit -m "Update API docs for /users endpoint

- Add request/response examples
- Document error codes
- Fixes #456"
```

#### Poor Commit Messages

```bash
# ✗ BAD - Too vague
git commit -m "fix stuff"

# ✗ BAD - Past tense instead of imperative
git commit -m "Fixed bug"

# ✗ BAD - Too long subject line
git commit -m "This commit adds a new feature that allows users to reset their passwords via email and also updates the documentation"

# ✗ BAD - Multiple unrelated changes
git commit -m "Add feature, fix bug, update readme"
```

#### Conventional Commits Format

```bash
# Format: <type>(<scope>): <subject>

git commit -m "feat(auth): add OAuth2 login"
git commit -m "fix(api): resolve timeout issue"
git commit -m "docs(readme): update installation steps"
git commit -m "refactor(users): simplify validation logic"
git commit -m "test(auth): add login integration tests"
git commit -m "chore(deps): update dependencies"
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`

### Understanding Commit Objects

Each commit is a snapshot, not a delta:

```
Commit 1:
├─ Tree abc123
│  ├─ file1.txt (version A)
│  ├─ file2.txt (version A)
│  └─ file3.txt (version A)
├─ Author: John Doe
├─ Date: 2024-01-15
└─ Message: "Initial commit"

Commit 2 (child of Commit 1):
├─ Tree def456
│  ├─ file1.txt (version B) ← changed
│  ├─ file2.txt (version A) ← unchanged (linked)
│  └─ file3.txt (version A) ← unchanged (linked)
├─ Parent: Commit 1
├─ Author: John Doe
├─ Date: 2024-01-16
└─ Message: "Update file1"
```

---

## Viewing History

Understanding your project's history is crucial for debugging and collaboration.

### git log - View Commit History

#### Basic Log

```bash
# Show commit history
git log

# Output:
# commit a1b2c3d4e5f6... (HEAD -> main)
# Author: John Doe <john@example.com>
# Date:   Mon Jan 15 10:00:00 2024 -0500
#
#     Add user authentication feature
```

#### Formatted Log Views

```bash
# One line per commit
git log --oneline
# a1b2c3d Add user authentication
# b2c3d4e Fix login validation
# c3d4e5f Update UI components

# Graph view (shows branches)
git log --oneline --graph --all
# * a1b2c3d (HEAD -> main) Add authentication
# * b2c3d4e Fix validation
# | * c3d4e5f (feature-branch) Add feature
# |/
# * d4e5f6g Initial commit

# Show changes in each commit
git log -p

# Show stats (files changed, insertions, deletions)
git log --stat

# Last N commits
git log -n 5

# Show commits by author
git log --author="John Doe"

# Show commits in date range
git log --since="2 weeks ago"
git log --after="2024-01-01" --before="2024-01-31"

# Search commit messages
git log --grep="fix"

# Show commits that changed specific file
git log -- path/to/file.txt
```

#### Custom Log Format

```bash
# Custom format
git log --pretty=format:"%h - %an, %ar : %s"
# a1b2c3d - John Doe, 2 days ago : Add authentication
# b2c3d4e - Jane Smith, 1 week ago : Fix validation

# Useful format placeholders:
# %h  - Abbreviated commit hash
# %H  - Full commit hash
# %an - Author name
# %ae - Author email
# %ar - Author date, relative
# %ad - Author date
# %s  - Commit subject
# %b  - Commit body
```

### git show - Show Commit Details

```bash
# Show latest commit
git show

# Show specific commit
git show a1b2c3d

# Show specific file from commit
git show a1b2c3d:path/to/file.txt

# Show commit stats only
git show --stat a1b2c3d
```

### git diff - Compare Changes

```bash
# Show unstaged changes
git diff

# Show staged changes
git diff --staged
git diff --cached  # Same as --staged

# Compare two commits
git diff commit1 commit2

# Compare branches
git diff main feature-branch

# Show changes in specific file
git diff path/to/file.txt

# Word-level diff (instead of line-level)
git diff --word-diff

# Show only changed file names
git diff --name-only
```

---

## Working Directory States

Files in your working directory can be in various states. Understanding these states is crucial for effective Git usage.

### File Status States

```
┌──────────────────────────────────────────────────┐
│              File Lifecycle in Git               │
└──────────────────────────────────────────────────┘

Untracked  →  Staged  →  Committed
    ↑           ↓            ↓
    └───────────┴────────────┘
           (Modified)
```

### Checking Status

```bash
# Check repository status
git status

# Example output:
# On branch main
# Changes to be committed:
#   (use "git restore --staged <file>..." to unstage)
#         modified:   file1.txt
#
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git restore <file>..." to discard changes)
#         modified:   file2.txt
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#         file3.txt

# Short status
git status -s
# M  file1.txt   # Modified and staged
#  M file2.txt   # Modified but not staged
# ?? file3.txt   # Untracked
```

### Status Flags in Short Format

| Flag | Meaning |
|------|---------|
| `??` | Untracked |
| `A ` | Added (staged new file) |
| `M ` | Modified (staged) |
| ` M` | Modified (not staged) |
| `MM` | Modified, staged, then modified again |
| `D ` | Deleted (staged) |
| ` D` | Deleted (not staged) |
| `R ` | Renamed |

### Working with Different States

#### Untracked Files

```bash
# New file not in Git
echo "Hello" > newfile.txt

git status
# Untracked files:
#   newfile.txt

# Add to staging area
git add newfile.txt
```

#### Modified Files

```bash
# Modify tracked file
echo "Update" >> existingfile.txt

git status
# Changes not staged for commit:
#   modified: existingfile.txt

# Stage the change
git add existingfile.txt

# Or discard the change
git restore existingfile.txt
```

#### Staged Files

```bash
# File is staged and ready to commit
git status
# Changes to be committed:
#   modified: file.txt

# Unstage if needed
git restore --staged file.txt

# Or commit
git commit -m "Update file"
```

---

## Undoing Changes

Git provides multiple ways to undo changes depending on what stage they're in.

### Discard Unstaged Changes

```bash
# Restore file to last committed version
git restore file.txt

# Restore all files
git restore .

# Old syntax (still works)
git checkout -- file.txt
```

### Unstage Files

```bash
# Remove from staging area (keep changes)
git restore --staged file.txt

# Unstage all files
git restore --staged .

# Old syntax (still works)
git reset HEAD file.txt
```

### Amend Last Commit

```bash
# Forgot to include a file
git add forgotten-file.txt
git commit --amend --no-edit

# Change commit message
git commit --amend -m "New message"

# Warning: Don't amend commits already pushed to shared branches!
```

### Undo Commits

```bash
# Undo last commit, keep changes staged
git reset --soft HEAD~1

# Undo last commit, keep changes unstaged
git reset HEAD~1
git reset --mixed HEAD~1  # Same as above (default)

# Undo last commit, discard all changes
git reset --hard HEAD~1  # DANGEROUS - permanently loses work!

# Undo multiple commits
git reset --soft HEAD~3  # Undo last 3 commits
```

### Revert Commits (Safe for Shared Branches)

```bash
# Create new commit that undoes previous commit
git revert a1b2c3d

# Revert without auto-commit
git revert -n a1b2c3d
git revert --no-commit a1b2c3d
```

---

## Ignoring Files

Use `.gitignore` to prevent tracking of certain files.

### Creating .gitignore

```bash
# Create .gitignore file
touch .gitignore

# Add patterns
cat > .gitignore << EOF
# Logs
*.log

# Dependencies
node_modules/

# Environment
.env

# IDE
.vscode/
.idea/

# OS
.DS_Store
EOF

# Stage and commit
git add .gitignore
git commit -m "Add .gitignore"
```

### Common .gitignore Patterns

```bash
# Comments start with #

# Ignore specific file
secret.txt

# Ignore all files with extension
*.log
*.tmp

# Ignore directory
build/
dist/

# Ignore directory contents but not directory
logs/*
!logs/.gitkeep

# Ignore nested directory
**/temp/

# Negate ignore (whitelist)
!important.log
```

See [.gitignore topic](./09-gitignore.md) for comprehensive coverage.

---

## Practical Workflow Example

Here's a complete workflow from start to finish:

```bash
# 1. Create new project
mkdir my-project
cd my-project

# 2. Initialize Git
git init

# 3. Create initial files
echo "# My Project" > README.md
echo "console.log('Hello');" > app.js

# 4. Check status
git status
# Untracked files: README.md, app.js

# 5. Stage files
git add README.md app.js
# Or: git add .

# 6. Check what's staged
git status
git diff --staged

# 7. Commit
git commit -m "Initial commit: Add README and app.js"

# 8. Make changes
echo "More content" >> README.md
echo "// Add function" >> app.js

# 9. Review changes
git diff

# 10. Stage selectively
git add README.md

# 11. Check status
git status -s
# M  README.md    (staged)
#  M app.js       (not staged)

# 12. Commit staged changes
git commit -m "Update README"

# 13. Stage and commit remaining
git add app.js
git commit -m "Add function to app.js"

# 14. View history
git log --oneline
# c3d4e5f Add function to app.js
# b2c3d4e Update README
# a1b2c3d Initial commit: Add README and app.js
```

---

## Summary

| Concept | Key Commands | Purpose |
|---------|-------------|---------|
| Initialize | `git init` | Create new repository |
| Clone | `git clone <url>` | Copy existing repository |
| Stage | `git add <file>` | Prepare changes for commit |
| Commit | `git commit -m "msg"` | Save snapshot to history |
| Status | `git status` | Check working directory state |
| History | `git log` | View commit history |
| Diff | `git diff` | See changes |
| Unstage | `git restore --staged` | Remove from staging area |
| Discard | `git restore` | Undo working directory changes |

### Key Takeaways

1. **Staging area** gives you control over what goes in each commit
2. **Commits** are complete snapshots, not deltas
3. **Three states**: Modified, Staged, Committed
4. **Review before committing** using `git status` and `git diff`
5. **Commit often** with clear, descriptive messages
6. **History is your friend** - use `git log` to understand changes

## Next Topic

Continue to [Git Installation and Configuration](./04-git-installation-configuration.md) to set up Git on your system.
