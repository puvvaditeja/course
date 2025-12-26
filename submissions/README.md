# Submission Guidelines

Follow these guidelines to submit your exercises and challenges for review.

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
│   └── portfolio.html            # Exercise
├── 05-css/
├── 06-javascript/
│   ├── objects.js                # Exercise 07
│   ├── dom-events.html           # Exercise 08
│   ├── dom-events.js             # Exercise 08
│   └── challenge-01/             # Challenge
├── 07-bootstrap/
├── 08-jquery/
├── 09-mysql/
│   ├── schema.sql                # Exercise
│   ├── queries.sql               # Exercise
│   └── challenge-01/             # Challenge
├── 10-java-core/
│   └── challenge-01/             # Challenge
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
│   └── challenge-01/             # Challenge
├── 24-kubernetes/
├── 25-gcp/
├── 26-devops/
└── README.md
```

---

## Work Types

### 1. Exercises (per topic)

Focused tasks on specific concepts. Each exercise contains multiple **Problems** to solve.

| Property | Value |
|----------|-------|
| **Location in course** | `submissions/XX-topic/EXERCISE.md` or `submissions/XX-topic/exercises/` |
| **Submit to** | `XX-topic/` folder in your repo |
| **File names** | Exactly as specified in exercise |

**Topics with multiple exercises:**
- `04-html/exercises/` - 6 exercises (exercise-01 to exercise-06)
- `06-javascript/exercises/` - 8 exercises (exercise-01 to exercise-08)

**Example submissions:**
```
your-repo/
├── 01-linux/
│   └── log-analyzer.sh
├── 04-html/
│   └── portfolio.html
└── 06-javascript/
    ├── objects.js
    ├── dom-events.html
    └── dom-events.js
```

---

### 2. Challenges (per topic)

More complex problems that combine multiple concepts within a topic.

| Property | Value |
|----------|-------|
| **Location in course** | `submissions/XX-topic/challenges/CODE-CHALLENGE.md` |
| **Submit to** | `XX-topic/challenge-01/` folder in your repo |
| **File names** | As specified in challenge |

**Topics with challenges:**
- 01-linux
- 06-javascript
- 09-mysql
- 10-java-core
- 23-docker

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

## Naming Conventions

### Folder Names
| Correct | Incorrect |
|---------|-----------|
| `01-linux` | `1.linux`, `linux`, `Linux` |
| `04-html` | `4-html`, `html`, `HTML` |
| `challenge-01` | `challenge1`, `Challenge-01` |

### File Names
| Correct | Incorrect |
|---------|-----------|
| `log-analyzer.sh` | `LogAnalyzer.sh`, `script.sh` |
| `portfolio.html` | `Portfolio.html`, `index.html` |
| `objects.js` | `Objects.js`, `exercise-07.js` |

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
- `Complete 06-javascript: objects exercise`
- `Complete 06-javascript/challenge-01: DOM project`

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

## Quick Reference

### Exercises by Topic
| Topic | Folder | Expected Files |
|-------|--------|----------------|
| Linux | `01-linux/` | `log-analyzer.sh` |
| HTML | `04-html/` | `portfolio.html` |
| CSS | `05-css/` | `styles.css` |
| JavaScript | `06-javascript/` | `objects.js`, `dom-events.html`, `dom-events.js` |
| Bootstrap | `07-bootstrap/` | `dashboard.html` |
| jQuery | `08-jquery/` | `quiz.html`, `quiz.js` |
| MySQL | `09-mysql/` | `schema.sql`, `queries.sql` |
| Java Core | `10-java-core/` | `Book.java`, `Member.java`, `LibraryService.java` |
| Docker | `23-docker/` | `Dockerfile`, `docker-compose.yml` |

### Challenges by Topic
| Topic | Folder | Description |
|-------|--------|-------------|
| Linux | `01-linux/challenge-01/` | Advanced shell scripting |
| JavaScript | `06-javascript/challenge-01/` | Complex application |
| MySQL | `09-mysql/challenge-01/` | Advanced queries |
| Java Core | `10-java-core/challenge-01/` | Design patterns |
| Docker | `23-docker/challenge-01/` | Multi-container orchestration |

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
06-javascript/
├── Objects.js        # Should be objects.js
└── exercise-07.js    # Use exact name from exercise

# CORRECT
06-javascript/
└── objects.js
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

## Getting Help

1. Read the exercise requirements in `submissions/XX-topic/`
2. Check your `review.md` files for feedback
3. Ask during training sessions
4. Reach out to your trainer

---

*Last updated: December 2025*
