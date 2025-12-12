# Git Installation and Configuration

## Overview

Before you can use Git, you need to install it on your system and configure it with your information. This topic covers installation on different platforms and essential configuration steps.

---

## Installing Git

Git is available for all major operating systems. The installation process varies by platform.

### Windows Installation

#### Method 1: Official Git for Windows

1. **Download Git for Windows**
   - Visit: https://git-scm.com/download/win
   - Download the latest version (64-bit recommended)

2. **Run the Installer**
   ```
   - Click through the installer
   - Recommended options:
     ✓ Use Git from Git Bash only (or "Git from command line")
     ✓ Use OpenSSH
     ✓ Use OpenSSL library
     ✓ Checkout Windows-style, commit Unix-style line endings
     ✓ Use MinTTY terminal
     ✓ Enable file system caching
     ✓ Enable Git Credential Manager
   ```

3. **Verify Installation**
   ```bash
   # Open Git Bash or Command Prompt
   git --version
   # Output: git version 2.43.0.windows.1
   ```

#### Method 2: Using Package Manager (Chocolatey)

```bash
# Install using Chocolatey
choco install git

# Verify
git --version
```

#### Method 3: Using Scoop

```bash
# Install using Scoop
scoop install git

# Verify
git --version
```

### macOS Installation

#### Method 1: Using Homebrew (Recommended)

```bash
# Install Homebrew if not already installed
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Git
brew install git

# Verify installation
git --version
# Output: git version 2.43.0
```

#### Method 2: Using Xcode Command Line Tools

```bash
# This installs an older version of Git
xcode-select --install

# Verify
git --version
```

#### Method 3: Download Installer

- Visit: https://git-scm.com/download/mac
- Download and run the installer

### Linux Installation

#### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install Git
sudo apt install git

# Verify installation
git --version
# Output: git version 2.34.1
```

#### Fedora/RHEL/CentOS

```bash
# Fedora
sudo dnf install git

# RHEL/CentOS (older versions)
sudo yum install git

# Verify
git --version
```

#### Arch Linux

```bash
# Install Git
sudo pacman -S git

# Verify
git --version
```

#### From Source (Any Linux)

```bash
# Install dependencies (Debian/Ubuntu)
sudo apt install dh-autoreconf libcurl4-gnutls-dev libexpat1-dev \
  gettext libz-dev libssl-dev

# Download latest version
cd /tmp
wget https://mirrors.edge.kernel.org/pub/software/scm/git/git-2.43.0.tar.gz
tar -zxf git-2.43.0.tar.gz
cd git-2.43.0

# Build and install
make configure
./configure --prefix=/usr
make all
sudo make install

# Verify
git --version
```

---

## Initial Configuration

After installation, configure Git with your identity and preferences.

### Configuration Levels

Git has three configuration levels:

```
System Level    (/etc/gitconfig)
    ↓
Global Level    (~/.gitconfig or ~/.config/git/config)
    ↓
Local Level     (.git/config in repository)

Lower levels override higher levels
```

| Level | Scope | File Location | Flag |
|-------|-------|---------------|------|
| System | All users on system | `/etc/gitconfig` | `--system` |
| Global | All repos for current user | `~/.gitconfig` | `--global` |
| Local | Current repository only | `.git/config` | `--local` |

### Setting Username and Email

This information is included in every commit you make.

```bash
# Set global username
git config --global user.name "Your Name"

# Set global email
git config --global user.email "your.email@example.com"

# Verify settings
git config user.name
git config user.email

# Or view all config
git config --list
```

**Important**: Use the email associated with your GitHub/GitLab account for proper attribution.

### Project-Specific Configuration

Override global settings for specific projects:

```bash
# Navigate to project
cd /path/to/project

# Set local username (only for this repo)
git config --local user.name "Work Name"
git config --local user.email "work.email@company.com"

# Verify local config
git config --local --list
```

Example use case: Different email for work vs. personal projects.

---

## Configuring Your Editor

Git uses your default editor for commit messages and other operations.

### Set Default Editor

```bash
# Use VS Code
git config --global core.editor "code --wait"

# Use Vim
git config --global core.editor "vim"

# Use Nano
git config --global core.editor "nano"

# Use Emacs
git config --global core.editor "emacs"

# Use Sublime Text (macOS)
git config --global core.editor "subl -n -w"

# Use Notepad++ (Windows)
git config --global core.editor "'C:/Program Files/Notepad++/notepad++.exe' -multiInst -notabbar -nosession -noPlugin"
```

### Testing Editor Configuration

```bash
# This will open your configured editor
git config --global --edit

# You can edit your global config here
# Save and close to test
```

---

## Setting Up SSH Keys

SSH keys allow you to connect to Git hosting services securely without entering passwords.

### Why Use SSH Keys?

- No password needed for push/pull operations
- More secure than HTTPS with passwords
- Required by some organizations
- Better automation support

### Generating SSH Keys

#### Check for Existing Keys

```bash
# List existing SSH keys
ls -al ~/.ssh

# Look for files like:
# id_rsa.pub
# id_ed25519.pub
```

#### Generate New SSH Key

```bash
# Generate Ed25519 key (recommended)
ssh-keygen -t ed25519 -C "your.email@example.com"

# For older systems that don't support Ed25519
ssh-keygen -t rsa -b 4096 -C "your.email@example.com"

# Follow prompts:
# Enter file: (press Enter for default)
# Enter passphrase: (optional but recommended)
```

Output:
```
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/user/.ssh/id_ed25519):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/user/.ssh/id_ed25519
Your public key has been saved in /home/user/.ssh/id_ed25519.pub
```

### Adding SSH Key to SSH Agent

```bash
# Start ssh-agent
eval "$(ssh-agent -s)"

# Add your SSH private key
ssh-add ~/.ssh/id_ed25519

# For RSA key
ssh-add ~/.ssh/id_rsa
```

#### macOS Specific Configuration

```bash
# Add to macOS keychain
ssh-add --apple-use-keychain ~/.ssh/id_ed25519

# Configure SSH to use keychain
cat >> ~/.ssh/config << EOF
Host *
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519
EOF
```

### Adding SSH Key to GitHub

```bash
# Copy public key to clipboard (macOS)
pbcopy < ~/.ssh/id_ed25519.pub

# Copy public key to clipboard (Linux with xclip)
xclip -selection clipboard < ~/.ssh/id_ed25519.pub

# Copy public key to clipboard (Windows Git Bash)
cat ~/.ssh/id_ed25519.pub | clip

# Or simply display and copy manually
cat ~/.ssh/id_ed25519.pub
```

Then on GitHub:
1. Go to Settings → SSH and GPG keys
2. Click "New SSH key"
3. Paste your public key
4. Give it a descriptive title
5. Click "Add SSH key"

### Adding SSH Key to GitLab

Similar process:
1. Go to Preferences → SSH Keys
2. Paste your public key
3. Set expiration date (optional)
4. Click "Add key"

### Testing SSH Connection

```bash
# Test GitHub connection
ssh -T git@github.com
# Output: Hi username! You've successfully authenticated...

# Test GitLab connection
ssh -T git@gitlab.com
# Output: Welcome to GitLab, @username!
```

---

## Essential Git Configuration

### Line Ending Configuration

Different operating systems use different line ending characters:
- **Windows**: CRLF (`\r\n`)
- **macOS/Linux**: LF (`\n`)

Configure Git to handle this automatically:

```bash
# Windows
git config --global core.autocrlf true

# macOS/Linux
git config --global core.autocrlf input

# Never change line endings (not recommended)
git config --global core.autocrlf false
```

### Default Branch Name

Modern Git uses `main` as the default branch name:

```bash
# Set default branch name for new repositories
git config --global init.defaultBranch main
```

### Color Output

Enable colored output for better readability:

```bash
# Enable color (usually default)
git config --global color.ui auto

# Disable color
git config --global color.ui false
```

### Pull Strategy

Configure default pull behavior:

```bash
# Merge (default)
git config --global pull.rebase false

# Rebase
git config --global pull.rebase true

# Fast-forward only
git config --global pull.ff only
```

### Push Default

Configure default push behavior:

```bash
# Push current branch to matching remote branch
git config --global push.default simple

# Push only current branch
git config --global push.default current

# Push all matching branches
git config --global push.default matching
```

### Credential Helper

Store credentials to avoid repeated password entry:

```bash
# macOS (uses Keychain)
git config --global credential.helper osxkeychain

# Windows (uses Credential Manager)
git config --global credential.helper manager-core

# Linux (cache for 15 minutes)
git config --global credential.helper cache

# Linux (cache for 1 hour)
git config --global credential.helper 'cache --timeout=3600'

# Linux (store permanently - less secure)
git config --global credential.helper store
```

---

## Advanced Configuration

### Aliases

Create shortcuts for common commands:

```bash
# Basic aliases
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status

# Useful aliases
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual 'log --oneline --graph --all'
git config --global alias.amend 'commit --amend --no-edit'

# Now you can use:
git co main          # instead of git checkout main
git st               # instead of git status
git visual           # pretty log graph
```

### Diff and Merge Tools

Configure external tools for diffs and merges:

```bash
# Set VS Code as diff tool
git config --global diff.tool vscode
git config --global difftool.vscode.cmd 'code --wait --diff $LOCAL $REMOTE'

# Set VS Code as merge tool
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'

# Other popular tools:
# - meld
# - kdiff3
# - vimdiff
# - Beyond Compare
```

### Ignore File Permissions

Useful when working across different OS:

```bash
git config --global core.fileMode false
```

### Ignore Case Changes

```bash
# Ignore case changes (useful on case-insensitive filesystems)
git config --global core.ignorecase true
```

---

## Viewing and Managing Configuration

### View Configuration

```bash
# View all configuration
git config --list

# View configuration with file locations
git config --list --show-origin

# View specific setting
git config user.name
git config user.email

# View global config only
git config --global --list

# View local (repo-specific) config
git config --local --list
```

### Edit Configuration Files

```bash
# Edit global config in default editor
git config --global --edit

# Edit local config
git config --local --edit

# Edit system config
sudo git config --system --edit
```

### Remove Configuration

```bash
# Remove global setting
git config --global --unset user.name

# Remove section
git config --global --remove-section alias
```

---

## Complete Configuration Example

Here's a complete setup for a new developer:

```bash
# Identity
git config --global user.name "John Doe"
git config --global user.email "john.doe@example.com"

# Editor
git config --global core.editor "code --wait"

# Default branch
git config --global init.defaultBranch main

# Line endings (macOS/Linux)
git config --global core.autocrlf input

# Colors
git config --global color.ui auto

# Pull strategy
git config --global pull.rebase false

# Credential helper (macOS)
git config --global credential.helper osxkeychain

# Useful aliases
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"

# Verify configuration
git config --list
```

---

## Configuration File Format

Git configuration files use INI format:

```ini
# ~/.gitconfig

[user]
    name = John Doe
    email = john.doe@example.com

[core]
    editor = code --wait
    autocrlf = input

[init]
    defaultBranch = main

[alias]
    co = checkout
    br = branch
    ci = commit
    st = status
    unstage = reset HEAD --
    last = log -1 HEAD

[color]
    ui = auto

[pull]
    rebase = false

[credential]
    helper = osxkeychain
```

---

## Verifying Installation

After installation and configuration, verify everything works:

```bash
# Check Git version
git --version

# Check user configuration
git config user.name
git config user.email

# Test creating a repository
mkdir test-repo
cd test-repo
git init
echo "# Test" > README.md
git add README.md
git commit -m "Initial commit"
git log

# Test SSH (if configured)
ssh -T git@github.com

# Clean up test
cd ..
rm -rf test-repo
```

---

## Troubleshooting

### Git Command Not Found

```bash
# Check if Git is in PATH
which git

# Add to PATH (macOS/Linux - add to ~/.bashrc or ~/.zshrc)
export PATH="/usr/local/bin:$PATH"

# Windows: Add Git to PATH through System Environment Variables
```

### Permission Denied (SSH)

```bash
# Check SSH key permissions
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub

# Verify ssh-agent is running
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

### CRLF/LF Line Ending Warnings

```bash
# Set appropriate autocrlf setting for your OS
# Windows:
git config --global core.autocrlf true

# macOS/Linux:
git config --global core.autocrlf input
```

---

## Summary

| Task | Command | Purpose |
|------|---------|---------|
| Install | Platform-specific | Get Git on your system |
| Set name | `git config --global user.name "Name"` | Identify commits |
| Set email | `git config --global user.email "email"` | Identify commits |
| Set editor | `git config --global core.editor "editor"` | Edit commit messages |
| Generate SSH | `ssh-keygen -t ed25519` | Secure authentication |
| View config | `git config --list` | See all settings |
| Create alias | `git config --global alias.co checkout` | Shortcuts |

### Key Takeaways

1. **Install Git** appropriate for your operating system
2. **Configure identity** with name and email (required)
3. **Set up SSH keys** for secure, passwordless authentication
4. **Configure editor** for commit messages
5. **Use aliases** to speed up common operations
6. **Check configuration** with `git config --list`

## Next Topic

Continue to [Basic Git Commands](./05-basic-git-commands.md) to start using Git for version control.
