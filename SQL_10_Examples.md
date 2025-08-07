# 10 SQL Query Examples

## Sample Database Setup

First, let's create sample tables for our examples:

```sql
-- Create sample tables
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(100),
    hire_date DATE,
    salary DECIMAL(10,2),
    department_id INT
);

CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(100),
    location VARCHAR(100)
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    total_amount DECIMAL(10,2),
    status VARCHAR(20)
);

-- Insert sample data
INSERT INTO departments VALUES 
(1, 'Engineering', 'Building A'),
(2, 'Marketing', 'Building B'),
(3, 'Sales', 'Building C'),
(4, 'HR', 'Building D');

INSERT INTO employees VALUES 
(1, 'John', 'Doe', 'john.doe@company.com', '2023-01-15', 65000.00, 1),
(2, 'Jane', 'Smith', 'jane.smith@company.com', '2023-02-20', 70000.00, 1),
(3, 'Bob', 'Johnson', 'bob.johnson@company.com', '2023-03-10', 55000.00, 2),
(4, 'Alice', 'Brown', 'alice.brown@company.com', '2023-04-05', 60000.00, 1),
(5, 'Charlie', 'Wilson', 'charlie.wilson@company.com', '2023-05-12', 75000.00, 3),
(6, 'Diana', 'Davis', 'diana.davis@company.com', '2023-06-18', 80000.00, 2);

INSERT INTO orders VALUES 
(1, 101, '2024-01-15', 1500.00, 'Completed'),
(2, 102, '2024-01-20', 2300.00, 'Pending'),
(3, 101, '2024-02-05', 800.00, 'Completed'),
(4, 103, '2024-02-10', 3200.00, 'Shipped'),
(5, 102, '2024-02-15', 950.00, 'Completed');
```

---

## Example 1: Basic SELECT Query
**Find all employees with their basic information**

```sql
SELECT employee_id, first_name, last_name, email, salary
FROM employees
ORDER BY last_name;
```

**Result**: Returns all employees sorted by last name with their ID, name, email, and salary.

---

## Example 2: Filtering with WHERE Clause
**Find employees with salary greater than $60,000**

```sql
SELECT first_name, last_name, salary, department_id
FROM employees
WHERE salary > 60000
ORDER BY salary DESC;
```

**Result**: Returns only employees earning more than $60,000, sorted by salary in descending order.

---

## Example 3: JOIN Query
**Get employee names with their department information**

```sql
SELECT e.first_name, e.last_name, e.salary, d.department_name, d.location
FROM employees e
INNER JOIN departments d ON e.department_id = d.department_id
ORDER BY d.department_name, e.last_name;
```

**Result**: Returns employee details along with their department name and location.

---

## Example 4: Aggregate Functions with GROUP BY
**Calculate average salary by department**

```sql
SELECT 
    d.department_name,
    COUNT(e.employee_id) as employee_count,
    AVG(e.salary) as avg_salary,
    MAX(e.salary) as max_salary,
    MIN(e.salary) as min_salary
FROM departments d
LEFT JOIN employees e ON d.department_id = e.department_id
GROUP BY d.department_id, d.department_name
ORDER BY avg_salary DESC;
```

**Result**: Shows department statistics including employee count, average, maximum, and minimum salaries.

---

## Example 5: Subquery Example
**Find employees who earn more than the company average**

```sql
SELECT first_name, last_name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees)
ORDER BY salary DESC;
```

**Result**: Returns employees whose salary is above the company average.

---

## Example 6: Conditional Logic with CASE
**Categorize employees by salary level**

```sql
SELECT 
    first_name,
    last_name,
    salary,
    CASE 
        WHEN salary >= 75000 THEN 'High'
        WHEN salary >= 60000 THEN 'Medium'
        ELSE 'Low'
    END as salary_level
FROM employees
ORDER BY salary DESC;
```

**Result**: Returns employees with a salary level category (High/Medium/Low).

---

## Example 7: Date Functions and Filtering
**Find employees hired in 2023**

```sql
SELECT first_name, last_name, hire_date
FROM employees
WHERE YEAR(hire_date) = 2023
ORDER BY hire_date;
```

**Result**: Returns employees hired specifically in 2023, ordered by hire date.

---

## Example 8: Window Functions
**Rank employees by salary within their department**

```sql
SELECT 
    first_name,
    last_name,
    salary,
    department_id,
    RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) as salary_rank
FROM employees
ORDER BY department_id, salary_rank;
```

**Result**: Shows each employee's salary rank within their department.

---

## Example 9: Complex JOIN with Multiple Conditions
**Find high-performing employees in Engineering department**

```sql
SELECT 
    e.first_name,
    e.last_name,
    e.salary,
    d.department_name
FROM employees e
INNER JOIN departments d ON e.department_id = d.department_id
WHERE d.department_name = 'Engineering' 
    AND e.salary > 65000
ORDER BY e.salary DESC;
```

**Result**: Returns Engineering employees with salary above $65,000.

---

## Example 10: Advanced Analytics Query
**Calculate salary statistics and identify outliers**

```sql
WITH salary_stats AS (
    SELECT 
        AVG(salary) as avg_salary,
        STDDEV(salary) as salary_stddev
    FROM employees
)
SELECT 
    e.first_name,
    e.last_name,
    e.salary,
    ROUND((e.salary - s.avg_salary) / s.salary_stddev, 2) as z_score
FROM employees e, salary_stats s
WHERE ABS((e.salary - s.avg_salary) / s.salary_stddev) > 1
ORDER BY ABS((e.salary - s.avg_salary) / s.salary_stddev) DESC;
```

**Result**: Identifies employees whose salary is more than 1 standard deviation from the mean (potential outliers).

---

## Summary

These 10 examples demonstrate:
1. **Basic SELECT** - Simple data retrieval
2. **WHERE filtering** - Conditional data selection
3. **JOIN operations** - Combining data from multiple tables
4. **Aggregate functions** - Statistical calculations
5. **Subqueries** - Nested query logic
6. **CASE statements** - Conditional logic
7. **Date functions** - Time-based filtering
8. **Window functions** - Advanced analytics
9. **Complex JOINs** - Multiple conditions
10. **Advanced analytics** - Statistical analysis

Each query showcases different SQL capabilities and can be adapted for various real-world scenarios.
