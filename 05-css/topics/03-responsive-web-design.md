# Responsive Web Design

## Overview

Responsive Web Design (RWD) is an approach to web design that ensures web pages render well on all devices and screen sizes. It combines flexible layouts, flexible images, and media queries to create an optimal viewing experience.

**Key Principles:**
1. **Fluid Grids**: Layouts based on percentages or relative units
2. **Flexible Images**: Images that scale within containers
3. **Media Queries**: Apply styles based on device characteristics

---

## Why Responsive Design?

### Device Diversity

Modern users access websites from various devices:

```
Desktop:     1920x1080, 1366x768, 1440x900
Laptop:      1280x720, 1366x768
Tablet:      768x1024, 834x1194, 1024x1366
Mobile:      375x667, 414x896, 390x844
```

### Statistics

- Over 60% of web traffic comes from mobile devices
- Google uses mobile-first indexing for SEO
- Users expect seamless experiences across devices
- Responsive sites improve conversion rates

### Benefits

- **Single Codebase**: One site for all devices
- **Better SEO**: Google favors mobile-friendly sites
- **Improved UX**: Optimized for each screen size
- **Cost-Effective**: No separate mobile site needed
- **Future-Proof**: Adapts to new device sizes

---

## Media Queries

Media queries allow you to apply CSS rules based on device characteristics like screen width, height, orientation, and resolution.

### Basic Syntax

```css
@media media-type and (condition) {
  /* CSS rules */
}
```

### Media Types

```css
@media screen { }      /* Computer screens, tablets, phones */
@media print { }       /* Printers */
@media all { }         /* All devices (default) */
@media speech { }      /* Screen readers */
```

**Note:** Most responsive designs focus on `screen`.

### Common Media Features

#### Width

```css
/* Minimum width */
@media (min-width: 768px) {
  .container {
    max-width: 750px;
  }
}

/* Maximum width */
@media (max-width: 767px) {
  .sidebar {
    display: none;
  }
}

/* Range */
@media (min-width: 768px) and (max-width: 1024px) {
  .container {
    max-width: 960px;
  }
}
```

#### Height

```css
@media (min-height: 800px) {
  .hero {
    height: 100vh;
  }
}

@media (max-height: 600px) {
  .header {
    height: 60px;
  }
}
```

#### Orientation

```css
/* Portrait mode (height > width) */
@media (orientation: portrait) {
  .gallery {
    grid-template-columns: 1fr;
  }
}

/* Landscape mode (width > height) */
@media (orientation: landscape) {
  .gallery {
    grid-template-columns: repeat(2, 1fr);
  }
}
```

#### Resolution

```css
/* High DPI displays (Retina) */
@media (-webkit-min-device-pixel-ratio: 2),
       (min-resolution: 192dpi) {
  .logo {
    background-image: url('logo@2x.png');
  }
}
```

#### Aspect Ratio

```css
@media (aspect-ratio: 16/9) {
  .video-container {
    /* Special styling for 16:9 displays */
  }
}
```

### Logical Operators

#### AND

Combines multiple conditions.

```css
@media (min-width: 768px) and (max-width: 1024px) {
  /* Styles for tablets */
}

@media screen and (min-width: 768px) and (orientation: landscape) {
  /* Landscape tablets */
}
```

#### OR (Comma)

Applies if any condition is true.

```css
@media (min-width: 768px), (orientation: landscape) {
  /* Tablets or landscape mode */
}
```

#### NOT

Negates a condition.

```css
@media not screen and (color) {
  /* Non-color screen devices */
}
```

#### ONLY

Hides styles from older browsers.

```css
@media only screen and (min-width: 768px) {
  /* Modern browsers only */
}
```

### Modern Range Syntax

CSS Media Queries Level 4 introduces cleaner syntax:

```css
/* Old syntax */
@media (min-width: 768px) and (max-width: 1024px) { }

/* New syntax */
@media (768px <= width <= 1024px) { }

/* Greater than */
@media (width >= 768px) { }

/* Less than */
@media (width < 768px) { }
```

---

## Breakpoints

Breakpoints are screen widths where your design adjusts to accommodate different device sizes.

### Common Breakpoint Systems

#### Mobile-First Breakpoints

```css
/* Mobile first: Base styles for mobile, then build up */

/* Extra small devices (phones, less than 576px) */
/* No media query needed - this is the default */

/* Small devices (landscape phones, 576px and up) */
@media (min-width: 576px) {
  .container { max-width: 540px; }
}

/* Medium devices (tablets, 768px and up) */
@media (min-width: 768px) {
  .container { max-width: 720px; }
}

/* Large devices (desktops, 992px and up) */
@media (min-width: 992px) {
  .container { max-width: 960px; }
}

/* Extra large devices (large desktops, 1200px and up) */
@media (min-width: 1200px) {
  .container { max-width: 1140px; }
}

/* XXL devices (larger desktops, 1400px and up) */
@media (min-width: 1400px) {
  .container { max-width: 1320px; }
}
```

#### Bootstrap Breakpoints

```css
/* X-Small devices (portrait phones) */
/* < 576px - No media query */

/* Small devices (landscape phones) */
@media (min-width: 576px) { }  /* sm */

/* Medium devices (tablets) */
@media (min-width: 768px) { }  /* md */

/* Large devices (desktops) */
@media (min-width: 992px) { }  /* lg */

/* X-Large devices (large desktops) */
@media (min-width: 1200px) { } /* xl */

/* XX-Large devices (larger desktops) */
@media (min-width: 1400px) { } /* xxl */
```

#### Tailwind Breakpoints

```css
/* Small */
@media (min-width: 640px) { }  /* sm */

/* Medium */
@media (min-width: 768px) { }  /* md */

/* Large */
@media (min-width: 1024px) { } /* lg */

/* Extra Large */
@media (min-width: 1280px) { } /* xl */

/* 2X Extra Large */
@media (min-width: 1536px) { } /* 2xl */
```

#### Material Design Breakpoints

```css
/* Small */
@media (min-width: 600px) { }  /* sm */

/* Medium */
@media (min-width: 960px) { }  /* md */

/* Large */
@media (min-width: 1280px) { } /* lg */

/* Extra Large */
@media (min-width: 1920px) { } /* xl */
```

### Custom Breakpoints

Choose breakpoints based on your content, not devices:

```css
/* Content-based breakpoints */
@media (min-width: 480px) {
  /* When navigation needs more space */
}

@media (min-width: 800px) {
  /* When sidebar becomes viable */
}

@media (min-width: 1100px) {
  /* When content needs wider layout */
}
```

### Organizing Media Queries

#### Method 1: Component-Level

```css
/* Component styles together with media queries */
.card {
  width: 100%;
  padding: 15px;
}

@media (min-width: 768px) {
  .card {
    width: 50%;
    padding: 20px;
  }
}

@media (min-width: 1200px) {
  .card {
    width: 33.333%;
    padding: 25px;
  }
}
```

#### Method 2: Grouped Breakpoints

```css
/* All base styles */
.header { font-size: 24px; }
.nav { display: block; }
.sidebar { display: none; }

/* Tablet styles */
@media (min-width: 768px) {
  .header { font-size: 28px; }
  .nav { display: flex; }
  .sidebar { display: block; }
}

/* Desktop styles */
@media (min-width: 1200px) {
  .header { font-size: 32px; }
}
```

#### Method 3: Separate Files

```css
/* main.css */
@import url('base.css');
@import url('tablet.css') (min-width: 768px);
@import url('desktop.css') (min-width: 1200px);
```

---

## Mobile-First Design

Mobile-first design means designing for mobile devices first, then progressively enhancing for larger screens.

### Why Mobile-First?

- **Forced Prioritization**: Focus on essential content
- **Performance**: Mobile gets lighter base styles
- **Progressive Enhancement**: Add features for capable devices
- **Future-Proof**: New devices tend to be mobile
- **SEO**: Google uses mobile-first indexing

### Mobile-First vs Desktop-First

#### Desktop-First (Avoid)

```css
/* Base styles for desktop */
.container {
  width: 1200px;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
}

/* Override for mobile */
@media (max-width: 768px) {
  .container {
    width: 100%;
    grid-template-columns: 1fr;
  }
}
```

**Problems:**
- Mobile downloads unnecessary desktop CSS
- More overrides needed
- Harder to maintain

#### Mobile-First (Recommended)

```css
/* Base styles for mobile */
.container {
  width: 100%;
  padding: 15px;
}

/* Enhance for tablets */
@media (min-width: 768px) {
  .container {
    max-width: 750px;
    margin: 0 auto;
    display: grid;
    grid-template-columns: repeat(2, 1fr);
  }
}

/* Enhance for desktop */
@media (min-width: 1200px) {
  .container {
    max-width: 1140px;
    grid-template-columns: repeat(4, 1fr);
  }
}
```

**Benefits:**
- Mobile gets optimized, minimal CSS
- Natural progression of features
- Easier to maintain

### Mobile-First Patterns

#### Navigation

```css
/* Mobile: Hamburger menu */
.nav {
  display: none;
  flex-direction: column;
  position: absolute;
  background: white;
  width: 100%;
}

.nav.active {
  display: flex;
}

.menu-toggle {
  display: block;
}

/* Desktop: Horizontal menu */
@media (min-width: 768px) {
  .nav {
    display: flex;
    flex-direction: row;
    position: static;
    width: auto;
  }

  .menu-toggle {
    display: none;
  }
}
```

#### Typography

```css
/* Mobile: Smaller, tighter text */
body {
  font-size: 16px;
  line-height: 1.5;
}

h1 {
  font-size: 28px;
  margin-bottom: 15px;
}

/* Tablet: Slightly larger */
@media (min-width: 768px) {
  body {
    font-size: 18px;
    line-height: 1.6;
  }

  h1 {
    font-size: 36px;
    margin-bottom: 20px;
  }
}

/* Desktop: Largest */
@media (min-width: 1200px) {
  body {
    font-size: 20px;
    line-height: 1.7;
  }

  h1 {
    font-size: 48px;
    margin-bottom: 25px;
  }
}
```

#### Grid Layout

```css
/* Mobile: Single column */
.grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 15px;
}

/* Tablet: Two columns */
@media (min-width: 768px) {
  .grid {
    grid-template-columns: repeat(2, 1fr);
    gap: 20px;
  }
}

/* Desktop: Four columns */
@media (min-width: 1200px) {
  .grid {
    grid-template-columns: repeat(4, 1fr);
    gap: 30px;
  }
}
```

---

## Responsive Units

Use relative units instead of fixed pixels for truly responsive designs.

### Relative Units

#### em

Relative to parent element's font size.

```css
.parent {
  font-size: 16px;
}

.child {
  font-size: 1.5em;     /* 24px (16 * 1.5) */
  padding: 0.5em;       /* 12px (24 * 0.5) */
  margin: 1em;          /* 24px (24 * 1) */
}
```

**Use Cases:**
- Padding/margin relative to font size
- Creating scalable components

**Warning:** `em` compounds (child inherits parent's computed value).

#### rem

Relative to root element's font size (usually `<html>`).

```css
html {
  font-size: 16px;  /* Base size */
}

h1 {
  font-size: 2rem;        /* 32px */
  margin-bottom: 1rem;    /* 16px */
}

p {
  font-size: 1rem;        /* 16px */
  line-height: 1.5rem;    /* 24px */
}
```

**Use Cases:**
- Consistent sizing across site
- Easy responsive scaling (change root size only)

**Recommended:** Use `rem` for most sizing.

#### Percentages (%)

Relative to parent element.

```css
.container {
  width: 100%;           /* Full parent width */
  max-width: 1200px;
}

.column {
  width: 50%;            /* Half of parent */
  padding: 2%;           /* 2% of parent width */
}
```

**Use Cases:**
- Fluid widths
- Responsive layouts

#### Viewport Units

Relative to viewport size.

```css
/* vw - Viewport Width */
.hero {
  width: 100vw;          /* Full viewport width */
  font-size: 5vw;        /* 5% of viewport width */
}

/* vh - Viewport Height */
.fullscreen {
  height: 100vh;         /* Full viewport height */
}

.hero-section {
  min-height: 50vh;      /* Half viewport height */
}

/* vmin - Smaller of vw or vh */
.square {
  width: 50vmin;         /* 50% of smaller dimension */
  height: 50vmin;
}

/* vmax - Larger of vw or vh */
.responsive-text {
  font-size: 3vmax;      /* 3% of larger dimension */
}
```

**Use Cases:**
- Full-screen sections
- Responsive typography
- Maintaining aspect ratios

**Warning:** Be careful with viewport units - can cause issues with mobile browsers' address bars.

### Clamp() Function

Responsive sizing with min, ideal, and max values.

```css
/* clamp(minimum, preferred, maximum) */

h1 {
  font-size: clamp(1.5rem, 5vw, 3rem);
  /* Min: 1.5rem, Ideal: 5vw, Max: 3rem */
}

.container {
  width: clamp(300px, 90%, 1200px);
  /* Min: 300px, Ideal: 90%, Max: 1200px */
}

p {
  font-size: clamp(1rem, 2.5vw, 1.25rem);
  line-height: clamp(1.5rem, 4vw, 2rem);
}
```

**Benefits:**
- Single line replaces multiple media queries
- Fluid scaling within bounds
- Better readability

### Min/Max Functions

```css
/* Use minimum value */
.box {
  width: min(100%, 600px);  /* Never exceeds 600px */
}

/* Use maximum value */
.text {
  font-size: max(1rem, 2vw);  /* Never smaller than 1rem */
}

/* Combined */
.container {
  width: min(90%, 1200px);
  padding: max(20px, 5%);
}
```

### Calc() Function

Perform calculations.

```css
.sidebar {
  width: calc(100% - 300px);
}

.card {
  width: calc(33.333% - 20px);  /* 3 columns with 20px gap */
}

.offset {
  margin-left: calc(50% - 600px);
}

/* Combined with other functions */
.responsive {
  width: calc(100% - 2rem);
  max-width: min(calc(100% - 40px), 1200px);
}
```

---

## Responsive Images

### Flexible Images

```css
img {
  max-width: 100%;
  height: auto;  /* Maintains aspect ratio */
  display: block;
}
```

### Object-Fit

Control how images fit within containers.

```css
.cover {
  width: 100%;
  height: 300px;
  object-fit: cover;      /* Crop to fill */
  object-position: center;
}

.contain {
  object-fit: contain;    /* Scale to fit (letterbox) */
}

.fill {
  object-fit: fill;       /* Stretch to fill */
}

.scale-down {
  object-fit: scale-down; /* Smaller of contain or none */
}
```

### Picture Element

Serve different images for different screen sizes.

```html
<picture>
  <source media="(min-width: 1200px)" srcset="large.jpg">
  <source media="(min-width: 768px)" srcset="medium.jpg">
  <img src="small.jpg" alt="Responsive image">
</picture>
```

### Srcset Attribute

```html
<!-- Resolution switching -->
<img srcset="small.jpg 480w,
             medium.jpg 800w,
             large.jpg 1200w"
     sizes="(max-width: 600px) 480px,
            (max-width: 1000px) 800px,
            1200px"
     src="medium.jpg"
     alt="Responsive image">

<!-- Pixel density -->
<img srcset="image.jpg 1x,
             image@2x.jpg 2x,
             image@3x.jpg 3x"
     src="image.jpg"
     alt="High DPI image">
```

### Background Images

```css
/* Mobile */
.hero {
  background-image: url('hero-mobile.jpg');
  background-size: cover;
  background-position: center;
}

/* Tablet */
@media (min-width: 768px) {
  .hero {
    background-image: url('hero-tablet.jpg');
  }
}

/* Desktop */
@media (min-width: 1200px) {
  .hero {
    background-image: url('hero-desktop.jpg');
  }
}

/* Retina */
@media (-webkit-min-device-pixel-ratio: 2),
       (min-resolution: 192dpi) {
  .hero {
    background-image: url('hero-desktop@2x.jpg');
  }
}
```

---

## Complete Responsive Example

```css
/* ==========================================
   Reset and Base Styles (Mobile First)
   ========================================== */

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html {
  font-size: 16px;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  line-height: 1.6;
  color: #333;
}

img {
  max-width: 100%;
  height: auto;
  display: block;
}

/* ==========================================
   Container
   ========================================== */

.container {
  width: 100%;
  padding: 0 15px;
  margin: 0 auto;
}

@media (min-width: 576px) {
  .container { max-width: 540px; }
}

@media (min-width: 768px) {
  .container { max-width: 720px; }
}

@media (min-width: 992px) {
  .container { max-width: 960px; }
}

@media (min-width: 1200px) {
  .container { max-width: 1140px; }
}

/* ==========================================
   Header
   ========================================== */

.header {
  background: #2c3e50;
  color: white;
  padding: 15px 0;
}

@media (min-width: 768px) {
  .header {
    padding: 20px 0;
  }
}

/* ==========================================
   Navigation
   ========================================== */

.nav {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.nav-link {
  padding: 10px;
  color: white;
  text-decoration: none;
}

@media (min-width: 768px) {
  .nav {
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
  }

  .nav-link {
    padding: 10px 15px;
  }
}

/* ==========================================
   Grid
   ========================================== */

.grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 20px;
  padding: 20px 0;
}

@media (min-width: 768px) {
  .grid {
    grid-template-columns: repeat(2, 1fr);
    gap: 30px;
  }
}

@media (min-width: 1200px) {
  .grid {
    grid-template-columns: repeat(4, 1fr);
    gap: 40px;
  }
}

/* ==========================================
   Card Component
   ========================================== */

.card {
  background: white;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.card-image {
  width: 100%;
  height: 200px;
  object-fit: cover;
}

.card-content {
  padding: 15px;
}

.card-title {
  font-size: clamp(1.25rem, 3vw, 1.5rem);
  margin-bottom: 10px;
}

@media (min-width: 768px) {
  .card-content {
    padding: 20px;
  }
}

/* ==========================================
   Typography
   ========================================== */

h1 {
  font-size: clamp(1.75rem, 5vw, 3rem);
  line-height: 1.2;
  margin-bottom: 1rem;
}

h2 {
  font-size: clamp(1.5rem, 4vw, 2.5rem);
  line-height: 1.3;
  margin-bottom: 0.875rem;
}

p {
  font-size: clamp(1rem, 2vw, 1.125rem);
  margin-bottom: 1rem;
}

/* ==========================================
   Utilities
   ========================================== */

.hide-mobile {
  display: none;
}

@media (min-width: 768px) {
  .hide-mobile {
    display: block;
  }
}

.hide-desktop {
  display: block;
}

@media (min-width: 768px) {
  .hide-desktop {
    display: none;
  }
}
```

---

## Best Practices

### 1. Use Mobile-First Approach

Start with mobile styles, enhance for desktop.

### 2. Test on Real Devices

Emulators don't catch everything - test on actual phones and tablets.

### 3. Use Relative Units

Prefer `rem`, `em`, `%`, `vw`/`vh` over fixed `px`.

### 4. Optimize Images

- Use appropriate image sizes
- Implement lazy loading
- Use modern formats (WebP, AVIF)

### 5. Touch-Friendly Targets

```css
.button {
  min-height: 44px;  /* Apple recommendation */
  min-width: 44px;
  padding: 12px 24px;
}
```

### 6. Consider Performance

- Minimize CSS file size
- Use CSS instead of images where possible
- Avoid unnecessary animations on mobile

### 7. Use Container Queries (Future)

```css
/* When supported */
@container (min-width: 700px) {
  .card {
    display: grid;
    grid-template-columns: 2fr 1fr;
  }
}
```

### 8. Test Accessibility

- Ensure sufficient contrast
- Test with screen readers
- Support keyboard navigation

---

## Common Responsive Patterns

### Sticky Footer

```css
body {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

.main-content {
  flex: 1;
}

.footer {
  flex-shrink: 0;
}
```

### Responsive Tables

```css
/* Mobile: Card layout */
@media (max-width: 767px) {
  table, thead, tbody, th, td, tr {
    display: block;
  }

  thead {
    display: none;
  }

  tr {
    margin-bottom: 15px;
    border: 1px solid #ddd;
  }

  td {
    position: relative;
    padding-left: 50%;
  }

  td::before {
    content: attr(data-label);
    position: absolute;
    left: 6px;
    font-weight: bold;
  }
}
```

### Off-Canvas Menu

```css
.menu {
  position: fixed;
  left: -250px;
  width: 250px;
  height: 100vh;
  background: white;
  transition: left 0.3s;
}

.menu.active {
  left: 0;
}

@media (min-width: 768px) {
  .menu {
    position: static;
    width: auto;
    height: auto;
  }
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Media Queries | `@media (min-width: 768px)` for conditional styles |
| Breakpoints | 576px, 768px, 992px, 1200px (common values) |
| Mobile-First | Base styles for mobile, enhance for desktop |
| Responsive Units | rem, em, %, vw/vh, clamp() |
| Flexible Images | `max-width: 100%`, `height: auto`, object-fit |
| Testing | Test on real devices, not just emulators |

---

## Next Topic

Continue to [Advanced Features](./04-advanced-features.md) to learn about colors, backgrounds, box model, transitions, and animations.
