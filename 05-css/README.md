# CSS3

## Overview
Cascading Style Sheets (CSS3) is used to style and layout web pages. This module covers modern CSS features including flexbox, grid, responsive design, and animations.

## Learning Objectives
- Style HTML elements effectively
- Create responsive layouts with Flexbox and Grid
- Implement responsive web design
- Use CSS3 advanced features
- Apply modern CSS techniques

## Topics Covered

### 1. [Introduction to CSS3](./topics/01-introduction-to-css3.md)
- CSS Syntax
- Ways to apply CSS (inline, internal, external)
- CSS Selectors (basic, combinator, pseudo-class, pseudo-element)
- Styling Text and Fonts
- Selector Specificity

### 2. [Layout with CSS](./topics/02-layout-with-css.md)
- Display Property (block, inline, inline-block, flex, grid)
- Position Property (static, relative, absolute, fixed, sticky)
- Float and Clear
- Flexbox Layout
  - flex-direction, justify-content, align-items
  - flex-wrap, align-content
  - flex-grow, flex-shrink, flex-basis
- CSS Grid Layout
  - grid-template-columns/rows
  - grid-gap, grid-template-areas
  - Grid item placement and alignment

### 3. [Responsive Web Design](./topics/03-responsive-web-design.md)
- Media Queries and Media Features
- Breakpoints and Breakpoint Systems
- Mobile-first Design Approach
- Responsive Units (rem, em, %, vw/vh, clamp)
- Flexible Images and Picture Element

### 4. [Advanced Features](./topics/04-advanced-features.md)
- Colors and Backgrounds
  - Color formats (Hex, RGB, HSL, CSS Variables)
  - Gradients (linear, radial, conic)
- Borders and Effects
  - Border styles and radius
  - Box shadows and text shadows
  - Filters and backdrop filters
- Box Model
  - Content, Padding, Border, Margin
  - Box-sizing property
- Font Properties
  - Web fonts and @font-face
  - Font weight, size, line-height
- Transitions and Animations
  - Transition properties and timing functions
  - Keyframe animations
  - Transform property (2D and 3D)

## Key Concepts

### CSS Box Model
Every element is a box with: Content → Padding → Border → Margin

### Flexbox
Modern layout system for one-dimensional layouts
- `display: flex`
- `justify-content`, `align-items`
- `flex-direction`, `flex-wrap`

### CSS Grid
Two-dimensional layout system
- `display: grid`
- `grid-template-columns`, `grid-template-rows`
- `grid-gap`

### Responsive Design
- Mobile-first approach
- Media queries: `@media (min-width: 768px)`
- Flexible units: `em`, `rem`, `%`, `vw`, `vh`

## Exercises
See the [exercises](./exercises/) directory for styling challenges.

## Additional Resources
- [MDN CSS Documentation](https://developer.mozilla.org/en-US/docs/Web/CSS)
- [CSS-Tricks](https://css-tricks.com/)
- [Flexbox Froggy](https://flexboxfroggy.com/) - Game to learn Flexbox
- [Grid Garden](https://cssgridgarden.com/) - Game to learn CSS Grid

## Next Steps
Proceed to [Module 6: JavaScript](../06-javascript/)

---
**Time Estimate:** 2 days | **Difficulty:** Beginner to Intermediate | **Prerequisites:** HTML5
