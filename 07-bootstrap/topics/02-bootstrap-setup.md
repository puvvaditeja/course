# Bootstrap Setup (CDN)

## Understanding CDN (Content Delivery Network)

A CDN is a network of servers distributed globally that delivers web content to users based on their geographic location. Using a CDN for Bootstrap provides several advantages:

### Benefits of Using CDN

1. **Fast Loading**: Files are served from servers close to your users
2. **Caching**: Many users already have Bootstrap cached from other sites
3. **No Download Required**: No need to download and manage files locally
4. **Automatic Updates**: Easy to update versions by changing URLs
5. **Reduced Bandwidth**: Saves your server bandwidth
6. **High Availability**: CDNs have excellent uptime and redundancy

### CDN Providers for Bootstrap

| Provider | Features | URL Pattern |
|----------|----------|-------------|
| jsDelivr | Fast, reliable, open-source CDN | cdn.jsdelivr.net |
| cdnjs | Large library collection | cdnjs.cloudflare.com |
| unpkg | npm CDN, always latest | unpkg.com |
| Bootstrap CDN | Official, powered by jsDelivr | cdn.jsdelivr.net |

---

## Basic Bootstrap Setup via CDN

### Minimal HTML Template

Here's the absolute minimum needed to use Bootstrap 5:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Bootstrap 5 Template</title>

  <!-- Bootstrap CSS -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-9ndCyUaIbzAi2FUVXJi0CjmCapSmO7SnpJef0486qhLnuZ2cdeRhO02iuK6FUUVM" crossorigin="anonymous">
</head>
<body>

  <h1>Hello, Bootstrap!</h1>

  <!-- Bootstrap JavaScript Bundle (includes Popper) -->
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js" integrity="sha384-geWF76RCwLtnZ8qwWowPQNguL3RmwHVBC9FhGdlKrxdiJJigb/j/68SIy3Te4Bkz" crossorigin="anonymous"></script>
</body>
</html>
```

### Essential Meta Tags

#### Viewport Meta Tag (Required)

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

This tag is **critical** for responsive design:
- `width=device-width`: Sets the width to the device's screen width
- `initial-scale=1.0`: Sets initial zoom level to 100%

#### Character Encoding (Required)

```html
<meta charset="UTF-8">
```

Ensures proper text rendering across languages.

#### Optional Meta Tags

```html
<!-- IE Compatibility -->
<meta http-equiv="X-UA-Compatible" content="IE=edge">

<!-- Description for SEO -->
<meta name="description" content="Your page description">

<!-- Author -->
<meta name="author" content="Your Name">
```

---

## Including Bootstrap CSS

### Standard CSS Link

```html
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-9ndCyUaIbzAi2FUVXJi0CjmCapSmO7SnpJef0486qhLnuZ2cdeRhO02iuK6FUUVM" crossorigin="anonymous">
```

### Breaking Down the Link Tag

| Attribute | Purpose | Example |
|-----------|---------|---------|
| `href` | URL to the CSS file | cdn.jsdelivr.net/npm/bootstrap@5.3.0/... |
| `rel` | Relationship (stylesheet) | stylesheet |
| `integrity` | Security hash (SRI) | sha384-9ndC... |
| `crossorigin` | CORS setting | anonymous |

### Subresource Integrity (SRI)

The `integrity` attribute ensures the file hasn't been tampered with:

```html
<!-- With SRI (Recommended for production) -->
<link href="..." integrity="sha384-..." crossorigin="anonymous">

<!-- Without SRI (Not recommended) -->
<link href="...">
```

### Different Bootstrap CSS Options

```html
<!-- 1. Full Bootstrap (Recommended) -->
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">

<!-- 2. Grid Only -->
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap-grid.min.css" rel="stylesheet">

<!-- 3. Utilities Only -->
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap-utilities.min.css" rel="stylesheet">

<!-- 4. Reboot Only (CSS Reset) -->
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap-reboot.min.css" rel="stylesheet">
```

---

## Including Bootstrap JavaScript

### Bootstrap Bundle (Recommended)

Includes Bootstrap JavaScript + Popper.js (for tooltips, popovers, dropdowns):

```html
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js" integrity="sha384-geWF76RCwLtnZ8qwWowPQNguL3RmwHVBC9FhGdlKrxdiJJigb/j/68SIy3Te4Bkz" crossorigin="anonymous"></script>
```

### Bootstrap JS Only (Without Popper)

If you don't need tooltips, popovers, or dropdowns:

```html
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.min.js" integrity="sha384-fbbOQedDUMZZ5KreZpsbe1LCZPVmfTnH7ois6mU1QK+m14rQ1l2bGBq41eYeM/fS" crossorigin="anonymous"></script>
```

### Separate Popper and Bootstrap

```html
<!-- Popper.js first -->
<script src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.11.8/dist/umd/popper.min.js"></script>

<!-- Then Bootstrap JS -->
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.min.js"></script>
```

### Where to Place JavaScript

```html
<!DOCTYPE html>
<html>
<head>
  <!-- CSS goes in head -->
  <link href="bootstrap.min.css" rel="stylesheet">
</head>
<body>

  <!-- Your content here -->

  <!-- JavaScript goes before closing body tag -->
  <script src="bootstrap.bundle.min.js"></script>
</body>
</html>
```

**Why at the end?**
- HTML loads before JavaScript
- Page appears faster to users
- DOM is ready when scripts execute

---

## Complete Starter Template

### Production-Ready Template

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <!-- Required meta tags -->
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">

  <!-- SEO Meta Tags -->
  <meta name="description" content="A Bootstrap 5 website">
  <meta name="author" content="Your Name">
  <meta name="keywords" content="bootstrap, website, responsive">

  <title>My Bootstrap Website</title>

  <!-- Bootstrap CSS -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css"
        rel="stylesheet"
        integrity="sha384-9ndCyUaIbzAi2FUVXJi0CjmCapSmO7SnpJef0486qhLnuZ2cdeRhO02iuK6FUUVM"
        crossorigin="anonymous">

  <!-- Bootstrap Icons (Optional) -->
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.5/font/bootstrap-icons.css">

  <!-- Custom CSS (Your styles override Bootstrap) -->
  <link href="css/custom.css" rel="stylesheet">
</head>
<body>

  <!-- Navigation -->
  <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
    <div class="container">
      <a class="navbar-brand" href="#">Brand</a>
      <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarNav">
        <ul class="navbar-nav ms-auto">
          <li class="nav-item">
            <a class="nav-link active" href="#">Home</a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="#">About</a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="#">Contact</a>
          </li>
        </ul>
      </div>
    </div>
  </nav>

  <!-- Main Content -->
  <main>
    <div class="container my-5">
      <h1>Welcome to Bootstrap 5</h1>
      <p class="lead">This is a starter template.</p>
    </div>
  </main>

  <!-- Footer -->
  <footer class="bg-dark text-white text-center py-3 mt-5">
    <div class="container">
      <p class="mb-0">&copy; 2024 Your Company. All rights reserved.</p>
    </div>
  </footer>

  <!-- Bootstrap Bundle JS (includes Popper) -->
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"
          integrity="sha384-geWF76RCwLtnZ8qwWowPQNguL3RmwHVBC9FhGdlKrxdiJJigb/j/68SIy3Te4Bkz"
          crossorigin="anonymous"></script>

  <!-- Custom JavaScript -->
  <script src="js/custom.js"></script>
</body>
</html>
```

---

## Version Management

### Using Specific Versions

```html
<!-- Specific version (5.3.0) -->
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">

<!-- Specific minor version (5.3.x - gets latest patch) -->
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3/dist/css/bootstrap.min.css" rel="stylesheet">

<!-- Major version (5.x.x - gets latest minor and patch) -->
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5/dist/css/bootstrap.min.css" rel="stylesheet">

<!-- Latest version (not recommended for production) -->
<link href="https://cdn.jsdelivr.net/npm/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet">
```

### Version Selection Best Practices

| Environment | Recommendation | Example |
|-------------|----------------|---------|
| Production | Lock to specific version | bootstrap@5.3.0 |
| Development | Minor version | bootstrap@5.3 |
| Prototyping | Latest stable | bootstrap@5 |
| Testing | Never use latest | bootstrap@5.3.0 |

### Checking Available Versions

Visit: https://www.jsdelivr.com/package/npm/bootstrap

---

## Adding Bootstrap Icons

Bootstrap Icons is a separate library with 1,800+ icons.

### Including via CDN

```html
<head>
  <!-- Bootstrap CSS -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">

  <!-- Bootstrap Icons -->
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.5/font/bootstrap-icons.css">
</head>
```

### Using Icons

```html
<!-- As icon font -->
<i class="bi bi-house-fill"></i>
<i class="bi bi-heart"></i>
<i class="bi bi-star-fill"></i>

<!-- In buttons -->
<button class="btn btn-primary">
  <i class="bi bi-download"></i> Download
</button>

<!-- With sizing utilities -->
<i class="bi bi-house fs-1"></i>
<i class="bi bi-house fs-3"></i>
<i class="bi bi-house fs-5"></i>

<!-- With colors -->
<i class="bi bi-heart-fill text-danger fs-2"></i>
<i class="bi bi-star-fill text-warning fs-2"></i>
```

---

## Testing Your Setup

### Quick Verification Checklist

Create this test file to verify everything works:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Bootstrap Test</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
  <div class="container mt-5">
    <!-- Test 1: Typography -->
    <h1 class="display-4">Bootstrap is Working!</h1>
    <p class="lead">If this text looks styled, CSS is loaded.</p>

    <!-- Test 2: Colors -->
    <div class="alert alert-success">Success alert - Colors work!</div>

    <!-- Test 3: Grid -->
    <div class="row">
      <div class="col-md-6 bg-primary text-white p-3">Column 1</div>
      <div class="col-md-6 bg-secondary text-white p-3">Column 2</div>
    </div>

    <!-- Test 4: JavaScript Component -->
    <button class="btn btn-primary mt-3" data-bs-toggle="modal" data-bs-target="#testModal">
      Test Modal (Click to test JS)
    </button>

    <!-- Modal -->
    <div class="modal fade" id="testModal">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">JavaScript Works!</h5>
            <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
          </div>
          <div class="modal-body">
            <p>If you can see this modal, Bootstrap JavaScript is loaded correctly.</p>
          </div>
        </div>
      </div>
    </div>
  </div>

  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

### What to Check

- [ ] Typography looks styled (not default browser font)
- [ ] Alert has green background
- [ ] Grid creates two equal columns on desktop
- [ ] Button has blue background with rounded corners
- [ ] Modal opens when button is clicked
- [ ] Modal closes when X is clicked

---

## Common Setup Issues

### Issue 1: Viewport Not Responsive

**Problem:**
```html
<!-- Missing viewport meta tag -->
<head>
  <title>My Site</title>
</head>
```

**Solution:**
```html
<head>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>My Site</title>
</head>
```

### Issue 2: JavaScript Not Working

**Problem:**
```html
<!-- JS loaded before HTML content -->
<head>
  <script src="bootstrap.bundle.min.js"></script>
</head>
<body>
  <button data-bs-toggle="modal">Click</button>
</body>
```

**Solution:**
```html
<body>
  <button data-bs-toggle="modal">Click</button>

  <!-- Load JS after content -->
  <script src="bootstrap.bundle.min.js"></script>
</body>
```

### Issue 3: Tooltips/Dropdowns Not Working

**Problem:**
```html
<!-- Using bootstrap.min.js instead of bootstrap.bundle.min.js -->
<script src="bootstrap.min.js"></script>
```

**Solution:**
```html
<!-- Use bundle version (includes Popper.js) -->
<script src="bootstrap.bundle.min.js"></script>
```

### Issue 4: Version Mismatch

**Problem:**
```html
<!-- CSS and JS versions don't match -->
<link href="bootstrap@5.3.0/css/bootstrap.min.css" rel="stylesheet">
<script src="bootstrap@5.2.0/js/bootstrap.bundle.min.js"></script>
```

**Solution:**
```html
<!-- Use same version for both -->
<link href="bootstrap@5.3.0/css/bootstrap.min.css" rel="stylesheet">
<script src="bootstrap@5.3.0/js/bootstrap.bundle.min.js"></script>
```

---

## Alternative Setup Methods

### Method 1: Download and Host Locally

```bash
# Download from getbootstrap.com
# Extract files
# Place in your project
```

```html
<!-- Link to local files -->
<link href="css/bootstrap.min.css" rel="stylesheet">
<script src="js/bootstrap.bundle.min.js"></script>
```

**When to use:**
- No internet connectivity required
- Want full control over updates
- Corporate environments with restrictions

### Method 2: Package Manager (npm)

```bash
npm install bootstrap
```

```html
<!-- Import in your build process -->
<link href="node_modules/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet">
```

**When to use:**
- Using build tools (Webpack, Vite, etc.)
- Want to customize with Sass
- Managing dependencies with npm

### Method 3: CDN (Recommended for Learning)

```html
<!-- Use CDN links as shown above -->
```

**When to use:**
- Quick prototypes
- Learning/testing
- Simple projects
- Don't want build process

---

## Performance Optimization

### Loading Only What You Need

```html
<!-- If you only need grid system -->
<link href="bootstrap-grid.min.css" rel="stylesheet">

<!-- If you only need utilities -->
<link href="bootstrap-utilities.min.css" rel="stylesheet">

<!-- If you don't need JavaScript -->
<!-- Don't include the JS file -->
```

### Async/Defer for JavaScript

```html
<!-- Defer: Load after HTML parsing -->
<script src="bootstrap.bundle.min.js" defer></script>

<!-- Async: Load asynchronously -->
<script src="bootstrap.bundle.min.js" async></script>
```

**Note:** For Bootstrap, `defer` is usually better since components depend on DOM being ready.

### Using DNS Prefetch

```html
<head>
  <!-- Speed up CDN connection -->
  <link rel="dns-prefetch" href="https://cdn.jsdelivr.net">
  <link rel="preconnect" href="https://cdn.jsdelivr.net">

  <!-- Then load Bootstrap -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
```

---

## Best Practices

### Do's

1. **Always include viewport meta tag**
2. **Use the same version for CSS and JS**
3. **Load JavaScript before closing body tag**
4. **Use bootstrap.bundle.min.js for simplicity**
5. **Lock versions in production**
6. **Test on actual devices**
7. **Use SRI hashes for security**

### Don'ts

1. **Don't mix Bootstrap versions**
2. **Don't load JavaScript in head (unless defer/async)**
3. **Don't forget to test responsive behavior**
4. **Don't use latest version in production**
5. **Don't load both bundle and separate files**
6. **Don't skip testing JavaScript components**

---

## Quick Reference

### Essential CDN Links (Bootstrap 5.3.0)

```html
<!-- CSS -->
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-9ndCyUaIbzAi2FUVXJi0CjmCapSmO7SnpJef0486qhLnuZ2cdeRhO02iuK6FUUVM" crossorigin="anonymous">

<!-- JS Bundle -->
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js" integrity="sha384-geWF76RCwLtnZ8qwWowPQNguL3RmwHVBC9FhGdlKrxdiJJigb/j/68SIy3Te4Bkz" crossorigin="anonymous"></script>

<!-- Icons -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.5/font/bootstrap-icons.css">
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| CDN Benefits | Fast, cached, no download, high availability |
| Required Meta | Viewport tag is essential for responsive design |
| CSS Placement | In `<head>` section |
| JS Placement | Before closing `</body>` tag |
| Bundle vs Separate | Use bundle.min.js for simplicity (includes Popper) |
| Version Management | Lock specific version in production |

## Next Topic

Continue to [Bootstrap Utilities and Helpers](./03-utilities-and-helpers.md) to learn about Bootstrap's powerful utility classes.
