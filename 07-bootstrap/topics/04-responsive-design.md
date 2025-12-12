# Responsive Design with Bootstrap

## What is Responsive Design?

Responsive design is an approach where websites adapt their layout and appearance based on the device's screen size, orientation, and platform. Bootstrap makes responsive design straightforward with its grid system and responsive utilities.

### Core Principles

1. **Fluid Grids**: Layouts use relative units (percentages) instead of fixed units (pixels)
2. **Flexible Images**: Images scale with their containers
3. **Media Queries**: CSS rules apply at different screen sizes
4. **Mobile-First**: Start with mobile design, then enhance for larger screens

### Why Mobile-First?

```css
/* Mobile-First Approach (Bootstrap's way) */
.element {
  /* Base styles for mobile */
  font-size: 14px;
}

@media (min-width: 768px) {
  /* Enhancement for tablets and up */
  .element {
    font-size: 16px;
  }
}

/* vs Desktop-First (not Bootstrap's approach) */
.element {
  /* Base styles for desktop */
  font-size: 16px;
}

@media (max-width: 767px) {
  /* Override for mobile */
  .element {
    font-size: 14px;
  }
}
```

---

## Bootstrap Breakpoints

Bootstrap uses six breakpoint tiers for responsive design:

| Breakpoint | Class Infix | Dimensions | Device Type | Container Width |
|------------|-------------|------------|-------------|-----------------|
| Extra small | None | <576px | Portrait phones | 100% (fluid) |
| Small | `sm` | ≥576px | Landscape phones | 540px |
| Medium | `md` | ≥768px | Tablets | 720px |
| Large | `lg` | ≥992px | Desktops | 960px |
| Extra large | `xl` | ≥1200px | Large desktops | 1140px |
| Extra extra large | `xxl` | ≥1400px | Larger desktops | 1320px |

### Breakpoint Visualization

```
|-------|--------|--------|--------|--------|
  XS      SM       MD       LG       XL      XXL
<576px  576px   768px    992px   1200px  1400px
Mobile  Phone   Tablet  Desktop  Large   Larger
```

---

## The Grid System

Bootstrap's grid system is built with flexbox and allows up to 12 columns across the page.

### Grid Basics

```html
<div class="container">
  <div class="row">
    <div class="col">Column 1</div>
    <div class="col">Column 2</div>
    <div class="col">Column 3</div>
  </div>
</div>
```

### Grid Anatomy

```
┌─────────────────────────────────────────┐
│            Container                     │
│  ┌───────────────────────────────────┐  │
│  │              Row                  │  │
│  │  ┌───────┐ ┌───────┐ ┌───────┐  │  │
│  │  │  Col  │ │  Col  │ │  Col  │  │  │
│  │  │   4   │ │   4   │ │   4   │  │  │
│  │  └───────┘ └───────┘ └───────┘  │  │
│  └───────────────────────────────────┘  │
└─────────────────────────────────────────┘
```

### Container Types

```html
<!-- Fixed-width container (responsive breakpoints) -->
<div class="container">
  <!-- Max-width changes at breakpoints -->
</div>

<!-- Fluid container (always 100% width) -->
<div class="container-fluid">
  <!-- Always full width -->
</div>

<!-- Responsive containers -->
<div class="container-sm">100% wide until small breakpoint</div>
<div class="container-md">100% wide until medium breakpoint</div>
<div class="container-lg">100% wide until large breakpoint</div>
<div class="container-xl">100% wide until xl breakpoint</div>
<div class="container-xxl">100% wide until xxl breakpoint</div>
```

---

## Column Sizing

### Equal Width Columns

```html
<!-- 3 equal columns -->
<div class="container">
  <div class="row">
    <div class="col">Column</div>
    <div class="col">Column</div>
    <div class="col">Column</div>
  </div>
</div>
```

### Fixed Width Columns

```html
<!-- Columns with specific widths (out of 12) -->
<div class="container">
  <div class="row">
    <div class="col-8">8 columns wide</div>
    <div class="col-4">4 columns wide</div>
  </div>
</div>

<!-- Another example -->
<div class="container">
  <div class="row">
    <div class="col-3">3/12 = 25%</div>
    <div class="col-6">6/12 = 50%</div>
    <div class="col-3">3/12 = 25%</div>
  </div>
</div>
```

### Auto-Width Columns

```html
<div class="container">
  <div class="row">
    <div class="col">Equal width</div>
    <div class="col-6">50% width</div>
    <div class="col">Equal width (fills remaining)</div>
  </div>
</div>
```

### Column Widths Reference

| Columns | Width | Percentage |
|---------|-------|------------|
| `col-1` | 1/12 | 8.33% |
| `col-2` | 2/12 | 16.66% |
| `col-3` | 3/12 | 25% |
| `col-4` | 4/12 | 33.33% |
| `col-5` | 5/12 | 41.66% |
| `col-6` | 6/12 | 50% |
| `col-7` | 7/12 | 58.33% |
| `col-8` | 8/12 | 66.66% |
| `col-9` | 9/12 | 75% |
| `col-10` | 10/12 | 83.33% |
| `col-11` | 11/12 | 91.66% |
| `col-12` | 12/12 | 100% |

---

## Responsive Columns

### Basic Responsive Layout

```html
<!-- Stack on mobile, 2 columns on tablet+, 4 columns on desktop+ -->
<div class="container">
  <div class="row">
    <div class="col-12 col-md-6 col-lg-3">Item 1</div>
    <div class="col-12 col-md-6 col-lg-3">Item 2</div>
    <div class="col-12 col-md-6 col-lg-3">Item 3</div>
    <div class="col-12 col-md-6 col-lg-3">Item 4</div>
  </div>
</div>
```

**Breakdown:**
- Mobile (< 768px): 4 stacked items (col-12 = 100% width each)
- Tablet (≥ 768px): 2 columns, 2 rows (col-md-6 = 50% width each)
- Desktop (≥ 992px): 4 columns, 1 row (col-lg-3 = 25% width each)

### Common Responsive Patterns

```html
<!-- Pattern 1: Sidebar Layout -->
<div class="container">
  <div class="row">
    <!-- Sidebar: Full width on mobile, 1/4 on desktop -->
    <div class="col-12 col-lg-3">Sidebar</div>

    <!-- Main: Full width on mobile, 3/4 on desktop -->
    <div class="col-12 col-lg-9">Main Content</div>
  </div>
</div>

<!-- Pattern 2: Card Grid -->
<div class="container">
  <div class="row">
    <!-- 1 column mobile, 2 columns tablet, 3 columns desktop, 4 columns large -->
    <div class="col-12 col-sm-6 col-md-4 col-xl-3">Card</div>
    <div class="col-12 col-sm-6 col-md-4 col-xl-3">Card</div>
    <div class="col-12 col-sm-6 col-md-4 col-xl-3">Card</div>
    <div class="col-12 col-sm-6 col-md-4 col-xl-3">Card</div>
  </div>
</div>

<!-- Pattern 3: Feature Blocks -->
<div class="container">
  <div class="row">
    <!-- Stack on mobile, 3 equal columns on tablet+ -->
    <div class="col-md-4">Feature 1</div>
    <div class="col-md-4">Feature 2</div>
    <div class="col-md-4">Feature 3</div>
  </div>
</div>
```

---

## Column Alignment

### Horizontal Alignment (justify-content)

```html
<div class="container">
  <!-- Align to start -->
  <div class="row justify-content-start">
    <div class="col-4">Column</div>
    <div class="col-4">Column</div>
  </div>

  <!-- Align to center -->
  <div class="row justify-content-center">
    <div class="col-4">Column</div>
    <div class="col-4">Column</div>
  </div>

  <!-- Align to end -->
  <div class="row justify-content-end">
    <div class="col-4">Column</div>
    <div class="col-4">Column</div>
  </div>

  <!-- Space between -->
  <div class="row justify-content-between">
    <div class="col-4">Column</div>
    <div class="col-4">Column</div>
  </div>

  <!-- Space around -->
  <div class="row justify-content-around">
    <div class="col-4">Column</div>
    <div class="col-4">Column</div>
  </div>

  <!-- Space evenly -->
  <div class="row justify-content-evenly">
    <div class="col-4">Column</div>
    <div class="col-4">Column</div>
  </div>
</div>
```

### Vertical Alignment (align-items)

```html
<div class="container">
  <!-- Align to top -->
  <div class="row align-items-start" style="height: 200px;">
    <div class="col">Column</div>
    <div class="col">Column</div>
  </div>

  <!-- Align to center -->
  <div class="row align-items-center" style="height: 200px;">
    <div class="col">Column</div>
    <div class="col">Column</div>
  </div>

  <!-- Align to bottom -->
  <div class="row align-items-end" style="height: 200px;">
    <div class="col">Column</div>
    <div class="col">Column</div>
  </div>
</div>
```

### Individual Column Alignment

```html
<div class="container">
  <div class="row" style="height: 200px;">
    <div class="col align-self-start">Top</div>
    <div class="col align-self-center">Center</div>
    <div class="col align-self-end">Bottom</div>
  </div>
</div>
```

---

## Column Ordering

### Order Classes

```html
<div class="container">
  <div class="row">
    <!-- Appears second on mobile, first on desktop -->
    <div class="col order-2 order-md-1">First on desktop</div>

    <!-- Appears first on mobile, second on desktop -->
    <div class="col order-1 order-md-2">Second on desktop</div>
  </div>
</div>

<!-- Specific order (1-5) -->
<div class="row">
  <div class="col order-5">Fifth</div>
  <div class="col order-1">First</div>
  <div class="col order-3">Third</div>
  <div class="col order-2">Second</div>
  <div class="col order-4">Fourth</div>
</div>

<!-- Order first/last -->
<div class="row">
  <div class="col order-last">Appears last</div>
  <div class="col">Default order</div>
  <div class="col order-first">Appears first</div>
</div>
```

---

## Offsetting Columns

```html
<!-- Offset by 3 columns -->
<div class="container">
  <div class="row">
    <div class="col-md-4 offset-md-4">
      Centered (4 cols + 4 offset = starts at position 5)
    </div>
  </div>
</div>

<!-- Responsive offsets -->
<div class="container">
  <div class="row">
    <div class="col-6 offset-3 col-md-4 offset-md-0">
      Offset on mobile, no offset on tablet+
    </div>
  </div>
</div>

<!-- Margin utilities for offset -->
<div class="container">
  <div class="row">
    <div class="col-md-4 ms-auto">Pushed to right</div>
  </div>
  <div class="row">
    <div class="col-md-4 mx-auto">Centered</div>
  </div>
</div>
```

---

## Gutters (Column Spacing)

### Default Gutters

Bootstrap columns have default horizontal spacing (gutters):

```html
<div class="container">
  <div class="row">
    <div class="col">Default gutter</div>
    <div class="col">Default gutter</div>
  </div>
</div>
```

### Custom Gutters

```html
<!-- No gutters -->
<div class="row g-0">
  <div class="col">No spacing</div>
  <div class="col">No spacing</div>
</div>

<!-- Custom gutter sizes (0-5) -->
<div class="row g-1">Small gutters</div>
<div class="row g-2">Medium-small gutters</div>
<div class="row g-3">Medium gutters</div>
<div class="row g-4">Medium-large gutters</div>
<div class="row g-5">Large gutters</div>

<!-- Horizontal gutters only -->
<div class="row gx-3">
  <div class="col">Horizontal gutter</div>
  <div class="col">Horizontal gutter</div>
</div>

<!-- Vertical gutters only -->
<div class="row gy-3">
  <div class="col-12">Vertical gutter</div>
  <div class="col-12">Vertical gutter</div>
</div>

<!-- Different gutters at breakpoints -->
<div class="row g-2 g-md-3 g-lg-4">
  <div class="col">Responsive gutters</div>
  <div class="col">Responsive gutters</div>
</div>
```

---

## Nesting Rows

```html
<div class="container">
  <div class="row">
    <div class="col-12 col-md-8">
      <!-- Nested row -->
      <div class="row">
        <div class="col-6">Nested column 1</div>
        <div class="col-6">Nested column 2</div>
      </div>
    </div>
    <div class="col-12 col-md-4">
      Sidebar
    </div>
  </div>
</div>
```

---

## Responsive Display Utilities

### Hiding/Showing Elements

```html
<!-- Hide on mobile, show on tablet+ -->
<div class="d-none d-md-block">
  Visible on tablet and larger
</div>

<!-- Show on mobile, hide on tablet+ -->
<div class="d-block d-md-none">
  Only visible on mobile
</div>

<!-- Visible only on specific breakpoint -->
<div class="d-none d-md-block d-lg-none">
  Only visible on tablets (md)
</div>
```

### Display Utility Combinations

| Classes | Behavior |
|---------|----------|
| `d-none` | Hidden on all sizes |
| `d-none d-sm-block` | Hidden on xs, visible sm and up |
| `d-sm-none d-md-block` | Visible xs, hidden sm, visible md and up |
| `d-none d-md-block d-xl-none` | Hidden xs-sm, visible md-lg, hidden xl+ |
| `d-block d-lg-none` | Visible xs-md, hidden lg+ |

### Responsive Display Types

```html
<!-- Different display types at breakpoints -->
<div class="d-block d-md-flex">
  Block on mobile, flex on tablet+
</div>

<div class="d-inline d-lg-block">
  Inline until large, then block
</div>
```

---

## Responsive Text

### Text Alignment

```html
<!-- Center on mobile, left on tablet+ -->
<p class="text-center text-md-start">
  Responsive text alignment
</p>

<!-- Different alignment at each breakpoint -->
<p class="text-start text-md-center text-lg-end">
  Left → Center → Right
</p>
```

### Font Sizes

```html
<!-- While not responsive by default, combine with display utilities -->
<h1 class="fs-3 fs-md-2 fs-lg-1">
  Responsive heading size
</h1>
```

---

## Responsive Images

```html
<!-- Responsive image (scales with container) -->
<img src="image.jpg" class="img-fluid" alt="Responsive image">

<!-- With rounded corners -->
<img src="image.jpg" class="img-fluid rounded" alt="Rounded image">

<!-- Thumbnail style -->
<img src="image.jpg" class="img-thumbnail" alt="Thumbnail">

<!-- Responsive video -->
<div class="ratio ratio-16x9">
  <iframe src="https://www.youtube.com/embed/..." title="Video"></iframe>
</div>
```

### Aspect Ratios

```html
<!-- 16:9 aspect ratio -->
<div class="ratio ratio-16x9">
  <iframe src="..."></iframe>
</div>

<!-- 4:3 aspect ratio -->
<div class="ratio ratio-4x3">
  <iframe src="..."></iframe>
</div>

<!-- 21:9 aspect ratio -->
<div class="ratio ratio-21x9">
  <iframe src="..."></iframe>
</div>

<!-- 1:1 (square) -->
<div class="ratio ratio-1x1">
  <div>Square content</div>
</div>
```

---

## Real-World Responsive Examples

### Example 1: Blog Layout

```html
<div class="container">
  <div class="row">
    <!-- Main content: Full width mobile, 2/3 on desktop -->
    <div class="col-12 col-lg-8">
      <article class="mb-4">
        <h1>Blog Post Title</h1>
        <img src="featured.jpg" class="img-fluid mb-3" alt="Featured">
        <p>Blog content...</p>
      </article>
    </div>

    <!-- Sidebar: Full width mobile, 1/3 on desktop -->
    <div class="col-12 col-lg-4">
      <div class="card mb-3">
        <div class="card-body">
          <h5>About</h5>
          <p>Sidebar content...</p>
        </div>
      </div>
    </div>
  </div>
</div>
```

### Example 2: Product Grid

```html
<div class="container">
  <div class="row g-4">
    <!-- 1 column mobile, 2 tablet, 3 desktop, 4 xl -->
    <div class="col-12 col-sm-6 col-md-4 col-xl-3">
      <div class="card h-100">
        <img src="product1.jpg" class="card-img-top" alt="Product">
        <div class="card-body">
          <h5 class="card-title">Product 1</h5>
          <p class="card-text">$99.99</p>
          <button class="btn btn-primary w-100">Add to Cart</button>
        </div>
      </div>
    </div>
    <!-- Repeat for more products -->
  </div>
</div>
```

### Example 3: Dashboard Layout

```html
<div class="container-fluid">
  <div class="row">
    <!-- Sidebar: Collapsed on mobile, visible on tablet+ -->
    <div class="col-12 col-md-3 col-lg-2 bg-dark text-white d-none d-md-block">
      <nav class="navbar-nav">
        <a href="#" class="nav-link">Dashboard</a>
        <a href="#" class="nav-link">Settings</a>
        <a href="#" class="nav-link">Profile</a>
      </nav>
    </div>

    <!-- Main content -->
    <div class="col-12 col-md-9 col-lg-10">
      <div class="row g-3">
        <!-- Stats cards: 1 mobile, 2 tablet, 4 desktop -->
        <div class="col-12 col-sm-6 col-lg-3">
          <div class="card">
            <div class="card-body">
              <h6 class="text-muted">Sales</h6>
              <h2>$12,345</h2>
            </div>
          </div>
        </div>
        <!-- More stat cards... -->
      </div>
    </div>
  </div>
</div>
```

### Example 4: Hero Section

```html
<section class="bg-primary text-white py-5">
  <div class="container">
    <div class="row align-items-center">
      <!-- Stack on mobile, side-by-side on tablet+ -->
      <div class="col-12 col-md-6 mb-4 mb-md-0">
        <h1 class="display-4 display-md-3 display-lg-1">
          Welcome
        </h1>
        <p class="lead">Build responsive websites with ease</p>
        <button class="btn btn-light btn-lg">Get Started</button>
      </div>
      <div class="col-12 col-md-6">
        <img src="hero.jpg" class="img-fluid rounded" alt="Hero">
      </div>
    </div>
  </div>
</section>
```

---

## Testing Responsive Design

### Browser DevTools

1. Open DevTools (F12)
2. Click device toolbar icon
3. Select device or custom dimensions
4. Test interactions and layouts

### Recommended Test Sizes

| Device | Width | Purpose |
|--------|-------|---------|
| Mobile S | 320px | Small phones |
| Mobile M | 375px | iPhone SE, 6, 7, 8 |
| Mobile L | 425px | Larger phones |
| Tablet | 768px | iPad portrait |
| Laptop | 1024px | Small laptops |
| Desktop | 1440px | Standard desktop |

### Testing Checklist

- [ ] Content readable on 320px width
- [ ] No horizontal scroll at any breakpoint
- [ ] Images scale appropriately
- [ ] Navigation accessible on mobile
- [ ] Touch targets at least 44x44px
- [ ] Text remains legible (min 16px)
- [ ] Forms usable on mobile
- [ ] Tables adapt or scroll

---

## Common Responsive Patterns

### Pattern 1: Hamburger Menu

```html
<nav class="navbar navbar-expand-lg navbar-dark bg-dark">
  <div class="container">
    <a class="navbar-brand" href="#">Logo</a>

    <!-- Hamburger button (mobile) -->
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
      <span class="navbar-toggler-icon"></span>
    </button>

    <!-- Collapsible menu -->
    <div class="collapse navbar-collapse" id="navbarNav">
      <ul class="navbar-nav ms-auto">
        <li class="nav-item">
          <a class="nav-link" href="#">Home</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="#">About</a>
        </li>
      </ul>
    </div>
  </div>
</nav>
```

### Pattern 2: Mobile-First Form

```html
<form class="container">
  <div class="row g-3">
    <!-- Full width on mobile, half on tablet+ -->
    <div class="col-12 col-md-6">
      <label class="form-label">First Name</label>
      <input type="text" class="form-control">
    </div>
    <div class="col-12 col-md-6">
      <label class="form-label">Last Name</label>
      <input type="text" class="form-control">
    </div>
    <div class="col-12">
      <label class="form-label">Email</label>
      <input type="email" class="form-control">
    </div>
    <div class="col-12">
      <button type="submit" class="btn btn-primary w-100 w-md-auto">
        Submit
      </button>
    </div>
  </div>
</form>
```

---

## Best Practices

### Do's

1. **Start with mobile** - Design for smallest screen first
2. **Test on real devices** - Emulators don't show everything
3. **Use responsive images** - Add `img-fluid` class
4. **Think in breakpoints** - Design for 2-3 key sizes
5. **Use Bootstrap classes** - Leverage built-in responsive utilities
6. **Keep it simple** - Don't over-complicate layouts
7. **Consider touch targets** - Make buttons easy to tap

### Don'ts

1. **Don't use fixed widths** - Use percentages or Bootstrap classes
2. **Don't hide important content** - Make it accessible on all devices
3. **Don't assume viewport** - Test various screen sizes
4. **Don't forget landscape** - Test both orientations
5. **Don't overuse breakpoints** - Too many can be confusing
6. **Don't ignore performance** - Optimize images and code

---

## Responsive Design Checklist

### Layout
- [ ] Uses container/container-fluid
- [ ] Grid system implemented correctly
- [ ] Columns add up to 12
- [ ] Responsive column classes applied

### Content
- [ ] Images use `img-fluid`
- [ ] Text readable on small screens
- [ ] No horizontal scrolling
- [ ] Content accessible on all devices

### Navigation
- [ ] Mobile menu works (hamburger)
- [ ] Links touchable (44x44px minimum)
- [ ] Navigation accessible without hover

### Testing
- [ ] Tested on mobile (< 576px)
- [ ] Tested on tablet (768px)
- [ ] Tested on desktop (992px+)
- [ ] Tested on actual devices

---

## Summary

| Concept | Key Points |
|---------|------------|
| Breakpoints | XS (<576), SM (≥576), MD (≥768), LG (≥992), XL (≥1200), XXL (≥1400) |
| Grid System | 12-column layout with containers, rows, and columns |
| Responsive Columns | Use `col-{breakpoint}-{size}` for different layouts |
| Display Utilities | Show/hide with `d-{breakpoint}-{value}` |
| Mobile-First | Start with base styles, enhance for larger screens |
| Testing | Test on multiple devices and screen sizes |

## Next Topic

Continue to [Customization](./05-customization.md) to learn how to customize Bootstrap to match your brand and requirements.
