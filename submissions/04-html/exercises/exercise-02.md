# Exercise: Build an Employee Directory Table

## Objective
Create a well-structured HTML table displaying employee information with proper semantic markup, accessibility features, and styling hooks.

## Requirements

Build an HTML page (`employee-table.html`) that includes:

### 1. Page Structure
- Proper DOCTYPE and meta tags
- Page title: "Employee Directory"
- A header with company name and department filter

### 2. Employee Table

Create a table with the following structure:

#### Table Caption
- "Company Employee Directory - 2024"

#### Table Header (`<thead>`)
Columns:
| ID | Photo | Name | Department | Position | Email | Phone | Start Date | Status |

#### Table Body (`<tbody>`)
Add at least 8 employees with varied data:
- Mix of departments (Engineering, Marketing, HR, Sales, Finance)
- Mix of positions (Manager, Senior, Junior, Intern)
- Mix of statuses (Active, On Leave, Remote)
- Use placeholder images for photos

#### Table Footer (`<tfoot>`)
- Total employee count
- Department summary

### 3. Required Table Features

```html
<!-- Use these table elements -->
<table>
<caption>
<thead>
<tbody>
<tfoot>
<tr>
<th> (with scope attribute)
<td>
<colgroup> and <col>
```

### 4. Accessibility Requirements
- Use `scope="col"` for column headers
- Use `scope="row"` for row headers (if applicable)
- Add `aria-label` to the table
- Include proper `alt` text for employee photos

### 5. Advanced Features
- Use `colspan` to span the footer across columns
- Use `rowspan` to group employees by department (bonus)
- Add `data-*` attributes for department and status

## Sample Data Structure

```html
<tr data-department="engineering" data-status="active">
    <td>EMP001</td>
    <td><img src="..." alt="John Doe"></td>
    <td>John Doe</td>
    <td>Engineering</td>
    <td>Senior Developer</td>
    <td><a href="mailto:john@company.com">john@company.com</a></td>
    <td><a href="tel:+1234567890">123-456-7890</a></td>
    <td><time datetime="2022-03-15">Mar 15, 2022</time></td>
    <td><span class="status active">Active</span></td>
</tr>
```

## Expected Output Structure

```
+------------------------------------------------------------------------+
|                  Company Employee Directory - 2024                      |
+------+-------+------------+------------+----------+-------+------+-----+
|  ID  | Photo |    Name    | Department | Position | Email | Phone| Date|
+------+-------+------------+------------+----------+-------+------+-----+
| E001 |  [img]| John Doe   | Engineering| Sr. Dev  | ...   | ...  | ... |
| E002 |  [img]| Jane Smith | Marketing  | Manager  | ...   | ...  | ... |
| ...  |  ...  | ...        | ...        | ...      | ...   | ...  | ... |
+------+-------+------------+------------+----------+-------+------+-----+
|                    Total Employees: 8 | Engineering: 3 | Marketing: 2   |
+------------------------------------------------------------------------+
```

## Bonus Challenges

1. **Sortable Headers**: Add `data-sort` attributes to headers
2. **Row Grouping**: Group rows by department using `rowspan`
3. **Responsive Table**: Wrap in a scrollable container
4. **Status Colors**: Use different classes for status (active/leave/remote)

## Skills Tested
- Table structure (`table`, `thead`, `tbody`, `tfoot`)
- Table cells (`th`, `td`) with attributes
- Accessibility (`scope`, `aria-label`)
- Semantic elements (`time`, `a` with mailto/tel)
- Data attributes

## Validation
Your HTML should pass the W3C Validator without errors.
