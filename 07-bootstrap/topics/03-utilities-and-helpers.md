# Bootstrap Utilities and Helpers

## What are Utility Classes?

Utility classes are single-purpose CSS classes that apply specific styles without requiring custom CSS. They follow a predictable naming pattern and can be combined to create complex designs quickly.

### Benefits of Utilities

- **Rapid Development**: Apply styles without writing CSS
- **Consistency**: Standardized spacing, colors, and sizing
- **Maintainability**: Clear, self-documenting class names
- **Responsive**: Built-in responsive variants
- **Composability**: Mix and match for complex designs

### Utility vs Component Classes

```html
<!-- Component: Pre-built, multiple styles -->
<button class="btn btn-primary">Button</button>

<!-- Utilities: Single-purpose, combinable -->
<div class="p-3 mb-2 bg-primary text-white rounded">
  Custom styled div
</div>

<!-- Combining both -->
<button class="btn btn-primary mt-3 px-4 shadow">
  Enhanced Button
</button>
```

---

## Spacing Utilities

Bootstrap provides comprehensive spacing utilities for margins and padding.

### Spacing Scale

Bootstrap uses a spacing scale based on 1rem (16px default):

| Class | Size | Pixels (default) |
|-------|------|------------------|
| `0` | 0 | 0px |
| `1` | 0.25rem | 4px |
| `2` | 0.5rem | 8px |
| `3` | 1rem | 16px |
| `4` | 1.5rem | 24px |
| `5` | 3rem | 48px |
| `auto` | auto | auto |

### Margin Classes

Format: `m{sides}-{size}`

```html
<!-- All sides -->
<div class="m-0">No margin</div>
<div class="m-3">1rem margin all sides</div>
<div class="m-5">3rem margin all sides</div>

<!-- Specific sides -->
<div class="mt-3">Margin top</div>
<div class="mb-4">Margin bottom</div>
<div class="ms-2">Margin start (left in LTR)</div>
<div class="me-2">Margin end (right in LTR)</div>

<!-- Horizontal/Vertical -->
<div class="mx-auto">Horizontal margin auto (centering)</div>
<div class="my-4">Vertical margin</div>

<!-- Negative margins -->
<div class="mt-n3">Negative margin top</div>
```

### Padding Classes

Format: `p{sides}-{size}`

```html
<!-- All sides -->
<div class="p-0">No padding</div>
<div class="p-3">1rem padding all sides</div>
<div class="p-5">3rem padding all sides</div>

<!-- Specific sides -->
<div class="pt-3">Padding top</div>
<div class="pb-4">Padding bottom</div>
<div class="ps-2">Padding start (left in LTR)</div>
<div class="pe-2">Padding end (right in LTR)</div>

<!-- Horizontal/Vertical -->
<div class="px-3">Horizontal padding</div>
<div class="py-4">Vertical padding</div>
```

### Spacing Abbreviations

| Abbreviation | Side |
|--------------|------|
| `t` | top |
| `b` | bottom |
| `s` | start (left in LTR, right in RTL) |
| `e` | end (right in LTR, left in RTL) |
| `x` | left and right |
| `y` | top and bottom |
| (none) | all sides |

### Responsive Spacing

```html
<!-- Different spacing at different breakpoints -->
<div class="p-2 p-md-3 p-lg-4 p-xl-5">
  Responsive padding
</div>

<!-- Mobile: 8px, Tablet: 16px, Desktop: 24px, Large: 48px -->
<div class="mt-2 mt-md-3 mt-lg-4 mt-xl-5">
  Responsive margin-top
</div>
```

---

## Color Utilities

### Text Colors

```html
<!-- Theme colors -->
<p class="text-primary">Primary text</p>
<p class="text-secondary">Secondary text</p>
<p class="text-success">Success text</p>
<p class="text-danger">Danger text</p>
<p class="text-warning">Warning text</p>
<p class="text-info">Info text</p>
<p class="text-light bg-dark">Light text</p>
<p class="text-dark">Dark text</p>

<!-- Grayscale -->
<p class="text-body">Body text (default)</p>
<p class="text-muted">Muted text</p>
<p class="text-black">Black text</p>
<p class="text-white bg-dark">White text</p>
<p class="text-black-50">50% opacity black</p>
<p class="text-white-50 bg-dark">50% opacity white</p>

<!-- Emphasis -->
<p class="text-body-emphasis">Body emphasis</p>
<p class="text-body-secondary">Body secondary</p>
<p class="text-body-tertiary">Body tertiary</p>
```

### Background Colors

```html
<!-- Theme backgrounds -->
<div class="bg-primary text-white p-3">Primary background</div>
<div class="bg-secondary text-white p-3">Secondary background</div>
<div class="bg-success text-white p-3">Success background</div>
<div class="bg-danger text-white p-3">Danger background</div>
<div class="bg-warning text-dark p-3">Warning background</div>
<div class="bg-info text-dark p-3">Info background</div>
<div class="bg-light text-dark p-3">Light background</div>
<div class="bg-dark text-white p-3">Dark background</div>

<!-- Subtle backgrounds -->
<div class="bg-primary-subtle p-3">Primary subtle</div>
<div class="bg-success-subtle p-3">Success subtle</div>
<div class="bg-danger-subtle p-3">Danger subtle</div>

<!-- Opacity variants -->
<div class="bg-primary bg-opacity-75 p-3">75% opacity</div>
<div class="bg-primary bg-opacity-50 p-3">50% opacity</div>
<div class="bg-primary bg-opacity-25 p-3">25% opacity</div>
<div class="bg-primary bg-opacity-10 p-3">10% opacity</div>
```

### Gradient Backgrounds

```html
<div class="bg-primary bg-gradient text-white p-3">
  Primary gradient
</div>

<div class="bg-success bg-gradient text-white p-3">
  Success gradient
</div>
```

### Color Reference Table

| Class | Color | Usage |
|-------|-------|-------|
| `primary` | Blue | Primary actions, links |
| `secondary` | Gray | Secondary actions |
| `success` | Green | Success messages, confirmations |
| `danger` | Red | Errors, destructive actions |
| `warning` | Yellow | Warnings, cautions |
| `info` | Cyan | Informational messages |
| `light` | Light gray | Subtle backgrounds |
| `dark` | Dark gray | Dark themes, footers |

---

## Display Utilities

Control element display property:

```html
<!-- Display values -->
<div class="d-none">Hidden (display: none)</div>
<div class="d-inline">Inline</div>
<div class="d-inline-block">Inline block</div>
<div class="d-block">Block</div>
<div class="d-table">Table</div>
<div class="d-table-cell">Table cell</div>
<div class="d-flex">Flex container</div>
<div class="d-inline-flex">Inline flex</div>
<div class="d-grid">Grid container</div>

<!-- Responsive display -->
<div class="d-none d-md-block">
  Hidden on mobile, visible on tablet and up
</div>

<div class="d-block d-md-none">
  Visible on mobile, hidden on tablet and up
</div>

<div class="d-none d-lg-flex">
  Hidden until large screens, then flex
</div>
```

### Print Display Utilities

```html
<!-- Control display when printing -->
<div class="d-print-none">Won't print</div>
<div class="d-none d-print-block">Only visible when printing</div>
<div class="d-print-inline">Inline when printing</div>
```

---

## Flexbox Utilities

### Flex Container

```html
<!-- Enable flexbox -->
<div class="d-flex">Flex container</div>
<div class="d-inline-flex">Inline flex container</div>

<!-- Direction -->
<div class="d-flex flex-row">Row (default)</div>
<div class="d-flex flex-row-reverse">Row reversed</div>
<div class="d-flex flex-column">Column</div>
<div class="d-flex flex-column-reverse">Column reversed</div>

<!-- Wrapping -->
<div class="d-flex flex-wrap">Wrap items</div>
<div class="d-flex flex-nowrap">No wrap (default)</div>
<div class="d-flex flex-wrap-reverse">Wrap reverse</div>
```

### Justify Content (Horizontal Alignment)

```html
<div class="d-flex justify-content-start">Start (default)</div>
<div class="d-flex justify-content-end">End</div>
<div class="d-flex justify-content-center">Center</div>
<div class="d-flex justify-content-between">Space between</div>
<div class="d-flex justify-content-around">Space around</div>
<div class="d-flex justify-content-evenly">Space evenly</div>
```

### Align Items (Vertical Alignment)

```html
<div class="d-flex align-items-start">Start</div>
<div class="d-flex align-items-end">End</div>
<div class="d-flex align-items-center">Center</div>
<div class="d-flex align-items-baseline">Baseline</div>
<div class="d-flex align-items-stretch">Stretch (default)</div>
```

### Align Self (Individual Item)

```html
<div class="d-flex">
  <div class="align-self-start">Start</div>
  <div class="align-self-center">Center</div>
  <div class="align-self-end">End</div>
</div>
```

### Flex Item Properties

```html
<!-- Grow and shrink -->
<div class="d-flex">
  <div class="flex-grow-1">Grows to fill space</div>
  <div class="flex-shrink-1">Can shrink</div>
  <div class="flex-shrink-0">Won't shrink</div>
</div>

<!-- Fill -->
<div class="d-flex">
  <div class="flex-fill">Equal width item</div>
  <div class="flex-fill">Equal width item</div>
  <div class="flex-fill">Equal width item</div>
</div>
```

### Practical Flexbox Examples

```html
<!-- Center content vertically and horizontally -->
<div class="d-flex justify-content-center align-items-center" style="height: 200px;">
  <div>Perfectly centered</div>
</div>

<!-- Navigation bar layout -->
<nav class="d-flex justify-content-between align-items-center p-3 bg-dark text-white">
  <div class="brand">Logo</div>
  <div class="d-flex gap-3">
    <a href="#" class="text-white">Home</a>
    <a href="#" class="text-white">About</a>
    <a href="#" class="text-white">Contact</a>
  </div>
</nav>

<!-- Card with footer at bottom -->
<div class="card d-flex flex-column" style="height: 300px;">
  <div class="card-body flex-grow-1">
    Content that grows
  </div>
  <div class="card-footer">
    Footer stays at bottom
  </div>
</div>
```

---

## Typography Utilities

### Text Alignment

```html
<p class="text-start">Left aligned (default)</p>
<p class="text-center">Center aligned</p>
<p class="text-end">Right aligned</p>

<!-- Responsive alignment -->
<p class="text-start text-md-center text-lg-end">
  Changes alignment at breakpoints
</p>
```

### Text Transform

```html
<p class="text-lowercase">LOWERCASED TEXT</p>
<p class="text-uppercase">uppercased text</p>
<p class="text-capitalize">capitalized text</p>
```

### Font Size

```html
<p class="fs-1">Font size 1 (largest)</p>
<p class="fs-2">Font size 2</p>
<p class="fs-3">Font size 3</p>
<p class="fs-4">Font size 4</p>
<p class="fs-5">Font size 5</p>
<p class="fs-6">Font size 6 (smallest)</p>
```

### Font Weight

```html
<p class="fw-light">Light weight</p>
<p class="fw-lighter">Lighter weight</p>
<p class="fw-normal">Normal weight</p>
<p class="fw-bold">Bold weight</p>
<p class="fw-bolder">Bolder weight</p>
<p class="fw-semibold">Semibold weight</p>
```

### Font Style

```html
<p class="fst-italic">Italic text</p>
<p class="fst-normal">Normal text</p>
```

### Line Height

```html
<p class="lh-1">Line height 1</p>
<p class="lh-sm">Small line height</p>
<p class="lh-base">Base line height</p>
<p class="lh-lg">Large line height</p>
```

### Text Decoration

```html
<p class="text-decoration-underline">Underlined text</p>
<p class="text-decoration-line-through">Strikethrough text</p>
<p class="text-decoration-none">No decoration (removes link underline)</p>
```

### Text Wrapping

```html
<div class="text-wrap" style="width: 100px;">
  This text will wrap
</div>

<div class="text-nowrap" style="width: 100px;">
  This text won't wrap
</div>

<div class="text-break" style="width: 100px;">
  Thisverylongwordwillbreakappropriately
</div>
```

---

## Border Utilities

### Adding Borders

```html
<!-- All sides -->
<div class="border">Border all sides</div>

<!-- Specific sides -->
<div class="border-top">Top border</div>
<div class="border-end">Right border</div>
<div class="border-bottom">Bottom border</div>
<div class="border-start">Left border</div>

<!-- Remove borders -->
<div class="border border-top-0">No top border</div>
<div class="border-0">No border</div>
```

### Border Colors

```html
<div class="border border-primary">Primary border</div>
<div class="border border-secondary">Secondary border</div>
<div class="border border-success">Success border</div>
<div class="border border-danger">Danger border</div>
<div class="border border-warning">Warning border</div>
<div class="border border-info">Info border</div>
<div class="border border-light">Light border</div>
<div class="border border-dark">Dark border</div>
<div class="border border-white">White border</div>
```

### Border Width

```html
<div class="border border-1">1px border</div>
<div class="border border-2">2px border</div>
<div class="border border-3">3px border</div>
<div class="border border-4">4px border</div>
<div class="border border-5">5px border</div>
```

### Border Radius (Rounded Corners)

```html
<!-- Rounded corners -->
<div class="rounded">Default rounded</div>
<div class="rounded-0">No rounded corners</div>
<div class="rounded-1">Small rounded</div>
<div class="rounded-2">Medium rounded</div>
<div class="rounded-3">Large rounded</div>
<div class="rounded-4">Extra large rounded</div>
<div class="rounded-5">Super rounded</div>

<!-- Specific corners -->
<div class="rounded-top">Top corners rounded</div>
<div class="rounded-end">Right corners rounded</div>
<div class="rounded-bottom">Bottom corners rounded</div>
<div class="rounded-start">Left corners rounded</div>

<!-- Special shapes -->
<div class="rounded-circle">Circle (for square elements)</div>
<div class="rounded-pill">Pill shape</div>
```

---

## Sizing Utilities

### Width

```html
<div class="w-25">Width 25%</div>
<div class="w-50">Width 50%</div>
<div class="w-75">Width 75%</div>
<div class="w-100">Width 100%</div>
<div class="w-auto">Width auto</div>

<!-- Min/Max width -->
<div class="mw-100">Max width 100%</div>
<div class="min-vw-100">Min width 100vw</div>
<div class="vw-100">Width 100vw</div>
```

### Height

```html
<div class="h-25">Height 25%</div>
<div class="h-50">Height 50%</div>
<div class="h-75">Height 75%</div>
<div class="h-100">Height 100%</div>
<div class="h-auto">Height auto</div>

<!-- Min/Max height -->
<div class="mh-100">Max height 100%</div>
<div class="min-vh-100">Min height 100vh</div>
<div class="vh-100">Height 100vh</div>
```

---

## Shadow Utilities

```html
<div class="shadow-none">No shadow</div>
<div class="shadow-sm">Small shadow</div>
<div class="shadow">Regular shadow</div>
<div class="shadow-lg">Large shadow</div>
```

---

## Opacity Utilities

```html
<div class="opacity-100">100% opacity</div>
<div class="opacity-75">75% opacity</div>
<div class="opacity-50">50% opacity</div>
<div class="opacity-25">25% opacity</div>
<div class="opacity-0">0% opacity (invisible but takes space)</div>
```

---

## Position Utilities

```html
<!-- Position type -->
<div class="position-static">Static (default)</div>
<div class="position-relative">Relative</div>
<div class="position-absolute">Absolute</div>
<div class="position-fixed">Fixed</div>
<div class="position-sticky">Sticky</div>

<!-- Position values -->
<div class="position-absolute top-0 start-0">Top left</div>
<div class="position-absolute top-0 end-0">Top right</div>
<div class="position-absolute bottom-0 start-0">Bottom left</div>
<div class="position-absolute bottom-0 end-0">Bottom right</div>

<!-- Center positioning -->
<div class="position-absolute top-50 start-50 translate-middle">
  Perfectly centered
</div>
```

---

## Visibility Utilities

```html
<!-- Visibility (element takes space) -->
<div class="visible">Visible</div>
<div class="invisible">Invisible but takes space</div>

<!-- Display (element doesn't take space) -->
<div class="d-none">Not displayed</div>
<div class="d-block">Displayed</div>
```

---

## Overflow Utilities

```html
<div class="overflow-auto">Auto overflow</div>
<div class="overflow-hidden">Hidden overflow</div>
<div class="overflow-visible">Visible overflow</div>
<div class="overflow-scroll">Scrollable overflow</div>

<!-- Specific directions -->
<div class="overflow-x-auto">Horizontal scroll</div>
<div class="overflow-y-auto">Vertical scroll</div>
```

---

## Gap Utilities (Flexbox/Grid)

```html
<!-- Flexbox gaps -->
<div class="d-flex gap-0">No gap</div>
<div class="d-flex gap-1">0.25rem gap</div>
<div class="d-flex gap-2">0.5rem gap</div>
<div class="d-flex gap-3">1rem gap</div>
<div class="d-flex gap-4">1.5rem gap</div>
<div class="d-flex gap-5">3rem gap</div>

<!-- Directional gaps -->
<div class="d-flex column-gap-3">Column gap</div>
<div class="d-flex row-gap-3">Row gap</div>
```

---

## Practical Examples

### Card with Multiple Utilities

```html
<div class="card shadow-lg rounded-3 border-0">
  <div class="card-body p-4">
    <h5 class="card-title fs-4 fw-bold text-primary mb-3">
      Product Title
    </h5>
    <p class="card-text text-muted lh-lg">
      Product description with utilities for spacing, color, and typography.
    </p>
    <div class="d-flex justify-content-between align-items-center mt-4">
      <span class="fs-3 fw-bold text-success">$99.99</span>
      <button class="btn btn-primary px-4 py-2 rounded-pill shadow-sm">
        Add to Cart
      </button>
    </div>
  </div>
</div>
```

### Alert Banner

```html
<div class="bg-warning bg-opacity-25 border-start border-5 border-warning p-3 rounded-2 d-flex align-items-center gap-3">
  <div class="fs-2 text-warning">
    <i class="bi bi-exclamation-triangle-fill"></i>
  </div>
  <div>
    <h6 class="fw-bold mb-1">Warning!</h6>
    <p class="mb-0 text-muted">This action cannot be undone.</p>
  </div>
</div>
```

### Centered Hero Section

```html
<div class="bg-primary bg-gradient text-white d-flex justify-content-center align-items-center vh-100">
  <div class="text-center">
    <h1 class="display-1 fw-bold mb-3">Welcome</h1>
    <p class="fs-4 mb-4 opacity-75">Build amazing things with Bootstrap</p>
    <button class="btn btn-light btn-lg px-5 py-3 rounded-pill shadow">
      Get Started
    </button>
  </div>
</div>
```

---

## Utility API (Advanced)

Bootstrap 5 allows you to customize and create utilities via Sass.

### Creating Custom Utilities

```scss
// In your custom.scss
$utilities: (
  "custom-margin": (
    property: margin,
    class: m,
    values: (
      6: 4rem,
      7: 5rem,
      8: 6rem
    )
  )
);
```

---

## Quick Reference Table

### Most Common Utilities

| Category | Classes | Example |
|----------|---------|---------|
| Margin | `m-{0-5}`, `mt-`, `mb-`, `ms-`, `me-`, `mx-`, `my-` | `mt-3 mb-4` |
| Padding | `p-{0-5}`, `pt-`, `pb-`, `ps-`, `pe-`, `px-`, `py-` | `p-3 px-4` |
| Text Color | `text-{color}` | `text-primary` |
| Background | `bg-{color}` | `bg-success` |
| Display | `d-{value}` | `d-flex` |
| Flex | `justify-content-*`, `align-items-*` | `d-flex justify-content-center` |
| Text Align | `text-{start\|center\|end}` | `text-center` |
| Border | `border`, `border-{side}`, `rounded` | `border rounded` |
| Shadow | `shadow-{none\|sm\|lg}` | `shadow-lg` |

---

## Best Practices

### Do's

1. **Use utilities for common patterns** - Faster than custom CSS
2. **Combine utilities** - Build complex layouts
3. **Use responsive variants** - Different styles at breakpoints
4. **Follow naming conventions** - Predictable and consistent
5. **Prefer utilities over inline styles** - More maintainable

### Don'ts

1. **Don't overuse utilities** - If repeating same combination, make a component
2. **Don't ignore semantics** - Use proper HTML elements
3. **Don't fight specificity** - Use `!important` sparingly
4. **Don't create overly complex combinations** - Consider custom CSS

---

## Summary

| Concept | Key Points |
|---------|------------|
| Spacing | Use `m-*` and `p-*` with scale 0-5 and sides (t/b/s/e/x/y) |
| Colors | `text-*` and `bg-*` with theme colors |
| Flexbox | `d-flex` with `justify-content-*` and `align-items-*` |
| Display | `d-{none\|block\|flex\|grid}` with responsive variants |
| Typography | `fs-*`, `fw-*`, `text-*` for font styling |
| Borders | `border`, `border-*`, `rounded` for borders and corners |

## Next Topic

Continue to [Responsive Design with Bootstrap](./04-responsive-design.md) to learn about Bootstrap's grid system and responsive utilities.
