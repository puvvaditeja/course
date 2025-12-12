# .gitignore

## Overview

The `.gitignore` file tells Git which files and directories to ignore and not track. This is essential for keeping your repository clean and secure by excluding build artifacts, dependencies, sensitive data, and OS-specific files.

---

## What is .gitignore?

### Purpose

The `.gitignore` file specifies intentionally untracked files that Git should ignore.

**Common files to ignore:**
- Build outputs (compiled code, bundles)
- Dependencies (node_modules, vendor)
- Environment files (.env, config files with secrets)
- IDE/editor settings (.vscode, .idea)
- OS-generated files (.DS_Store, Thumbs.db)
- Log files (*.log)
- Temporary files (*.tmp, *.cache)

### Why Use .gitignore?

**Benefits:**
- Keep repository clean and focused
- Prevent accidentally committing sensitive data
- Reduce repository size
- Avoid merge conflicts on generated files
- Speed up Git operations
- Improve collaboration (avoid personal config conflicts)

**Problems without .gitignore:**
```bash
$ git status
Untracked files:
  node_modules/        (500+ files)
  .env                 (contains secrets!)
  .DS_Store            (Mac OS file)
  dist/                (build output)
  *.log                (log files)
  .vscode/             (personal IDE settings)
```

---

## Creating .gitignore

### Basic Setup

```bash
# Create .gitignore file
touch .gitignore

# Or with content
cat > .gitignore << EOF
# Dependencies
node_modules/

# Environment variables
.env

# Build output
dist/
build/
EOF

# Stage and commit
git add .gitignore
git commit -m "Add .gitignore"
```

### Location

```
project/
├── .gitignore          # Repository root (most common)
├── src/
│   └── .gitignore      # Directory-specific (less common)
└── docs/
    └── .gitignore      # Directory-specific
```

**Best practice**: Use a single `.gitignore` at repository root.

---

## .gitignore Syntax and Patterns

### Basic Patterns

```bash
# Comments (lines starting with #)
# This is a comment

# Blank lines are ignored


# Ignore specific file
secret.txt

# Ignore specific directory
logs/
build/

# Ignore all files with extension
*.log
*.tmp
*.cache

# Ignore files in any directory
**/temp.txt
```

### Wildcards and Patterns

```bash
# * matches anything except /
*.log           # Matches: app.log, error.log
                # Matches: logs/app.log

# ** matches zero or more directories
**/logs/        # Matches: logs/, src/logs/, app/src/logs/
logs/**/debug/  # Matches: logs/debug/, logs/2024/debug/

# ? matches single character
app?.log        # Matches: app1.log, app2.log
                # Not: app.log, app10.log

# [] matches character range
*.[oa]          # Matches: file.o, file.a
                # Not: file.c
log[0-9].txt    # Matches: log0.txt, log1.txt
                # Not: logA.txt

# ! negates pattern (whitelist)
*.log           # Ignore all .log files
!important.log  # But track this one
```

### Directory vs File Patterns

```bash
# Ignore directory (trailing slash)
build/          # Ignores directory named 'build'

# Ignore file or directory
build           # Ignores file OR directory named 'build'

# Ignore only in root
/build/         # Ignores /build/ but not /src/build/

# Ignore in any subdirectory
**/build/       # Ignores build/ anywhere
```

### Advanced Patterns

```bash
# Ignore everything in directory except certain files
logs/*          # Ignore all in logs/
!logs/.gitkeep  # But keep this file (preserves empty dir)

# Ignore nested directories
node_modules/   # Ignores node_modules anywhere
**/node_modules/# Same effect

# Ignore files only in specific location
/TODO           # Ignores /TODO but not /src/TODO

# Multiple extensions
*.{log,tmp,cache}   # Ignores *.log, *.tmp, *.cache

# Complex pattern
src/**/test/*.js    # Ignores .js in any test/ under src/
```

---

## Common .gitignore Patterns

### Node.js / JavaScript

```bash
# Dependencies
node_modules/
npm-debug.log*
yarn-debug.log*
yarn-error.log*
package-lock.json  # If using yarn
yarn.lock          # If using npm

# Environment variables
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# Build output
dist/
build/
out/
.next/
.nuxt/

# Testing
coverage/
.nyc_output/

# Cache
.cache/
.eslintcache
.npm/
.yarn/

# OS
.DS_Store
Thumbs.db

# IDE
.vscode/
.idea/
*.swp
*.swo
*~
```

### Python

```bash
# Byte-compiled / optimized
__pycache__/
*.py[cod]
*$py.class

# Virtual environments
venv/
env/
ENV/
.venv

# Distribution / packaging
build/
dist/
*.egg-info/
*.egg

# Unit test / coverage
.pytest_cache/
.coverage
htmlcov/
.tox/

# Jupyter Notebook
.ipynb_checkpoints

# Environment
.env
.venv

# IDE
.vscode/
.idea/
*.pyc

# Database
*.db
*.sqlite3

# OS
.DS_Store
```

### Java / Maven / Gradle

```bash
# Compiled class files
*.class

# Package files
*.jar
*.war
*.ear

# Maven
target/
pom.xml.tag
pom.xml.releaseBackup
pom.xml.versionsBackup

# Gradle
.gradle/
build/
gradle-app.setting
!gradle-wrapper.jar

# IDE
.idea/
.vscode/
*.iml
*.iws
*.ipr
.classpath
.project
.settings/

# OS
.DS_Store
```

### React / Create React App

```bash
# Dependencies
node_modules/
/.pnp
.pnp.js

# Testing
/coverage

# Production
/build

# Misc
.DS_Store
.env.local
.env.development.local
.env.test.local
.env.production.local

# Logs
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# IDE
.vscode/
.idea/

# Optional npm cache
.npm
```

### Angular

```bash
# Dependencies
node_modules/

# Build output
/dist
/tmp
/out-tsc
/bazel-out

# IDEs
.idea/
.vscode/
*.sublime-workspace

# Miscellaneous
.sass-cache/
connect.lock
coverage/
libpeerconnection.log
testem.log
typings/

# System Files
.DS_Store
Thumbs.db

# Angular specific
.angular/
```

### .NET / C#

```bash
# Build results
[Dd]ebug/
[Rr]elease/
x64/
x86/
[Bb]in/
[Oo]bj/

# Visual Studio
.vs/
*.suo
*.user
*.userosscache
*.sln.docstates

# NuGet
packages/
*.nupkg

# Test results
[Tt]est[Rr]esult*/
[Bb]uild[Ll]og.*

# User-specific files
*.suo
*.user
*.cache
```

### Ruby / Rails

```bash
# Dependencies
vendor/bundle/

# Database
*.sqlite3
*.db

# Logs
*.log
log/

# Temporary files
tmp/
*.swp

# Environment
.env

# IDE
.vscode/
.idea/

# OS
.DS_Store

# Rails specific
/public/system
/coverage/
/spec/tmp
```

---

## Global .gitignore

A global `.gitignore` applies to all repositories on your system.

### Creating Global .gitignore

```bash
# Create global gitignore file
touch ~/.gitignore_global

# Add OS and editor specific files
cat > ~/.gitignore_global << EOF
# OS Files
.DS_Store
.DS_Store?
._*
.Spotlight-V100
.Trashes
ehthumbs.db
Thumbs.db

# Editor files
.vscode/
.idea/
*.swp
*.swo
*~
.project
.classpath
.settings/

# General
*.log
.env
EOF

# Configure Git to use it
git config --global core.excludesfile ~/.gitignore_global
```

### What to Include in Global .gitignore

**Good for global:**
- OS-specific files (.DS_Store, Thumbs.db)
- Editor/IDE files (.vscode/, .idea/)
- Personal tool configs
- Temporary files (*.swp, *~)

**Bad for global (use repo-specific):**
- Build directories (dist/, build/)
- Dependencies (node_modules/)
- Environment files (.env)
- Language-specific files

---

## Working with Already Tracked Files

### Stopping Tracking of Files

If you already committed files that should be ignored:

```bash
# Remove from Git but keep locally
git rm --cached filename

# Remove directory from Git but keep locally
git rm -r --cached directory/

# Commit the removal
git commit -m "Remove ignored files"

# Now .gitignore will work for these files
```

### Common Scenario: Accidentally Committed node_modules

```bash
# 1. Add to .gitignore
echo "node_modules/" >> .gitignore

# 2. Remove from Git (keep locally)
git rm -r --cached node_modules/

# 3. Commit
git add .gitignore
git commit -m "Remove node_modules from tracking"

# 4. Push
git push origin main
```

### Removing Sensitive Data

```bash
# If you accidentally committed secrets:

# 1. Add to .gitignore
echo ".env" >> .gitignore

# 2. Remove from Git
git rm --cached .env

# 3. Commit
git commit -m "Remove .env from tracking"

# 4. IMPORTANT: Rotate the exposed secrets!
# The data is still in Git history
# Consider using git-filter-branch or BFG Repo-Cleaner
# to remove from history
```

---

## Checking .gitignore Rules

### Testing Patterns

```bash
# Check if file would be ignored
git check-ignore -v filename

# Example:
$ git check-ignore -v node_modules/package.json
.gitignore:3:node_modules/    node_modules/package.json

# Check multiple files
git check-ignore -v file1 file2 file3

# See all ignored files
git status --ignored
```

### Debugging .gitignore

```bash
# Show which .gitignore rule ignores a file
git check-ignore -v path/to/file

# No output = file is not ignored
# Output shows: file:line:pattern  path

# Example:
$ git check-ignore -v src/secret.txt
.gitignore:5:*.txt    src/secret.txt
```

### Forcing Add Ignored Files

```bash
# Add ignored file anyway (rare, use carefully)
git add -f ignored-file.txt
git add --force ignored-file.txt
```

---

## .gitignore Templates

### Using GitHub Templates

GitHub provides templates for many languages and frameworks:

```bash
# Visit: https://github.com/github/gitignore

# Common templates:
# - Node.gitignore
# - Python.gitignore
# - Java.gitignore
# - VisualStudio.gitignore
```

### Using gitignore.io

```bash
# Visit: https://www.toptal.com/developers/gitignore

# Or use API:
curl -L https://www.toptal.com/developers/gitignore/api/node,react,vscode > .gitignore

# Multiple technologies:
curl -L https://www.toptal.com/developers/gitignore/api/python,django,pycharm > .gitignore
```

### Template for Full-Stack Project

```bash
# Full-stack project .gitignore

###################
# Dependencies    #
###################
node_modules/
vendor/
__pycache__/

###################
# Build Output    #
###################
dist/
build/
out/
target/
*.class

###################
# Environment     #
###################
.env
.env.local
.env.*.local
*.env

###################
# Logs            #
###################
*.log
logs/
npm-debug.log*
yarn-debug.log*

###################
# Testing         #
###################
coverage/
.nyc_output/
.pytest_cache/

###################
# Database        #
###################
*.db
*.sqlite
*.sqlite3

###################
# IDE / Editor    #
###################
.vscode/
.idea/
*.swp
*.swo
*~
.project
.classpath
.settings/

###################
# OS              #
###################
.DS_Store
.DS_Store?
._*
Thumbs.db
ehthumbs.db
Desktop.ini

###################
# Temporary       #
###################
tmp/
temp/
*.tmp
*.cache
```

---

## Best Practices

### Do's

```bash
✓ Commit .gitignore early in project
✓ Keep .gitignore at repository root
✓ Use comments to organize sections
✓ Be specific rather than broad
✓ Include language-specific patterns
✓ Ignore build artifacts
✓ Ignore dependencies (can be reinstalled)
✓ Ignore environment files with secrets
✓ Ignore IDE/editor specific files
✓ Review .gitignore in code reviews
```

### Don'ts

```bash
✗ Don't ignore source code files
✗ Don't ignore configuration files (unless they have secrets)
✗ Don't ignore files needed to build project
✗ Don't ignore test files
✗ Don't rely only on global .gitignore for project files
✗ Don't ignore files already tracked (remove first)
✗ Don't be too broad (might ignore important files)
✗ Don't commit secrets then just add to .gitignore
```

### Security Considerations

```bash
# Always ignore files containing:
- API keys
- Passwords
- Private keys
- SSL certificates
- Database credentials
- OAuth tokens
- SSH keys

# Example patterns:
.env
*.key
*.pem
credentials.json
secrets.yml
id_rsa
```

---

## Special Cases

### Tracking Empty Directories

Git doesn't track empty directories. Common workaround:

```bash
# Create .gitkeep file
mkdir logs
touch logs/.gitkeep

# In .gitignore:
logs/*
!logs/.gitkeep
```

### Different Ignores for Different Branches

```bash
# Not recommended, but possible:

# On main branch:
echo "config.dev.json" > .gitignore

# On develop branch:
echo "config.prod.json" > .gitignore
```

### Repo-specific Ignores (not committed)

```bash
# .git/info/exclude
# Like .gitignore but not committed

# Edit:
nano .git/info/exclude

# Add patterns:
my-local-file.txt
personal-notes/
```

---

## Common Scenarios

### Starting New Node.js Project

```bash
# Create project
mkdir my-app
cd my-app
npm init -y

# Create .gitignore before first commit
cat > .gitignore << EOF
node_modules/
.env
.env.local
dist/
coverage/
*.log
.DS_Store
.vscode/
EOF

# Initialize Git
git init
git add .
git commit -m "Initial commit"
```

### Cleaning Up After Forgetting .gitignore

```bash
# You committed node_modules and other files

# 1. Create .gitignore
cat > .gitignore << EOF
node_modules/
.env
dist/
*.log
EOF

# 2. Remove from tracking
git rm -r --cached node_modules/
git rm --cached .env
git rm -r --cached dist/

# 3. Add .gitignore
git add .gitignore

# 4. Commit
git commit -m "Add .gitignore and remove tracked files"

# 5. Push
git push origin main
```

### Project with Multiple Technologies

```bash
# Frontend + Backend project

# .gitignore at root
# Backend (Python)
backend/__pycache__/
backend/venv/
backend/*.db

# Frontend (React)
frontend/node_modules/
frontend/build/
frontend/.env.local

# Shared
.env
*.log
.DS_Store
.vscode/
.idea/

# Database
*.sqlite3
data/
```

---

## Summary

| Concept | Description | Example |
|---------|-------------|---------|
| .gitignore | File specifying untracked files | `node_modules/` |
| Pattern | Rule for matching files | `*.log` |
| Wildcard | Match multiple files | `**/*.tmp` |
| Negation | Whitelist files | `!important.log` |
| Global ignore | Applies to all repos | `~/.gitignore_global` |
| --cached | Remove from Git, keep locally | `git rm --cached file` |

### Common Patterns Reference

```bash
# Specific file
secret.txt

# All files with extension
*.log

# Directory
build/

# Any nested directory
**/temp/

# Except file
!important.log

# Root only
/config.json

# Multiple extensions
*.{log,tmp,cache}
```

### Essential Commands

```bash
# Create .gitignore
touch .gitignore

# Check if file is ignored
git check-ignore -v filename

# Remove tracked file
git rm --cached filename

# Show ignored files
git status --ignored

# Force add ignored file
git add -f filename
```

### Quick Start Template

```bash
# Minimal .gitignore for most projects

# Dependencies
node_modules/
vendor/

# Environment
.env
.env.local

# Build
dist/
build/

# IDE
.vscode/
.idea/

# OS
.DS_Store
Thumbs.db

# Logs
*.log
```

## Course Completion

Congratulations! You've completed the Git module. You should now understand:
- Version control concepts
- Git fundamentals and workflow
- Branching and merging strategies
- Remote repositories and collaboration
- Pull requests and code review
- Keeping repositories clean with .gitignore

Continue to [Module 3: Agile Methodology](../../03-agile/) to learn about modern software development practices.
