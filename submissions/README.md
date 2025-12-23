# Submission Guidelines

Follow these guidelines to submit your exercises and assignments for review.

---

## Repository Setup

### 1. Create Your Repository
Create a **public** GitHub repository named `fullstack-workshop` (recommended)

### 2. Add Trainer as Collaborator
Add your trainer as a collaborator with **write/edit permission** so they can add reviews directly to your repository.

### 3. Clone your repo and Start Working
```bash
git clone https://github.com/YOUR_USERNAME/fullstack-workshop.git
cd fullstack-workshop
```

---

## Repository Structure

Your repository **must** follow this exact folder structure:

```
fullstack-workshop/
├── 01-linux/
│   ├── log-analyzer.sh           # Exercise
│   └── challenge-01/             # Challenge (subfolder)
├── 02-git/
├── 03-agile/
├── 04-html/
│   ├── portfolio.html            # Exercise
│   └── challenge-01/             # Challenge
├── 05-css/
├── 06-javascript/
│   ├── task-manager.html         # Exercise
│   ├── task-manager.js           # Exercise
│   └── challenge-01/             # Challenge
├── 07-bootstrap/
├── 08-jquery/
├── 09-mysql/
│   ├── schema.sql                # Exercise
│   ├── queries.sql               # Exercise
│   └── challenge-01/             # Challenge
├── 10-java-core/
├── 11-jdbc/
├── 12-testing/
├── 13-logging/
├── 14-spring-framework/
├── 15-spring-boot/
├── 16-spring-mvc/
├── 17-spring-data-jpa/
├── 18-rest-api/
├── 19-microservices/
├── 20-typescript/
├── 21-angular/
├── 22-rxjs/
├── 23-docker/
├── 24-kubernetes/
├── 25-gcp/
├── 26-devops/
├── week-01/                      # Weekly Assignment
├── week-02/
└── README.md
```

---

## Work Types

### 1. Exercises (per topic)

Daily exercises for each topic - small, focused tasks on specific concepts.

| Property | Value |
|----------|-------|
| **Location in course** | `XX-topic/exercises/exercise-01.md` |
| **Submit to** | `XX-topic/` folder in your repo |
| **File names** | Exactly as specified in exercise |

**Example submissions:**
```
your-repo/
├── 01-linux/
│   └── log-analyzer.sh
├── 04-html/
│   └── portfolio.html
└── 06-javascript/
    ├── task-manager.html
    └── task-manager.js
```

---

### 2. Challenges (per topic)

More complex problems that combine multiple concepts within a topic.

| Property | Value |
|----------|-------|
| **Location in course** | `XX-topic/challenges/challenge-01.md` |
| **Submit to** | `XX-topic/challenge-01/` folder in your repo |
| **File names** | As specified in challenge |

**Example submissions:**
```
your-repo/
├── 01-linux/
│   └── challenge-01/
│       └── advanced-log-processor.sh
├── 06-javascript/
│   └── challenge-01/
│       ├── index.html
│       └── app.js
└── 10-java-core/
    └── challenge-01/
        ├── Main.java
        └── (other files)
```

---

### 3. Weekly Assignments

End-of-week assignments covering multiple topics from that week.

| Property | Value |
|----------|-------|
| **Location in course** | `week-XX/assignment.md` |
| **Submit to** | `week-XX/` folder in your repo |

**Example submission:**
```
your-repo/
└── week-01/
    └── (assignment files)
```

---

## Naming Conventions

### Folder Names
| Correct | Incorrect |
|---------|-----------|
| `01-linux` | `1.linux`, `linux`, `Linux` |
| `04-html` | `4-html`, `html`, `HTML` |
| `week-01` | `week1`, `Week-01`, `w1` |

### File Names
| Correct | Incorrect |
|---------|-----------|
| `log-analyzer.sh` | `LogAnalyzer.sh`, `script.sh` |
| `portfolio.html` | `Portfolio.html`, `index.html` |
| `task-manager.js` | `taskManager.js`, `app.js` |

**Always use the exact file name specified in the exercise.**

---

## How to Submit

### Step 1: Create the folder
```bash
mkdir -p 01-linux
```

### Step 2: Add your solution files
```bash
# Copy or create your solution in the folder
cp log-analyzer.sh 01-linux/
```

### Step 3: Commit with a clear message
```bash
git add 01-linux/
git commit -m "Complete 01-linux: log analyzer script"
```

### Step 4: Push to GitHub
```bash
git push origin main
```

---

## Commit Message Format

Use clear, descriptive messages:

```
Complete XX-topic: brief description
```

**Examples:**
- `Complete 01-linux: log analyzer script`
- `Complete 04-html: portfolio page`
- `Complete 06-javascript: task manager app`
- `Complete week-01: weekly assignment`

---

## Before Submitting Checklist

### General
- [ ] Files are in the correct folder (`01-linux/`, `04-html/`, etc.)
- [ ] File names match exactly what's required
- [ ] Code runs without errors
- [ ] Tested locally

### By File Type

#### Shell Scripts (.sh)
- [ ] Has shebang: `#!/bin/bash`
- [ ] Is executable: `chmod +x script.sh`
- [ ] Runs without errors

#### HTML Files (.html)
- [ ] Has `<!DOCTYPE html>`
- [ ] Opens correctly in browser
- [ ] Images have `alt` attributes

#### JavaScript Files (.js)
- [ ] Uses `const`/`let` (not `var`)
- [ ] No errors in browser console

#### SQL Files (.sql)
- [ ] Runs without errors in MySQL
- [ ] Tables have PRIMARY KEYs

#### Java Files (.java)
- [ ] Compiles: `javac *.java`
- [ ] Follows naming conventions

---

## Review Process

1. **Submit** your work by pushing to GitHub
2. **Trainer reviews** and adds `review.md` to your topic folders
3. **Pull** to see feedback:
   ```bash
   git pull origin main
   ```
4. **Check** for `review.md` files in each topic folder
5. **Address feedback** and resubmit if needed

---

## Common Mistakes

### Wrong folder name
```
# WRONG
your-repo/
├── 1.linux/          # Should be 01-linux
├── html/             # Should be 04-html
└── javascript/       # Should be 06-javascript

# CORRECT
your-repo/
├── 01-linux/
├── 04-html/
└── 06-javascript/
```

### Wrong file name
```
# WRONG
01-linux/
├── LogAnalyzer.sh    # Should be log-analyzer.sh
└── script.sh         # Use exact name from exercise

# CORRECT
01-linux/
└── log-analyzer.sh
```

### Files in wrong location
```
# WRONG
your-repo/
├── log-analyzer.sh   # Should be inside 01-linux/
└── portfolio.html    # Should be inside 04-html/

# CORRECT
your-repo/
├── 01-linux/
│   └── log-analyzer.sh
└── 04-html/
    └── portfolio.html
```

---

## Quick Reference

### Exercises
| Topic | Folder | Expected Files |
|-------|--------|----------------|
| Linux | `01-linux/` | `log-analyzer.sh` |
| HTML | `04-html/` | `portfolio.html` |
| JavaScript | `06-javascript/` | `task-manager.html`, `task-manager.js` |
| Bootstrap | `07-bootstrap/` | `dashboard.html` |
| jQuery | `08-jquery/` | `quiz.html`, `quiz.js` |
| MySQL | `09-mysql/` | `schema.sql`, `queries.sql` |
| Java Core | `10-java-core/` | `Book.java`, `Member.java`, `LibraryService.java` |
| Docker | `23-docker/` | `Dockerfile`, `docker-compose.yml` |

### Challenges
| Topic | Folder | Description |
|-------|--------|-------------|
| Linux | `01-linux/challenge-01/` | Advanced shell scripting |
| JavaScript | `06-javascript/challenge-01/` | Complex DOM manipulation |
| Java Core | `10-java-core/challenge-01/` | Design patterns implementation |

### Weekly Assignments
| Week | Folder | Topics Covered |
|------|--------|----------------|
| Week 1 | `week-01/` | Linux, Git, Agile, HTML, CSS |
| Week 2 | `week-02/` | JavaScript, Bootstrap, jQuery |

---

## Getting Help

1. Read the exercise requirements in `XX-topic/exercises/`
2. Check your `review.md` files for feedback
3. Ask during training sessions
4. Reach out to your trainer

---

*Last updated: December 2025*
