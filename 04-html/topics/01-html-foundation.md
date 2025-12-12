# HTML Foundation

## Overview of HTML

HTML (HyperText Markup Language) is the standard markup language for creating web pages and web applications. It describes the structure and content of web documents using a system of tags and attributes.

### What is HTML?

HTML is not a programming language - it's a markup language that tells web browsers how to structure and display content. HTML documents consist of a series of elements (represented by tags) that label different parts of the content.

### Key Characteristics

- **Declarative**: You describe what you want, not how to achieve it
- **Tag-based**: Uses opening and closing tags to define elements
- **Hierarchical**: Elements nest within other elements to create structure
- **Platform-independent**: Works across all browsers and operating systems
- **Text-based**: Human-readable plain text format

### HTML Document Structure

Every HTML document follows a basic structure:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Page Title</title>
</head>
<body>
    <!-- Content goes here -->
</body>
</html>
```

**Structure Breakdown:**

| Element | Purpose |
|---------|---------|
| `<!DOCTYPE html>` | Declares this as an HTML5 document |
| `<html>` | Root element containing all other elements |
| `<head>` | Contains metadata, title, links to CSS/scripts |
| `<meta>` | Defines metadata (character set, viewport, etc.) |
| `<title>` | Sets the page title (shown in browser tab) |
| `<body>` | Contains the visible page content |

---

## HTML5 Features

HTML5 is the latest version of HTML, introducing many new features and improvements over HTML4.

### New Semantic Elements

HTML5 introduced semantic elements that clearly describe their meaning:

```html
<header>
    <nav>
        <ul>
            <li><a href="#home">Home</a></li>
            <li><a href="#about">About</a></li>
        </ul>
    </nav>
</header>

<main>
    <article>
        <h1>Article Title</h1>
        <section>
            <h2>Section Heading</h2>
            <p>Content here...</p>
        </section>
    </article>

    <aside>
        <h2>Related Links</h2>
    </aside>
</main>

<footer>
    <p>&copy; 2025 Company Name</p>
</footer>
```

**Semantic Elements:**

| Element | Purpose |
|---------|---------|
| `<header>` | Introductory content or navigation links |
| `<nav>` | Navigation links |
| `<main>` | Main content of the document |
| `<article>` | Self-contained, independent content |
| `<section>` | Thematic grouping of content |
| `<aside>` | Sidebar content, tangentially related |
| `<footer>` | Footer for document or section |
| `<figure>` | Self-contained content (images, diagrams) |
| `<figcaption>` | Caption for figure element |

### New Form Input Types

HTML5 added specialized input types for better user experience:

```html
<input type="email" placeholder="Enter email">
<input type="url" placeholder="Enter website URL">
<input type="tel" placeholder="Phone number">
<input type="number" min="1" max="100">
<input type="range" min="0" max="100">
<input type="date">
<input type="time">
<input type="color">
<input type="search" placeholder="Search...">
```

### Multimedia Support

HTML5 provides native support for audio and video without plugins:

```html
<!-- Video -->
<video width="640" height="360" controls>
    <source src="movie.mp4" type="video/mp4">
    <source src="movie.webm" type="video/webm">
    Your browser does not support the video tag.
</video>

<!-- Audio -->
<audio controls>
    <source src="audio.mp3" type="audio/mpeg">
    <source src="audio.ogg" type="audio/ogg">
    Your browser does not support the audio tag.
</audio>
```

### Graphics and Visualization

```html
<!-- Canvas for drawing graphics via JavaScript -->
<canvas id="myCanvas" width="400" height="400"></canvas>

<!-- SVG for scalable vector graphics -->
<svg width="100" height="100">
    <circle cx="50" cy="50" r="40" fill="blue" />
</svg>
```

### Key HTML5 Features Summary

| Feature | Description |
|---------|-------------|
| Semantic Elements | Clear, meaningful tags for page structure |
| Form Enhancements | New input types, validation, placeholders |
| Multimedia | Native audio/video support |
| Canvas API | 2D drawing and graphics |
| SVG Support | Scalable vector graphics |
| Web Storage | localStorage and sessionStorage |
| Geolocation | Access user's geographical location |
| Offline Support | Application cache for offline access |
| Web Workers | Background JavaScript processing |

---

## HTML Tags

Tags are the fundamental building blocks of HTML. They define how content should be displayed and structured.

### Tag Syntax

```html
<!-- Opening and closing tags -->
<tagname>Content goes here</tagname>

<!-- Self-closing tags (void elements) -->
<img src="image.jpg" alt="Description">
<br>
<hr>
<input type="text">
```

### Common HTML Tags

#### Text Content Tags

```html
<!-- Headings (h1 is most important, h6 is least) -->
<h1>Main Heading</h1>
<h2>Subheading</h2>
<h3>Sub-subheading</h3>

<!-- Paragraphs -->
<p>This is a paragraph of text.</p>

<!-- Line break and horizontal rule -->
<br>
<hr>

<!-- Text formatting -->
<strong>Bold/Important text</strong>
<em>Italic/Emphasized text</em>
<mark>Highlighted text</mark>
<small>Smaller text</small>
<del>Deleted text</del>
<ins>Inserted text</ins>
<sub>Subscript</sub>
<sup>Superscript</sup>
```

#### Lists

```html
<!-- Unordered list -->
<ul>
    <li>Item 1</li>
    <li>Item 2</li>
    <li>Item 3</li>
</ul>

<!-- Ordered list -->
<ol>
    <li>First item</li>
    <li>Second item</li>
    <li>Third item</li>
</ol>

<!-- Description list -->
<dl>
    <dt>Term</dt>
    <dd>Definition of the term</dd>
    <dt>Another term</dt>
    <dd>Another definition</dd>
</dl>
```

#### Links and Images

```html
<!-- Link -->
<a href="https://example.com" target="_blank" rel="noopener">Visit Example</a>

<!-- Image -->
<img src="path/to/image.jpg" alt="Description of image" width="300" height="200">

<!-- Image with link -->
<a href="https://example.com">
    <img src="logo.png" alt="Company Logo">
</a>
```

#### Tables

```html
<table border="1">
    <thead>
        <tr>
            <th>Header 1</th>
            <th>Header 2</th>
            <th>Header 3</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Row 1, Col 1</td>
            <td>Row 1, Col 2</td>
            <td>Row 1, Col 3</td>
        </tr>
        <tr>
            <td>Row 2, Col 1</td>
            <td>Row 2, Col 2</td>
            <td>Row 2, Col 3</td>
        </tr>
    </tbody>
</table>
```

#### Divisions and Containers

```html
<!-- Generic block container -->
<div class="container">
    <p>Content inside a div</p>
</div>

<!-- Generic inline container -->
<p>This is <span class="highlight">highlighted text</span> in a paragraph.</p>
```

---

## Elements and Attributes

### HTML Elements

An HTML element consists of a start tag, content, and an end tag:

```html
<tagname attribute="value">Content</tagname>
```

**Element Components:**
- **Start tag**: `<tagname>`
- **Attributes**: `attribute="value"`
- **Content**: Text or other nested elements
- **End tag**: `</tagname>`

### Void Elements

Some elements don't have closing tags or content:

```html
<img src="image.jpg" alt="Description">
<br>
<hr>
<input type="text" name="username">
<meta charset="UTF-8">
<link rel="stylesheet" href="style.css">
```

### HTML Attributes

Attributes provide additional information about elements. They are always specified in the start tag.

#### Common Global Attributes

These attributes can be used on any HTML element:

| Attribute | Purpose | Example |
|-----------|---------|---------|
| `id` | Unique identifier | `<div id="header">` |
| `class` | CSS class name(s) | `<p class="text-large">` |
| `style` | Inline CSS styles | `<p style="color: red;">` |
| `title` | Tooltip text | `<abbr title="HyperText Markup Language">HTML</abbr>` |
| `data-*` | Custom data attributes | `<div data-user-id="123">` |
| `hidden` | Hide element | `<div hidden>` |
| `lang` | Language of element content | `<p lang="es">Hola</p>` |
| `tabindex` | Tab order | `<button tabindex="1">` |
| `accesskey` | Keyboard shortcut | `<button accesskey="s">Save</button>` |

#### Element-Specific Attributes

```html
<!-- Links -->
<a href="https://example.com" target="_blank" rel="noopener">Link</a>

<!-- Images -->
<img src="photo.jpg" alt="Description" width="400" height="300" loading="lazy">

<!-- Forms -->
<input type="text" name="username" placeholder="Enter username" required>
<form action="/submit" method="POST" enctype="multipart/form-data">

<!-- Media -->
<video src="video.mp4" controls autoplay muted loop></video>
```

#### Boolean Attributes

Some attributes don't need values - their presence indicates true:

```html
<input type="checkbox" checked>
<input type="text" disabled>
<input type="email" required>
<script src="app.js" defer></script>
<video controls autoplay muted></video>
```

### Best Practices for Attributes

1. **Use lowercase**: `<div class="container">` (not `<DIV CLASS="container">`)
2. **Quote values**: Always use quotes around attribute values
3. **Meaningful IDs**: Use descriptive, unique ID names
4. **Multiple classes**: Separate with spaces: `class="btn btn-primary"`
5. **Data attributes**: Use for storing custom data: `data-user-role="admin"`

---

## Inline vs Block Elements

HTML elements are categorized by their default display behavior: inline or block.

### Block-Level Elements

Block elements start on a new line and take up the full width available.

**Characteristics:**
- Always start on a new line
- Take up the full width of their container
- Can contain inline and other block elements
- Stack vertically

```html
<div>This is a div (block element)</div>
<p>This is a paragraph (block element)</p>
<h1>This is a heading (block element)</h1>
```

**Common Block Elements:**

| Element | Purpose |
|---------|---------|
| `<div>` | Generic container |
| `<p>` | Paragraph |
| `<h1>` to `<h6>` | Headings |
| `<ul>`, `<ol>`, `<li>` | Lists |
| `<table>` | Table |
| `<form>` | Form container |
| `<header>`, `<footer>` | Page sections |
| `<section>`, `<article>` | Content sections |
| `<nav>` | Navigation |
| `<blockquote>` | Block quotation |
| `<pre>` | Preformatted text |
| `<hr>` | Horizontal rule |

**Visual Example:**

```html
<div style="background: lightblue; padding: 10px;">
    Block Element 1 (takes full width)
</div>
<div style="background: lightgreen; padding: 10px;">
    Block Element 2 (starts on new line)
</div>
```

### Inline Elements

Inline elements do not start on a new line and only take up as much width as necessary.

**Characteristics:**
- Do not start on a new line
- Only take up necessary width
- Cannot contain block elements (usually)
- Flow horizontally

```html
<p>This is a <strong>bold word</strong> in a sentence.</p>
<p>Visit our <a href="#">website</a> for more info.</p>
```

**Common Inline Elements:**

| Element | Purpose |
|---------|---------|
| `<span>` | Generic inline container |
| `<a>` | Hyperlink |
| `<strong>`, `<b>` | Bold text |
| `<em>`, `<i>` | Italic text |
| `<img>` | Image |
| `<input>` | Form input |
| `<label>` | Form label |
| `<code>` | Code snippet |
| `<small>` | Small text |
| `<mark>` | Highlighted text |
| `<sub>`, `<sup>` | Subscript, superscript |
| `<br>` | Line break |

**Visual Example:**

```html
<p>
    This sentence contains <strong>bold text</strong>,
    <em>italic text</em>, and a <a href="#">link</a>,
    all flowing inline.
</p>
```

### Inline-Block Elements

Some elements or CSS can make elements behave as both inline and block:

```html
<!-- Using CSS to create inline-block behavior -->
<span style="display: inline-block; width: 100px; height: 50px; background: coral;">
    Inline-block
</span>
<span style="display: inline-block; width: 100px; height: 50px; background: teal;">
    Inline-block
</span>
```

### Comparison Table

| Aspect | Block Elements | Inline Elements |
|--------|----------------|-----------------|
| New line | Yes | No |
| Width | Full width available | Only necessary width |
| Height | Can set height | Height based on content |
| Can contain | Block and inline elements | Only inline elements |
| Padding/Margin | All sides | Horizontal only (vertical ignored) |
| Examples | `<div>`, `<p>`, `<h1>` | `<span>`, `<a>`, `<strong>` |

### Nesting Rules

**Valid:**
```html
<!-- Block containing inline -->
<div>
    <span>Inline element inside block</span>
</div>

<!-- Block containing block -->
<div>
    <p>Block element inside block</p>
</div>
```

**Invalid:**
```html
<!-- Inline containing block (not allowed) -->
<span>
    <div>This is not valid!</div>
</span>

<!-- Paragraph containing another paragraph (not allowed) -->
<p>
    <p>This is not valid!</p>
</p>
```

### Changing Display Behavior with CSS

You can override default display behavior using CSS:

```html
<!-- Make block element inline -->
<div style="display: inline;">Now behaves like inline</div>

<!-- Make inline element block -->
<span style="display: block;">Now behaves like block</span>

<!-- Inline-block (best of both) -->
<span style="display: inline-block; width: 200px;">
    Can set width but stays inline
</span>
```

---

## Best Practices

### 1. Use Semantic HTML

Choose tags based on meaning, not appearance:

```html
<!-- Good: Semantic -->
<article>
    <h1>Article Title</h1>
    <p>Article content...</p>
</article>

<!-- Bad: Non-semantic -->
<div>
    <div class="title">Article Title</div>
    <div class="content">Article content...</div>
</div>
```

### 2. Proper Document Structure

Always include proper HTML5 structure:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Page Title</title>
</head>
<body>
    <!-- Content -->
</body>
</html>
```

### 3. Accessibility

Make your HTML accessible to all users:

```html
<!-- Use alt text for images -->
<img src="chart.png" alt="Sales data chart showing 20% growth">

<!-- Use proper heading hierarchy -->
<h1>Main Title</h1>
<h2>Section Title</h2>
<h3>Subsection</h3>

<!-- Associate labels with inputs -->
<label for="email">Email:</label>
<input type="email" id="email" name="email">

<!-- Use ARIA attributes when needed -->
<button aria-label="Close dialog">X</button>
```

### 4. Keep It Clean

Write clean, readable HTML:

```html
<!-- Good: Well-formatted and indented -->
<nav>
    <ul>
        <li><a href="#home">Home</a></li>
        <li><a href="#about">About</a></li>
        <li><a href="#contact">Contact</a></li>
    </ul>
</nav>

<!-- Bad: Hard to read -->
<nav><ul><li><a href="#home">Home</a></li><li><a href="#about">About</a></li></ul></nav>
```

### 5. Validate Your HTML

Use [W3C Validator](https://validator.w3.org/) to check for errors and ensure standards compliance.

---

## Summary

| Concept | Key Points |
|---------|------------|
| HTML | Markup language for structuring web content |
| HTML5 | Latest version with semantic elements, forms, multimedia |
| Tags | Building blocks: `<tagname>content</tagname>` |
| Attributes | Additional information: `attribute="value"` |
| Block Elements | Full width, new line (`<div>`, `<p>`, `<h1>`) |
| Inline Elements | Necessary width, same line (`<span>`, `<a>`, `<strong>`) |

---

## Next Topic

Continue to [HTML Forms](./02-html-forms.md) to learn about creating interactive forms with various input types and validation.
