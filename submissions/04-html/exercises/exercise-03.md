# Exercise: Build a Survey Form

## Objective
Create a comprehensive survey form using all HTML5 form elements including select dropdowns, checkboxes, radio buttons, textareas, and various input types.

## Requirements

Build an HTML page (`survey-form.html`) that includes:

### 1. Page Structure
- Proper DOCTYPE and meta tags
- Page title: "Customer Satisfaction Survey"
- Form should use `POST` method with action `#`

### 2. Form Sections

Organize the form using `<fieldset>` and `<legend>`:

#### Section 1: Personal Information
```html
<fieldset>
    <legend>Personal Information</legend>
    <!-- fields here -->
</fieldset>
```

Required fields:
- **Full Name** - text input (required, minlength=2, maxlength=100)
- **Email** - email input (required)
- **Phone** - tel input (pattern for phone format)
- **Age Range** - select dropdown:
  - Under 18
  - 18-24
  - 25-34
  - 35-44
  - 45-54
  - 55+
- **Gender** - radio buttons:
  - Male
  - Female
  - Prefer not to say

#### Section 2: Product Feedback
- **Products Used** - checkboxes (select multiple):
  - [ ] Product A - Web Application
  - [ ] Product B - Mobile App
  - [ ] Product C - Desktop Software
  - [ ] Product D - API Services
  - [ ] Product E - Cloud Storage

- **Primary Product** - select dropdown (same options as above)

- **How long have you been a customer?** - select dropdown:
  - Less than 1 month
  - 1-6 months
  - 6-12 months
  - 1-2 years
  - More than 2 years

#### Section 3: Satisfaction Rating
- **Overall Satisfaction** - radio buttons (1-5 stars)
- **Product Quality** - range input (1-10)
- **Customer Support** - range input (1-10)
- **Value for Money** - range input (1-10)
- **Would you recommend us?** - radio buttons:
  - Definitely
  - Probably
  - Not sure
  - Probably not
  - Definitely not

#### Section 4: Detailed Feedback
- **What do you like most?** - textarea (rows=4, maxlength=500)
- **What can we improve?** - textarea (rows=4, maxlength=500)
- **Additional Comments** - textarea (rows=6, maxlength=1000)

#### Section 5: Contact Preferences
- **Preferred Contact Method** - checkboxes:
  - [ ] Email
  - [ ] Phone
  - [ ] SMS
  - [ ] No contact

- **Best Time to Contact** - select (multiple):
  - Morning (9am-12pm)
  - Afternoon (12pm-5pm)
  - Evening (5pm-8pm)

- **Subscribe to Newsletter** - checkbox
- **Agree to Terms** - checkbox (required)

### 3. Form Controls to Include

```html
<!-- Text Inputs -->
<input type="text">
<input type="email">
<input type="tel">
<input type="number">

<!-- Selection -->
<select>
<select multiple>
<option>
<optgroup>
<datalist>

<!-- Choices -->
<input type="radio">
<input type="checkbox">

<!-- Range -->
<input type="range">

<!-- Text Area -->
<textarea>

<!-- Buttons -->
<button type="submit">
<button type="reset">
```

### 4. Required Attributes

Use these HTML5 form attributes:
- `required` - for mandatory fields
- `placeholder` - for input hints
- `pattern` - for phone number format
- `minlength` / `maxlength` - for text limits
- `min` / `max` - for range inputs
- `disabled` - for conditional fields
- `readonly` - for display-only fields
- `autofocus` - on first field
- `autocomplete` - appropriate values

### 5. Accessibility Requirements
- Every input must have a `<label>` with `for` attribute
- Use `aria-describedby` for helper text
- Group related radio/checkbox with `fieldset`
- Include `aria-required="true"` for required fields

## Sample Code Structure

```html
<form action="#" method="POST">
    <fieldset>
        <legend>Personal Information</legend>

        <div class="form-group">
            <label for="fullname">Full Name *</label>
            <input type="text" id="fullname" name="fullname"
                   required minlength="2" maxlength="100"
                   placeholder="Enter your full name"
                   aria-required="true">
        </div>

        <div class="form-group">
            <label for="age-range">Age Range</label>
            <select id="age-range" name="age-range">
                <option value="">-- Select --</option>
                <option value="under-18">Under 18</option>
                <option value="18-24">18-24</option>
                <!-- more options -->
            </select>
        </div>

        <div class="form-group">
            <span id="gender-label">Gender</span>
            <div role="radiogroup" aria-labelledby="gender-label">
                <label>
                    <input type="radio" name="gender" value="male"> Male
                </label>
                <label>
                    <input type="radio" name="gender" value="female"> Female
                </label>
                <label>
                    <input type="radio" name="gender" value="other"> Prefer not to say
                </label>
            </div>
        </div>
    </fieldset>

    <fieldset>
        <legend>Products Used (Select all that apply)</legend>
        <label>
            <input type="checkbox" name="products" value="web-app">
            Product A - Web Application
        </label>
        <!-- more checkboxes -->
    </fieldset>

    <fieldset>
        <legend>Satisfaction Rating</legend>
        <div class="form-group">
            <label for="quality">Product Quality: <output id="quality-output">5</output>/10</label>
            <input type="range" id="quality" name="quality"
                   min="1" max="10" value="5"
                   oninput="document.getElementById('quality-output').value = this.value">
        </div>
    </fieldset>

    <fieldset>
        <legend>Feedback</legend>
        <div class="form-group">
            <label for="comments">Additional Comments</label>
            <textarea id="comments" name="comments"
                      rows="6" maxlength="1000"
                      placeholder="Share your thoughts..."></textarea>
            <small>Maximum 1000 characters</small>
        </div>
    </fieldset>

    <div class="form-actions">
        <button type="submit">Submit Survey</button>
        <button type="reset">Clear Form</button>
    </div>
</form>
```

## Bonus Challenges

1. **Conditional Fields**: Show/hide fields based on selections
2. **Character Counter**: Display remaining characters for textareas
3. **Multi-Select with Optgroup**: Group select options by category
4. **Datalist**: Add autocomplete suggestions for text inputs
5. **Output Element**: Display calculated values (e.g., average rating)

## Skills Tested
- Form structure (`form`, `fieldset`, `legend`)
- Input types (text, email, tel, number, range)
- Selection elements (`select`, `option`, `optgroup`, `datalist`)
- Choice inputs (radio, checkbox)
- Textarea
- Form validation attributes
- Accessibility (labels, ARIA)

## Validation
Your HTML should pass the W3C Validator without errors.
