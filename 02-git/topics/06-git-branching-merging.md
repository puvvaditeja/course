# Git Branching and Merging

## Overview

Branching is one of Git's most powerful features. It allows you to diverge from the main line of development and work on different features or experiments independently without affecting the main codebase.

---

## Understanding Branches

### What is a Branch?

A branch is a lightweight, movable pointer to a commit. The default branch is typically called `main` (or `master` in older repositories).

```
main:  A---B---C---D
```

When you create a branch, you're creating a new pointer to the current commit:

```
main:         A---B---C---D
                       ↑
                    feature  (new branch)
```

### Why Use Branches?

- **Isolated development**: Work on features without affecting main code
- **Experimentation**: Try new ideas safely
- **Parallel development**: Multiple features can be developed simultaneously
- **Code review**: Develop on branches, merge after review
- **Release management**: Maintain different versions

### Branches are Cheap

Unlike other VCS, Git branches are:
- **Fast** to create (milliseconds)
- **Lightweight** (40 bytes - just a pointer)
- **Easy** to switch between
- **Safe** to experiment with

---

## Creating Branches

### Basic Branch Creation

```bash
# Create new branch
git branch feature-login

# Create and switch to new branch
git checkout -b feature-login
# Or using modern syntax:
git switch -c feature-login

# Create branch from specific commit
git branch feature-name a1b2c3d

# Create branch from remote branch
git branch feature-name origin/feature-name
```

### Branch Naming Conventions

```bash
# Feature branches
git branch feature/user-authentication
git branch feature/payment-integration

# Bug fix branches
git branch fix/login-validation
git branch bugfix/null-pointer-error

# Hotfix branches (urgent production fixes)
git branch hotfix/security-patch
git branch hotfix/critical-bug

# Release branches
git branch release/v1.2.0
git branch release/2024-01

# Experimental branches
git branch experiment/new-architecture
git branch poc/graphql-api
```

### Visualizing Branch Creation

```
Before:
main:  A---B---C (HEAD)

After: git branch feature
main:         A---B---C (HEAD)
                      ↑
                   feature

After: git checkout feature
main:         A---B---C
                      ↑
                   feature (HEAD)
```

---

## Switching Branches

### Modern Syntax (git switch)

```bash
# Switch to existing branch
git switch feature-login

# Create and switch to new branch
git switch -c new-feature

# Switch to previous branch
git switch -

# Switch to remote branch (creates local tracking branch)
git switch feature-remote
```

### Traditional Syntax (git checkout)

```bash
# Switch to existing branch
git checkout feature-login

# Create and switch to new branch
git checkout -b new-feature

# Switch to previous branch
git checkout -

# Switch to specific commit (detached HEAD)
git checkout a1b2c3d
```

### What Happens When You Switch?

1. Updates working directory files
2. Moves HEAD pointer to new branch
3. Updates staging area

```bash
# On main branch
$ cat app.js
console.log('main version');

$ git switch feature
# Switched to branch 'feature'

$ cat app.js
console.log('feature version');
```

### Switching with Uncommitted Changes

```bash
# If you have uncommitted changes:

# Option 1: Commit changes first
git commit -am "Work in progress"
git switch other-branch

# Option 2: Stash changes
git stash
git switch other-branch
# Later: git stash pop

# Option 3: Force switch (loses changes!)
git switch -f other-branch  # DANGEROUS
```

---

## Listing and Viewing Branches

### List Branches

```bash
# List local branches
git branch

# Output:
#   feature-login
# * main          # * indicates current branch
#   develop

# List with last commit info
git branch -v

# List all branches (local and remote)
git branch -a

# List remote branches only
git branch -r

# List merged branches
git branch --merged

# List unmerged branches
git branch --no-merged
```

### Detailed Branch Information

```bash
# Show branches with tracking info
git branch -vv

# Output:
# * main           a1b2c3d [origin/main] Latest commit
#   feature-login  b2c3d4e Add login form
#   develop        c3d4e5f [origin/develop: ahead 2] New feature

# Show branches containing specific commit
git branch --contains a1b2c3d

# Show branches not containing commit
git branch --no-contains a1b2c3d
```

---

## Deleting Branches

### Delete Local Branch

```bash
# Delete merged branch (safe)
git branch -d feature-login

# Force delete unmerged branch
git branch -D feature-login  # CAREFUL!

# Delete multiple branches
git branch -d branch1 branch2 branch3
```

### Delete Remote Branch

```bash
# Delete remote branch
git push origin --delete feature-login

# Alternative syntax
git push origin :feature-login
```

### Cleanup Deleted Remote Branches

```bash
# Remove references to deleted remote branches
git fetch --prune
git fetch -p

# Remove local branches that were deleted on remote
git branch -vv | grep ': gone]' | awk '{print $1}' | xargs git branch -D
```

---

## Merging Branches

Merging combines changes from different branches.

### Fast-Forward Merge

When there are no divergent changes, Git can simply move the pointer forward.

```
Before merge:
main:         A---B---C
                       \
feature:                D---E (HEAD)

git checkout main
git merge feature

After merge (fast-forward):
main:         A---B---C---D---E (HEAD)
                               ↑
                            feature
```

```bash
# Fast-forward merge
git checkout main
git merge feature-login

# Output:
# Updating a1b2c3d..e5f6g7h
# Fast-forward
#  login.js | 25 +++++++++++++++++++
#  1 file changed, 25 insertions(+)
```

### Three-Way Merge

When branches have diverged, Git creates a merge commit.

```
Before merge:
main:         A---B---C---F
                   \
feature:            D---E

git checkout main
git merge feature

After merge (three-way):
main:         A---B---C---F---G (merge commit)
                   \           /
feature:            D---------E
```

```bash
# Three-way merge
git checkout main
git merge feature-login

# Output:
# Merge made by the 'recursive' strategy.
#  login.js | 25 +++++++++++++++++++
#  auth.js  | 15 +++++++++++++
#  2 files changed, 40 insertions(+)
```

### Merge Commands

```bash
# Basic merge
git merge branch-name

# Merge with commit message
git merge branch-name -m "Merge feature-login into main"

# Merge without fast-forward (always create merge commit)
git merge --no-ff branch-name

# Merge with only fast-forward (abort if not possible)
git merge --ff-only branch-name

# Squash merge (combine all commits into one)
git merge --squash branch-name
# Then: git commit -m "Merged feature"
```

---

## Resolving Merge Conflicts

Conflicts occur when the same part of a file is modified differently in both branches.

### Understanding Conflicts

```
main branch:
function login(user, password) {
    return authenticate(user, password);
}

feature branch:
function login(username, pwd) {
    return verifyCredentials(username, pwd);
}

Result: CONFLICT!
```

### Conflict Process

```bash
# Attempt merge
git merge feature-login

# Output:
# Auto-merging auth.js
# CONFLICT (content): Merge conflict in auth.js
# Automatic merge failed; fix conflicts and then commit the result.

# Check status
git status
# Unmerged paths:
#   both modified:   auth.js
```

### Conflict Markers

Git marks conflicts in the file:

```javascript
<<<<<<< HEAD (current branch)
function login(user, password) {
    return authenticate(user, password);
}
=======
function login(username, pwd) {
    return verifyCredentials(username, pwd);
}
>>>>>>> feature-login (incoming branch)
```

### Resolving Conflicts

#### Manual Resolution

```bash
# 1. Open conflicted file
# 2. Find conflict markers (<<<<<<<, =======, >>>>>>>)
# 3. Edit to keep desired code
# 4. Remove conflict markers
# 5. Save file

# Example resolution:
function login(username, password) {
    return authenticate(username, password);
}

# 6. Stage resolved file
git add auth.js

# 7. Complete merge
git commit
# Git will provide default merge commit message
```

#### Using Merge Tools

```bash
# Launch configured merge tool
git mergetool

# Common merge tools:
# - VS Code
# - vimdiff
# - meld
# - kdiff3
# - Beyond Compare

# Configure merge tool
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'
```

### Conflict Resolution Strategies

```bash
# Accept all changes from current branch (ours)
git checkout --ours conflicted-file.txt
git add conflicted-file.txt

# Accept all changes from incoming branch (theirs)
git checkout --theirs conflicted-file.txt
git add conflicted-file.txt

# Abort merge
git merge --abort

# After resolving conflicts
git merge --continue
```

### Viewing Conflicts

```bash
# Show conflicts
git diff

# Show conflicts in three-way format
git diff --ours
git diff --theirs
git diff --base

# List conflicted files
git diff --name-only --diff-filter=U

# Show log of merge conflicts
git log --merge
```

---

## Branch Strategies

### Feature Branch Workflow

Most common workflow for modern development.

```
main:     A---B---C-------G---H
               \         /
feature:        D---E---F

1. Create feature branch
2. Develop feature
3. Create pull request
4. Code review
5. Merge to main
6. Delete feature branch
```

```bash
# Start new feature
git checkout -b feature/user-profile
# Develop feature
git add .
git commit -m "Add user profile page"
git push -u origin feature/user-profile
# Create pull request on GitHub/GitLab
# After approval: merge and delete branch
```

### Gitflow Workflow

Structured workflow for release management.

```
main:       A-----------F-----------K (production)
             \         / \         /
hotfix:       \       H---I       /
               \     /     \     /
release:        \   /       J---+
                 \ /             \
develop:    B---C---D---E---------L
                 \     /
feature:          G---+
```

**Branches:**
- `main`: Production-ready code
- `develop`: Integration branch
- `feature/*`: Feature development
- `release/*`: Release preparation
- `hotfix/*`: Emergency production fixes

```bash
# Feature development
git checkout -b feature/payment develop
# Work on feature
git checkout develop
git merge --no-ff feature/payment
git branch -d feature/payment

# Release
git checkout -b release/1.2 develop
# Prepare release (version bump, etc.)
git checkout main
git merge --no-ff release/1.2
git tag -a v1.2
git checkout develop
git merge --no-ff release/1.2

# Hotfix
git checkout -b hotfix/security main
# Fix critical bug
git checkout main
git merge --no-ff hotfix/security
git tag -a v1.2.1
git checkout develop
git merge --no-ff hotfix/security
```

### GitHub Flow

Simplified workflow, ideal for continuous deployment.

```
main:  A---B---E---F---I
            \     /     \
feature1:    C---D       \
                          \
feature2:                  G---H

Always deployable main branch
Feature branches for everything
```

```bash
# Create feature branch
git checkout -b feature/new-feature main
# Make changes
git commit -am "Add feature"
# Push and create PR
git push -u origin feature/new-feature
# After review: merge to main
# Deploy main
# Delete feature branch
```

### Trunk-Based Development

Main branch is always deployable, very short-lived branches.

```
main:  A---B---C---D---E---F (always deployable)
            \   / \   /
feature:     b-+   d-+

Features: < 1 day old
Continuous integration
Feature flags for incomplete features
```

---

## Branch Management Best Practices

### Naming Conventions

```bash
# Good branch names
feature/user-authentication
fix/login-button-crash
hotfix/security-vulnerability
release/v2.1.0
experiment/graphql-migration

# Bad branch names
new-stuff
fix
mybranch
test123
```

### Keep Branches Updated

```bash
# Update feature branch with latest main
git checkout feature-branch
git merge main
# Or rebase (cleaner history)
git rebase main

# Regularly pull from remote
git pull origin main
```

### Delete Merged Branches

```bash
# After merging, delete feature branch
git branch -d feature-login

# Delete remote branch
git push origin --delete feature-login

# Clean up old remote branches
git fetch --prune
```

### Avoid Long-Lived Branches

- Merge frequently
- Keep branches small and focused
- Aim for branches < 1 week old
- Regular integration reduces merge conflicts

---

## Advanced Branching Operations

### Rebase

Reapply commits on top of another base.

```
Before rebase:
main:     A---B---C---D
               \
feature:        E---F---G

git checkout feature
git rebase main

After rebase:
main:     A---B---C---D
                       \
feature:                E'--F'--G'
```

```bash
# Rebase feature onto main
git checkout feature-branch
git rebase main

# Interactive rebase (edit history)
git rebase -i HEAD~3

# Continue after resolving conflicts
git rebase --continue

# Abort rebase
git rebase --abort
```

**Warning**: Never rebase public/shared branches!

### Cherry-Pick

Apply specific commits to current branch.

```bash
# Apply commit to current branch
git cherry-pick a1b2c3d

# Apply multiple commits
git cherry-pick a1b2c3d e4f5g6h

# Cherry-pick without committing
git cherry-pick -n a1b2c3d
```

### Branch Reset

```bash
# Reset branch to match remote
git fetch origin
git reset --hard origin/main

# Reset branch to specific commit
git reset --hard a1b2c3d
```

---

## Practical Examples

### Complete Feature Development

```bash
# 1. Update main
git checkout main
git pull origin main

# 2. Create feature branch
git checkout -b feature/shopping-cart

# 3. Develop feature
echo "cart code" > cart.js
git add cart.js
git commit -m "Add shopping cart functionality"

echo "more code" >> cart.js
git commit -am "Add item removal from cart"

# 4. Push to remote
git push -u origin feature/shopping-cart

# 5. Keep branch updated with main
git fetch origin
git merge origin/main

# 6. Create pull request (on GitHub/GitLab)

# 7. After approval, merge to main
git checkout main
git merge --no-ff feature/shopping-cart

# 8. Clean up
git branch -d feature/shopping-cart
git push origin --delete feature/shopping-cart
```

### Handling Merge Conflicts

```bash
# Start merge
git checkout main
git merge feature-branch

# Conflict occurs
# CONFLICT (content): Merge conflict in app.js

# Check status
git status

# Resolve conflicts in editor
# Edit app.js, remove markers, save

# Stage resolved file
git add app.js

# Complete merge
git commit
# (Git provides default message)

# Push merge
git push origin main
```

---

## Summary

| Command | Purpose | Example |
|---------|---------|---------|
| `git branch` | Create/list branches | `git branch feature-login` |
| `git switch` | Switch branches | `git switch feature-login` |
| `git checkout -b` | Create and switch | `git checkout -b new-feature` |
| `git merge` | Merge branches | `git merge feature-login` |
| `git branch -d` | Delete branch | `git branch -d feature-login` |
| `git rebase` | Reapply commits | `git rebase main` |
| `git cherry-pick` | Apply specific commit | `git cherry-pick a1b2c3d` |

### Key Takeaways

1. **Branches are cheap** - create them freely
2. **Branch often** - isolate features and experiments
3. **Merge regularly** - avoid long-lived branches
4. **Delete merged branches** - keep repository clean
5. **Resolve conflicts carefully** - understand both changes
6. **Choose a workflow** - be consistent across team
7. **Use meaningful names** - make branch purpose clear

### Merge vs Rebase

| Aspect | Merge | Rebase |
|--------|-------|--------|
| History | Preserves complete history | Creates linear history |
| Safety | Safe for public branches | Dangerous for public branches |
| Commits | Creates merge commits | No merge commits |
| Use When | Integrating finished features | Cleaning up local history |

## Next Topic

Continue to [Remote Repositories](./07-remote-repositories.md) to learn about collaborating with others using remote Git repositories.
