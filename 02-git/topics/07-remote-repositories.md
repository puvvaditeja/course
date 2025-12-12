# Remote Repositories

## Overview

Remote repositories are versions of your project hosted on the internet or network. They enable collaboration, backup, and distribution of your code. This topic covers working with remote repositories including GitHub, GitLab, and Bitbucket.

---

## Understanding Remote Repositories

### What is a Remote Repository?

A remote repository is a Git repository hosted on a server that multiple developers can access to collaborate on a project.

```
┌──────────────────┐         ┌──────────────────┐         ┌──────────────────┐
│   Developer A    │         │   Developer B    │         │   Developer C    │
│                  │         │                  │         │                  │
│ Local Repository │         │ Local Repository │         │ Local Repository │
└────────┬─────────┘         └────────┬─────────┘         └────────┬─────────┘
         │                            │                            │
         │    push/pull               │    push/pull               │    push/pull
         │                            │                            │
         └────────────────────────────┼────────────────────────────┘
                                      │
                              ┌───────▼────────┐
                              │     Remote     │
                              │   Repository   │
                              │  (GitHub, etc) │
                              └────────────────┘
```

### Why Use Remote Repositories?

- **Collaboration**: Multiple developers work on same project
- **Backup**: Code is stored safely on remote servers
- **Distribution**: Share code with others
- **Code Review**: Pull requests and merge requests
- **CI/CD**: Integration with automated pipelines
- **Portfolio**: Showcase your work (public repos)
- **Access**: Work from anywhere

---

## Git Hosting Platforms

### GitHub

**Features:**
- Largest code hosting platform
- Free public and private repositories
- GitHub Actions (CI/CD)
- Project management tools
- GitHub Pages (static site hosting)
- Strong community features

**Use Cases:**
- Open source projects
- Personal portfolio
- Team collaboration
- Enterprise development (GitHub Enterprise)

**Pricing:**
- Free: Unlimited public/private repos
- Pro: $4/month - Advanced features
- Enterprise: Custom pricing

### GitLab

**Features:**
- Complete DevOps platform
- Built-in CI/CD (GitLab CI)
- Issue tracking and boards
- Container registry
- Self-hosted option available
- Integrated security scanning

**Use Cases:**
- Enterprise DevOps
- Self-hosted solutions
- Complete CI/CD pipelines
- Security-focused development

**Pricing:**
- Free: Core features
- Premium: $19/user/month
- Ultimate: $99/user/month
- Self-hosted: Free (Community Edition)

### Bitbucket

**Features:**
- Atlassian integration (Jira, Confluence)
- Bitbucket Pipelines (CI/CD)
- Pull requests and code review
- Branch permissions
- IP whitelisting

**Use Cases:**
- Teams using Atlassian products
- Enterprise with Jira integration
- Small teams (free for up to 5 users)

**Pricing:**
- Free: Up to 5 users
- Standard: $3/user/month
- Premium: $6/user/month

### Comparison Table

| Feature | GitHub | GitLab | Bitbucket |
|---------|--------|--------|-----------|
| Free Private Repos | Yes | Yes | Yes (≤5 users) |
| CI/CD | GitHub Actions | GitLab CI | Bitbucket Pipelines |
| Self-Hosted | GitHub Enterprise | Yes (CE/EE) | Bitbucket Server |
| Market Share | ~65% | ~20% | ~15% |
| Best For | Open source, Community | DevOps, Self-hosted | Atlassian users |

---

## Cloning Repositories

Cloning creates a local copy of a remote repository.

### Basic Cloning

```bash
# Clone via HTTPS
git clone https://github.com/username/repository.git

# Clone via SSH
git clone git@github.com:username/repository.git

# Clone into specific directory
git clone https://github.com/username/repository.git my-project

# Clone specific branch
git clone -b develop https://github.com/username/repository.git

# Shallow clone (limited history)
git clone --depth 1 https://github.com/username/repository.git
```

### What Happens When You Clone?

```bash
$ git clone https://github.com/username/repository.git

# Output:
# Cloning into 'repository'...
# remote: Enumerating objects: 100, done.
# remote: Counting objects: 100% (100/100), done.
# remote: Compressing objects: 100% (80/80), done.
# remote: Total 100 (delta 20), reused 90 (delta 15)
# Receiving objects: 100% (100/100), 25.00 KiB | 5.00 MiB/s, done.
# Resolving deltas: 100% (20/20), done.
```

Actions performed:
1. Creates directory
2. Initializes `.git` directory
3. Downloads all repository data
4. Checks out default branch
5. Sets up remote tracking

### HTTPS vs SSH

#### HTTPS Cloning

```bash
# HTTPS URL
git clone https://github.com/username/repository.git

# Pros:
# - Works everywhere (firewalls, proxies)
# - Easy to set up
# - No SSH key required

# Cons:
# - May need to enter credentials
# - Less secure with passwords
# - Credential helper needed for convenience
```

#### SSH Cloning

```bash
# SSH URL
git clone git@github.com:username/repository.git

# Pros:
# - No password needed (after SSH key setup)
# - More secure
# - Better for automation

# Cons:
# - Requires SSH key setup
# - May be blocked by firewalls
# - Slightly more complex setup

# Set up SSH key (see Installation topic)
ssh-keygen -t ed25519 -C "your.email@example.com"
# Add to GitHub/GitLab/Bitbucket
```

---

## Working with Remotes

### Viewing Remotes

```bash
# List remotes
git remote
# Output: origin

# List remotes with URLs
git remote -v
# Output:
# origin  https://github.com/username/repo.git (fetch)
# origin  https://github.com/username/repo.git (push)

# Show detailed remote info
git remote show origin
# Output includes:
# - Remote URL
# - Branch tracking info
# - Push/pull configuration
```

### Adding Remotes

```bash
# Add new remote
git remote add <name> <url>

# Example: Add origin
git remote add origin https://github.com/username/repository.git

# Example: Add upstream (for forks)
git remote add upstream https://github.com/original/repository.git

# Verify
git remote -v
# origin    https://github.com/username/repository.git
# upstream  https://github.com/original/repository.git
```

### Renaming Remotes

```bash
# Rename remote
git remote rename old-name new-name

# Example:
git remote rename origin github
```

### Removing Remotes

```bash
# Remove remote
git remote remove <name>

# Example:
git remote remove upstream
```

### Changing Remote URL

```bash
# Change remote URL
git remote set-url origin new-url

# Example: Switch from HTTPS to SSH
git remote set-url origin git@github.com:username/repository.git

# Verify
git remote -v
```

---

## Push Operations

Pushing uploads your local commits to a remote repository.

### Basic Push

```bash
# Push current branch to remote
git push origin main

# Push and set upstream (first time)
git push -u origin main
git push --set-upstream origin main

# After setting upstream, simply:
git push
```

### Push Variations

```bash
# Push specific branch
git push origin feature-branch

# Push all branches
git push --all origin

# Push tags
git push --tags

# Push branch and tags
git push --follow-tags

# Force push (DANGEROUS - overwrites remote)
git push --force origin main
git push -f origin main

# Force push with safety (fails if remote has changes you don't have)
git push --force-with-lease origin main
```

### Understanding Upstream

```bash
# Set upstream for current branch
git push -u origin feature-branch

# Now branch tracks remote
git branch -vv
# * feature-branch  a1b2c3d [origin/feature-branch] Latest commit

# After setting upstream:
git push    # Automatically pushes to origin/feature-branch
git pull    # Automatically pulls from origin/feature-branch
```

### Push Examples

```bash
# New branch - first push
git checkout -b feature/new-feature
git add .
git commit -m "Add new feature"
git push -u origin feature/new-feature

# Subsequent pushes
git commit -am "Update feature"
git push  # Goes to origin/feature/new-feature

# Delete remote branch
git push origin --delete feature/old-feature
```

---

## Pull Operations

Pulling downloads changes from remote repository and merges them.

### Basic Pull

```bash
# Pull from tracked remote branch
git pull

# Pull from specific remote and branch
git pull origin main

# Pull with rebase instead of merge
git pull --rebase origin main
```

### Pull vs Fetch

```bash
# git pull = git fetch + git merge

# Fetch only (doesn't merge)
git fetch origin
# Downloads data but doesn't change working directory

# Then merge manually
git merge origin/main

# Pull (fetch + merge in one step)
git pull origin main
```

### Fetch Operations

```bash
# Fetch from all remotes
git fetch --all

# Fetch and prune deleted remote branches
git fetch --prune
git fetch -p

# Fetch specific branch
git fetch origin feature-branch

# Fetch tags
git fetch --tags
```

### Pull Strategies

```bash
# Merge strategy (default)
git pull origin main
# Creates merge commit if diverged

# Rebase strategy (cleaner history)
git pull --rebase origin main
# Reapplies your commits on top

# Fast-forward only (fails if not possible)
git pull --ff-only origin main

# Configure default pull strategy
git config pull.rebase false  # merge (default)
git config pull.rebase true   # rebase
git config pull.ff only       # fast-forward only
```

### Handling Pull Conflicts

```bash
# Pull causes conflict
git pull origin main
# CONFLICT (content): Merge conflict in file.txt

# Resolve conflicts
# Edit file.txt, remove markers, save

# Stage resolved files
git add file.txt

# Complete merge
git commit

# Or abort
git merge --abort
```

---

## Remote Branch Management

### Tracking Remote Branches

```bash
# List remote branches
git branch -r
# Output:
# origin/main
# origin/develop
# origin/feature-login

# List all branches (local and remote)
git branch -a
# Output:
# * main
#   develop
#   remotes/origin/main
#   remotes/origin/develop

# Create local branch tracking remote
git checkout -b feature-branch origin/feature-branch
# Modern syntax:
git switch feature-branch  # Auto-tracks if name matches remote
```

### Updating Remote References

```bash
# Update remote tracking branches
git fetch origin

# See what changed
git fetch --dry-run

# Remove deleted remote branch references
git fetch --prune
git remote prune origin  # Same effect
```

### Comparing with Remote

```bash
# See commits not yet pushed
git log origin/main..main

# See commits not yet pulled
git log main..origin/main

# Show all differences
git diff main origin/main

# Show stats
git diff --stat main origin/main
```

---

## Working with Forks

Forks are personal copies of repositories, common in open source.

### Fork Workflow

```
Original Repo (upstream)
    └─> Your Fork (origin)
            └─> Your Local Clone
```

### Setting Up Fork

```bash
# 1. Fork repository on GitHub (click Fork button)

# 2. Clone your fork
git clone https://github.com/your-username/project.git
cd project

# 3. Add upstream remote
git remote add upstream https://github.com/original-owner/project.git

# 4. Verify remotes
git remote -v
# origin    https://github.com/your-username/project.git
# upstream  https://github.com/original-owner/project.git
```

### Keeping Fork Updated

```bash
# 1. Fetch upstream changes
git fetch upstream

# 2. Checkout your main branch
git checkout main

# 3. Merge upstream changes
git merge upstream/main

# 4. Push to your fork
git push origin main
```

### Contributing to Upstream

```bash
# 1. Update from upstream
git fetch upstream
git checkout main
git merge upstream/main

# 2. Create feature branch
git checkout -b feature/contribution

# 3. Make changes and commit
git add .
git commit -m "Add awesome feature"

# 4. Push to your fork
git push -u origin feature/contribution

# 5. Create pull request on GitHub
# (from your fork to upstream)
```

---

## Practical Workflows

### Daily Development Workflow

```bash
# Morning: Get latest changes
git checkout main
git pull origin main

# Create feature branch
git checkout -b feature/new-feature

# Develop feature
# (edit files)
git add .
git commit -m "Implement feature"

# Push to remote
git push -u origin feature/new-feature

# Continue working
git commit -am "Refine feature"
git push

# Keep updated with main
git fetch origin
git merge origin/main
# Or: git rebase origin/main

# When done: create pull request
```

### Team Collaboration Workflow

```bash
# Clone repository
git clone git@github.com:team/project.git
cd project

# Create feature branch
git checkout -b feature/my-contribution

# Work on feature
git commit -am "Add my changes"

# Before pushing, get latest main
git checkout main
git pull origin main

# Update feature branch
git checkout feature/my-contribution
git rebase main  # Or merge main

# Push feature
git push -u origin feature/my-contribution

# Create pull request for review

# After PR merged, clean up
git checkout main
git pull origin main
git branch -d feature/my-contribution
git push origin --delete feature/my-contribution
```

### Multiple Remotes Workflow

```bash
# Add multiple remotes
git remote add github git@github.com:user/repo.git
git remote add gitlab git@gitlab.com:user/repo.git

# Push to both
git push github main
git push gitlab main

# Or push to all
git remote set-url --add --push origin git@github.com:user/repo.git
git remote set-url --add --push origin git@gitlab.com:user/repo.git
git push origin main  # Pushes to both
```

---

## Troubleshooting

### Push Rejected

```bash
# Error: Updates were rejected

# Cause: Remote has changes you don't have

# Solution 1: Pull and merge
git pull origin main
# Resolve conflicts if any
git push origin main

# Solution 2: Pull with rebase
git pull --rebase origin main
git push origin main

# Solution 3: Force push (CAREFUL!)
git push --force-with-lease origin main
```

### Authentication Issues

```bash
# HTTPS: Credential helper
git config --global credential.helper cache

# SSH: Check SSH key
ssh -T git@github.com

# SSH: Add key to agent
ssh-add ~/.ssh/id_ed25519
```

### Fetch/Pull Too Slow

```bash
# Shallow clone
git clone --depth 1 https://github.com/user/repo.git

# Limit fetch depth
git fetch --depth 1

# Single branch clone
git clone --single-branch --branch main https://github.com/user/repo.git
```

### Accidental Force Push

```bash
# If you force pushed and regret it:

# Find the commit before force push
git reflog

# Reset to that commit
git reset --hard commit-hash

# Force push again (carefully!)
git push --force origin main
```

---

## Best Practices

### Pushing

- Pull before pushing
- Never force push to shared branches (main, develop)
- Use `--force-with-lease` instead of `--force`
- Push frequently (don't hoard commits)
- Write clear commit messages

### Pulling

- Pull frequently to stay updated
- Review changes before merging
- Resolve conflicts carefully
- Consider rebase for cleaner history
- Communicate with team about conflicts

### Branch Management

- Delete merged remote branches
- Prune old remote references regularly
- Use descriptive branch names
- Track remote branches explicitly
- Keep forks updated

---

## Summary

| Command | Purpose | Example |
|---------|---------|---------|
| `git clone` | Copy remote repository | `git clone <url>` |
| `git remote` | Manage remotes | `git remote -v` |
| `git push` | Upload commits | `git push origin main` |
| `git pull` | Download and merge | `git pull origin main` |
| `git fetch` | Download only | `git fetch origin` |
| `git remote add` | Add remote | `git remote add upstream <url>` |

### Key Concepts

| Concept | Description |
|---------|-------------|
| Remote | Repository hosted on server |
| Origin | Default remote name |
| Upstream | Original repository (for forks) |
| Tracking | Local branch follows remote branch |
| Push | Send commits to remote |
| Pull | Fetch and merge from remote |
| Fetch | Download without merging |
| Fork | Personal copy of repository |

### Common Workflows

```bash
# Clone and contribute
git clone <url>
git checkout -b feature
git commit -am "Changes"
git push -u origin feature

# Update from remote
git fetch origin
git merge origin/main

# Fork workflow
git remote add upstream <original-url>
git fetch upstream
git merge upstream/main
git push origin main
```

## Next Topic

Continue to [Pull Requests](./08-pull-requests.md) to learn about code review and collaboration through pull requests.
