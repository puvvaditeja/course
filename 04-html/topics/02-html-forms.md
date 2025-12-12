# HTML Forms

## Overview

HTML forms are the primary way to collect user input on web pages. They enable interactive communication between users and web applications through various input controls like text fields, checkboxes, radio buttons, and dropdown menus.

---

## Form Element

The `<form>` element is a container that wraps all form controls and defines how the form data should be submitted.

### Basic Form Structure

```html
<form action="/submit" method="POST">
    <!-- Form controls go here -->
    <input type="text" name="username">
    <button type="submit">Submit</button>
</form>
```

### Form Workflow

1. User fills out form fields
2. User submits the form (click submit button)
3. Browser collects form data
4. Data is sent to server based on `action` and `method` attributes
5. Server processes the data and sends response

---

## Form Attributes

Form attributes control how and where form data is sent.

### Essential Form Attributes

#### `action`

Specifies where to send form data when submitted.

```html
<!-- Send to server endpoint -->
<form action="/api/users" method="POST">
    <!-- form fields -->
</form>

<!-- Send to different domain -->
<form action="https://example.com/submit" method="POST">
    <!-- form fields -->
</form>

<!-- Empty action submits to current page -->
<form action="" method="POST">
    <!-- form fields -->
</form>
```

#### `method`

Specifies the HTTP method to use when sending form data.

```html
<!-- GET method - data in URL -->
<form action="/search" method="GET">
    <input type="text" name="query">
    <button type="submit">Search</button>
</form>
<!-- Submits to: /search?query=user_input -->

<!-- POST method - data in request body -->
<form action="/register" method="POST">
    <input type="text" name="username">
    <input type="password" name="password">
    <button type="submit">Register</button>
</form>
```

#### Other Form Attributes

| Attribute | Purpose | Example |
|-----------|---------|---------|
| `name` | Form identifier | `<form name="loginForm">` |
| `target` | Where to display response | `<form target="_blank">` |
| `enctype` | How to encode data | `<form enctype="multipart/form-data">` |
| `autocomplete` | Enable/disable autocomplete | `<form autocomplete="off">` |
| `novalidate` | Disable HTML5 validation | `<form novalidate>` |
| `accept-charset` | Character encoding | `<form accept-charset="UTF-8">` |

### Encoding Types (`enctype`)

```html
<!-- Default: application/x-www-form-urlencoded -->
<form action="/submit" method="POST">
    <!-- Regular form data -->
</form>

<!-- For file uploads: multipart/form-data -->
<form action="/upload" method="POST" enctype="multipart/form-data">
    <input type="file" name="document">
    <button type="submit">Upload</button>
</form>

<!-- Plain text (rarely used): text/plain -->
<form action="/submit" method="POST" enctype="text/plain">
    <!-- form fields -->
</form>
```

---

## Input Elements

The `<input>` element is the most versatile form control, with behavior determined by its `type` attribute.

### Basic Input Syntax

```html
<input type="type_name" name="field_name" id="field_id" value="default_value">
```

### Common Input Attributes

| Attribute | Purpose | Example |
|-----------|---------|---------|
| `type` | Input type | `type="email"` |
| `name` | Field name (sent to server) | `name="username"` |
| `id` | Unique identifier | `id="email"` |
| `value` | Default/current value | `value="John"` |
| `placeholder` | Hint text | `placeholder="Enter email"` |
| `required` | Field must be filled | `required` |
| `disabled` | Disable input | `disabled` |
| `readonly` | Read-only (can't edit) | `readonly` |
| `maxlength` | Max character length | `maxlength="50"` |
| `minlength` | Min character length | `minlength="8"` |
| `pattern` | Regex validation | `pattern="[0-9]{3}-[0-9]{4}"` |
| `autocomplete` | Autocomplete hint | `autocomplete="email"` |
| `autofocus` | Auto-focus on load | `autofocus` |

---

## Input Types

HTML5 introduced many specialized input types for better user experience and built-in validation.

### Text Input Types

#### `text` - Basic text input

```html
<label for="username">Username:</label>
<input type="text" id="username" name="username" placeholder="Enter username" required>
```

#### `password` - Password input (masked)

```html
<label for="password">Password:</label>
<input type="password" id="password" name="password" minlength="8" required>
```

#### `email` - Email validation

```html
<label for="email">Email:</label>
<input type="email" id="email" name="email" placeholder="user@example.com" required>
```

#### `url` - URL validation

```html
<label for="website">Website:</label>
<input type="url" id="website" name="website" placeholder="https://example.com">
```

#### `tel` - Telephone number

```html
<label for="phone">Phone:</label>
<input type="tel" id="phone" name="phone" pattern="[0-9]{3}-[0-9]{3}-[0-9]{4}" placeholder="123-456-7890">
```

#### `search` - Search field

```html
<label for="search">Search:</label>
<input type="search" id="search" name="search" placeholder="Search...">
```

### Numeric Input Types

#### `number` - Numeric input with spinners

```html
<label for="age">Age:</label>
<input type="number" id="age" name="age" min="0" max="120" step="1" value="25">
```

#### `range` - Slider control

```html
<label for="volume">Volume:</label>
<input type="range" id="volume" name="volume" min="0" max="100" step="5" value="50">
<output for="volume">50</output>
```

### Date and Time Input Types

#### `date` - Date picker

```html
<label for="birthdate">Birth Date:</label>
<input type="date" id="birthdate" name="birthdate" min="1900-01-01" max="2025-12-31">
```

#### `time` - Time picker

```html
<label for="appointment">Appointment Time:</label>
<input type="time" id="appointment" name="appointment" min="09:00" max="17:00">
```

#### `datetime-local` - Date and time picker

```html
<label for="meeting">Meeting:</label>
<input type="datetime-local" id="meeting" name="meeting">
```

#### `month` - Month and year picker

```html
<label for="start-month">Start Month:</label>
<input type="month" id="start-month" name="start-month">
```

#### `week` - Week picker

```html
<label for="week">Week:</label>
<input type="week" id="week" name="week">
```

### Other Input Types

#### `color` - Color picker

```html
<label for="favcolor">Favorite Color:</label>
<input type="color" id="favcolor" name="favcolor" value="#ff0000">
```

#### `file` - File upload

```html
<label for="document">Upload Document:</label>
<input type="file" id="document" name="document" accept=".pdf,.doc,.docx" multiple>
```

#### `hidden` - Hidden field (not visible to user)

```html
<input type="hidden" name="user_id" value="12345">
```

### Selection Input Types

#### `checkbox` - Multiple selections allowed

```html
<fieldset>
    <legend>Select your interests:</legend>

    <input type="checkbox" id="coding" name="interests" value="coding">
    <label for="coding">Coding</label>

    <input type="checkbox" id="design" name="interests" value="design">
    <label for="design">Design</label>

    <input type="checkbox" id="music" name="interests" value="music" checked>
    <label for="music">Music</label>
</fieldset>
```

#### `radio` - Single selection from group

```html
<fieldset>
    <legend>Select your gender:</legend>

    <input type="radio" id="male" name="gender" value="male">
    <label for="male">Male</label>

    <input type="radio" id="female" name="gender" value="female">
    <label for="female">Female</label>

    <input type="radio" id="other" name="gender" value="other">
    <label for="other">Other</label>
</fieldset>
```

### Button Input Types

#### `submit` - Submit form

```html
<input type="submit" value="Submit Form">
<!-- Or use button element -->
<button type="submit">Submit</button>
```

#### `reset` - Reset form to defaults

```html
<input type="reset" value="Reset Form">
<!-- Or use button element -->
<button type="reset">Reset</button>
```

#### `button` - Generic button (requires JavaScript)

```html
<input type="button" value="Click Me" onclick="alert('Clicked!')">
<!-- Or use button element -->
<button type="button" onclick="alert('Clicked!')">Click Me</button>
```

### Input Types Summary Table

| Type | Purpose | Validation |
|------|---------|------------|
| `text` | General text | Length, pattern |
| `password` | Password (masked) | Length, pattern |
| `email` | Email address | Email format |
| `url` | Web address | URL format |
| `tel` | Phone number | Pattern |
| `number` | Numeric value | Min, max, step |
| `range` | Slider | Min, max, step |
| `date` | Date selection | Date range |
| `time` | Time selection | Time range |
| `color` | Color picker | Color format |
| `file` | File upload | File type, size |
| `checkbox` | Multiple choice | - |
| `radio` | Single choice | - |

---

## Label Element

The `<label>` element defines a label for form controls, improving usability and accessibility.

### Label Syntax

#### Method 1: Using `for` attribute

```html
<label for="username">Username:</label>
<input type="text" id="username" name="username">
```

#### Method 2: Wrapping the input

```html
<label>
    Username:
    <input type="text" name="username">
</label>
```

### Benefits of Labels

1. **Accessibility**: Screen readers can associate labels with inputs
2. **Usability**: Clicking label focuses the input
3. **Better UX**: Larger click target for checkboxes/radios

### Label Examples

```html
<!-- Text input -->
<label for="email">Email Address:</label>
<input type="email" id="email" name="email" required>

<!-- Checkbox -->
<label for="subscribe">
    <input type="checkbox" id="subscribe" name="subscribe">
    Subscribe to newsletter
</label>

<!-- Radio buttons -->
<fieldset>
    <legend>Shipping Method:</legend>

    <label>
        <input type="radio" name="shipping" value="standard">
        Standard (5-7 days)
    </label>

    <label>
        <input type="radio" name="shipping" value="express">
        Express (2-3 days)
    </label>
</fieldset>
```

---

## Textarea

The `<textarea>` element provides a multi-line text input area.

### Textarea Syntax

```html
<label for="message">Message:</label>
<textarea id="message" name="message" rows="5" cols="40" placeholder="Enter your message..."></textarea>
```

### Textarea Attributes

| Attribute | Purpose | Example |
|-----------|---------|---------|
| `rows` | Number of visible rows | `rows="5"` |
| `cols` | Number of visible columns | `cols="40"` |
| `placeholder` | Hint text | `placeholder="Type here..."` |
| `maxlength` | Maximum characters | `maxlength="500"` |
| `minlength` | Minimum characters | `minlength="10"` |
| `required` | Field required | `required` |
| `readonly` | Read-only | `readonly` |
| `disabled` | Disabled | `disabled` |
| `wrap` | Text wrapping | `wrap="hard"` or `wrap="soft"` |

### Textarea Examples

```html
<!-- Basic textarea -->
<label for="bio">Biography:</label>
<textarea id="bio" name="bio" rows="6" cols="50" placeholder="Tell us about yourself..."></textarea>

<!-- With character limit -->
<label for="comment">Comment (max 200 characters):</label>
<textarea id="comment" name="comment" maxlength="200" rows="4" cols="50"></textarea>

<!-- Required textarea -->
<label for="feedback">Feedback (required):</label>
<textarea id="feedback" name="feedback" required rows="5" cols="50"></textarea>

<!-- Readonly textarea -->
<label for="terms">Terms and Conditions:</label>
<textarea id="terms" readonly rows="10" cols="60">
Terms and conditions text here...
This cannot be edited by the user.
</textarea>
```

---

## Select and Option

The `<select>` element creates a dropdown menu, containing `<option>` elements for each choice.

### Basic Select

```html
<label for="country">Country:</label>
<select id="country" name="country">
    <option value="">--Select a country--</option>
    <option value="us">United States</option>
    <option value="uk">United Kingdom</option>
    <option value="ca">Canada</option>
    <option value="au">Australia</option>
</select>
```

### Select Attributes

| Attribute | Purpose | Example |
|-----------|---------|---------|
| `name` | Field name | `name="country"` |
| `id` | Unique identifier | `id="country"` |
| `required` | Must select an option | `required` |
| `disabled` | Disable dropdown | `disabled` |
| `multiple` | Allow multiple selections | `multiple` |
| `size` | Visible options | `size="5"` |
| `autofocus` | Auto-focus on load | `autofocus` |

### Option Attributes

| Attribute | Purpose | Example |
|-----------|---------|---------|
| `value` | Value sent to server | `value="us"` |
| `selected` | Pre-selected option | `selected` |
| `disabled` | Disable option | `disabled` |
| `label` | Alternative label | `label="USA"` |

### Select Examples

#### With default selection

```html
<label for="language">Preferred Language:</label>
<select id="language" name="language">
    <option value="en" selected>English</option>
    <option value="es">Spanish</option>
    <option value="fr">French</option>
    <option value="de">German</option>
</select>
```

#### With option groups

```html
<label for="vehicle">Select Vehicle:</label>
<select id="vehicle" name="vehicle">
    <optgroup label="Cars">
        <option value="sedan">Sedan</option>
        <option value="suv">SUV</option>
        <option value="hatchback">Hatchback</option>
    </optgroup>
    <optgroup label="Motorcycles">
        <option value="sport">Sport Bike</option>
        <option value="cruiser">Cruiser</option>
        <option value="touring">Touring</option>
    </optgroup>
</select>
```

#### With disabled options

```html
<label for="membership">Membership Level:</label>
<select id="membership" name="membership">
    <option value="free">Free</option>
    <option value="basic">Basic - $9.99/month</option>
    <option value="premium">Premium - $19.99/month</option>
    <option value="enterprise" disabled>Enterprise - Contact Sales</option>
</select>
```

---

## Multi-Select

Multi-select allows users to select multiple options from a list.

### Multi-Select Syntax

```html
<label for="technologies">Select Technologies (hold Ctrl/Cmd):</label>
<select id="technologies" name="technologies" multiple size="6">
    <option value="html">HTML</option>
    <option value="css">CSS</option>
    <option value="javascript">JavaScript</option>
    <option value="react">React</option>
    <option value="angular">Angular</option>
    <option value="vue">Vue.js</option>
</select>
```

### Multi-Select with Pre-selected Options

```html
<label for="skills">Your Skills:</label>
<select id="skills" name="skills" multiple size="8">
    <option value="java" selected>Java</option>
    <option value="python">Python</option>
    <option value="javascript" selected>JavaScript</option>
    <option value="csharp">C#</option>
    <option value="ruby">Ruby</option>
    <option value="php">PHP</option>
    <option value="go">Go</option>
    <option value="rust">Rust</option>
</select>
```

### Alternative: Checkbox Group

For better UX, consider using checkboxes instead of multi-select:

```html
<fieldset>
    <legend>Select Technologies:</legend>

    <label><input type="checkbox" name="tech" value="html"> HTML</label><br>
    <label><input type="checkbox" name="tech" value="css"> CSS</label><br>
    <label><input type="checkbox" name="tech" value="js"> JavaScript</label><br>
    <label><input type="checkbox" name="tech" value="react"> React</label><br>
    <label><input type="checkbox" name="tech" value="angular"> Angular</label><br>
    <label><input type="checkbox" name="tech" value="vue"> Vue.js</label><br>
</fieldset>
```

---

## Fieldset and Legend

`<fieldset>` groups related form controls, and `<legend>` provides a caption for the group.

### Basic Fieldset

```html
<fieldset>
    <legend>Personal Information</legend>

    <label for="fname">First Name:</label>
    <input type="text" id="fname" name="fname"><br><br>

    <label for="lname">Last Name:</label>
    <input type="text" id="lname" name="lname"><br><br>

    <label for="email">Email:</label>
    <input type="email" id="email" name="email">
</fieldset>
```

### Multiple Fieldsets

```html
<form action="/register" method="POST">
    <fieldset>
        <legend>Account Details</legend>
        <label for="username">Username:</label>
        <input type="text" id="username" name="username" required><br><br>

        <label for="password">Password:</label>
        <input type="password" id="password" name="password" required>
    </fieldset>

    <fieldset>
        <legend>Personal Information</legend>
        <label for="fullname">Full Name:</label>
        <input type="text" id="fullname" name="fullname" required><br><br>

        <label for="email">Email:</label>
        <input type="email" id="email" name="email" required>
    </fieldset>

    <fieldset>
        <legend>Preferences</legend>
        <label>
            <input type="checkbox" name="newsletter" value="yes">
            Subscribe to newsletter
        </label><br>

        <label>
            <input type="checkbox" name="notifications" value="yes" checked>
            Enable notifications
        </label>
    </fieldset>

    <button type="submit">Register</button>
</form>
```

### Disabled Fieldset

```html
<fieldset disabled>
    <legend>Billing Information (Coming Soon)</legend>
    <label for="card">Credit Card:</label>
    <input type="text" id="card" name="card"><br><br>

    <label for="expiry">Expiry:</label>
    <input type="text" id="expiry" name="expiry">
</fieldset>
```

---

## HTML5 Form Validation

HTML5 provides built-in form validation without JavaScript.

### Validation Attributes

#### `required` - Field must be filled

```html
<input type="text" name="username" required>
<input type="email" name="email" required>
```

#### `pattern` - Regex validation

```html
<!-- Phone number: 123-456-7890 -->
<input type="tel" pattern="[0-9]{3}-[0-9]{3}-[0-9]{4}"
       placeholder="123-456-7890" title="Format: 123-456-7890">

<!-- Zip code: 5 digits -->
<input type="text" pattern="[0-9]{5}"
       placeholder="12345" title="5-digit zip code">

<!-- Username: 3-16 alphanumeric -->
<input type="text" pattern="[a-zA-Z0-9]{3,16}"
       placeholder="Username" title="3-16 alphanumeric characters">
```

#### `min` and `max` - Numeric/date range

```html
<!-- Age between 18 and 100 -->
<input type="number" min="18" max="100" name="age">

<!-- Date range -->
<input type="date" min="2025-01-01" max="2025-12-31" name="event_date">

<!-- Price between $1 and $1000 -->
<input type="number" min="1" max="1000" step="0.01" name="price">
```

#### `minlength` and `maxlength` - String length

```html
<!-- Password: 8-20 characters -->
<input type="password" minlength="8" maxlength="20" name="password">

<!-- Bio: at least 50 characters -->
<textarea minlength="50" maxlength="500" name="bio"></textarea>
```

#### `step` - Numeric increment

```html
<!-- Increments of 0.5 -->
<input type="number" step="0.5" name="rating">

<!-- Increments of 5 -->
<input type="number" min="0" max="100" step="5" name="percentage">
```

### Input Type Validation

Certain input types have automatic validation:

```html
<!-- Validates email format -->
<input type="email" name="email" required>

<!-- Validates URL format -->
<input type="url" name="website" required>

<!-- Validates number -->
<input type="number" name="quantity" min="1" required>
```

### Custom Validation Messages

Use the `title` attribute to provide custom validation messages:

```html
<input type="text"
       pattern="[A-Z][a-z]+"
       title="First letter must be uppercase, rest lowercase"
       placeholder="Name">
```

### Validation Example: Registration Form

```html
<form action="/register" method="POST">
    <fieldset>
        <legend>Registration</legend>

        <!-- Username: 3-20 alphanumeric -->
        <label for="username">Username:</label>
        <input type="text" id="username" name="username"
               pattern="[a-zA-Z0-9_]{3,20}"
               title="3-20 alphanumeric characters or underscore"
               required><br><br>

        <!-- Email validation -->
        <label for="email">Email:</label>
        <input type="email" id="email" name="email" required><br><br>

        <!-- Password: min 8 characters -->
        <label for="password">Password:</label>
        <input type="password" id="password" name="password"
               minlength="8"
               title="Minimum 8 characters"
               required><br><br>

        <!-- Age: 18-100 -->
        <label for="age">Age:</label>
        <input type="number" id="age" name="age"
               min="18" max="100" required><br><br>

        <!-- Website (optional) -->
        <label for="website">Website:</label>
        <input type="url" id="website" name="website"
               placeholder="https://example.com"><br><br>

        <!-- Phone: 123-456-7890 -->
        <label for="phone">Phone:</label>
        <input type="tel" id="phone" name="phone"
               pattern="[0-9]{3}-[0-9]{3}-[0-9]{4}"
               placeholder="123-456-7890"
               title="Format: 123-456-7890"><br><br>

        <!-- Terms acceptance required -->
        <label>
            <input type="checkbox" name="terms" required>
            I accept the terms and conditions
        </label><br><br>

        <button type="submit">Register</button>
    </fieldset>
</form>
```

### Disabling Validation

To disable HTML5 validation (for custom JavaScript validation):

```html
<!-- Disable validation on entire form -->
<form novalidate>
    <!-- form fields -->
</form>

<!-- Disable validation on specific button -->
<button type="submit" formnovalidate>Submit without validation</button>
```

---

## GET vs POST

The two most common HTTP methods for sending form data.

### GET Method

Appends form data to the URL as query parameters.

```html
<form action="/search" method="GET">
    <input type="text" name="query" placeholder="Search...">
    <input type="number" name="page" value="1">
    <button type="submit">Search</button>
</form>
<!-- Submits to: /search?query=hello&page=1 -->
```

**GET Characteristics:**

| Aspect | Details |
|--------|---------|
| Data Location | URL query string |
| Visibility | Visible in URL |
| Bookmarkable | Yes |
| Cacheable | Yes |
| Length Limit | Limited by URL length (~2000 chars) |
| Security | Less secure (data visible) |
| Idempotent | Yes (repeatable without side effects) |
| Back Button | Safe |

**When to Use GET:**
- Search forms
- Filtering/sorting data
- Retrieving data
- Shareable URLs
- Bookmarkable pages

**Example Use Cases:**
```html
<!-- Search -->
<form action="/products" method="GET">
    <input type="search" name="q">
    <button type="submit">Search</button>
</form>
<!-- URL: /products?q=laptop -->

<!-- Filter -->
<form action="/articles" method="GET">
    <select name="category">
        <option value="tech">Technology</option>
        <option value="sports">Sports</option>
    </select>
    <button type="submit">Filter</button>
</form>
<!-- URL: /articles?category=tech -->
```

### POST Method

Sends form data in the HTTP request body.

```html
<form action="/register" method="POST">
    <input type="text" name="username">
    <input type="password" name="password">
    <button type="submit">Register</button>
</form>
<!-- Data sent in request body, not visible in URL -->
```

**POST Characteristics:**

| Aspect | Details |
|--------|---------|
| Data Location | Request body |
| Visibility | Hidden from URL |
| Bookmarkable | No |
| Cacheable | No |
| Length Limit | No practical limit |
| Security | More secure (data not in URL) |
| Idempotent | No (may cause changes) |
| Back Button | May warn about resubmission |

**When to Use POST:**
- Submitting sensitive data (passwords, credit cards)
- Creating/updating resources
- File uploads
- Large amounts of data
- Actions that modify server state

**Example Use Cases:**
```html
<!-- Login (sensitive data) -->
<form action="/login" method="POST">
    <input type="email" name="email">
    <input type="password" name="password">
    <button type="submit">Login</button>
</form>

<!-- File upload -->
<form action="/upload" method="POST" enctype="multipart/form-data">
    <input type="file" name="document">
    <button type="submit">Upload</button>
</form>

<!-- Create resource -->
<form action="/posts" method="POST">
    <input type="text" name="title">
    <textarea name="content"></textarea>
    <button type="submit">Create Post</button>
</form>
```

### GET vs POST Comparison

| Use Case | Method | Reason |
|----------|--------|--------|
| Search form | GET | Bookmarkable, shareable results |
| Login form | POST | Password security |
| Contact form | POST | Data shouldn't be in URL |
| Filter products | GET | Shareable filter URLs |
| Registration | POST | Sensitive data, creates account |
| Pagination | GET | Bookmarkable page numbers |
| File upload | POST | Required for files |
| Delete action | POST | Modifies server state |

### Security Considerations

**Never use GET for:**
- Passwords
- Credit card numbers
- Personal identification
- Any data that shouldn't be in browser history/logs

**GET Security Issue Example:**
```html
<!-- BAD: Password visible in URL -->
<form action="/login" method="GET">
    <input type="password" name="password">
    <button type="submit">Login</button>
</form>
<!-- Results in: /login?password=secretpass123 - NEVER DO THIS! -->
```

**POST Security Best Practice:**
```html
<!-- GOOD: Sensitive data in request body -->
<form action="/login" method="POST">
    <input type="email" name="email">
    <input type="password" name="password">
    <button type="submit">Login</button>
</form>
```

---

## Complete Form Example

Here's a comprehensive form demonstrating all concepts:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Event Registration Form</title>
</head>
<body>
    <h1>Event Registration</h1>

    <form action="/register-event" method="POST" enctype="multipart/form-data">

        <!-- Personal Information -->
        <fieldset>
            <legend>Personal Information</legend>

            <label for="fullname">Full Name:</label>
            <input type="text" id="fullname" name="fullname" required><br><br>

            <label for="email">Email:</label>
            <input type="email" id="email" name="email" required><br><br>

            <label for="phone">Phone:</label>
            <input type="tel" id="phone" name="phone"
                   pattern="[0-9]{3}-[0-9]{3}-[0-9]{4}"
                   placeholder="123-456-7890"><br><br>

            <label for="birthdate">Birth Date:</label>
            <input type="date" id="birthdate" name="birthdate"
                   min="1900-01-01" max="2007-12-31"><br><br>
        </fieldset>

        <!-- Event Details -->
        <fieldset>
            <legend>Event Details</legend>

            <label for="event">Select Event:</label>
            <select id="event" name="event" required>
                <option value="">--Choose an event--</option>
                <option value="web-dev">Web Development Workshop</option>
                <option value="data-sci">Data Science Seminar</option>
                <option value="mobile">Mobile App Development</option>
            </select><br><br>

            <label for="attendees">Number of Attendees:</label>
            <input type="number" id="attendees" name="attendees"
                   min="1" max="10" value="1" required><br><br>

            <label>Ticket Type:</label><br>
            <input type="radio" id="regular" name="ticket" value="regular" checked>
            <label for="regular">Regular - $50</label><br>
            <input type="radio" id="vip" name="ticket" value="vip">
            <label for="vip">VIP - $100</label><br><br>
        </fieldset>

        <!-- Preferences -->
        <fieldset>
            <legend>Preferences</legend>

            <label>Dietary Restrictions:</label><br>
            <input type="checkbox" id="vegetarian" name="dietary" value="vegetarian">
            <label for="vegetarian">Vegetarian</label><br>
            <input type="checkbox" id="vegan" name="dietary" value="vegan">
            <label for="vegan">Vegan</label><br>
            <input type="checkbox" id="gluten-free" name="dietary" value="gluten-free">
            <label for="gluten-free">Gluten-Free</label><br><br>

            <label for="tshirt">T-Shirt Size:</label>
            <select id="tshirt" name="tshirt">
                <option value="s">Small</option>
                <option value="m" selected>Medium</option>
                <option value="l">Large</option>
                <option value="xl">Extra Large</option>
            </select><br><br>
        </fieldset>

        <!-- Additional Information -->
        <fieldset>
            <legend>Additional Information</legend>

            <label for="resume">Upload Resume (optional):</label>
            <input type="file" id="resume" name="resume"
                   accept=".pdf,.doc,.docx"><br><br>

            <label for="comments">Comments/Questions:</label><br>
            <textarea id="comments" name="comments"
                      rows="5" cols="50"
                      placeholder="Any special requirements or questions?"></textarea><br><br>
        </fieldset>

        <!-- Agreement -->
        <label>
            <input type="checkbox" name="terms" required>
            I agree to the terms and conditions
        </label><br><br>

        <label>
            <input type="checkbox" name="marketing">
            Send me marketing emails about future events
        </label><br><br>

        <!-- Submit Buttons -->
        <button type="submit">Register</button>
        <button type="reset">Clear Form</button>
    </form>
</body>
</html>
```

---

## Best Practices

### 1. Always Use Labels

```html
<!-- Good -->
<label for="email">Email:</label>
<input type="email" id="email" name="email">

<!-- Bad -->
<input type="email" name="email">
```

### 2. Use Appropriate Input Types

```html
<!-- Good: Uses specific types -->
<input type="email" name="email">
<input type="tel" name="phone">
<input type="date" name="birthdate">

<!-- Bad: Uses generic text -->
<input type="text" name="email">
<input type="text" name="phone">
<input type="text" name="birthdate">
```

### 3. Provide Validation and Feedback

```html
<input type="text"
       name="username"
       pattern="[a-zA-Z0-9]{3,20}"
       title="3-20 alphanumeric characters"
       placeholder="Enter username"
       required>
```

### 4. Group Related Fields

```html
<fieldset>
    <legend>Shipping Address</legend>
    <!-- address fields -->
</fieldset>

<fieldset>
    <legend>Billing Address</legend>
    <!-- address fields -->
</fieldset>
```

### 5. Use Proper HTTP Methods

```html
<!-- Searching/filtering: GET -->
<form action="/search" method="GET">

<!-- Creating/updating: POST -->
<form action="/register" method="POST">
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Form Element | Container for form controls with action/method |
| Input Types | text, email, password, number, date, file, etc. |
| Labels | Associate labels with inputs for accessibility |
| Textarea | Multi-line text input |
| Select/Option | Dropdown menus for selections |
| Fieldset/Legend | Group related form controls |
| HTML5 Validation | required, pattern, min/max, minlength/maxlength |
| GET vs POST | GET for queries, POST for sensitive/create operations |

---

## Next Topic

Continue to [HTML5 Advanced Features](./03-html5-advanced.md) to learn about web storage, geolocation, and other advanced HTML5 APIs.
