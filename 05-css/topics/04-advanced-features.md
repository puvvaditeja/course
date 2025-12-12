# Advanced CSS Features

## Overview

This topic covers advanced CSS3 features that enable rich, interactive, and visually appealing web designs. These features allow developers to create sophisticated effects without JavaScript or images.

**Topics Covered:**
1. Colors and Backgrounds
2. Borders and Effects
3. Box Model
4. Font Properties
5. Transitions and Animations

---

## Colors and Backgrounds

### Color Values

CSS provides multiple ways to specify colors.

#### Named Colors

```css
.element {
  color: red;
  background-color: navy;
  border-color: transparent;
}
```

**Common Named Colors:** red, blue, green, yellow, black, white, gray, orange, purple, pink, brown, etc.

#### Hexadecimal (Hex)

```css
.element {
  color: #FF0000;           /* Red */
  background: #00FF00;      /* Green */
  border-color: #0000FF;    /* Blue */
}

/* Shorthand (when pairs are identical) */
.element {
  color: #F00;              /* Same as #FF0000 */
  background: #0F0;         /* Same as #00FF00 */
}

/* With alpha channel (transparency) */
.element {
  color: #FF0000FF;         /* Red, fully opaque */
  background: #00FF0080;    /* Green, 50% transparent */
}
```

#### RGB (Red, Green, Blue)

```css
.element {
  color: rgb(255, 0, 0);              /* Red */
  background: rgb(0, 255, 0);         /* Green */
  border-color: rgb(0, 0, 255);       /* Blue */
}
```

**Values:** 0-255 for each channel.

#### RGBA (RGB with Alpha)

```css
.element {
  background-color: rgba(255, 0, 0, 0.5);     /* Red, 50% transparent */
  color: rgba(0, 0, 0, 0.8);                  /* Black, 80% opaque */
}
```

**Alpha:** 0 (fully transparent) to 1 (fully opaque).

#### HSL (Hue, Saturation, Lightness)

```css
.element {
  color: hsl(0, 100%, 50%);           /* Red */
  background: hsl(120, 100%, 50%);    /* Green */
  border-color: hsl(240, 100%, 50%);  /* Blue */
}
```

**Values:**
- Hue: 0-360 (degrees on color wheel)
- Saturation: 0%-100%
- Lightness: 0%-100%

```css
/* Color wheel: */
/* 0°/360° = Red */
/* 60° = Yellow */
/* 120° = Green */
/* 180° = Cyan */
/* 240° = Blue */
/* 300° = Magenta */

.brand-colors {
  --primary: hsl(210, 80%, 50%);      /* Blue */
  --primary-light: hsl(210, 80%, 70%);
  --primary-dark: hsl(210, 80%, 30%);
}
```

#### HSLA (HSL with Alpha)

```css
.element {
  background-color: hsla(0, 100%, 50%, 0.5);  /* Red, 50% transparent */
}
```

### CSS Variables (Custom Properties)

```css
:root {
  --primary-color: #3498db;
  --secondary-color: #2ecc71;
  --text-color: #333;
  --bg-color: #f5f5f5;
  --spacing: 1rem;
}

.button {
  background-color: var(--primary-color);
  color: white;
  padding: var(--spacing);
}

.button:hover {
  background-color: var(--secondary-color);
}

/* With fallback */
.element {
  color: var(--text-color, #000);
}

/* Nested variables */
:root {
  --base-size: 16px;
  --large-size: calc(var(--base-size) * 2);
}
```

### Background Properties

#### Background Color

```css
.element {
  background-color: #f5f5f5;
}
```

#### Background Image

```css
.hero {
  background-image: url('hero.jpg');
}

/* Multiple backgrounds */
.multi {
  background-image:
    url('overlay.png'),
    url('background.jpg');
}

/* Gradient backgrounds */
.gradient {
  background-image: linear-gradient(to right, #ff7e5f, #feb47b);
}
```

#### Background Size

```css
.cover {
  background-size: cover;           /* Scale to cover entire element */
}

.contain {
  background-size: contain;         /* Scale to fit within element */
}

.custom {
  background-size: 50% auto;        /* Width height */
  background-size: 200px 100px;     /* Specific dimensions */
}
```

#### Background Position

```css
.positioned {
  background-position: center;              /* Center both axes */
  background-position: top right;           /* Keywords */
  background-position: 50% 50%;             /* Percentage */
  background-position: 20px 30px;           /* Pixels from top-left */
  background-position: center bottom 20px;  /* Offset from edge */
}
```

#### Background Repeat

```css
.no-repeat {
  background-repeat: no-repeat;     /* Don't repeat */
}

.repeat-x {
  background-repeat: repeat-x;      /* Repeat horizontally */
}

.repeat-y {
  background-repeat: repeat-y;      /* Repeat vertically */
}

.repeat {
  background-repeat: repeat;        /* Repeat both (default) */
}

.space {
  background-repeat: space;         /* Repeat with spacing */
}

.round {
  background-repeat: round;         /* Repeat and stretch to fit */
}
```

#### Background Attachment

```css
.scroll {
  background-attachment: scroll;    /* Scroll with page (default) */
}

.fixed {
  background-attachment: fixed;     /* Fixed to viewport (parallax) */
}

.local {
  background-attachment: local;     /* Scroll with element content */
}
```

#### Background Shorthand

```css
.element {
  background: #f5f5f5 url('bg.jpg') no-repeat center/cover fixed;
  /* color image repeat position/size attachment */
}
```

### Gradients

#### Linear Gradients

```css
/* Basic gradient */
.gradient {
  background: linear-gradient(to right, #ff7e5f, #feb47b);
}

/* Angle-based */
.angle {
  background: linear-gradient(45deg, red, blue);
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

/* Multiple color stops */
.multi-stop {
  background: linear-gradient(
    to right,
    red 0%,
    yellow 25%,
    green 50%,
    blue 75%,
    purple 100%
  );
}

/* Sharp color transitions */
.stripes {
  background: linear-gradient(
    90deg,
    red 0% 25%,
    blue 25% 50%,
    green 50% 75%,
    yellow 75% 100%
  );
}
```

#### Radial Gradients

```css
/* Basic radial gradient */
.radial {
  background: radial-gradient(circle, #ff7e5f, #feb47b);
}

/* Ellipse shape */
.ellipse {
  background: radial-gradient(ellipse, red, blue);
}

/* Positioned */
.positioned {
  background: radial-gradient(circle at top right, red, blue);
}

/* With size */
.sized {
  background: radial-gradient(
    circle 100px at center,
    red, blue
  );
}
```

#### Conic Gradients

```css
/* Pie chart effect */
.conic {
  background: conic-gradient(red, yellow, green, blue, red);
}

/* From angle */
.from-angle {
  background: conic-gradient(from 45deg, red, blue);
}

/* Positioned */
.positioned {
  background: conic-gradient(at 25% 25%, red, blue);
}

/* Color wheel */
.color-wheel {
  background: conic-gradient(
    red, yellow, lime, aqua, blue, magenta, red
  );
  border-radius: 50%;
}
```

#### Repeating Gradients

```css
.repeating-linear {
  background: repeating-linear-gradient(
    45deg,
    #606dbc 0px,
    #606dbc 10px,
    #465298 10px,
    #465298 20px
  );
}

.repeating-radial {
  background: repeating-radial-gradient(
    circle,
    red 0px,
    red 10px,
    blue 10px,
    blue 20px
  );
}
```

---

## Borders and Effects

### Border Properties

#### Basic Borders

```css
.border {
  border-width: 1px;
  border-style: solid;
  border-color: #ddd;
}

/* Shorthand */
.border {
  border: 1px solid #ddd;
}

/* Individual sides */
.custom-border {
  border-top: 2px solid red;
  border-right: 1px dashed blue;
  border-bottom: 3px dotted green;
  border-left: 4px double yellow;
}
```

#### Border Styles

```css
.solid { border-style: solid; }       /* ──────── */
.dashed { border-style: dashed; }     /* - - - - */
.dotted { border-style: dotted; }     /* · · · · */
.double { border-style: double; }     /* ════════ */
.groove { border-style: groove; }     /* 3D groove */
.ridge { border-style: ridge; }       /* 3D ridge */
.inset { border-style: inset; }       /* 3D inset */
.outset { border-style: outset; }     /* 3D outset */
.none { border-style: none; }         /* No border */
.hidden { border-style: hidden; }     /* Hidden */
```

#### Border Radius

```css
/* All corners */
.rounded {
  border-radius: 8px;
}

/* Individual corners */
.custom-rounded {
  border-top-left-radius: 10px;
  border-top-right-radius: 20px;
  border-bottom-right-radius: 30px;
  border-bottom-left-radius: 40px;
}

/* Shorthand */
.corners {
  border-radius: 10px 20px 30px 40px;  /* TL TR BR BL */
}

/* Circle */
.circle {
  width: 100px;
  height: 100px;
  border-radius: 50%;
}

/* Pill shape */
.pill {
  border-radius: 9999px;  /* Very large value */
}

/* Elliptical corners */
.elliptical {
  border-radius: 50px / 25px;  /* horizontal / vertical */
}
```

#### Border Images

```css
.border-image {
  border: 10px solid transparent;
  border-image: url('border.png') 30 round;
}

.gradient-border {
  border: 5px solid;
  border-image: linear-gradient(45deg, red, blue) 1;
}
```

### Box Shadow

Add shadows to elements.

```css
/* box-shadow: offset-x offset-y blur-radius spread-radius color */

/* Basic shadow */
.shadow {
  box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.3);
}

/* No offset, just glow */
.glow {
  box-shadow: 0 0 10px rgba(0, 123, 255, 0.5);
}

/* Multiple shadows */
.multi-shadow {
  box-shadow:
    0 1px 3px rgba(0, 0, 0, 0.12),
    0 1px 2px rgba(0, 0, 0, 0.24);
}

/* Inset shadow */
.inset {
  box-shadow: inset 0 0 10px rgba(0, 0, 0, 0.5);
}

/* Material Design elevation */
.elevation-1 {
  box-shadow: 0 1px 3px rgba(0,0,0,0.12), 0 1px 2px rgba(0,0,0,0.24);
}

.elevation-2 {
  box-shadow: 0 3px 6px rgba(0,0,0,0.16), 0 3px 6px rgba(0,0,0,0.23);
}

.elevation-3 {
  box-shadow: 0 10px 20px rgba(0,0,0,0.19), 0 6px 6px rgba(0,0,0,0.23);
}

/* Neumorphism */
.neumorphic {
  box-shadow:
    5px 5px 10px #d1d9e6,
    -5px -5px 10px #ffffff;
}
```

### Text Shadow

```css
/* text-shadow: offset-x offset-y blur-radius color */

.text-shadow {
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
}

/* Glow effect */
.glow-text {
  text-shadow: 0 0 10px #fff, 0 0 20px #fff, 0 0 30px #e60073;
}

/* 3D effect */
.text-3d {
  text-shadow:
    1px 1px 0 #ccc,
    2px 2px 0 #c9c9c9,
    3px 3px 0 #bbb,
    4px 4px 0 #b9b9b9,
    5px 5px 0 #aaa,
    6px 6px 5px rgba(0,0,0,0.3);
}

/* Outline */
.text-outline {
  text-shadow:
    -1px -1px 0 #000,
    1px -1px 0 #000,
    -1px 1px 0 #000,
    1px 1px 0 #000;
}
```

### Opacity

```css
.semi-transparent {
  opacity: 0.5;  /* 0 (invisible) to 1 (opaque) */
}

.fade-out {
  opacity: 0;
}

/* Note: opacity affects entire element including children */
/* Use rgba/hsla for background-only transparency */
.bg-transparent {
  background-color: rgba(0, 0, 0, 0.5);
}
```

### Filters

Apply visual effects to elements.

```css
/* Blur */
.blur {
  filter: blur(5px);
}

/* Brightness */
.bright {
  filter: brightness(1.5);  /* 0 to ∞, 1 is normal */
}

.dark {
  filter: brightness(0.5);
}

/* Contrast */
.high-contrast {
  filter: contrast(2);
}

/* Grayscale */
.grayscale {
  filter: grayscale(100%);  /* 0% to 100% */
}

/* Sepia */
.sepia {
  filter: sepia(100%);
}

/* Hue rotation */
.hue-rotate {
  filter: hue-rotate(90deg);  /* 0deg to 360deg */
}

/* Invert */
.invert {
  filter: invert(100%);
}

/* Saturate */
.saturate {
  filter: saturate(200%);
}

/* Drop shadow */
.drop-shadow {
  filter: drop-shadow(2px 2px 5px rgba(0, 0, 0, 0.5));
}

/* Multiple filters */
.multi-filter {
  filter: blur(2px) brightness(1.2) contrast(1.1);
}

/* Hover effects */
img {
  filter: grayscale(100%);
  transition: filter 0.3s;
}

img:hover {
  filter: grayscale(0%);
}
```

### Backdrop Filter

Apply filters to element background.

```css
.glass-morphism {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.2);
}

.frosted-glass {
  backdrop-filter: blur(20px) saturate(180%);
  background-color: rgba(255, 255, 255, 0.5);
}
```

---

## Box Model

The CSS box model describes how elements are structured and sized.

### Box Model Diagram

```
┌─────────────────────────────────────────┐
│              MARGIN (transparent)        │
│  ┌───────────────────────────────────┐  │
│  │         BORDER                    │  │
│  │  ┌─────────────────────────────┐ │  │
│  │  │      PADDING                │ │  │
│  │  │  ┌───────────────────────┐  │ │  │
│  │  │  │                       │  │ │  │
│  │  │  │      CONTENT          │  │ │  │
│  │  │  │    (width x height)   │  │ │  │
│  │  │  │                       │  │ │  │
│  │  │  └───────────────────────┘  │ │  │
│  │  │                             │ │  │
│  │  └─────────────────────────────┘ │  │
│  │                                   │  │
│  └───────────────────────────────────┘  │
│                                          │
└─────────────────────────────────────────┘
```

### Box Sizing

```css
/* Default (content-box) */
.content-box {
  box-sizing: content-box;
  /* width/height apply to content only */
  /* Total width = width + padding + border */
}

/* Border-box (recommended) */
.border-box {
  box-sizing: border-box;
  /* width/height include padding and border */
  /* Total width = width */
}

/* Apply to all elements */
* {
  box-sizing: border-box;
}
```

**Example:**

```css
/* Content-box (default) */
.box1 {
  box-sizing: content-box;
  width: 200px;
  padding: 20px;
  border: 5px solid black;
  /* Total width: 200 + 40 + 10 = 250px */
}

/* Border-box */
.box2 {
  box-sizing: border-box;
  width: 200px;
  padding: 20px;
  border: 5px solid black;
  /* Total width: 200px (includes padding and border) */
}
```

### Width and Height

```css
.element {
  width: 300px;
  height: 200px;
}

/* Min/Max constraints */
.flexible {
  width: 100%;
  max-width: 1200px;  /* Won't exceed this */
  min-width: 300px;   /* Won't shrink below this */

  height: auto;
  min-height: 400px;  /* At least this tall */
  max-height: 800px;  /* Won't exceed this */
}

/* Viewport units */
.full-screen {
  width: 100vw;   /* Full viewport width */
  height: 100vh;  /* Full viewport height */
}

/* Fit content */
.auto-width {
  width: fit-content;
  width: max-content;
  width: min-content;
}
```

### Padding

Space inside the element, between content and border.

```css
/* All sides */
.padding-all {
  padding: 20px;
}

/* Vertical | Horizontal */
.padding-vh {
  padding: 10px 20px;
}

/* Top | Horizontal | Bottom */
.padding-thb {
  padding: 10px 20px 15px;
}

/* Top | Right | Bottom | Left (clockwise) */
.padding-trbl {
  padding: 10px 20px 15px 5px;
}

/* Individual sides */
.padding-individual {
  padding-top: 10px;
  padding-right: 20px;
  padding-bottom: 15px;
  padding-left: 5px;
}
```

### Margin

Space outside the element, between border and other elements.

```css
/* Same syntax as padding */
.margin {
  margin: 20px;
  margin: 10px 20px;
  margin: 10px 20px 15px;
  margin: 10px 20px 15px 5px;
}

/* Individual sides */
.margin-individual {
  margin-top: 20px;
  margin-right: 15px;
  margin-bottom: 20px;
  margin-left: 15px;
}

/* Centering horizontally */
.center {
  width: 800px;
  margin: 0 auto;  /* Top/bottom: 0, Left/right: auto */
}

/* Negative margins */
.overlap {
  margin-top: -20px;  /* Pull up */
  margin-left: -10px; /* Pull left */
}

/* Auto margins */
.push-right {
  margin-left: auto;
}

.push-apart {
  display: flex;
}
.push-apart > *:last-child {
  margin-left: auto;  /* Pushes last item to right */
}
```

### Margin Collapse

Vertical margins between elements collapse to the larger of the two.

```css
.element1 {
  margin-bottom: 30px;
}

.element2 {
  margin-top: 20px;
}

/* Actual gap between elements: 30px (not 50px) */
```

**Preventing Margin Collapse:**
- Use padding instead of margin
- Add border or padding to parent
- Use flexbox or grid (doesn't collapse)
- Use `overflow: auto` on parent

---

## Font Properties

Advanced font styling and web fonts.

### Font Family

```css
/* System fonts stack */
body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI',
               Roboto, Oxygen, Ubuntu, Cantarell,
               'Helvetica Neue', sans-serif;
}

/* Font categories */
.serif { font-family: Georgia, Times, serif; }
.sans-serif { font-family: Arial, Helvetica, sans-serif; }
.monospace { font-family: 'Courier New', Courier, monospace; }
.cursive { font-family: 'Comic Sans MS', cursive; }
.fantasy { font-family: Impact, fantasy; }
```

### Web Fonts

#### Google Fonts

```html
<!-- In HTML -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;700&display=swap" rel="stylesheet">
```

```css
/* In CSS */
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;700&display=swap');

body {
  font-family: 'Roboto', sans-serif;
}
```

#### Custom Fonts (@font-face)

```css
@font-face {
  font-family: 'MyCustomFont';
  src: url('fonts/mycustomfont.woff2') format('woff2'),
       url('fonts/mycustomfont.woff') format('woff');
  font-weight: normal;
  font-style: normal;
  font-display: swap;  /* Improve loading performance */
}

.custom {
  font-family: 'MyCustomFont', sans-serif;
}
```

### Font Size

```css
/* Absolute units */
.pixels { font-size: 16px; }
.points { font-size: 12pt; }

/* Relative units (recommended) */
.em { font-size: 1.5em; }      /* 1.5x parent size */
.rem { font-size: 1.5rem; }    /* 1.5x root size */
.percent { font-size: 150%; }  /* 1.5x parent size */

/* Viewport units */
.viewport { font-size: 5vw; }  /* 5% of viewport width */

/* Keywords */
.xx-small { font-size: xx-small; }
.x-small { font-size: x-small; }
.small { font-size: small; }
.medium { font-size: medium; }
.large { font-size: large; }
.x-large { font-size: x-large; }
.xx-large { font-size: xx-large; }

/* Responsive with clamp */
.responsive {
  font-size: clamp(1rem, 2.5vw, 2rem);
}
```

### Font Weight

```css
.thin { font-weight: 100; }
.extra-light { font-weight: 200; }
.light { font-weight: 300; }
.normal { font-weight: 400; }  /* or 'normal' */
.medium { font-weight: 500; }
.semi-bold { font-weight: 600; }
.bold { font-weight: 700; }     /* or 'bold' */
.extra-bold { font-weight: 800; }
.black { font-weight: 900; }

/* Relative */
.bolder { font-weight: bolder; }  /* Relative to parent */
.lighter { font-weight: lighter; }
```

### Font Style and Variant

```css
.italic { font-style: italic; }
.oblique { font-style: oblique; }
.normal { font-style: normal; }

/* Small caps */
.small-caps { font-variant: small-caps; }
```

### Line Height

```css
/* Unitless (recommended) */
.unitless { line-height: 1.6; }  /* 1.6x font size */

/* With units */
.pixels { line-height: 24px; }
.em { line-height: 1.5em; }
.percent { line-height: 150%; }

/* Best practice for readability */
p {
  line-height: 1.6;  /* 1.5 to 1.8 for body text */
}
```

### Letter and Word Spacing

```css
/* Letter spacing (tracking) */
.spaced { letter-spacing: 2px; }
.tight { letter-spacing: -1px; }
.normal { letter-spacing: normal; }
.loose { letter-spacing: 0.1em; }

/* Word spacing */
.word-spaced { word-spacing: 5px; }
.word-tight { word-spacing: -2px; }
```

### Text Properties

```css
/* Alignment */
.left { text-align: left; }
.center { text-align: center; }
.right { text-align: right; }
.justify { text-align: justify; }

/* Decoration */
.underline { text-decoration: underline; }
.overline { text-decoration: overline; }
.line-through { text-decoration: line-through; }
.none { text-decoration: none; }

/* Decoration style */
.wavy { text-decoration: underline wavy red; }
.dotted { text-decoration: underline dotted blue; }

/* Transform */
.uppercase { text-transform: uppercase; }
.lowercase { text-transform: lowercase; }
.capitalize { text-transform: capitalize; }

/* Indentation */
.indent { text-indent: 2em; }

/* White space */
.nowrap { white-space: nowrap; }     /* No wrapping */
.pre { white-space: pre; }           /* Preserve whitespace */
.pre-wrap { white-space: pre-wrap; } /* Preserve + wrap */

/* Text overflow */
.ellipsis {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* Word break */
.break-word { word-break: break-word; }
.break-all { word-break: break-all; }
```

---

## Transitions

Smooth animations between CSS property values.

### Basic Syntax

```css
.element {
  transition: property duration timing-function delay;
}
```

### Transition Properties

```css
/* Single property */
.button {
  background-color: blue;
  transition: background-color 0.3s ease;
}

.button:hover {
  background-color: red;
}

/* Multiple properties */
.box {
  width: 100px;
  height: 100px;
  background: blue;
  transition: width 0.3s, height 0.3s, background 0.5s;
}

.box:hover {
  width: 200px;
  height: 200px;
  background: red;
}

/* All properties */
.all-transition {
  transition: all 0.3s ease;
}

/* Individual properties */
.detailed {
  transition-property: opacity, transform;
  transition-duration: 0.3s, 0.5s;
  transition-timing-function: ease, ease-in-out;
  transition-delay: 0s, 0.1s;
}
```

### Timing Functions

```css
.linear { transition-timing-function: linear; }
.ease { transition-timing-function: ease; }           /* Default */
.ease-in { transition-timing-function: ease-in; }
.ease-out { transition-timing-function: ease-out; }
.ease-in-out { transition-timing-function: ease-in-out; }

/* Cubic bezier (custom) */
.custom {
  transition-timing-function: cubic-bezier(0.42, 0, 0.58, 1);
}

/* Steps */
.steps {
  transition-timing-function: steps(4, end);
}
```

### Common Transition Examples

```css
/* Button hover */
.button {
  background-color: #3498db;
  color: white;
  padding: 10px 20px;
  border: none;
  transition: background-color 0.3s, transform 0.1s;
}

.button:hover {
  background-color: #2980b9;
  transform: translateY(-2px);
}

.button:active {
  transform: translateY(0);
}

/* Fade in/out */
.fade {
  opacity: 0;
  transition: opacity 0.5s;
}

.fade.visible {
  opacity: 1;
}

/* Slide in */
.slide {
  transform: translateX(-100%);
  transition: transform 0.3s ease-out;
}

.slide.active {
  transform: translateX(0);
}

/* Scale on hover */
.scale {
  transition: transform 0.3s;
}

.scale:hover {
  transform: scale(1.1);
}

/* Color change */
.color-change {
  color: black;
  transition: color 0.3s;
}

.color-change:hover {
  color: #3498db;
}
```

---

## Animations

Create complex, keyframe-based animations.

### Keyframes

```css
@keyframes animation-name {
  from {
    /* Starting state */
  }
  to {
    /* Ending state */
  }
}

/* Or with percentages */
@keyframes animation-name {
  0% {
    /* Starting state */
  }
  50% {
    /* Midpoint */
  }
  100% {
    /* Ending state */
  }
}
```

### Animation Properties

```css
.element {
  animation-name: slideIn;
  animation-duration: 1s;
  animation-timing-function: ease;
  animation-delay: 0s;
  animation-iteration-count: 1;
  animation-direction: normal;
  animation-fill-mode: none;
  animation-play-state: running;
}

/* Shorthand */
.element {
  animation: slideIn 1s ease 0s 1 normal none running;
  /* name duration timing delay iteration direction fill-mode play-state */
}

/* Common shorthand */
.element {
  animation: slideIn 1s ease-in-out;
}
```

### Animation Examples

#### Fade In

```css
@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

.fade-in {
  animation: fadeIn 1s ease-in;
}
```

#### Slide In

```css
@keyframes slideIn {
  from {
    transform: translateX(-100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

.slide-in {
  animation: slideIn 0.5s ease-out;
}
```

#### Bounce

```css
@keyframes bounce {
  0%, 20%, 50%, 80%, 100% {
    transform: translateY(0);
  }
  40% {
    transform: translateY(-30px);
  }
  60% {
    transform: translateY(-15px);
  }
}

.bounce {
  animation: bounce 2s infinite;
}
```

#### Pulse

```css
@keyframes pulse {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.1);
  }
  100% {
    transform: scale(1);
  }
}

.pulse {
  animation: pulse 1.5s infinite;
}
```

#### Spin

```css
@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

.spinner {
  animation: spin 1s linear infinite;
}
```

#### Shake

```css
@keyframes shake {
  0%, 100% {
    transform: translateX(0);
  }
  10%, 30%, 50%, 70%, 90% {
    transform: translateX(-10px);
  }
  20%, 40%, 60%, 80% {
    transform: translateX(10px);
  }
}

.shake {
  animation: shake 0.5s;
}
```

#### Color Change

```css
@keyframes colorChange {
  0% { background-color: red; }
  25% { background-color: yellow; }
  50% { background-color: blue; }
  75% { background-color: green; }
  100% { background-color: red; }
}

.color-animation {
  animation: colorChange 4s infinite;
}
```

### Animation Properties Detail

#### Animation Iteration Count

```css
.once { animation-iteration-count: 1; }
.three-times { animation-iteration-count: 3; }
.infinite { animation-iteration-count: infinite; }
```

#### Animation Direction

```css
.normal { animation-direction: normal; }           /* 0% → 100% */
.reverse { animation-direction: reverse; }         /* 100% → 0% */
.alternate { animation-direction: alternate; }     /* 0% → 100% → 0% */
.alternate-reverse { animation-direction: alternate-reverse; }
```

#### Animation Fill Mode

```css
.none { animation-fill-mode: none; }              /* Default state before/after */
.forwards { animation-fill-mode: forwards; }      /* Keep end state */
.backwards { animation-fill-mode: backwards; }    /* Apply start state during delay */
.both { animation-fill-mode: both; }              /* Both forwards and backwards */
```

#### Animation Play State

```css
.element {
  animation: spin 2s linear infinite;
}

.element.paused {
  animation-play-state: paused;
}

.element:hover {
  animation-play-state: paused;
}
```

### Multiple Animations

```css
.element {
  animation:
    fadeIn 1s ease-out,
    slideIn 1s ease-out,
    pulse 2s ease-in-out 1s infinite;
}
```

---

## Transform Property

Manipulate elements in 2D and 3D space.

### 2D Transforms

```css
/* Translate (move) */
.translate {
  transform: translate(50px, 100px);  /* X, Y */
  transform: translateX(50px);
  transform: translateY(100px);
}

/* Scale */
.scale {
  transform: scale(1.5);           /* Uniform scaling */
  transform: scale(2, 0.5);        /* X, Y */
  transform: scaleX(2);
  transform: scaleY(0.5);
}

/* Rotate */
.rotate {
  transform: rotate(45deg);
  transform: rotate(-90deg);
}

/* Skew */
.skew {
  transform: skew(20deg, 10deg);   /* X, Y */
  transform: skewX(20deg);
  transform: skewY(10deg);
}

/* Multiple transforms */
.multi {
  transform: translate(50px, 50px) rotate(45deg) scale(1.5);
}
```

### 3D Transforms

```css
/* 3D Translate */
.translate-3d {
  transform: translate3d(50px, 100px, 75px);  /* X, Y, Z */
  transform: translateZ(100px);
}

/* 3D Rotate */
.rotate-3d {
  transform: rotateX(45deg);
  transform: rotateY(45deg);
  transform: rotateZ(45deg);  /* Same as rotate() */
  transform: rotate3d(1, 1, 1, 45deg);  /* X, Y, Z, angle */
}

/* 3D Scale */
.scale-3d {
  transform: scale3d(2, 2, 2);
  transform: scaleZ(2);
}

/* Perspective */
.perspective-container {
  perspective: 1000px;
}

.card {
  transform-style: preserve-3d;
  transition: transform 0.6s;
}

.card:hover {
  transform: rotateY(180deg);
}
```

### Transform Origin

```css
.element {
  transform-origin: center;           /* Default */
  transform-origin: top left;
  transform-origin: 50% 50%;
  transform-origin: 100px 50px;
}

/* Rotate around different points */
.rotate-corner {
  transform-origin: top right;
  transform: rotate(45deg);
}
```

---

## Summary

| Feature | Key Points |
|---------|------------|
| Colors | Hex, RGB, RGBA, HSL, HSLA, CSS variables |
| Backgrounds | Images, gradients, multiple layers |
| Borders | Styles, radius, images, shadows |
| Box Model | Content, padding, border, margin; box-sizing |
| Fonts | Web fonts, @font-face, size, weight, line-height |
| Transitions | Smooth property changes, timing functions |
| Animations | Keyframes, multiple animations, fill-mode |
| Transforms | 2D/3D translate, rotate, scale, skew |

---

## Best Practices

1. **Use CSS Variables** for consistent theming
2. **Box-sizing: border-box** for predictable sizing
3. **Optimize Animations** - use transform and opacity (GPU-accelerated)
4. **Avoid animating layout properties** (width, height, margin) - use transform instead
5. **Use `will-change`** sparingly for performance hints
6. **Preload web fonts** to prevent FOIT (Flash of Invisible Text)
7. **Test across browsers** for vendor prefix requirements
8. **Use transitions for simple effects**, animations for complex sequences

---

## Next Steps

You've completed the CSS3 module! You now have a solid understanding of:
- CSS fundamentals and selectors
- Layout techniques (Flexbox and Grid)
- Responsive design principles
- Advanced visual effects and animations

**Continue your learning with:**
- [Module 6: JavaScript](../../06-javascript/) - Add interactivity to your styled pages
- Practice by building complete responsive websites
- Explore CSS frameworks like Bootstrap or Tailwind
- Learn CSS preprocessors (Sass, Less)

---

**Congratulations on completing the CSS3 module!**
