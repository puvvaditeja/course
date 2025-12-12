# Git - Source Control Management

## What is Version Control?

Version control (also known as source control or revision control) is a system that records changes to files over time so that you can recall specific versions later. It allows you to:

- Track changes to your code over time
- Revert files or entire projects to previous states
- Compare changes over time
- See who last modified something that might be causing a problem
- Collaborate with team members without overwriting each other's work
- Maintain multiple versions of your codebase simultaneously

### Why Version Control Matters

Without version control, developers might:

1. **Lose work**: Accidentally overwrite important code
2. **Lack history**: No record of what changed, when, or why
3. **Struggle with collaboration**: Multiple developers can't work simultaneously
4. **Have backup issues**: No way to recover from mistakes
5. **Face integration problems**: Difficult to merge different code versions

---

## Centralized vs Distributed Version Control

Version control systems are broadly categorized into two types based on their architecture.

### Centralized Version Control Systems (CVCS)

In centralized systems, there is a single central repository that stores all versions of the code.

**How it works:**
```
        Central Server
             ┃
      ┏━━━━━━╋━━━━━━┓
      ┃      ┃      ┃
   Client A Client B Client C
   (Working (Working (Working
     Copy)    Copy)    Copy)
```

**Characteristics:**
- Single source of truth on a central server
- Developers check out files from the central repository
- Requires network connection for most operations
- Examples: SVN (Subversion), CVS, Perforce

**Advantages:**
- Simple mental model (one central repository)
- Easy to understand who has what
- Centralized access control
- Smaller local storage requirements

**Disadvantages:**
- Single point of failure
- Requires network access for most operations
- Slower operations (network dependent)
- Limited offline capabilities
- Bottleneck when many developers work simultaneously

### Distributed Version Control Systems (DVCS)

In distributed systems, every developer has a complete copy of the repository including full history.

**How it works:**
```
    Developer A          Developer B          Developer C
   ┏━━━━━━━━━━┓        ┏━━━━━━━━━━┓        ┏━━━━━━━━━━┓
   ┃ Full Repo ┃        ┃ Full Repo ┃        ┃ Full Repo ┃
   ┃ + History ┃ ←────→ ┃ + History ┃ ←────→ ┃ + History ┃
   ┗━━━━━━━━━━┛        ┗━━━━━━━━━━┛        ┗━━━━━━━━━━┛
         ↕                    ↕                    ↕
    ┌──────────┐        ┌──────────┐        ┌──────────┐
    │  Remote  │←──────→│  Remote  │←──────→│  Remote  │
    │   Repo   │        │   Repo   │        │   Repo   │
    └──────────┘        └──────────┘        └──────────┘
```

**Characteristics:**
- Complete repository copy on each developer's machine
- Most operations are local and fast
- Can work offline
- Examples: Git, Mercurial, Bazaar

**Advantages:**
- Fast operations (everything is local)
- Full offline capability
- No single point of failure
- Flexible workflows
- Better branching and merging
- Multiple backup copies naturally exist

**Disadvantages:**
- Larger local storage requirements
- Steeper learning curve
- More complex mental model
- Can be overwhelming for beginners

### Comparison Table

| Feature | Centralized (CVCS) | Distributed (DVCS) |
|---------|-------------------|-------------------|
| Repository | Single central server | Every clone is a full repository |
| Network Required | For most operations | Only for sync operations |
| Speed | Slower (network dependent) | Faster (local operations) |
| Offline Work | Limited | Full capability |
| Backup | Single point of failure | Multiple copies |
| Branching | Complex, heavyweight | Fast, lightweight |
| Learning Curve | Easier | Steeper |
| Examples | SVN, Perforce | Git, Mercurial |

---

## Git Architecture and Concepts

Git is a distributed version control system with a unique architecture designed for speed, efficiency, and distributed development.

### Core Concepts

#### 1. Repository (Repo)

A repository is a directory that contains:
- All project files
- Complete version history
- Configuration information
- Branch and tag data

```bash
my-project/
├── .git/           # Git repository data
├── src/            # Project files
├── README.md
└── .gitignore
```

#### 2. Three States of Files

Git has three main states that files can be in:

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  Modified   │────→│   Staged    │────→│  Committed  │
│  (Working)  │ add │  (Index)    │commit│(Repository) │
└─────────────┘     └─────────────┘     └─────────────┘
```

**Modified**: You've changed the file but haven't committed it yet
**Staged**: You've marked a modified file to go into your next commit
**Committed**: Data is safely stored in your local repository

#### 3. Three Main Areas

```
┌──────────────────┐
│  Working         │  ← Where you edit files
│  Directory       │
└────────┬─────────┘
         │ git add
         ↓
┌──────────────────┐
│  Staging Area    │  ← Prepared snapshot for next commit
│  (Index)         │
└────────┬─────────┘
         │ git commit
         ↓
┌──────────────────┐
│  Repository      │  ← Permanent storage of snapshots
│  (.git directory)│
└──────────────────┘
```

**Working Directory**: Current version of files you're working on
**Staging Area (Index)**: Proposed next commit; files ready to be committed
**Repository**: Committed snapshots of your project

#### 4. Snapshots, Not Differences

Unlike other VCS that store changes as file differences, Git stores data as snapshots.

**Traditional VCS (Delta-based):**
```
File A: [Version 1] → [+changes] → [+changes] → [+changes]
File B: [Version 1] → [+changes] → [+changes]
```

**Git (Snapshot-based):**
```
Commit 1: [Snapshot: A1, B1, C1]
Commit 2: [Snapshot: A2, B1, C2]  # B unchanged (linked to B1)
Commit 3: [Snapshot: A2, B3, C3]  # A unchanged (linked to A2)
```

Benefits:
- Fast branching and merging
- Efficient storage (unchanged files are linked)
- Better integrity (each commit is a complete state)

#### 5. Git Object Model

Git uses four object types:

```
Blob (Binary Large Object)
  ├─ Stores file contents
  └─ Identified by SHA-1 hash

Tree
  ├─ Represents directories
  ├─ Contains blobs and other trees
  └─ Maps names to blobs/trees

Commit
  ├─ Points to a tree (project snapshot)
  ├─ Contains metadata (author, date, message)
  ├─ References parent commit(s)
  └─ Creates the version history

Tag
  ├─ Named reference to a commit
  └─ Used for releases/milestones
```

---

## Git Workflow Overview

Understanding the basic Git workflow is essential for effective version control.

### Basic Daily Workflow

```
1. Modify files in working directory
         ↓
2. Stage changes you want to commit
         ↓
3. Commit staged changes to repository
         ↓
4. Push commits to remote repository
```

### Complete Workflow with Commands

```bash
# 1. Check current status
git status

# 2. Make changes to files
# (edit files in your text editor)

# 3. Review changes
git diff

# 4. Stage changes
git add filename.txt        # Stage specific file
git add .                   # Stage all changes

# 5. Review staged changes
git diff --staged

# 6. Commit changes
git commit -m "Descriptive message"

# 7. View history
git log

# 8. Push to remote
git push origin main
```

### Common Workflow Patterns

#### Feature Development Workflow

```
1. Create feature branch
   ├─→ git checkout -b feature/new-feature

2. Make changes and commit
   ├─→ (edit files)
   ├─→ git add .
   └─→ git commit -m "Add new feature"

3. Push feature branch
   └─→ git push origin feature/new-feature

4. Create pull request (on GitHub/GitLab)

5. Merge to main after review

6. Delete feature branch
   └─→ git branch -d feature/new-feature
```

#### Collaboration Workflow

```
Morning:
  git pull origin main           # Get latest changes

During day:
  (make changes)
  git add .
  git commit -m "Message"
  git push origin your-branch    # Share your work

Before end of day:
  git pull origin main           # Get others' changes
  (resolve any conflicts)
  git push origin your-branch
```

### Git Workflow Visualization

```
Time ─────────────────────────→

main:     A───B───────F───G
               \     /
feature:        C───D───E

A, B: Initial commits on main
C, D, E: Feature development
F: Merge feature into main
G: Continue on main
```

---

## Benefits of Using Git

### 1. Speed and Performance
- Most operations are local and instantaneous
- No network latency for commits, diffs, logs
- Can work efficiently with large projects

### 2. Distributed Development
- Every developer has full repository
- Can work offline
- Multiple backup copies
- No single point of failure

### 3. Branching and Merging
- Lightweight and fast branch creation
- Easy context switching
- Safe experimentation
- Powerful merge capabilities

### 4. Data Integrity
- Every file and commit is checksummed (SHA-1)
- Impossible to change history without detection
- Corruption is immediately detectable

### 5. Staging Area
- Fine-grained control over commits
- Review changes before committing
- Commit logical units of work
- Split work into multiple commits

### 6. Free and Open Source
- No licensing costs
- Large community support
- Continuous improvements
- Widely adopted industry standard

---

## Git in the Software Development Lifecycle

### Where Git Fits

```
┌─────────────────────────────────────────────┐
│         Software Development                │
│                                             │
│  Plan → Code → Build → Test → Deploy       │
│           ↑                                 │
│         [GIT]                               │
│    Version Control                          │
│    - Track changes                          │
│    - Collaboration                          │
│    - Code review                            │
│    - Release management                     │
└─────────────────────────────────────────────┘
```

### Integration with Development Tools

- **IDEs**: IntelliJ, VS Code, Eclipse have Git integration
- **CI/CD**: Jenkins, GitHub Actions, GitLab CI
- **Code Review**: GitHub Pull Requests, GitLab Merge Requests
- **Project Management**: Jira, Trello integration
- **Documentation**: GitHub Pages, GitBook

---

## Summary

| Concept | Key Points |
|---------|------------|
| Version Control | Tracks changes, enables collaboration, maintains history |
| CVCS vs DVCS | Centralized (SVN) vs Distributed (Git) |
| Git Architecture | Three states: Modified, Staged, Committed |
| Snapshots | Git stores complete snapshots, not deltas |
| Workflow | Modify → Stage → Commit → Push |
| Benefits | Speed, distributed, branching, integrity |

## Next Topic

Continue to [VCS, CVCS, DVCS](./02-vcs-cvcs-dvcs.md) for a deeper comparison of version control system types.
