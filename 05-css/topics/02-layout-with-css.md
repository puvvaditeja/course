# Layout with CSS

## Overview

CSS provides multiple techniques for controlling the layout of web pages. Modern CSS offers powerful tools like Flexbox and Grid that make creating complex, responsive layouts much easier than traditional methods.

This topic covers three fundamental layout approaches:
1. Traditional layout (Display, Position, Float)
2. Flexbox (one-dimensional layouts)
3. CSS Grid (two-dimensional layouts)

---

## Display Property

The `display` property determines how an element is rendered in the document flow.

### Display Values

#### `block`

Elements take up the full width available and start on a new line.

```css
div {
  display: block;  /* Default for div, p, h1-h6, etc. */
}
```

**Characteristics:**
- Takes full width available
- Starts on a new line
- Respects width, height, padding, margin (all sides)

**Block-level Elements:** `<div>`, `<p>`, `<h1>-<h6>`, `<ul>`, `<ol>`, `<li>`, `<section>`, `<article>`, `<header>`, `<footer>`

#### `inline`

Elements flow within text and only take up necessary width.

```css
span {
  display: inline;  /* Default for span, a, strong, em */
}
```

**Characteristics:**
- Takes only necessary width
- Does not start on a new line
- Ignores width and height properties
- Only respects horizontal padding/margin

**Inline Elements:** `<span>`, `<a>`, `<strong>`, `<em>`, `<img>`, `<button>`, `<input>`

#### `inline-block`

Combines features of both inline and block.

```css
.inline-block {
  display: inline-block;
}
```

**Characteristics:**
- Flows like inline elements
- Respects width, height, padding, margin like block elements
- Perfect for creating button groups or navigation menus

**Example:**
```css
.button {
  display: inline-block;
  padding: 10px 20px;
  margin: 5px;
  background-color: #3498db;
  color: white;
}
```

#### `none`

Element is removed from the document flow (not rendered).

```css
.hidden {
  display: none;  /* Element not visible and takes no space */
}

/* Compare with visibility */
.invisible {
  visibility: hidden;  /* Element invisible but still takes space */
}
```

#### Other Display Values

```css
.flex { display: flex; }           /* Flexbox container */
.grid { display: grid; }           /* Grid container */
.table { display: table; }         /* Table layout */
.inline-flex { display: inline-flex; }
.inline-grid { display: inline-grid; }
```

---

## Position Property

The `position` property controls how elements are positioned within the document.

### Position Values

#### `static` (Default)

Elements are positioned according to normal document flow.

```css
div {
  position: static;  /* Default positioning */
}
```

- Not affected by top, right, bottom, left properties
- Normal document flow

#### `relative`

Positioned relative to its normal position.

```css
.relative {
  position: relative;
  top: 20px;    /* Moves 20px down from normal position */
  left: 30px;   /* Moves 30px right from normal position */
}
```

**Characteristics:**
- Original space is preserved
- Can use top, right, bottom, left
- Creates positioning context for absolute children
- Does not affect other elements

**Example:**
```css
.badge {
  position: relative;
  top: -5px;  /* Slightly raised badge */
}
```

#### `absolute`

Positioned relative to the nearest positioned ancestor (not static).

```css
.container {
  position: relative;  /* Positioning context */
}

.absolute {
  position: absolute;
  top: 0;
  right: 0;
}
```

**Characteristics:**
- Removed from normal document flow
- Positioned relative to nearest positioned ancestor
- If no positioned ancestor, uses document body
- Other elements ignore it

**Common Use Cases:**
```css
/* Modal/Overlay */
.modal {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);  /* Center it */
}

/* Close button in corner */
.close-button {
  position: absolute;
  top: 10px;
  right: 10px;
}

/* Notification badge */
.notification-badge {
  position: absolute;
  top: -8px;
  right: -8px;
}
```

#### `fixed`

Positioned relative to the viewport (browser window).

```css
.fixed-header {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  z-index: 1000;
}
```

**Characteristics:**
- Removed from normal flow
- Stays in same position when scrolling
- Positioned relative to viewport
- Always visible (unless scrolled past with mobile browsers)

**Common Use Cases:**
```css
/* Fixed navigation */
.navbar {
  position: fixed;
  top: 0;
  width: 100%;
  background: white;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

/* Floating action button */
.fab {
  position: fixed;
  bottom: 20px;
  right: 20px;
  width: 56px;
  height: 56px;
  border-radius: 50%;
}

/* Cookie consent banner */
.cookie-banner {
  position: fixed;
  bottom: 0;
  width: 100%;
}
```

#### `sticky`

Hybrid between relative and fixed positioning.

```css
.sticky-header {
  position: sticky;
  top: 0;
  background: white;
}
```

**Characteristics:**
- Behaves like relative until scroll threshold
- Then becomes fixed
- Remains within parent container
- Great for table headers and navigation

**Example:**
```css
/* Sticky table header */
thead th {
  position: sticky;
  top: 0;
  background: #f5f5f5;
  z-index: 10;
}

/* Sticky sidebar */
.sidebar {
  position: sticky;
  top: 20px;
  height: fit-content;
}
```

### Z-Index

Controls stacking order of positioned elements.

```css
.layer-1 { z-index: 1; }
.layer-2 { z-index: 2; }  /* Appears on top */
.layer-3 { z-index: 3; }  /* Appears on top of both */

/* Common z-index scale */
.dropdown { z-index: 1000; }
.modal-backdrop { z-index: 1040; }
.modal { z-index: 1050; }
.tooltip { z-index: 1060; }
```

**Note:** `z-index` only works on positioned elements (not static).

---

## Float Property

Floats allow elements to wrap around other content. Originally designed for wrapping text around images.

### Float Values

```css
.float-left { float: left; }
.float-right { float: right; }
.float-none { float: none; }
```

### Basic Float Example

```css
.image {
  float: left;
  margin-right: 20px;
  margin-bottom: 10px;
}
```

**HTML:**
```html
<img src="image.jpg" class="image" alt="Sample">
<p>Text wraps around the floated image. This is the traditional
   use case for floats in modern web design.</p>
```

### Clearing Floats

The `clear` property prevents elements from wrapping around floats.

```css
.clear-left { clear: left; }
.clear-right { clear: right; }
.clear-both { clear: both; }
```

### Clearfix Pattern

Prevent parent collapse when children are floated.

```css
/* Method 1: Clearfix class */
.clearfix::after {
  content: "";
  display: table;
  clear: both;
}

/* Method 2: Overflow */
.container {
  overflow: auto;  /* or overflow: hidden; */
}

/* Method 3: Modern approach with flow-root */
.container {
  display: flow-root;
}
```

### Float Layout Example

```css
/* Two-column layout with floats (legacy approach) */
.sidebar {
  float: left;
  width: 25%;
  padding: 20px;
}

.main-content {
  float: right;
  width: 75%;
  padding: 20px;
}

.container::after {
  content: "";
  display: table;
  clear: both;
}
```

**Note:** Floats are largely replaced by Flexbox and Grid for layouts. Use floats primarily for wrapping text around images.

---

## Flexbox Layout

Flexbox is a powerful one-dimensional layout system designed for distributing space and aligning items within a container.

### Flexbox Concepts

```
┌─────────────────────────────────────────┐
│  Flex Container (display: flex)         │
│  ┌──────┐  ┌──────┐  ┌──────┐          │
│  │ Flex │  │ Flex │  │ Flex │          │
│  │ Item │  │ Item │  │ Item │          │
│  └──────┘  └──────┘  └──────┘          │
│                                         │
│  Main Axis ────────────────────>        │
│  Cross Axis ↓                           │
└─────────────────────────────────────────┘
```

### Creating a Flex Container

```css
.container {
  display: flex;  /* or inline-flex */
}
```

### Flex Direction

Controls the direction of the main axis.

```css
.row {
  flex-direction: row;  /* Default: left to right */
}

.row-reverse {
  flex-direction: row-reverse;  /* Right to left */
}

.column {
  flex-direction: column;  /* Top to bottom */
}

.column-reverse {
  flex-direction: column-reverse;  /* Bottom to top */
}
```

### Justify Content (Main Axis)

Aligns items along the main axis.

```css
.flex-start {
  justify-content: flex-start;  /* Default: items at start */
}

.flex-end {
  justify-content: flex-end;  /* Items at end */
}

.center {
  justify-content: center;  /* Items centered */
}

.space-between {
  justify-content: space-between;  /* Space between items */
}

.space-around {
  justify-content: space-around;  /* Space around items */
}

.space-evenly {
  justify-content: space-evenly;  /* Equal space */
}
```

**Visual Example:**
```
flex-start:     [1][2][3]___________
flex-end:       ___________[1][2][3]
center:         _____[1][2][3]______
space-between:  [1]______[2]______[3]
space-around:   __[1]____[2]____[3]__
space-evenly:   ___[1]___[2]___[3]___
```

### Align Items (Cross Axis)

Aligns items along the cross axis.

```css
.stretch {
  align-items: stretch;  /* Default: stretch to fill */
}

.flex-start {
  align-items: flex-start;  /* Align to start */
}

.flex-end {
  align-items: flex-end;  /* Align to end */
}

.center {
  align-items: center;  /* Center alignment */
}

.baseline {
  align-items: baseline;  /* Align text baselines */
}
```

### Flex Wrap

Controls whether items wrap to new lines.

```css
.no-wrap {
  flex-wrap: nowrap;  /* Default: single line */
}

.wrap {
  flex-wrap: wrap;  /* Wrap to multiple lines */
}

.wrap-reverse {
  flex-wrap: wrap-reverse;  /* Wrap in reverse */
}
```

### Align Content

Aligns multiple lines (when wrapping).

```css
.container {
  flex-wrap: wrap;
  align-content: flex-start;  /* or flex-end, center, space-between, etc. */
}
```

### Flex Item Properties

#### Flex Grow

Controls how items grow to fill space.

```css
.item {
  flex-grow: 1;  /* Grow to fill available space */
}

.item-double {
  flex-grow: 2;  /* Takes twice as much space */
}
```

#### Flex Shrink

Controls how items shrink when space is limited.

```css
.item {
  flex-shrink: 1;  /* Can shrink (default) */
}

.no-shrink {
  flex-shrink: 0;  /* Won't shrink */
}
```

#### Flex Basis

Sets the initial size before growing/shrinking.

```css
.item {
  flex-basis: 200px;  /* Initial size */
}

.auto {
  flex-basis: auto;  /* Based on content (default) */
}
```

#### Flex Shorthand

```css
.item {
  flex: 1;  /* flex-grow: 1, flex-shrink: 1, flex-basis: 0% */
}

.item {
  flex: 0 1 auto;  /* grow shrink basis */
}

/* Common patterns */
.equal-width {
  flex: 1;  /* Equal width items */
}

.fixed-width {
  flex: 0 0 200px;  /* Fixed 200px width */
}

.content-width {
  flex: 0 1 auto;  /* Based on content */
}
```

#### Align Self

Override container's align-items for specific item.

```css
.special-item {
  align-self: flex-end;  /* Different alignment */
}
```

### Common Flexbox Patterns

#### Horizontal Navigation

```css
.nav {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.nav-item {
  padding: 10px 20px;
}
```

#### Centered Content

```css
.center-box {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
```

#### Card Layout

```css
.card-container {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}

.card {
  flex: 0 1 calc(33.333% - 20px);  /* 3 columns with gaps */
}
```

#### Holy Grail Layout

```css
.page {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

.header, .footer {
  flex: 0 0 auto;
}

.main {
  display: flex;
  flex: 1;
}

.sidebar {
  flex: 0 0 250px;
}

.content {
  flex: 1;
}
```

---

## CSS Grid Layout

CSS Grid is a powerful two-dimensional layout system for creating complex, responsive layouts.

### Grid Concepts

```
┌─────────────────────────────────────────┐
│  Grid Container (display: grid)         │
│  ┌────────┬────────┬────────┐           │
│  │ Cell 1 │ Cell 2 │ Cell 3 │  Row 1   │
│  ├────────┼────────┼────────┤           │
│  │ Cell 4 │ Cell 5 │ Cell 6 │  Row 2   │
│  └────────┴────────┴────────┘           │
│   Col 1    Col 2    Col 3               │
└─────────────────────────────────────────┘
```

### Creating a Grid Container

```css
.container {
  display: grid;  /* or inline-grid */
}
```

### Grid Template Columns

Defines column tracks.

```css
/* Fixed width columns */
.grid {
  grid-template-columns: 200px 200px 200px;
}

/* Flexible columns with fr unit */
.grid {
  grid-template-columns: 1fr 1fr 1fr;  /* 3 equal columns */
}

/* Mixed units */
.grid {
  grid-template-columns: 200px 1fr 2fr;  /* Fixed + flexible */
}

/* repeat() function */
.grid {
  grid-template-columns: repeat(3, 1fr);  /* 3 equal columns */
}

/* Auto-fit and minmax */
.grid {
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
}

/* Named columns */
.grid {
  grid-template-columns: [start] 1fr [middle] 1fr [end];
}
```

### Grid Template Rows

Defines row tracks (same syntax as columns).

```css
.grid {
  grid-template-rows: 100px 200px auto;
}

.grid {
  grid-template-rows: repeat(3, 1fr);
}

.grid {
  grid-template-rows: minmax(100px, auto) 1fr;
}
```

### Grid Gap

Spacing between grid items.

```css
.grid {
  gap: 20px;  /* Both row and column gap */
}

.grid {
  row-gap: 20px;
  column-gap: 30px;
}

/* Old syntax (still works) */
.grid {
  grid-gap: 20px;
}
```

### Grid Template Areas

Create named grid areas for layout.

```css
.grid {
  display: grid;
  grid-template-columns: 1fr 3fr 1fr;
  grid-template-rows: auto 1fr auto;
  grid-template-areas:
    "header header header"
    "sidebar content aside"
    "footer footer footer";
}

.header { grid-area: header; }
.sidebar { grid-area: sidebar; }
.content { grid-area: content; }
.aside { grid-area: aside; }
.footer { grid-area: footer; }
```

### Grid Item Placement

#### Grid Column

```css
.item {
  grid-column: 1 / 3;  /* Spans columns 1-2 */
}

.item {
  grid-column: 1 / span 2;  /* Starts at 1, spans 2 columns */
}

.item {
  grid-column-start: 1;
  grid-column-end: 3;
}
```

#### Grid Row

```css
.item {
  grid-row: 1 / 3;  /* Spans rows 1-2 */
}

.item {
  grid-row: 2 / span 2;  /* Starts at row 2, spans 2 */
}
```

#### Shorthand

```css
.item {
  grid-area: 1 / 1 / 3 / 3;  /* row-start / col-start / row-end / col-end */
}
```

### Alignment in Grid

#### Justify Items (Horizontal)

```css
.grid {
  justify-items: start;    /* Left align */
  justify-items: end;      /* Right align */
  justify-items: center;   /* Center align */
  justify-items: stretch;  /* Fill cell (default) */
}
```

#### Align Items (Vertical)

```css
.grid {
  align-items: start;      /* Top align */
  align-items: end;        /* Bottom align */
  align-items: center;     /* Center align */
  align-items: stretch;    /* Fill cell (default) */
}
```

#### Place Items (Shorthand)

```css
.grid {
  place-items: center;  /* Align and justify center */
}

.grid {
  place-items: start end;  /* align / justify */
}
```

#### Justify Content (Grid Container)

```css
.grid {
  justify-content: start;
  justify-content: end;
  justify-content: center;
  justify-content: space-between;
  justify-content: space-around;
  justify-content: space-evenly;
}
```

#### Align Content (Grid Container)

```css
.grid {
  align-content: start;
  align-content: end;
  align-content: center;
  align-content: space-between;
  align-content: space-around;
  align-content: space-evenly;
}
```

### Auto Columns and Rows

```css
.grid {
  grid-auto-columns: 100px;  /* Implicit column size */
  grid-auto-rows: 150px;     /* Implicit row size */
}

.grid {
  grid-auto-flow: row;       /* Fill rows first (default) */
  grid-auto-flow: column;    /* Fill columns first */
  grid-auto-flow: dense;     /* Fill gaps */
}
```

### Common Grid Patterns

#### Simple 3-Column Layout

```css
.grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
}
```

#### Responsive Auto-Fit Grid

```css
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 20px;
}
```

#### Dashboard Layout

```css
.dashboard {
  display: grid;
  grid-template-columns: repeat(12, 1fr);
  grid-template-rows: auto 1fr auto;
  gap: 20px;
}

.header {
  grid-column: 1 / -1;  /* Span all columns */
}

.sidebar {
  grid-column: 1 / 3;   /* Span 2 columns */
  grid-row: 2;
}

.main {
  grid-column: 3 / -1;  /* Remaining columns */
  grid-row: 2;
}

.footer {
  grid-column: 1 / -1;
}
```

#### Card Grid with Feature Card

```css
.card-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 20px;
}

.featured-card {
  grid-column: span 2;  /* Takes 2 columns */
  grid-row: span 2;     /* Takes 2 rows */
}
```

---

## Flexbox vs Grid: When to Use Each

### Use Flexbox When:
- Creating one-dimensional layouts (row OR column)
- Aligning items within a container
- Building navigation menus
- Distributing space between items
- Content determines layout size

### Use Grid When:
- Creating two-dimensional layouts (rows AND columns)
- Precise control over placement needed
- Overlapping elements
- Complex page layouts
- Layout determines content size

### Can Use Together:
```css
.page {
  display: grid;  /* Overall page layout */
  grid-template-columns: 250px 1fr;
}

.navigation {
  display: flex;  /* Nav items alignment */
  justify-content: space-between;
}

.card-grid {
  display: grid;  /* Card layout */
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
}

.card {
  display: flex;  /* Card content alignment */
  flex-direction: column;
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Display | block, inline, inline-block, flex, grid, none |
| Position | static, relative, absolute, fixed, sticky |
| Float | Legacy layout, now use for text wrapping only |
| Flexbox | One-dimensional, flex-direction, justify-content, align-items |
| Grid | Two-dimensional, grid-template-columns/rows, gap |
| Alignment | Different for Flexbox vs Grid, use place-items shorthand |

---

## Next Topic

Continue to [Responsive Web Design](./03-responsive-web-design.md) to learn about media queries, breakpoints, and mobile-first design.
