# Customization

## Why Customize Bootstrap?

While Bootstrap provides excellent default styles, you'll often want to customize it to match your brand, project requirements, or design preferences. Bootstrap offers multiple levels of customization from simple CSS overrides to complete Sass customization.

### Levels of Customization

| Level | Difficulty | Method | Use Case |
|-------|------------|--------|----------|
| 1 | Beginner | CSS overrides | Quick changes, prototypes |
| 2 | Intermediate | CSS variables | Runtime theming, simple customization |
| 3 | Advanced | Sass variables | Complete brand customization |
| 4 | Expert | Sass compilation | Full framework customization |

---

## Method 1: CSS Overrides (Simplest)

### Basic Override Technique

Create a custom CSS file and load it AFTER Bootstrap:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <!-- Bootstrap CSS first -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">

  <!-- Your custom CSS last (overrides Bootstrap) -->
  <link href="css/custom.css" rel="stylesheet">
</head>
<body>
  <!-- Your content -->
</body>
</html>
```

### Custom CSS Examples

```css
/* custom.css */

/* Override primary color */
.btn-primary {
  background-color: #6a0dad;
  border-color: #6a0dad;
}

.btn-primary:hover {
  background-color: #5a0c9d;
  border-color: #5a0c9d;
}

/* Custom heading styles */
h1, h2, h3, h4, h5, h6 {
  font-family: 'Montserrat', sans-serif;
  font-weight: 700;
}

/* Custom card styling */
.card {
  border-radius: 15px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

/* Custom navigation */
.navbar {
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

/* Custom button spacing */
.btn {
  padding: 12px 30px;
  border-radius: 25px;
}
```

### Targeting Bootstrap Classes

```css
/* Increase specificity to override Bootstrap */
.btn.btn-primary {
  background-color: #custom-color;
}

/* Or use !important (use sparingly) */
.btn-primary {
  background-color: #custom-color !important;
}

/* Override specific components */
.navbar-brand {
  font-size: 1.5rem;
  font-weight: bold;
  color: #6a0dad !important;
}

.navbar-nav .nav-link {
  text-transform: uppercase;
  letter-spacing: 1px;
  font-weight: 600;
}
```

### Pros and Cons

**Pros:**
- Easy to implement
- No build tools required
- Quick for small changes
- Works with CDN

**Cons:**
- Increased CSS file size (duplicate declarations)
- May need !important
- Harder to maintain
- Not all aspects customizable

---

## Method 2: CSS Custom Properties (CSS Variables)

Bootstrap 5 uses CSS custom properties, making runtime customization easier.

### Understanding Bootstrap's CSS Variables

Bootstrap defines CSS variables at the root level:

```css
:root {
  --bs-blue: #0d6efd;
  --bs-indigo: #6610f2;
  --bs-purple: #6f42c1;
  --bs-primary: #0d6efd;
  --bs-secondary: #6c757d;
  --bs-success: #198754;
  --bs-danger: #dc3545;
  --bs-warning: #ffc107;
  --bs-info: #0dcaf0;
  /* ... many more */
}
```

### Overriding CSS Variables

```css
/* custom.css */

/* Override at root level */
:root {
  --bs-primary: #6a0dad;
  --bs-primary-rgb: 106, 13, 173;
  --bs-secondary: #ff6b6b;
  --bs-success: #51cf66;
  --bs-danger: #ff6b6b;
  --bs-warning: #ffd43b;
  --bs-info: #4dabf7;

  /* Typography */
  --bs-body-font-family: 'Inter', sans-serif;
  --bs-body-font-size: 1rem;
  --bs-body-font-weight: 400;
  --bs-body-line-height: 1.6;

  /* Spacing */
  --bs-gutter-x: 1.5rem;
  --bs-gutter-y: 0;

  /* Border radius */
  --bs-border-radius: 0.5rem;
  --bs-border-radius-sm: 0.25rem;
  --bs-border-radius-lg: 1rem;
}
```

### Common CSS Variables

| Category | Variables |
|----------|-----------|
| Colors | `--bs-primary`, `--bs-secondary`, `--bs-success`, etc. |
| Typography | `--bs-body-font-family`, `--bs-body-font-size`, `--bs-body-color` |
| Spacing | `--bs-gutter-x`, `--bs-gutter-y` |
| Border | `--bs-border-width`, `--bs-border-radius` |
| Shadows | `--bs-box-shadow`, `--bs-box-shadow-sm`, `--bs-box-shadow-lg` |

### Runtime Theme Switching

```html
<!-- HTML -->
<button onclick="switchTheme()">Toggle Theme</button>

<style>
/* Light theme (default) */
:root {
  --bs-primary: #0d6efd;
  --bs-body-bg: #ffffff;
  --bs-body-color: #212529;
}

/* Dark theme */
:root[data-theme="dark"] {
  --bs-primary: #375a7f;
  --bs-body-bg: #222222;
  --bs-body-color: #ffffff;
}
</style>

<script>
function switchTheme() {
  const root = document.documentElement;
  const currentTheme = root.getAttribute('data-theme');
  const newTheme = currentTheme === 'dark' ? 'light' : 'dark';
  root.setAttribute('data-theme', newTheme);
}
</script>
```

### Pros and Cons

**Pros:**
- Easy to implement
- Runtime theme switching
- No build tools needed
- Works with CDN
- Better than CSS overrides

**Cons:**
- Limited to variables Bootstrap exposes
- Not as comprehensive as Sass
- Browser support (IE not supported)

---

## Method 3: Sass Variables (Advanced)

For complete customization, use Sass to recompile Bootstrap with your custom variables.

### Setting Up Sass

#### Option 1: Using npm

```bash
# Install Bootstrap and Sass
npm install bootstrap sass

# Create directory structure
mkdir -p src/scss
touch src/scss/custom.scss
```

#### Option 2: Download Source Files

1. Download Bootstrap source from getbootstrap.com
2. Extract to your project
3. Create custom.scss file

### Custom Sass File Structure

```scss
// custom.scss

// 1. Include functions first (required)
@import "../node_modules/bootstrap/scss/functions";

// 2. Override default variables
$primary: #6a0dad;
$secondary: #ff6b6b;
$success: #51cf66;
$danger: #ff6b6b;
$warning: #ffd43b;
$info: #4dabf7;

$font-family-base: 'Inter', sans-serif;
$font-size-base: 1rem;
$line-height-base: 1.6;

$border-radius: 0.5rem;
$border-radius-sm: 0.25rem;
$border-radius-lg: 1rem;

// 3. Include remainder of required Bootstrap stylesheets
@import "../node_modules/bootstrap/scss/variables";
@import "../node_modules/bootstrap/scss/variables-dark";

// 4. Include optional Bootstrap mixins
@import "../node_modules/bootstrap/scss/maps";
@import "../node_modules/bootstrap/scss/mixins";
@import "../node_modules/bootstrap/scss/root";

// 5. Include Bootstrap components you need
@import "../node_modules/bootstrap/scss/reboot";
@import "../node_modules/bootstrap/scss/type";
@import "../node_modules/bootstrap/scss/images";
@import "../node_modules/bootstrap/scss/containers";
@import "../node_modules/bootstrap/scss/grid";
@import "../node_modules/bootstrap/scss/forms";
@import "../node_modules/bootstrap/scss/buttons";
@import "../node_modules/bootstrap/scss/transitions";
@import "../node_modules/bootstrap/scss/dropdown";
@import "../node_modules/bootstrap/scss/nav";
@import "../node_modules/bootstrap/scss/navbar";
@import "../node_modules/bootstrap/scss/card";
@import "../node_modules/bootstrap/scss/badge";
@import "../node_modules/bootstrap/scss/alert";
// ... include only what you need

// Or import everything
@import "../node_modules/bootstrap/scss/bootstrap";

// 6. Add your custom CSS
.custom-component {
  background-color: $primary;
  padding: 2rem;
  border-radius: $border-radius;
}
```

### Compiling Sass

#### Using npm Scripts

```json
// package.json
{
  "scripts": {
    "css-compile": "sass src/scss/custom.scss dist/css/custom.css",
    "css-minify": "sass src/scss/custom.scss dist/css/custom.min.css --style compressed",
    "css-watch": "sass --watch src/scss/custom.scss dist/css/custom.css"
  }
}
```

```bash
# Compile once
npm run css-compile

# Minify for production
npm run css-minify

# Watch for changes during development
npm run css-watch
```

### Common Sass Variables to Customize

#### Colors

```scss
// Brand colors
$primary: #6a0dad;
$secondary: #6c757d;
$success: #198754;
$danger: #dc3545;
$warning: #ffc107;
$info: #0dcaf0;
$light: #f8f9fa;
$dark: #212529;

// Grayscale
$white: #fff;
$gray-100: #f8f9fa;
$gray-200: #e9ecef;
$gray-300: #dee2e6;
$gray-400: #ced4da;
$gray-500: #adb5bd;
$gray-600: #6c757d;
$gray-700: #495057;
$gray-800: #343a40;
$gray-900: #212529;
$black: #000;
```

#### Typography

```scss
// Font family
$font-family-sans-serif: 'Inter', system-ui, -apple-system, sans-serif;
$font-family-monospace: 'Fira Code', monospace;
$font-family-base: $font-family-sans-serif;

// Font sizes
$font-size-base: 1rem;
$font-size-sm: $font-size-base * 0.875;
$font-size-lg: $font-size-base * 1.25;

// Headings
$h1-font-size: $font-size-base * 2.5;
$h2-font-size: $font-size-base * 2;
$h3-font-size: $font-size-base * 1.75;
$h4-font-size: $font-size-base * 1.5;
$h5-font-size: $font-size-base * 1.25;
$h6-font-size: $font-size-base;

// Font weights
$font-weight-lighter: lighter;
$font-weight-light: 300;
$font-weight-normal: 400;
$font-weight-semibold: 600;
$font-weight-bold: 700;
$font-weight-bolder: bolder;

// Line height
$line-height-base: 1.6;
$line-height-sm: 1.25;
$line-height-lg: 2;
```

#### Spacing

```scss
// Spacer units
$spacer: 1rem;
$spacers: (
  0: 0,
  1: $spacer * 0.25,  // 4px
  2: $spacer * 0.5,   // 8px
  3: $spacer,         // 16px
  4: $spacer * 1.5,   // 24px
  5: $spacer * 3,     // 48px
  6: $spacer * 4,     // 64px
  7: $spacer * 5      // 80px
);
```

#### Borders

```scss
// Border width
$border-width: 1px;
$border-widths: (
  1: 1px,
  2: 2px,
  3: 3px,
  4: 4px,
  5: 5px
);

// Border radius
$border-radius: 0.375rem;
$border-radius-sm: 0.25rem;
$border-radius-lg: 0.5rem;
$border-radius-xl: 1rem;
$border-radius-2xl: 2rem;
$border-radius-pill: 50rem;
```

#### Components

```scss
// Buttons
$btn-padding-y: 0.375rem;
$btn-padding-x: 0.75rem;
$btn-font-size: $font-size-base;
$btn-border-radius: $border-radius;
$btn-box-shadow: inset 0 1px 0 rgba($white, .15);

// Cards
$card-border-radius: $border-radius;
$card-border-width: $border-width;
$card-spacer-y: $spacer;
$card-spacer-x: $spacer;

// Navbar
$navbar-padding-y: $spacer * 0.5;
$navbar-brand-font-size: $font-size-lg;

// Forms
$input-padding-y: 0.375rem;
$input-padding-x: 0.75rem;
$input-border-radius: $border-radius;
```

### Adding Custom Theme Colors

```scss
// Add custom colors to the theme
$custom-colors: (
  "purple": #6a0dad,
  "orange": #fd7e14,
  "teal": #20c997
);

// Merge with theme colors
$theme-colors: map-merge($theme-colors, $custom-colors);

// Now you can use:
// .btn-purple, .bg-purple, .text-purple, etc.
```

---

## Method 4: Creating Custom Components

### Using Sass Mixins

```scss
// Custom button variant
@import "bootstrap/scss/mixins/buttons";

.btn-custom {
  @include button-variant(
    $background: #6a0dad,
    $border: #6a0dad,
    $color: $white,
    $hover-background: darken(#6a0dad, 7.5%),
    $hover-border: darken(#6a0dad, 10%),
    $active-background: darken(#6a0dad, 10%),
    $active-border: darken(#6a0dad, 12.5%)
  );
}
```

### Custom Utility Classes

```scss
// Add to your custom.scss after importing Bootstrap

// Custom spacing utilities
$custom-spacers: (
  6: 4rem,
  7: 5rem,
  8: 6rem
);

// Generate utilities
@each $size, $length in $custom-spacers {
  .m-#{$size} { margin: $length !important; }
  .mt-#{$size} { margin-top: $length !important; }
  .mb-#{$size} { margin-bottom: $length !important; }
  .p-#{$size} { padding: $length !important; }
  .pt-#{$size} { padding-top: $length !important; }
  .pb-#{$size} { padding-bottom: $length !important; }
}
```

---

## Complete Customization Example

### Project Structure

```
my-project/
├── src/
│   └── scss/
│       ├── custom.scss
│       └── _variables.scss
├── dist/
│   └── css/
│       └── custom.css
├── node_modules/
├── package.json
└── index.html
```

### _variables.scss

```scss
// Brand colors
$primary: #6a0dad;
$secondary: #ff6b6b;
$success: #51cf66;
$danger: #ff6b6b;
$warning: #ffd43b;
$info: #4dabf7;

// Custom colors
$purple: #6a0dad;
$orange: #ff8c00;

// Typography
$font-family-base: 'Inter', sans-serif;
$font-family-headings: 'Montserrat', sans-serif;
$font-size-base: 1rem;
$line-height-base: 1.6;

// Headings
$headings-font-family: $font-family-headings;
$headings-font-weight: 700;

// Spacing
$spacer: 1rem;
$grid-gutter-width: 1.5rem;

// Borders
$border-radius: 0.5rem;
$border-radius-sm: 0.25rem;
$border-radius-lg: 1rem;

// Shadows
$box-shadow: 0 0.5rem 1rem rgba($black, 0.15);
$box-shadow-sm: 0 0.125rem 0.25rem rgba($black, 0.075);
$box-shadow-lg: 0 1rem 3rem rgba($black, 0.175);

// Components
$btn-border-radius: 2rem;
$btn-padding-y: 0.75rem;
$btn-padding-x: 2rem;

$card-border-radius: 1rem;
$card-box-shadow: $box-shadow;

$navbar-padding-y: 1rem;
```

### custom.scss

```scss
// 1. Include functions
@import "../node_modules/bootstrap/scss/functions";

// 2. Include custom variables
@import "variables";

// 3. Include Bootstrap variables and the rest
@import "../node_modules/bootstrap/scss/variables";
@import "../node_modules/bootstrap/scss/variables-dark";
@import "../node_modules/bootstrap/scss/maps";
@import "../node_modules/bootstrap/scss/mixins";
@import "../node_modules/bootstrap/scss/root";

// 4. Include Bootstrap components
@import "../node_modules/bootstrap/scss/bootstrap";

// 5. Custom components and overrides
.card {
  transition: transform 0.3s ease, box-shadow 0.3s ease;

  &:hover {
    transform: translateY(-5px);
    box-shadow: 0 1.5rem 4rem rgba($black, 0.2);
  }
}

.btn {
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  transition: all 0.3s ease;

  &:hover {
    transform: translateY(-2px);
    box-shadow: $box-shadow;
  }
}

.navbar {
  box-shadow: $box-shadow-sm;
}

// Custom hero section
.hero {
  background: linear-gradient(135deg, $primary 0%, darken($primary, 15%) 100%);
  padding: 5rem 0;
  color: $white;
}

// Custom badge
.badge-custom {
  background: linear-gradient(135deg, $primary, $purple);
  border-radius: $border-radius-pill;
  padding: 0.5rem 1rem;
}
```

---

## Using Custom Fonts

### Google Fonts

```html
<!-- In HTML head -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&family=Montserrat:wght@700&display=swap" rel="stylesheet">
```

```scss
// In Sass
$font-family-sans-serif: 'Inter', system-ui, sans-serif;
$headings-font-family: 'Montserrat', sans-serif;
```

### Local Fonts

```scss
@font-face {
  font-family: 'CustomFont';
  src: url('../fonts/CustomFont.woff2') format('woff2'),
       url('../fonts/CustomFont.woff') format('woff');
  font-weight: normal;
  font-style: normal;
}

$font-family-base: 'CustomFont', sans-serif;
```

---

## Dark Mode Customization

### Using CSS Variables for Dark Mode

```css
/* Light mode (default) */
:root {
  --bs-body-bg: #ffffff;
  --bs-body-color: #212529;
  --bs-card-bg: #ffffff;
}

/* Dark mode */
[data-bs-theme="dark"] {
  --bs-body-bg: #212529;
  --bs-body-color: #dee2e6;
  --bs-card-bg: #2c2f33;
}
```

### Toggle Dark Mode

```html
<button id="themeToggle">Toggle Dark Mode</button>

<script>
const toggle = document.getElementById('themeToggle');
toggle.addEventListener('click', () => {
  const html = document.documentElement;
  const current = html.getAttribute('data-bs-theme');
  html.setAttribute('data-bs-theme', current === 'dark' ? 'light' : 'dark');
});
</script>
```

---

## Customization Tools

### Bootstrap Customizer (Online)

Visit: https://bootstrap.build/
- Visual interface for customizing
- Download customized CSS
- No build tools required

### Bootstrap Studio

Visual design tool for Bootstrap:
- Drag-and-drop interface
- Built-in components
- Export clean code

### Sass Playground

Test Sass online:
- https://www.sassmeister.com/
- Quick testing of Sass code

---

## Performance Considerations

### Include Only What You Need

```scss
// Instead of importing everything
@import "bootstrap/scss/bootstrap";

// Import only required components
@import "bootstrap/scss/functions";
@import "bootstrap/scss/variables";
@import "bootstrap/scss/mixins";
@import "bootstrap/scss/root";
@import "bootstrap/scss/reboot";
@import "bootstrap/scss/grid";
@import "bootstrap/scss/utilities";
@import "bootstrap/scss/buttons";
@import "bootstrap/scss/navbar";
// etc.
```

### PurgeCSS

Remove unused CSS:

```bash
npm install -D @fullhuman/postcss-purgecss
```

```js
// postcss.config.js
module.exports = {
  plugins: [
    require('@fullhuman/postcss-purgecss')({
      content: ['./**/*.html'],
      defaultExtractor: content => content.match(/[\w-/:]+(?<!:)/g) || []
    })
  ]
}
```

---

## Best Practices

### Do's

1. **Use Sass for major customization** - More powerful and maintainable
2. **Override CSS variables** - For simple theming
3. **Keep customizations organized** - Separate files for variables and custom code
4. **Document your changes** - Comment why you made customizations
5. **Test across browsers** - Ensure compatibility
6. **Version control your Sass** - Track changes to customizations
7. **Use semantic color names** - Brand colors should be meaningful

### Don'ts

1. **Don't edit Bootstrap source files** - Always override, never modify
2. **Don't use too many !important** - Indicates specificity issues
3. **Don't customize everything** - Defeats the purpose of using a framework
4. **Don't ignore updates** - Stay current with Bootstrap versions
5. **Don't skip documentation** - Document custom variables and components
6. **Don't over-complicate** - Keep it simple when possible

---

## Troubleshooting

### Issue 1: Customization Not Showing

**Problem:** Custom colors not appearing

**Solution:**
```scss
// Make sure order is correct
@import "bootstrap/scss/functions"; // First
@import "custom-variables";         // Second (your overrides)
@import "bootstrap/scss/variables"; // Third
```

### Issue 2: Build Errors

**Problem:** Sass compilation fails

**Solution:**
- Check import paths are correct
- Ensure all required partials are imported
- Verify Sass syntax

### Issue 3: Specificity Issues

**Problem:** Custom CSS not overriding Bootstrap

**Solution:**
```css
/* Increase specificity */
.btn.btn-primary.custom-btn {
  background-color: #custom;
}

/* Or load custom CSS after Bootstrap */
```

---

## Quick Reference

### Customization Methods Summary

| Method | Best For | Difficulty | Tools Required |
|--------|----------|------------|----------------|
| CSS Override | Quick fixes | Beginner | Text editor |
| CSS Variables | Simple theming | Beginner | Text editor |
| Sass Variables | Brand customization | Intermediate | Node.js, Sass |
| Full Sass | Complete control | Advanced | Node.js, Sass, build tools |

### Common Variables Quick Reference

```scss
// Colors
$primary: #value;
$secondary: #value;

// Typography
$font-family-base: 'Font', sans-serif;
$font-size-base: 1rem;

// Spacing
$spacer: 1rem;

// Borders
$border-radius: 0.375rem;

// Components
$btn-border-radius: value;
$card-border-radius: value;
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| CSS Override | Simplest method, load custom CSS after Bootstrap |
| CSS Variables | Runtime theming, good for light/dark modes |
| Sass Variables | Full customization, requires compilation |
| Custom Components | Extend Bootstrap with your own components |
| Performance | Only include what you need, use PurgeCSS |

## Further Learning

- Official Bootstrap Documentation: https://getbootstrap.com/docs/
- Bootstrap Themes: https://themes.getbootstrap.com/
- Sass Documentation: https://sass-lang.com/documentation
- Bootstrap Icons: https://icons.getbootstrap.com/

---

Congratulations! You've completed the Bootstrap 5 module. You now have the knowledge to build responsive, customized websites using Bootstrap.
