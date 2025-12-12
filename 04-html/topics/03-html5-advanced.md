# HTML5 Advanced Features

## Overview

HTML5 introduced powerful client-side APIs that enable web applications to store data locally, access device features, and provide rich user experiences without constant server communication.

---

## Web Storage

Web Storage provides a way to store data in the user's browser, persisting beyond a single session and offering more storage capacity than cookies.

### Types of Web Storage

HTML5 provides two storage mechanisms:

| Type | Scope | Lifetime | Storage Limit |
|------|-------|----------|---------------|
| `localStorage` | Origin (domain) | Permanent (until cleared) | ~5-10 MB |
| `sessionStorage` | Tab/window | Current session only | ~5-10 MB |

### localStorage

Data stored in `localStorage` persists even after the browser is closed and reopened.

#### localStorage Methods

```javascript
// Store data
localStorage.setItem('key', 'value');
localStorage.setItem('username', 'JohnDoe');
localStorage.setItem('theme', 'dark');

// Retrieve data
let username = localStorage.getItem('username');
console.log(username); // "JohnDoe"

// Remove specific item
localStorage.removeItem('theme');

// Clear all localStorage data
localStorage.clear();

// Get number of items
let count = localStorage.length;

// Get key by index
let firstKey = localStorage.key(0);
```

#### Storing Complex Data

localStorage only stores strings, so objects must be serialized:

```javascript
// Storing an object
const user = {
    name: 'John Doe',
    email: 'john@example.com',
    age: 30
};

// Convert object to JSON string
localStorage.setItem('user', JSON.stringify(user));

// Retrieving an object
const storedUser = localStorage.getItem('user');
const userObject = JSON.parse(storedUser);
console.log(userObject.name); // "John Doe"

// Storing an array
const todos = ['Buy milk', 'Walk dog', 'Code'];
localStorage.setItem('todos', JSON.stringify(todos));

// Retrieving an array
const storedTodos = JSON.parse(localStorage.getItem('todos'));
console.log(storedTodos[0]); // "Buy milk"
```

#### localStorage Example: Theme Preference

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Theme Preference</title>
    <style>
        body.light-theme {
            background-color: white;
            color: black;
        }
        body.dark-theme {
            background-color: #1a1a1a;
            color: white;
        }
    </style>
</head>
<body>
    <h1>Theme Preference Demo</h1>
    <button id="toggleTheme">Toggle Theme</button>

    <script>
        // Load saved theme on page load
        const savedTheme = localStorage.getItem('theme') || 'light-theme';
        document.body.className = savedTheme;

        // Toggle theme
        document.getElementById('toggleTheme').addEventListener('click', function() {
            const currentTheme = document.body.className;
            const newTheme = currentTheme === 'light-theme' ? 'dark-theme' : 'light-theme';

            // Apply new theme
            document.body.className = newTheme;

            // Save to localStorage
            localStorage.setItem('theme', newTheme);
        });
    </script>
</body>
</html>
```

#### localStorage Example: Shopping Cart

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Shopping Cart</title>
</head>
<body>
    <h1>Shopping Cart</h1>

    <div>
        <h2>Products</h2>
        <button onclick="addToCart('Laptop', 999)">Add Laptop ($999)</button>
        <button onclick="addToCart('Mouse', 25)">Add Mouse ($25)</button>
        <button onclick="addToCart('Keyboard', 75)">Add Keyboard ($75)</button>
    </div>

    <div>
        <h2>Cart</h2>
        <div id="cartItems"></div>
        <p>Total: $<span id="total">0</span></p>
        <button onclick="clearCart()">Clear Cart</button>
    </div>

    <script>
        // Initialize cart
        function getCart() {
            const cart = localStorage.getItem('cart');
            return cart ? JSON.parse(cart) : [];
        }

        // Save cart
        function saveCart(cart) {
            localStorage.setItem('cart', JSON.stringify(cart));
        }

        // Add item to cart
        function addToCart(name, price) {
            const cart = getCart();
            cart.push({ name, price });
            saveCart(cart);
            displayCart();
        }

        // Display cart
        function displayCart() {
            const cart = getCart();
            const cartDiv = document.getElementById('cartItems');
            const totalSpan = document.getElementById('total');

            if (cart.length === 0) {
                cartDiv.innerHTML = '<p>Cart is empty</p>';
                totalSpan.textContent = '0';
                return;
            }

            let html = '<ul>';
            let total = 0;

            cart.forEach((item, index) => {
                html += `<li>${item.name} - $${item.price}</li>`;
                total += item.price;
            });

            html += '</ul>';
            cartDiv.innerHTML = html;
            totalSpan.textContent = total;
        }

        // Clear cart
        function clearCart() {
            localStorage.removeItem('cart');
            displayCart();
        }

        // Display cart on page load
        displayCart();
    </script>
</body>
</html>
```

### sessionStorage

Data stored in `sessionStorage` is cleared when the browser tab is closed.

#### sessionStorage Methods

Same API as localStorage:

```javascript
// Store data (only for current session)
sessionStorage.setItem('sessionID', 'abc123');
sessionStorage.setItem('pageViews', '5');

// Retrieve data
let sessionID = sessionStorage.getItem('sessionID');

// Remove item
sessionStorage.removeItem('sessionID');

// Clear all
sessionStorage.clear();
```

#### sessionStorage Example: Form Data Preservation

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Form Auto-Save</title>
</head>
<body>
    <h1>Contact Form (Auto-saved during session)</h1>

    <form id="contactForm">
        <label for="name">Name:</label><br>
        <input type="text" id="name" name="name"><br><br>

        <label for="email">Email:</label><br>
        <input type="email" id="email" name="email"><br><br>

        <label for="message">Message:</label><br>
        <textarea id="message" name="message" rows="5" cols="40"></textarea><br><br>

        <button type="submit">Submit</button>
        <button type="button" onclick="clearFormData()">Clear Saved Data</button>
    </form>

    <script>
        const form = document.getElementById('contactForm');
        const fields = ['name', 'email', 'message'];

        // Load saved data on page load
        fields.forEach(field => {
            const savedValue = sessionStorage.getItem(field);
            if (savedValue) {
                document.getElementById(field).value = savedValue;
            }
        });

        // Save data on input
        fields.forEach(field => {
            document.getElementById(field).addEventListener('input', function(e) {
                sessionStorage.setItem(field, e.target.value);
            });
        });

        // Clear saved data
        function clearFormData() {
            fields.forEach(field => {
                sessionStorage.removeItem(field);
                document.getElementById(field).value = '';
            });
            alert('Saved form data cleared!');
        }

        // Handle form submission
        form.addEventListener('submit', function(e) {
            e.preventDefault();
            alert('Form submitted!');

            // Clear session storage after submission
            fields.forEach(field => sessionStorage.removeItem(field));
            form.reset();
        });
    </script>
</body>
</html>
```

### localStorage vs sessionStorage Comparison

| Use Case | Use localStorage | Use sessionStorage |
|----------|-----------------|-------------------|
| User preferences (theme, language) | ✓ | |
| Shopping cart | ✓ | |
| Authentication tokens | ✓ | |
| Form auto-save during session | | ✓ |
| Temporary filters/sorting | | ✓ |
| Multi-step form progress | | ✓ |
| Game high scores | ✓ | |
| Recent search history | ✓ | |

### Web Storage Events

Listen for storage changes across tabs/windows:

```javascript
// Listen for storage changes
window.addEventListener('storage', function(e) {
    console.log('Storage changed:');
    console.log('Key:', e.key);
    console.log('Old Value:', e.oldValue);
    console.log('New Value:', e.newValue);
    console.log('URL:', e.url);
});
```

### Web Storage Best Practices

1. **Always check for support:**
```javascript
if (typeof(Storage) !== 'undefined') {
    // localStorage/sessionStorage supported
    localStorage.setItem('key', 'value');
} else {
    // Storage not supported
    console.log('Web Storage not supported');
}
```

2. **Handle exceptions:**
```javascript
try {
    localStorage.setItem('key', 'value');
} catch (e) {
    if (e.name === 'QuotaExceededError') {
        console.log('Storage quota exceeded');
    }
}
```

3. **Don't store sensitive data:**
   - Never store passwords, credit card numbers, or sensitive personal data
   - Storage is accessible via JavaScript (XSS vulnerability)
   - Data is not encrypted

4. **Validate retrieved data:**
```javascript
const data = localStorage.getItem('user');
if (data) {
    try {
        const user = JSON.parse(data);
        // Validate user object structure
        if (user && user.name && user.email) {
            // Use validated data
        }
    } catch (e) {
        console.log('Invalid data in storage');
    }
}
```

---

## Geolocation

The Geolocation API allows web applications to access the user's geographical location (with permission).

### Geolocation Methods

#### `getCurrentPosition()` - Get current location

```javascript
if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(
        successCallback,
        errorCallback,
        options
    );
} else {
    console.log('Geolocation not supported');
}
```

#### Success Callback

```javascript
function successCallback(position) {
    const latitude = position.coords.latitude;
    const longitude = position.coords.longitude;
    const accuracy = position.coords.accuracy;
    const altitude = position.coords.altitude;
    const altitudeAccuracy = position.coords.altitudeAccuracy;
    const heading = position.coords.heading;
    const speed = position.coords.speed;
    const timestamp = position.timestamp;

    console.log(`Latitude: ${latitude}`);
    console.log(`Longitude: ${longitude}`);
    console.log(`Accuracy: ${accuracy} meters`);
}
```

#### Error Callback

```javascript
function errorCallback(error) {
    switch(error.code) {
        case error.PERMISSION_DENIED:
            console.log('User denied geolocation');
            break;
        case error.POSITION_UNAVAILABLE:
            console.log('Location unavailable');
            break;
        case error.TIMEOUT:
            console.log('Request timeout');
            break;
        case error.UNKNOWN_ERROR:
            console.log('Unknown error');
            break;
    }
}
```

#### Options

```javascript
const options = {
    enableHighAccuracy: true,  // Use GPS if available
    timeout: 5000,             // Wait max 5 seconds
    maximumAge: 0              // Don't use cached position
};

navigator.geolocation.getCurrentPosition(success, error, options);
```

### Geolocation Example: Display Location

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Geolocation Demo</title>
</head>
<body>
    <h1>Geolocation Demo</h1>
    <button onclick="getLocation()">Get My Location</button>

    <div id="location"></div>
    <div id="map"></div>

    <script>
        function getLocation() {
            const locationDiv = document.getElementById('location');

            if (!navigator.geolocation) {
                locationDiv.innerHTML = 'Geolocation is not supported by your browser';
                return;
            }

            locationDiv.innerHTML = 'Locating...';

            navigator.geolocation.getCurrentPosition(
                function(position) {
                    const lat = position.coords.latitude;
                    const lon = position.coords.longitude;
                    const accuracy = position.coords.accuracy;

                    locationDiv.innerHTML = `
                        <h2>Your Location:</h2>
                        <p><strong>Latitude:</strong> ${lat}</p>
                        <p><strong>Longitude:</strong> ${lon}</p>
                        <p><strong>Accuracy:</strong> ${accuracy} meters</p>
                        <p><a href="https://www.google.com/maps?q=${lat},${lon}" target="_blank">
                            View on Google Maps
                        </a></p>
                    `;
                },
                function(error) {
                    let message = 'Error getting location: ';
                    switch(error.code) {
                        case error.PERMISSION_DENIED:
                            message += 'User denied the request for Geolocation.';
                            break;
                        case error.POSITION_UNAVAILABLE:
                            message += 'Location information is unavailable.';
                            break;
                        case error.TIMEOUT:
                            message += 'The request to get user location timed out.';
                            break;
                        case error.UNKNOWN_ERROR:
                            message += 'An unknown error occurred.';
                            break;
                    }
                    locationDiv.innerHTML = message;
                }
            );
        }
    </script>
</body>
</html>
```

### Watching Position

Monitor location changes in real-time:

```javascript
let watchID;

// Start watching
function startWatching() {
    if (navigator.geolocation) {
        watchID = navigator.geolocation.watchPosition(
            function(position) {
                console.log('Position update:');
                console.log('Lat:', position.coords.latitude);
                console.log('Lon:', position.coords.longitude);
            },
            function(error) {
                console.log('Error:', error.message);
            },
            {
                enableHighAccuracy: true,
                timeout: 5000,
                maximumAge: 0
            }
        );
    }
}

// Stop watching
function stopWatching() {
    if (watchID) {
        navigator.geolocation.clearWatch(watchID);
        console.log('Stopped watching position');
    }
}
```

### Geolocation Example: Distance Calculator

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Distance Calculator</title>
</head>
<body>
    <h1>Distance to Landmark</h1>

    <button onclick="calculateDistance()">Calculate Distance to Statue of Liberty</button>

    <div id="result"></div>

    <script>
        // Statue of Liberty coordinates
        const landmark = {
            lat: 40.6892,
            lon: -74.0445,
            name: 'Statue of Liberty'
        };

        function calculateDistance() {
            const resultDiv = document.getElementById('result');

            if (!navigator.geolocation) {
                resultDiv.innerHTML = 'Geolocation not supported';
                return;
            }

            resultDiv.innerHTML = 'Getting your location...';

            navigator.geolocation.getCurrentPosition(
                function(position) {
                    const userLat = position.coords.latitude;
                    const userLon = position.coords.longitude;

                    // Calculate distance using Haversine formula
                    const distance = getDistanceInKm(
                        userLat, userLon,
                        landmark.lat, landmark.lon
                    );

                    resultDiv.innerHTML = `
                        <h2>Results:</h2>
                        <p><strong>Your Location:</strong> ${userLat.toFixed(4)}, ${userLon.toFixed(4)}</p>
                        <p><strong>${landmark.name}:</strong> ${landmark.lat}, ${landmark.lon}</p>
                        <p><strong>Distance:</strong> ${distance.toFixed(2)} km (${(distance * 0.621371).toFixed(2)} miles)</p>
                    `;
                },
                function(error) {
                    resultDiv.innerHTML = 'Error: ' + error.message;
                }
            );
        }

        // Haversine formula to calculate distance between two coordinates
        function getDistanceInKm(lat1, lon1, lat2, lon2) {
            const R = 6371; // Earth's radius in km
            const dLat = toRad(lat2 - lat1);
            const dLon = toRad(lon2 - lon1);

            const a = Math.sin(dLat / 2) * Math.sin(dLat / 2) +
                      Math.cos(toRad(lat1)) * Math.cos(toRad(lat2)) *
                      Math.sin(dLon / 2) * Math.sin(dLon / 2);

            const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
            return R * c;
        }

        function toRad(degrees) {
            return degrees * (Math.PI / 180);
        }
    </script>
</body>
</html>
```

### Geolocation Best Practices

1. **Always check for support:**
```javascript
if ('geolocation' in navigator) {
    // Geolocation available
}
```

2. **Handle errors gracefully:**
   - Provide fallback for denied permissions
   - Show user-friendly error messages
   - Don't require geolocation unless necessary

3. **Request permission contextually:**
   - Explain why you need location
   - Request when user triggers an action
   - Don't request on page load

4. **Respect privacy:**
   - Only request when necessary
   - Use appropriate accuracy level
   - Don't store location without consent

---

## WebSQL Database

**Important:** WebSQL is **deprecated** and should not be used in new projects. It has been replaced by **IndexedDB**.

### Why WebSQL is Deprecated

- Not standardized by W3C
- Only implemented in some browsers
- Removed from most modern browsers
- Replaced by IndexedDB

### WebSQL Basics (For Reference Only)

```javascript
// Opening database (DEPRECATED - DO NOT USE)
var db = openDatabase('mydb', '1.0', 'My Database', 2 * 1024 * 1024);

// Creating table
db.transaction(function(tx) {
    tx.executeSql('CREATE TABLE IF NOT EXISTS users (id unique, name)');
});

// Inserting data
db.transaction(function(tx) {
    tx.executeSql('INSERT INTO users (id, name) VALUES (1, "John Doe")');
});

// Querying data
db.transaction(function(tx) {
    tx.executeSql('SELECT * FROM users', [], function(tx, results) {
        var len = results.rows.length;
        for (var i = 0; i < len; i++) {
            console.log(results.rows.item(i).name);
        }
    });
});
```

### Modern Alternative: IndexedDB

Use IndexedDB instead of WebSQL for client-side database needs:

```javascript
// Open IndexedDB database
let db;
const request = indexedDB.open('MyDatabase', 1);

request.onerror = function(event) {
    console.log('Database error:', event.target.error);
};

request.onsuccess = function(event) {
    db = event.target.result;
    console.log('Database opened successfully');
};

request.onupgradeneeded = function(event) {
    db = event.target.result;

    // Create object store
    const objectStore = db.createObjectStore('users', { keyPath: 'id', autoIncrement: true });

    // Create indexes
    objectStore.createIndex('name', 'name', { unique: false });
    objectStore.createIndex('email', 'email', { unique: true });
};

// Adding data
function addUser(name, email) {
    const transaction = db.transaction(['users'], 'readwrite');
    const objectStore = transaction.objectStore('users');

    const user = {
        name: name,
        email: email
    };

    const request = objectStore.add(user);

    request.onsuccess = function() {
        console.log('User added');
    };

    request.onerror = function() {
        console.log('Error adding user');
    };
}

// Retrieving data
function getUser(id) {
    const transaction = db.transaction(['users'], 'readonly');
    const objectStore = transaction.objectStore('users');
    const request = objectStore.get(id);

    request.onsuccess = function(event) {
        if (request.result) {
            console.log('Name:', request.result.name);
            console.log('Email:', request.result.email);
        } else {
            console.log('User not found');
        }
    };
}
```

---

## Introduction to CSS3

CSS3 (Cascading Style Sheets Level 3) is used to style and layout HTML elements. While CSS is covered in detail in the next module, here's a brief introduction.

### What is CSS?

CSS describes how HTML elements should be displayed on screen, paper, or other media.

### CSS Syntax

```css
selector {
    property: value;
    property: value;
}
```

### Three Ways to Add CSS

#### 1. Inline CSS

Applied directly to HTML elements using the `style` attribute:

```html
<h1 style="color: blue; font-size: 24px;">Styled Heading</h1>
<p style="color: red; text-align: center;">Styled paragraph</p>
```

**Pros:** Quick for testing
**Cons:** Not reusable, hard to maintain

#### 2. Internal CSS

Placed in `<style>` tag within `<head>`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Internal CSS</title>
    <style>
        h1 {
            color: blue;
            font-size: 24px;
        }

        p {
            color: red;
            text-align: center;
        }

        .highlight {
            background-color: yellow;
        }
    </style>
</head>
<body>
    <h1>Styled Heading</h1>
    <p>Styled paragraph</p>
    <p class="highlight">Highlighted paragraph</p>
</body>
</html>
```

**Pros:** Styles contained in one page
**Cons:** Not reusable across pages

#### 3. External CSS (Best Practice)

Separate `.css` file linked to HTML:

**style.css:**
```css
h1 {
    color: blue;
    font-size: 24px;
}

p {
    color: red;
    text-align: center;
}

.highlight {
    background-color: yellow;
}
```

**index.html:**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>External CSS</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <h1>Styled Heading</h1>
    <p>Styled paragraph</p>
    <p class="highlight">Highlighted paragraph</p>
</body>
</html>
```

**Pros:** Reusable, maintainable, cacheable
**Cons:** Requires separate file

### CSS Selectors

```css
/* Element selector */
h1 {
    color: blue;
}

/* Class selector */
.highlight {
    background-color: yellow;
}

/* ID selector */
#main-heading {
    font-size: 32px;
}

/* Descendant selector */
div p {
    color: gray;
}

/* Multiple selectors */
h1, h2, h3 {
    font-family: Arial, sans-serif;
}
```

### Common CSS Properties

#### Text Styling

```css
p {
    color: #333;
    font-size: 16px;
    font-family: Arial, sans-serif;
    font-weight: bold;
    text-align: center;
    text-decoration: underline;
    line-height: 1.6;
}
```

#### Box Model

```css
div {
    width: 300px;
    height: 200px;
    padding: 20px;
    margin: 10px;
    border: 2px solid black;
    background-color: lightblue;
}
```

#### Layout

```css
.container {
    display: flex;
    justify-content: center;
    align-items: center;
}

.grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 20px;
}
```

### CSS3 New Features

#### Rounded Corners

```css
div {
    border-radius: 10px;
}
```

#### Shadows

```css
/* Box shadow */
div {
    box-shadow: 5px 5px 10px rgba(0, 0, 0, 0.3);
}

/* Text shadow */
h1 {
    text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
}
```

#### Gradients

```css
/* Linear gradient */
div {
    background: linear-gradient(to right, red, blue);
}

/* Radial gradient */
div {
    background: radial-gradient(circle, yellow, orange, red);
}
```

#### Transitions

```css
button {
    background-color: blue;
    transition: background-color 0.3s ease;
}

button:hover {
    background-color: darkblue;
}
```

#### Animations

```css
@keyframes slide {
    from {
        transform: translateX(0);
    }
    to {
        transform: translateX(100px);
    }
}

div {
    animation: slide 2s infinite;
}
```

#### Transforms

```css
/* 2D transforms */
div {
    transform: rotate(45deg);
    transform: scale(1.5);
    transform: translate(50px, 100px);
    transform: skew(10deg, 20deg);
}

/* 3D transforms */
div {
    transform: rotateX(45deg);
    transform: rotateY(45deg);
    transform: perspective(500px) rotateY(45deg);
}
```

### Complete CSS Example

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSS3 Demo</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: Arial, sans-serif;
            background: linear-gradient(to bottom, #667eea, #764ba2);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .card {
            background: white;
            padding: 40px;
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
            max-width: 400px;
            text-align: center;
            transition: transform 0.3s ease;
        }

        .card:hover {
            transform: translateY(-10px);
        }

        h1 {
            color: #667eea;
            margin-bottom: 20px;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
        }

        p {
            color: #555;
            line-height: 1.6;
            margin-bottom: 30px;
        }

        button {
            background: linear-gradient(to right, #667eea, #764ba2);
            color: white;
            padding: 15px 40px;
            border: none;
            border-radius: 30px;
            font-size: 16px;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        button:hover {
            transform: scale(1.05);
            box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
        }

        @keyframes fadeIn {
            from {
                opacity: 0;
                transform: translateY(20px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        .card {
            animation: fadeIn 0.8s ease;
        }
    </style>
</head>
<body>
    <div class="card">
        <h1>Welcome to CSS3</h1>
        <p>This card demonstrates modern CSS3 features including gradients, shadows, transitions, and animations.</p>
        <button>Click Me</button>
    </div>
</body>
</html>
```

### CSS3 Features Summary

| Feature | Description | Example |
|---------|-------------|---------|
| Border Radius | Rounded corners | `border-radius: 10px;` |
| Box Shadow | Drop shadows | `box-shadow: 5px 5px 10px rgba(0,0,0,0.3);` |
| Text Shadow | Text shadows | `text-shadow: 2px 2px 4px #000;` |
| Gradients | Color gradients | `background: linear-gradient(red, blue);` |
| Transitions | Smooth property changes | `transition: all 0.3s ease;` |
| Animations | Keyframe animations | `animation: slide 2s infinite;` |
| Transforms | 2D/3D transformations | `transform: rotate(45deg);` |
| Flexbox | Flexible layouts | `display: flex;` |
| Grid | Grid layouts | `display: grid;` |
| Media Queries | Responsive design | `@media (max-width: 768px) {...}` |

---

## Summary

| Technology | Purpose | Key Points |
|------------|---------|------------|
| Web Storage | Client-side data storage | localStorage (persistent), sessionStorage (session) |
| Geolocation | Access user location | getCurrentPosition(), watchPosition() |
| WebSQL | Client-side database | **DEPRECATED** - Use IndexedDB instead |
| CSS3 | Styling HTML | Selectors, properties, animations, layouts |

---

## Best Practices Summary

### Web Storage
- Check for browser support
- Handle quota exceeded errors
- Don't store sensitive data
- Validate retrieved data
- Use localStorage for persistent data
- Use sessionStorage for temporary data

### Geolocation
- Always check for support
- Handle errors gracefully
- Request permission contextually
- Respect user privacy
- Use appropriate accuracy settings

### CSS
- Use external stylesheets
- Follow naming conventions
- Organize CSS logically
- Use CSS preprocessors (SASS/LESS) for large projects
- Optimize for performance
- Make responsive designs

---

## Next Steps

You've completed the HTML5 module! Next, proceed to:
- [Module 5: CSS3](../../05-css/) for in-depth CSS styling and layouts
- Practice building complete web pages combining HTML and CSS
- Explore responsive web design techniques

---

## Additional Resources

### Web Storage
- [MDN Web Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)
- [HTML5 Storage Specification](https://html.spec.whatwg.org/multipage/webstorage.html)

### Geolocation
- [MDN Geolocation API](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API)
- [W3C Geolocation API Specification](https://www.w3.org/TR/geolocation-API/)

### IndexedDB (WebSQL Replacement)
- [MDN IndexedDB API](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API)
- [IndexedDB Tutorial](https://javascript.info/indexeddb)

### CSS3
- [MDN CSS Documentation](https://developer.mozilla.org/en-US/docs/Web/CSS)
- [CSS3 Specification](https://www.w3.org/Style/CSS/)
- [CSS Tricks](https://css-tricks.com/)
- [Can I Use](https://caniuse.com/) - Browser compatibility reference
