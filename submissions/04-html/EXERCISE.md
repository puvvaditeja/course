# Exercise: HTML - Portfolio Page

## Objective
Create a personal portfolio webpage using semantic HTML5 elements.

## Requirements

### Required Files
| File | Description |
|------|-------------|
| `portfolio.html` | Main portfolio page |
| `assets/` | Folder for images (optional) |

### Functional Requirements
1. Personal introduction section with name and photo
2. Skills/Technologies section
3. Projects section (at least 3 projects)
4. Contact information section
5. Navigation to jump between sections

### Technical Requirements
- [ ] Valid HTML5 doctype declaration
- [ ] Use semantic tags: `<header>`, `<nav>`, `<main>`, `<section>`, `<article>`, `<footer>`
- [ ] All images have `alt` attributes
- [ ] Include `<meta>` viewport tag for responsiveness
- [ ] Use proper heading hierarchy (h1 -> h2 -> h3)
- [ ] Include at least one list (`<ul>` or `<ol>`)
- [ ] Include at least one table
- [ ] Valid HTML (no errors in W3C validator)

## Page Structure
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Your Name - Portfolio</title>
</head>
<body>
    <header>
        <nav><!-- Navigation links --></nav>
    </header>

    <main>
        <section id="about"><!-- About me --></section>
        <section id="skills"><!-- Skills --></section>
        <section id="projects"><!-- Projects --></section>
        <section id="contact"><!-- Contact --></section>
    </main>

    <footer><!-- Footer content --></footer>
</body>
</html>
```

## Evaluation Criteria
| Criteria | Points |
|----------|--------|
| Valid HTML5 structure | 20 |
| Semantic elements used correctly | 25 |
| All required sections present | 20 |
| Accessibility (alt tags, labels) | 15 |
| Code organization & formatting | 20 |
| **Total** | **100** |

## Submission Checklist
- [ ] File named exactly `portfolio.html`
- [ ] Placed in `04-html/` folder
- [ ] Opens correctly in browser
- [ ] Validated at https://validator.w3.org/
- [ ] Committed and pushed to repository
