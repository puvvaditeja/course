# jQuery Effects

## Overview

jQuery provides a rich set of methods for creating animations and visual effects. These effects make your web pages more interactive and engaging by adding smooth transitions and animations to elements.

---

## Basic Show/Hide Effects

### `.show()` and `.hide()`

Show or hide elements with optional animation:

```javascript
// Instant show/hide
$('#box').hide();
$('#box').show();

// With duration (milliseconds)
$('#box').hide(1000);  // Hide over 1 second
$('#box').show(500);   // Show over 0.5 seconds

// With duration names
$('#box').hide('slow');  // Predefined: 600ms
$('#box').show('fast');  // Predefined: 200ms

// With callback function
$('#box').hide(1000, function() {
    console.log('Hide complete!');
    alert('Element is now hidden');
});

// With easing (requires jQuery UI or custom easing)
$('#box').show(1000, 'linear');
$('#box').hide(1000, 'swing'); // Default easing
```

**How it works:**
- `.hide()` animates width, height, and opacity to 0, then sets `display: none`
- `.show()` sets `display` to original value, then animates dimensions and opacity

### `.toggle()`

Toggle between show and hide:

```javascript
// Toggle visibility
$('#box').toggle();

// Toggle with animation
$('#box').toggle(1000);

// Toggle with callback
$('#box').toggle('slow', function() {
    console.log('Toggle complete');
});

// Practical example: toggle menu
$('#menu-button').click(function() {
    $('#menu').toggle('fast');
});
```

---

## Fading Effects

### `.fadeIn()` and `.fadeOut()`

Fade elements in or out by animating opacity:

```javascript
// Fade in
$('#message').fadeIn();

// Fade out
$('#message').fadeOut();

// With duration
$('#message').fadeIn(2000);
$('#message').fadeOut('slow');

// With callback
$('#message').fadeOut(1000, function() {
    console.log('Fade complete');
    $(this).remove(); // Remove after fading out
});

// Practical example: notification
function showNotification(message) {
    $('#notification')
        .text(message)
        .fadeIn(300)
        .delay(3000)
        .fadeOut(300);
}
```

### `.fadeToggle()`

Toggle between fadeIn and fadeOut:

```javascript
// Toggle fade
$('#box').fadeToggle();

// With duration
$('#box').fadeToggle(1000);

// With callback
$('#box').fadeToggle('fast', function() {
    if ($(this).is(':visible')) {
        console.log('Faded in');
    } else {
        console.log('Faded out');
    }
});
```

### `.fadeTo()`

Fade to a specific opacity:

```javascript
// Fade to 50% opacity
$('#box').fadeTo(1000, 0.5);

// Fade to full opacity
$('#box').fadeTo('fast', 1);

// Fade to transparent (but element still takes space)
$('#box').fadeTo(500, 0);

// With callback
$('#box').fadeTo(1000, 0.3, function() {
    console.log('Faded to 30% opacity');
});

// Practical example: hover effect
$('.image-card')
    .mouseenter(function() {
        $(this).fadeTo(200, 0.7);
    })
    .mouseleave(function() {
        $(this).fadeTo(200, 1);
    });
```

**Difference from fadeOut():**
- `.fadeOut()` ends with `display: none`
- `.fadeTo(0)` ends with `opacity: 0` but element still takes space

---

## Sliding Effects

### `.slideDown()` and `.slideUp()`

Slide elements down or up by animating height:

```javascript
// Slide down (reveal)
$('#panel').slideDown();

// Slide up (hide)
$('#panel').slideUp();

// With duration
$('#panel').slideDown(1000);
$('#panel').slideUp('slow');

// With callback
$('#panel').slideDown(500, function() {
    console.log('Panel revealed');
});

// Practical example: accordion
$('.accordion-header').click(function() {
    $(this).next('.accordion-content').slideDown();
});
```

### `.slideToggle()`

Toggle between slideDown and slideUp:

```javascript
// Toggle slide
$('#panel').slideToggle();

// With duration
$('#panel').slideToggle(800);

// With callback
$('#panel').slideToggle('fast', function() {
    console.log('Slide toggle complete');
});

// Practical example: FAQ accordion
$('.faq-question').click(function() {
    $(this).next('.faq-answer').slideToggle(300);
});

// Advanced: close others when opening one
$('.faq-question').click(function() {
    const $answer = $(this).next('.faq-answer');
    $('.faq-answer').not($answer).slideUp(300);
    $answer.slideToggle(300);
});
```

---

## Custom Animations

### `.animate()`

Create custom animations on CSS properties:

```javascript
// Animate single property
$('#box').animate({
    width: '300px'
}, 1000);

// Animate multiple properties
$('#box').animate({
    width: '300px',
    height: '200px',
    opacity: 0.5,
    marginLeft: '50px'
}, 1000);

// With easing and callback
$('#box').animate({
    left: '250px',
    opacity: 1
}, {
    duration: 1000,
    easing: 'swing',
    complete: function() {
        console.log('Animation complete');
    }
});

// Relative values
$('#box').animate({
    left: '+=50px',   // Move 50px right
    top: '-=20px',    // Move 20px up
    width: '+=100px', // Increase width by 100px
    fontSize: '+=5px' // Increase font size by 5px
}, 500);

// Predefined values
$('#box').animate({
    height: 'toggle',
    width: 'toggle'
}, 1000);
```

**Properties that can be animated:**
- Numeric properties: width, height, top, left, margin, padding, font-size, etc.
- Color properties: Requires jQuery UI or Color plugin
- Cannot animate: display, font-family, font-weight (non-numeric)

### Animation Options

```javascript
$('#box').animate(
    { width: '300px' },
    {
        duration: 1000,        // Duration in ms or 'slow'/'fast'
        easing: 'swing',       // Easing function ('swing' or 'linear')
        queue: true,           // Add to queue or run immediately
        step: function(now, fx) {
            // Called for each animated property on each step
            console.log('Current value:', now);
        },
        progress: function(animation, progress, remainingMs) {
            // Called after each step
            console.log('Progress:', progress);
        },
        complete: function() {
            // Called when animation completes
            console.log('Done!');
        },
        start: function() {
            // Called when animation starts
            console.log('Starting...');
        },
        done: function() {
            // Called when animation completes (Promise-like)
            console.log('Animation finished');
        },
        fail: function() {
            // Called when animation is stopped
            console.log('Animation stopped');
        },
        always: function() {
            // Called when animation completes or is stopped
            console.log('Animation ended');
        }
    }
);
```

### Practical Animation Examples

```javascript
// Bouncing effect
function bounce(element) {
    $(element)
        .animate({ top: '-=20px' }, 200)
        .animate({ top: '+=20px' }, 200)
        .animate({ top: '-=10px' }, 100)
        .animate({ top: '+=10px' }, 100);
}

// Pulsing effect
function pulse(element) {
    $(element)
        .animate({ opacity: 0.5 }, 500)
        .animate({ opacity: 1 }, 500);
}

// Slide and fade
$('#box')
    .css({ left: '-300px', opacity: 0 })
    .animate({
        left: '0px',
        opacity: 1
    }, 1000);

// Grow and shrink
$('#box')
    .animate({
        width: '+=50px',
        height: '+=50px'
    }, 500)
    .animate({
        width: '-=50px',
        height: '-=50px'
    }, 500);

// Color animation (requires jQuery UI)
$('#box').animate({
    backgroundColor: '#ff0000',
    color: '#ffffff'
}, 1000);
```

---

## Animation Queue

### Understanding the Queue

Animations are queued by default and run sequentially:

```javascript
// These animations run one after another
$('#box')
    .fadeOut(1000)
    .fadeIn(1000)
    .slideUp(1000)
    .slideDown(1000);
```

### `.delay()`

Add delay between animations:

```javascript
// Delay between animations
$('#box')
    .fadeOut(500)
    .delay(1000)        // Wait 1 second
    .fadeIn(500);

// Multiple delays
$('#box')
    .slideUp(300)
    .delay(500)
    .slideDown(300)
    .delay(500)
    .fadeOut(300);

// Practical example: sequential notifications
$('#message1').fadeIn(300).delay(2000).fadeOut(300);
$('#message2').delay(3000).fadeIn(300).delay(2000).fadeOut(300);
```

### `.queue()` and `.dequeue()`

Manually control the animation queue:

```javascript
// Add custom function to queue
$('#box')
    .fadeOut(1000)
    .queue(function(next) {
        console.log('Custom function');
        $(this).addClass('done');
        next(); // Continue queue
    })
    .fadeIn(1000);

// View queue
const queue = $('#box').queue();
console.log('Queue length:', queue.length);

// Clear queue
$('#box').queue([]);

// Dequeue - run next in queue
$('#box').dequeue();
```

### `.clearQueue()`

Clear queued animations:

```javascript
// Clear queue but finish current animation
$('#box').clearQueue();

// Stop and clear queue
$('#box').stop(true, false).clearQueue();
```

---

## Controlling Animations

### `.stop()`

Stop currently running animation:

```javascript
// Stop current animation
$('#box').stop();

// Stop and clear queue
$('#box').stop(true);

// Stop, clear queue, and jump to end
$('#box').stop(true, true);

// Parameters: .stop(clearQueue, jumpToEnd)
$('#box').stop(false, false); // Stop current animation
$('#box').stop(true, false);  // Stop all animations
$('#box').stop(false, true);  // Stop and complete current
$('#box').stop(true, true);   // Stop all and complete current

// Practical example: prevent animation buildup
$('#box').stop(true, true).fadeToggle(500);

// Hover with stop
$('#box')
    .mouseenter(function() {
        $(this).stop(true, true).fadeIn(300);
    })
    .mouseleave(function() {
        $(this).stop(true, true).fadeOut(300);
    });
```

### `.finish()`

Immediately complete all queued animations:

```javascript
// Finish all animations immediately
$('#box').finish();

// Different from .stop(true, true)
// .stop() completes current animation
// .finish() completes ALL queued animations

// Example
$('#box')
    .fadeOut(1000)
    .delay(1000)
    .fadeIn(1000)
    .finish(); // Instantly completes all three
```

### Animation State

```javascript
// Check if element is being animated
if ($('#box').is(':animated')) {
    console.log('Currently animating');
}

// Wait for animation to complete
$('#box').fadeOut(1000, function() {
    console.log('Animation complete');
});

// Using promises (jQuery 1.8+)
$('#box').fadeOut(1000).promise().done(function() {
    console.log('Animation complete');
});
```

---

## Effect Methods Comparison

| Method | Effect | Duration | Animates |
|--------|--------|----------|----------|
| `.show()` | Reveal element | Optional | Width, height, opacity |
| `.hide()` | Conceal element | Optional | Width, height, opacity |
| `.toggle()` | Toggle visibility | Optional | Width, height, opacity |
| `.fadeIn()` | Fade element in | Optional | Opacity |
| `.fadeOut()` | Fade element out | Optional | Opacity |
| `.fadeToggle()` | Toggle fade | Optional | Opacity |
| `.fadeTo()` | Fade to opacity | Required | Opacity |
| `.slideDown()` | Slide down | Optional | Height |
| `.slideUp()` | Slide up | Optional | Height |
| `.slideToggle()` | Toggle slide | Optional | Height |
| `.animate()` | Custom animation | Required | Numeric CSS properties |

---

## Common Patterns

### Hover Effects

```javascript
// Fade on hover
$('.image')
    .mouseenter(function() {
        $(this).stop(true, true).fadeTo(300, 0.7);
    })
    .mouseleave(function() {
        $(this).stop(true, true).fadeTo(300, 1);
    });

// Slide panel on hover
$('.menu')
    .mouseenter(function() {
        $(this).find('.submenu').stop(true, true).slideDown(200);
    })
    .mouseleave(function() {
        $(this).find('.submenu').stop(true, true).slideUp(200);
    });
```

### Modal/Dialog

```javascript
// Show modal
function showModal() {
    $('#overlay').fadeIn(300);
    $('#modal')
        .css({ top: '-300px', opacity: 0 })
        .show()
        .animate({
            top: '100px',
            opacity: 1
        }, 500);
}

// Hide modal
function hideModal() {
    $('#modal').animate({
        top: '-300px',
        opacity: 0
    }, 500, function() {
        $(this).hide();
        $('#overlay').fadeOut(300);
    });
}

// Close on overlay click
$('#overlay').click(hideModal);
```

### Notification System

```javascript
function showNotification(message, type, duration) {
    const $notification = $('<div>')
        .addClass('notification ' + type)
        .text(message)
        .css({ opacity: 0, top: '-50px' })
        .appendTo('body');

    $notification
        .animate({ opacity: 1, top: '20px' }, 300)
        .delay(duration || 3000)
        .animate({ opacity: 0, top: '-50px' }, 300, function() {
            $(this).remove();
        });
}

// Usage
showNotification('Success!', 'success', 3000);
showNotification('Error occurred', 'error', 5000);
```

### Loading Spinner

```javascript
// Show loading
function showLoading() {
    $('#loading-overlay').fadeIn(300);
    $('#spinner').show().animate({ rotate: '+=360' }, 1000, 'linear', function() {
        // Infinite rotation
        if ($('#loading-overlay').is(':visible')) {
            $(this).css({ rotate: 0 });
            showLoading();
        }
    });
}

// Hide loading
function hideLoading() {
    $('#loading-overlay').fadeOut(300);
    $('#spinner').stop(true, true);
}
```

### Slide Show

```javascript
function slideShow() {
    $('.slide')
        .first()
        .fadeIn(1000)
        .delay(3000)
        .fadeOut(1000, function() {
            $(this).appendTo('.slideshow');
            slideShow(); // Recursive call
        });
}

// Start slideshow
slideShow();
```

### Progressive Content Reveal

```javascript
// Reveal elements one by one
function revealElements(selector, delay) {
    $(selector).each(function(index) {
        $(this)
            .delay(index * delay)
            .fadeIn(500);
    });
}

// Usage
revealElements('.feature-card', 200);
```

### Smooth Scroll

```javascript
// Smooth scroll to element
function smoothScroll(target) {
    $('html, body').animate({
        scrollTop: $(target).offset().top
    }, 1000);
}

// Click handler
$('a[href^="#"]').click(function(e) {
    e.preventDefault();
    smoothScroll($(this).attr('href'));
});
```

---

## Best Practices

### 1. Always Use `.stop()` with Hover Events

```javascript
// Bad - animations queue up
$('#box').hover(
    function() { $(this).fadeIn(); },
    function() { $(this).fadeOut(); }
);

// Good - prevents queue buildup
$('#box').hover(
    function() { $(this).stop(true, true).fadeIn(); },
    function() { $(this).stop(true, true).fadeOut(); }
);
```

### 2. Cache jQuery Selectors

```javascript
// Bad - selector runs multiple times
$('#box').fadeOut(500);
$('#box').delay(1000);
$('#box').fadeIn(500);

// Good - cache selector
const $box = $('#box');
$box.fadeOut(500).delay(1000).fadeIn(500);

// Better - chain methods
$('#box').fadeOut(500).delay(1000).fadeIn(500);
```

### 3. Use Callbacks for Sequential Actions

```javascript
// Bad - may not work as expected
$('#box1').fadeOut(500);
$('#box2').fadeIn(500);

// Good - ensure order
$('#box1').fadeOut(500, function() {
    $('#box2').fadeIn(500);
});
```

### 4. Consider Performance

```javascript
// Bad - animating many elements individually
$('.item').each(function() {
    $(this).fadeIn(500);
});

// Better - use delays for staggered effect
$('.item').each(function(index) {
    $(this).delay(index * 100).fadeIn(500);
});

// Best - limit number of simultaneous animations
$('.item').slice(0, 10).each(function(index) {
    $(this).delay(index * 100).fadeIn(500);
});
```

### 5. Provide User Control

```javascript
// Allow users to skip animations
$('#skip-intro').click(function() {
    $('.animated').finish();
});

// Respect user preferences
if (window.matchMedia('(prefers-reduced-motion: reduce)').matches) {
    // Disable animations or use instant effects
    $.fx.off = true; // Disable all animations globally
}
```

### 6. Clean Up After Animations

```javascript
// Remove elements after animation
$('#notification').fadeOut(500, function() {
    $(this).remove();
});

// Reset states after animation
$('#box').animate({ left: '200px' }, 500, function() {
    $(this).css('left', '');
    $(this).removeClass('animating');
});
```

---

## Global Animation Settings

```javascript
// Disable all animations
$.fx.off = true;

// Change default duration
$.fx.speeds.slow = 1000;  // Default: 600
$.fx.speeds.fast = 100;   // Default: 200

// Change default easing
$.easing.def = 'linear';  // Default: 'swing'

// Custom duration
$.fx.speeds.custom = 800;
$('#box').fadeIn('custom');
```

---

## Summary

| Category | Methods | Use Case |
|----------|---------|----------|
| Show/Hide | `.show()`, `.hide()`, `.toggle()` | Basic visibility changes |
| Fading | `.fadeIn()`, `.fadeOut()`, `.fadeToggle()`, `.fadeTo()` | Smooth opacity transitions |
| Sliding | `.slideDown()`, `.slideUp()`, `.slideToggle()` | Vertical expand/collapse |
| Custom | `.animate()` | Any numeric CSS property |
| Control | `.stop()`, `.finish()`, `.delay()` | Animation flow control |
| Queue | `.queue()`, `.dequeue()`, `.clearQueue()` | Advanced queue management |

## Next Topic

Continue to [Basic AJAX with jQuery](./05-basic-ajax.md) to learn how to make asynchronous HTTP requests.
