# Exercise: JavaScript - Task Manager

## Objective
Build an interactive task manager application using vanilla JavaScript with DOM manipulation.

## Requirements

### Required Files
| File | Description |
|------|-------------|
| `task-manager.html` | HTML structure |
| `task-manager.js` | JavaScript logic |
| `styles.css` | Styling (optional) |

### Functional Requirements
1. Add new tasks with a title and optional description
2. Mark tasks as complete/incomplete (toggle)
3. Delete tasks
4. Filter tasks (All / Active / Completed)
5. Show task count
6. Persist tasks in localStorage

### Technical Requirements
- [ ] Use `const` and `let` (no `var`)
- [ ] Use arrow functions where appropriate
- [ ] Use template literals for HTML generation
- [ ] Use array methods (`map`, `filter`, `forEach`)
- [ ] Use event delegation for dynamic elements
- [ ] Handle edge cases (empty input, etc.)
- [ ] Use strict equality (`===`)

## Application Structure
```javascript
// Task object structure
{
    id: 1,
    title: "Learn JavaScript",
    description: "Complete the task manager exercise",
    completed: false,
    createdAt: "2025-01-01T10:00:00"
}
```

## Required Functions
```javascript
// Core functions to implement
addTask(title, description)    // Add new task
deleteTask(id)                 // Remove task by ID
toggleTask(id)                 // Toggle complete status
filterTasks(filter)            // Filter: 'all', 'active', 'completed'
saveTasks()                    // Save to localStorage
loadTasks()                    // Load from localStorage
renderTasks()                  // Update DOM
```

## Sample HTML Structure
```html
<div id="app">
    <h1>Task Manager</h1>
    <form id="task-form">
        <input type="text" id="task-input" placeholder="Add a task...">
        <button type="submit">Add</button>
    </form>
    <div id="filters">
        <button data-filter="all">All</button>
        <button data-filter="active">Active</button>
        <button data-filter="completed">Completed</button>
    </div>
    <ul id="task-list"></ul>
    <p id="task-count"></p>
</div>
```

## Evaluation Criteria
| Criteria | Points |
|----------|--------|
| Add/Delete tasks works | 20 |
| Toggle complete works | 15 |
| Filtering works | 15 |
| localStorage persistence | 15 |
| Modern JS practices (let/const, arrows) | 15 |
| Code organization & readability | 20 |
| **Total** | **100** |

## Submission Checklist
- [ ] Files named exactly as specified
- [ ] Placed in `06-javascript/` folder
- [ ] Application works in browser
- [ ] No console errors
- [ ] Tasks persist after page refresh
- [ ] Committed and pushed to repository
