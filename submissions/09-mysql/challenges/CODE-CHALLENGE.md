# Code Challenges: MySQL

SQL query challenges to practice database skills. Write each solution in a separate `.sql` file.

---

## Setup

Use this schema for all challenges:

```sql
CREATE TABLE employees (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    department VARCHAR(50),
    salary DECIMAL(10,2),
    hire_date DATE,
    manager_id INT
);

CREATE TABLE projects (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    budget DECIMAL(12,2),
    start_date DATE,
    end_date DATE
);

CREATE TABLE assignments (
    employee_id INT,
    project_id INT,
    role VARCHAR(50),
    hours_allocated INT,
    PRIMARY KEY (employee_id, project_id)
);
```

---

## Challenge 1: Department Stats
**File:** `department-stats.sql`

Write a query that returns each department with:
- Total employees
- Average salary (rounded to 2 decimals)
- Highest salary
- Only departments with more than 2 employees

**Expected columns:** `department`, `employee_count`, `avg_salary`, `max_salary`

---

## Challenge 2: Employee Hierarchy
**File:** `employee-hierarchy.sql`

Write a query to show employees with their manager's name.

**Expected columns:** `employee_name`, `manager_name`

**Hint:** Use self-join. Show `NULL` if no manager.

---

## Challenge 3: Project Workload
**File:** `project-workload.sql`

Find projects with total allocated hours and number of team members.
Only include projects with budget > 50000.
Order by total hours descending.

**Expected columns:** `project_name`, `budget`, `team_size`, `total_hours`

---

## Challenge 4: Top Earners
**File:** `top-earners.sql`

Write a query to find the top 3 highest-paid employees per department using window functions.

**Expected columns:** `department`, `name`, `salary`, `rank_in_dept`

**Hint:** Use `ROW_NUMBER()` or `DENSE_RANK()`

---

## Challenge 5: Unassigned Resources
**File:** `unassigned-resources.sql`

Find all employees who are NOT assigned to any project.

**Expected columns:** `id`, `name`, `department`

**Hint:** Use `LEFT JOIN` or `NOT EXISTS`

---

## Submission
Place all challenge files in: `09-mysql/challenges/`

| Challenge | File | Status |
|-----------|------|--------|
| Department Stats | `department-stats.sql` | ⬜ |
| Employee Hierarchy | `employee-hierarchy.sql` | ⬜ |
| Project Workload | `project-workload.sql` | ⬜ |
| Top Earners | `top-earners.sql` | ⬜ |
| Unassigned Resources | `unassigned-resources.sql` | ⬜ |
