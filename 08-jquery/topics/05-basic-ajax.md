# Basic AJAX with jQuery

## Overview

AJAX (Asynchronous JavaScript and XML) allows web pages to update content dynamically without reloading the entire page. jQuery provides simple methods for making AJAX requests that work consistently across all browsers.

---

## What is AJAX?

### Traditional Web Request

```
User clicks link
    ↓
Browser sends request to server
    ↓
Server processes request
    ↓
Server sends entire new page
    ↓
Browser loads entire new page
```

**Result:** Page refresh, loss of state, poor user experience

### AJAX Request

```
User interacts with page
    ↓
JavaScript sends background request
    ↓
Server processes request
    ↓
Server sends only data (JSON/XML/HTML)
    ↓
JavaScript updates part of page
```

**Result:** No page refresh, smooth experience, faster performance

### Benefits of AJAX

- **Better User Experience**: No page reloads
- **Faster Performance**: Only necessary data is transferred
- **Reduced Server Load**: Smaller requests
- **Interactive Applications**: Real-time updates possible
- **Bandwidth Efficient**: Transfer data, not entire pages

---

## jQuery AJAX Methods

jQuery provides several methods for AJAX requests, from simple to complex.

### Method Hierarchy

```
Low-level (most control):
    $.ajax() ─────────────────┐
                              │
Mid-level (convenience):      │
    $.get()                   │
    $.post()                  │
    $.getJSON()               │
    $.getScript()             │
                              │
High-level (specialized):     │
    .load()                   │
                              │
All built on: $.ajax() ───────┘
```

---

## `.load()` - Load HTML

The simplest AJAX method - loads HTML from server into an element:

```javascript
// Basic syntax
$('#content').load('page.html');

// Load specific element from page
$('#content').load('page.html #main-content');

// With callback
$('#content').load('page.html', function(response, status, xhr) {
    if (status === 'success') {
        console.log('Content loaded');
    } else {
        console.log('Error:', xhr.status, xhr.statusText);
    }
});

// With data (converts to POST request)
$('#content').load('search.php', {
    query: 'javascript',
    limit: 10
});

// Practical example: dynamic content
$('.nav-link').click(function(e) {
    e.preventDefault();
    const page = $(this).attr('href');
    $('#main-content').load(page + ' #content', function() {
        console.log('Page loaded:', page);
    });
});
```

**Use Cases:**
- Loading HTML fragments
- Implementing single-page applications
- Dynamic content loading
- Updating page sections

---

## `$.get()` - GET Request

Make HTTP GET request:

```javascript
// Basic GET request
$.get('api/users.json', function(data) {
    console.log('Data:', data);
});

// With query parameters
$.get('api/users.json', { role: 'admin', active: true }, function(data) {
    console.log('Filtered users:', data);
});

// With complete callback
$.get('api/data.json', function(data) {
    console.log('Success:', data);
}).done(function() {
    console.log('Request completed');
}).fail(function(xhr, status, error) {
    console.log('Error:', error);
}).always(function() {
    console.log('Request finished');
});

// Specifying data type
$.get('api/users.json', { limit: 10 }, function(data) {
    $('#users').html(data);
}, 'html');

// Practical example: load user data
function loadUser(userId) {
    $.get('api/users/' + userId, function(user) {
        $('#user-name').text(user.name);
        $('#user-email').text(user.email);
        $('#user-role').text(user.role);
    });
}
```

---

## `$.post()` - POST Request

Make HTTP POST request:

```javascript
// Basic POST request
$.post('api/users', {
    name: 'John Doe',
    email: 'john@example.com'
}, function(response) {
    console.log('User created:', response);
});

// With error handling
$.post('api/users', {
    name: 'Jane Doe',
    email: 'jane@example.com'
}).done(function(response) {
    console.log('Success:', response);
}).fail(function(xhr) {
    console.log('Error:', xhr.responseText);
});

// Practical example: form submission
$('#user-form').submit(function(e) {
    e.preventDefault();

    const formData = {
        name: $('#name').val(),
        email: $('#email').val(),
        message: $('#message').val()
    };

    $.post('api/contact', formData, function(response) {
        $('#message-box').html('<p class="success">Message sent!</p>');
        $('#user-form')[0].reset();
    }).fail(function() {
        $('#message-box').html('<p class="error">Error sending message</p>');
    });
});

// Sending JSON
$.post('api/users', JSON.stringify({
    name: 'John',
    email: 'john@example.com'
}), function(response) {
    console.log('Response:', response);
}, 'json');
```

---

## `$.getJSON()` - GET JSON Data

Convenient method for fetching JSON data:

```javascript
// Basic usage
$.getJSON('api/users.json', function(users) {
    console.log('Users:', users);
    users.forEach(function(user) {
        console.log(user.name);
    });
});

// With query parameters
$.getJSON('api/users.json', { role: 'admin' }, function(users) {
    console.log('Admin users:', users);
});

// With error handling
$.getJSON('api/data.json')
    .done(function(data) {
        console.log('Data loaded:', data);
    })
    .fail(function(xhr, status, error) {
        console.log('Error loading data:', error);
    });

// Practical example: display users list
function loadUsers() {
    $.getJSON('api/users.json', function(users) {
        const $list = $('#users-list');
        $list.empty();

        users.forEach(function(user) {
            const $item = $('<li>')
                .text(user.name + ' - ' + user.email)
                .appendTo($list);
        });
    });
}

// Example: populate dropdown
$.getJSON('api/countries.json', function(countries) {
    const $select = $('#country');
    countries.forEach(function(country) {
        $('<option>')
            .val(country.code)
            .text(country.name)
            .appendTo($select);
    });
});
```

---

## `$.ajax()` - Complete Control

The most powerful and flexible AJAX method:

```javascript
// Basic syntax
$.ajax({
    url: 'api/users.json',
    method: 'GET',
    success: function(data) {
        console.log('Success:', data);
    },
    error: function(xhr, status, error) {
        console.log('Error:', error);
    }
});

// Full options example
$.ajax({
    url: 'api/users',
    method: 'POST',              // or 'GET', 'PUT', 'DELETE', etc.
    data: {                      // Data to send
        name: 'John',
        email: 'john@example.com'
    },
    dataType: 'json',            // Expected response type
    contentType: 'application/json', // Request content type
    timeout: 5000,               // Request timeout (ms)
    cache: false,                // Disable caching
    headers: {                   // Custom headers
        'Authorization': 'Bearer token123',
        'X-Custom-Header': 'value'
    },
    beforeSend: function(xhr) {
        // Called before request is sent
        console.log('Sending request...');
        $('#loading').show();
    },
    success: function(data, status, xhr) {
        // Called on successful response
        console.log('Success:', data);
    },
    error: function(xhr, status, error) {
        // Called on error
        console.log('Error:', status, error);
    },
    complete: function(xhr, status) {
        // Called when request completes (success or error)
        console.log('Request complete');
        $('#loading').hide();
    }
});

// Using promises (recommended)
$.ajax({
    url: 'api/users.json',
    method: 'GET'
})
.done(function(data) {
    console.log('Success:', data);
})
.fail(function(xhr, status, error) {
    console.log('Error:', error);
})
.always(function() {
    console.log('Complete');
});
```

### Common AJAX Options

| Option | Description | Example |
|--------|-------------|---------|
| `url` | Request URL | `'api/users.json'` |
| `method` | HTTP method | `'GET'`, `'POST'`, `'PUT'`, `'DELETE'` |
| `data` | Data to send | `{ name: 'John' }` |
| `dataType` | Expected response type | `'json'`, `'html'`, `'xml'`, `'text'` |
| `contentType` | Request content type | `'application/json'` |
| `timeout` | Timeout in milliseconds | `5000` |
| `cache` | Enable/disable caching | `true` / `false` |
| `headers` | Custom HTTP headers | `{ 'Authorization': 'Bearer token' }` |
| `async` | Asynchronous request | `true` / `false` (default: true) |
| `beforeSend` | Pre-request callback | `function(xhr) { }` |
| `success` | Success callback | `function(data) { }` |
| `error` | Error callback | `function(xhr, status, error) { }` |
| `complete` | Complete callback | `function(xhr, status) { }` |

---

## RESTful AJAX Operations

### CREATE (POST)

```javascript
function createUser(userData) {
    return $.ajax({
        url: 'api/users',
        method: 'POST',
        contentType: 'application/json',
        data: JSON.stringify(userData)
    });
}

// Usage
createUser({
    name: 'John Doe',
    email: 'john@example.com'
})
.done(function(response) {
    console.log('User created:', response);
})
.fail(function(xhr) {
    console.log('Error:', xhr.responseJSON);
});
```

### READ (GET)

```javascript
// Get all users
function getUsers() {
    return $.ajax({
        url: 'api/users',
        method: 'GET'
    });
}

// Get single user
function getUser(userId) {
    return $.ajax({
        url: 'api/users/' + userId,
        method: 'GET'
    });
}

// Usage
getUsers().done(function(users) {
    console.log('All users:', users);
});

getUser(123).done(function(user) {
    console.log('User:', user);
});
```

### UPDATE (PUT)

```javascript
function updateUser(userId, userData) {
    return $.ajax({
        url: 'api/users/' + userId,
        method: 'PUT',
        contentType: 'application/json',
        data: JSON.stringify(userData)
    });
}

// Usage
updateUser(123, {
    name: 'John Smith',
    email: 'john.smith@example.com'
})
.done(function(response) {
    console.log('User updated:', response);
});
```

### DELETE

```javascript
function deleteUser(userId) {
    return $.ajax({
        url: 'api/users/' + userId,
        method: 'DELETE'
    });
}

// Usage
deleteUser(123)
    .done(function() {
        console.log('User deleted');
    })
    .fail(function() {
        console.log('Error deleting user');
    });
```

---

## Handling Responses

### Success Callback Parameters

```javascript
$.ajax({
    url: 'api/data.json',
    success: function(data, textStatus, jqXHR) {
        console.log('Data:', data);              // Response data
        console.log('Status:', textStatus);       // "success"
        console.log('Status Code:', jqXHR.status); // 200
    }
});
```

### Error Callback Parameters

```javascript
$.ajax({
    url: 'api/data.json',
    error: function(jqXHR, textStatus, errorThrown) {
        console.log('Status Code:', jqXHR.status);     // 404, 500, etc.
        console.log('Status:', textStatus);            // "error", "timeout", etc.
        console.log('Error:', errorThrown);            // Error message
        console.log('Response:', jqXHR.responseText);  // Response body
    }
});
```

### Status Codes

```javascript
$.ajax({
    url: 'api/data.json',
    statusCode: {
        200: function(data) {
            console.log('OK:', data);
        },
        404: function() {
            console.log('Not found');
        },
        500: function() {
            console.log('Server error');
        }
    }
});
```

---

## Loading Indicators

### Basic Loading Indicator

```javascript
function loadData() {
    // Show loading
    $('#loading').show();

    $.ajax({
        url: 'api/data.json',
        success: function(data) {
            displayData(data);
        },
        error: function() {
            showError('Failed to load data');
        },
        complete: function() {
            // Hide loading (runs on success or error)
            $('#loading').hide();
        }
    });
}
```

### Global AJAX Event Handlers

```javascript
// Show loader on any AJAX request
$(document).ajaxStart(function() {
    $('#global-loader').show();
});

// Hide loader when all requests complete
$(document).ajaxStop(function() {
    $('#global-loader').hide();
});

// Handle errors globally
$(document).ajaxError(function(event, xhr, settings, error) {
    console.log('AJAX Error:', error);
    showNotification('An error occurred', 'error');
});

// Called before each AJAX request
$(document).ajaxSend(function(event, xhr, settings) {
    console.log('Sending request to:', settings.url);
});

// Called when any AJAX request completes successfully
$(document).ajaxSuccess(function(event, xhr, settings) {
    console.log('Request successful:', settings.url);
});
```

---

## Practical Examples

### Live Search

```javascript
$('#search-input').on('keyup', function() {
    const query = $(this).val();

    if (query.length < 3) {
        $('#search-results').empty();
        return;
    }

    $.ajax({
        url: 'api/search',
        data: { q: query },
        success: function(results) {
            const $resultsDiv = $('#search-results');
            $resultsDiv.empty();

            results.forEach(function(result) {
                $('<div>')
                    .addClass('search-result')
                    .text(result.title)
                    .click(function() {
                        window.location = result.url;
                    })
                    .appendTo($resultsDiv);
            });
        }
    });
});
```

### Form Submission with Validation

```javascript
$('#contact-form').submit(function(e) {
    e.preventDefault();

    // Clear previous errors
    $('.error').remove();

    // Get form data
    const formData = {
        name: $('#name').val(),
        email: $('#email').val(),
        message: $('#message').val()
    };

    // Client-side validation
    let isValid = true;
    if (!formData.name) {
        $('#name').after('<span class="error">Name is required</span>');
        isValid = false;
    }
    if (!formData.email) {
        $('#email').after('<span class="error">Email is required</span>');
        isValid = false;
    }

    if (!isValid) return;

    // Disable submit button
    const $submitBtn = $('#submit-btn');
    $submitBtn.prop('disabled', true).text('Sending...');

    // Submit form
    $.ajax({
        url: 'api/contact',
        method: 'POST',
        data: formData,
        success: function(response) {
            $('#contact-form').hide();
            $('#success-message')
                .text('Thank you! Your message has been sent.')
                .fadeIn();
        },
        error: function(xhr) {
            const error = xhr.responseJSON?.message || 'An error occurred';
            $('#form-errors')
                .text(error)
                .fadeIn();
        },
        complete: function() {
            $submitBtn.prop('disabled', false).text('Send');
        }
    });
});
```

### Auto-save Feature

```javascript
let autoSaveTimer;

$('#editor').on('keyup', function() {
    // Clear existing timer
    clearTimeout(autoSaveTimer);

    // Set new timer
    autoSaveTimer = setTimeout(function() {
        saveContent();
    }, 2000); // Save after 2 seconds of inactivity
});

function saveContent() {
    const content = $('#editor').val();

    $.ajax({
        url: 'api/save',
        method: 'POST',
        data: { content: content },
        success: function() {
            $('#save-status')
                .text('Saved')
                .fadeIn()
                .delay(2000)
                .fadeOut();
        },
        error: function() {
            $('#save-status')
                .text('Error saving')
                .css('color', 'red')
                .fadeIn();
        }
    });
}
```

### Pagination

```javascript
let currentPage = 1;

function loadPage(page) {
    $.ajax({
        url: 'api/users',
        data: {
            page: page,
            limit: 10
        },
        beforeSend: function() {
            $('#users-list').html('<p>Loading...</p>');
        },
        success: function(response) {
            displayUsers(response.users);
            updatePagination(response.totalPages, page);
            currentPage = page;
        }
    });
}

function displayUsers(users) {
    const $list = $('#users-list');
    $list.empty();

    users.forEach(function(user) {
        $('<div>')
            .addClass('user-item')
            .html('<strong>' + user.name + '</strong><br>' + user.email)
            .appendTo($list);
    });
}

function updatePagination(totalPages, currentPage) {
    const $pagination = $('#pagination');
    $pagination.empty();

    for (let i = 1; i <= totalPages; i++) {
        $('<button>')
            .text(i)
            .toggleClass('active', i === currentPage)
            .click(function() {
                loadPage(i);
            })
            .appendTo($pagination);
    }
}

// Load first page on page load
loadPage(1);
```

### Infinite Scroll

```javascript
let page = 1;
let loading = false;

$(window).scroll(function() {
    // Check if near bottom
    if ($(window).scrollTop() + $(window).height() > $(document).height() - 100) {
        if (!loading) {
            loadMoreItems();
        }
    }
});

function loadMoreItems() {
    loading = true;
    $('#loading-indicator').show();

    $.ajax({
        url: 'api/items',
        data: {
            page: page,
            limit: 20
        },
        success: function(items) {
            items.forEach(function(item) {
                $('<div>')
                    .addClass('item')
                    .text(item.title)
                    .appendTo('#items-container');
            });

            page++;
            loading = false;
            $('#loading-indicator').hide();
        },
        error: function() {
            loading = false;
            $('#loading-indicator').hide();
        }
    });
}
```

---

## Error Handling

### Comprehensive Error Handling

```javascript
$.ajax({
    url: 'api/data.json',
    method: 'GET',
    timeout: 5000,
    success: function(data) {
        try {
            processData(data);
        } catch (e) {
            console.error('Error processing data:', e);
            showError('Error processing data');
        }
    },
    error: function(xhr, status, error) {
        if (status === 'timeout') {
            showError('Request timed out. Please try again.');
        } else if (status === 'abort') {
            showError('Request was cancelled.');
        } else if (xhr.status === 404) {
            showError('Resource not found.');
        } else if (xhr.status === 500) {
            showError('Server error. Please try again later.');
        } else {
            showError('An error occurred: ' + error);
        }
    },
    complete: function() {
        // Cleanup code
    }
});
```

### Retry Logic

```javascript
function ajaxWithRetry(options, maxRetries) {
    let retries = 0;

    function makeRequest() {
        $.ajax(options)
            .fail(function(xhr) {
                retries++;
                if (retries < maxRetries) {
                    console.log('Retrying... (' + retries + '/' + maxRetries + ')');
                    setTimeout(makeRequest, 1000 * retries); // Exponential backoff
                } else {
                    console.log('Max retries reached');
                    if (options.finalError) {
                        options.finalError(xhr);
                    }
                }
            });
    }

    makeRequest();
}

// Usage
ajaxWithRetry({
    url: 'api/data.json',
    success: function(data) {
        console.log('Success:', data);
    },
    finalError: function(xhr) {
        showError('Failed after multiple attempts');
    }
}, 3);
```

---

## Best Practices

### 1. Use Promise-based Syntax

```javascript
// Good
$.ajax({ url: 'api/users' })
    .done(function(data) { /* handle success */ })
    .fail(function(xhr) { /* handle error */ })
    .always(function() { /* cleanup */ });

// Better for chaining
$.ajax({ url: 'api/users' })
    .then(function(data) {
        return processData(data);
    })
    .then(function(processed) {
        return saveData(processed);
    })
    .catch(function(error) {
        handleError(error);
    });
```

### 2. Avoid Caching Issues

```javascript
$.ajax({
    url: 'api/data.json',
    cache: false,  // Adds timestamp to URL
    // or manually:
    // url: 'api/data.json?t=' + Date.now()
});
```

### 3. Use Global AJAX Settings

```javascript
// Set defaults for all AJAX requests
$.ajaxSetup({
    timeout: 5000,
    cache: false,
    headers: {
        'X-Requested-With': 'XMLHttpRequest'
    }
});
```

### 4. Abort Previous Requests

```javascript
let currentRequest = null;

$('#search').on('keyup', function() {
    // Abort previous request
    if (currentRequest) {
        currentRequest.abort();
    }

    currentRequest = $.ajax({
        url: 'api/search',
        data: { q: $(this).val() },
        success: function(results) {
            displayResults(results);
        },
        complete: function() {
            currentRequest = null;
        }
    });
});
```

### 5. Sanitize User Input

```javascript
// Bad - potential XSS
$('#results').html(userInput);

// Good - escape HTML
$('#results').text(userInput);

// Or use a sanitization library
$('#results').html(DOMPurify.sanitize(userInput));
```

---

## Summary

| Method | HTTP Method | Use Case | Returns |
|--------|-------------|----------|---------|
| `.load()` | GET/POST | Load HTML into element | jQuery object |
| `$.get()` | GET | Retrieve data | jqXHR |
| `$.post()` | POST | Submit data | jqXHR |
| `$.getJSON()` | GET | Retrieve JSON data | jqXHR |
| `$.ajax()` | Any | Full control over request | jqXHR |

**Key Concepts:**
- AJAX enables asynchronous communication without page reload
- jQuery simplifies cross-browser AJAX requests
- Use `.load()` for simple HTML loading
- Use `$.get()` and `$.post()` for common requests
- Use `$.ajax()` for complete control
- Always handle errors and provide user feedback
- Use promises for cleaner async code
- Implement loading indicators for better UX

---

**Module Complete!** You now have a comprehensive understanding of jQuery, from basic selectors to advanced AJAX operations. Practice these concepts by building interactive web applications.
