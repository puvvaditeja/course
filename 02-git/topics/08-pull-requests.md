# Pull Requests

## Overview

Pull requests (PRs) are a mechanism for developers to notify team members that they have completed a feature and want to merge their code into another branch. They are fundamental to modern collaborative development and code review processes.

---

## What are Pull Requests?

### Definition

A pull request is a request to merge changes from one branch into another, typically accompanied by:
- Description of changes
- Discussion and code review
- Automated tests
- Approval workflow

### Pull Requests vs Merge Requests

- **Pull Request (PR)**: GitHub, Bitbucket terminology
- **Merge Request (MR)**: GitLab terminology
- Same concept, different names

### Why Pull Requests Matter

```
Traditional Workflow:
Developer → Direct Commit to Main → Production
(Risky, no review, no discussion)

Pull Request Workflow:
Developer → Feature Branch → Pull Request → Code Review → Merge → Production
(Safe, collaborative, quality-controlled)
```

**Benefits:**
- Code review and quality control
- Knowledge sharing across team
- Documentation of changes
- Discussion platform
- Automated testing integration
- Approval workflows
- Audit trail

---

## The Pull Request Workflow

### Complete PR Lifecycle

```
1. Create Feature Branch
   ↓
2. Make Changes and Commit
   ↓
3. Push Branch to Remote
   ↓
4. Open Pull Request
   ↓
5. Code Review and Discussion
   ↓
6. Address Feedback (if needed)
   ↓
7. Automated Tests Pass
   ↓
8. Approval from Reviewers
   ↓
9. Merge Pull Request
   ↓
10. Delete Feature Branch
```

### Step-by-Step Example

```bash
# 1. Update main branch
git checkout main
git pull origin main

# 2. Create feature branch
git checkout -b feature/user-authentication

# 3. Make changes
# (edit files)
git add .
git commit -m "Add user authentication"

# 4. Push to remote
git push -u origin feature/user-authentication

# 5. Create PR on GitHub/GitLab
# (via web interface)

# 6. Address review comments
# (edit files based on feedback)
git add .
git commit -m "Address review comments"
git push

# 7. After approval and merge, clean up
git checkout main
git pull origin main
git branch -d feature/user-authentication
git push origin --delete feature/user-authentication
```

---

## Creating Pull Requests

### Via GitHub Web Interface

```
1. Navigate to repository on GitHub
2. Click "Pull requests" tab
3. Click "New pull request"
4. Select base branch (e.g., main)
5. Select compare branch (your feature branch)
6. Review changes
7. Click "Create pull request"
8. Fill in title and description
9. Add reviewers
10. Add labels, milestone, assignees (optional)
11. Click "Create pull request"
```

### Via GitHub CLI

```bash
# Install GitHub CLI
brew install gh  # macOS
# Or download from https://cli.github.com/

# Authenticate
gh auth login

# Create PR from current branch
gh pr create

# Create PR with options
gh pr create --title "Add user authentication" \
             --body "Implements JWT-based authentication" \
             --reviewer username \
             --assignee @me \
             --label enhancement

# Create draft PR
gh pr create --draft

# Create PR to specific branch
gh pr create --base develop --head feature-branch
```

### Via GitLab Web Interface

```
1. Navigate to repository on GitLab
2. Click "Merge requests"
3. Click "New merge request"
4. Select source branch
5. Select target branch
6. Click "Compare branches and continue"
7. Fill in title and description
8. Assign reviewers
9. Configure options (delete source branch, squash commits)
10. Click "Create merge request"
```

### PR Title and Description Best Practices

#### Good PR Title

```
✓ Add user authentication with JWT
✓ Fix null pointer exception in UserService
✓ Update API documentation for /users endpoint
✓ Refactor database connection pooling
```

#### Bad PR Title

```
✗ Updates
✗ Fix
✗ Changes
✗ asdfasdf
```

#### Good PR Description Template

```markdown
## Summary
Brief overview of what this PR does.

## Changes Made
- Implemented JWT authentication
- Added login and registration endpoints
- Updated user model with password hashing
- Added authentication middleware

## Testing
- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Manual testing completed
- [ ] Tested on staging environment

## Related Issues
Fixes #123
Related to #456

## Screenshots (if applicable)
[Add screenshots of UI changes]

## Checklist
- [ ] Code follows style guidelines
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] No console errors
- [ ] Backward compatible
```

---

## Code Review Process

### Reviewer Responsibilities

**What to Review:**
- Code quality and readability
- Logic and algorithm correctness
- Security vulnerabilities
- Performance implications
- Test coverage
- Documentation
- Code style consistency
- Edge cases handling

**How to Review:**

```markdown
✓ Good Review Comments:

"This function could be simplified using Array.map() instead of
the for loop. Would improve readability."

"Consider adding validation here for null/undefined values to
prevent runtime errors."

"Great use of the factory pattern here! Very clean."

"This query might cause N+1 problem with large datasets.
Consider using JOIN or eager loading."


✗ Poor Review Comments:

"This is wrong."  (Not helpful)

"Why did you do it this way???" (Confrontational)

"I would never write it like this." (Unhelpful)
```

### Responding to Reviews

#### As a Reviewer

```bash
# On GitHub PR page:
1. Go to "Files changed" tab
2. Hover over line of code
3. Click "+" to add comment
4. Write constructive feedback
5. Click "Add single comment" or "Start a review"

# Types of comments:
- Comment: Simple note
- Approve: Changes look good
- Request changes: Issues must be addressed
```

#### As PR Author

```bash
# Respond to feedback constructively
"Good catch! I'll add validation here."

"I chose this approach because... but I'm open to alternatives."

"Fixed in commit abc123"

# Make requested changes
git add .
git commit -m "Address review feedback: add validation"
git push

# Resolve conversations when addressed
(Click "Resolve conversation" on GitHub)
```

### Review Checklist

```markdown
## Code Review Checklist

### Functionality
- [ ] Code does what it's supposed to do
- [ ] Edge cases are handled
- [ ] Error handling is appropriate
- [ ] No obvious bugs

### Code Quality
- [ ] Code is readable and maintainable
- [ ] Functions are focused and single-purpose
- [ ] No code duplication (DRY principle)
- [ ] Naming is clear and consistent
- [ ] Comments explain "why", not "what"

### Testing
- [ ] Tests are included
- [ ] Tests cover edge cases
- [ ] Tests are meaningful
- [ ] All tests pass

### Security
- [ ] No hardcoded secrets
- [ ] Input is validated
- [ ] Authentication/authorization is correct
- [ ] No SQL injection vulnerabilities
- [ ] No XSS vulnerabilities

### Performance
- [ ] No obvious performance issues
- [ ] Database queries are optimized
- [ ] No memory leaks
- [ ] Appropriate use of caching

### Documentation
- [ ] README updated (if needed)
- [ ] API documentation updated
- [ ] Inline comments where necessary
- [ ] Breaking changes documented
```

---

## Merging Pull Requests

### Merge Strategies

#### 1. Merge Commit (Default)

```
Before:
main:     A---B---C
               \
feature:        D---E

After merge:
main:     A---B---C-------F (merge commit)
               \         /
feature:        D-------E
```

**Pros:**
- Preserves complete history
- Shows when feature was integrated
- Easy to revert entire feature

**Cons:**
- Cluttered history with merge commits
- Harder to read linear history

```bash
# Command line merge
git checkout main
git merge --no-ff feature-branch
git push origin main
```

#### 2. Squash and Merge

```
Before:
main:     A---B---C
               \
feature:        D---E---F

After squash:
main:     A---B---C---G (single commit containing D+E+F)
```

**Pros:**
- Clean linear history
- Single commit per feature
- Easy to revert feature

**Cons:**
- Loses individual commit history
- Harder to see development process

```bash
# Command line squash
git checkout main
git merge --squash feature-branch
git commit -m "Add feature: user authentication"
git push origin main
```

#### 3. Rebase and Merge

```
Before:
main:     A---B---C
               \
feature:        D---E

After rebase:
main:     A---B---C---D'---E'
```

**Pros:**
- Clean linear history
- Preserves individual commits
- No merge commits

**Cons:**
- Rewrites commit history
- More complex for beginners

```bash
# Rebase feature branch
git checkout feature-branch
git rebase main

# Merge (fast-forward)
git checkout main
git merge feature-branch
git push origin main
```

### Choosing a Strategy

| Use Case | Strategy |
|----------|----------|
| Small feature (1-2 commits) | Squash and merge |
| Large feature (many commits) | Merge commit |
| Want linear history | Rebase and merge |
| Open source project | Merge commit (preserves contributors) |
| Internal team project | Squash and merge (cleaner history) |

### Merge via GitHub

```
1. Navigate to pull request
2. Ensure all checks pass
3. Ensure approvals received
4. Click "Merge pull request" dropdown
5. Choose merge strategy:
   - Create a merge commit
   - Squash and merge
   - Rebase and merge
6. Click "Confirm merge"
7. Optionally delete branch
```

### Auto-Merge

```
On GitHub:
1. Open pull request
2. Click "Enable auto-merge"
3. Select merge method
4. PR will auto-merge when:
   - All checks pass
   - Required approvals received
   - No conflicts
```

---

## Handling Conflicts in Pull Requests

### When Conflicts Occur

```
main:     A---B---C---D (someone else's changes)
               \
feature:        E---F (your changes)

Conflict: Both changed same file/lines
```

### Resolving Conflicts

#### Method 1: Merge main into feature branch

```bash
# Update feature branch with latest main
git checkout feature-branch
git fetch origin
git merge origin/main

# Resolve conflicts
# Edit conflicted files, remove markers

# Stage resolved files
git add .

# Complete merge
git commit -m "Resolve merge conflicts with main"

# Push
git push origin feature-branch
```

#### Method 2: Rebase feature onto main

```bash
# Rebase feature branch
git checkout feature-branch
git fetch origin
git rebase origin/main

# Resolve conflicts for each commit
# Edit files, remove markers

# Stage resolved files
git add .

# Continue rebase
git rebase --continue

# Force push (rebase rewrites history)
git push --force-with-lease origin feature-branch
```

#### Method 3: Resolve on GitHub

```
Some platforms (GitHub) allow resolving simple conflicts in web editor:

1. Click "Resolve conflicts" button on PR
2. Edit files in web editor
3. Remove conflict markers
4. Click "Mark as resolved"
5. Click "Commit merge"
```

---

## Pull Request Best Practices

### For Authors

**Before Creating PR:**
- Pull latest changes from base branch
- Ensure all tests pass locally
- Review your own changes first
- Write clear commit messages
- Update documentation

**When Creating PR:**
- Write descriptive title and description
- Reference related issues
- Add appropriate labels
- Request specific reviewers
- Keep PR focused (one feature/fix)
- Include tests

**During Review:**
- Respond promptly to feedback
- Be open to suggestions
- Explain your reasoning
- Push additional commits to address feedback
- Mark conversations as resolved

### For Reviewers

**When Reviewing:**
- Review promptly (within 24-48 hours)
- Be constructive and respectful
- Explain reasoning for suggestions
- Approve when satisfied
- Request changes if issues found

**Review Guidelines:**
- Focus on important issues (not nitpicks)
- Suggest solutions, not just problems
- Praise good code
- Consider context and trade-offs
- Test the code if possible

### PR Size Guidelines

```
Ideal PR size:
- 200-400 lines of code
- Review time: 30-60 minutes
- Focus: Single feature or fix

Too large:
- > 1000 lines of code
- Review time: > 2 hours
- Scope: Multiple features
- Solution: Break into smaller PRs

Too small:
- < 10 lines (unless trivial fix)
- May lack context
- Could be combined with related changes
```

---

## Advanced PR Features

### Draft Pull Requests

```
Use draft PRs for:
- Work in progress
- Early feedback
- Visibility of ongoing work

On GitHub:
1. Create pull request
2. Select "Create draft pull request" from dropdown
3. Mark "Ready for review" when complete
```

### PR Templates

Create `.github/pull_request_template.md`:

```markdown
## Description
<!-- Describe your changes -->

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
<!-- Describe testing performed -->

## Checklist
- [ ] My code follows the style guidelines
- [ ] I have performed a self-review
- [ ] I have commented my code where needed
- [ ] I have updated the documentation
- [ ] My changes generate no new warnings
- [ ] I have added tests that prove my fix/feature works
- [ ] New and existing tests pass locally
```

### Required Status Checks

Require certain checks before merging:

```
On GitHub:
Settings → Branches → Branch protection rules

Configure:
- Require pull request reviews before merging
- Require status checks to pass
- Require conversation resolution
- Require signed commits
- Require linear history
```

### Code Owners

Create `.github/CODEOWNERS`:

```
# Backend team owns all backend code
/backend/ @backend-team

# Frontend team owns all frontend code
/frontend/ @frontend-team

# DevOps owns CI/CD
/.github/workflows/ @devops-team

# Security team reviews authentication
/auth/ @security-team
```

### Linking Issues

```markdown
# In PR description or commit message:

Closes #123
Fixes #456
Resolves #789

Related to #234
Part of #567
```

---

## CI/CD Integration

### GitHub Actions Example

`.github/workflows/pr-checks.yml`:

```yaml
name: PR Checks

on:
  pull_request:
    branches: [ main, develop ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        run: npm test

      - name: Run linter
        run: npm run lint

      - name: Build
        run: npm run build
```

### Status Checks in PRs

```
Pull Request Status Indicators:
✓ All checks have passed
✗ Some checks failed
⦿ Checks are running
- No checks configured

Common checks:
- Unit tests
- Integration tests
- Code coverage
- Linting
- Build
- Security scanning
- Performance tests
```

---

## Practical Examples

### Complete PR Workflow Example

```bash
# 1. Start new feature
git checkout main
git pull origin main
git checkout -b feature/payment-integration

# 2. Develop feature
echo "payment code" > payment.js
git add payment.js
git commit -m "Add payment processing"

echo "more payment code" >> payment.js
git add payment.js
git commit -m "Add refund functionality"

# 3. Push branch
git push -u origin feature/payment-integration

# 4. Create PR via GitHub CLI
gh pr create \
  --title "Add payment integration" \
  --body "Implements payment processing with Stripe" \
  --reviewer @teammate \
  --label enhancement

# 5. Address review feedback
echo "fix based on review" >> payment.js
git add payment.js
git commit -m "Address review: add error handling"
git push

# 6. Update from main (handle conflicts if any)
git fetch origin
git merge origin/main
# Resolve conflicts if needed
git push

# 7. After merge, clean up
git checkout main
git pull origin main
git branch -d feature/payment-integration
```

---

## Summary

| Concept | Description |
|---------|-------------|
| Pull Request | Request to merge changes with code review |
| Draft PR | Work-in-progress pull request |
| Code Review | Team evaluation of code changes |
| Merge Strategies | Merge commit, squash, rebase |
| Status Checks | Automated tests/builds on PRs |
| CODEOWNERS | Automatic reviewer assignment |
| PR Template | Standardized PR description format |

### PR Workflow Summary

```
1. Create feature branch
2. Make changes and commit
3. Push to remote
4. Open pull request
5. Code review and discussion
6. Address feedback
7. Merge after approval
8. Delete branch
```

### Best Practices Checklist

```
Before Creating PR:
- [ ] Pull latest changes
- [ ] Tests pass locally
- [ ] Self-review changes
- [ ] Update documentation

Creating PR:
- [ ] Descriptive title
- [ ] Detailed description
- [ ] Link related issues
- [ ] Add reviewers
- [ ] Small, focused changes

During Review:
- [ ] Respond to feedback
- [ ] Explain decisions
- [ ] Push fixes promptly
- [ ] Resolve conversations

After Merge:
- [ ] Delete feature branch
- [ ] Update local main
- [ ] Verify deployment
```

## Next Topic

Continue to [.gitignore](./09-gitignore.md) to learn about ignoring files and keeping your repository clean.
