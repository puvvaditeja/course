# Team Projects - UI Development

## Overview

This folder contains **10 team project requirements** for building multi-page websites using HTML, CSS, and Bootstrap. Each project is designed for a **team of 4 members**, with **3 features per member** (12 features total per project).

These projects simulate real-world Agile development workflows, emphasizing collaboration through Git and issue tracking.

---

## Project List

| # | Project | Description | Difficulty |
|---|---------|-------------|------------|
| 1 | [Restaurant Website](./project-01-restaurant.md) | Fine dining restaurant with menu, reservations | Medium |
| 2 | [E-Commerce Store](./project-02-ecommerce.md) | Online shopping platform with products, cart | Medium-High |
| 3 | [Tech Startup Landing](./project-03-tech-startup.md) | SaaS product landing with pricing, features | Medium |
| 4 | [Hotel/Resort Website](./project-04-hotel-resort.md) | Hospitality site with rooms, booking | Medium |
| 5 | [Fitness Center](./project-05-fitness-center.md) | Gym website with classes, memberships | Medium |
| 6 | [Online Magazine](./project-06-online-magazine.md) | News/blog platform with articles, categories | Medium-High |
| 7 | [Real Estate Listing](./project-07-real-estate.md) | Property listings with search, details | Medium-High |
| 8 | [Event/Conference](./project-08-event-conference.md) | Conference site with schedule, registration | Medium |
| 9 | [Non-Profit Organization](./project-09-nonprofit.md) | Charity site with donations, programs | Medium |
| 10 | [Educational Portal](./project-10-educational.md) | School/course site with catalog, admissions | Medium |

---

## Team Structure

Each team consists of **4 members** with distinct responsibilities:

| Role | Primary Focus | Features |
|------|---------------|----------|
| **Member A** | Core Structure & Navigation | Home page, Header/Nav, Footer |
| **Member B** | Forms & User Interaction | Contact, Booking/Registration, Newsletter |
| **Member C** | Data Display & Tables | Listings, Comparison tables, Pricing |
| **Member D** | Content & Media | About, Gallery, Team/Testimonials |

---

## Git Workflow Requirements

### Repository Setup

1. **One team member** creates the repository and adds others as collaborators
2. Initialize with a `README.md` and `.gitignore`
3. Create a `main` branch (protected - no direct commits)
4. Create a `develop` branch for integration

### Branch Strategy

```
main (protected)
  └── develop (integration branch)
        ├── feature/home-page
        ├── feature/header-nav
        ├── feature/footer
        ├── feature/menu-page
        └── ... (one branch per feature)
```

### Branch Naming Convention

```
feature/<feature-name>     # New features
bugfix/<bug-description>   # Bug fixes
hotfix/<issue-number>      # Urgent fixes
```

### Workflow Steps

1. **Create Issue** - Before starting work, create a GitHub issue
2. **Create Branch** - Branch from `develop` using naming convention
3. **Commit Often** - Small, meaningful commits with clear messages
4. **Push & Create PR** - Push branch and create Pull Request to `develop`
5. **Code Review** - At least 1 team member must review and approve
6. **Merge** - Squash and merge after approval
7. **Close Issue** - Link PR to issue for automatic closure

### Commit Message Format

```
<type>: <short description>

[optional body]

[optional footer - issue reference]
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `style`: CSS/styling changes
- `refactor`: Code restructuring
- `docs`: Documentation

**Examples:**
```
feat: add responsive navigation menu

- Implement hamburger menu for mobile
- Add dropdown for sub-navigation
- Style active nav states

Closes #12
```

---

## GitHub Issues & Project Board

### Issue Templates

Each feature should have an issue with:

```markdown
## User Story
As a [user type], I want to [action] so that [benefit].

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Technical Notes
- Bootstrap components to use
- CSS considerations
- Accessibility requirements

## Assigned To
@team-member-username

## Labels
- `feature`, `frontend`, `priority:high`
```

### Project Board Columns

Set up a GitHub Project board with:

| Column | Description |
|--------|-------------|
| **Backlog** | All user stories not yet started |
| **Sprint** | Current sprint items |
| **In Progress** | Actively being worked on |
| **In Review** | PR created, awaiting review |
| **Done** | Merged and completed |

### Labels to Create

- `feature` - New functionality
- `bug` - Something isn't working
- `enhancement` - Improvement to existing feature
- `documentation` - Documentation updates
- `priority:high` / `priority:medium` / `priority:low`
- `member-a` / `member-b` / `member-c` / `member-d`

---

## Sprint Planning

### Suggested Sprint Structure

| Sprint | Focus | Duration |
|--------|-------|----------|
| **Sprint 1** | Project setup, Header, Footer, Home page structure | 2-3 days |
| **Sprint 2** | Core pages (About, Contact, main content pages) | 2-3 days |
| **Sprint 3** | Forms, Tables, Interactive components | 2-3 days |
| **Sprint 4** | Polish, Responsive testing, Final integration | 2-3 days |

### Daily Standup Questions

Each team member should answer:
1. What did I complete yesterday?
2. What will I work on today?
3. Do I have any blockers?

---

## Technical Requirements

### HTML Standards
- Valid HTML5 with proper DOCTYPE
- Semantic elements (`header`, `nav`, `main`, `section`, `article`, `footer`)
- Accessible forms with labels and ARIA attributes
- Proper heading hierarchy (h1 > h2 > h3)
- Alt text for all images

### CSS Standards
- External stylesheets (no inline styles except for Bootstrap utilities)
- Mobile-first responsive design
- Consistent naming convention (BEM recommended)
- CSS custom properties for theming (colors, fonts)

### Bootstrap Requirements
- Use Bootstrap 5.x via CDN
- Utilize grid system for layouts
- Use Bootstrap components (navbar, cards, forms, tables, modals)
- Customize with your own CSS (don't rely solely on Bootstrap defaults)

### File Structure

```
project-name/
├── index.html
├── pages/
│   ├── about.html
│   ├── contact.html
│   └── [other pages].html
├── css/
│   └── styles.css
├── images/
│   └── [image files]
├── js/
│   └── script.js (optional for interactivity)
└── README.md
```

---

## Code Review Checklist

Before approving a PR, reviewers should verify:

- [ ] HTML validates without errors
- [ ] Semantic HTML elements used appropriately
- [ ] All images have alt text
- [ ] Forms have proper labels
- [ ] Page is responsive (mobile, tablet, desktop)
- [ ] Bootstrap grid used correctly
- [ ] CSS follows project conventions
- [ ] No broken links or missing images
- [ ] Accessibility considerations addressed
- [ ] Code is properly indented and formatted

---

## Future Backend Integration

These projects are designed with future backend integration in mind:

### Forms Ready for Backend
- All forms use proper `name` attributes
- Form `action` attributes set to `#` (placeholder)
- Form `method` set appropriately (POST for submissions)
- Input validation attributes in place

### Data Display Ready for Dynamic Content
- Product/item cards structured for easy templating
- Tables designed for dynamic data population
- Lists structured with consistent patterns

### API-Ready Structure
- Consistent ID and class naming
- Data attributes for JavaScript hooks
- Modular component structure

---

## Evaluation Criteria

Projects will be evaluated on:

| Criteria | Weight |
|----------|--------|
| **Functionality** - All features implemented | 25% |
| **Code Quality** - Clean, semantic, valid HTML/CSS | 20% |
| **Responsiveness** - Works on all device sizes | 20% |
| **Git Workflow** - Proper branching, PRs, commits | 20% |
| **Collaboration** - Issues, reviews, team communication | 15% |

---

## Getting Started

1. **Form your team** of 4 members
2. **Choose a project** from the list above
3. **Create a GitHub repository** with proper setup
4. **Set up the project board** with issues for all 12 features
5. **Assign features** to team members (3 each)
6. **Start Sprint 1** - Begin with setup and core structure
7. **Daily standups** - Communicate progress and blockers
8. **Submit** - Final PR to main when all features complete

Good luck and happy coding!
